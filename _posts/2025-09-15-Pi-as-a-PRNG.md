---
layout: post
title: "Pi as a PRNG?"
tags: Cryptography
description: "An experiment with using pi as a PRNG"
---
## Pi as a PRNG?

I've been curious about something for a while: pi is irrational, right? That means no repeating digit sequences. That being said, could it be used as a PRNG of sorts? Well, we're about to find out!

## Step 1: Entropy Calculations

The first step in all this is to make sure pi is actually suitable as a PRNG. This involves making sure that the digits themselves have plenty of entropy. To do this, I decided to make a frequency map of all the digits, then turn them into probabilities.

Here's a few lines of python to set up the test for us:
```python
from mpmath import mp
from scipy.stats import entropy
import numpy as np
mp.dps=1000 # Let's start small

PI=''.join(str(mp.pi).split(".")) # Convert pi into a string of all the digits
freq=[0,0,0,0,0,0,0,0,0,0]
```
Cool. We have everything set up. Actually calculating the frequencies is easy. We'll just use a for loop.
```python
for i in PI:
    freq[int(i)]+=1
```
Converting this into probabilities is pretty easy as well. We just have to divide the frequency of a number by the sum of all frequencies (aka every instance in the list)
```python
prob=[i/sum(freq) for i in freq] # probably a bad idea, but I implemented my own list sum function
```
Finally, we can compute the entropy of all this using SciPy
```python
np_prob=np.array(prob)
ent=entropy(np_prob, base=10)
```
The results of all this should be `0.9989835358466762`.
