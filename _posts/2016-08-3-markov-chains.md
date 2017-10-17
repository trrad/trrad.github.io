---
layout: category-post
title:  "Markov Chain Portmanteaus"
date:   2017-09-10
categories: python
---

Simple Markov chains have been the workhorse of blogposts and websites about text generation for many years. They are suprisingly effective at producing quite realistic sentences trained from a quite small corpus. A year ago I wrote a simple twitter bot trained on Trump's tweet history and it was retweeted and favourited a suprising amount, and often by people who hadn't realized the joke. Some of this can definitely be chalked up to bots retweeting bots - but for me it was certainly a demonstration of their effectiveness.

In terms of state of the art text generation, they've been surpassed by LTSMs and other RNN variants that are able to learn to condition themselves on longer or shorter sequences with different weights or do things like include noise in the activations to allow for 'true' creativity - but the simple Markov chain model still has it's appeal.

This post will provide some cute, largely unoptimized Py3 code for building Markov chains from any iterable, outline exactly what a Markov chain does and demonstrate a unique application for creating novel portmanteaus (hybrid words) from two seperate corpora (I had to double check this was the correct pluralization for corpus, but I'm sticking to it.)

### Some Quick Markov Chain Code for Syntax Highlighting

{%highlight python%}
from itertools import islice
import random

class Chain:

    def __init__(self,corpus,seperator,ngram_size=3):
        self.seperator = seperator
        self.ngram_size = ngram_size
        self.begin_states = []

        ngrams = self.n_gramify_file(corpus,self.ngram_size)

        self.chain = self.build_markov_chain(ngrams)

    def make_n_grams(self,iter, n):
        offset_lists = (islice(iter,i,None) for i in range(n)) #creates a tuple of input iterable offset by 1 through n
        return zip(*offset_lists) # here we unpack that tuple and zip it back up into a list of n-grams

    def n_gramify_file(self,file,n):
        with open(file,'r') as file:
            for line in file:
                    yield self.make_n_grams(line,n)

    def build_markov_chain(self,ngrams):
        """Here we build a dict of dicts that represents our markov chain.
            - top level keys are all possible states
            - 2nd level keys are the possible predictions for each state.
            - values stored are a count of prediction frequency in text corpus
         """
        model = {}
        for ngram_list in ngrams:
            self.begin_states.append(next(ngram_list))  # append first ngram to valid begin states list
            for ngram in ngram_list:
                state = ngram[:-1] #state is all but the last item
                prediction = ngram[-1:] #prediction is the last item

                if state not in model:
                    model[state] = {}

                if prediction not in model[state]:
                    model[state][prediction] = 0

                model[state][prediction] += 1
        return model

    def generate(self):
        """This function returns a generator expression that will iterate through a walk down the chain given an
         initial state (if not provided will be chosen randomly). Once complete, the object will be discarded.
        """
        choice = random.choice(self.begin_states)
        while self.seperator not in choice:
            state = choice[-(self.ngram_size-1):]
            if self.seperator not in state:
                prediction = random.choice(list(self.chain[state]))
            else:
                prediction = self.seperator
            choice += prediction
        return ''.join(choice)


{%endhighlight%}
