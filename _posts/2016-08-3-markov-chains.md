---
layout: category-post
title:  "Markov Chain Portmanteaus"
date:   2017-09-10
categories: python
---

Simple Markov chains have been the workhorse of blogposts and websites about text generation for many years. They are suprisingly effective at producing quite realistic sentences trained from a quite small corpus. A year ago I wrote a simple twitter bot trained on Trump's tweet history and it was retweeted and favourited a suprising amount, and often by people who hadn't realized the joke. Some of this can definitely be chalked up to bots retweeting bots - but for me it was certainly a demonstration of their effectiveness.

In terms of state of the art text generation, they've been surpassed by LTSMs and other RNN variants that are able to learn to condition themselves on longer or shorter sequences with different weights or do things like include noise in the activations to allow for 'true' creativity - but the simple Markov chain model still has it's appeal.

This post will provide some rough, largely unoptimized Py3 code for building Markov chains from any iterable, outline exactly what a Markov chain does and demonstrate a unique application for creating novel portmanteaus (hybrid words) from two seperate corpora (I had to double check this was the correct pluralization for corpus, but I'm sticking to it.)

### The Code
{%highlight python%}
from itertools import islice
import random

class Chain:

    def __init__(self,corpus,seperator,ngram_size=3):
        self.seperator = seperator
        self.ngram_size = ngram_size
        self.begin_states = []

        ngrams = self.n_gramify_file(corpus,ngram_size)
        self.chain = self.build_markov_chain(ngrams)

    def make_n_grams(self,iter, n=3):
        """Here we create a tuple of the the input iterable offset by 1 through n.
        We then zip up these offset iterables to create the desired n-grams."""
        offset_lists = (islice(iter,i,None) for i in range(n))
        return zip(*offset_lists)

    def n_gramify_file(self,file,n):
        """Simple helper function to load ngrams in from a file."""
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
            for i, ngram in enumerate(ngram_list):
                state = ngram[:-1] #state is all but the last item
                if i == 0: #append first ngram to valid begin states list
                    self.begin_states.append(state)
                prediction = ngram[-1] #prediction is the last item

                if state not in model:
                    model[state] = {}

                if prediction not in model[state]:
                    model[state][prediction] = 0

                model[state][prediction] += 1
        return model

    def predict(self,state):
        """Chooses the prediction given the state, using the frequency
        of occurrence to determine it's chance."""
        offset = random.randint(0, sum(iter(self.chain[state].values())) - 1)
        for key, value in iter(self.chain[state].items()):
            if offset < value:
                return key
            offset -= value

    def generate(self,begin_state=None):
        """This function returns a generator that will iterate through a walk down
        the chain given an initial state (if not provided will be chosen randomly).
        """
        if begin_state == None:
            state = random.choice(self.begin_states)

        while state in self.chain.keys():
            prediction = self.predict(state)
            last_state = state
            state = state[1:] + tuple(prediction)
            yield last_state, prediction
            
{%endhighlight%}

A few quick notes on this code:

  - it's not highly optimized. The topic of a future post may be re-writing this for dealing with very large corpora quickly (likely using numpy arrays as transition tables instead of the dict structure, and better I/O reading.)

  - I've used generators at most points, so it should already be able to handle pretty unreasonably long strings without memory constraint issues. The other advantage of this approach will be seen in section 2.

  - the interface isn't quite polished - a good idea for extending this code would be to allow for a custom weight function for **predict** and better utilities for loading the corpus in different forms (different seperators, file formats, etc.)

The logic of Markov chains for text generation is quite simple - the theory is that by looking at the frequency that words or characters follow each other in the corpus, we can create a probabalistic model of it. What the 'Markov' part of the name means is that it's a Markov process (memoryless) - so the prediction only depends on the current state. In this instance that means that we have a sliding window approach to the state, as it is only the last n-1 characters of the string generated - it doesn't care what path has brought the code to where it is, it simply looks at the previous 2 characters (in the case we're using tri-grams) and says "in the training corpus, these 2 characters were followed by an *e* 25% of the time and an *a* 75% of the time", and makes a selection based on that probability.

###Portmanteaus

To return to the title of the post, and my original intentin in writing this code - what I want to do is create a program that can take 2 sets of words and create novel combinations of them that flow together smoothly. I enjoy word-play and automating my humour, and this is a step down that path.

So in terms of the Markov chain, what I want to do is this:

1. Start off with a walk down a chain from one corpus.
2. Continue this walk until I hit a state that occurs in the 2nd corpus's chain.
3. Transition to walking down a chain from the 2nd corpus, starting at this state.
4. If the transition state isn't found or a transition made, start over.
