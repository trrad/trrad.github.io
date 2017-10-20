---
layout: post
title:  "Understanding Bayesian Linear Regression (Part 1)"
date:   2017-10-20
---
Linear regression is a basic statistical tool that pretty much anyone who has worked with data is familiar with. In this post, I'm going to outline a Bayesian approach to linear regression in as clear a way as possible, and walk through a simple example using Edward. Before we do that, let's quickly review a 'classical' approach to this regression and what it means.

When $a \ne 0$, there are two solutions to \(ax^2 + bx + c = 0\) and they are
$$x = {-b \pm \sqrt{b^2-4ac} \over 2a}.$$
