---
title: "Singapore Airlines AppChallenge 2018"
date: 2018-11-07T19:31:16+01:00
draft: false
description: "My experience as a SIA AppChallenge finalist"
---

I had the chance to be a finalist in the Singapore Airlines AppChallenge 2018 ; this is a short rundown of this amazing experience.

# The challenge

Singapore Airlines, Singapore's flag carrier airline, is looking for developers, start-up founders and students to brainstorm over several problem statements and come up with a prototype, as a part of the **Singapore Airlines AppChallenge**.



## 2018 edition

### Some numbers
The SIA AppChallenge started in 2015 and has only been growing since then.

In 2018, the challenge gathered **1527 participants**, **784 teams** in **73 different countries**.

The jury received **406 submissions** this year, during the initial selection phase.

**India** was the country that was the most involved in the challenge, with 399 participants, closely followed by **Singapore** (377), **France** (112), **Honk-Kong** (83) and the **United States** (75).

Only **12 teams** would make it to the Grand Finale : 6 from the **open category**, and 6 other composed of **Singaporean students**.


### Problem statements
Five problem statements were proposed to the participants :

* Improving Mobile App Engagement
* Offloaded Passenger Compensation Process
* Enabling the KrisShop (SIA's duty-free shopping) Experience
* Tracking Inventory Usage
* Tracking Food&Beverages Consumption


### Prizes

#### Student category
* 1st place : all expenses paid tech trip to Silicon Valley
* 2nd and 3rd place : 50k and 30k KrisFlyer Miles per member

#### Open category
* 1st place : return airfare to any SIA destination & team prize of 5000 SGD
* 2nd and 3rd place : team prize of 3000 & 1500 SGD

# Our project

## Idea

### Let it fly
"Let it fly" is a simple pun : offloaded passengers have to let their plane fly off, but they should also let it fly as it's no big deal, thanks to the awesome compensation they get.

### Features
"Let it fly" falls right into the **Offloaded Passenger Compensation Process** category.

The app aims at increasing the numbers of volunteers by providing them a way to swiftly take action, should they be offloaded. For this, it offers several features :

* Lists all the **perks of volunteering a seat**, such as free activities, hotels or restaurants
* Allows passengers to **volunteer** when an overbooking situation arises
* Provides SIA staff members with a **live description** of the overbooking situation
* Allows SIA staff members to **approve** or deny volunteers to free the necessary number of seats
* Allows passengers to **book any perk** directly from the app

## Initial submission
Our initial submission was a **React/Redux** web application connected to a **NodeJS** back-end. 

### Front-end
Thanks to **create-react-app**, React is a great choice to quickly build prototypes from the ground up while writing clean code.

Component libraries allowed us to build a pretty UI in seconds, while state management libraries such as Redux helped us dealing with the data coming from the back-end.


![Front-end](https://i.imgur.com/ZDkwZ0o.jpg)
The code is available [on GitHub](https://github.com/ojathelonius/let-it-fly-ui).

### Back-end

Singapore Airlines did provide a dummy REST API, however there wasn't much data available. Moreover, we could not get access to essential third-party APIs to populate our app, such as AirBnB's or Booking.com's, and had to use additional dummy JSON data.

**NodeJS** and **ExpressJS** allows creating a webserver in seconds, although using a boilerplate can make things even easier as it includes several utilities for parsing, dealing with security or querying external APIs.

The code is available [on GitHub](https://github.com/ojathelonius/let-it-fly-back-end).


### Presentation
We created a voiced-over 5 minutes video describing the problem and our solution, as well as demoing the web app.

{{< youtube 3PAMQNL95pk >}}

## Final submission
After being selected for the grand finale, we decided to take "Let it fly" further by integrating it right into the official SIA mobile application, and provide a way for staff members to monitor the overbooking situation.

We only had a few days to submit our final project, thus could not actually connect these applications to the back-end previously created.

### Mobile application
We implemented a React Native application using the **Expo framework**, which makes building and sharing native cross-platform apps a breeze.

Although we did not need the cross-platform feature, using React Native allowed us to reuse some of the components and the logic of the web app from the initial submission.

The **native-base** library was quite handy to avoid styling each component by hand.

![Mobile application](https://i.imgur.com/H3DjTrM.jpg)

The code is available [on GitHub](https://github.com/ojathelonius/let-it-fly-app).

### Dashboard
The dashboard was a last minute idea, and we used simple components along with **react-chart** and dummy data to showcase the overbooking monitoring feature.

![Dashboard](https://i.imgur.com/IualX6Z.jpg)

### Demo video
A demo video was created, and was voiced over live during the pitch.

{{< youtube rh8bgbEPJvI >}}

# Grand Finale

After making it to the final phase, our team was assigned with a mentor with whom we were able to talk about the project on Skype.

Our team was flown out to Singapore on October 25th for the grand finale, and we were to stay there for the entire week-end.

## Rehearsal
### Mentor rehearsal
We were able to rehearse with our mentor and an advisor at the **National University of Singapore (NUS)**, and received useful tips for our presentation.

### Final rehearsal
The day before the finale, each team would present their own project in front of a small audience, composed of the challenge organisers, to make sure that everything would run smoothly.

## D-day
On October 27th, each teams pitched their idea in front of Singapore Airlines execute commitee for 13 minutes :

* 5 minutes of presentation
* 3 minutes of demo
* 5 minutes of Q&A from the jury

## Results
The results were announced on the same day, shortly after the pitches. Sadly we did not make it on the podium, but were still happy with our presentation.


### Hindsight

#### Grading criterias
We felt that the winning ideas were quite interesting and definitely deserved to win. However we felt that some of these ideas had drifted from the grading criterias, which were :

* Innovation (20%)
* Business Value (40%), e.g. *How commercially feasible i-s it to implement for SIA?*
* Technical Competency (30%), e.g. *How technically feasible is the solution, in view of current leading-edge technologies?*
* User Experience (10%)

Indeed, one of the core principles that we kept in mind while designing "Let it fly" was **feasability**. Our solution had a short time to market and could easily reach production in months. Due do this, "Let it fly" may not have been as audacious as some of the winning ideas.


#### Software/hardware

Our project was software-only. Many projects that ended up at the top involved both hardware and software, and some even had a physical prototype ; this may have been a disadvantage.

#### Live performance
Oral presence and presentation quality were absolutely key during this event. Some teams had really good speakers, which would make the pitch much more lively. 

![Live demo](https://i.imgur.com/Bpqe6FF.jpg)

Some teams attempted a live demo, but it usually did not work well due to the unpredictable conditions (spotty Wi-Fi, Murphy's law...). Pre-recorded demo videos sometimes had to be used instead.

Answering jury questions was no easy task, as these were quite relevant and were really trying to frame each project.

Overall, all teams did a great job at presenting and respected the strict 13 minutes maximum time for the presentation.


![Group photo](https://i.imgur.com/y1YMiYF.jpg)

We had a blast during this event, that was quite well organized by SIA employees and Agorize. We will definitely be entering the challenge again next year !























