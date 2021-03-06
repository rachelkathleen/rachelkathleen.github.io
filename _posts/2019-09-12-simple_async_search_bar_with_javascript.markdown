---
layout: post
title:      "Simple Async Search Bar with Javascript"
date:       2019-09-12 00:44:46 -0400
permalink:  simple_async_search_bar_with_javascript
---




<iframe src="https://share.getcloudapp.com/jkuJkq99?embed=true" width=90% style="border:none" frameborder="0" allowtransparency="true" allowfullscreen="false">              </iframe>

This is a quick guide on how to set up a search bar. I'll be using a list of the 50 states, rendered as an unordered list as an example. However, you can use this same search for anything or any part of an application by changing the  variables and tags. 

First take a look at the sample HTML.

```
<ul id="stateList">
  <li>Alabama</li>
  <li>Alaska</li>
  <li>Arizona</li>
  <li>Arkansas</li>
  <li>California</li>
  <li>Colorado</li>
  <li>Connecticut</li>
  <li>Delaware</li>
  <li>Florida</li>
  <li>Georgia</li>
  <li>Hawaii</li>
  <li>Idaho</li>
  <li>Illinois</li>
  <li>Indiana</li>
  <li>Iowa</li>
  <li>Kansas</li>
  <li>Kentucky</li>
  <li>Louisiana</li>
  <li>Maine</li>
  <li>Maryland</li>
  <li>Massachusetts</li>
  <li>Michigan</li>
  <li>Minnesota</li>
  <li>Mississippi</li>
  <li>Missouri</li>
  <li>Montana</li>
  <li>Nebraska</li>
  <li>Nevada</li>
  <li>New Hampshire</li>
  <li>New Jersey</li>
  <li>New Mexico</li>
  <li>New York</li>
  <li>North Carolina</li>
  <li>North Dakota</li>
  <li>Ohio</li>
  <li>Oklahoma</li>
  <li>Oregon</li>
  <li>Pennsylvania</li>
  <li>Rhode Island</li>
  <li>South Carolina</li>
  <li>South Dakota</li>
  <li>Tennessee</li>
  <li>Texas</li>
  <li>Utah</li>
  <li>Vermont</li>
  <li>Virginia</li>
  <li>Washington</li>
  <li>West Virginia</li>
  <li>Wisconsin</li>
  <li>Wyoming</li>
</ul>
```

The first thing to add for a search bar is... a search bar. 

```<input type="text" placeholder="Search">```

We need to add an id to this, so that we can identify it with Javascript.

```<input type="text" id="myInput"  placeholder="Search">```

We also need to add an event listener and call our Javascript function (coming soon!) There are a few that woulr work - I'm going to use onKeyUp, which is triggered after the user types something.

```<input type="text" id="myInput" onkeyup="mySearchFunction()" placeholder="Search">```

Now let's put together the Javascript. In the HTML for our search bar, a function is being called - let's build that.
To get this up and working, here are the steps to code out:

1.  Declare variables, the user input (what's being typed in the search box, a filter (so we can ignore case), the list, each item in the list.
2.  Treat our list like an array - using the indeces to iterate over each item, camparing the inner text or inner html with the input.
3.  Display matching list items, or nothing if no match.

```
function mySearchFunction() {
  var input, filter, ul, li, item, i, txtValue;
  input = document.getElementById("myInput");
  filter = input.value.toUpperCase();
  ul = document.getElementById("stateList");
  li = ul.getElementsByTagName("li");

  for (i = 0; i < li.length; i++) {
    item = li[i];
    txtValue = item.textContent || item.innerText;
    if (txtValue.toUpperCase().indexOf(filter) > -1) {
      li[i].style.display = "";
    } else {
      li[i].style.display = "none";
    }
  }
}
```

Here's a [CodePen](https://codepen.io/rachelhawa/pen/vYBjQMY) to see this in action.








