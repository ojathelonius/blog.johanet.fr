---
title: "Tooltip with arrow and true shadow"
date: 2018-07-18T22:31:44+02:00
draft: true
---
<link rel="stylesheet" href="/css/css_tooltip.css">

Do you want to show a tooltip on hover with an **arrow pointing** at the hovered element ? Do you also want it to have a **nice shadow all around** ? Would you, by any chances, would like to do this **without Javascript** ? Here's a way.


# Obligatory finished product first

<div class="oja-show-container">
    <div class="oja-btn-hover">
    <h3>
    Hover me
    </h3>
    <div class="oja-container">
        <div class="oja-dropdown">
        <p>
            This is a pretty tooltip. Notice how its shadow is both on the tooltip and the arrow ?
        </p>
        </div>
        <span class="oja-dropdown-hidden"></span>
    </div>
    </div>
</div>

# Where the magic lies
## It's a lie
The trick is to display a shadowed tooltip as well as a square with its own shadow. We will then hide half of the square with another white element, making it look like an arrow.


<div class="oja-ex-container">
  <div class="oja-ex-tooltip">
  </div>
  <span class="oja-ex-hidden"></span>
</div>


<br>
## Why is this even necessary ?
Well, if you want the tooltip to cast a shadow all around, you need to apply the `box-shadow` property to both the arrow and the tooltip. Whether one is in front of the other, it'll cast an unwanted shadow on its neighbor.

## Implementation

### HTML

```html
<div class="oja-show-container">
    <div class="oja-btn-hover">
    <button>
    Hover me
    </button>
    <div class="oja-container">
        <div class="oja-dropdown">
        <p>
            This is a pretty tooltip.
        </p>
        </div>
        <span class="oja-dropdown-hidden"></span>
    </div>
    </div>
</div>
```

### SCSS
```scss
/* Shadow with inverted Y-axis for realism */
$shadow1: -2px -2px 5px 1px rgba(0, 0, 0, 0.10);
$shadow2: -2px 2px 5px 1px rgba(0, 0, 0, 0.10);
$popup-border-radius: 5px;
$popup-padding: 20px;

.btn-hover {
  /* Dropdown container hidden by default */
  .container {
    display: none;
  }
   
  &:hover {
    
    .container {
      display: block;
      background-color: transparent;
      
      /* Offset with the hovered button */
      position: relative;
      top: 20px;
      
      .dropdown {
        /* z-index needs to be lower than the dropdown-hidden's */
        z-index: 2;
        display: block;
        box-shadow: $shadow1;
        background-color: white;
        border-radius: $popup-border-radius;
        padding: $popup-padding;
        width: 350px;

        &:after {
          background-color: white;
          content: '';
          width: 25px;
          height: 25px;
          z-index: 2;
          box-shadow: $shadow2;
          transform: rotate(45deg);
          
          position: absolute;
          top: -13px; /* Trickier to position*/
          left: 15px; /* Wherever you want along the X-axis */
        }

      }
      .dropdown-hidden {
        background-color: white;
        width: 60px; /* Wide enough to hide the arrow */
        
        /* z-index needs to be higher than the dropdown's */
        z-index: 3;
        
        height: $popup-padding; /* Tall enough to hide the arrow without hiding any other content */
        position: absolute;
        border-radius: $popup-border-radius;
        top: 0px;
      }
    }
  }
}
```





 
