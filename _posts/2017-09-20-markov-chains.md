---
layout: post
title:  "Markov Chain Portmanteaus"
date:   2017-09-10
categories: python
---

Simple Markov chains have been the workhorse of blogposts and websites about text generation for many years. They are suprisingly effective at producing quite realistic sentences trained from a quite small corpus. A year ago I wrote a simple twitter bot trained on Trump's tweet history and it was retweeted and favourited a suprising amount, and often by people who hadn't realized the joke. Some of this can definitely be chalked up to bots retweeting bots - but for me it was certainly a demonstration of their effectiveness.

In terms of state of the art text generation, they've been surpassed by LTSMs and other RNN variants that are able to learn to condition themselves on longer or shorter sequences with different weights or do things like include noise in the activations to allow for 'true' creativity - but the simple Markov chain model still has it's appeal. The subject of a later post may be a more formal comparison between what is happening in this style of Markov chain vs. an RNN model.

In this post I will extend the normal application of Markov chains by building novel words from two seperate corpora, but first a quick review:

The logic of Markov chains for text generation is quite simple - the theory is that by looking at the frequency that words or character n-grams follow each other in the corpus, we can create a probabalistic model of it. What the 'Markov' part of the name means is that it's a Markov process (memoryless) - so the prediction only depends on the current state. In this instance that means that we have a sliding window approach to the state, as it is only the last n-1 characters of the string generated - it doesn't care what path has brought the code to where it is, it simply looks at the previous 2 characters (in the case we're using tri-grams) and says "in the training corpus, these 2 characters were followed by an *e* 25% of the time and an *a* 75% of the time", and makes a selection based on that probability.

### Portmanteaus

So what I want to do is create a program that can take 2 sets of words and create novel combinations of them (or new words drawn from a similar distribution) that flow together smoothly.

So in terms of the Markov chain, what I want to do is this:

1. Start off with a walk down a chain from one corpus.
2. Continue this walk until I hit a state that occurs in the 2nd corpus's chain.
3. Transition to walking down a chain from the 2nd corpus, starting at this state.
4. If the transition state isn't found or a transition made, start over.

### The Code
{%highlight python%}
class Chain:

    def __init__(self,corpus,ngram_size=3):
        self.ngram_size = ngram_size
        self.begin_states = []

        ngrams = self.n_gramify_file(corpus,ngram_size)
        self.chain = self.build_markov_chain(ngrams)

    def make_n_grams(self,iter, n):
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
        """This returns a generator that will iterate through a walk down
        the chain given an initial state (if not provided will be chosen randomly
        from valid begin_states).
        """
        if begin_state == None:
            state = random.choice(self.begin_states)
        else:
            state = begin_state
            if state not in self.chain.keys():
                raise KeyError('Provided begin_state not found in model.')

        while state in self.chain.keys():
            prediction = self.predict(state)
            last_state = state
            state = state[1:] + tuple(prediction)
            yield last_state, prediction

{%endhighlight%}

A few quick notes on this code:

  - it's not highly optimized. The topic of a future post may be re-writing this for dealing with very large corpora quickly (likely using numpy arrays as transition tables instead of the dict structure, and better I/O reading.)

  - I've used generators at most points, so it should already be able to handle pretty unreasonably long strings without memory constraint issues. The other advantage of this approach is that it allows us to naturally walk down the first chain until we don't need it anymore.

  - the interface isn't quite polished - a good idea for extending this code would be to allow for a custom weight function for **predict** and better utilities for loading the corpus in different forms (different seperators, file formats, etc.). If refactoring I'd probably reorganize the way the functions are structed as well (lots of this logic could just happen in \_init_ and still be quite readable, but when I started writing this I wasn't totally sure where I was going with it.)

And here's a quick and dirty function that will handle the logic for transitioning between chains. Right now it doesn't use the frequency information for deciding when to transition, but just walks down the first chain until it finds any overlap.

{%highlight python%}
def Pormanteau(chain1,chain2):
    """Here we manage the logic of blending the two generators together"""

    #here we build the transition list, with a count of occurences in both chains
    transition_list = {}
    for key in chain1.chain:
        if key in chain2.chain:
            if key not in transition_list:
                transition_list[key] = 0
            transition_list[key] += 1

    not_transitioned = True
    #this walks down the first list until it transitions, then down the second
    while not_transitioned:
        word = []
        first_walk = chain1.generate()
        for step in first_walk:
            state = step[0]
            if state not in transition_list:
                word.append(step)
            else:
                second_walk = chain2.generate(state)
                for step in second_walk:
                    word.append(step)
                not_transitioned = False
                break

    string = []

    #we build a string out of the list of steps.
    for i,list in enumerate(word):
        #this logic is to include both the state & prediction for the first step
        if i == 0:
            string.append(str.join('',list[0]))
            string.append(str.join('', list[1]))
        else:
            #add just the prediction to the string for later steps
            string.append(str(list[1]))

    string = str.join('',string)

    return string
{%endhighlight%}

## The Results

Here are some example outputs given different training corpora. These are all done on 4-gram chains (meaning it looks for a shared state of length 3 to transition.) Using corpora with distinct patterns makes the most obvious use of this technique, which is why I've selected the ones I have here.

| Countries+Pokemon | Fruits+Countries| Countries+Elements  |
|  -------------|-------------   | -----|
| Yemence     | Lychelles        | Luxembodium |
| Cubonee     | Duritan          | Afghanum |
| Ghandour    | Prunei           | Guine |
| Kyrgyzsta   | Blackcurra Leone | Philicon |
| Grenipede   | Mangladesh       | Kyrgyzstatine |
