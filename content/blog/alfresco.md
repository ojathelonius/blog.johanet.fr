---
title: "Single Sign-On with Alfresco Share and Keycloak"
date: 2018-05-28T23:10:33+02:00
draft: false
---
I've been looking for a solution to provide Single Sign-On for Alfresco using Keycloak for some time. I found a few, however none including both recent versions of **Alfresco Community Edition** and Keycloak. Indeed, **Alfresco Content Services** includes a CAS feature but its free counterpart does not.

As a reminder, [Alfresco Community Edition](https://www.alfresco.com/fr/alfresco-community-editions) is an open-source **content management system** while [Keycloak](https://www.keycloak.org/) is a powerful, **open-source identity and access management software** developed by RedHat.

I finally got these two to work together using the `keycloak-protocol-cas` plugin and thought I would share the solution, which required modifying the `mod_auth_cas` Apache module which would not validate CAS service tickets otherwise.

This method has been tested with **Keycloak 4.0.0** and **Alfresco Community Edition 5.2**.

# Setting up Alfresco
## Download


Download Alfresco 5.2 on the [official website]( https://www.alfresco.com/products/community/download).

Make sure **LibreOffice dependencies** are installed (else you'll be prompted with a warning when trying to install Alfresco)

```bash
yum install libXrender libXext libSM libICE libGLU fontconfig cups-libs cairo mesa-libGL
```

If needed, give proper rights to current_user in order to install Alfresco.

```bash
chown current_user ./alfresco-community-installer-201707-linux-x64.bin
chmod +x ./alfresco-community-installer-201707-linux-x64.bin
```

## Optional : use an external database

It is possible to use an external database other than the one set up during the Alfresco installation.
This might make viewing and querying the database easier ; however this is not supported.

Connect to the external database :
```bash
psql -h database-url -p database-port -U postgres
```

Create a new role :
```sql
CREATE ROLE alfresco LOGIN PASSWORD 'admin';
```

Create the database :
```sql
CREATE DATABASE "alfresco" WITH OWNER "alfresco" ENCODING 'UTF8' LC_COLLATE = 'en_US.UTF-8' LC_CTYPE = 'en_US.UTF-8';
```

Add the proper database information in the `alfresco-global.properties` file in `/alfresco-community/tomcat/shared/classes` :

```
db.username=alfresco
db.password=admin
db.name=alfresco
db.url=jdbc:postgresql://database-url:database-port/${db.name}`
```

# Keycloak configuration

Install the `keycloak-protocol-cas` add-on by downloading the JAR from its [GitHub repo](https://github.com/Doccrazy/keycloak-protocol-cas/releases) and moving it to `standalone/deployments`. 

Create a new client on Keycloak with the **CAS client protocol** and enter the required redirect URIs. Make sure you use `http` instead of `https` even if using SSL.

# Apache configuration

## Reverse proxy
Alfresco should sit behind a reverse proxy to keep the CAS header-based authentication secure and prevent users from injecting their own headers.

Add new entries for Alfresco in your Apache proxy configuration files (example : `httpd.conf`) :
```apache
ProxyPass               /alfresco           http://127.0.0.1:8082/alfresco
ProxyPassReverse        /alfresco           http://127.0.0.1:8082/alfresco

ProxyPass               /share           http://127.0.0.1:8082/share
ProxyPassReverse        /share           http://127.0.0.1:8082/share
```

Add the proper values in the `alfresco-global.properties` file since Alfresco is now proxied :

```
alfresco.context=alfresco
alfresco.host=your-proxy-url
alfresco.port=80
alfresco.protocol=http

share.context=share
share.host=your-proxy-url
share.port=80
share.protocol=http
```

## Hijacking mod_auth_cas

**Since CAS 5.2.2, service tickets should not contain underscores**. However the `keycloak-protocol-cas` creates tokens that contains some.
We need to modify and recompile mod_auth_cas to make these two work together.

Clone the `mod_auth_cas` repo :

```bash
git clone https://github.com/apereo/mod_auth_cas
```

In `/src/mod_auth_cas.c`, change the following :

```c
if (*ticket != '-' && *ticket != '.' && !isalnum(*ticket))
```
to
```c
if (*ticket != '-' && *ticket != '.' && *ticket != '_' && !isalnum(*ticket))
```

Install the required dependencies if missing, to compile the module.

```bash
yum install openssl-devel curl-devel pcre-devel httpd-devel
```

Recompile the module with :
```shell
autoreconf -ivf
./configure &&  make && sudo make install
```

Create a new `mod_auth_cas` cache directory in `/var/cache/httpd/` : 
```sh
cd /var/cache/httpd/
mkdir mod_auth_cas
```

## Integrate Alfresco
Add the following configuration to your Apache proxy ; make sure you review the **realm name**.
The user used to launch the Alfresco server should also **be allowed to create files in the `CASCookiePath`.**
```apache
LoadModule auth_cas_module modules/mod_auth_cas.so

CASCookiePath /var/cache/httpd/mod_auth_cas/
CASLoginURL https://proxy-url/keycloak/realms/realm-name/protocol/cas/login
CASValidateURL https://proxy-url/keycloak/realms/realm-name/protocol/cas/serviceValidate
CASProxyValidateURL https://proxy-url/keycloak/realms/realm-name/protocol/cas/proxyValidate

<Location /share>
    AuthType CAS
    AuthName 'CAS'
    require valid-user
    CASAuthNHeader X-Alfresco-Remote-User
    CASScope /share
</Location>


<LocationMatch ^/alfresco/(?!service/|service$|webdav/|webdav$|s/|s$|wcs$|wcs/|scripts/|css/|(?!cmisatom/)|cmisatom$|images/).*>
    AuthType CAS
    AuthName 'CAS'
    require valid-user
    CASAuthNHeader X-Alfresco-Remote-User
    CASScope /alfresco
</LocationMatch>
```

It is also recommended that you add the following configuration to get **more information from the logs** :

```
LogLevel debug
CASDebug On
```

# Alfresco configuration

There is no client ID to configure ; the client is identified by its **redirect URI**, provided in the first request to the authentication server.

Activate the external authentication by adding the following to `alfresco-global.properties` :

```
authentication.chain=external1:external
external.authentication.proxyUserName=
external.authentication.enabled=true
external.authentication.defaultAdministratorUserNames=admin
external.authentication.proxyHeader=X-Alfresco-Remote-User
```

Make sure `proxyHeader` has the same value as `CASAuthNHeader` entered in the Apache config.

When accessing the Alfresco server, you should now be redirected to the **Keycloak login page**.

In `/alfresco-community/tomcat/shared/classes/alfresco/web-extension`, we need to modify the **Share config** to work with SSO.

Uncomment the second `<config evaluator="string-compare" condition="Remote">` .

Replace the `<connector-id>alfrescoCookie</connector-id>` with `<connector-id>alfrescoHeader</connector-id>` in this config tag.

Update the `userHeader` tags with the header name that was used in the **Apache CAS configuration**, as shown below :

```
<userHeader>X-Alfresco-Remote-User</userHeader>
```

Restart the Alfresco server and try logging into Share through Keycloak. You should then be redirected to Share with an authenticated user.


# Troubleshoot
If you see an IPV6 in the URL like the following as you log into Keycloak :

```
/login?service=http%3a%2f%2ffe80::251:66ff:fed1:2b58%2fshare
```

Then you need to add a `ServerName` to your Apache config :

```apache
ServerName your-proxy-url
```








 
