---
title: Coding Questions - Answers
layout: layouts/page.njk
---

## What is the value of `foo`?
```javascript
var foo = 10 + '20';
```

**`"1020"`** - When one operand of `+` is a string, JavaScript coerces the other to a string and performs concatenation.

## What will be the output of the code below?
```javascript
console.log(0.1 + 0.2 == 0.3);
```

**`false`** - Due to floating-point arithmetic, `0.1 + 0.2` evaluates to `0.30000000000000004`, which is not exactly equal to `0.3`.

## How would you make this work?
```javascript
add(2, 5); // 7
add(2)(5); // 7
```

```javascript
function add(a, b) {
  if (arguments.length === 2) {
    return a + b;
  }
  return function(c) {
    return a + c;
  };
}
```

When called as `add(2, 5)`, both arguments are present, so it returns `7`. When called as `add(2)(5)`, the first call returns a function that captures `a = 2` via closure, and the second call passes `5` to that function, returning `7`.

## What value is returned from the following statement?
```javascript
"i'm a lasagna hog".split("").reverse().join("");
```

**`"goh angasal a m'i"`** - The string is split into an array of characters, reversed, then joined back.

## What is the value of `window.foo`?
```javascript
( window.foo || ( window.foo = "bar" ) );
```

**`"bar"`** - Short-circuit evaluation. `window.foo` is initially `undefined` (falsy), so the right side executes, setting `window.foo = "bar"` and returning `"bar"`.

## What is the outcome of the two alerts below?
```javascript
var foo = "Hello";
(function() {
  var bar = " World";
  alert(foo + bar);
})();
alert(foo + bar);
```

**First alert**: `"Hello World"` - The IIFE accesses `foo` from the outer scope and its own `bar`.

**Second alert**: Throws a `ReferenceError` - `bar` is scoped to the IIFE and is not defined in the outer scope. The error prevents the second alert from executing.

## What is the value of `foo.length`?
```javascript
var foo = [];
foo.push(1);
foo.push(2);
```

**`2`** - After two pushes, the array is `[1, 2]`, with length `2`.

## What is the value of `foo.x`?
```javascript
var foo = {n: 1};
var bar = foo;
foo.x = foo = {n: 2};
```

**`undefined`** - Breaking it down:

1. `foo` and `bar` both point to `{n: 1}`
2. JavaScript evaluates the left side `foo.x` first, creating property `x` on the original object
3. The right side evaluates right-to-left: `foo = {n: 2}` reassigns `foo` to a new object
4. `{n: 2}` is assigned to the `x` property on the original object

Result:
- `bar` is `{n: 1, x: {n: 2}}`
- `foo` is `{n: 2}`
- `foo.x` is `undefined` (the new object has no `x` property)

## What does the following code print?
```javascript
console.log('one');
setTimeout(function() {
  console.log('two');
}, 0);
Promise.resolve().then(function() {
  console.log('three');
})
console.log('four');
```

```
one
four
three
two
```

**Explanation**: `'one'` and `'four'` execute synchronously. The Promise callback goes to the microtask queue. The setTimeout callback goes to the macrotask queue. Microtasks run before macrotasks, so `'three'` logs before `'two'`.

## What is the difference between these four promises?

```javascript
doSomething().then(function () {
  return doSomethingElse();
});
```
**Correct chaining.** The chain waits for `doSomethingElse()` to complete, and the next `.then()` receives its resolved value.

```javascript
doSomething().then(function () {
  doSomethingElse();
});
```
**Fire-and-forget.** `doSomethingElse()` executes but is not returned. The next `.then()` receives `undefined` immediately, not waiting for `doSomethingElse()` to finish.

```javascript
doSomething().then(doSomethingElse());
```
**Immediate execution.** `doSomethingElse()` is called immediately, not after `doSomething()` resolves. Its return value is passed to `.then()` as the callback, which is likely a bug.

```javascript
doSomething().then(doSomethingElse);
```
**Delegated reference.** The function reference is passed directly to `.then()`. It will be called with `doSomething()`'s resolved value when it completes.

## What will the code below output to the console and why?
```javascript
(function(){
  var a = b = 3;
})();

console.log("a defined? " + (typeof a !== 'undefined'));
console.log("b defined? " + (typeof b !== 'undefined'));
```

```
a defined? false
b defined? true
```

**Why**: `var a = b = 3` is parsed as `b = 3` followed by `var a = b`. `b` has no declaration keyword, so it becomes an implicit global. `a` is declared with `var` inside the IIFE and is function-scoped. Outside the IIFE, `b` is globally accessible but `a` is not.

## Consider the two functions below. Will they both return the same thing? Why or why not?
```javascript
function foo1()
{
  return {
      bar: "hello"
  };
}

function foo2()
{
  return
  {
      bar: "hello"
  };
}
```

**No.**

- `foo1()` returns `{ bar: "hello" }` - the opening brace is on the same line as `return`.
- `foo2()` returns `undefined` - Automatic Semicolon Insertion places a semicolon after `return`, making it `return;`. The object literal on the next line is unreachable code.
