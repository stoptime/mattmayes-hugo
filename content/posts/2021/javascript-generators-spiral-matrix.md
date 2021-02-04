---
title: 'Javascript Generators and the Spiral Matrix algorithm'
date: 2021-02-03T10:33:06-06:00
tags: ['dev', 'javascript', 'generators']
draft: false
showpagemeta: true
showcomments: false
---

Generators are dope - and JavaScript has had them since ES6 (aka ECMAScript 2015, _not confusing at all_). I thought the [Spiral Matrix algorithm](https://www.educative.io/edpresso/spiral-matrix-algorithm) would make a good example. To solve this algorithm you need to incrementally fill a grid with sequential numbers from the outside in ("sprialing" towards the center). I thought it would be handy if I could control the iteration and manually animate the filling of the grid, and since a generator lets you control iteration, it was the perfect thing to use.

If you click the "Browser" tab below and then click the "Gimme One" button, a generator is returning an object containing the row, column, and value to plot in the grid:

<iframe width="100%" height="400" src="https://jsitor.com/embed/SrWvN7kA9" frameborder="0" title="Spiral Matrix with Generators example"></iframe>

After you click "Gimme One" - the generator pauses iteration, until you click the button again to get the _next_ value. When you click the "Finish It" button - the generator is emptied out via a function that uses `setTimeout()` to slow things down a bit. [(JSitor page.)](https://jsitor.com/SrWvN7kA9)

## So What are "Generators"?

A "Generator" can be thought of as a special kind of object that lets you define your own iteration pattern.

Take a function that iterates through something. What if you could control exactly when the next iteration of the loop occurs? That's what a generator lets you do. Let's take a basic non-generator example:

```javascript
function printIt() {
  for (l of 'hello') {
    console.log(l)
  }
}
printIt()
// h
// e
// l
// l
// o
```

When the function `printIt()` runs, you have no control over the loop - it just loops over the entire string at once, as fast as it can. And yeah, most of the time that's what we want. Now let's turn that into a generator by adding an asterisk after the function keyword, and using `yield` to pass the result of the next iteration:

```javascript
function* printIt() {
  for (l of 'hello') {
    yield l
  }
}
const gimmeOne = printIt()
console.log(gimmeOne.next().value)
// h
console.log(gimmeOne.next().value)
// e
```

In the above, we used our generator function to create an object called `gimmeOne` that suspends the `printIt()` iteration until the `next()` method is called. The `next()` method gives the next value, and then stops iteration until `next()` is called again. Keep in mind the yielded value can be anything: string, array, object, etc.

### Why Should I Care?

Generators give you control over iteration - which can be handy when you...

- are incrementally processing a huge dataset (can save memory via deferred computation)
- only want to produce values when they are actually needed (think consumer -> producer pipelines)
- need to iterate in an unusual order
- don't want to construct a huge dataset in memory
- need complete control over infinite loops/data streams

And probably other reasons too - they really get you thinking, and there's much more they can do - [this is a good write-up on javascript generators](https://codeburst.io/understanding-generators-in-es6-javascript-with-examples-6728834016d5) that goes into a bit more detail.
