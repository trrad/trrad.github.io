---
layout: post
title:  "Skeumorphism in Programmatic Design"
date:   2017-10-26
---

One of the most difficult things to get right about object-oriented programming is learning to choose the right abstractions. It is ultimately a black magic and unless you're a code-guru or extremely lucky you will make mistakes that will cause you or your users a considerable amount of discomfort and annoyance down the road, as having awkward abstractions locked into a large existing code base can make implementing new features many, many times more difficult than something more natural would.

When I first started programming, my father taught me a simple guiding rule that I've come to call skeumorphism of abstraction. Skeumorphism refers to a trend in design (especially UI/UX design) of making digital things look like their real-world counter-part. So the button for the camera app on my phone looks like an real camera, the save icon looked like a floppy disk, and so on. The programming adaption of this is that your code should be built around classes and objects that represent their real world counterparts, and do the same things they do.

This may seem obvious, but I have found suprisingly little written about practical advice for actually choosing abstractions when you first start writing your code. This approach does somewhat conflict with the single responsability principle (SRP) that S.O.L.I.D advocates for, but I think often for the better.

## Extensible

By having digitial representations of the real-world concepts that are involved in my problem at hand, it becomes easy for me to add features later, since what ever part of the problem I want to model should already have a representation in the code. Almost all code written is to model and solve problems in the real world - and this is a reliable way to make sure it's prepared to do that.

## Clear Organization

It often answers the question "where should this code go?" in a rule that's easy to understand - you can consistently implement code in the place that you would expect it, even without knowledge of the code base, as long as you have knowledge of how that task is done in the real world. It's also a simple principle that decides it - so it's easier for others to add to the code-base in a consistently organized way.

## Understandable

Code is read far more often that it's written. Having a design that facilitates people's understanding of what is happening is a good idea.

It's by no means a complete system, or even an attempt at one - but it is how I start designing a system when I first sit down and think about a problem, and I feel it's saved me from many more problems than it's caused, which is all we can really ask for.
