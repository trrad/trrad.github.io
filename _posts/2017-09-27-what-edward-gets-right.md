---
layout: post
title:  "What Edward Gets Right"
date:   2017-09-27
---

For the past few months I've been ranting about [Edward](http://edwardlib.org/){:target="_blank"} to anyone who will listen, so I figured I might as well write some if it down so I can repeat myself a little less. It's a probabalistic programming library, written in Python built on top of Tensorflow (and recently announced that it's merging into the main tf project.) It's maintained and developed by [Dustin Tran](http://dustintran.com/){:target="_blank"}.

## 1. Abstraction

The one of the cool things Edward does is that it creates a RandomVariable implementation as a first-class citizen. It uses this to define a number of default distributions, and you can easily sample from it. It's compositional, meaning I can connect these variables together as inputs and outputs to each other in a graphical models. This simple, imperative implementation has almost unlimited power in terms of what it can represent. It is also, I feel, the correct way to reason about modelling phenomena - everything is a random variable, all we have are samples, and all we're trying to do is do inference on some latent (also random!) variables.

## 2. Box's Loop

The theory behind Edward's approach to learning is explained [here](http://www.annualreviews.org/doi/full/10.1146/annurev-statistics-022513-115657){:target="_blank"}. It is built around a process that has three steps. First, I build a model that explains my data, second I condition my model on my data, and lastly I evaluate my updated model. The high-level API is divided in such a way that it encourages users to follow this pattern.

## 3. Power

By being built on top of Tensorflow, Edward gains a considerable amount of computational sophistification that would otherwise be impractical for a single-developer project. It means it can effectively leverage learning on GPUs to handle the large scale linear algebra problems required to implement many modern neural techniques, plus all the benefits of it's own interface.

***


I think it's the third point, plus the Pythonic, imperative interface that really set Edward apart from previous forays into probabilistic programming (Stan, Anglican, Church, etc.). Development is still in fairly early stages to, and I'm excited to see the impact it can have once integrated with TF.
