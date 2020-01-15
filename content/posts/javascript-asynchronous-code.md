---
title: "Asynchronous Code In JavaScript"
date: 2020-01-15T14:03:24-06:00
draft: true
tags: [ 'javascript', 'async', 'promise', '100 level' ]
---


## What Is Asynchronous Code and When To Use It?

For many developers the thought of "Asynchronous Code" conjours one of two spectres.  One spirit capable of mysteriously unlocking untold amounts of performance by shipping tasks off to some fabled 'background thread'; and the other a ghoul of odd behaviours and synchronization problems.  By the end of this article I hope to banish both of these ghosts, and replace them with a solid understanding of 'async' code, and just how it works in JavaScript.

> We're *not* going to be diving deep under the hood here.  This is about practical understanding and examples that can help you get over async superstitions.

> We're also not going to go into *Web Workers* as they're more of a '200 level' article.

First of all, if you're coming from desktop development: simple async code in JavaScript is much simpler than it is on a desktop.  That simplicity stems from the fact that JavaScript's async code isn't about starting up multiple threads to improve performance.  Instead, it's about un-blocking the *single thread* that JavaScript has to use.

That's right: JavaScript only has *one thread*.  

**So what's the point in writing asynchronous code if we aren't farming it off to some background thread?  Aren't we just going to have to wait for that code anyway?**

Yes, and no.  

*Under most simple implementations async code is only beneficical for un-blocking the JS thread when making calls to a server*.  That is to say, async code is only useful when we're making API calls, retrieving static files such as images or scripts, or even connecting or communicating through web sockets.

## The Brief History of Async Code in JavaScript

Asynchronous code isn't new to JavaScript, and while *Promises* and *async/await* are relative new comers (2012 and 2017 respectively), the basis of these features can be found in jQuery's deffered objects, and even the concept of 'Promises' was [https://en.wikipedia.org/wiki/Futures_and_promises] (first introduced in 1976).  Prior to the 're-discovery' of promises, events and callback functions were the most common ways of handling 'asynchronous' code in JavaScript. Starting around 2011, deffered objects and the idea of Promises started to take hold, with their eventual wide-spread adoption by browsers in 2014-2015.

Promises formalized many of the practices being used in the wild, and were a very good solution to a common problem.  However they weren't without problems.  Most often extensive use of promises devolved into a grotesque chain and nesting of '.then' calls:

`const promise1 = someAsyncCall().then(res => {
    someOtherAsyncCall(res).then(otherRes => {
	console.log('Success!!!');
    }).catch(insideErr => {
       console.log('${insideErr} occurred');
    })
}).catch(err => {
    console.log('${err) occurred');
});`

That's two async calls, one dependent on the result of the other, and handling the failure (a.k.a. rejection) of either Promise.

In 2017 ES8* introduced async/await as a standard language feature, however - support started appearing in browsers in 2016.  The async/await keywords address some of the shortcomings of Promises.  Mostly the ugly world of nested promises and error handling.  Using async/await the code above can be refactored to this:

`try {
    const result = await someAsyncCall();
    const otherResult  = await someOtherAsyncCall(result);
    console.log('Success!!!');
} catch (err) {
    console.log('${err) occurred');
}`

Not only is this significantly easier to read, but it uses common JS error handling (try/catch), and it's immediately clear what is going on: wait for the result of someAsyncCall(), use that to call someOtherAsyncCall, if either fail log the error to the console.

So much better.

## A Little Deeper Look at Promises

I know your first response to the header on this section is to either **scroll past** or ask, "If async/await is better, why should I learn about Promises at all?"

The short answer is: async/await is just syntactic sugar around Promises, understanding promises gives you more power over how you write your code, and *Promises still have some value*.

Before we get any further, let's take a look at the Promise constructor:
` new Promise( function(resolve, reject) {} ); `

The Promise contstructor takes in a function that takes two parameters *resolve* and *reject*.  The function itself is the action that the Promise will take during it's execution.  This could be anything from making an API call, to doing some advanced math (though we've covered why there's no immediate benefit to running so-called *long running functions* using JS's async features).

The *resolve* and *reject* parameters provide callback functions that the Promise can execute after it either succeeds (resolved) or fails (rejected).

`myFunction() {
	return Promise(function(resolve, reject) {
		try {
			// do a thing, possibly async, then…

			if (/* everything turned out fine */) {
				resolve("Stuff worked!");  }
			else {
				reject(Error("It broke"));
			}
		} catch (err) {
			reject(err);
		}
	});`
}`

This example gives us a function that returns a *Promise*.  That Promise will (eventually) call either the *resolve* or *reject* functions.

When calling the above function we could do something like this:

`const promise = myFunction();

promise.then(result => {
    // Handle successful completion
}).catch(err => {
    // Handle failure
}).finally(() => {
    // The finally block is always executed after the promise is satisfied
});`
