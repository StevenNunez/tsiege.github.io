---
layout: post
title: "Spaghetti to Object Oriented JavaScript"
date: 2014-10-22 18:27:12 -0400
comments: true
categories: JavaScript JS Object&nbspOriented&nbspProgramming OO OOP D3.js JavaScript&nbspLibraries
---
#Introduction
This post covers how to turn any pile of spaghetti JavaScript into an object oriented composition.  Be it jQuery selectors and listeners, some other JS library, or your own code.  The code I'm using will contain some D3.js, the data visualization library, but you do not need to be familiar with it to take away good design ideas.  I'll make sure to explain any object oriented terms that I use.

# The Problem I Faced
Recently I've been working on a graph feature for [Heat Seek NYC](http://heatseeknyc.com/) that display's a user's temperature readings. The graph, in concept, was straightforward enough, it used D3.js to draw a user's temperature readings sent via AJAX as a JSON.  I hadn't used D3.js before, but there were plenty of tutorials, and sure enough I got a working version.  The problem was that all the tutorials were piles of spaghetti code themselves, so mine was even worse being a customized version.

The graph looked good, but it was brittle.  Adding new features was painful, and I was the only one on the team who understood how it worked.  This bothered me, what if someone else needed to do something, or what if I forgot how the code worked!  I knew there was one solution to this problem, and it was object orientation.

# The Solution (you guessed it, OO!)
Meaning, that I needed to create a set of objects to communicate with one another.  The code as it was functional programming.  A series of functions were called in an exact order, and it _had_ to be that order.  That meant any change couldn't disturb that order.  However, if I had used an object oriented approach I could have objects talk to one another and use their own methods as they saw fit.

Basically, object orientation is awesome and organized while functional programming is not as much.  Both have their virtues, but I prefer the former.
