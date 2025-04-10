---
layout: post
title: "How does Diffie-Hellman work?"
tags: Security
description: "Explaination of the Diffie-Hellman key exchange algorithm"
---
## How does Diffie-Hellman work?

*Funny announcer voice: In FIVE EASY STEPS, you too can create the Diffie-Hellman key exchange!!!*

### A short bit of history
Imagine you and your friend Bob are trying to exchange secret codes or keys to a cipher. Before Diffie Hellman and RSA, there weren't very many good ways to do that. On the internet, everything is accessible from everywhere. That's why Ralph Merkle made the Diffie-Hellman key exchange algorithm (No, it wasn't actually made by Whitfield Diffie and Martin Hellman, just named after them)

### Setup
I'm better at writing algorithms in Python, so I'll be using that for this.

### Step 1: The large prime number
Before you can do anything, you and Bob have to agree on a large prime number. Currently, a 2048 prime number is considered secure, so with a little bit of math, we can find out that a 2048 bit prime number would be a whopping **617 digits!** Yeah, have fun picking one of those. But just so you're sure you've really picked a prime number, here's a nice way to tell:

#### Euler's Totient
*As all the math nerds will tell you: No, This guy's name isn't pronounced "yoo-ler". It's pronounced "oiler".*

Euler created a wonderful function called Euler's Totient function, or more simply known as the phi function. I didn't exactly know what this function represented for the longest time until a friend made a pretty helpful simplification: Euler's Totient represents the *breakability* of a number. 

The function works by counting all the numbers that are coprime to the input number, n. For all you guys out there that don't know what coprime means, it basically means that the createst common factor of two numbers is one. Another way you may have heard this described is that a and b are *relatively prime.*
___
Here's a simple implementation of Euler's Totient in Python:

``` python
import math

def phi(n):
  count = 0r 
  for i in range(1, n):
    if math.gcd(i, n) == 1:
      count+=1
  return count
```
Oh, and if you're like me and somehow find it entertaining to do things with as few libraries as possible, we can eliminate the math library with our own implementation of the GCD function:
```
def gcd(a, b):
  if (a==0):
    return b
  return gcd(b%a, a)
```
There's definitely a better way to do this, because you normally want to *avoid* recursion, but this is a simple excercise, so I'm using simple code.
___
I bet you're wondering "Okay, but how does this tell us if a number is prime or not?" Well stop wondering, because I'm about to tell you :)

If the number the phi function returns is one less than n, or ```phi(n) == n-1```, then the number is prime. This indicates that every number except for n is coprime to n, which essentially means that n isn't divisible by any of the numbers that come before it.
___
### Step 2: The generator
*Finally, that wacko shut up about prime numbers...*

Oh, you thought we were done with prime numbers? HA! It gets worse from here!

The generator number is what's called a *primitive root* of our aforementioned large prime number. Primitive roots are an absolute PAIN to get. If you want to check out the code where I actually did this, look at the [github repo I wrote for this](https://github.com/Blooper7/Diffie-Hellman) It's a very long and intensive process, and I'm *still* not sure I got it completely right.

Just so you know what *insanity* you're dealing with, the prime number 2039 (an eleven bit number) has **1680 primitive roots.** Have fun finding them all, kiddo.
___
### Step 3: Private keys
Here's the easy part. Pick a number, any number. Cool, now that's your private key. It's literally as easy as that.
___
### Step 4: Public keys
*Who likes modular arithmetic? Not you? Too bad!*

To calculate your public key, you can use a pretty simple formula, where a is your private key, g is your agreed upon generator number, and p is your agreed upon large prime number

$$A = g^a mod p$$

Or in Python:  `privateKey = (g**a) % p`

Bob calculates their public key B using the same g and p values, but with their own randomly chosen private key value b.

$$B = g^b mod p$$

Cool! You and Bob have both of your private and public keys! The best part? You can totally distrubute your public keys over any insecure channel your want! Nobody can use them except for you and Bob!
___
### Step 5: Shared secret
*Dude, Diffie-Hellman is all about modular arithmetic. You didn't think we were done, did you?*

Finally, it's time to calculate the shared secret key. So, after waiting for an hour because Bob took a lunch break before calculating his keys, you now have his public key. After telling him that yes, hamburgers are *indubitably* better than hot dogs and anyone who says otherwise is simply incorrect, it's finally time to calculate your shared secret. This can be done about as simply as we calculated your public keys before.

Let's set up our equation. We need to find the shared secret K, and we have Bob's public key B, your private key a, and the large prime number that keeps coming back like a seagull that happened to spot the cheeseburger you got to show Bob what's up. The equation looks pretty similar to the one we used to generate the public keys:

$$K = B^a mod p$$

Bob does something similar using your public key A and his private key b:

$$K = A^b mod p$$

If all goes well, your K and Bob's K should come out to be the same number!
___
### Final words
Okay, that's it. Go use your powers for good :D
