---
layout: post
title:  "Understanding Bayesian Linear Regression (Part 1)"
date:   2017-10-10
---
Linear regression is a basic statistical tool that pretty much anyone who has worked with data is familiar with. In this post, I'm going to outline a Bayesian approach to linear regression in as clear a way as possible, and walk through a simple example using Edward. Before we do that, let's quickly review a 'classical' approach to this regression and what it means. In this model of linear regression, I have the equation:

$$y_i=α+βx_i+ε$$

and I want to find the specific paramater values for this equation that will maximize the likelihood (read: chance) of me seeing this particular combination of data that I do. In the case that the error term ($ε$) has a Gaussian distribution, the ordinary least squares method (which minimizes the squared difference between the values predicted by my linear equation and the measured values) is the same as the more general maximum likelihood estimation (MLE).

One of the main things that the Bayesian approach does differently is that it is not trying to estimate a single parameter value. Instead, it aims to estimate a distribution that describes a range and likelihood of possible values for each paramater. The question behind the regression becomes not "what is the most likely slope and intercept for the data I have?", but "what can I possibly say about the possible values for the slope and intercept given the data that I have?". Both approaches require you make assumptions about the nature of your data, parameter and error distributions - but the bayesian approach explicitly includes these assumptions in the form of your priors.
