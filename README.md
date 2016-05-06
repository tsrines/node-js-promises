Promises
========

## Overview

In this lesson, we will examine an exciting new(-ish) tool for managing asynchrony called **Promises**. Promises are rapidly becoming the contemporary standard for javascript programming, and have now officially become part of the ES6 javascript specification. By the end of this lesson, you will be able to:

1. Explain what a Promise is.
2. Explain the advantages of Promises over the Async library.
3. Write asynchronous code using Promises.

## Another Async Pattern!?

Right!? We're getting confused here. Why do we need one more way to manage asynchrony? Isn't the async library sufficient?

Excellent point. As we saw in the last lesson, the async library really helped us flatten down the awful nested hierarchies and restore a sense of step-wise logic to our asynchronous code. The async library is a really nice tool worthy of our appreciation.

And yet the async library does have weaknesses. Its weaknesses, though, are probably the best kind to have because they are weaknesses that arise from its main strength: namely, it provides a beautiful but simple interface (or layer) on top of the basic pattern of callbacks. The beauty of this is that as long as you're familiar with callbacks you can pretty quickly start to use the async library. You'll still be writing callbacks.

But this is precisely the problem with the async library. When we use it, we are still essentially writing callbacks, and we are still locked into some of their limitations. What if we could do better? What if, for instance, we had a way to handle asynchrony that allowed us to reason about the values and the events before they were present? Now that would be something!

This is probably a fairly abstract idea at this point, and that's entirely appropriate. Let's break it down a bit.

## What's in a Promise?

![Pinky Promise Animated Gif](https://s3.amazonaws.com/ezmiller/public/flatiron-imgs/promises.gif)

A good way to start understanding Promises is actually just to pay some attention to their name. What's in a promise? We use this term in our everyday life all the time, but let's think about what it might mean if we were to think of our life algorithmically (which thankfully we don't need to most of the time!) If you ponder on it a bit, I think you'll agree that when we talk about making promises we are really talking about asynchrony again.

![Ciara Promises Animated Gif](https://s3.amazonaws.com/ezmiller/public/flatiron-imgs/promise-asynchrony.gif)

"I promise I'll pay you back your $10 dollars." If I make such a promise to my friend, what I'm saying, essentially, is that an asynchronous process has begun that when it has completed will result in them having $10 dollars.  She doesn't know when that money will arrive, only that it will at some point. Taking this a bit further, if I were to give her a symbol of this promise, an IOU, that IOU would represent the value (the future value, if you will) of my promise once I have made good on it.

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

But wait, you say, what about the `catch` function? Good point. Well, if you noticed in the above definition, the spec says that the `then` method allows us to register *multiple* callbacks -- yes we are still using callbacks -- and one of these callbacks takes the eventual value, while the other accepts a reason the promise failed. The actual signature of the `then` function therefore is: `then(onFulfilled, onRejected)`. And the `catch` method is just a shorthand for calling `then` with only a rejection callback handler, like so: `then(null, rejectionCallback)`. The code above, in other words, could also have been written like this:

```javascript
makeLoan('10', 0.5')
  .then((amountPaid) => {
    console.log(amountPaidback)
  }).then(null, (reason) => {
    console.log(reason);
  });
```

Okay, so that's pretty much it! In sum: A promise is an object or function with a then method. The then method allows you to 1) know when a Promise has either been fulfilled or rejected and 2) to know either the value that resulted from its fulfilment, or the reason it failed. More generally, Promises provide us with the tools to write asynchronous code synchronously and to reason clearly about future values. Now we know, basically, what's in a Promise. So let's use them!

## Code-Along: Using Promises to Database I/O

In this code-along, what we'll do is play around with some async operations using a database and also http requests. The scenario is that we've been tasked by a client with collecting some data about flea markets in the United States. And it just so happens that the United States Department of Agriculture has provided an open source dataset with an API that we can query to collect some of this data. So what we'll be doing is writing a simple javascript script to pull data from the web and insert into a Postgres database. 

### 1. Getting Going

Let's begin by getting things setup and going through some preliminary code already provided. Your lesson directory includes a package.json file with the necessary dependencies already supplied so we can just do an `npm install` to get things started. Go ahead and do that now.

Now if you open `promises-example.js`, you'll see that there's some code already present. In this code-along, we'll be using a library called [knex](http://knexjs.org) in connection with another library called [pg]() for our database input/output (I/O) operations. The pg library is easy: it provides an interface the Postgres database that we'll be using for this exercise. The knex library, which is the library that we'll really be using, adds an additional layer on top of pg, which further simplifies (or "abstracts") us away from the specifics of the database we are using.

> Note: The knex library is what we call an ORM (or Object Relational Mapping) tool. You'll likely run into other ORMs in your career. Basically, what they do is provide a "virtual" database object that can be manipulated within a programming environment using a syntax that is distinct from the one the actual database uses. ORMs provide two important advantages: 1) The language or API that they provide for manipulating the database can be made to be easier to understand and read (or what programmer's call "expressive"); 2) because they provide an independent API for the database, ORMs can usually be used interchangeably with different types of databases. That means you can change your database withotu needing to change your code!

The first set of lines of code already present in the `promises-example.js` file (until about Line 17) configure our database connection. As this configuration is unique to knex itself, let's just accept it for now. The next it of code is a function that we've provided that can be used to query the Department of Agriculture's farmer's market API. 

This function is interesting. Do you see why? Take a look at it for a second.

It's interesting because it has been setup to return a Promise! Let's examine how it works:

```
var queryUSDAFarmersMarkets = (zipCode) => {
  return new Promise((resolve, reject) => {
    const baseUrl = 'http://search.ms.usda.gov/farmersmarkets/v1/data.svc/zipSearch?zip=';
    request(baseUrl + zipCode, (err, resp, body) => {
      if (err) reject(err);
      resolve(body);
    });
  });
};
```

What we have here is function declaration for `queryUSDAFarmersMarkets` that takes a zip code as an argument. This function, however, immediately returns promise object generated by calling the Promise constructor `new Promise()`. The Promise constructor, in turn, takes a callback function with two arguments: `resolve` and `reject`. These arguments, which are callbacks themselves, are called when the Promise is either resolved (meaning it has reached its end state) or there's been an error in which case it's rejected. These function, in other words, allow us to either return the final value of the Promise, or provide a reason why it has failed. 

In the logic of our function, what we are doing is using the *request* module to call the API. Once the request has returned, we then check for an error in the request. If there's been an error we call `reject` and supply the error as the "reason" for the Promises failure. If, on the other hand, the request completed successfully we call `resolve` and supply the response body as the Promises final resolved value. Pretty logical and easy to understand, no? Hopefully, seeing the internals of a function that has been "promisified" helps to clarify how Promises end up either failing or resolving.

### 2. Taking our Custom Promies for a Spin (Plus Learning about Promise Error Handling)

But now let's get to the point where we can use this Promise! Let's first just get ourselves to the point where we are getting a response from the USDA servers by fetching a list of the farmer's markets near the Flatiron School. This will be useful because we can see in what for the data comes back. Open our file, enter the following code, and then do `node promises-example.js` to run it:

```
queryUSDAFarmersMarkets(10004)
  .then((respBody) => {
    console.log('response', JSON.parse(respBody));
  })
  .then(null, (err) => {
    console.log('error: ', err);
  });
```

So what did you see? What you should have gotten, in fact, is an error! Something like this:

```
{ [Error: getaddrinfo ENOTFOUND search.ms.usda.gov search.ms.usda.gov:80]
  code: 'ENOTFOUND',
  errno: 'ENOTFOUND',
  syscall: 'getaddrinfo',
  hostname: 'search.ms.usda.gov',
  host: 'search.ms.usda.gov',
  port: 80 }
```

You're getting this error because we built an error into the queryUSDAFarmersMarket function so that we can see how errors work in Promises land. Basically, what has happened here is that the URL was wrong. There's a letter missing from the url and so the request is throwing an error. As a result, our promisified function is calling `reject` with the error. When `reject` is called, all the following `then` functions containing only callbacks for the Promises's resolution are skipped and the first `then` function with a rejection handler callback is called.

> Note: Remember that the `catch` method on a Promise is just a shorthand for a `then` function called with only an rejection handler callback, i.e. `then(null, rejectionHandlerCb)`. As such, the code above could also have been written with the `catch` method.

Before we move on, we need to discover a very important characteristic -- indeed, it's arguably a flaw! -- of Promises. To see this flaw, let's go back to this code block in our file, and remove the second `then` that is catching the Promises rejection reason; then run the code again. Do you see what happened? Absolutely, nothing. The promise ate our error! As a result, we have no idea what's going wrong. All we know is that we haven't seen our data as expected. 

This is a BIG (one might even say YUUGE) problem. At the moment, we can keep track of what is going on with our code because our program is tiny. But what if we had a Promise that was eating an error in a large application with tens of thousands of lines of code? This would be bad. Our team might take ages to track the bug down. Not good. So what's the lesson here? The lesson is: when writing Promises, we should always ask ourselves if we have included either `then` or `catch` functions that will catch the error. Okay, warning made. Let's move on.

In order to correct the error in our code, change the url in queryUSDAFarmersMarkets so that the rood address reads: search.ams.usda.gov. There was a missing 'a' in 'ams'. See how easy it is to produce a bug! Once you've fixed this bug, run our script again. You should now (finally!) see some data.

```
{ results:
   [ { id: '1000066', marketname: '0.1 Bowling Green Greenmarket' },
     { id: '1000082',
       marketname: '0.2 Staten Island Ferry/Whitehall Greenmarket' },
     { id: '1006003', marketname: '0.3 Cedar St. Greenmarket' },
     { id: '1007282', marketname: '0.5 New Amsterdam Market' },
     { id: '1000070', marketname: '0.6 Downtown PATH Greenmarket' },
     { id: '1000067', marketname: '0.7 City Hall Park Greenmarket' },
     { id: '1003457',
       marketname: '0.8 Maria Hernandez Park Farmers Market' },
     { id: '1000079', marketname: '0.8 Tribeca Greenmarket' },
     { id: '1005109',
       marketname: '1.3 Added Value Farm Stand at Governors Island' },
     { id: '1000100',
       marketname: '1.4 Brooklyn Borough Hall Greenmarket' },
     { id: '1002180', marketname: '1.7 Lower East Side Youthmarket' },
     { id: '1008909', marketname: '1.8 Lower East Side YM' },
     { id: '1006021', marketname: '1.8 Carroll Gardens Greenmarket' },
     { id: '1005349', marketname: '1.9 HDSID Farmer\'s Market' },
     { id: '1003900', marketname: '2.1 Van Vorst Farmers\' Market' },
     { id: '1000078', marketname: '2.1 Tompinks Square Greenmarket' },
     { id: '1000058', marketname: '2.1 Fort Green Park Greenmarket' },
     { id: '1000076',
       marketname: '2.2 St. Mark\'s Church Greenmarket' },
     { id: '1005108', marketname: '2.2 Red Hook Farmers Market' } ] }
```

Okay, so there we have it: our farmer's market data. So basically what we get back from the USDA servers is a string in JSON form (we've parsed it above by using the `JSON.parse` method). This JSON is an object with one main property `results` that contains an array of other objects. The objects in the `results` array contain an `id` and a `marketname` specify the market.

### 3. Completing Our Script

Now that we have our data, let's set up our database code.

Here's where we'll starting using the knex library. In addition to the high level abstraction that it provides as an ORM, the other nice thing about knex is that it's API methods are all promisified. So whereas we'd normally have to interact with our database using callbacks (because database operations are almost always asynchronous), here we can use Promises instead. Nice!

So let's do this. Let's start by commenting out or removing the code we were just playing with, and starting over. Again, let's think about what are steps are. What we need to do is:

1. Check to see if we have the needed table in our database; if not, create it.
2. Fetch the list of markets from the USDA servers.
3. Insert each USDA market object into our database as an individual row.
4. Check to see if we succeeded by fetching all the records in our table.

Let's begin by getting through step 1. The knex API provides a very (very!) nice function that allows us to check to see if a table exists and if not create it. Want to guess what its' called? It's really tricky:

```
Promise.resolve(knex.schema.createTableIfNotExists('markets', (table) => {
   table.string('id').primary();
   table.string('marketname');
}));
```

Yep, the function is called `createTableIfNotExists`. Now that's expressive! No ambiguity about what that function does. But there are some patterns above that might be a bit more confusing. What's this `Promise.resolve` thing? 

`Promise.resolve` is a standard function made available by the Promises library that returns a Promise and is resolved with the supplied value. In this case, unlike the knex library's other functions, createTableIfNotExists does not return a Promise. But that's not a problem because we can wrap in a promise with the `resolve` method. Then we can call the then function on it anyway to start the next step.

But let's proceed. Now that we've got our table being created -- if not exists already -- we can now fetch our records from the USDA API as before in the next step:

```
Promise.resolve(knex.schema.createTableIfNotExists('markets', (table) => {
   table.string('id');
   table.string('marketname');
}))
  .then(() => {
    return queryUSDAFarmersMarkets(10004).then((respBody) => {
      return JSON.parse(respBody).results;
    });
  })
  .then((markets) => {
    console.log('markets: ', markets);
  })
  .catch((err) => {
     console.log(err);
  });
```

Now if you run this code, we should be more or less back where we started, except for our table in the database. But let's look at what's going on here because we are starting to see the wonderfuly way that Promises normalize synchronicity. Look how easy this is to read. First we `createTableIfNotExists`; then inside our next then function callback we call our promisified queryUSDAFramersMarket function, and return its result to the next `then` method, passing in the results as the variable `markets`. These results we then output to the console.

Important: Notice that in the code above we `return` this promisified function, and then in its then function we return the results of the response. This is important! If we don't do this, the result of our request won't be passed on to the next `then` method, where we output the result of our request that has been passed in as the variable `markets`. If you want to experiment a bit, you can see what happens to our console.log output if you remove the return before the `queryUSDAFarmersMarkets` method call.

But let's move on to our final step. What we need to do now, of course, is save these entires in our database. In order to do this, we'll use another built-in promise method: `Promise.all`. This function takes an array of Promises, and resolves itself only when all the Promises in the array have been resolved. The results of each Promise (if any) are the passed to the next `then` method callback. This is extremely powerful! And it works particularly well with the array map function like so:

```
Promise.all(markets.map((market) => {
  return knex('markets').insert(market);
}));
```

When we call map on the array `markets`, which contains our array of market objects, map runs the specified anonymous function as many times as there are market objects, each time passing the individual market object in as an argument.

Then comes the crucial step! Our anonymous function returns the result of the call to `knex('markets').insert(market)`. So what is this call? Well, it's just the knex way of inserting records into a table. The table in question is our 'markets' table (`knex('markets')`), which we then insert our market data into with `insert(market)`. Because knex has been promisified the result of this chained function call is nothing other than a promise, and the result of the cap call is an array of Promises. Putting this all together then, and adding a final call to check the contents of our table after the insert, we can do this:

```
Promise.resolve(knex.schema.createTableIfNotExists('markets', (table) => {
  table.string('id');
  table.string('marketname');
}))
  .then(() => {
    console.log('Fetching list of markets from USDA server...');
    return queryUSDAFarmersMarkets(10004).then((respBody) => {
      return JSON.parse(respBody).results;
    });
  })
  .then((markets) => {
    console.log('Inserting query results into the db...');
    return Promise.all(markets.map((market) => {
      return knex('markets').insert(market);
    }));
  })
  .then((result) => {
    return knex.select('*').from('markets').then((result) => {
      console.log('Now our markets table has data in it!');
      console.log('Contents of markets table: ', result);
    });
  })
  .catch((err) => {
    console.log(err);
  });
```

Now when you run this code you should see that the market table now contains the data we fetched from the USDA's servers. Hoorah! Do you see how we fetched the results from the database with another knex call? This code could all be written more concisely, but it exhibits nicely the way Promises allow us to pass the results of one function along into the next. Combined with the `then` method which allows us to reason clearly about the order of operations, Promises help us reason synchronously about our asynchronous processes.

## Resources
 
* The [Promise/A+](https://promisesaplus.com/) Specification.
* Kyle Simpson, ["Promises"](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20&%20performance/ch3.md) in You Don't Know JS: Async & Performance. 

[^1]: https://promisesaplus.com/#point-6

