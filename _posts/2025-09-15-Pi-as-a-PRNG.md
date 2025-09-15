---
layout: post
title: "Pi as a PRNG?"
tags: Cryptography
description: "An experiment with using pi as a PRNG"
---

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
The results of all this should be `0.9989835358466762`. For reference, we can find the optimal probability by feeding a completely even distribution into `entropy()`, which gives us a perfect 1.0. So our entropy is nearly perfect!

## Step 2: SUPER simple PRNG

This may surprise you, but I'm not a cryptographer. Basically, my concept of encryption is to scramble things up so bad it's unintelligible. As such, I don't really know what I'm doing. I am, however, a math nerd, so let's see what we can concoct.

So, we know that pi has high entropy, so let's use it as the core.
```python
def pi_prng(n):
    return PI[n]
```
Just kidding. We're totally doing more than that.

What we have simply returns a one digit number from 0-9. That's not exactly very complex. So what if we allowed for even bigger numbers? Let's try something like this:
```python
def pi_prng(n,size):
    chunk=PI[n:n+size]
    out=0
    for d in chunk:
        out=(out*10)+d
    return out
```
That's more like it!

...Or maybe not.

See, most PRNGs generate between 0 and 1. This lets the user determine the range of the generation when they use it. Want a range from 0 to 1000? Just multiply your randomly generated value between zero and one by 1000! So how can we make our PRNG do this? How about some extremely simple normalization? Let's also compute a maximum value, then normalize our pi value.
```python
def pi_prng(n,size):
    chunk=PI[n:n+size]
    out=0
    mx=0
    for d in chunk:
        out=(out*10)+d
        mx=(mx*10)+9 # This creates a maximum value of all nines
    return out
```
Great! Now we're getting some numbers between zero and one. Let's see what the first ten values are when the size is four.
```
0 : 0.31413141314131415
1 : 0.1415141514151415
2 : 0.41594159415941595
3 : 0.15921592159215922
4 : 0.5926592659265927
5 : 0.9265926592659266
6 : 0.26532653265326533
7 : 0.6535653565356536
8 : 0.5358535853585359
9 : 0.35893589358935896
```
Wow! That's a lot of numbers! Let's try to actually make sense of them. None of the numbers are duplicates, so we're safe there. Let's compute the average to see if we're biased in any direction. The closer to 0.5, the better. Our average comes out to be about 0.44. That's pretty good for only ten inputs. Let's test it over 1000.

I'm not actually going to put all the numbers here, because that's a lot of numbers, but the average came out to be 0.4967. That's actually extremely promising!

## Conclusion

Pi is actually a really good base for a PRNG! There are definitely some things I could have done to make it better, but this was intended to be a pretty short experiment, so I didn't. Off the top of my head, another feature we could add is adding a unique number in based off of our inputs, like `PI[n]/n` to give us a sort of identifier for our numbers based on their pi-index and value. Feel free to use my code from above and experiment with it yourselves!
