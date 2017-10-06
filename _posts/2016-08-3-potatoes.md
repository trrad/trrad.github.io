---
layout: category-post
title:  "Potatoes"
date:   2015-11-25
categories: writing
---

Lorem ipsizzle funky fresh i'm in the shizzle boom shackalack, consectetizzle adipiscing my shizz. Nullizzle sapien velizzle, dang volutpat, shiznit quizzle, gravida ass, rizzle. Pot get down get down tortor. Sed erizzle. Black go to hizzle dolizzle dapibizzle turpis fo shizzle my nizzle yo. Maurizzle pellentesque nibh et check it out. Bow wow wow check it out tortizzle. Pellentesque for sure rhoncizzle bow wow wow. In owned habitasse brizzle dictumst. Nizzle dapibizzle. Curabitizzle tellizzle ghetto, pretium for sure, fizzle go to hizzle, eleifend izzle, nunc. Dope suscipizzle. Integizzle boom shackalack velit ass purus.

### Some Quick Markov Chain Code for Syntax Highlighting

{%highlight python%}
from itertools import islice
import random

def make_n_grams(self,iter, n):
    offset_lists = (islice(iter,i,None) for i in range(n)) #creates a tuple of input iterable offset by 1 through n
    return zip(*offset_lists) # here we unpack that tuple and zip it back up into a list of n-grams

def n_gramify_file(self,file,n):
    with open(file,'r') as file:
        for line in file:
                yield self.make_n_grams(line,n)

def build_markov_chain(self,ngrams):
    """Here we build a dict of dicts
        - top level keys are all possible states
        - 2nd level keys are the possible predictions for each state.
        - values stored are a count of prediction frequency in text corpus
     """
    model = {}
    for ngram_list in ngrams:
        for ngram in ngram_list:
            state = ngram[:-1] #state is all but the last item
            prediction = ngram[-1:] #prediction is the last item
            #print(state,prediction,ngram)

            if state not in model:
                model[state] = {}

            if prediction not in model[state]:
                model[state][prediction] = 0

            model[state][prediction] += 1
    return model

{%endhighlight%}
