---
layout: post
title: "Spaghetti to Object Oriented JavaScript"
date: 2014-10-22 18:27:12 -0400
comments: true
categories: JavaScript JS Object&nbspOriented&nbspProgramming OO OOP D3.js JavaScript&nbspLibraries Refactoring
---
# Introduction
This post covers how to turn any pile of spaghetti JavaScript into an object oriented composition.  Be it jQuery selectors and listeners, some other JS library, or your own code.  The code I'm using will contain some D3.js, the data visualization library, but you do not need to be familiar with it to take away good design ideas.  I'll make sure to explain any object oriented terms that I use.

I will also be creating JavaScript constructor functions and prototypical methods. If you are unfamiliar with these parts of the language see this post for [more](http://pivotallabs.com/javascript-constructors-prototypes-and-the-new-keyword/).

# The Problem I Faced
Recently I've been working on a graph feature for [Heat Seek NYC](http://heatseeknyc.com/) that display's a user's temperature readings. The graph, in concept, was straightforward enough, it used D3.js to draw a user's temperature readings sent via AJAX as a JSON.  I hadn't used D3.js before, but there were plenty of tutorials, and sure enough I got a working version.  The problem was that all the tutorials were piles of spaghetti code themselves, so mine was even worse being a customized version.

The graph looked good, but it was brittle.  Adding new features was painful, and I was the only one on the team who understood how it worked.  This bothered me, what if someone else needed to do something, or what if I forgot how the code worked!  I knew there was one solution to this problem, and it was object orientation.

# The Solution (you guessed it, OO!)
Meaning, that I needed to create a set of objects to communicate with one another.  The code as it was functional programming.  A series of functions were called in an exact order, and it _had_ to be that order.  That meant any change couldn't disturb that order.  However, if I had used an object oriented approach I could have objects talk to one another and use their own methods as they saw fit.

Basically, I like object orientation for organizing my code, both styles have their merits and detractions.  This post isn't about that debate, so let's move along.

# Organizing the Spaghetti
Before I could begin designing my objects and how they interacted I needed to separate all the spaghetti into functions.  I began by putting any selector or d3 methods into functions. Doing this helped me get a better sense of the chart's discrete parts.

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
Many of the refactored functions were like `createSvg`, setters that use the D3js library to create elements for the DOM, attach elements to the DOM, and to create other functions involved in drawing the chart.  Some of the variables created in this manner were used various times, others just once.  

Realizing this allowed me to group the functions together. I ended up with several different sets, one being the main SVG element that all the parts of the graph get added to.  Each part of the graph such as the line itself, would become their own constructor function.

Another part of this process was moving all variable declarations to the top of the file.  Partially because I wasn't too sure if the variables were being used by multiple parts of the graph or not.  The main reason was that I already knew one aspect of my design; I would make a duck type that would pass itself to other options when it called them.

# Creating the Duck Type
A duck type is a substitute for inheritance.  Instead of worrying about if all the objects interacting with one another are of a common class, all we need to be concerned with is whether or not it they share a common interface, i.e. implement the same method(s).

My interface is simple.  All parts of the graphs just need to add themselves to the chart via an `#addToChart` method. The object that would call the `#addToChart` method would only care if they had implented their own version; it does not need to know how each object performs the method, just that it does.

Using the sets of functions I mentioned in the previous section I began creating my constructors, for example the function that created the main SVG became it's a constructor function with all of it's related functions becoming methods on the constructor's prototype.

Once I had all the separate parts of the graph made into constructors with functions attached to the prototype as methods, I could then begin implementing the `#addToMap` method for each prototype.

Once this was set up I began removing the parts of the code that was not necessary for the ChartSVG constructor and moved them into the other constructors' prototypes.

To view this code see this [commit](https://github.com/heatseeknyc/heatseeknyc/commit/de86a6272c99e648f214d82acc509eaa30a86798?diff=split).


# Using the Duck Type


The
