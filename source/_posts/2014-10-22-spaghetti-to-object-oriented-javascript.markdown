---
layout: post
title: "Spaghetti to Object Oriented JavaScript"
date: 2014-10-22 18:27:12 -0400
comments: true
categories: JavaScript JS Object&nbspOriented&nbspProgramming OO OOP D3.js JavaScript&nbspLibraries Refactoring
---
# Introduction
This post covers how to turn any pile of spaghetti JavaScript into an object oriented composition.  Be it jQuery selectors and listeners, some other JS library, or your own code.  The code I'm using will contain some D3.js, the data visualization library, but you do not need to be familiar with it to take away good design ideas.  I'll make sure to explain any object oriented terms that I use.

I will also be creating JavaScript constructor functions and prototypical methods. If you are unfamiliar with these parts of the language see this great [post](http://pivotallabs.com/javascript-constructors-prototypes-and-the-new-keyword/) for clarification.

All of my ideas on Object Oriented Design Patterns I owe to Sandi Metz and here amazing book POODR.  While it's written in ruby it's easy for any developer, including novices, to understand.

# The Problem I Faced
Recently, I've been working on a graph feature for [Heat Seek NYC](http://heatseeknyc.com/) that display's a user's temperature readings. The graph, in concept, was straightforward enough, it used D3.js to draw a user's temperature readings sent via AJAX as a JSON. I hadn't used D3.js before, but there were plenty of tutorials, and sure enough I got a working version.  The problem was that all the tutorials were piles of spaghetti code themselves, so mine was even worse being a customized version setup by a novice.

The graph looked good, but it was brittle.  Adding new features was painful, and I was the only one on the team who understood how it worked.  This bothered me, what if someone else needed to do something, or what if I forgot how the code worked!  I knew there was one solution to this problem, and it was object orientation.

To get a better sense of what I was dealing with checkout the code [here](https://github.com/heatseeknyc/heatseeknyc/blob/9bb153b89afde75556f3178c62e7c364fdaa676b/app/assets/javascripts/d3-chart.js).

# The Solution
My plan was to create a set of objects to communicate with one another.  How this would work exactly was unclear to me because, looking at the spaghetti code, it was hard for me to determine the discrete parts of the graph. For example, what lines of code were just used to draw the x-axis? Was this code vital to other elements?

The code as it was, was functional programming. A series of functions were called in an exact order, and it _had_ to be that order or the code would break.  However, if I had used an object oriented approach I could have had objects talk to one another via public interfaces (a prototype's methods that I allowed other objects to call).  This would have allowed an object to use it's own private methods as it saw fit and would not require code to in an exact order.

# Organizing the Spaghetti
Before I could begin designing my objects and how they interacted I needed to separate all the spaghetti into functions. Doing this helped me get a better sense of the chart's discrete parts; that is, this function was needed only to perform this one action.

__Before__
```javascript
if (svg.empty()) {
  var svg = d3.select('#d3-chart')
    .append('svg:svg')
    .attr('width', w)
    .attr('height', h)
    .attr('class', 'viz')
    .append('svg:g')
    .attr('transform', 'translate(' + margin + ',' + margin + ')');
}
```
__After__
```javascript
function createSvg(){
  if (svg.empty()) {
    svg = d3.select('#d3-chart')
      .append('svg:svg')
      .attr('width', w)
      .attr('height', h)
      .attr('class', 'viz')
      .append('svg:g')
      .attr('transform', 'translate(' + margin + ',' + margin + ')');
  }
}
```

To see the full conversion check out this [commit](https://github.com/heatseeknyc/heatseeknyc/commit/40ae8168b8b80aad350e0d73b56f618fcd819d8d?diff=split).

Many of the refactored functions used the D3js library to create elements for the DOM, attach elements to the DOM, or to create other functions involved in drawing the chart.  Some of the variables created in this manner were used various times, others just once.

Realizing this allowed me to group the functions together and variables together. I ended up with several different sets. For example, one set of functions and variables were centered around creating the main SVG element that all the parts of the graph get added to.  Each set of functions and variables would then be grouped into a constructor and prototypical methods.

Another part of this process was moving all variable declarations to the top of the file and attaching them to the Chart SVG constructor.  Partially because I wasn't too sure if some variables were being used by multiple parts of the graph or not.  Another reason was that I already knew one aspect of my design; I would make a duck type that would pass itself to other options when it called them.  This would allow for other constructors to access properties from the duck type when it was passed in.  If a property or method was only used by one constructor I would move that code into the constructor.

For a better sense of what I did check out this [commit](https://github.com/heatseeknyc/heatseeknyc/commit/de86a6272c99e648f214d82acc509eaa30a86798).

# Creating the Duck Type
A duck type is a substitute for inheritance.  Instead of worrying about if all the objects interacting with one another are of a common class, all we need to be concerned with is whether or not it they share a common interface, i.e. implement the same method(s).

My interface is simple.  All parts of the graphs just need to add themselves to the chart via an `#addToChart` method. The object that would call the `#addToChart` method would only care if they had implented their own version; it does not need to know how each object performs the method, just that it does.

Using the sets of functions I mentioned in the previous section I began creating my constructors, for example the function that created the main SVG became it's a constructor function with all of it's related functions becoming methods on the constructor's prototype.

Once I had all the separate parts of the graph made into constructors with functions attached to the prototype as methods, I could then begin implementing the `#addToMap` method for each prototype.

Here is one constructor's `#addToMap` method. For the whole constructor see it on [github](https://github.com/heatseeknyc/heatseeknyc/blob/master/app%2Fassets%2Fjavascripts%2Fuser-show-temp-line-group.js).
```javascript
UserShowTempChartLine.prototype.addToChart = function(){
  if ( this.hasTransitions ) {
    this.drawDataLineWithTransitions();
  } else {
    this.drawDataLineWithoutTransitions();
  }
};
```

Once this was set up I began removing the parts of the code that was not necessary for the Chart SVG constructor and moved them into the other constructors' prototypes.  See the whole at its infancy here constructor [here](https://github.com/heatseeknyc/heatseeknyc/commit/de86a6272c99e648f214d82acc509eaa30a86798?diff=split).

# Using the Duck Type
Once I had the other constructors created, using the duck type was easy.  I'd pass the instance of the Chart SVG constructor to the new instances of the duck types.  I'd then call their duck type method.

```javascript
UserShowTempChartSvg.prototype.addChartElements = function(){
  // area is placed first behind all other elements
  var groupArea = new UserShowTempChartAreaGroup(this, this.optionsObj);
    groupArea.addToChart();
  // x ticks and labels gets placed first
  var xGroup = new UserShowTempChartXAxisGroup(this, this.optionsObj);
    xGroup.addToChart();
  // y ticks and labels gets placed second
  var yGroup = new UserShowTempChartYAxisGroup(this, this.optionsObj);
    yGroup.addToChart();
  // lines and labels gets placed third
  var dataLinesGroup = new UserShowTempChartLine(this, this.optionsObj);
    dataLinesGroup.addToChart();
  // circles and labels are placed fourth
  var circles = new UserShowTempChartCircles(this, this.optionsObj);
    circles.addToChart();
};
```

# Final Thoughts
While the process was not easy; it also wasn't as hard as I thought it would be.  And an important thing to keep in mind is as Sandi Metz said, "Design is more the art of preserving changeability than it is the act of achieving perfection."  My code is not perfect, but it is more flexible than it was before, and therefore "good enough".  That is, I will never make my code, perfect, but as long I strive to make it flexible it will be changeable; therefore, good.


Here are links to all the final parts of the chart as well as where I started.
* [The Code Before Refactoring](https://github.com/heatseeknyc/heatseeknyc/blob/9bb153b89afde75556f3178c62e7c364fdaa676b/app/assets/javascripts/d3-chart.js)
* [The Chart SVG](https://github.com/heatseeknyc/heatseeknyc/blob/master/app/assets/javascripts/user-show-temp-chart.js)
* [The Object That handles the AJAX and calls the Chart SVG](https://github.com/heatseeknyc/heatseeknyc/blob/master/app/assets/javascripts/user-show-temp-chart.js)
* [The Area Group](https://github.com/heatseeknyc/heatseeknyc/blob/master/app/assets/javascripts/user-show-temp-area-group.js)
* [The X-Axis Group](https://github.com/heatseeknyc/heatseeknyc/blob/master/app/assets/javascripts/user-show-temp-xaxis-group.js)
* [The Y-Axis Group](https://github.com/heatseeknyc/heatseeknyc/blob/master/app/assets/javascripts/user-show-temp-yaxis-group.js)
* [The Temperature Line Group](https://github.com/heatseeknyc/heatseeknyc/blob/master/app/assets/javascripts/user-show-temp-line-group.js)
* [The Circles Group](https://github.com/heatseeknyc/heatseeknyc/blob/master/app/assets/javascripts/user-show-temp-circles.js)
* [The Tool Tips](https://github.com/heatseeknyc/heatseeknyc/blob/master/app/assets/javascripts/user-show-temp-tool-tips.js)

# TL;DR
1. Group spaghetti code into functions (each function must be responsible for one thing).
2. Group functions that perform similar tasks into a constructor.
3. Create a common method between parts so that a main object can call them easily (the duck type).
4. Enjoy the code, and remember what Sandi Metz says about classes (or protorypes in this case), "A class should do the smallest possible useful thing; that is, it should have a single responsibility."  
