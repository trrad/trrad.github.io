---
layout: post
title:  "An Ode To Python"
date:   2017-08-06
---
I've been writing Python for about half of my life - I started right about when 2.6 was first released (I'm 23 now). It was my first 'real' programming language, not constrained by a proprietary program or GUI building blocks, and it is still the vast majority of the (back-end) code I write.

Since then, I've written code in lots of other langauges too - a few different C variants, a little bit of C++, Java and a deeply unfortunate amount of Javascript - but Python keeps bringing me back and still holds a special place in my heart. In this post I'll outline why that is, and also talk about some common criticisms of the language.

This post is by no means comprehensive, and is largely just my personal reflection on what I like about the language (and a defense of why!).

## 1 - It's fun to write.

Many readers of this are probably familiar with [this](https://xkcd.com/353/) old XKCD comic about Python. It's certainly true (and more so today than it was back when it was first published), that Python has great library support, and services like pip and PyPi make using them a relative breeze, even when they aren't included in the substantial standard library - but this isn't really unique.

{%highlight python%}
import antigravity #flight achieved!
{%endhighlight%}

I will admit it is pretty great to be able to poke around the filesystem, parse JSON, run asynchronous event loops and unit test (and so much more!) all with simple imports from the standard library. But what I consider to be a more fundemental part of the Python experience is that if you have a reasonably good understanding of the Pythonic way of writing code you can expect to be able to write code in that way and it **just works**. I likely have some bias here, it being my first and most used-language, but I found this to be true even when I first started writing it. There have been a countless number of times where I won't know what the syntax to do something is, so rather than opening a Google tab and searching for it, I'll just guess and *voila* it does exactly what I was looking for. It has a strong, central design philosophy focused around readability (at least once you know the idioms) and useability, and a huge amount of commonly used logic for working with strings, lists and all kinds of iterables encoded in the standard library that makes a number of common programming tasks easy to both write *and* interpret 1-liners.

I've never been all that interetested in 'programming challenges' or writing effecient implementations of these low-level algorithms - Python lets me do less of that kind of rote coding and skip to the interesting parts of the problem. By leveraging *collections* and *itertools*, I don't have to bother re-implementing even more stuff.

## 2 - It's objects, all the way down.

A big part of Python's design philosophy that resonates me is that everything is an object, and can be accessed and treated as such. Classes are objects, functions are objects, it's all objects and you can assign them to variables and pass them as arguments however you would like. This enables some quite elegant metaprogramming like decorators.

I find it is also very useful in terms of how it underlies decisions in how programs should be structured and helpful for debugging. The fact that regardless of what part of the code I'm in, I can query concepts like the objects they are underneath is both useful in guiding my thought process when debugging and in providing information about what's going wrong (ie: I can easily look and see what the actual instance of the function I'm calling is and what it's properties are - where it's stored in memory, etc.).

## The Common Objections

This next section goes over some of the common responses people who don't love Python like I do respond when I start proselytizing on it's behalf:

#### 1 - It's too slow!

There are a few different responses to this. First of all, speed is over-rated for the vast, vast majority of applications. Clock cycles are pretty much free in today's day and age, and developers are not. For many, many problems it is often going to be that the more effecient solution involves making life easier for your developers and buying a little extra compute time. Secondly, by using a tool like Cython that allows easy interoperability of C++/C and Python, you can still use your high-performance BLAS library written in C for all the difficult computation via an easy to write and read Python wrapper. Lastly, Python's slowness is often over-exageratted - most excessively slow code is badly written code - and many of the features of Python discussed above encourage users to write more effecient implementations. Yes - Python is not a tool for writing high-performance low-level code - but that's okay.

#### 2 - Whitespace is the devil!

This one I actually have some sympathy for, and no complete response to. I think it mostly comes down to personal preference - how used one is to working out the depths of nested brackets vs. the pain that hidden or missing whitespace can cause. IDEs do a lot of work to mitigate both of those problems for users. It also brings about some limitations in terms of scoping - it's not as powerful as being able to manally scope something out using parantheses and can lead to some unexpected behaviour from the interpreter.
