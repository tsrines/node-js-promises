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

But this is precisely the problem with the async library. When we use it, we are still essentially writing callbacks, and we are still locked into some of their limitations. What if we could do better? What if, for instance, we had a way to handle asynchrony that allowed us to reason about the values and the events before they were present? Now that would be something!

This is probably a fairly abstract idea at this point, and that's entirely appropriate. Let's break it down a bit.

## What's in a Promise?

![Pinky Promise Animated Gif](https://s3.amazonaws.com/ezmiller/public/flatiron-imgs/promises.gif)

A good way to start understanding Promises is actually just to pay some attention to their name. What's in a promise? We use this term in our everyday life all the time, but let's think about what it might mean if we were to think of our life algorithmically (which thankfully we don't need to most of the time!) If you ponder on it a bit, I think you'll agree that when we talk about making promises we are really talking about asynchrony again.

![Ciara Promises Animated Gif](https://s3.amazonaws.com/ezmiller/public/flatiron-imgs/promise-asynchrony.gif)

"I promise I'll pay you back your $10 dollars." If I make such a promise to my friend, what I'm saying, essentially, is that an ascynhronous process has begun that when it has completed will result in them having $10 dollars.  She doesn't know when that money will arrive, only that it will at some point. Taking this a bit further, if I were to give her a symbol of this promise, an IOU, that IOU would represent the value (the future value, if you will) of my promise once I have made good on it.

Now we are getting pretty close to understanding javascript Promises. At its most basic, a javascript Promise is really just a special kind of object that makes two promises to us. These promises are:

1. I, Promise object, promise to tell you when I have either fulfilled (resolved) or failed to fulfill (rejected) my promise to you.
2. I, Promise object, promise to provide you with the specified value(s), if specified, at the moment that I tell you that I've fulfilled my promise; or, if I fail to fulfill my promise, I will give you a reason why.

Okay, so that's what a promise is conceptually. Now let's look at what that looks like in code. When we use Promises in our code we usually call a function that has been designed to return a Promise object. We'll go a bit more deeply into this in a bit, but for the moment let's just say we have a function `makeLoan(amountInUSD, interestRate)` that takes a promise and when it has finished supplies the amount of the original loan plus the correct interest earned. This is how we'd use that function:

```javascript
makeLoan('10', '.05')
  .then((amountPaid) => {
    console.log(amountPaidback);
  }).catch((reason) {
    console.log(reason);
  });
```

This is a very simple example, but it expresses the core of the promises that a javascript Promise makes. The first promise (#1 above) is evident in the "chained" `then()` and `catch()` functions that are called after on the Promise object returned by `makeLoan`; the second (#2 above), is present in the argument `amountPaidback` supplied to the anonymous function supplied to `then`.

If you're feeling confused by the "chaining" here, remember that we've seen this before when we used jQuery, e.g. `$('.some-div').show().hide()`. All that's happening here is that the `show` and `hide` functions are being called on the object that is returned by each previous method in the chain of functions. The jQuery API guarantees that each function returns objects that contain the methods in its library.

Similarly, the Promises specification guarantees that all Promises will have a `then` method that when called returns the Promise object in question. Indeed, the [Promise/A+](http://promisesaplus.com) specification, which is the most influential Promises spec, defines promises in terms of the then method, stating bluntly: "[A] 'promise' is an object or function with a then method...."[^1]. And a then method, the spec continues, is just a method that "registers callbacks to receive either a promise's eventual value or the reason why he promise cannot be fulfilled." 

But wait, you say, what about the `catch` function? Good point. Well, if you noticed in the above definition, the spec says that the `then` method allows us to register *multiple* callbacks -- yes we are still using callbacks -- and one of these callbacks takes the eventual value, while the other accepts a reason the promise failed. The actual signature of the `then` function therefore is: `then(onFulfilled, onRejected)`. And the `catch` method is just a shorthand for calling `then` with only a rejection callback handler, like so: `then(null, rejectionCallback)`.

Okay, so that's pretty much it! Now you know, basically, what a promise is. Let's use them!

## Code-Along:

## Resources

* The [Promise/A+](https://promisesaplus.com/) Specification.


[^1]: https://promisesaplus.com/#point-6

Points to get made:

* Benefits of Promises: 1) sequentiality; 2) uninversion of control. 

