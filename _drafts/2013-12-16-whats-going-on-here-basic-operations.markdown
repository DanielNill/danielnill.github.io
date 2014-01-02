---
layout: base
title: "What's Going On Here: Basic Operations"
alias: /blog/whats-going-on-here-basic-operations
---

A central tenant of computer and programming architecture is the idea of levels of black boxes.  That is, one does not need a deep understanding of what is happening at a lower level of a program in order to utilize its features at a higher level.

For example:

Say I am making a hamburger.  I do not need to know how to raise and slaughter a cow.  Someone else has already done that work for me in a reproducible and automated fashion.  This principle is observed everywhere in computer programming and architecture.  Jean Baptiste Queue wrote a great article on this principle several months ago giving multiple examples of how we observe and utilize this idea every day.

This idea is great and at its most extended levels allows a lot more people to learn to program and build web apps.  But as I have found,  moving from a programmer simply stringing classes and method calls together in a framework, to actually building and maintaining complex systems (particularly in the world of PHP) it can be helpful to break this paradigm and dig deeper to actually understand what is happening inside your program.

In order to do this I am going to attempt to write some posts exploring different tools of the programming world explaining to myself, and in the process, to others what is going on behind these tiny snippets of "copy paste code".

In this first entry I'm going to attempt to review something I'm already somewhat familiar with: basic pointer operations.

The Rules:

We will only go one level deeper.
A lot of this research is on the fly so there is a decent chance I'm going to be wrong about some things.  I will work to fix these errors as they are pointed out to me.
Use the source.  When available provide source code as proof.
Basic Operations:

For our purposes basic operations consist of the first elements a programmer learns: variables and loops.  These are basic concepts so it shouldn't take very long (but honestly, this first post was mostly an introduction to the concept).

Variables:

We all know what a variable is.  It is the most basic building block of most programming languages.  And probably most programmers know what is going on underneath the hood when we create a variable in our favorite language, so I'll keep this one brief.  What we are going to explore is quite simply what is happening when we say:

    x = 1
or

    var x = 1;
or

    $x = 1;
and so on and so on.

Simply put, we are taking a unit of memory and assigning it a value.  Later when we ask to do something with the variable whether that is print it, change it, or use it in a comparison, we call to that same unit of memory and get the value stored in it.

When we are talking about memory storage it is important to understand that each unit of memory has three elements that are utilized:

Size
Value
Identifier
In our example for most languages we consume eight bites of memory, so that is our size.   Simply put, eight bites is enough to store one letter or number in memory.  When we talk about strings, arrays, closures, objects, etc. it is worth noting that that variable is simply "pointing" to a collection of memory.  So for our purposes we will say that a variable in and of itself is always a single unit of memory.

The value is even easier.  It is, as you would suspect, the value you are actually assigning.  So in our example "1".  This is changed whenever we change the actual value of our variable.

Lastly, everytime we create a

