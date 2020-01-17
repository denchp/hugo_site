---
title: "Asynchronous Code In JavaScript"
date: 2020-01-15T14:03:24-06:00
draft: true
tags: [ 'javascript', 'async', 'promise', '100 level' ]
---


## What Is Asynchronous Code and When To Use It?

For many developers the thought of "Asynchronous Code" conjures one of two spectres.  One spirit capable of mysteriously unlocking untold amounts of performance by shipping tasks off to some fabled 'background thread'; and the other a ghoul of odd behaviors and impossible-to-test synchronization problems.  By the end of this article I hope to banish both of these ghosts, and replace them with a solid understanding of 'async' code, and how to use it in JavaScript.

> We're *not* going to be diving deep under the hood here.  This is about practical understanding and examples that can help you get over async superstitions.

> We're also not going to go into *Web Workers* as they're more of a '200 level' article.

First of all, if you're coming from desktop development: simple async code in JavaScript is much simpler than it is on a desktop.  That simplicity stems from the fact that JavaScript's async code isn't about starting up multiple threads to improve performance.  Instead, it's about un-blocking the *single thread* that JavaScript has to use.

That's right: JavaScript only has *one thread*.  

**So what's the point in writing asynchronous code if we aren't farming it off to some background thread?  Aren't we just going to have to wait for that code anyway?**

Yes, and no.  

*Under most simple implementations async code is only beneficial for un-blocking the JS thread when making calls to a server*.  That is to say, async code is only useful when we're making API calls, retrieving static files such as images or scripts, or even connecting or communicating through web sockets.

## The Brief History of Async Code in JavaScript

Asynchronous code isn't new to JavaScript, and while *Promises* and *async/await* are relative new comers (2012 and 2017 respectively), the foundation of these language features can be found in jQuery's deferred objects, and even the concept of 'Promises' was [first introduced in 1976](https://en.wikipedia.org/wiki/Futures_and_promises).  Prior to the 're-invention' of promises: events and callback functions were the most common ways of handling 'asynchronous' code in JavaScript. Starting around 2011, jQuery's deferred objects and the idea of Promises started to take hold, with their eventual wide-spread adoption by browsers in 2014-2015 with ES6.

> ES6 (or 7 or 8) refers to the "ECMAScript" version.  ECMAScript is the industry standard on which JavaScript is based.

Promises formalized many of the practices being used in the wild, and were a good solution to a common problem.  However they weren't without problems themselves.  Most often: the extensive use of Promises devolved into a grotesque chain and nesting of '.then' calls:

```
const promise1 = someAsyncCall().then(res => {
	someOtherAsyncCall(res).then(otherRes => {
		console.log('Success!!!');
	}).catch(insideErr => {
		console.log('${insideErr} occurred');
	})
}).catch(err => {
	console.log('${err) occurred');
});
```

That's two async calls, one dependent on the result of the other, and handling the failure (a.k.a. rejection) of each Promise.

If you can imagine a site that requires multiple inter-related API calls, the code quickly looks like crusty three-day-old spaghetti.  It becomes unreadable, with error handling (*catch* handlers) nested inside the different *result* (*then*) handlers.  It's a mess, it's difficult to read, and nearly impossible to maintain.

In 2017 ES8 introduced *async/await* as a standard language feature. However, support started appearing in browsers in 2016.  The *async/await* keywords address some of the shortcomings of Promises.  Mostly the ugly world of nested Promises and error handling.  Using *async/await* the code above can be refactored to this:

```
try {
    const result = await someAsyncCall();
    const otherResult  = await someOtherAsyncCall(result);
    console.log('Success!!!');
} catch (err) {
    console.log('${err) occurred');
}
```
> Note: For this to be valid the *async* keyword needs to be applied to the function that contains this code-block.

Not only is this significantly easier to read, but it uses common JavaScript error handling (*try/catch*), and it's immediately clear what is going on: wait for the result of *someAsyncCall*, use that to call *someOtherAsyncCall*, if either fail: log the error to the console.

So much better.

## A Little Deeper Look at Promises

I know your first response to the header on this section is to either **scroll past** or ask, *"If async/await is better, why should I learn about Promises at all?"*

The short answer is: async/await is just a wrapper around Promises, understanding promises gives you more power over how you write your code, and *Promises still have some significant value*.

Before we get any further, let's take a look at the Promise constructor:

``` new Promise( function(resolve, reject) { /* Do the thing here */ } ); ```

The Promise constructor takes in a function as an argument, which takes two additional arguments *resolve* and *reject*.  The function itself is the action that the Promise will take during it's execution.  This could be anything from making an API call, to doing some advanced math (though we've covered why there's no immediate benefit to running so-called *long running functions* using JavaScript's async features).

The *resolve* and *reject* arguments passed to the action function provide callback functions that the Promise can execute after it either succeeds (resolved) or fails (rejected).

```
function myFunction() {
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
}
```

This example gives us a function that returns a *Promise*.  That Promise will (eventually) call either the *resolve* or *reject* functions.

When calling the above function we could do something like this:

```
const promise = myFunction();

promise.then(result => {
    // Handle successful completion
}).catch(err => {
    // Handle failure
}).finally(() => {
    // The finally block is always executed after the promise is satisfied
});
```

Here we are calling our function, and appending functions to the *then*, *catch*, and *finally* 'events' of the Promise.  The Promise prototype appends the handlers to the resolve (*then*) and reject (*catch*) function calls within our function.  The *finally* event is a special callback that is triggered after the Promise is *either* resolved or rejected.  The *finally* block is useful for code common to both success and failure code-branches.  For example: cleaning up the state of the UI after the call is completed (e.g., hiding a spinner).

## Pour Some Sugar On Me

You may have heard the term 'Syntactic Sugar' thrown around in conversations about JavaScript.  It refers to the fact that a *lot* of the 'new' features of ES6/7/8 are simply re-wrapping existing features, or practices; in a simpler, more standardized format.  Promises standardized the Deferred objects in jQuery, the 'new' import statement replaces calls to the *require* function.

The new async/await keywords are no exception to this practice:

> "async" functions are just functions that wrap their return value in a Promise.
> "await" simply 'un-wraps' a Promise and gets the return value.

That is either a major revelation, or a major let-down - I'll let you decide which.

The huge positive to this is that with your understanding of Promises gives you a *lot* of power, and introducing async/await can bring a *lot* of simplicity.

Let's imagine we have two API calls: *getRandomNumber* and *getAPlusB*.

I won't go into 'what this looks like without Promises'.  I started to, immediately shuddered in revulsion and deleted it...

So with promises:

```
function getRandomNumber() {
	return new Promise((resolve, reject) => {
		try {
			// Make our API call
			resolve([some value we get from the API]);
		} catch (err) {
			reject(err);
		}
	});
}

function getAPlusB(a, b) {
	return new Promise((resolve, reject) => {
		try {
			// Make our API call using *a* and *b*
			resolve([the sum we get from the API]);
		} catch (err) {
			reject(err);
		}
	});
}

function someFunction() {
	let someValue; // We instantiate the variable here
	getRandomNumber().then(a => {
		getRandomNumber().then(b => {
			getAPlusB(a, b).then(c => someValue = c) // and set that value here...
		})
	});
	
	console.log(someValue);
}

```
> Note: we're omitting the catch handlers here for brevity, but you should **always** provide them.

Here we have three API calls being made, assuming their named correctly: we're getting two random numbers from the server, then asking the server to add them together for us.  Seems like a bit of a waste of server power, but hey... it's in the cloud...

The big question is "What is logged to the console at the end of *someFunction()*?"

...

The answer: *undefined*.

The problem we have here is that while we're not blocking the thread with our API calls, the values are set *inside* the callback functions.  So our call to log out *someValue* is executed **before** the resolution of the Promise.

The really nasty situation is when code like this will work...  *sometimes*.

Imagine our call to log out the result of that inner function happens somewhere further down the line.  And it takes *n* seconds before we get to that line.  Well, if the API call is takes less than *n* seconds (which in this example I would hope it takes less than a second...), we get the answer logged out to the console instead of 'undefined'.  But, the network is inherently unreliable, so while the server may execute the call quickly, there may be delays in the request getting to-and-from the server.  Not to mention mobile users who have latencies approaching carrier-pigeon-like performance.

So you can see how async can be a bit... dodgy, *especially* when the code is difficult to understand.

But look at this code:
```
async function someFunction() {
	const a = await getRandomNumber();
	const b = await getRandomNumber();
	const someValue = await getAPlusB(a, b);
	
	console.log(someValue); // => a + b
}

```

The exact same calls are made, but this time we get the expected result *every time*.

And here's the amazing thing: **we don't even have to change the API functions to use asyc/await!**  This is because the async/await keywords work hand-in-hand with Promises!

But we can!

Here are those same API functions with the async/await keywords:
```
async function getRandomNumber() {
	try {
		// Make our API call
		return [some value we get from the API];
	} catch (err) {
		throw(err);
	}
}

async function getAPlusB(a, b) {
	try {
		// Make our API call using *a* and *b*
		return [the sum we get from the API];
	} catch (err) {
		throw(err);
	}
}
```
## Turtles All The Way Down...
> A little old lady was discussing the universe with a scientist...
>
>Lady: "Your theory that the sun is the centre of the solar system, and the earth is a ball which rotates around it is wrong."
>
>Scientist: "What is the correct model then, madam?"
>
>Lady: "That we live on a crust of earth which is on the back of a giant turtle."
>
>Scientist: "If your theory is correct, madam, what does this turtle stand on?"
>
>Lady: "Why, the first turtle stands on the back of a second, far larger, turtle, who stands directly under him."
>
>Scientist: "But what does this second turtle stand on?"
>
>Lady: "Hah! It's simple!  It's turtles all the way down!"

One challenge of the new async/await keywords is that the *await keyword can only be used inside a function with the async keyword on it (an async function)*.  And any async function **returns a promise**.

```
function myFunc() { return 'foo' }

async function myAsyncFunc() { return 'foo' }

console.log(myFunc()); // => 'foo'

console.log(myAsyncFunc()); // > Promise {}
console.log(await myAsyncFunc()); // => 'foo'
myAsyncFunc().then(console.log); // => 'foo'
```
These two functions look nearly identical, but with the *async* keyword the latter returns a Promise *automatically* and it's behavior, and the behavior around any calling code is going to be **completely** different.

Also, in order for that code to be valid it *has* to be within an async function:
```
async function someFunc() {
	console.log(await myAsyncFunc()); // => 'foo'
}

```

This introduces a few problems:
1. A function that returns a value, and is converted to an async function will *automatically* change to return a Promise instead.
2. Any calling functions wanting to access the returned value will have to await the result of the original request.
3. In order to await the result the calling function has to be an async function.
4. Go to #1

See?  Turtles...

The final problem here is that: **await cannot be used in 'top level code'**.  Which gives us a bit of a snag: *at some point that last turtle has to be standing on **something**.*  That is to say we can't just make everything async through the entire stack - nor should we.

Which is where Promises come back into play.  As we know those async functions return promises, that we're then awaiting; but we don't have to.  Instead, we can use the 'old' way of handling the Promise resolution by attaching a handler to the 'then' event of the Promise.  Those handlers *do not* have to be in an async function.

Handling the resolution of a Promise gives us a very powerful tool to determine when and where we break into the async stack in order to make it synchronous again.  Generally you'll want to find a pattern that works for your application and stick to it since constantly changing between Promise event handlers and async/await can be confusing.


## Async Rules-of-Thumb and Patterns

There a few patterns that are useful when using both Promises and async/await.  As always there are exceptions to the rules, but these have always served me well as guidelines.

---

#### Rule #1: Default to using async/await
As we've seen the new async/await pattern is significantly more readable, and maintainable.  If it doesn't clutter your code with additional *then* handlers, or make the stack too weird: always use async/await.

---

#### Rule #2: Break into the async stack with Promises where layers naturally arise
This will take some feel, but all code can be broken into layers: data, presentation, supporting libraries, etc...  If you find yourself needing to use Promises to 'break' the async stack it's best to do so at one of these layers, and to do it either universally, or explicitly.  For example:
In our hypothetical app we decided that all library functions should be synchronous.  Any async functions should be prefixed with 'async' (e.g., asyncMyFunc).

---

#### Pattern #1: Wait for all the things!

One feature we haven't discussed are the static functions available in Promises: *all*, *allSettled*, and *race*. Each of these functions accepts an array of promises and returns a Promise that resolves when.

* all: All Promises are resolved.  Rejects if *any* Promise fails.
* allSettled: All Promises are resolved *or* rejected.
* race: Resolves when *one* of the Promises is resolved.

Using ```Promise.all([p1, p2, p3 ])``` can be very useful.  For example we have multiple api calls, and we want to take an action when they're *all* resolved.

```
const promises = [];
try {
	for (let i = 0; i < max; i++)
		promises.push(someApiCall(i));
		
	const results = await Promise.all();
	// Do something with our results...

} catch (err) {
	// Something went wrong with one of our API calls
} finally {
	// Clean-up
}
```

This way we can trigger multiple API calls without awaiting the results of each one separately.

---

#### Pattern #2: Make a Synchronous Function into an Async - generically
If you find you have a need to make multiple synchronous functions fit the async pattern you can use an *asyncFactory* pattern.
```
function asyncFactory(func, context){
    return function() {
        return Promise.resolve(func.apply(context, arguments));
    }
}

const asyncRandom = asyncFactory(Math.random);

console.log(asyncRandom()); // => Promise { <resolved>: 0.7033608289175788 }
console.log(await asyncRandom()); // => 0.1739279357239483

asyncRandom().then(console.log); // => 0.7505884066772504
```
Promise.resolve returns a Promise that resolves to the given value (in our case the result of the provided synchronous function).

---