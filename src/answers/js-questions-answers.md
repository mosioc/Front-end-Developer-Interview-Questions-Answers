---
title: JS Questions - Answers
layout: layouts/page.njk
---
## Explain event delegation

Event delegation is a pattern in JavaScript where instead of attaching event listeners to multiple child elements, you attach a single listener to a parent element. This works because events bubble up through the DOM.

I used this pattern in a notification center where notifications were fetched in batches (infinite scroll). Using delegation on the container avoided re-binding events every time new items loaded.

In short: you listen once at the parent and handle interactions for all current and future children.

### **Without event delegation (inefficient)**

```js
const items = document.querySelectorAll(".item");

items.forEach((item) => {
  item.addEventListener("click", () => {
    console.log("Clicked:", item.dataset.id);
  });
});
```

### **With event delegation (better)**

```js
Copy code
const list = document.querySelector('.item-list');

list.addEventListener('click', (e) => {
  // Find the closest child with .item class
  const item = e.target.closest('.item');
  if (!item) return;

  console.log('Clicked:', item.dataset.id);
});
```

## Explain how `this` works in JavaScript.Can you give an example of one of the ways that working with `this` has changed in ES6?

In JavaScript, the value of `this` depends on how a function is **called**, not where it’s written. It can refer to different things:

- **Global context** → `this` is the global object (or `undefined` in strict mode)
- **Object method** → `this` is the object
- **Event handlers** → `this` is the DOM element
- **Constructor functions / classes** → `this` is the instance created

One of the biggest changes in ES6 is how **arrow functions** handle `this`.

Arrow functions do **not** create their own `this`. Instead, they **lexically inherit** `this` from their surrounding scope.

### Example from experience

Before ES6, when working with callbacks (especially in event listeners or async code), `this` often changed unexpectedly. We had to write things like:

```js
function Timer() {
  this.seconds = 0;
  setInterval(function () {
    this.seconds++; // this is NOT the Timer instance
  }, 1000);
}
```

We had to fix it using `.bind(this)` or the classic `const self = this`.

### With ES6 arrow functions

```js
function Timer() {
  this.seconds = 0;
  setInterval(() => {
    this.seconds++; // 'this' correctly refers to Timer instance
  }, 1000);
}
```

In a real project, I used this pattern when implementing an auto-refreshing dashboard timer. Using arrow functions inside async callbacks ensured `this` always pointed to the class instance without needing `.bind(this)` everywhere.

So in short: **ES6 made working with `this` much more predictable thanks to arrow functions.**

## Explain how prototypal inheritance works

Prototypal inheritance in JavaScript is how objects can inherit properties and methods from other objects. Unlike classical OOP languages that use classes for inheritance, JS uses **objects linked to other objects** through their prototype.

Every object has an internal link called `[[Prototype]]` (accessible via `__proto__` or `Object.getPrototypeOf`). When you try to access a property or method on an object, JS looks up the prototype chain until it finds it or returns `undefined`.

### Example

```js
const animal = {
  speak() {
    console.log(`${this.name} makes a sound`);
  },
};

const dog = {
  name: "Rex",
  __proto__: animal,
};

dog.speak(); // Rex makes a sound
```

Here, `dog` doesn’t have `speak` directly, but JS finds it in `animal` via the prototype chain.

### Real-world experience

In a React project, I used prototypal inheritance to create utility objects for different chart types. Base chart objects had shared methods (`render`, `updateData`), and specific chart objects (`barChart`, `lineChart`) inherited those, keeping code DRY without repeating common logic.

**In short:** prototypal inheritance lets objects share behavior efficiently, and it’s the backbone of JS object-oriented patterns.

## What is the difference between a variable that is: `null`, `undefined` or undeclared? How would you go about checking for any of these states?

In JavaScript, `null`, `undefined`, and undeclared are distinct:

- **`null`**: Explicitly assigned to a variable to indicate "no value" or "empty".

  ```js
  let a = null;
  ```

- **`undefined`**: The variable exists but hasn’t been assigned a value yet.

  ```js
  let b;
  console.log(b); // undefined
  ```

- **Undeclared**: The variable doesn’t exist in the current scope at all.

  ```js
  console.log(c); // ReferenceError: c is not defined
  ```

### Checking these states

- **`null` check**

  ```js
  if (a === null) {
    /* explicitly null */
  }
  ```

- **`undefined` check**

  ```js
  if (typeof b === "undefined") {
    /* variable exists but not assigned */
  }
  ```

- **Undeclared check**

  ```js
  if (typeof c === "undefined") {
    /* safe check, avoids ReferenceError */
  }
  ```

## What is a closure, and how/why would you use one?

A closure in JavaScript is a function that **remembers the scope in which it was created**, even after that outer function has finished executing. In other words, it allows a function to access variables from an outer function’s scope.

### Why use closures

- **Encapsulation / private variables:** You can create data that’s accessible only through specific functions.
- **Maintain state between calls:** Useful for counters, caches, memoization.
- **Functional programming patterns:** Passing functions with pre-configured state.

### Example

```js
function createCounter() {
  let count = 0; // private variable
  return function () {
    count++;
    return count;
  };
}

const counter = createCounter();
console.log(counter()); // 1
console.log(counter()); // 2
```

Here, the inner function retains access to `count` even after `createCounter` has returned.

### Real-world use

In a project I worked on, I used closures for caching API responses in a hook. The closure held a local cache object, so repeated calls with the same parameters could return cached data instead of making unnecessary network requests, improving performance without polluting global state.

## What language constructions do you use for iterating over object properties and array items?

In JavaScript, I use different language constructs depending on whether I’m iterating over **arrays** or **object properties**.

### Arrays

- **`for` loop**; classic, flexible, useful when you need index control.

```js
for (let i = 0; i < arr.length; i++) {
  console.log(arr[i]);
}
```

- **`for...of`**; clean syntax for iterating over values.

```js
for (const item of arr) {
  console.log(item);
}
```

- **`forEach`**; functional, great for inline processing.

```js
arr.forEach((item) => console.log(item));
```

- **Higher-order functions** – `map`, `filter`, `reduce`, etc., for transforming or aggregating.

```js
const doubled = arr.map((x) => x * 2);
```

### Objects

- **`for...in`** – iterates over enumerable property keys.

```js
for (const key in obj) {
  console.log(key, obj[key]);
}
```

- **`Object.keys` / `Object.values` / `Object.entries`** – modern methods for iterating keys, values, or [key, value] pairs.

```js
Object.entries(obj).forEach(([key, value]) => console.log(key, value));
```

## Can you describe the main difference between the `Array.forEach()` loop and `Array.map()` methods and why you would pick one versus the other?

The main difference between `forEach` and `map` is that **`forEach` is used for side effects**, while **`map` is used to create a new array based on the original**.

- **`forEach`** executes a function for each element but **does not return anything**.

```js
const arr = [1, 2, 3];
arr.forEach((x) => console.log(x * 2)); // logs 2, 4, 6
```

Use when we need to perform actions like logging, updating external state, or calling APIs.

- **`map`** creates a **new array** with the results of applying the function to each element.

```js
const arr = [1, 2, 3];
const doubled = arr.map((x) => x * 2);
console.log(doubled); // [2, 4, 6]
```

Use `map` when we need a transformed array.

### Real-world example

In a React project, I used `map` to transform a list of API data into JSX components for rendering a list of cards. I used `forEach` when iterating over data to log analytics events without needing a new array.

## Сan you describe other popular methods for iterating over arrays?

Yes, besides `forEach` and `map`, JavaScript offers several popular methods for iterating over arrays, each serving specific use cases:

- **`filter`**; creates a new array with elements that pass a condition.

```js
const numbers = [1, 2, 3, 4];
const evens = numbers.filter((x) => x % 2 === 0); // [2, 4]
```

- **`reduce`**; accumulates a single value by applying a function to each element.

```js
const sum = numbers.reduce((acc, x) => acc + x, 0); // 10
```

- **`some`**; returns `true` if at least one element satisfies the condition.

```js
const hasEven = numbers.some((x) => x % 2 === 0); // true
```

- **`every`**; returns `true` if all elements satisfy the condition.

```js
const allPositive = numbers.every((x) => x > 0); // true
```

- **`find`**; returns the first element that satisfies the condition.

```js
const firstEven = numbers.find((x) => x % 2 === 0); // 2
```

- **`findIndex`**; returns the index of the first element that satisfies the condition.

```js
const indexFirstEven = numbers.findIndex((x) => x % 2 === 0); // 1
```

- **`for...of`**; modern loop for iterating over values, can be combined with `break` or `continue`.

```js
for (const num of numbers) {
  console.log(num);
}
```

## What is a typical use case for anonymous functions?

Anonymous functions are functions **without a name** and are commonly used when a function is needed **temporarily or as a callback**.

- **Callbacks for event handlers**

```js
document.getElementById("btn").addEventListener("click", function () {
  console.log("Button clicked");
});
```

- **Array iteration methods**

```js
[1, 2, 3].forEach(function (x) {
  console.log(x * 2);
});
```

- **Immediately Invoked Function Expressions (IIFE)** for creating a private scope

```js
(function () {
  const privateVar = "hidden";
  console.log(privateVar);
})();
```

- **Promises / async operations**

```js
fetch("/api/data")
  .then(function (response) {
    return response.json();
  })
  .then(function (data) {
    console.log(data);
  });
```

## What is the difference between host objects and native objects?

In JavaScript, the difference between **host objects** and **native objects** lies in their origin and implementation.

- **Native objects** are built into the JavaScript language itself and provided by the ECMAScript specification. They include `Array`, `Object`, `Date`, `RegExp`, `Promise`, etc. They behave consistently across environments.

- **Host objects** are provided by the runtime environment (e.g., browser, Node.js) to interact with the environment outside of JavaScript. Examples in browsers include `window`, `document`, `XMLHttpRequest`, `console`, `HTMLElement`, etc. Their behavior can vary across environments.

## Explain the difference between: `function Person(){}`, `var person = Person()`, and `var person = new Person()`?

### `function Person(){}`

- This **declares a function** named `Person`. It does not execute the function yet.
- You can call it later, either as a regular function or as a constructor with `new`.

### `var person = Person()`

- Calls `Person` as a **regular function**.
- Inside the function, `this` will refer to the global object (`window` in browsers) or `undefined` in strict mode.
- If the function doesn’t return anything explicitly, `person` will be `undefined`.

### `var person = new Person()`

- Calls `Person` as a **constructor**.
- A new object is created, `this` inside the function refers to that new object.
- The new object is returned implicitly (unless the function explicitly returns another object).

### Example

```js
function Person(name) {
  this.name = name;
  this.sayHi = function () {
    console.log(`Hi, I'm ${this.name}`);
  };
}

var a = Person("Mehdi");
console.log(a); // undefined

var b = new Person("Bob");
b.sayHi(); // Hi, I'm Bob
```

## Explain the differences on the usage of `foo` between `function foo() {}` and `var foo = function() {}`

The difference between `function foo() {}` (function declaration) and `var foo = function() {}` (function expression) mainly comes down to **hoisting**, **naming**, and **when the function becomes available**.

1. **Function Declaration (`function foo(){}`)**

- **Hoisted:** The function is fully hoisted to the top of its scope, meaning you can call it **before it’s defined**.

```js
console.log(foo()); // works
function foo() { return 'hello'; }
```

- The function has a **name** (`foo`) and is easier to debug.

2. **Function Expression (`var foo = function(){}`)**

- **Not fully hoisted:** Only the variable `foo` is hoisted, not the function assignment. Calling it before the line will give `TypeError: foo is not a function`.

```js
console.log(foo()); // TypeError
var foo = function() { return 'hello'; };
```

- The function can be **anonymous** or named. Naming helps in stack traces but isn’t required.

## Can you explain what `Function.call` and `Function.apply` do? What is the notable difference between the two?

`Function.call` and `Function.apply` are methods that allow you to invoke a function with a **specific `this` context** and arguments.

1. **`call`**

- Invokes the function with a specified `this` value and **comma-separated arguments**.

```js
function greet(greeting, punctuation) {
  console.log(`${greeting}, I'm ${this.name}${punctuation}`);
}

const person = { name: 'Mehdi' };
greet.call(person, 'Hello', '!'); // Hello, I'm Mehdi!
```

2. **`apply`**

- Invokes the function with a specified `this` value and **arguments as an array**.

```js
greet.apply(person, ['Hi', '...']); // Hi, I'm Mehdi...
```

### Notable difference

- **Argument passing:** `call` uses comma-separated arguments, `apply` uses an array of arguments.
- Functionally, both are the same in terms of controlling `this`.

## Explain `Function.prototype.bind`

`Function.prototype.bind` is a method that **creates a new function** with a specified `this` value and optionally pre-set initial arguments.

### Key points

- Returns a **new function**; it does **not** call the original function immediately.
- Allows you to **lock `this`** to a specific context.
- Can also **partially apply arguments** (currying).

### Example

```js
function greet(greeting, punctuation) {
  console.log(`${greeting}, I'm ${this.name}${punctuation}`);
}

const person = { name: 'Mehdi' };

const greetMehdi = greet.bind(person, 'Hello');
greetMehdi('!'); // Hello, I'm Mehdi!
```

Here, `bind` sets `this` to `person` and pre-sets `'Hello'` as the first argument.

### Real-world use

In React, I often use `bind` to ensure class methods retain the correct `this` when passed as event handlers:

```js
class Button extends React.Component {
  constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
  }
  handleClick() {
    console.log(this.props.label);
  }
  render() {
    return <button onClick={this.handleClick}>Click me</button>;
  }
}
```

Without `bind`, `this` inside `handleClick` would be `undefined` in strict mode.

## What is the difference between feature detection, feature inference, and using the UA string?

In frontend development, these are three strategies to handle differences in browser capabilities:

### 1. Feature Detection

- Checks **directly if a specific feature exists** before using it.
- **Reliable and recommended** approach.
- Example:

```js
if ('fetch' in window) {
  fetch('/api/data').then(res => res.json());
} else {
  // fallback or polyfill
}
```

### 2. Feature Inference

- Assumes a feature is supported based on the presence of **related features or behaviors**.
- Less reliable than detection because related features may not guarantee the one you need.
- Example:

```js
if (window.XMLHttpRequest) {
  // assume fetch exists (not always true)
}
```

### 3. User-Agent (UA) String

- Checks the **browser’s user-agent string** to guess capabilities.
- Least reliable; UA strings can be spoofed or outdated.
- Example:

```js
if (navigator.userAgent.includes('Chrome')) {
  // Chrome-specific code (risky)
}
```

## Explain "hoisting"

Hoisting in JavaScript is a behavior where **variable and function declarations are moved to the top of their containing scope** during the compilation phase, before the code executes.

### Key points

- **Function declarations** are fully hoisted, so you can call the function before it’s defined in the code.

```js
console.log(foo()); // works
function foo() { return 'hello'; }
```

- **Variable declarations** using `var` are hoisted, but **only the declaration**, not the initialization. Using the variable before assignment gives `undefined`.

```js
console.log(a); // undefined
var a = 5;
```

- `let` and `const` are also hoisted but reside in a **temporal dead zone** until their declaration line; accessing them before declaration throws a `ReferenceError`.

```js
console.log(b); // ReferenceError
let b = 10;
```

## What is type coercion? What are common pitfalls of relying on type coercion in JavaScript code?

Type coercion in JavaScript is the **automatic or implicit conversion of values from one type to another** during evaluation, especially in expressions involving different types.

### Examples

```js
console.log('5' - 2); // 3 (string '5' coerced to number)
console.log('5' + 2); // '52' (number 2 coerced to string)
console.log(true == 1); // true (boolean coerced to number)
console.log(false == 0); // true
```

### Common pitfalls

1. **Unexpected results with `==`**

```js
'' == 0; // true
null == undefined; // true
```

2. **Concatenation vs addition confusion**

```js
'5' + 2; // '52' instead of 7
```

3. **Boolean coercion in conditionals**

```js
if ('0') { console.log('runs'); } // runs, because non-empty string is truthy
```

4. **Mixing types in calculations** can lead to `NaN` or unexpected strings.

### Best practices

- Prefer **strict equality `===`** and inequality `!==` to avoid implicit coercion.
- Explicitly convert types when needed (`Number()`, `String()`, `Boolean()`).
- Be cautious in conditionals and arithmetic involving mixed types.

## Describe event bubbling

Event bubbling is a concept in the DOM where an event triggered on a **child element propagates up through its ancestors** in the hierarchy until it reaches the root or is explicitly stopped.

### Key points

- Events **bubble from the target element up to the document** by default.
- Useful for **event delegation**, where a single listener on a parent handles events for multiple children.
- Can be **stopped** using `event.stopPropagation()`.

### Example

```html
<div id="parent">
  <button id="child">Click me</button>
</div>
<script>
document.getElementById('parent').addEventListener('click', () => {
  console.log('Parent clicked');
});

document.getElementById('child').addEventListener('click', (e) => {
  console.log('Child clicked');
  // e.stopPropagation(); // uncomment to prevent bubbling
});
</script>
```

Clicking the button will log:

```bash
Child clicked
Parent clicked
```

If `stopPropagation()` is used, only “Child clicked” is logged.

## Describe event capturing

Event capturing, also called the **capture phase**, is the opposite of event bubbling. In capturing, the event **propagates from the root down to the target element** before reaching the target.

### Key points

- By default, events bubble, but capturing can be enabled by passing `true` as the third argument to `addEventListener`.
- Useful when we want a parent element to handle an event **before** the child.
- Can be combined with bubbling for fine-grained control.

### Example

```html
<div id="parent">
  <button id="child">Click me</button>
</div>
<script>
document.getElementById('parent').addEventListener('click', () => {
  console.log('Parent capturing');
}, true); // 'true' enables capture

document.getElementById('child').addEventListener('click', () => {
  console.log('Child clicked');
});
</script>
```

Clicking the button logs:

```bash
Parent capturing
Child clicked
```

In complex UIs, I sometimes use event capturing to **intercept events early** on parent containers, such as preventing clicks on a modal overlay from triggering underlying elements, before they reach child elements.

## What is the difference between an "attribute" and a "property"?

In the DOM:

### 1. Attribute

- Defined in **HTML markup**.
- Always **a string**.
- Represents the **initial/default state** of an element when the page loads.
- Can be accessed or modified with `getAttribute` and `setAttribute`.

```html
<input id="name" type="text" value="Mehdi">
```

```js
const input = document.getElementById('name');
console.log(input.getAttribute('value')); // "Mehdi"
```

### 2. Property

- Part of the **DOM object** representing the element.
- Can have **any type** (string, number, boolean, object).
- Represents the **current state** of the element, which can change dynamically.

```js
console.log(input.value); // "Mehdi"
input.value = "Bob";
console.log(input.value); // "Bob"
console.log(input.getAttribute('value')); // still "Mehdi"
```

**Key difference:** Attributes = initial HTML, Properties = current DOM state.

## What are the pros and cons of extending built-in JavaScript objects?

Extending built-in JavaScript objects (like `Array`, `Object`, `String`) means adding new methods or modifying prototypes.

### Pros

- **Convenience**: Can add utility methods globally for reuse.
- **Cleaner syntax**: Can call new methods directly on instances.
- Example:

```js
Array.prototype.last = function() {
  return this[this.length - 1];
};
[1,2,3].last(); // 3
```

### Cons

- **Risk of conflicts**: Future ECMAScript versions may add methods with the same name.
- **Breaks code predictability**: Other developers may not expect modified built-ins.
- **Can affect loops**: Using `for...in` may include added methods unless careful.
- **Maintenance issues**: Harder to debug and refactor.

### Real-world practice

In professional projects, I usually **avoid extending built-ins**. Instead, I create standalone utility functions or use libraries like Lodash to ensure code remains predictable and safe.

## What is the difference between `==` and `===`?

In JavaScript:

- **`==` (loose equality)**: Compares values **after type coercion**.

```js
'5' == 5; // true
null == undefined; // true
```

- **`===` (strict equality)**: Compares **both value and type**, no coercion.

```js
'5' === 5; // false
null === undefined; // false
```

## Explain the same-origin policy with regards to JavaScript

The **same-origin policy (SOP)** is a security concept in browsers that restricts how scripts from one origin can interact with resources from another origin.

- **Origin** is defined by **protocol, domain, and port**.
- Scripts can freely access resources (like DOM, cookies, or AJAX responses) **only if the origin matches**.
- Prevents malicious scripts from reading sensitive data on another site.

```js
// script on https://example.com
fetch('https://api.example.com/data') // allowed
fetch('https://another.com/data')   // blocked by SOP unless CORS headers allow it
```

### Workarounds

- **CORS (Cross-Origin Resource Sharing)** headers on the server.
- **JSONP** (older approach, limited to GET requests).
- **Proxying requests** through the same-origin server.

I solve it by ensuring the server sets proper `Access-Control-Allow-Origin` headers or using a proxy during development.

## Why is it called a Ternary operator, what does the word "Ternary" indicate?

The ternary operator is called “ternary” because it **takes three operands**, which is what “ternary” literally means. So “ternary” indicates that this operator **requires three inputs** to work.

The syntax is:

```js
condition ? expressionIfTrue : expressionIfFalse;
```

- **First operand:** `condition` -> evaluated as true or false
- **Second operand:** `expressionIfTrue` -> returned if condition is true
- **Third operand:** `expressionIfFalse` -> returned if condition is false

```js
const age = 18;
const canVote = age >= 18 ? 'Yes' : 'No';
console.log(canVote); // Yes
```

## What is strict mode? What are some of the advantages/disadvantages of using it?

Strict mode in JavaScript is a way to opt in to a **restricted variant of JavaScript** that catches common coding mistakes and unsafe actions.

### How to enable

```js
'use strict';
function myFunc() { /* code */ }
```

Or at the top of a script or function.

### Advantages

- **Catches errors early:** e.g., assigning to undeclared variables throws an error.
- **Prevents silent failures:** e.g., assigning to read-only properties.
- **Disables deprecated features:** e.g., `with` statement.
- **Safer `this` binding:** `this` is `undefined` in functions instead of the global object.

### Disadvantages

- **Older code may break:** legacy scripts may rely on sloppy behavior.
- **Some browser-specific quirks:** might cause subtle issues if mixing strict and non-strict code.

## What are some of the advantages/disadvantages of writing JavaScript code in a language that compiles to JavaScript?

### Advantages

- **Type safety / static analysis:** Helps catch bugs early (TypeScript).
- **Modern syntax:** Some languages offer cleaner or more expressive syntax.
- **Maintainability & scalability:** Easier to manage large codebases.
- **Optional features:** Interfaces, enums, or pattern matching not in vanilla JS.

### Disadvantages

- **Build step required:** Must compile to JS before running.
- **Learning curve:** Developers need to learn the new syntax and rules.
- **Debugging complexity:** Source maps help, but errors might appear in compiled JS.
- **Ecosystem mismatch:** Some libraries might not have type definitions or full support.

## What tools and techniques do you use debugging JavaScript code?

I use a combination of **browser tools, code analysis, and structured debugging techniques**.

### Tools

- **Browser DevTools:**

  - Breakpoints, watch expressions, call stacks.
  - Network tab for API requests.
  - Console for logging and inspection.
- **VSCode Debugger:** Debug Node.js and front-end code directly with breakpoints.
- **Linting tools (ESLint, TypeScript):** Detect potential errors before runtime.
- **Unit testing frameworks (Jest, React Testing Library):** Catch bugs in isolation.
- **Logging libraries:** More structured logging in large applications.

### Techniques

- **Console logging:** Quick checks for variable values and flow.
- **Breakpoints & step execution:** Inspect code line by line.
- **Conditional breakpoints:** Stop only when certain conditions are met.
- **Call stack inspection:** Understand the sequence of function calls.
- **Error stack traces:** Trace the origin of exceptions.
- **Binary search debugging:** Comment/uncomment sections to isolate issues.

## Explain the difference between mutable and immutable objects. What is an example of an immutable object in JavaScript? What are the pros and cons of immutability? How can you achieve immutability in your own code?

In JavaScript, the difference between **mutable** and **immutable** objects is about whether the object's state can be changed after creation.

### Mutable objects

- Their properties can be modified after creation. Most objects and arrays in JS are mutable.
- Example:

```js
const arr = [1, 2, 3];
arr.push(4); // arr is now [1,2,3,4]
```

### Immutable objects

- Cannot be changed once created. Any modification returns a new object.
- Example: **String**

```js
const str = 'hello';
const upper = str.toUpperCase(); // str remains 'hello', upper = 'HELLO'
```

### Pros of immutability

- Predictable state and fewer side effects.
- Easier debugging and reasoning about code.
- Simplifies change detection in frameworks like React.

### Cons of immutability

- Can increase memory usage due to creating new copies.
- May need more processing for large data structures.

### Achieving immutability

- Using **`const`** for variables.
- Avoid mutating objects/arrays; using methods that return new copies (`map`, `filter`, `slice`, `concat`).
- Using **Object.freeze()** to make an object immutable.

```js
const obj = Object.freeze({ name: 'Alice' });
obj.name = 'Bob'; // ignored in strict mode
```

- Using libraries like **Immer** for easier immutable updates in complex structures.

## Explain the difference between synchronous and asynchronous functions

In JavaScript, the difference between **synchronous** and **asynchronous** functions lies in how they execute and handle tasks.

### Synchronous functions

- Execute **line by line**, blocking the main thread until each completes.
- Easy to reason about, but can cause UI freezing if long-running.

```js
console.log('Start');
console.log('End'); // waits for previous line
```

### Asynchronous functions

- Allow tasks to run in the background, freeing the main thread.
- Use callbacks, promises, or async/await.
- Useful for I/O operations, timers, or network requests.

```js
console.log('Start');
setTimeout(() => console.log('Async'), 1000);
console.log('End');
// Logs: Start, End, Async
```

In projects, I use asynchronous functions for API calls and database queries, often with `async/await` for readability. This prevents blocking the UI or server event loop, ensuring smooth performance.

## What is event loop? What is the difference between call stack and task queue?

The **event loop** in JavaScript is a mechanism that allows JS, which is single-threaded, to perform **non-blocking asynchronous operations**.

### Call Stack

- Stores **currently executing functions** in LIFO (last in, first out) order.
- Synchronous code runs here.
- If the stack is empty, the event loop can process queued tasks.

### Task Queue (or Callback Queue)

- Stores **asynchronous callbacks** (e.g., `setTimeout`, promises resolved).
- FIFO (first in, first out) order.
- Event loop moves tasks from the queue to the call stack **when it’s empty**.

```js
console.log('Start');
setTimeout(() => console.log('Timeout'), 0);
Promise.resolve().then(() => console.log('Promise'));
console.log('End');
```

**Output:**

```bash
Start
End
Promise
Timeout
```

- `Start` and `End` run synchronously on the call stack.
- Promise microtask goes to the **microtask queue** (higher priority than task queue).
- setTimeout callback goes to the **task queue**.

## What are the differences between variables created using `let`, `var` or `const`? Can you change a property of an object defined via `const`? How you can change this behavior?

| Feature       | var                                 | let                                  | const           |
| ------------- | ----------------------------------- | ------------------------------------ | --------------- |
| Scope         | Function-scoped                     | Block-scoped                         | Block-scoped    |
| Hoisting      | Hoisted, initialized with undefined | Hoisted, in TDZ (temporal dead zone) | Hoisted, in TDZ |
| Reassignment  | Allowed                             | Allowed                              | Not allowed     |
| Redeclaration | Allowed                             | Not allowed in same scope            | Not allowed     |

### Example

```js
var a = 1;
let b = 2;
const c = 3;

b = 4; // (correct; ok)
c = 5; // TypeError
```

### Changing properties of a `const` object

- `const` only **prevents reassignment of the variable itself**, not its properties.

```js
const obj = { name: 'Mehdi' };
obj.name = 'Bob'; // allowed
obj = {}; // TypeError
```

- To make object truly immutable, we use `Object.freeze()`.

```js
const obj = Object.freeze({ name: 'Mehdi' });
obj.name = 'Bob'; // ignored in strict mode
```

## What are the differences between ES6 class and ES5 function constructors?

### Differences between ES6 classes and ES5 function constructors

| Feature           | ES5 Function Constructor                             | ES6 Class                                             |
| ----------------- | ---------------------------------------------------- | ----------------------------------------------------- |
| Syntax            | Function-based, prototype for methods                | `class` keyword, cleaner syntax                       |
| Hoisting          | Function declarations are hoisted                    | Class declarations are not hoisted                    |
| Methods           | Added via `Constructor.prototype.method`             | Defined inside class body, automatically on prototype |
| Inheritance       | `Child.prototype = Object.create(Parent.prototype)`  | `extends` keyword with `super()`                      |
| Strict mode       | Optional                                             | Classes are always in strict mode                     |
| `new` requirement | Can be called without `new` (may need manual checks) | Must be called with `new`, otherwise throws TypeError |

### Example

- **ES5:**

```js
function Person(name) {
  this.name = name;
}
Person.prototype.greet = function() {
  console.log('Hello, ' + this.name);
};
```

- **ES6:**

```js
class Person {
  constructor(name) {
    this.name = name;
  }
  greet() {
    console.log(`Hello, ${this.name}`);
  }
}
```

In projects, I prefer ES6 classes for cleaner syntax, easier inheritance, and strict mode by default, but I still encounter ES5 constructor patterns in legacy code or libraries.

## Can you offer a use case for the new arrow `=>` function syntax? How does this new syntax differ from other functions?

Arrow functions are particularly useful when you need **lexical `this` binding**, i.e., when you want `this` to refer to the surrounding scope rather than the function itself.

### Example

**Traditional function problem:**

```js
function Timer() {
  this.seconds = 0;
  setInterval(function() {
    this.seconds++; // 'this' is not Timer, it's window/global
    console.log(this.seconds);
  }, 1000);
}
```

**Arrow function solution:**

```js
function Timer() {
  this.seconds = 0;
  setInterval(() => {
    this.seconds++; // 'this' correctly refers to Timer
    console.log(this.seconds);
  }, 1000);
}
```

### Key differences from traditional functions

- **No own `this`**: Uses `this` from enclosing scope.
- **No `arguments` object**: Must use rest parameters instead.
- **Cannot be used as constructors**: `new` with arrow functions throws an error.
- **Concise syntax**: Can return expressions directly without `return` keyword.

I often use arrow functions for class method callbacks or inline event handlers to avoid manual `bind(this)` calls, keeping the code clean and preventing `this` binding errors.

## What advantage is there for using the arrow syntax for a method in a constructor?

Using arrow syntax for a method in a constructor ensures that **`this` is lexically bound** to the instance, eliminating the need to manually bind the method.

```js
class Timer {
  constructor() {
    this.seconds = 0;
    this.tick = () => {
      this.seconds++; // 'this' correctly refers to the Timer instance
      console.log(this.seconds);
    };
  }
}

const timer = new Timer();
setInterval(timer.tick, 1000); // works without bind
```

- **Advantage:** Cleaner code with consistent `this` handling inside callbacks, no `bind(this)` required.

## What is the definition of a higher-order function?

A **higher-order function** is a function that **either takes one or more functions as arguments, returns a function, or both**.

- Can abstract behavior and enable function composition.
- Common in functional programming patterns.

### Examples

**1. Function as argument:**

```js
function repeat(n, action) {
  for (let i = 0; i < n; i++) {
    action(i);
  }
}
repeat(3, console.log); // 0 1 2
```

**2. Function as return value:**

```js
function multiplier(factor) {
  return function(number) {
    return number * factor;
  };
}
const double = multiplier(2);
console.log(double(5)); // 10
```

In React, higher-order functions are often used for event handling, array manipulations (`map`, `filter`, `reduce`), and higher-order components (HOCs) that wrap components to add functionality.

## Can you give an example for destructuring an object or an array?

```js
// object destructuring example
const user = {
  id: 1,
  name: 'Mehdi',
  role: 'Frontend Developer'
};

const { name, role } = user;
console.log(name); // 'Mehdi'
console.log(role); // 'Frontend Developer'

// array destructuring example
const colors = ['red', 'green', 'blue'];
const [firstColor, secondColor] = colors;
console.log(firstColor); // 'red'
console.log(secondColor); // 'green'

// use case in a real project: extracting props in a React component
const Profile = ({ user }) => {
  const { name, role } = user;
  return <div>{name} - {role}</div>;
};
```

## Can you give an example of generating a string with ES6 Template Literals?

```js
const user = {
  name: 'Mehdi',
  role: 'Frontend Developer'
};

const greeting = `Hello, my name is ${user.name} and I work as a ${user.role}.`;
console.log(greeting); 
// Output: 'Hello, my name is Mehdi and I work as a Frontend Developer.'
```

In a React project, I often use template literals for dynamic rendering in JSX:

```js
const UserCard = ({ user }) => {
  return <div>{`Name: ${user.name}, Role: ${user.role}`}</div>;
};
```

It makes string interpolation cleaner compared to traditional concatenation.

## Can you give an example of a curry function and why this syntax offers an advantage?

Currying is a functional programming technique where a function, instead of taking all its arguments at once, takes them one at a time, returning a new function for each argument until all arguments are received. Once all arguments are provided, it executes the original function.

- **In simpler terms:** we transform a function like f(a, b) into f(a)(b).

```js
// curry function example
const multiply = a => b => a * b;

const double = multiply(2);
console.log(double(5)); // 10

const triple = multiply(3);
console.log(triple(5)); // 15

// real project example: in a React project, currying can help create specialized event handlers:
const handleChange = field => event => {
  console.log(`Field ${field} changed to ${event.target.value}`);
};

<input onChange={handleChange('username')} />
```

- **Advantage**: Currying allows partial application of functions, making code more reusable and composable.

## What are the benefits of using `spread syntax` and how is it different from `rest syntax`?

**Spread syntax** and **rest syntax** use the same `...` notation but serve opposite purposes:

### 1. Spread syntax: Expands elements of an array or object

- Easily clone arrays or objects without mutating the original.
- Concatenate arrays or merge objects cleanly.
- Pass array elements as individual arguments to functions.

```js
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 4, 5]; // [1, 2, 3, 4, 5]

const obj1 = { a: 1, b: 2 };
const obj2 = { ...obj1, c: 3 }; // { a: 1, b: 2, c: 3 }
```

### 2. Rest syntax: Collects remaining elements into an array or object

- Handles variable numbers of function arguments.
- Extracts a subset while keeping the rest together.

```js
const [first, ...rest] = arr2;
console.log(first); // 1
console.log(rest);  // [2, 3, 4, 5]

const { a, ...others } = obj2;
console.log(a);      // 1
console.log(others); // { b: 2, c: 3 }
```

- **Difference**

  - **Spread** “spreads out” values.
  - **Rest** “gathers” remaining values.

In practice, I use spread to merge props in React and rest to pick up extra props dynamically.

## How can you share code between files?

There are two main ways: **named exports** and **default exports**.

- **Named exports:**

```js
// utils.js
export const add = (a, b) => a + b;
export const multiply = (a, b) => a * b;

// main.js
import { add, multiply } from './utils.js';

console.log(add(2, 3));      // 5
console.log(multiply(2, 3)); // 6
```

- **Default export:**

```js
// logger.js
export default function log(message) {
  console.log(message);
}

// main.js
import log from './logger.js';
log('Hello World');
```

I use this pattern to keep utilities, constants, and React components in separate files, which keeps the codebase organized and maintainable.

## Why you might want to create static class members?

Creating static class members is useful when you want properties or methods that are shared across all instances of a class and don’t depend on individual object data.

```js
class MathUtils {
  static PI = 3.14159;

  static calculateCircleArea(radius) {
    return MathUtils.PI * radius * radius;
  }
}

console.log(MathUtils.PI); // 3.14159
console.log(MathUtils.calculateCircleArea(2)); // 12.56636
```

- **Benefits:**

  - We don’t need to instantiate the class to access them.
  - Great for constants or utility functions logically related to the class.
  - Keeps code organized and avoids polluting the global scope.

## What is the difference between `while` and `do-while` loops in JavaScript?

- **while:** May never execute if the condition is false initially.
- **do-while:** Executes at least once regardless of the condition.

```js
// while loop: condition is checked before executing
let i = 0;
while (i < 3) {
  console.log(`while loop iteration: ${i}`);
  i++;
}
// Output: 0, 1, 2

// do-while loop: body executes first, then condition is checked
let j = 0;
do {
  console.log(`do-while loop iteration: ${j}`);
  j++;
} while (j < 3);
// Output: 0, 1, 2
```

Everyone typically use `do-while` when need the loop to run at least once, like prompting a user until they enter valid input.

## What is a promise? Where and how would you use promise?

A **Promise** in JavaScript is an object that represents the eventual completion (or failure) of an asynchronous operation and its resulting value. Essentially, it’s a way to handle async operations more cleanly than callbacks, allowing chaining and better error handling.

A Promise has three states:

1. **Pending**; initial state, neither fulfilled nor rejected.
2. **Fulfilled**; the operation completed successfully.
3. **Rejected**; the operation failed.

- **Example usage:**

```javascript
// simulating an API call
function fetchUser(userId) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      const user = { id: userId, name: 'Mehdi' };
      if (user) {
        resolve(user);
      } else {
        reject('User not found');
      }
    }, 1000);
  });
}

fetchUser(1)
  .then(user => console.log('User fetched:', user))
  .catch(error => console.error('Error:', error));
```

- **Where I’d use Promises:**

  - Fetching data from REST APIs using `fetch` or Axios.
  - Reading files in Node.js asynchronously.
  - Any scenario where multiple async operations need to be chained or coordinated.

In one of my projects, we had a dashboard that needed to fetch data from multiple APIs and display aggregated stats. I used `Promise.all` to fetch all datasets concurrently, which improved load times and simplified error handling compared to nested callbacks.

## Discuss how you might use Object Oriented Programming principles when coding with JavaScript

Even though JavaScript is prototype-based, it fully supports OOP concepts such as **encapsulation**, **inheritance**, **polymorphism**, and **abstraction**. In real-world frontend development, I use OOP mainly when organizing reusable components, modeling domain entities, or building complex UI logic.

### 1. Encapsulation

Encapsulation helps keep related data and behavior together.

```js
class User {
  #password; // private field

  constructor(name, email, password) {
    this.name = name;
    this.email = email;
    this.#password = password;
  }

  checkPassword(input) {
    return input === this.#password;
  }
}
```

I use this pattern when managing form objects or complex business logic. The private fields help prevent accidental mutation.

### 2. Inheritance

Inheritance allows me to create specialized versions of a base class.

```js
class Shape {
  constructor(color) {
    this.color = color;
  }

  draw() {
    console.log("Drawing shape");
  }
}

class Rectangle extends Shape {
  constructor(color, width, height) {
    super(color);
    this.width = width;
    this.height = height;
  }

  draw() {
    console.log(`Drawing a ${this.color} rectangle`);
  }
}
```

This is useful in canvas-heavy apps, animations, or UI element systems.

### 3. Polymorphism

Polymorphism lets different classes share the same interface but have different implementations. For example, in a rendering system:

```js
const shapes = [new Rectangle('blue', 100, 50), new Shape('red')];

shapes.forEach(shape => shape.draw());
```

Each shape responds to `.draw()` differently.

### 4. Abstraction

Abstraction simplifies complexity by exposing only what’s necessary.

```js
class ApiClient {
  async get(url) {
    return fetch(url).then(res => res.json());
  }
}
```

I use abstraction to wrap API logic or create reusable services that hide implementation details.

### Real-world Example

In a past React project, I built a small state machine to manage complex component behavior. I modeled each "state" as a class, each with its own `enter()`, `exit()`, and `handleEvent()` methods. This made the code much easier to test and extend.

Even though I use OOP, I lean toward functional patterns for React components because they’re easier to reason about. But for modeling domain logic or reusable UI modules, OOP fits perfectly.

## What is the difference between `event.target` and `event.currentTarget`?

As a frontend developer, this is one of the most common event-handling concepts I use, especially when working with delegation.

- **`event.target`** -> element where the event *originated*.
- **`event.currentTarget`** -> element whose listener is *running*.

### `event.target`

`event.target` refers to **the actual element that triggered the event**, the deepest element in the DOM where the event originated.

- It can be a child element.
- It does **not** change as the event bubbles.

- **Example:** If you click a `<span>` inside a `<button>`, the `event.target` is the `<span>`.

### `event.currentTarget`

`event.currentTarget` refers to **the element that the event listener is attached to**.

- This is the element whose callback is currently executing.
- It changes at each step of the event bubbling/capturing phase.

- **Example:** If you attach a click handler to a `<button>`, and click the `<span>` inside it:

- `event.currentTarget` -> the `<button>`
- `event.target` -> the `<span>`

### When I use them in real projects

#### 1. Event Delegation

I often attach a single event listener to a parent container (e.g., a list or grid), then use `event.target` to detect which child was clicked.

```js
document.querySelector('.list').addEventListener('click', (event) => {
  if (event.target.matches('.list-item')) {
    console.log('Item clicked:', event.target.dataset.id);
  }
});
```

#### 2. Avoiding Incorrect Element Access

Sometimes devs accidentally style or modify the wrong element because they use `target` instead of `currentTarget`. I use `currentTarget` whenever I specifically need to interact with the element with the listener.

```js
button.addEventListener('click', (event) => {
  event.currentTarget.classList.add('active');
});
```

This ensures the **button** gets the class even if the user clicks an inner SVG icon.

## What is the difference between `event.preventDefault()` and `event.stopPropagation()`?

These two methods are commonly used in event handling, but they solve **completely different problems**.

| Method                  | What it does                     | Does NOT do                              |
| ----------------------- | -------------------------------- | ---------------------------------------- |
| **`preventDefault()`**  | Stops browser's default behavior | Does not stop the event from bubbling    |
| **`stopPropagation()`** | Stops event bubbling/capturing   | Does not prevent default browser actions |

## `event.preventDefault()`

This method **prevents the browser's default behavior** for a given event.

- Submitting a form reloads the page.
- Clicking an `<a>` tag navigates to a link.
- Right‑click triggers the context menu.
- Dragging text selects it.

```js
form.addEventListener('submit', (event) => {
  event.preventDefault(); // stop page reload
  validateForm();
});
```

## `event.stopPropagation()`

This method stops the event from **bubbling up** (or capturing down) the DOM tree. If we have nested elements, clicking a child normally triggers **all parent listeners** as the event bubbles.

```js
document.getElementById('child').addEventListener('click', (event) => {
  event.stopPropagation();
  console.log('Child clicked');
});

document.getElementById('parent').addEventListener('click', () => {
  console.log('Parent clicked');
});
```

Clicking the child logs **only**:

```bash
Child clicked
```

Parent won't fire.

### Real-world Example

In a dropdown component, I had a parent container that closed the dropdown on any outside click. Inside the dropdown, buttons used for actions needed to behave normally **without triggering the parent close handler**. So inside the dropdown button handler, I used:

```js
button.addEventListener('click', (event) => {
  event.stopPropagation();
});
```

And inside form submissions inside modals, I typically use:

```js
form.addEventListener('submit', (event) => event.preventDefault());
```

Both work together in many UI patterns but serve different purposes.

## Make this work

```js
duplicate([1, 2, 3, 4, 5]); // [1,2,3,4,5,1,2,3,4,5]
```

---
A simple way to implement the `duplicate` function is to return a new array containing the original array twice.

```js
function duplicate(arr) {
  return [...arr, ...arr];
}

console.log(duplicate([1, 2, 3, 4, 5])); // [1,2,3,4,5,1,2,3,4,5]
```

We can also use `concat`:

```js
function duplicate(arr) {
  return arr.concat(arr);
}
```

## Create a for loop that iterates up to `100` while outputting **"fizz"** at multiples of `3`, **"buzz"** at multiples of `5` and **"fizzbuzz"** at multiples of `3` and `5`

```js
for (let i = 1; i <= 100; i++) {
  if (i % 15 === 0) {
    console.log("fizzbuzz");
  } else if (i % 3 === 0) {
    console.log("fizz");
  } else if (i % 5 === 0) {
    console.log("buzz");
  } else {
    console.log(i);
  }
}
```

## What will be returned by each of these?

```js
console.log("hello" || "world");
console.log("foo" && "bar");
```

---

- **`"hello" || "world"` returns -> `"hello"`**
- **`"foo" && "bar"` returns -> `"bar"`**

### `||` (OR)

The OR operator returns the **first truthy value**.

- `"hello"` is truthy -> returned immediately.

### `&&` (AND)

The AND operator returns the **last value if all are truthy**.

- Both `"foo"` and `"bar"` are truthy -> returns the last one (`"bar"`).

These operators don't return booleans, they return the actual values they evaluate.

## Write an immediately invoked function expression (IIFE)

```js
(function () {
  console.log("IIFE executed");
})();
```

Or using an arrow function:

```js
(() => {
  console.log("IIFE executed");
})();
```
