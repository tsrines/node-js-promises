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



## Resources

* The [Promise/A=](https://promisesaplus.com/) Specification.


Points to get made:

* Benefits of Promises: 1) sequentiality; 2) uninversion of control. 
*
