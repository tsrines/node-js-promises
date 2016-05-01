Promises
========

## Overview

In this lesson, we will examine an exciting new(-ish) tool for managing asynchrony called **Promises**. Promises are rapidly becoming the contemporary standard for javascript programming, and have now officially become part of the ES6 javascript specification. By the end of this lesson, you will be able to:

1. Explain what a Promise is.
2. Explain the advantages of Promises over the Async library.
3. Write asynchronous code using Promises.

## Another Async Pattern!?

Right!? We're getting confused here. Why do we need one more way to manage asynchrony? Isn't the async library sufficient?

Excellent point. As we saw in the last lesson, the the async library really helped us flatten down the awful nested hierarchies and restore a sense of step-wise logic to our asynchronous code. The async library is a really nice tool worthy of our appreciation.

And yet the the async library does have weaknesses. Its weaknesses, though, are probably the best kind to have because they are weaknesses that arise from its main strength: namely, it provides a beautiful but simple interface (or layer) on top of the basic pattern of callbacks. The beauty of this is that as long as you're familiar with callbacks you can pretty quickly start to use the async library. You'll still be writing callbacks.

But this is precisely the problem with the async library. When we use it we are still essentially writing callbacks, and we are still locked into some of their limitations. What if we could do better? What if, for instance, we had a way to handle asynchrony that allowed us to reason about the values and the events before they were present? Now that would be something! This is probably a fairly abstract idea at this point, and that's entirely appropriate. Let's break it down a bit.

## What's in a Promise?

A good way to start understanding Promises is actually just to pay some attention to their name. What's in a promise? We use this term in our everyday life all the time, but let's think about what it might mean if we were to think of our life algorithmically (which thankfully we don't need to most of the time!) If you ponder on it a bit, I think you'll agree that when we talk about making promises we are really talking about asynchrony again.

"I promise I'll pay you back your $10 dollars." If I make such a promise to my friend, what I'm saying is that an ascynhronous process has begun that when it has completed will result in them having $10 dollars.  She doesn't know when that money will arrive, only that it will at some point. Taking this a bit further, if I were to give her a symbol of this promise, an IOU, that IOU would represent the value (the future value, if you will) of my promise once I have made good on it.

Now we are getting pretty close to understanding javascript Promises. At its most basic, a javascript Promise is really just a special kind of object that makes two promises to us. These promises are:

1. I, Promise object, promise to tell you when I have either fulfilled (resolved) or failed to fulfill (rejected) my promise to you.
2. When I fullfill (resolve) my promise or fail to fullfill (reject) my promise to you, I will provide you with the specified value (if you've asked for one).



## Resources

* The [Promise/A=](https://promisesaplus.com/) Specification.


Points to get made:

* Benefits of Promises: 1) sequentiality; 2) uninversion of control. 
*
