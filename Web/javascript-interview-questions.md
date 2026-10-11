# JavaScript Interview Questions & Answers

A comprehensive guide covering JavaScript fundamentals through advanced concepts, the type system, scope and closures, asynchronous programming, the event loop, prototypes/OOP, ES6+ features, and performance/best practices.

---

## Table of Contents

1. [JavaScript Fundamentals](#1-javascript-fundamentals)
2. [Variables, Scope & Hoisting](#2-variables-scope--hoisting)
3. [Data Types & Type Coercion](#3-data-types--type-coercion)
4. [Operators & Comparisons](#4-operators--comparisons)
5. [Functions](#5-functions)
6. [Closures](#6-closures)
7. [`this`, `call`, `apply` & `bind`](#7-this-call-apply--bind)
8. [Objects & Prototypes](#8-objects--prototypes)
9. [Classes (ES6)](#9-classes-es6)
10. [Arrays & Array Methods](#10-arrays--array-methods)
11. [Strings, Numbers & Math](#11-strings-numbers--math)
12. [Destructuring, Spread & Rest](#12-destructuring-spread--rest)
13. [Asynchronous JavaScript: Callbacks & Promises](#13-asynchronous-javascript-callbacks--promises)
14. [Async/Await](#14-asyncawait)
15. [The Event Loop & Concurrency Model](#15-the-event-loop--concurrency-model)
16. [Error Handling](#16-error-handling)
17. [Modules](#17-modules)
18. [The DOM & Browser APIs](#18-the-dom--browser-apis)
19. [Events](#19-events)
20. [Iterators, Generators & Symbols](#20-iterators-generators--symbols)
21. [Functional Programming Concepts](#21-functional-programming-concepts)
22. [Memory Management & Performance](#22-memory-management--performance)
23. [Design Patterns](#23-design-patterns)
24. [Modern ES Features (ES2020+)](#24-modern-es-features-es2020)
25. [Miscellaneous & Best Practices](#25-miscellaneous--best-practices)

---

## 1. JavaScript Fundamentals

### Q1. What is JavaScript?
JavaScript is a high-level, interpreted (or JIT-compiled), multi-paradigm programming language, originally created to add interactivity to web pages. It's dynamically typed, prototype-based (with class syntax added as sugar in ES6), single-threaded with an event-driven concurrency model, and now runs virtually everywhere — browsers, servers (Node.js), mobile (React Native), and embedded systems.

### Q2. What is the difference between JavaScript and ECMAScript?
**ECMAScript (ES)** is the standardized specification (maintained by TC39/Ecma International) that defines the core language — syntax, types, semantics. **JavaScript** is the most well-known and widely used **implementation** of that specification (with vendor-specific extensions like the DOM/browser APIs, which are separate specifications). Other implementations of ECMAScript exist (like ActionScript), but JavaScript is by far the dominant one.

### Q3. What is the difference between a compiled and an interpreted language, and where does JavaScript fit?
Compiled languages are translated entirely into machine code ahead of execution; interpreted languages are executed line-by-line at runtime by an interpreter. JavaScript is technically a hybrid: modern engines (V8, SpiderMonkey) use **Just-In-Time (JIT) compilation** — code is initially interpreted, and "hot" (frequently executed) code paths are compiled to optimized machine code on the fly during execution, combining aspects of both approaches for a balance of fast startup and good runtime performance.

### Q4. What is the difference between JavaScript running in a browser vs. in Node.js?
The core language (ECMAScript) is identical in both. The difference is the **host environment's APIs**: browsers provide the DOM, `window`, `document`, and browser-specific APIs (localStorage, fetch); Node.js provides server-side APIs (`fs`, `http`, `process`) and lacks browser-only globals. Both typically use a V8-based (or similar) JS engine under the hood, but the surrounding runtime environment differs substantially.

### Q5. What are the main paradigms JavaScript supports?
JavaScript is **multi-paradigm**: it supports **imperative** programming (explicit step-by-step statements), **object-oriented** programming (via prototypes, and class syntax as sugar over them), and **functional** programming (functions as first-class values, closures, higher-order functions, array methods like `map`/`filter`/`reduce`) — developers can mix and match these styles freely within the same codebase.

### Q6. What does it mean that JavaScript is "single-threaded"?
JavaScript executes code on a **single call stack** — only one operation can run at a time; there's no true parallel execution of JS code within a single realm (though Web Workers/Worker Threads allow separate, isolated threads for parallel computation). Asynchronous behavior (I/O, timers, network requests) is achieved via the **event loop**, not true multi-threading of the JS execution itself.

---

## 2. Variables, Scope & Hoisting

### Q7. What is the difference between `var`, `let`, and `const`?
- **`var`** — function-scoped (or globally scoped if declared outside a function), can be redeclared and reassigned, hoisted and initialized with `undefined`.
- **`let`** — block-scoped, can be reassigned but not redeclared in the same scope, hoisted but **not** initialized (lives in the "temporal dead zone" until its declaration line executes).
- **`const`** — block-scoped like `let`, cannot be reassigned after initialization (though object/array contents referenced by a `const` binding **can** still be mutated — only the binding itself is immutable).

### Q8. What is block scope vs. function scope?
**Function scope** means a variable is accessible anywhere within the function it's declared in, regardless of nested blocks (`if`, `for`, etc.) — this is `var`'s behavior. **Block scope** means a variable is only accessible within the nearest enclosing pair of curly braces `{}` (an `if` block, a `for` loop body, or a bare block) — this is `let`/`const`'s behavior, matching the scoping model found in most other C-family languages.

### Q9. What is hoisting?
Hoisting refers to JavaScript's behavior of processing variable and function **declarations** during a compilation phase before code execution, effectively "moving" the declaration (not the initialization/assignment) to the top of its containing scope. `var` declarations are hoisted and initialized to `undefined`; `function` declarations are hoisted **with their full body**, making them callable before their textual position in the code; `let`/`const` are hoisted but remain uninitialized until their declaration line runs.

### Q10. What is the Temporal Dead Zone (TDZ)?
The period between the start of a scope (where a `let`/`const` variable is technically hoisted) and the actual line where it's declared, during which accessing the variable throws a `ReferenceError` rather than returning `undefined` — a deliberate design choice to catch bugs from using a variable before its intended declaration point, unlike `var`'s more permissive (and bug-prone) `undefined` behavior.
```js
console.log(x); // ReferenceError: Cannot access 'x' before initialization
let x = 5;
```

### Q11. Why does the classic `var` + `setTimeout` loop bug happen, and how does `let` fix it?
```js
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0); // logs 3, 3, 3
}
for (let j = 0; j < 3; j++) {
  setTimeout(() => console.log(j), 0); // logs 0, 1, 2
}
```
`var` is function/global-scoped, so all three closures created in the loop share the **same single variable `i`**, which has already reached `3` by the time the callbacks execute. `let` creates a **new binding for each iteration** of the loop, so each closure captures its own distinct `j` value at that point in the loop — this is a core reason `let` is generally preferred over `var` in loops involving closures.

### Q12. What is the global object, and how do you access it consistently across environments?
The global object (`window` in browsers, `global` in Node.js, `self` in Web Workers) holds global variables/functions and provides environment-specific globals. `globalThis` (standardized in ES2020) provides a consistent, environment-agnostic way to reference the global object regardless of where the code runs.

### Q13. What happens if you assign to an undeclared variable?
In non-strict mode, assigning to a variable that was never declared with `var`/`let`/`const` implicitly creates a **global variable** (a common source of bugs). In **strict mode**, this instead throws a `ReferenceError`, which is one of several reasons strict mode is recommended.

### Q14. What is strict mode, and how do you enable it?
A restricted variant of JavaScript (`'use strict';` at the top of a file or function) that eliminates certain silent errors by throwing them instead (like the undeclared-global-assignment case above), disallows some unsafe/confusing syntax (like duplicate parameter names), and changes some behaviors (like `this` being `undefined` instead of the global object inside a plain function call). ES6 modules and class bodies are automatically in strict mode without needing the directive explicitly.

---

## 3. Data Types & Type Coercion

### Q15. What are the primitive data types in JavaScript?
`string`, `number`, `bigint`, `boolean`, `undefined`, `null`, and `symbol` — seven primitive types (as of current ECMAScript). Everything else (objects, arrays, functions, dates, etc.) is of type `object` (with functions being a callable subtype of object).

### Q16. What is the difference between `null` and `undefined`?
`undefined` means a variable has been declared but not yet assigned a value (or a function parameter wasn't provided, or accessing a non-existent object property) — it's the language's own default "absence of value" marker. `null` is an explicit, intentional assignment representing "no value"/"empty," set deliberately by a developer. `typeof undefined === 'undefined'`, while (notoriously) `typeof null === 'object'` (a long-standing language quirk/bug preserved for backward compatibility).

### Q17. What is the difference between primitive and reference (object) types in terms of assignment and comparison?
Primitives are **copied by value** — assigning a primitive to another variable creates an independent copy. Objects (including arrays and functions) are **assigned/copied by reference** — assigning an object to another variable copies the *reference* to the same underlying object, so mutating it through either variable affects both. Comparison (`===`) similarly compares primitives by value but compares objects by reference identity (same object in memory), not by structural equality.

### Q18. What is type coercion, and what's the difference between implicit and explicit coercion?
Type coercion is the automatic or manual conversion of a value from one type to another. **Implicit coercion** happens automatically, often via operators (`'5' + 3` implicitly converts `3` to a string, yielding `'53'`; `'5' - 3` implicitly converts `'5'` to a number instead, yielding `2`, since `-` has no string-concatenation meaning). **Explicit coercion** is done deliberately by the developer via functions/syntax (`Number('5')`, `String(5)`, `Boolean(0)`, `!!value`).

### Q19. What are "truthy" and "falsy" values?
Values that evaluate to `true` or `false` respectively when coerced to a boolean context (e.g., in an `if` condition). The complete set of **falsy** values in JavaScript: `false`, `0`, `-0`, `0n` (BigInt zero), `""` (empty string), `null`, `undefined`, and `NaN`. **Everything else is truthy**, including `"0"` (a non-empty string), `[]` (an empty array), and `{}` (an empty object) — a common source of bugs for developers expecting these to be falsy.

### Q20. What is `NaN`, and what are its unusual properties?
`NaN` ("Not a Number") is a special numeric value representing the result of an invalid or undefined mathematical operation (e.g., `0/0`, `Number('abc')`). Its most notorious property: `NaN !== NaN` — it's the only value in JavaScript that is **not equal to itself**, per the IEEE 754 floating-point standard. To reliably check for `NaN`, use `Number.isNaN(value)` (which doesn't coerce its argument, unlike the global `isNaN()`) or `Object.is(value, NaN)`.

### Q21. What is `typeof`, and what are its quirks?
An operator returning a string indicating a value's type: `'string'`, `'number'`, `'boolean'`, `'undefined'`, `'object'`, `'function'`, `'symbol'`, `'bigint'`. Notable quirks: `typeof null === 'object'` (a long-standing bug), and `typeof` cannot distinguish between different object types (arrays, plain objects, dates, and `null` — arrays and dates all also report `'object'`; use `Array.isArray()` or `instanceof` for finer distinction).

### Q22. What is the difference between `==` and `===`?
`===` (strict equality) compares both **value and type** without any coercion — two values are only equal if they're the same type and have the same value (or reference, for objects). `==` (loose equality) performs **type coercion** before comparing if the operands are of different types, following a somewhat complex and often unintuitive set of coercion rules (e.g., `null == undefined` is `true`, but `null == 0` is `false`) — using `===` by default is a widely recommended best practice to avoid coercion-related surprises.

### Q23. What is `Object.is()`, and how does it differ from `===`?
`Object.is(a, b)` behaves almost identically to `===`, but with two edge-case differences: `Object.is(NaN, NaN)` returns `true` (whereas `NaN === NaN` is `false`), and `Object.is(0, -0)` returns `false` (whereas `0 === -0` is `true`) — used when these specific edge cases matter (e.g., some library/internal implementations, like React's dependency comparison in hooks).

### Q24. What is BigInt, and why was it introduced?
A primitive type (ES2020) for representing integers of **arbitrary precision**, beyond the safe integer range that the standard `number` type can represent exactly (`Number.MAX_SAFE_INTEGER`, `2^53 - 1`). Created with an `n` suffix (`123n`) or `BigInt(123)`. BigInts cannot be freely mixed with regular numbers in arithmetic operations without explicit conversion, to avoid silent precision-loss bugs.

### Q25. What is a Symbol, and what is it used for?
A primitive type representing a **guaranteed-unique** value, typically used as a non-colliding property key on objects (avoiding accidental property name clashes, including with future language features that reserve certain string keys) — e.g., `Symbol.iterator` is used to define custom iteration behavior for an object (see [Section 20](#20-iterators-generators--symbols)).

---

## 4. Operators & Comparisons

### Q26. What is the difference between `&&`, `||`, and the nullish coalescing operator `??`?
`&&` and `||` operate on **truthiness/falsiness** — `a || b` returns `a` if truthy, else `b`; this means `0 || 'default'` returns `'default'` even though `0` might be a legitimately intended value. `??` specifically checks only for **`null` or `undefined`** (not other falsy values), returning the right-hand operand only when the left side is exactly `null`/`undefined` — better suited for providing defaults without accidentally overriding legitimate falsy values like `0` or `''`.
```js
const count = 0;
console.log(count || 10); // 10 (unintended!)
console.log(count ?? 10); // 0 (correct)
```

### Q27. What is optional chaining (`?.`), and what problem does it solve?
Allows safely accessing a potentially nested, possibly-missing property/method without manually checking each level for `null`/`undefined`, short-circuiting to `undefined` instead of throwing a `TypeError` if any part of the chain is nullish.
```js
const city = user?.address?.city; // undefined instead of throwing, if address is missing
user?.sayHello?.(); // only calls sayHello if it exists
```

### Q28. What is short-circuit evaluation?
The behavior where `&&` and `||` don't necessarily evaluate their right-hand operand — `&&` stops and returns the left operand immediately if it's falsy (since the overall result must be falsy regardless); `||` stops and returns the left operand if it's truthy. This is commonly leveraged for conditional execution (`isLoggedIn && doSomething()`) and default value assignment.

### Q29. What is the difference between the comma operator, the ternary operator, and standard `if/else`?
The **comma operator** (`a, b`) evaluates both expressions left to right and returns the value of the last one, rarely used explicitly outside specific contexts like `for` loop increments. The **ternary operator** (`condition ? a : b`) is a concise, expression-based conditional (usable inline, e.g., within JSX or a variable assignment). `if/else` is a full statement, not an expression, and can't be used inline within another expression.

### Q30. What does the spread/rest syntax's `...` do, contextually?
The same `...` syntax serves two different purposes depending on context: as **spread**, it expands an iterable (array, string) or object's own enumerable properties into individual elements/properties (e.g., in a function call, array literal, or object literal). As **rest**, it collects multiple remaining arguments/elements into a single array (in a function parameter list or a destructuring pattern). See [Section 12](#12-destructuring-spread--rest) for details.

---

## 5. Functions

### Q31. What is the difference between a function declaration and a function expression?
A **function declaration** (`function foo() {}`) is hoisted entirely (including its body), making it callable before its textual position in the code. A **function expression** (`const foo = function() {}`, or an arrow function) is only hoisted as a variable declaration (per `var`/`let`/`const` rules) — the function itself isn't available until the assignment line actually executes.

### Q32. What are arrow functions, and how do they differ from regular functions?
A concise function syntax (`(a, b) => a + b`) introduced in ES6, differing from regular functions in several key ways: they **don't have their own `this`** (they lexically inherit `this` from their enclosing scope at definition time), they **don't have their own `arguments` object** (also inherited from the enclosing scope, or absent), they **cannot be used as constructors** (`new` throws an error), and they have no `prototype` property.

### Q33. When should you avoid using an arrow function?
When you specifically need a dynamic `this` binding — e.g., an object method that needs `this` to refer to the object it's called on (arrow functions would instead inherit `this` from the surrounding lexical scope, typically not what's intended), or when using the function as a **constructor** with `new`, or when you need access to the function's own `arguments` object.

### Q34. What are default parameters?
A syntax (ES6) allowing a function parameter to have a fallback value used when the caller omits that argument (or explicitly passes `undefined`):
```js
function greet(name = 'Guest') { return `Hello, ${name}`; }
greet(); // 'Hello, Guest'
```

### Q35. What are rest parameters in a function signature?
Collects any remaining arguments beyond explicitly named parameters into a real array, unlike the older `arguments` object (which is array-*like*, not a true array, and unavailable in arrow functions):
```js
function sum(...numbers) { return numbers.reduce((a, b) => a + b, 0); }
sum(1, 2, 3); // 6
```

### Q36. What is a higher-order function?
A function that either **accepts one or more functions as arguments**, **returns a function**, or both — a foundational concept enabling functional programming patterns in JavaScript (e.g., `Array.prototype.map`, `setTimeout`, or a function that returns a specialized/configured version of another function).

### Q37. What is an Immediately Invoked Function Expression (IIFE), and why is it used?
A function expression that's defined and **immediately called** in a single statement, typically used historically to create an isolated scope (avoiding polluting the global namespace) before `let`/`const`/block scoping and modules made this less necessary.
```js
(function () {
  const privateVar = 'hidden';
})();
```

### Q38. What is currying?
A functional programming technique where a function that accepts multiple arguments is transformed into a sequence of functions, each accepting a **single argument**, returning a new function until all arguments have been supplied.
```js
const add = (a) => (b) => (c) => a + b + c;
add(1)(2)(3); // 6
```

### Q39. What is function composition?
Combining two or more functions into a single new function, where the output of one becomes the input of the next — a core functional programming technique for building complex behavior from small, reusable, single-purpose functions.
```js
const compose = (...fns) => (x) => fns.reduceRight((acc, fn) => fn(acc), x);
const addOneThenDouble = compose((x) => x * 2, (x) => x + 1);
addOneThenDouble(3); // 8
```

### Q40. What is a pure function, and why does it matter?
A function that (1) always returns the same output given the same input, and (2) produces no observable **side effects** (doesn't mutate external state, perform I/O, etc.). Pure functions are easier to test, reason about, memoize, and parallelize — a core principle in functional programming and important in state-management contexts (like React reducers) that rely on predictable, side-effect-free updates.

### Q41. What is the difference between a named function expression and an anonymous function expression?
An anonymous function expression (`const f = function() {}`) has no name of its own (useful mainly for inline callbacks). A named function expression (`const f = function myFunc() {}`) has a name that's accessible **within its own body** (useful for recursion, and it also appears more helpfully in stack traces during debugging) but isn't accessible from the outer enclosing scope under that internal name — only via the variable `f` it was assigned to.

---

## 6. Closures

### Q42. What is a closure?
A closure is the combination of a function bundled together with references to its **surrounding lexical scope** (the variables that were in scope where the function was defined), allowing the function to continue accessing those variables even after the outer function has finished executing and would otherwise have had its scope discarded.
```js
function makeCounter() {
  let count = 0;
  return () => ++count;
}
const counter = makeCounter();
counter(); // 1
counter(); // 2 — count persists between calls, private to this closure
```

### Q43. Why are closures useful, and what are some common use cases?
- **Data privacy/encapsulation** — creating variables accessible only through specific returned functions (a private-state pattern, especially before ES2022 private class fields existed).
- **Function factories** — generating specialized functions with pre-configured behavior (like the `add(1)(2)(3)` currying example).
- **Maintaining state across asynchronous callbacks** (event handlers, timers) without needing external/global state.
- **Module patterns** — exposing a public API while keeping implementation details private within an enclosing function scope.

### Q44. What is a common pitfall with closures inside loops, and how do you avoid it?
As covered in Q11, using `var` in a loop that creates closures (e.g., attaching event listeners or scheduling callbacks in a loop) causes all closures to share the same variable, typically capturing its final value rather than the value at each iteration. Fixed by using `let` (block-scoped per iteration) or by wrapping the loop body in an IIFE that captures the current value explicitly.

### Q45. Can closures cause memory leaks, and how?
Yes — since a closure keeps its referenced outer variables alive as long as the closure itself is reachable, unintentionally long-lived closures (e.g., attached to an event listener or timer that's never cleaned up) can prevent otherwise-unneeded objects/data from being garbage collected, growing memory usage over time if this happens repeatedly (e.g., in a loop creating many such closures).

---

## 7. `this`, `call`, `apply` & `bind`

### Q46. What determines the value of `this` in a regular function?
`this` is determined by **how a function is called** (its "call site"), not where it's defined — this is called **dynamic** (or "call-time") binding. The rules, in order of precedence: `new` binding (constructor calls), explicit binding (`call`/`apply`/`bind`), implicit binding (called as a method: `obj.method()`, where `this` becomes `obj`), and default binding (a plain, unqualified function call — `this` is the global object in non-strict mode, or `undefined` in strict mode).

### Q47. How does `this` behave differently in an arrow function?
Arrow functions have **no `this` binding of their own** — they inherit `this` **lexically** from the enclosing scope at the time/place they're defined, and this binding cannot be changed even with `call`/`apply`/`bind`. This makes arrow functions particularly useful for callbacks that need to preserve the outer `this` (e.g., inside a class method's event handler, or inside `setTimeout`), avoiding the classic `var self = this;` workaround pattern from pre-ES6 code.

### Q48. What is the classic `this` pitfall with a method passed as a callback, and how is it fixed?
```js
const obj = {
  name: 'Alice',
  greet() { console.log(`Hi, I'm ${this.name}`); },
};
setTimeout(obj.greet, 1000); // "Hi, I'm undefined" — this is lost!
```
When `obj.greet` is passed as a bare reference (detached from `obj`), it loses its implicit binding — when later invoked by `setTimeout`, it's called as a plain function, so `this` defaults to `undefined`/the global object rather than `obj`. Fixed via `.bind(obj)`, wrapping in an arrow function (`() => obj.greet()`), or converting `greet` itself to an arrow function class field (which permanently binds `this` to the instance).

### Q49. What do `call()`, `apply()`, and `bind()` do, and how do they differ?
All three explicitly set a function's `this` value. **`call(thisArg, arg1, arg2, ...)`** invokes the function immediately, passing arguments individually. **`apply(thisArg, [argsArray])`** invokes the function immediately, passing arguments as a single array. **`bind(thisArg, arg1, ...)`** does **not** invoke the function — instead, it returns a **new function** with `this` (and optionally some leading arguments) permanently bound, to be called later.
```js
function greet(greeting) { console.log(`${greeting}, ${this.name}`); }
const person = { name: 'Bob' };
greet.call(person, 'Hi');      // 'Hi, Bob'
greet.apply(person, ['Hi']);   // 'Hi, Bob'
const boundGreet = greet.bind(person);
boundGreet('Hi');               // 'Hi, Bob'
```

### Q50. What is the value of `this` inside a class method, and what pitfall arises when passing it as a callback?
Inside a regular class method, `this` refers to the instance when called via `instance.method()` — but like any regular function, it loses that binding if detached and called elsewhere (e.g., passed directly as an event handler). Common fixes: binding the method in the constructor (`this.method = this.method.bind(this)`), or defining the method as an **arrow function class field** (`method = () => {...}`), which captures `this` lexically at the point of the instance's creation and remains bound regardless of how it's later called.

---

## 8. Objects & Prototypes

### Q51. What is the prototype chain, and how does JavaScript implement inheritance?
Every JavaScript object has an internal link (`[[Prototype]]`, accessible via `Object.getPrototypeOf()` or the deprecated `__proto__`) to another object, its **prototype**. When accessing a property/method on an object, if it's not found directly on the object itself, JavaScript automatically looks up the **prototype chain** (the object's prototype, then that prototype's own prototype, and so on) until it finds the property or reaches `null` (the end of the chain). This prototype-based delegation is JavaScript's native inheritance mechanism — class syntax (Section 9) is syntactic sugar built on top of it.

### Q52. What is the difference between `Object.create()`, a constructor function, and class syntax for creating objects with shared behavior?
- **`Object.create(proto)`** creates a new object with its prototype explicitly set to `proto` — the most direct, low-level way to set up prototypal inheritance.
- **Constructor functions** (`function Person() {}`, invoked with `new`) create objects whose prototype is automatically set to the constructor function's `.prototype` property — the traditional pre-ES6 pattern for prototype-based "classes."
- **`class` syntax** (ES6) is syntactic sugar over the same constructor-function-and-prototype mechanism, providing cleaner, more familiar syntax (and some genuine behavioral differences, like requiring `new` and not being hoisted the same way) without changing JavaScript's underlying prototypal nature.

### Q53. What happens when you access `obj.prop` and `prop` doesn't exist anywhere on the prototype chain?
It returns `undefined` (rather than throwing an error) — accessing a genuinely non-existent property is not an error in JavaScript, unlike calling a non-existent method (which throws a `TypeError`, since you're attempting to invoke `undefined` as a function).

### Q54. What is the difference between `Object.freeze()`, `Object.seal()`, and `Object.preventExtensions()`?
- **`Object.preventExtensions()`** — prevents new properties from being added, but existing properties can still be modified or deleted.
- **`Object.seal()`** — prevents adding or deleting properties, but existing property **values** can still be modified.
- **`Object.freeze()`** — the strictest: prevents adding, deleting, **and** modifying existing properties (making the object fully immutable at the top level).

All three are **shallow** — nested objects within a frozen/sealed object remain fully mutable unless recursively frozen/sealed themselves.

### Q55. What are getters and setters in JavaScript objects?
Special object properties defined using `get`/`set` syntax that run a function when the property is **read** or **assigned to**, respectively — allowing computed properties or validation/side-effect logic to run transparently as if it were a plain property access.
```js
const person = {
  firstName: 'Jane',
  lastName: 'Doe',
  get fullName() { return `${this.firstName} ${this.lastName}`; },
  set fullName(value) { [this.firstName, this.lastName] = value.split(' '); },
};
person.fullName; // 'Jane Doe' (calls the getter)
```

### Q56. What is the difference between `Object.keys()`, `Object.values()`, `Object.entries()`, and `for...in`?
`Object.keys(obj)` / `Object.values(obj)` / `Object.entries(obj)` return an array of an object's **own enumerable** property names, values, or `[key, value]` pairs, respectively — not including inherited properties from the prototype chain. `for...in` iterates over **all enumerable properties, including inherited ones** from the prototype chain (a common source of unexpected bugs if the prototype chain includes unwanted enumerable properties), which is why `Object.keys()`/`entries()` combined with `for...of`, or `.hasOwnProperty()` checks within a `for...in` loop, are generally preferred.

### Q57. What is the difference between shallow copy and deep copy, and how do you perform each?
A **shallow copy** duplicates only the top-level properties of an object — nested objects/arrays are still shared by reference with the original (`Object.assign({}, obj)`, or spread syntax `{ ...obj }`). A **deep copy** recursively duplicates all nested levels so the copy shares no references with the original — achievable via `structuredClone(obj)` (a modern, built-in solution supporting many complex types), `JSON.parse(JSON.stringify(obj))` (a common but lossy approach — fails on functions, `undefined`, `Date` objects, circular references, etc.), or a dedicated utility library (e.g., Lodash's `cloneDeep`).

### Q58. What is property enumerability, and how does it relate to `for...in` and `Object.keys()`?
Every object property has an internal `enumerable` attribute (part of its **property descriptor**, alongside `writable` and `configurable`) determining whether it shows up in enumeration methods like `for...in` and `Object.keys()`. Properties defined via `Object.defineProperty()` default to `enumerable: false` unless specified otherwise — a mechanism sometimes used deliberately to hide certain properties (like internal/utility methods) from casual enumeration while keeping them directly accessible.

### Q59. What is `Object.defineProperty()`, and why would you use it directly instead of normal assignment?
Allows precise control over a property's descriptor — its value, `writable` (can it be reassigned), `enumerable` (does it show in enumeration), and `configurable` (can it be redefined/deleted) attributes — useful for creating read-only properties, hidden/non-enumerable internal properties, or implementing getters/setters programmatically, beyond what plain object literal syntax allows.

---

## 9. Classes (ES6)

### Q60. What is class syntax in JavaScript, and is it "real" classical inheritance?
`class` syntax (ES6) provides a cleaner, more familiar declarative way to define constructor functions and their prototype methods, but it's fundamentally **syntactic sugar over JavaScript's existing prototype-based inheritance** — there's no new underlying object model; a `class` still produces a constructor function with a `.prototype` object, and instances still resolve methods via the prototype chain.
```js
class Animal {
  constructor(name) { this.name = name; }
  speak() { return `${this.name} makes a sound.`; }
}
```

### Q61. What are some genuine behavioral differences between `class` syntax and pre-ES6 constructor functions (not just syntax)?
- Class declarations are **not hoisted** the same way function declarations are — they exist in the Temporal Dead Zone until their definition line executes, similar to `let`/`const`.
- Class bodies are **always in strict mode**, regardless of the surrounding code.
- Class constructors **must be called with `new`** — calling a class as a plain function throws a `TypeError` (unlike a regular constructor function, which can technically be called without `new`, albeit incorrectly).
- Methods defined in a class body are **non-enumerable** by default (unlike methods manually added to `.prototype`, which are enumerable unless configured otherwise).

### Q62. How does `extends` and `super` work in class inheritance?
`extends` establishes a subclass relationship, linking the subclass's prototype chain to the parent class. `super(...)`, called within a subclass's constructor, invokes the parent class's constructor (required **before** accessing `this` in a subclass constructor). `super.methodName()` calls a specific parent class method, useful for extending (rather than fully overriding) inherited behavior.
```js
class Dog extends Animal {
  constructor(name, breed) {
    super(name);
    this.breed = breed;
  }
  speak() { return `${super.speak()} Specifically, a bark.`; }
}
```

### Q63. What are static methods/properties in a class?
Members defined with the `static` keyword that belong to the **class itself**, not to instances — called directly on the class (`ClassName.staticMethod()`) rather than on an instance, commonly used for utility/factory functions related to the class that don't need instance-specific data.
```js
class MathUtils {
  static square(x) { return x * x; }
}
MathUtils.square(4); // 16
```

### Q64. What are private class fields/methods, and how do you declare them?
ES2022 introduced true **private** class members, prefixed with `#`, accessible only from within the class body itself — genuinely inaccessible from outside (unlike the older convention of prefixing with `_` as a mere naming signal, which provides no actual enforcement).
```js
class BankAccount {
  #balance = 0;
  deposit(amount) { this.#balance += amount; }
  getBalance() { return this.#balance; }
}
```

### Q65. What are class fields (public instance fields), and how do they compare to setting properties in the constructor?
A more concise syntax (ES2022, though usable earlier via transpilers) for declaring instance properties directly in the class body, without needing an explicit constructor assignment — commonly used for initializing default values or for arrow-function-bound methods (see Q50) that need permanently-bound `this`.
```js
class Counter {
  count = 0; // class field, equivalent to this.count = 0 in the constructor
  increment = () => { this.count++; }; // arrow function field — permanently bound `this`
}
```

### Q66. Are getters, setters, and computed method names supported inside class bodies?
Yes — class bodies support `get`/`set` accessor methods (identical semantics to plain object getters/setters, Q55), and computed method names using bracket syntax (`[expression]() {}`), providing the same flexibility available in object literals.

---

## 10. Arrays & Array Methods

### Q67. What is the difference between mutating and non-mutating array methods? Give examples of each.
**Mutating** methods modify the original array in place: `push`, `pop`, `shift`, `unshift`, `splice`, `sort`, `reverse`, `fill`. **Non-mutating** methods return a **new** array (or value) without modifying the original: `map`, `filter`, `slice`, `concat`, `reduce`, and (since ES2023) the new immutable variants `toSorted`, `toReversed`, `toSpliced`, `with`. Understanding this distinction is important for avoiding unintended side effects, especially relevant in contexts requiring immutability (like React state updates).

### Q68. What is the difference between `map()` and `forEach()`?
`map()` returns a **new array** containing the results of calling the provided function on every element (and is intended for **transformation**). `forEach()` returns `undefined` and is intended purely for **side effects** (like logging or mutating external state) — using `map()` without using its return value (or `forEach()` when you actually need a transformed array) is a common stylistic/functional mistake.

### Q69. What is `reduce()`, and what can it be used for beyond just summing numbers?
`reduce((accumulator, current, index, array) => ..., initialValue)` iterates through an array, accumulating a single result by applying a reducer function to each element in sequence. Beyond summation, it can implement virtually any array transformation — flattening nested arrays, grouping items into an object, building a pipeline of transformations, or even implementing `map`/`filter` themselves — making it one of the most powerful and general-purpose array methods.
```js
const groupedByCategory = items.reduce((acc, item) => {
  (acc[item.category] ??= []).push(item);
  return acc;
}, {});
```

### Q70. What is the difference between `filter()`, `find()`, and `findIndex()`?
`filter()` returns a **new array** of all elements matching a predicate. `find()` returns the **first matching element itself** (or `undefined` if none match). `findIndex()` returns the **index** of the first matching element (or `-1` if none match). (ES2023 also added `findLast()`/`findLastIndex()` for searching from the end.)

### Q71. What is the difference between `slice()` and `splice()`?
`slice(start, end)` returns a **new array** containing a shallow copy of a portion of the original, **without** modifying it. `splice(start, deleteCount, ...itemsToInsert)` **mutates** the original array in place, removing and/or inserting elements, and returns an array of the **removed** elements.

### Q72. What is the difference between `Array.isArray()` and using `typeof` to check if something is an array?
`typeof someArray` returns `'object'` for arrays (since arrays are a subtype of object) — indistinguishable from a plain object via `typeof` alone. `Array.isArray()` correctly and reliably identifies actual arrays specifically, and is the recommended way to check.

### Q73. How do `some()` and `every()` differ?
`some(predicate)` returns `true` if **at least one** element satisfies the predicate (short-circuits on the first match). `every(predicate)` returns `true` only if **all** elements satisfy the predicate (short-circuits on the first non-match, i.e., the first failure).

### Q74. How do you flatten a nested array in JavaScript?
```js
[1, [2, [3, 4]], 5].flat(); // [1, 2, [3, 4], 5] — flattens one level by default
[1, [2, [3, 4]], 5].flat(Infinity); // [1, 2, 3, 4, 5] — fully flattened
```
`flat(depth)` (ES2019) flattens nested arrays up to the specified depth (default 1); `flatMap()` combines `map()` followed by a single-level `flat()` in one efficient pass, useful when a mapping function itself returns arrays that should be merged into the result rather than nested.

### Q75. What is the difference between `Array.from()` and using the spread operator to convert an iterable/array-like object to an array?
Both can convert iterables (`Set`, `Map`, strings) and array-like objects (`NodeList`, `arguments`) into a real array. `Array.from(iterable, mapFn)` additionally accepts an optional **mapping function** applied during conversion, and can also handle array-like objects that aren't technically iterable (having only a `.length` and indexed properties, like a plain `{ 0: 'a', 1: 'b', length: 2 }`) — something spread syntax (`[...x]`) cannot do, since spread strictly requires an iterable.

---

## 11. Strings, Numbers & Math

### Q76. What is the difference between template literals and regular string concatenation?
Template literals (backtick-delimited strings, ES6) allow embedded expressions via `${expression}` interpolation and support **multi-line strings** natively, avoiding both manual `+` concatenation and explicit `\n` escapes for readability.
```js
const name = 'World';
console.log(`Hello, ${name}!
Welcome.`);
```

### Q77. What are tagged template literals?
A template literal preceded immediately by a function name, allowing that function to process the template's string parts and interpolated values programmatically before producing the final result — used for things like custom string sanitization/escaping (e.g., safely building SQL/HTML), internationalization, or styled-components-style CSS-in-JS libraries.
```js
function highlight(strings, ...values) {
  return strings.reduce((acc, str, i) => `${acc}${str}${values[i] ? `<b>${values[i]}</b>` : ''}`, '');
}
highlight`Score: ${score} points`;
```

### Q78. What is the difference between `Number.isInteger()`, `parseInt()`, and `Number()` for converting/checking numeric values?
`Number(value)` attempts to convert an entire value to a number, returning `NaN` if the whole string isn't cleanly numeric. `parseInt(value, radix)` parses a **leading numeric portion** of a string, ignoring trailing non-numeric characters (`parseInt('42px')` returns `42`), and importantly should always be called with an explicit radix (base) argument to avoid ambiguity. `Number.isInteger(value)` checks whether a given **number** value (not a string) is an integer, without any coercion — passing a string always returns `false`, unlike the global `isFinite`/`isNaN` functions, which do coerce.

### Q79. Why is floating-point arithmetic sometimes imprecise in JavaScript (e.g., `0.1 + 0.2 !== 0.3`)?
JavaScript uses the **IEEE 754 double-precision floating-point** standard for all numbers, which cannot represent most decimal fractions (like 0.1) with perfect binary precision — the same limitation exists in virtually all languages using this standard, not something unique to JavaScript. Practical fixes: rounding to a fixed number of decimal places for display (`toFixed()`), comparing with a small tolerance (`Math.abs(a - b) < Number.EPSILON`), or using integer-based arithmetic (e.g., representing currency in cents) to avoid fractional precision issues entirely.

### Q80. What are common `Math` object methods used in interviews/practice?
`Math.max()`/`Math.min()` (note: these don't natively accept an array — use spread: `Math.max(...arr)`), `Math.round()`/`Math.floor()`/`Math.ceil()`, `Math.random()` (returns a float in `[0, 1)`), `Math.pow(base, exp)` (or the `**` exponentiation operator), `Math.abs()`, and `Math.sqrt()`.

### Q81. What are common string methods for searching, transforming, and formatting?
`includes()`, `startsWith()`, `endsWith()` (membership/prefix/suffix checks), `slice()`/`substring()` (extracting a portion — note subtle behavioral differences with negative indices), `split()` (string to array), `trim()`/`trimStart()`/`trimEnd()`, `padStart()`/`padEnd()` (padding to a target length), `replace()`/`replaceAll()` (string or regex-based replacement), and `toUpperCase()`/`toLowerCase()`.

### Q82. How do you reverse a string in JavaScript, and why is there no built-in `.reverse()` method for strings directly?
Strings are immutable and don't have a native `reverse()` method (unlike arrays); the common approach converts to an array, reverses, and rejoins:
```js
const reversed = str.split('').reverse().join('');
```
(Note: this approach can mishandle multi-byte Unicode characters/emoji correctly represented as surrogate pairs — more robust Unicode-aware reversal requires using `Array.from(str)` or the spread operator, which correctly iterate by Unicode code point rather than raw UTF-16 code unit.)

---

## 12. Destructuring, Spread & Rest

### Q83. What is destructuring, and what are examples of array and object destructuring?
A syntax (ES6) for unpacking values from arrays or properties from objects into distinct variables in a single, concise expression.
```js
const [first, second] = [1, 2]; // array destructuring
const { name, age } = { name: 'Alice', age: 30 }; // object destructuring
const { name: userName } = user; // renaming during destructuring
const { address: { city = 'Unknown' } = {} } = user; // nested + default value
```

### Q84. How do you swap two variables' values using destructuring?
```js
let a = 1, b = 2;
[a, b] = [b, a]; // a = 2, b = 1
```
Destructuring provides a concise alternative to using a temporary third variable, since the entire right-hand array is evaluated first before assignment.

### Q85. How does destructuring work with function parameters, and why is it commonly used for options objects?
Destructuring can be applied directly in a function's parameter list, commonly used to cleanly accept a configuration/options object with named (and optionally defaulted) properties, avoiding long positional parameter lists that are error-prone to call correctly.
```js
function createUser({ name, age = 18, role = 'user' } = {}) {
  return { name, age, role };
}
```

### Q86. What is the difference between spread and rest syntax, given they use the same `...` symbol?
**Spread** *expands* an iterable/object into individual elements/properties — used in array/object literals or function call arguments (`[...arr1, ...arr2]`, `{ ...obj1, ...obj2 }`, `fn(...args)`). **Rest** *collects* multiple values into a single array — used in function parameter lists or destructuring patterns (`function f(...args) {}`, `const [first, ...rest] = arr;`). The direction of "expand vs collect" (and the syntactic position — right-hand collection vs. either-side expansion) determines which behavior applies.

### Q87. How does object spread handle property conflicts, and how is this useful for immutable updates?
When spreading multiple objects together, **later properties override earlier ones** with the same key — commonly leveraged for creating an updated copy of an object without mutating the original (a common pattern in Redux reducers/React state updates):
```js
const updatedUser = { ...user, age: user.age + 1 }; // all other fields preserved, age updated
```

---

## 13. Asynchronous JavaScript: Callbacks & Promises

### Q88. What is a callback function, and what is "callback hell"?
A callback is a function passed as an argument to another function, to be invoked later (often upon completion of an asynchronous operation). "Callback hell" refers to deeply **nested** callbacks (each dependent on the previous one's result), producing hard-to-read, pyramid-shaped code that's difficult to maintain, debug, and handle errors within consistently — a key motivation for the introduction of Promises.

### Q89. What is a Promise, and what are its three states?
A Promise is an object representing the eventual completion (or failure) of an asynchronous operation and its resulting value. It exists in one of three mutually exclusive states: **pending** (initial state, neither fulfilled nor rejected), **fulfilled** (the operation completed successfully, with a resulting value), or **rejected** (the operation failed, with a reason/error). Once settled (fulfilled or rejected), a Promise's state and value are **immutable** — it cannot transition to a different state or value afterward.

### Q90. How do you create a Promise, and how do `resolve`/`reject` work?
```js
const promise = new Promise((resolve, reject) => {
  doSomethingAsync((err, result) => {
    if (err) reject(err);
    else resolve(result);
  });
});
```
The **executor function** passed to the `Promise` constructor runs immediately/synchronously, and calling `resolve(value)` or `reject(reason)` within it transitions the promise to fulfilled or rejected accordingly (only the first call to either has any effect — subsequent calls are ignored).

### Q91. What is Promise chaining, and how does returning a value (or another Promise) from `.then()` affect the chain?
Each `.then()` call returns a **new Promise**, allowing chaining. If the callback passed to `.then()` returns a plain value, the next `.then()` in the chain receives that value directly. If it returns **another Promise**, the chain automatically "flattens" — waiting for that inner promise to settle before passing its resolved value along, rather than nesting promises within promises.
```js
fetchUser(id)
  .then(user => fetchPosts(user.id)) // returns a Promise, chain waits for it
  .then(posts => console.log(posts))
  .catch(err => console.error(err));
```

### Q92. How does error propagation work in a Promise chain, and what does `.catch()` actually do?
A rejection at any point in a chain **skips forward** past any subsequent `.then()` handlers (which only handle the fulfilled case) until it reaches a `.catch()` (or a `.then()` call that provides a second, rejection-handling argument) — meaning a single `.catch()` at the end of a chain can handle errors from **any** step earlier in that chain, similar to how a single `try/catch` can catch errors from multiple statements.

### Q93. What is `.finally()`, and when is it used?
Registers a callback that runs when the promise **settles**, regardless of whether it was fulfilled or rejected — commonly used for cleanup logic that must run either way (hiding a loading spinner, closing a connection), without needing to duplicate that logic in both the success and error paths.

### Q94. Explain `Promise.all()`, `Promise.allSettled()`, `Promise.race()`, and `Promise.any()`, with example use cases.
- **`Promise.all(promises)`** — resolves when **all** input promises resolve, with an array of their results; rejects immediately if **any** rejects. Use case: fetching multiple independent resources that are all required before proceeding.
- **`Promise.allSettled(promises)`** — always resolves once every promise has settled, returning each result's status (`fulfilled`/`rejected`) individually. Use case: running multiple independent operations where partial failures shouldn't abort the whole batch.
- **`Promise.race(promises)`** — settles as soon as the **first** promise settles (whichever direction). Use case: implementing a timeout by racing an operation's promise against a `setTimeout`-based rejection.
- **`Promise.any(promises)`** — resolves as soon as the **first** promise fulfills; rejects only if **all** reject. Use case: trying multiple redundant sources/mirrors and using whichever responds successfully first.

### Q95. What happens if a Promise rejection is never handled (no `.catch()` anywhere in the chain)?
It becomes an **unhandled promise rejection** — in browsers, this fires an `unhandledrejection` event on `window` (loggable/observable, but doesn't crash the page by default); in Node.js (since v15), an unhandled rejection **terminates the process** by default. It's considered best practice to always attach error handling to every promise chain.

### Q96. What is the difference between synchronous and asynchronous errors when working with Promises — why does `throw` inside a `.then()` callback not require a `try/catch`?
Any error **thrown** inside a `.then()`/`.catch()` callback (or a rejected value returned from an executor) is automatically caught by the Promise machinery and converted into a **rejection** of the resulting chained promise, propagating to the next `.catch()` handler — this is a key benefit of Promises over raw callbacks, which have no built-in, unified error-propagation mechanism.

---

## 14. Async/Await

### Q97. What is `async`/`await`, and how does it relate to Promises?
Syntactic sugar (ES2017) built directly on top of Promises, allowing asynchronous code to be written in a more linear, synchronous-looking style. An `async function` always **implicitly returns a Promise** (wrapping its return value, or the rejection reason of any thrown error). `await` **pauses** execution within that async function (without blocking the rest of the program/JS thread) until the awaited Promise settles, then either returns its resolved value or throws its rejection reason.

### Q98. How do you handle errors in `async`/`await` code?
Using a standard `try/catch` block wrapping the `await` call(s), since a rejected awaited promise is converted into a thrown exception within the `async` function's execution:
```js
async function getUser(id) {
  try {
    const response = await fetch(`/api/users/${id}`);
    if (!response.ok) throw new Error('Request failed');
    return await response.json();
  } catch (err) {
    console.error('Failed to fetch user:', err);
    throw err; // re-throw if the caller needs to know too
  }
}
```

### Q99. What is a common performance pitfall when using multiple independent `await` calls sequentially, and how do you fix it?
```js
// Slow — runs sequentially, one after another
const user = await fetchUser();
const posts = await fetchPosts(); // waits unnecessarily for fetchUser to finish first

// Fast — runs concurrently
const [user, posts] = await Promise.all([fetchUser(), fetchPosts()]);
```
If the operations don't actually depend on each other's results, awaiting them one at a time forces unnecessary sequential waiting; running them concurrently via `Promise.all()` (or by starting both promises before awaiting either) significantly reduces total wait time.

### Q100. Can you use `await` at the top level of a module, outside an `async` function?
Yes — **top-level await** (ES2022) is supported within ES modules (not in regular scripts or CommonJS `require`-based files), allowing a module to `await` a promise directly during its own initialization/evaluation, useful for scenarios like awaiting a dynamic import or an async configuration/connection step before the rest of the module's exports are finalized.

### Q101. How do you run asynchronous operations in a loop, and what's the difference between using `for...of` with `await` vs. `forEach()` with an async callback?
```js
// Correct — sequential execution, awaits properly
for (const id of ids) {
  await processItem(id);
}

// Broken — forEach does NOT wait for the async callbacks; all fire "simultaneously"
// and any errors won't be caught by a surrounding try/catch around the forEach call
ids.forEach(async (id) => { await processItem(id); });
```
`forEach()` ignores the return value of its callback (including any promise it returns) and doesn't wait for it — using an `async` callback with `forEach()` silently fires off all the async operations without any sequencing or error-propagation guarantee, a very common and subtle bug. A `for...of` loop with `await` correctly pauses on each iteration; `Promise.all(ids.map(id => processItem(id)))` correctly runs all iterations **concurrently** while still allowing proper awaiting/error handling.

---

## 15. The Event Loop & Concurrency Model

### Q102. What is the JavaScript execution/concurrency model, at a high level?
JavaScript uses a **single-threaded**, event-driven model consisting of: a **call stack** (tracks currently executing function calls), a **heap** (memory allocation for objects), task/callback **queues** (holding callbacks ready to run once the stack is clear), and the **event loop** (continuously checks whether the call stack is empty, and if so, pulls the next callback from a queue to execute) — this is how JS achieves non-blocking asynchronous behavior despite having only one thread of JS execution.

### Q103. What is the call stack, and what happens when it overflows?
The call stack tracks the sequence of function calls currently in progress — each function call pushes a new **stack frame**; returning from a function pops it off. Deeply nested/unterminated recursive calls (with no base case, or simply too many levels of legitimate recursion) exceed the stack's size limit, throwing a `RangeError: Maximum call stack size exceeded` ("stack overflow").

### Q104. What is the difference between the macrotask queue and the microtask queue?
The **microtask queue** holds callbacks from Promises (`.then`/`.catch`/`.finally`), `queueMicrotask()`, and `MutationObserver` callbacks. The **macrotask (task) queue** holds callbacks from `setTimeout`/`setInterval`, I/O events, and UI rendering-related events. Critically, **all** pending microtasks are fully drained (processed to completion, including any new microtasks they themselves enqueue) **before** the event loop proceeds to process the next single macrotask — meaning microtasks always run before the next macrotask, even if scheduled after it.

### Q105. Given the following code, what is the logged output order, and why?
```js
console.log('1');
setTimeout(() => console.log('2'), 0);
Promise.resolve().then(() => console.log('3'));
console.log('4');
```
Output: `1, 4, 3, 2`. Synchronous code (`console.log('1')` and `'4'`) runs first, completing the initial call stack execution entirely. Then, the microtask queue (the resolved Promise's `.then()`) is drained **before** the macrotask queue (the `setTimeout` callback), even though the `setTimeout` was scheduled earlier in the source code — illustrating the microtask-before-macrotask priority rule.

### Q106. What is `queueMicrotask()`, and when might you use it directly?
Schedules a callback to run as a microtask, with the same "before the next macrotask" priority as a resolved Promise's `.then()` callback, without needing to actually create/resolve a Promise — useful for deferring a piece of synchronous-feeling logic to run just after the current synchronous execution finishes, but still before any timers/I/O callbacks.

### Q107. Why does heavy synchronous computation "block" the UI in a browser, and what can be done about it?
Since rendering/UI updates and other event-loop-driven work (user input handling, timers) also depend on the single call stack being free, a long-running synchronous operation (e.g., a large loop, expensive computation) prevents **anything else** — including rendering and user interaction — from happening until it completes, causing a frozen/unresponsive UI. Mitigations: breaking the work into smaller chunks yielded across multiple event loop turns (e.g., via `setTimeout(fn, 0)` or `requestIdleCallback`), or offloading genuinely heavy computation to a **Web Worker**, which runs on a separate thread entirely.

### Q108. What is a Web Worker, and how does it enable "parallelism" in JavaScript despite the single-threaded model?
A Web Worker runs JavaScript code on a genuinely separate background thread, with its own independent call stack, heap, and event loop — allowing CPU-intensive work to run without blocking the main UI thread. Workers communicate with the main thread via message passing (`postMessage`/`onmessage`), since they don't share memory directly (aside from more advanced, opt-in mechanisms like `SharedArrayBuffer`) — this is JavaScript's primary mechanism for achieving true parallel execution, distinct from the concurrency (single-threaded, interleaved) model of the standard event loop.

---

## 16. Error Handling

### Q109. What is the difference between `try/catch/finally` blocks and how do they interact?
`try` wraps code that might throw an error. `catch(err)` runs only if an error was thrown within the `try` block, receiving the thrown value. `finally` runs **unconditionally**, whether or not an error occurred (and even if the `try`/`catch` block contains a `return` statement) — commonly used for cleanup logic (closing a resource, hiding a spinner) that must always execute.

### Q110. What are the built-in Error types in JavaScript?
`Error` (the base type), and several more specific built-in subtypes: `TypeError` (an operation performed on a value of the wrong type, e.g., calling something that isn't a function), `RangeError` (a value outside an allowed range, e.g., an invalid array length or excessive recursion), `ReferenceError` (referencing an undeclared variable), `SyntaxError` (invalid code syntax, typically thrown during parsing rather than catchable at runtime for the code being parsed itself), and `URIError` (malformed URI-handling function arguments).

### Q111. How do you create a custom error type, and why would you?
Extend the built-in `Error` class, allowing you to attach additional structured context and enable `instanceof` checks to distinguish error types in a `catch` block — useful for centralized, type-aware error handling logic (e.g., displaying different messages for validation errors vs. network errors).
```js
class ValidationError extends Error {
  constructor(message, field) {
    super(message);
    this.name = 'ValidationError';
    this.field = field;
  }
}
```

### Q112. Can `try/catch` catch errors from asynchronous code (like a `setTimeout` callback or a Promise executor)?
Not directly — a `try/catch` only catches errors that occur **synchronously within its own execution**. An error thrown inside a `setTimeout` callback (which runs later, as a separate call stack execution entirely) will **not** be caught by a `try/catch` wrapping the `setTimeout()` call itself. However, `await`ing a Promise **does** allow `try/catch` to catch its rejection, since `await`'s pausing behavior effectively brings the asynchronous rejection back into the synchronous flow of the `async` function at that point.

### Q113. What is the difference between throwing an `Error` object and throwing a plain value (e.g., a string)?
While JavaScript technically allows throwing **any** value (`throw 'oops'`, `throw 42`), throwing an actual `Error` (or subclass) instance is strongly recommended — `Error` objects automatically capture a **stack trace** (`.stack`) at the point they're constructed, which is invaluable for debugging, and provides a consistent, expected shape (`.message`, `.name`) that error-handling code and tooling generally expect.

### Q114. What are `window.onerror` and the `unhandledrejection` event used for?
Global, catch-all handlers for errors that escape any explicit `try/catch`: `window.onerror` (or `addEventListener('error', ...)`) fires for uncaught synchronous errors anywhere in the page's script execution, while the `unhandledrejection` event fires for Promise rejections that were never handled by a `.catch()`. Both are commonly used as a last-resort safety net for logging/reporting unexpected errors to an error-tracking service (Sentry, etc.) in production.

---

## 17. Modules

### Q115. What are JavaScript modules (ES Modules), and what problem do they solve?
ES Modules (`import`/`export` syntax, standardized in ES6) allow JavaScript code to be split across multiple files, each with its own **isolated scope** (avoiding global namespace pollution, a significant problem in pre-module JavaScript where all scripts shared one global scope), with explicit, declared dependencies between files via `import`/`export` statements.

### Q116. What is the difference between a named export and a default export?
```js
// Named exports — a module can have multiple, imported by exact name (optionally renamed)
export const PI = 3.14;
export function add(a, b) { return a + b; }
import { PI, add as sum } from './math.js';

// Default export — a module can have at most one, imported under any chosen name
export default function Calculator() {}
import Calc from './Calculator.js';
```

### Q117. What is the difference between ES Modules and CommonJS modules (`require`/`module.exports`)?
ES Modules use **static** `import`/`export` syntax, analyzed at parse time (before execution), enabling tree-shaking (bundlers can statically determine and eliminate unused exports) and are inherently asynchronous-loading-capable. CommonJS (`require()`/`module.exports`) resolves and loads modules **dynamically at runtime**, is synchronous, and doesn't support the same static analysis benefits — historically the standard in Node.js, though modern Node.js supports ES Modules natively as well (see the Node.js interview guide's Modules section for more detail).

### Q118. What is a dynamic `import()`, and how does it differ from a static `import` statement?
`import()` is a function-like syntax that returns a **Promise**, allowing a module to be loaded **asynchronously and conditionally** at runtime (e.g., only when a certain code path/feature is actually used), unlike static `import` statements, which must appear at the top level of a module and are resolved eagerly before the module executes. Commonly used for **code splitting** — lazily loading a large dependency or route-specific code only when actually needed, reducing initial bundle/load size.
```js
button.addEventListener('click', async () => {
  const { showModal } = await import('./modal.js');
  showModal();
});
```

### Q119. What is "tree shaking," and why does it require ES Modules' static structure?
Tree shaking is a bundler optimization (used by tools like Webpack, Rollup, esbuild) that eliminates unused exports from the final bundled output, reducing bundle size. It relies on ES Modules' **static** import/export structure — since the dependency graph can be fully determined without executing any code, a bundler can confidently prove which exports are genuinely unused and safe to remove. CommonJS's dynamic, runtime-resolved `require()` calls make this kind of confident static analysis significantly harder, which is why tree shaking is closely associated with ES Modules specifically.

---

## 18. The DOM & Browser APIs

### Q120. What is the DOM, and how does it relate to JavaScript?
The Document Object Model (DOM) is a tree-structured, programmatic representation of an HTML/XML document, provided by the browser as a set of objects/APIs (not part of the JavaScript language itself, but a separate Web API standard) — JavaScript running in a browser can read and manipulate this tree (adding/removing/modifying elements, attributes, text) to dynamically update what's displayed on the page.

### Q121. What is the difference between `document.getElementById()`, `querySelector()`, and `querySelectorAll()`?
`getElementById(id)` selects a single element by its exact `id` attribute (fastest, most specific). `querySelector(cssSelector)` returns the **first** element matching any valid CSS selector (more flexible, slightly slower). `querySelectorAll(cssSelector)` returns a **static** `NodeList` of **all** matching elements (not automatically updated if the DOM later changes, unlike some older live collections like `getElementsByClassName`).

### Q122. What is event delegation, and why is it useful?
A pattern of attaching a **single** event listener to a common **parent** element rather than individual listeners on many child elements, relying on **event bubbling** (Q125) to detect which specific child triggered the event (via `event.target`). Useful for performance (fewer total listeners) and for automatically handling **dynamically added** child elements without needing to re-attach listeners to each new element individually.
```js
document.getElementById('list').addEventListener('click', (e) => {
  if (e.target.tagName === 'LI') console.log('Clicked:', e.target.textContent);
});
```

### Q123. What is the difference between `innerHTML`, `textContent`, and `innerText`?
`innerHTML` gets/sets an element's content as an **HTML string** (parsed and rendered as markup — a potential **XSS security risk** if set with untrusted user input). `textContent` gets/sets the element's content as **plain text** (no HTML parsing, and includes text from hidden elements), the safer and generally faster choice when HTML rendering isn't needed. `innerText` is similar to `textContent` but is **layout-aware** (respects CSS visibility/styling, e.g., excluding hidden text), and triggers a layout reflow to compute it, making it slower.

### Q124. What is the difference between `localStorage`, `sessionStorage`, and cookies?
| | `localStorage` | `sessionStorage` | Cookies |
|---|---|---|---|
| Persistence | Until explicitly cleared | Until the tab/window closes | Configurable expiration |
| Capacity | ~5–10MB | ~5–10MB | ~4KB |
| Sent to server | Never | Never | Sent with every matching HTTP request (unless flagged otherwise) |
| Access | JavaScript only (unless `httpOnly`, which doesn't apply here) | JavaScript only | JavaScript (unless `httpOnly`) and server |

Cookies are the only one of the three automatically transmitted to the server with requests, making them relevant to authentication/session mechanisms in ways `localStorage`/`sessionStorage` are not (those require the app to explicitly attach their contents to requests if needed).

### Q125. What is event bubbling and event capturing?
When an event fires on a DOM element, it propagates through the DOM tree in two phases: **capturing** (from the document root down to the target element) and **bubbling** (from the target element back up to the root) — by default, `addEventListener` listens during the bubbling phase, but passing `{ capture: true }` (or `true` as the third argument) listens during the capturing phase instead. This propagation is what makes event delegation (Q122) possible.

### Q126. What do `event.stopPropagation()` and `event.preventDefault()` do, and how do they differ?
`stopPropagation()` prevents the event from continuing to bubble/capture further up/down the DOM tree, stopping other listeners on ancestor (or descendant, during capture) elements from being triggered by this particular event. `preventDefault()` prevents the browser's **default action** associated with the event (e.g., a link's navigation, a form's submission, a checkbox's toggle) without affecting event propagation at all — the two are unrelated and independently controllable.

### Q127. What is `requestAnimationFrame()`, and why is it preferred over `setTimeout` for animations?
Schedules a callback to run **just before the browser's next repaint**, synchronized with the display's refresh rate (typically ~60fps), producing smoother animations than `setTimeout`-driven approaches (which aren't synchronized with the browser's actual rendering/paint cycle, and can cause visible stutter or wasted work if triggered at the wrong moment relative to a repaint).

---

## 19. Events

### Q128. What is the difference between `addEventListener()` and using an inline `onclick`-style HTML attribute or a `.onclick` property assignment?
`addEventListener()` allows attaching **multiple** independent listeners for the same event type to the same element (each called in registration order), and allows fine control (capturing phase, `once`, `passive` options, and clean removal via `removeEventListener`). Assigning directly to a `.onclick` property (or an inline HTML attribute) allows only a **single** handler per event type per element — assigning a new one silently **overwrites** any previous one — making `addEventListener()` the generally preferred, more flexible approach.

### Q129. What is a passive event listener, and why does it matter for scroll performance?
An event listener registered with `{ passive: true }`, signaling to the browser upfront that the listener will **never** call `preventDefault()` — allowing the browser to proceed with default behavior (like scrolling) **immediately**, without waiting for the listener to finish executing first, meaningfully improving scroll responsiveness for touch/wheel event listeners that don't need to block/cancel the default scroll behavior.

### Q130. What is a custom event, and how do you create/dispatch one?
The `CustomEvent` constructor allows creating application-defined events (beyond the browser's built-in event types), which can be dispatched on any DOM element and carry custom data via the `detail` property — useful for decoupled communication between different parts of an application via the DOM's existing event system.
```js
const event = new CustomEvent('itemAdded', { detail: { itemId: 42 } });
element.dispatchEvent(event);
element.addEventListener('itemAdded', (e) => console.log(e.detail.itemId));
```

### Q131. What is debouncing and throttling, and how do they differ?
Both limit how often a function (typically an event handler for a high-frequency event like `scroll`, `resize`, or keystroke input) actually executes. **Debouncing** delays execution until a specified period of **inactivity** has passed since the last invocation attempt — if the event keeps firing, execution keeps getting pushed further out (useful for a search-input autocomplete, waiting until the user pauses typing). **Throttling** ensures the function executes **at most once** per specified time interval, regardless of how many times the event fires within that window (useful for a scroll handler that should update at a steady, bounded rate rather than on every single scroll event).

---

## 20. Iterators, Generators & Symbols

### Q132. What is an iterable, and what is the iterator protocol?
An **iterable** is any object implementing the `Symbol.iterator` method, which returns an **iterator** — an object with a `.next()` method that returns `{ value, done }` on each call, advancing through a sequence of values until `done: true`. Built-in iterables include arrays, strings, `Map`, `Set`, and `NodeList` — this protocol is what powers `for...of` loops, spread syntax, and destructuring over these types.

### Q133. What is a generator function, and how does it relate to iterators?
A generator function (declared with `function*`) can **pause and resume** its execution using the `yield` keyword, automatically producing an object that conforms to the iterator protocol — each call to `.next()` resumes execution until the next `yield` (or the function ends), making generators a convenient, more readable way to hand-write custom iterators without manually managing `.next()`/`done` state yourself.
```js
function* idGenerator() {
  let id = 1;
  while (true) yield id++;
}
const gen = idGenerator();
gen.next().value; // 1
gen.next().value; // 2
```

### Q134. What are practical use cases for generators?
Implementing custom iteration logic for a data structure (making it usable with `for...of`), lazily generating potentially infinite or very large sequences without computing them all upfront, implementing simple coroutine-like control flow (this was actually how async/await was polyfilled/implemented conceptually before native support), and building custom, resumable data-processing pipelines.

### Q135. What is `Symbol.iterator`, and how do you make a custom object iterable with `for...of`?
Implementing a `[Symbol.iterator]` method on an object (returning an iterator, often conveniently via a generator function) makes that object usable directly with `for...of`, spread syntax, and destructuring — the standardized mechanism JavaScript uses to recognize an object as iterable.
```js
class Range {
  constructor(start, end) { this.start = start; this.end = end; }
  *[Symbol.iterator]() {
    for (let i = this.start; i <= this.end; i++) yield i;
  }
}
[...new Range(1, 3)]; // [1, 2, 3]
```

### Q136. What is the difference between `Map`/`Set` and plain objects/arrays?
`Map` is a key-value collection (like an object) but allows **any value type as a key** (not just strings/symbols, unlike plain objects), maintains **insertion order** reliably, and provides a direct `.size` property and built-in iteration methods. `Set` stores a collection of **unique** values (automatically de-duplicating), useful for membership checks and removing duplicates from an array (`[...new Set(arr)]`) — both generally offer better performance characteristics than plain objects/arrays for their specific use cases (frequent additions/removals, large collections, non-string keys).

---

## 21. Functional Programming Concepts

### Q137. What is immutability, and why is it emphasized in modern JavaScript patterns (e.g., React/Redux)?
Immutability means not modifying data in place, but instead creating new copies with the desired changes (see Q57, Q87). It's emphasized because it makes state changes easier to track/detect (a simple reference comparison can reveal whether something changed, rather than deep-comparing contents), avoids subtle bugs from unexpected shared-reference mutations, and enables features like time-travel debugging and predictable re-render optimization (e.g., `React.memo`'s shallow comparison relies on this).

### Q138. What is memoization, and how would you implement a simple memoization function?
Caching the result of an expensive function call, keyed by its input arguments, so repeated calls with the same arguments return the cached result instead of recomputing.
```js
function memoize(fn) {
  const cache = new Map();
  return (...args) => {
    const key = JSON.stringify(args);
    if (cache.has(key)) return cache.get(key);
    const result = fn(...args);
    cache.set(key, result);
    return result;
  };
}
```

### Q139. What is the difference between declarative and imperative programming, with a JavaScript example?
**Imperative** code explicitly describes the step-by-step "how" (a manual loop with an index variable, mutating an accumulator). **Declarative** code describes the desired "what," letting the underlying implementation handle the how (e.g., using `.map()`/`.filter()`/`.reduce()` instead of manual `for` loops with mutation) — generally considered more readable and less error-prone, and a core stylistic hallmark of functional-leaning JavaScript code.

### Q140. What is a side effect, in the functional programming sense, and why do frameworks like React care about controlling them?
Any observable interaction with state/systems outside a function's own local scope — mutating an external variable, making a network request, writing to the DOM/console, or modifying an argument passed by reference. Frameworks like React care because predictable rendering depends on component render functions being (mostly) pure — side effects during rendering (rather than being properly scheduled via `useEffect`) can cause inconsistent behavior, especially under React's concurrent rendering, which may render a component multiple times or out of the expected order.

---

## 22. Memory Management & Performance

### Q141. How does JavaScript's garbage collection work, at a high level?
JavaScript engines automatically manage memory via **garbage collection** — periodically identifying and reclaiming memory occupied by objects that are no longer **reachable** from any root reference (global variables, currently executing function scopes/closures, etc.). The dominant algorithm used by modern engines (like V8) is **mark-and-sweep**: starting from root references, the collector marks all reachable objects, then sweeps away (frees) everything left unmarked.

### Q142. What is a memory leak in JavaScript, and what are common causes?
Memory that's no longer actually needed by the application but remains **reachable** (and thus never garbage collected), causing memory usage to grow unboundedly over time. Common causes: forgotten timers/intervals (`setInterval` that's never cleared) that keep their closure's referenced variables alive, detached DOM nodes still referenced by JavaScript variables after being removed from the document, unremoved event listeners, and accidentally-created global variables (see Q13) that persist for the application's entire lifetime.

### Q143. What is the difference between the stack and the heap, in JavaScript's memory model?
The **stack** stores primitive values and references (pointers) for the currently executing function calls, with fast, automatic allocation/deallocation as functions are called/return (following a strict last-in-first-out order). The **heap** is a larger, less structured memory region where objects (and functions, arrays) are actually allocated, managed by the garbage collector rather than automatically freed when a function returns — since objects can outlive the function scope that created them (e.g., via closures or being returned/stored elsewhere).

### Q144. What are `WeakMap` and `WeakSet`, and how do they help with memory management?
Similar to `Map`/`Set`, but they hold their keys (`WeakMap`) or values (`WeakSet`) with **weak references** — meaning the garbage collector is free to reclaim an object even while it's still a key/value in a `WeakMap`/`WeakSet`, as long as there are no other strong references to it elsewhere. This makes them suitable for associating metadata with an object (e.g., caching computed data per DOM element) **without** that association itself preventing the object from being garbage collected once it's no longer used elsewhere — avoiding a category of memory leaks that a regular `Map` could otherwise cause. A trade-off: `WeakMap`/`WeakSet` are not iterable and have no `.size`, since their contents can change unpredictably via garbage collection.

### Q145. What are common performance optimization techniques in JavaScript?
- **Debouncing/throttling** high-frequency event handlers (Q131).
- **Memoization** of expensive, repeatable computations.
- Avoiding unnecessary object/array creation inside hot loops or frequently-called functions.
- Using efficient data structures (`Map`/`Set` for frequent lookups instead of array `.includes()`/`.find()`, which are O(n)).
- Minimizing DOM manipulation/reflows (batching reads and writes, avoiding forced synchronous layout thrashing).
- Lazy-loading/code-splitting to reduce initial parse/execution cost.
- Profiling with browser DevTools (Performance tab, Memory tab) to identify actual bottlenecks before optimizing, rather than guessing.

---

## 23. Design Patterns

### Q146. What is the Module pattern, and how do closures enable it?
A pattern (predating native ES Modules) for creating encapsulated, private state and a controlled public API using an IIFE and closures — variables/functions declared inside the IIFE are private (inaccessible from outside), while an explicitly returned object exposes only the intended public interface.
```js
const Counter = (function () {
  let count = 0; // private
  return {
    increment: () => ++count,
    getCount: () => count,
  };
})();
```

### Q147. What is the Singleton pattern, and how would you implement it in JavaScript?
Ensures a class/object has only **one** instance, providing a single global point of access to it — commonly implemented via a module (since ES Modules are already singletons by nature — imported once, cached, and shared across all importers) or a class with a static instance-check in its constructor/factory method.

### Q148. What is the Observer pattern, and where does it show up in built-in JavaScript/browser APIs?
A pattern where an object (the "subject") maintains a list of dependents ("observers") and notifies them automatically of state changes, without the subject needing direct knowledge of what the observers do with that notification. It shows up throughout the platform: the DOM's event listener system itself (`addEventListener`), Node.js's `EventEmitter`, and reactive state libraries (RxJS Observables) all implement variations of this pattern.

### Q149. What is the Factory pattern, in a JavaScript context?
A function (or method) responsible for creating and returning objects, encapsulating the object-creation logic (potentially varying the returned object's shape/type based on input) rather than requiring the caller to use `new SomeClass()` directly — useful for centralizing creation logic, hiding implementation details, or returning different concrete types behind a common interface.

### Q150. What is the difference between the Prototype pattern and using ES6 classes, and how does JavaScript's native prototypal inheritance relate to the classic "Prototype design pattern"?
The classic Prototype design pattern (creating new objects by cloning an existing "prototype" object rather than instantiating from a class blueprint) maps very naturally onto JavaScript's own **native** object model, since JavaScript's prototypal inheritance is fundamentally about objects delegating to other objects (Section 8) — unlike class-based languages, where this pattern is a deliberate alternative to the language's default instantiation model, in JavaScript it's arguably closer to the language's default underlying behavior, with `class` syntax being the layer added on top.

---

## 24. Modern ES Features (ES2020+)

### Q151. What is optional chaining and nullish coalescing (recap in the context of "modern" features)?
Both introduced in ES2020 — see Q26/Q27 for detailed explanations. Together, they significantly reduce boilerplate for safely handling potentially missing/nullish nested data and default values.

### Q152. What is `Promise.allSettled()`, and when was it introduced?
Introduced in ES2020 — see Q94 for details. Solves the specific gap left by `Promise.all()`'s all-or-nothing rejection behavior, when partial results/failures need to be inspected individually.

### Q153. What are logical assignment operators (`&&=`, `||=`, `??=`), introduced in ES2021?
Shorthand combinations of a logical operator and assignment, conditionally assigning a new value based on the current value's truthiness/nullishness:
```js
user.name ??= 'Guest'; // assign only if user.name is currently null/undefined
config.enabled &&= isFeatureAllowed(); // assign only if config.enabled is currently truthy
```

### Q154. What is `Array.prototype.at()`, and what problem does it solve?
Introduced in ES2022, `.at(index)` allows accessing array (and string) elements using **negative indices** to count from the end, something bracket notation doesn't natively support (`arr[-1]` simply returns `undefined`, unlike Python's negative indexing).
```js
[1, 2, 3].at(-1); // 3, equivalent to arr[arr.length - 1]
```

### Q155. What are `Object.hasOwn()`, `structuredClone()`, and array "change by copy" methods (`toSorted`, `toReversed`, `toSpliced`, `with`), introduced in ES2022/ES2023?
- **`Object.hasOwn(obj, prop)`** (ES2022) — a safer, more concise alternative to `obj.hasOwnProperty(prop)`, working correctly even for objects with `null` prototypes (which lack `.hasOwnProperty` entirely).
- **`structuredClone(value)`** (widely available ~2022) — a built-in, robust deep-cloning function supporting many complex types (Dates, Maps, Sets, circular references) that `JSON.parse(JSON.stringify())` cannot handle correctly.
- **`toSorted()`/`toReversed()`/`toSpliced()`/`with()`** (ES2023) — non-mutating counterparts to `sort()`/`reverse()`/`splice()`, and a way to replace a single index (`with()`) — all returning a **new** array rather than mutating the original, aligning with immutability-favoring patterns without needing manual spread-copy workarounds.

### Q156. What is `Array.prototype.group()`/`Object.groupBy()`, and what does it simplify?
`Object.groupBy(items, keyFn)` (a newer addition) groups an iterable's items into a plain object keyed by the result of a callback function applied to each item — directly replacing the common manual `reduce()`-based grouping pattern (Q69) with a purpose-built, more readable method.

---

## 25. Miscellaneous & Best Practices

### Q157. What is the difference between "pass by value" and "pass by reference," and how does JavaScript actually handle function arguments?
JavaScript is technically **always pass-by-value** — but for objects, the "value" being passed is the **reference** (memory address) to the object, not a full copy of the object itself. This means: reassigning a parameter inside a function never affects the caller's original variable (true for both primitives and objects), but **mutating** an object's properties through a parameter **does** affect the original object, since both the parameter and the original variable point to the same underlying object in memory.

### Q158. What is the difference between `Function.prototype.call`-style explicit invocation and simply calling a method, in terms of understanding "everything is an object"?
Functions in JavaScript are themselves objects (first-class citizens) — they can have their own properties, be passed around as values, and are instances of `Function`, which itself has methods like `call`/`apply`/`bind` available on its prototype, illustrating that JavaScript's object/prototype system applies uniformly, even to functions themselves.

### Q159. What is `use strict`, and are there any behaviors it doesn't change that are commonly assumed?
Covered in Q14 — strict mode changes several error-handling and scoping behaviors, but does **not**, for example, change type coercion rules for `==`, nor does it make `this` behave identically to normal-mode object method calls (a method called as `obj.method()` still gets `this === obj` in strict mode; it's specifically plain, unqualified function calls where the `this` behavior differs between modes).

### Q160. What is the difference between a polyfill and a transpiler (e.g., Babel)?
A **polyfill** provides a runtime implementation of a missing API/method for environments that don't natively support it (e.g., a JS implementation of `Array.prototype.flat` for older browsers that lack it natively) — it adds missing **functionality**. A **transpiler** (like Babel) converts modern JavaScript **syntax** into an older, more widely-supported syntax (e.g., converting arrow functions or optional chaining into equivalent ES5-compatible code) — it doesn't add missing runtime APIs, only translates syntax; the two are often used together to support older environments comprehensively.

### Q161. What are common JavaScript anti-patterns/pitfalls to avoid?
- Relying on `==` instead of `===`, inviting unexpected coercion bugs.
- Using `var` in modern code instead of `let`/`const`, reintroducing function-scoping and hoisting pitfalls.
- Mutating function arguments or shared objects unexpectedly, causing action-at-a-distance bugs.
- Forgetting `await` (or a `.catch()`) on a Promise, silently swallowing errors or causing out-of-order execution.
- Using `forEach()` with an `async` callback expecting it to wait (Q101).
- Comparing objects/arrays with `===` and expecting structural (deep) equality — it always checks reference identity.
- Deeply nested callbacks instead of Promises/async-await.
- Overusing global variables/state instead of proper scoping/module boundaries.
- Not handling the `NaN !== NaN` and floating-point precision quirks when doing numeric comparisons.

### Q162. What are general JavaScript best practices?
- Prefer `const` by default, `let` when reassignment is genuinely needed, and avoid `var` in new code.
- Use strict equality (`===`) unless loose equality's coercion is specifically and deliberately intended.
- Favor immutable data patterns (spread/`structuredClone`, non-mutating array methods) where reasonable.
- Handle errors explicitly and consistently — always attach `.catch()`/`try-catch` to async operations.
- Keep functions small, pure, and single-purpose where practical, favoring composition.
- Use `async`/`await` over raw `.then()` chains for readability, but understand the underlying Promise mechanics.
- Avoid unnecessary global state; scope variables as tightly as possible (module scope, function scope, block scope).
- Write code that clearly communicates intent (descriptive naming, avoiding overly clever one-liners at the expense of readability).
- Use a linter (ESLint) and formatter (Prettier) to catch common bugs and maintain consistency automatically.

---

*This guide covers the core JavaScript interview surface area. For role-specific deep dives (e.g., V8 engine internals, advanced generator/async-iterator patterns, or specific browser rendering-pipeline mechanics), further topic-specific preparation is recommended.*
