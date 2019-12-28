---
title: "JavaScript Arrow Functions and 'this'"
date: 2019-12-27T16:27:37-06:00
tags : [ "dev", "javascript" ]
---

One of the things that makes JavaScript so interesting (and weird) is not only [the various ways to write an arrow function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions#Description), but how '`this`' behaves within one. The paricular part I find interesting is that arrow functions don't have their own `this` - rather, they inherit `this` from the **invocation context that calls them**, aka their _outer function_. 

## Maybe That's a Strength?
We can use that knowledge to get around some dodgy problems. Take the `setInterval` function as an example. It is a built-in/global javascript function, and the browser _window_ is the global object. Even if you use `setInterval` inside a non-arrow function or object, its `this` value will still be the `window`.

> Fun fact: _Window_ is a function, whereas the lowercase _window_ is a global variable/object holding an instance of it, very weird - not confusing at all 😬.

Without an arrow function, to maintain context it was common to do something along these lines:

``` javascript
const classyClass = {
	name : 'How about that',
	func() {
		const that = this; // store 'this' before it changes 
		setInterval(function() {
			console.log(that); // classyClass
			console.log(that.name); // How about that
			console.log(this); // window
			console.log(this.name); // window.name, which is ""
		}, 5000);
	}
};
```
(If you're wondering why the `window.name` property is an empty string, [check this out](https://developer.mozilla.org/en-US/docs/Web/API/Window/name).)

But since arrow functions don't have their own `this`, it won't change when we call `setInterval` - so we can really tidy things up:

```js
const classyClass = {
	name : 'No more "that" crap',
	func() {
		setInterval(() => {
			console.log(this); // classyClass
            console.log(this.name); // No more "that" crap
		}, 5000);
	}
};
```

So what may be considered a short-coming when using arrow functions to create methods, can actually be considered a plus in circumstances like, erm, _this_.
