<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# 📋 50 Latest JavaScript Interview Questions \& Answers

## 📖 Table of Contents

1. [Question List](#question-list)
2. [Detailed Questions with Answers \& Examples](#detailed-questions-with-answers--examples)

## 🔍 Question List

Here are the 50 latest JavaScript interview questions:

1. **What is debouncing in JavaScript and how do you implement it?**
2. **Explain `Promise.all()` and its use cases**
3. **How do you implement deep equality checking in JavaScript?**
4. **What is an EventEmitter and how do you implement it?**
5. **Explain `Array.prototype.reduce()` and its applications**
6. **How do you flatten a nested array in JavaScript?**
7. **What are different ways to merge objects and arrays?**
8. **How does `getElementsByClassName` work?**
9. **What is memoization and how do you implement it?**
10. **How do you safely access nested object properties?**
11. **Explain hoisting in JavaScript with examples**
12. **What are the differences between `let`, `var`, and `const`?**
13. **Difference between `==` and `===` operators**
14. **How does the event loop work in JavaScript?**
15. **What is event delegation and how do you implement it?**
16. **How does `this` work in different contexts?**
17. **Differences between cookies, `sessionStorage`, and `localStorage`**
18. **Explain `<script>`, `<script async>`, and `<script defer>`**
19. **What are `null`, `undefined`, and undeclared variables?**
20. **Difference between `.call()` and `.apply()`**
21. **How does `Function.prototype.bind` work?**
22. **What's the advantage of arrow functions in constructors?**
23. **How does prototypal inheritance work?**
24. **Difference between function declaration and expression**
25. **What are different ways to create objects in JavaScript?**
26. **What is a higher-order function?**
27. **Differences between ES5 constructor functions and ES2015 classes**
28. **What is event bubbling?**
29. **What is event capturing?**
30. **Difference between `mouseenter` and `mouseover`**
31. **Synchronous vs asynchronous functions**
32. **What is AJAX and how do you use it?**
33. **Differences between `XMLHttpRequest` and `fetch()`**
34. **What are JavaScript data types?**
35. **Different ways to iterate over objects and arrays**
36. **Explain spread and rest operators**
37. **Difference between Map and plain objects**
38. **Differences between `Map`/`Set` and `WeakMap`/`WeakSet`**
39. **Use cases for arrow function syntax**
40. **What is a callback function?**
41. **Explain debouncing and throttling concepts**
42. **What is destructuring assignment?**
43. **How does hoisting work with function declarations?**
44. **What is inheritance in ES2015 classes?**
45. **What is lexical scoping?**
46. **Explain different types of scope in JavaScript**
47. **How does the spread operator work?**
48. **How does `this` work in event handlers?**
49. **What are template literals and their benefits?**
50. **How do you handle errors in JavaScript?**

## ❓ Detailed Questions with Answers \& Examples

### 1. What is debouncing in JavaScript and how do you implement it?

**Answer:**
Debouncing is a technique that delays function execution until after a specified time has passed since the last event trigger. It prevents performance bottlenecks by reducing unnecessary function calls.[^1]

**Example:**

```javascript
function debounce(func, delay) {
    let timeoutId;
    return function(...args) {
        clearTimeout(timeoutId);
        timeoutId = setTimeout(() => func.apply(this, args), delay);
    };
}

// Usage example
const searchInput = document.getElementById('search-input');
const debouncedSearch = debounce(() => {
    console.log('Searching for:', searchInput.value);
}, 300);

searchInput.addEventListener('input', debouncedSearch);
```


### 2. Explain `Promise.all()` and its use cases

**Answer:**
`Promise.all()` is a method that takes an array of promises and returns a single promise that resolves when all promises resolve, or rejects if any one fails.[^1]

**Example:**

```javascript
const promise1 = fetch('https://api.example.com/data/1');
const promise2 = fetch('https://api.example.com/data/2');
const promise3 = fetch('https://api.example.com/data/3');

Promise.all([promise1, promise2, promise3])
    .then((responses) => {
        console.log('All responses:', responses);
    })
    .catch((error) => {
        console.error('Error:', error);
    });
```


### 3. How do you implement deep equality checking in JavaScript?

**Answer:**
Deep equality compares two objects or arrays to determine if they are structurally identical, examining all nested values.[^1]

**Example:**

```javascript
function deepEqual(obj1, obj2) {
    if (obj1 === obj2) return true;
    
    if (obj1 == null || typeof obj1 !== 'object' || 
        obj2 == null || typeof obj2 !== 'object') 
        return false;
    
    let keys1 = Object.keys(obj1);
    let keys2 = Object.keys(obj2);
    
    if (keys1.length !== keys2.length) return false;
    
    for (let key of keys1) {
        if (!keys2.includes(key) || !deepEqual(obj1[key], obj2[key])) 
            return false;
    }
    
    return true;
}

// Example usage
const object1 = {
    name: 'John',
    address: { city: 'New York', zip: '10001' }
};

const object2 = {
    name: 'John',
    address: { city: 'New York', zip: '10001' }
};

console.log(deepEqual(object1, object2)); // true
```


### 4. What is an EventEmitter and how do you implement it?

**Answer:**
An EventEmitter is a utility that enables objects to listen for and emit events, implementing the observer pattern.[^1]

**Example:**

```javascript
class EventEmitter {
    constructor() {
        this.events = {};
    }
    
    on(event, callback) {
        if (!this.events[event]) {
            this.events[event] = [];
        }
        this.events[event].push(callback);
    }
    
    emit(event, data) {
        if (this.events[event]) {
            this.events[event].forEach(callback => callback(data));
        }
    }
    
    off(event, callback) {
        if (this.events[event]) {
            this.events[event] = this.events[event].filter(cb => cb !== callback);
        }
    }
}

// Usage
const eventEmitter = new EventEmitter();

eventEmitter.on('customEvent', (data) => {
    console.log('Event emitted with data:', data);
});

eventEmitter.emit('customEvent', { message: 'Hello, world!' });
```


### 5. Explain `Array.prototype.reduce()` and its applications

**Answer:**
`Array.prototype.reduce()` is a versatile method for iterating through an array and reducing it to a single value using a callback function.[^1]

**Example:**

```javascript
// Sum of numbers
const numbers = [1, 2, 3, 4, 5];
const sum = numbers.reduce((accumulator, currentValue) => {
    return accumulator + currentValue;
}, 0);
console.log(sum); // 15

// Flattening arrays
const nestedArray = [[1, 2], [3, 4], [5, 6]];
const flattened = nestedArray.reduce((acc, val) => acc.concat(val), []);
console.log(flattened); // [1, 2, 3, 4, 5, 6]

// Counting occurrences
const fruits = ['apple', 'banana', 'apple', 'orange', 'banana', 'apple'];
const count = fruits.reduce((acc, fruit) => {
    acc[fruit] = (acc[fruit] || 0) + 1;
    return acc;
}, {});
console.log(count); // { apple: 3, banana: 2, orange: 1 }
```


### 6. How do you flatten a nested array in JavaScript?

**Answer:**
Flattening transforms a nested array into a single-level array. JavaScript provides `Array.prototype.flat()` method since ES2019.[^1]

**Example:**

```javascript
// Using built-in flat() method
const nestedArray = [1, [2, [3, [4, [^5]]]]];
const flatArray = nestedArray.flat(Infinity);
console.log(flatArray); // [1, 2, 3, 4, 5]

// Custom recursive flatten function
function flattenArray(arr) {
    return arr.reduce((acc, val) =>
        Array.isArray(val) ? acc.concat(flattenArray(val)) : acc.concat(val),
        []
    );
}

const nested = [1, [2, [3, [4, [^5]]]]];
const flattened = flattenArray(nested);
console.log(flattened); // [1, 2, 3, 4, 5]
```


### 7. What are different ways to merge objects and arrays?

**Answer:**
JavaScript provides multiple ways to merge objects and arrays using spread operator, `Object.assign()`, and `Array.concat()`.[^1]

**Example:**

```javascript
// Merging objects with spread operator
const obj1 = { a: 1, b: 2 };
const obj2 = { b: 3, c: 4 };
const mergedObj = { ...obj1, ...obj2 };
console.log(mergedObj); // { a: 1, b: 3, c: 4 }

// Merging objects with Object.assign()
const merged = Object.assign({}, obj1, obj2);
console.log(merged); // { a: 1, b: 3, c: 4 }

// Merging arrays with spread operator
const array1 = [1, 2, 3];
const array2 = [4, 5, 6];
const mergedArray = [...array1, ...array2];
console.log(mergedArray); // [1, 2, 3, 4, 5, 6]

// Merging arrays with concat()
const concatenated = array1.concat(array2);
console.log(concatenated); // [1, 2, 3, 4, 5, 6]
```


### 8. How does `getElementsByClassName` work?

**Answer:**
`getElementsByClassName` fetches elements matching a specific class and returns them as a live `HTMLCollection`.[^1]

**Example:**

```javascript
// HTML: <div class="example">Content 1</div>
//       <div class="example">Content 2</div>

// Fetch and loop through elements
const elements = document.getElementsByClassName('example');
for (let i = 0; i < elements.length; i++) {
    console.log(elements[i].textContent);
}

// Multiple class names
const multipleClasses = document.getElementsByClassName('class1 class2');

// Converting to array for modern methods
const elementsArray = Array.from(elements);
elementsArray.forEach(element => {
    console.log(element.textContent);
});
```


### 9. What is memoization and how do you implement it?

**Answer:**
Memoization saves computed results to avoid redundant calculations, improving performance for expensive operations.[^1]

**Example:**

```javascript
function memoize(func) {
    const cache = {};
    return function(n) {
        if (cache[n] !== undefined) {
            console.log('From cache for', n);
            return cache[n];
        }
        const result = func(n);
        cache[n] = result;
        return result;
    };
}

function expensiveOperation(n) {
    console.log('Calculating for', n);
    return n * 2;
}

const memoizedOperation = memoize(expensiveOperation);
console.log(memoizedOperation(5)); // Calculating for 5, 10
console.log(memoizedOperation(5)); // From cache for 5, 10

// Fibonacci with memoization
const fibonacciMemo = memoize((n) => {
    if (n <= 1) return n;
    return fibonacciMemo(n - 1) + fibonacciMemo(n - 2);
});
```


### 10. How do you safely access nested object properties?

**Answer:**
JavaScript provides optional chaining (`?.`) and you can implement custom `get` functions to safely access nested properties.[^1]

**Example:**

```javascript
const user = { 
    address: { 
        city: 'New York',
        coordinates: { lat: 40.7128, lng: -74.0060 }
    } 
};

// Using optional chaining (ES2020)
console.log(user.address?.city); // 'New York'
console.log(user.address?.country?.name); // undefined (no error)

// Custom get function
function get(obj, path, defaultValue) {
    const keys = path.split('.');
    let result = obj;
    
    for (const key of keys) {
        if (result == null || typeof result !== 'object') {
            return defaultValue;
        }
        result = result[key];
    }
    
    return result !== undefined ? result : defaultValue;
}

console.log(get(user, 'address.city')); // 'New York'
console.log(get(user, 'address.country.name', 'Unknown')); // 'Unknown'
```


### 11. Explain hoisting in JavaScript with examples

**Answer:**
Hoisting refers to how JavaScript moves variable and function declarations to the top of their scope during compilation.[^1]

**Example:**

```javascript
// Variable hoisting with var
console.log(foo); // undefined (not an error)
var foo = 1;
console.log(foo); // 1

// let and const - temporal dead zone
console.log(bar); // ReferenceError
let bar = 'local';

// Function declaration hoisting
sayHello(); // 'Hello!' (works fine)
function sayHello() {
    console.log('Hello!');
}

// Function expression - not hoisted
console.log(sayGoodbye); // undefined
sayGoodbye(); // TypeError: sayGoodbye is not a function
var sayGoodbye = function() {
    console.log('Goodbye!');
};
```


### 12. What are the differences between `let`, `var`, and `const`?

**Answer:**
`let`, `var`, and `const` differ in scope, initialization, redeclaration, and reassignment behavior.[^1]

**Example:**

```javascript
// Scope differences
if (true) {
    var varVariable = 1;    // Function-scoped
    let letVariable = 2;    // Block-scoped
    const constVariable = 3; // Block-scoped
}
console.log(varVariable); // 1
// console.log(letVariable); // ReferenceError
// console.log(constVariable); // ReferenceError

// Redeclaration
var x = 10;
var x = 20; // Allowed

let y = 10;
// let y = 20; // SyntaxError

// Reassignment
let a = 1;
a = 2; // Allowed

const b = 1;
// b = 2; // TypeError

// const with objects/arrays
const obj = { name: 'John' };
obj.name = 'Jane'; // Allowed (mutating content)
obj.age = 30; // Allowed
// obj = {}; // TypeError (reassigning reference)
```


### 13. Difference between `==` and `===` operators

**Answer:**
`==` performs type coercion before comparison, while `===` checks both value and type without conversion.[^1]

**Example:**

```javascript
// Loose equality (==) with type coercion
console.log(42 == '42');        // true
console.log(0 == false);        // true
console.log(null == undefined); // true
console.log('' == 0);           // true

// Strict equality (===) without type coercion
console.log(42 === '42');        // false
console.log(0 === false);        // false
console.log(null === undefined); // false
console.log('' === 0);           // false

// Object.is() for special cases
console.log(Object.is(-0, +0));     // false
console.log(Object.is(NaN, NaN));   // true
console.log(-0 === +0);             // true
console.log(NaN === NaN);           // false
```


### 14. How does the event loop work in JavaScript?

**Answer:**
The event loop manages JavaScript's asynchronous execution, handling the call stack, callback queue, and microtask queue.[^1]

**Example:**

```javascript
console.log('Start');

setTimeout(() => console.log('Timeout 1'), 0);
Promise.resolve().then(() => console.log('Promise 1'));
setTimeout(() => console.log('Timeout 2'), 0);

console.log('End');

// Output order:
// Start
// End
// Promise 1
// Timeout 1
// Timeout 2

// Explanation:
// 1. Synchronous code runs first
// 2. Microtasks (Promise callbacks) run before macrotasks
// 3. Macrotasks (setTimeout) run last
```


### 15. What is event delegation and how do you implement it?

**Answer:**
Event delegation attaches a single event listener to a parent element to handle events for multiple child elements.[^1]

**Example:**

```javascript
// HTML:
// <ul id="item-list">
//   <li>Item 1</li>
//   <li>Item 2</li>
//   <li>Item 3</li>
// </ul>

const itemList = document.getElementById('item-list');

// Instead of adding listeners to each <li>
itemList.addEventListener('click', (event) => {
    if (event.target.tagName === 'LI') {
        console.log(`Clicked on ${event.target.textContent}`);
    }
});

// Benefits:
// 1. Better performance with many elements
// 2. Automatically handles dynamically added elements
// 3. Less memory usage

// Adding new items dynamically
const newItem = document.createElement('li');
newItem.textContent = 'Item 4';
itemList.appendChild(newItem); // Event delegation still works!
```


### 16. How does `this` work in different contexts?

**Answer:**
The value of `this` depends on how a function is called - constructor, method call, explicit binding, or regular function call.[^1]

**Example:**

```javascript
// Constructor function
function Person(name) {
    this.name = name;
}
const person = new Person('Alice');
console.log(person.name); // 'Alice'

// Method call
const obj = {
    name: 'Bob',
    greet() {
        console.log(this.name);
    }
};
obj.greet(); // 'Bob'

// Explicit binding with call/apply/bind
function sayHello() {
    console.log(`Hello, ${this.name}`);
}
const user = { name: 'Charlie' };
sayHello.call(user); // 'Hello, Charlie'

// Arrow functions - lexical this
const arrowObj = {
    name: 'Dave',
    greet: () => {
        console.log(this.name); // undefined (global this)
    },
    normalGreet() {
        const arrow = () => console.log(this.name);
        arrow(); // 'Dave' (inherits from normalGreet)
    }
};
```


### 17. Differences between cookies, `sessionStorage`, and `localStorage`

**Answer:**
These are different client-side storage mechanisms with varying persistence, capacity, and server interaction.[^1]

**Example:**

```javascript
// Cookies - sent with HTTP requests, limited size (4KB)
document.cookie = 'userId=12345; expires=Fri, 31 Dec 2025 23:59:59 GMT; path=/';
console.log(document.cookie);

// localStorage - persistent across sessions, ~5MB
localStorage.setItem('username', 'john_doe');
localStorage.setItem('preferences', JSON.stringify({theme: 'dark'}));
console.log(localStorage.getItem('username'));
localStorage.removeItem('username');
localStorage.clear();

// sessionStorage - cleared when tab closes, ~5MB
sessionStorage.setItem('sessionId', 'abcdef');
console.log(sessionStorage.getItem('sessionId'));
sessionStorage.removeItem('sessionId');
sessionStorage.clear();

// Comparison:
// | Feature      | Cookies | localStorage | sessionStorage |
// |--------------|---------|--------------|----------------|
// | Persistence  | Expiry  | Permanent    | Session        |
// | Size         | 4KB     | ~5MB         | ~5MB           |
// | Server sent  | Yes     | No           | No             |
// | API          | Manual  | setItem/get  | setItem/get    |
```


### 18. Explain `<script>`, `<script async>`, and `<script defer>`

**Answer:**
These script loading strategies control when JavaScript executes relative to HTML parsing.[^1]

**Example:**

```html
<!-- Regular script - blocks HTML parsing -->
<script src="main.js"></script>

<!-- Async - loads in parallel, executes immediately when ready -->
<script async src="analytics.js"></script>

<!-- Defer - loads in parallel, executes after HTML parsing -->
<script defer src="dom-dependent.js"></script>
```

```javascript
// Timeline visualization:
// HTML parsing: ----[blocked]----[blocked]----[complete]
// Regular:           [load+exec]  [load+exec]
// Async:        [load][exec]      [load]  [exec]
// Defer:        [load]            [load]        [exec][exec]

// Use cases:
// Regular: Critical scripts needed immediately
// Async: Independent scripts (analytics, ads)
// Defer: Scripts that need complete DOM (jQuery, DOM manipulation)
```


### 19. What are `null`, `undefined`, and undeclared variables?

**Answer:**
These represent different states of variables in JavaScript - intentional absence, uninitialized, or not declared.[^1]

**Example:**

```javascript
// Undefined - declared but not assigned
let a;
console.log(a); // undefined
console.log(typeof a); // 'undefined'

// Null - intentional absence of value
let b = null;
console.log(b); // null
console.log(typeof b); // 'object' (known quirk)

// Undeclared - variable not declared at all
try {
    console.log(c); // ReferenceError: c is not defined
} catch (e) {
    console.log('c is undeclared');
}

// Checking for each type
function checkVariable(variable) {
    try {
        if (variable === null) return 'null';
        if (variable === undefined) return 'undefined';
        return 'defined';
    } catch (e) {
        return 'undeclared';
    }
}

// Function parameter example
function greet(name) {
    if (name === undefined) {
        name = 'Guest'; // Default value
    }
    console.log(`Hello, ${name}!`);
}
```


### 20. Difference between `.call()` and `.apply()`

**Answer:**
Both methods invoke functions with a specified `this` value, but differ in how arguments are passed.[^1]

**Example:**

```javascript
function introduce(greeting, punctuation) {
    return `${greeting}, I'm ${this.name}${punctuation}`;
}

const person = { name: 'Alice' };

// call() - arguments as comma-separated list
const result1 = introduce.call(person, 'Hello', '!');
console.log(result1); // "Hello, I'm Alice!"

// apply() - arguments as an array
const result2 = introduce.apply(person, ['Hi', '.']);
console.log(result2); // "Hi, I'm Alice."

// Practical example: finding max in array
const numbers = [1, 5, 3, 9, 2];

// Using apply to pass array elements as arguments
const max = Math.max.apply(null, numbers);
console.log(max); // 9

// Modern alternative with spread operator
const maxModern = Math.max(...numbers);
console.log(maxModern); // 9

// Memory aid: "A for Array, C for Comma-separated"
```


### 21. How does `Function.prototype.bind` work?

**Answer:**
`bind` creates a new function with a specific `this` value and optionally preset arguments.[^1]

**Example:**

```javascript
const person = {
    name: 'John',
    age: 42,
    getAge: function() {
        return this.age;
    }
};

// Direct method call
console.log(person.getAge()); // 42

// Lost context when assigned
const unboundGetAge = person.getAge;
console.log(unboundGetAge()); // undefined

// Bind to preserve context
const boundGetAge = person.getAge.bind(person);
console.log(boundGetAge()); // 42

// Bind to different object
const mary = { age: 21 };
const boundGetAgeMary = person.getAge.bind(mary);
console.log(boundGetAgeMary()); // 21

// Partial application with bind
function multiply(a, b) {
    return a * b;
}

const double = multiply.bind(null, 2);
console.log(double(5)); // 10

// Event handler example
class Button {
    constructor(element) {
        this.element = element;
        this.clickCount = 0;
        
        // Bind to preserve context
        this.element.addEventListener('click', this.handleClick.bind(this));
    }
    
    handleClick() {
        this.clickCount++;
        console.log(`Clicked ${this.clickCount} times`);
    }
}
```


### 22. What's the advantage of arrow functions in constructors?

**Answer:**
Arrow functions in constructors automatically bind the `this` context, eliminating the need for manual binding.[^1]

**Example:**

```javascript
// Traditional constructor with binding issues
const Person = function(name) {
    this.name = name;
    
    // Regular function - loses context when passed around
    this.sayName1 = function() {
        console.log(this.name);
    };
    
    // Arrow function - preserves context
    this.sayName2 = () => {
        console.log(this.name);
    };
};

const john = new Person('John');
const dave = new Person('Dave');

// Direct calls
john.sayName1(); // John
john.sayName2(); // John

// When context is changed with call()
john.sayName1.call(dave); // Dave (context changed)
john.sayName2.call(dave); // John (context preserved)

// React class component example
class Component {
    constructor() {
        this.state = { count: 0 };
        
        // Arrow function preserves 'this'
        this.handleClick = () => {
            this.setState({ count: this.state.count + 1 });
        };
    }
    
    render() {
        // No need to bind in JSX
        return <button onClick={this.handleClick}>Click me</button>;
    }
}
```


### 23. How does prototypal inheritance work?

**Answer:**
Prototypal inheritance allows objects to share properties and methods through their prototype chain.[^1]

**Example:**

```javascript
// Base constructor
function Animal(name) {
    this.name = name;
}

// Add method to prototype
Animal.prototype.sayName = function() {
    console.log(`My name is ${this.name}`);
};

Animal.prototype.speak = function() {
    console.log(`${this.name} makes a sound`);
};

// Derived constructor
function Dog(name, breed) {
    Animal.call(this, name); // Call parent constructor
    this.breed = breed;
}

// Set up inheritance
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;

// Add dog-specific method
Dog.prototype.bark = function() {
    console.log(`${this.name} barks!`);
};

// Override parent method
Dog.prototype.speak = function() {
    console.log(`${this.name} barks loudly!`);
};

// Usage
const fido = new Dog('Fido', 'Labrador');
fido.sayName(); // "My name is Fido"
fido.bark();    // "Fido barks!"
fido.speak();   // "Fido barks loudly!"

// Prototype chain verification
console.log(fido instanceof Dog);    // true
console.log(fido instanceof Animal); // true
console.log(Object.getPrototypeOf(fido) === Dog.prototype); // true
```


### 24. Difference between function declaration and expression

**Answer:**
Function declarations are hoisted completely, while function expressions are only variable-hoisted.[^1]

**Example:**

```javascript
// Function declaration - fully hoisted
console.log(declared()); // "I'm declared!" (works)

function declared() {
    return "I'm declared!";
}

// Function expression - variable hoisted, function not
console.log(typeof expressed); // "undefined"
// console.log(expressed()); // TypeError: expressed is not a function

var expressed = function() {
    return "I'm expressed!";
};

console.log(expressed()); // "I'm expressed!" (works now)

// Named function expression
const namedExpression = function namedFunc() {
    return "I'm a named expression!";
};

// Arrow function expression
const arrowExpression = () => "I'm an arrow function!";

// IIFE (Immediately Invoked Function Expression)
(function() {
    console.log("I run immediately!");
})();

// Conditional function creation
let condition = true;

if (condition) {
    // Function declaration in block (avoided in strict mode)
    function conditionalFunc() {
        return "Conditional";
    }
    
    // Better: function expression
    var betterConditional = function() {
        return "Better conditional";
    };
}
```


### 25. What are different ways to create objects in JavaScript?

**Answer:**
JavaScript offers multiple object creation patterns, each with specific use cases.[^1]

**Example:**

```javascript
// 1. Object literal
const person1 = {
    firstName: 'John',
    lastName: 'Doe',
    fullName() {
        return `${this.firstName} ${this.lastName}`;
    }
};

// 2. Object constructor
const person2 = new Object();
person2.firstName = 'Jane';
person2.lastName = 'Smith';

// 3. Constructor function
function Person(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
}
Person.prototype.fullName = function() {
    return `${this.firstName} ${this.lastName}`;
};
const person3 = new Person('Bob', 'Johnson');

// 4. Object.create()
const personPrototype = {
    init(firstName, lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
        return this;
    },
    fullName() {
        return `${this.firstName} ${this.lastName}`;
    }
};
const person4 = Object.create(personPrototype).init('Alice', 'Brown');

// 5. ES2015 Classes
class PersonClass {
    constructor(firstName, lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }
    
    fullName() {
        return `${this.firstName} ${this.lastName}`;
    }
}
const person5 = new PersonClass('Charlie', 'Wilson');

// 6. Factory function
function createPerson(firstName, lastName) {
    return {
        firstName,
        lastName,
        fullName() {
            return `${this.firstName} ${this.lastName}`;
        }
    };
}
const person6 = createPerson('Diana', 'Prince');

// Testing all methods
console.log(person1.fullName()); // John Doe
console.log(person3.fullName()); // Bob Johnson
console.log(person5.fullName()); // Charlie Wilson
```


### 26. What is a higher-order function?

**Answer:**
A higher-order function either takes functions as arguments or returns functions, enabling functional programming patterns.[^1]

**Example:**

```javascript
// 1. Function that takes another function as argument
function greetUser(greeter, name) {
    return greeter(name);
}

function formalGreet(name) {
    return `Good day, ${name}!`;
}

function casualGreet(name) {
    return `Hey, ${name}!`;
}

console.log(greetUser(formalGreet, 'Alice')); // Good day, Alice!
console.log(greetUser(casualGreet, 'Bob'));   // Hey, Bob!

// 2. Function that returns another function
function multiplier(factor) {
    return function(number) {
        return number * factor;
    };
}

const double = multiplier(2);
const triple = multiplier(3);

console.log(double(5)); // 10
console.log(triple(4)); // 12

// 3. Built-in higher-order functions
const numbers = [1, 2, 3, 4, 5];

// map - transforms each element
const doubled = numbers.map(x => x * 2);
console.log(doubled); // [2, 4, 6, 8, 10]

// filter - selects elements
const evens = numbers.filter(x => x % 2 === 0);
console.log(evens); // [2, 4]

// reduce - accumulates values
const sum = numbers.reduce((acc, x) => acc + x, 0);
console.log(sum); // 15

// 4. Function composition
function add(a) {
    return function(b) {
        return a + b;
    };
}

function multiply(a) {
    return function(b) {
        return a * b;
    };
}

const addThenMultiply = (x) => multiply(2)(add(3)(x));
console.log(addThenMultiply(5)); // (5 + 3) * 2 = 16
```


### 27. Differences between ES5 constructor functions and ES2015 classes

**Answer:**
ES2015 classes provide cleaner syntax for object-oriented programming compared to ES5 constructor functions.[^1]

**Example:**

```javascript
// ES5 Constructor Function
function PersonES5(name, age) {
    this.name = name;
    this.age = age;
}

PersonES5.prototype.greet = function() {
    return `Hi, I'm ${this.name} and I'm ${this.age} years old.`;
};

PersonES5.staticMethod = function() {
    return "This is a static method";
};

// Inheritance in ES5
function StudentES5(name, age, grade) {
    PersonES5.call(this, name, age);
    this.grade = grade;
}

StudentES5.prototype = Object.create(PersonES5.prototype);
StudentES5.prototype.constructor = StudentES5;

StudentES5.prototype.study = function() {
    return `${this.name} is studying in grade ${this.grade}`;
};

// ES2015 Class
class PersonES6 {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    
    greet() {
        return `Hi, I'm ${this.name} and I'm ${this.age} years old.`;
    }
    
    static staticMethod() {
        return "This is a static method";
    }
}

// Inheritance in ES2015
class StudentES6 extends PersonES6 {
    constructor(name, age, grade) {
        super(name, age); // Call parent constructor
        this.grade = grade;
    }
    
    study() {
        return `${this.name} is studying in grade ${this.grade}`;
    }
    
    greet() {
        return super.greet() + ` I'm in grade ${this.grade}.`;
    }
}

// Usage comparison
const student1 = new StudentES5('John', 16, 10);
const student2 = new StudentES6('Jane', 17, 11);

console.log(student1.greet()); // Hi, I'm John and I'm 16 years old.
console.log(student2.greet()); // Hi, I'm Jane and I'm 17 years old. I'm in grade 11.
```


### 28. What is event bubbling?

**Answer:**
Event bubbling is when an event propagates from the target element upwards through its ancestors in the DOM tree.[^1]

**Example:**

```javascript
// HTML structure:
// <div id="outer">
//   <div id="middle">
//     <button id="inner">Click me</button>
//   </div>
// </div>

const outer = document.getElementById('outer');
const middle = document.getElementById('middle');
const inner = document.getElementById('inner');

// Add event listeners
outer.addEventListener('click', () => {
    console.log('Outer div clicked');
});

middle.addEventListener('click', () => {
    console.log('Middle div clicked');
});

inner.addEventListener('click', (event) => {
    console.log('Button clicked');
    
    // Stop propagation to prevent bubbling
    // event.stopPropagation();
});

// When button is clicked, output (with bubbling):
// Button clicked
// Middle div clicked  
// Outer div clicked

// Practical example: Event delegation
const list = document.getElementById('todo-list');

list.addEventListener('click', (event) => {
    if (event.target.tagName === 'LI') {
        console.log(`Clicked item: ${event.target.textContent}`);
    } else if (event.target.classList.contains('delete-btn')) {
        event.target.parentElement.remove();
    }
});

// Controlling event flow
inner.addEventListener('click', (event) => {
    event.stopPropagation(); // Stops bubbling
    event.preventDefault();  // Prevents default behavior
    event.stopImmediatePropagation(); // Stops other listeners on same element
});
```


### 29. What is event capturing?

**Answer:**
Event capturing (trickling) is the reverse of bubbling - events propagate from the root down to the target element.[^1]

**Example:**

```javascript
// HTML structure:
// <div id="outer">
//   <div id="middle">
//     <button id="inner">Click me</button>
//   </div>
// </div>

const outer = document.getElementById('outer');
const middle = document.getElementById('middle');
const inner = document.getElementById('inner');

// Capturing phase listeners (third parameter: true or {capture: true})
outer.addEventListener('click', () => {
    console.log('Outer div - CAPTURING');
}, true);

middle.addEventListener('click', () => {
    console.log('Middle div - CAPTURING');
}, true);

// Bubbling phase listeners (default)
inner.addEventListener('click', () => {
    console.log('Button - TARGET');
});

middle.addEventListener('click', () => {
    console.log('Middle div - BUBBLING');
});

outer.addEventListener('click', () => {
    console.log('Outer div - BUBBLING');
});

// When button is clicked, complete order:
// Outer div - CAPTURING
// Middle div - CAPTURING  
// Button - TARGET
// Middle div - BUBBLING
// Outer div - BUBBLING

// Mixed capturing and bubbling
document.addEventListener('click', (event) => {
    console.log(`Capturing: ${event.target.tagName}`);
}, { capture: true });

document.addEventListener('click', (event) => {
    console.log(`Bubbling: ${event.target.tagName}`);
}, { capture: false });

// Use case: Early event interception
document.addEventListener('click', (event) => {
    if (event.target.disabled) {
        event.stopPropagation();
        console.log('Disabled element clicked - preventing further propagation');
    }
}, true); // Capturing phase
```


### 30. Difference between `mouseenter` and `mouseover`

**Answer:**
`mouseenter` doesn't bubble and only fires when entering the element, while `mouseover` bubbles and fires for child elements too.[^1]

**Example:**

```javascript
// HTML:
// <div id="parent">
//   Parent
//   <div id="child">Child</div>
// </div>

const parent = document.getElementById('parent');
const child = document.getElementById('child');

// mouseenter - doesn't bubble
parent.addEventListener('mouseenter', () => {
    console.log('Mouse ENTERED parent');
});

parent.addEventListener('mouseleave', () => {
    console.log('Mouse LEFT parent');
});

// mouseover - bubbles
parent.addEventListener('mouseover', (event) => {
    console.log(`Mouse OVER: ${event.target.id}`);
});

parent.addEventListener('mouseout', (event) => {
    console.log(`Mouse OUT: ${event.target.id}`);
});

// Behavior comparison:
// When moving mouse from outside to parent:
// - mouseenter: fires once
// - mouseover: fires once

// When moving mouse from parent to child:
// - mouseenter: no additional firing
// - mouseover: fires again (bubbling from child)

// Practical usage
function createHoverEffect(element) {
    // Better for simple hover effects (no bubbling issues)
    element.addEventListener('mouseenter', () => {
        element.style.backgroundColor = 'lightblue';
    });
    
    element.addEventListener('mouseleave', () => {
        element.style.backgroundColor = '';
    });
}

// For delegation, use mouseover/mouseout
document.addEventListener('mouseover', (event) => {
    if (event.target.classList.contains('hoverable')) {
        event.target.style.backgroundColor = 'yellow';
    }
});
```


### 31. Synchronous vs asynchronous functions

**Answer:**
Synchronous functions block execution until completion, while asynchronous functions allow other code to run while waiting.[^1]

**Example:**

```javascript
// Synchronous function - blocks execution
function syncOperation() {
    console.log('Sync: Starting');
    
    // Simulate heavy computation
    const start = Date.now();
    while (Date.now() - start < 2000) {
        // Block for 2 seconds
    }
    
    console.log('Sync: Finished');
    return 'Sync result';
}

console.log('Before sync');
const result = syncOperation();
console.log('After sync:', result);

// Output:
// Before sync
// Sync: Starting
// (2 second pause)
// Sync: Finished  
// After sync: Sync result

// Asynchronous function - non-blocking
function asyncOperation() {
    console.log('Async: Starting');
    
    return new Promise((resolve) => {
        setTimeout(() => {
            console.log('Async: Finished');
            resolve('Async result');
        }, 2000);
    });
}

console.log('Before async');
asyncOperation().then(result => {
    console.log('After async:', result);
});
console.log('This runs immediately');

// Output:
// Before async
// Async: Starting
// This runs immediately
// (2 seconds later)
// Async: Finished
// After async: Async result

// Modern async/await syntax
async function modernAsync() {
    console.log('Modern: Starting');
    
    try {
        const result = await asyncOperation();
        console.log('Modern result:', result);
    } catch (error) {
        console.error('Error:', error);
    }
    
    console.log('Modern: Complete');
}

// Parallel vs Sequential async operations
async function sequentialOperations() {
    const start = Date.now();
    
    const result1 = await asyncOperation(); // Wait 2s
    const result2 = await asyncOperation(); // Wait another 2s
    
    console.log(`Sequential took: ${Date.now() - start}ms`); // ~4000ms
}

async function parallelOperations() {
    const start = Date.now();
    
    const [result1, result2] = await Promise.all([
        asyncOperation(), // Both start simultaneously
        asyncOperation()
    ]);
    
    console.log(`Parallel took: ${Date.now() - start}ms`); // ~2000ms
}
```


### 32. What is AJAX and how do you use it?

**Answer:**
AJAX (Asynchronous JavaScript and XML) allows web pages to fetch and send data asynchronously without reloading the page.[^1]

**Example:**

```javascript
// 1. XMLHttpRequest (traditional)
function fetchDataXHR() {
    const xhr = new XMLHttpRequest();
    
    xhr.onreadystatechange = function() {
        if (xhr.readyState === XMLHttpRequest.DONE) {
            if (xhr.status === 200) {
                const data = JSON.parse(xhr.responseText);
                console.log('XHR Success:', data);
            } else {
                console.error('XHR Error:', xhr.status);
            }
        }
    };
    
    xhr.open('GET', 'https://jsonplaceholder.typicode.com/posts/1', true);
    xhr.setRequestHeader('Content-Type', 'application/json');
    xhr.send();
}

// 2. Fetch API (modern)
async function fetchDataModern() {
    try {
        const response = await fetch('https://jsonplaceholder.typicode.com/posts/1');
        
        if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
        }
        
        const data = await response.json();
        console.log('Fetch Success:', data);
    } catch (error) {
        console.error('Fetch Error:', error);
    }
}

// 3. POST request with fetch
async function createPost(postData) {
    try {
        const response = await fetch('https://jsonplaceholder.typicode.com/posts', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify(postData)
        });
        
        const result = await response.json();
        console.log('Post created:', result);
        return result;
    } catch (error) {
        console.error('Error creating post:', error);
    }
}

// 4. Handling different response types
async function fetchImage() {
    const response = await fetch('https://example.com/image.jpg');
    const blob = await response.blob();
    
    const imageUrl = URL.createObjectURL(blob);
    const img = document.createElement('img');
    img.src = imageUrl;
    document.body.appendChild(img);
}

// 5. Abort controller for cancellation
function fetchWithCancel() {
    const controller = new AbortController();
    
    fetch('https://api.example.com/data', {
        signal: controller.signal
    })
    .then(response => response.json())
    .then(data => console.log(data))
    .catch(error => {
        if (error.name === 'AbortError') {
            console.log('Request cancelled');
        } else {
            console.error('Error:', error);
        }
    });
    
    // Cancel after 5 seconds
    setTimeout(() => controller.abort(), 5000);
}

// Usage examples
fetchDataXHR();
fetchDataModern();
createPost({ title: 'New Post', body: 'Content here', userId: 1 });
```


### 33. Differences between `XMLHttpRequest` and `fetch()`

**Answer:**
`fetch()` provides a modern Promise-based API with cleaner syntax, while `XMLHttpRequest` offers more granular control.[^1]

**Example:**

```javascript
// XMLHttpRequest - detailed control
function xhrExample() {
    const xhr = new XMLHttpRequest();
    
    // Progress tracking
    xhr.upload.onprogress = (event) => {
        if (event.lengthComputable) {
            const percentComplete = (event.loaded / event.total) * 100;
            console.log(`Upload progress: ${percentComplete}%`);
        }
    };
    
    // State changes
    xhr.onreadystatechange = function() {
        switch(xhr.readyState) {
            case XMLHttpRequest.UNSENT:
                console.log('Request not initialized');
                break;
            case XMLHttpRequest.OPENED:
                console.log('Request opened');
                break;
            case XMLHttpRequest.HEADERS_RECEIVED:
                console.log('Headers received');
                break;
            case XMLHttpRequest.LOADING:
                console.log('Loading response');
                break;
            case XMLHttpRequest.DONE:
                console.log('Request complete');
                break;
        }
    };
    
    xhr.open('POST', '/api/upload');
    xhr.setRequestHeader('X-Custom-Header', 'value');
    xhr.send(new FormData(document.getElementById('form')));
}

// Fetch - clean Promise-based API
async function fetchExample() {
    try {
        const response = await fetch('/api/data', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'X-Custom-Header': 'value'
            },
            body: JSON.stringify({ key: 'value' })
        });
        
        // Response object provides rich interface
        console.log('Status:', response.status);
        console.log('Headers:', [...response.headers.entries()]);
        console.log('OK:', response.ok);
        
        const data = await response.json();
        return data;
    } catch (error) {
        console.error('Fetch failed:', error);
    }
}

// Comparison table implementation
const comparisonFeatures = {
    syntax: {
        xhr: 'Callback-based, verbose',
        fetch: 'Promise-based, clean'
    },
    errorHandling: {
        xhr: 'Manual status checking',
        fetch: 'Network errors only, manual HTTP error checking'
    },
    progressTracking: {
        xhr: 'Built-in upload/download progress',
        fetch: 'No built-in progress (needs streams)'
    },
    responseTypes: {
        xhr: 'Multiple built-in types',
        fetch: 'Methods for different types (.json(), .text(), etc.)'
    },
    requestCancellation: {
        xhr: 'xhr.abort()',
        fetch: 'AbortController'
    },
    cors: {
        xhr: 'Manual CORS handling',
        fetch: 'Better CORS support'
    }
};

// Fetch with AbortController
function fetchWithTimeout(url, timeout = 5000) {
    const controller = new AbortController();
    
    const timeoutId = setTimeout(() => controller.abort(), timeout);
    
    return fetch(url, { signal: controller.signal })
        .then(response => {
            clearTimeout(timeoutId);
            return response;
        })
        .catch(error => {
            clearTimeout(timeoutId);
            throw error;
        });
}

// Progress tracking with fetch (using streams)
async function fetchWithProgress(url) {
    const response = await fetch(url);
    const reader = response.body.getReader();
    const contentLength = +response.headers.get('Content-Length');
    
    let receivedLength = 0;
    const chunks = [];
    
    while(true) {
        const { done, value } = await reader.read();
        
        if (done) break;
        
        chunks.push(value);
        receivedLength += value.length;
        
        const progress = (receivedLength / contentLength) * 100;
        console.log(`Download progress: ${progress}%`);
    }
    
    const allChunks = new Uint8Array(receivedLength);
    let position = 0;
    for(const chunk of chunks) {
        allChunks.set(chunk, position);
        position += chunk.length;
    }
    
    return new TextDecoder().decode(allChunks);
}
```


### 34. What are JavaScript data types?

**Answer:**
JavaScript has primitive types (string, number, boolean, null, undefined, symbol, bigint) and non-primitive types (object).[^1]

**Example:**

```javascript
// Primitive data types
let str = "Hello World";           // String
let num = 42;                      // Number
let bigNum = 9007199254740991n;    // BigInt
let bool = true;                   // Boolean
let empty = null;                  // Null
let notDefined;                    // Undefined
let sym = Symbol('id');            // Symbol

console.log(typeof str);           // "string"
console.log(typeof num);           // "number"
console.log(typeof bigNum);        // "bigint"
console.log(typeof bool);          // "boolean"
console.log(typeof empty);         // "object" (known quirk)
console.log(typeof notDefined);    // "undefined"
console.log(typeof sym);           // "symbol"

// Non-primitive (Reference) types
let obj = { name: "John" };        // Object
let arr = [1, 2, 3];              // Array (type of object)
let func = function() {};          // Function (type of function)
let date = new Date();             // Date (type of object)
let regex = /pattern/;             // RegExp (type of object)

console.log(typeof obj);           // "object"
console.log(typeof arr);           // "object"
console.log(typeof func);          // "function"
console.log(typeof date);          // "object"
console.log(typeof regex);         // "object"

// More specific type checking
console.log(Array.isArray(arr));          // true
console.log(obj instanceof Object);       // true
console.log(date instanceof Date);        // true
console.log(Object.prototype.toString.call(arr)); // "[object Array]"

// Type coercion examples
console.log("5" + 3);             // "53" (string concatenation)
console.log("5" - 3);             // 2 (numeric subtraction)
console.log(Boolean(""));         // false
console.log(Boolean("hello"));    // true
console.log(Number("123"));       // 123
console.log(String(123));         // "123"

// Checking for specific values
function getType(value) {
    if (value === null) return 'null';
    if (Array.isArray(value)) return 'array';
    if (value instanceof Date) return 'date';
    if (value instanceof RegExp) return 'regexp';
    return typeof value;
}

// Symbol usage example
const id1 = Symbol('id');
const id2 = Symbol('id');
console.log(id1 === id2);         // false (symbols are unique)

const person = {
    name: 'John',
    [id1]: 'secret value'
};
console.log(person[id1]);         // "secret value"

// BigInt for large numbers
const largeNumber = BigInt(Number.MAX_SAFE_INTEGER) + 1n;
console.log(largeNumber);         // 9007199254740992n
```


### 35. Different ways to iterate over objects and arrays

**Answer:**
JavaScript provides multiple iteration methods for objects and arrays, each with specific use cases.[^1]

**Example:**

```javascript
// Sample data
const obj = { a: 1, b: 2, c: 3 };
const arr = ['x', 'y', 'z'];

// Object iteration methods
console.log('=== OBJECT ITERATION ===');

// 1. for...in (includes inherited enumerable properties)
console.log('for...in:');
for (const key in obj) {
    if (Object.hasOwn(obj, key)) { // Check own properties
        console.log(`${key}: ${obj[key]}`);
    }
}

// 2. Object.keys()
console.log('Object.keys():');
Object.keys(obj).forEach(key => {
    console.log(`${key}: ${obj[key]}`);
});

// 3. Object.entries()
console.log('Object.entries():');
Object.entries(obj).forEach(([key, value]) => {
    console.log(`${key}: ${value}`);
});

// 4. Object.values()
console.log('Object.values():');
Object.values(obj).forEach(value => {
    console.log(value);
});

// 5. Object.getOwnPropertyNames() (includes non-enumerable)
console.log('Object.getOwnPropertyNames():');
Object.getOwnPropertyNames(obj).forEach(prop => {
    console.log(`${prop}: ${obj[prop]}`);
});

// Array iteration methods
console.log('\n=== ARRAY ITERATION ===');

// 1. Traditional for loop
console.log('Traditional for:');
for (let i = 0; i < arr.length; i++) {
    console.log(`${i}: ${arr[i]}`);
}

// 2. for...of (values)
console.log('for...of:');
for (const value of arr) {
    console.log(value);
}

// 3. for...in (indices, not recommended for arrays)
console.log('for...in (indices):');
for (const index in arr) {
    console.log(`${index}: ${arr[index]}`);
}

// 4. forEach
console.log('forEach:');
arr.forEach((value, index) => {
    console.log(`${index}: ${value}`);
});

// 5. entries() for index-value pairs
console.log('entries():');
for (const [index, value] of arr.entries()) {
    console.log(`${index}: ${value}`);
}

// 6. keys() for indices
console.log('keys():');
for (const index of arr.keys()) {
    console.log(`Index: ${index}`);
}

// 7. values() (same as for...of)
console.log('values():');
for (const value of arr.values()) {
    console.log(value);
}

// Advanced array methods
const numbers = [1, 2, 3, 4, 5];

// map - transform elements
const doubled = numbers.map(x => x * 2);
console.log('Mapped:', doubled); // [2, 4, 6, 8, 10]

// filter - select elements
const evens = numbers.filter(x => x % 2 === 0);
console.log('Filtered:', evens); // [2, 4]

// reduce - accumulate
const sum = numbers.reduce((acc, x) => acc + x, 0);
console.log('Reduced:', sum); // 15

// find - first matching element
const found = numbers.find(x => x > 3);
console.log('Found:', found); // 4

// some - test if any match
const hasEven = numbers.some(x => x % 2 === 0);
console.log('Has even:', hasEven); // true

// every - test if all match
const allPositive = numbers.every(x => x > 0);
console.log('All positive:', allPositive); // true

// Nested object/array iteration
const nested = {
    users: [
        { name: 'John', age: 30 },
        { name: 'Jane', age: 25 }
    ],
    settings: {
        theme: 'dark',
        notifications: true
    }
};

function iterateNested(obj, prefix = '') {
    for (const [key, value] of Object.entries(obj)) {
        const currentPath = prefix ? `${prefix}.${key}` : key;
        
        if (Array.isArray(value)) {
            console.log(`${currentPath}: [Array with ${value.length} items]`);
            value.forEach((item, index) => {
                if (typeof item === 'object') {
                    iterateNested(item, `${currentPath}[${index}]`);
                } else {
                    console.log(`${currentPath}[${index}]: ${item}`);
                }
            });
        } else if (typeof value === 'object' && value !== null) {
            console.log(`${currentPath}: [Object]`);
            iterateNested(value, currentPath);
        } else {
            console.log(`${currentPath}: ${value}`);
        }
    }
}

console.log('\n=== NESTED ITERATION ===');
iterateNested(nested);
```


### 36. Explain spread and rest operators

**Answer:**
The spread operator (`...`) expands elements, while the rest operator (`...`) collects multiple elements into an array or object.[^1]

**Example:**

```javascript
// SPREAD OPERATOR (...) - Expands elements
console.log('=== SPREAD OPERATOR ===');

// 1. Array spreading
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];

// Copying arrays
const copiedArray = [...arr1];
console.log('Copied:', copiedArray); // [1, 2, 3]

// Merging arrays
const mergedArray = [...arr1, ...arr2];
console.log('Merged:', mergedArray); // [1, 2, 3, 4, 5, 6]

// Adding elements
const extendedArray = [0, ...arr1, 3.5, ...arr2, 7];
console.log('Extended:', extendedArray); // [0, 1, 2, 3, 3.5, 4, 5, 6, 7]

// 2. Object spreading
const obj1 = { a: 1, b: 2 };
const obj2 = { c: 3, d: 4 };

// Copying objects
const copiedObject = { ...obj1 };
console.log('Copied object:', copiedObject); // { a: 1, b: 2 }

// Merging objects
const mergedObject = { ...obj1, ...obj2 };
console.log('Merged object:', mergedObject); // { a: 1, b: 2, c: 3, d: 4 }

// Overriding properties
const overridden = { ...obj1, b: 99, ...obj2 };
console.log('Overridden:', overridden); // { a: 1, b: 99, c: 3, d: 4 }

// 3. Function arguments
function sum(a, b, c) {
    return a + b + c;
}

const numbers = [1, 2, 3];
console.log('Sum with spread:', sum(...numbers)); // 6

// Finding max/min
const values = [10, 5, 8, 3, 15];
console.log('Max:', Math.max(...values)); // 15
console.log('Min:', Math.min(...values)); // 3

// 4. Converting NodeList to Array
// const elements = document.querySelectorAll('div');
// const elementsArray = [...elements];

// REST OPERATOR (...) - Collects elements
console.log('\n=== REST OPERATOR ===');

// 1. Function parameters
function collectArguments(first, second, ...rest) {
    console.log('First:', first);
    console.log('Second:', second);
    console.log('Rest:', rest);
}

collectArguments(1, 2, 3, 4, 5);
// First: 1
// Second: 2
// Rest: [3, 4, 5]

// Variable arguments function
function multiply(...numbers) {
    return numbers.reduce((acc, num) => acc * num, 1);
}

console.log('Multiply:', multiply(2, 3, 4)); // 24

// 2. Array destructuring
const [first, second, ...remaining] = [1, 2, 3, 4, 5];
console.log('First:', first);        // 1
console.log('Second:', second);      // 2
console.log('Remaining:', remaining); // [3, 4, 5]

// Skipping elements
const [x, , z, ...others] = [1, 2, 3, 4, 5];
console.log('x:', x);        // 1
console.log('z:', z);        // 3
console.log('others:', others); // [4, 5]

// 3. Object destructuring
const person = { name: 'John', age: 30, city: 'NYC', country: 'USA' };
const { name, age, ...address } = person;
console.log('Name:', name);       // John
console.log('Age:', age);         // 30
console.log('Address:', address); // { city: 'NYC', country: 'USA' }

// 4. Advanced patterns
// Swapping variables
let a = 1, b = 2;
[a, b] = [b, a];
console.log('Swapped:', a, b); // 2, 1

// Default values with rest
function greet(greeting = 'Hello', ...names) {
    return `${greeting} ${names.join(', ')}!`;
}
console.log(greet('Hi', 'Alice', 'Bob', 'Charlie')); // Hi Alice, Bob, Charlie!

// Nested destructuring with rest
const data = {
    users: [
        { id: 1, name: 'John', roles: ['admin', 'user'] },
        { id: 2, name: 'Jane', roles: ['user'] }
    ]
};

const { users: [firstUser, ...otherUsers] } = data;
const { id, name, roles: [primaryRole, ...secondaryRoles] } = firstUser;

console.log('First user ID:', id);                    // 1
console.log('Primary role:', primaryRole);            // admin
console.log('Secondary roles:', secondaryRoles);      // ['user']
console.log('Other users:', otherUsers);              // [{ id: 2, name: 'Jane', roles: ['user'] }]
```


### 37. Difference between Map and plain objects

**Answer:**
Maps provide better key handling, size tracking, and iteration compared to plain objects, though objects have simpler syntax.[^1]

**Example:**

```javascript
// Plain Object
const plainObject = {
    name: 'John',
    age: 30,
    'key with spaces': 'value'
};

// Map
const map = new Map();
map.set('name', 'John');
map.set('age', 30);
map.set('key with spaces', 'value');

console.log('=== KEY TYPES ===');

// Objects: string/symbol keys only
const obj = {};
obj[^1] = 'number key becomes string';
obj[true] = 'boolean key becomes string';
console.log('Object keys:', Object.keys(obj)); // ['1', 'true']

// Maps: any type of key
const mapWithVariousKeys = new Map();
mapWithVariousKeys.set(1, 'number key');
mapWithVariousKeys.set('1', 'string key');
mapWithVariousKeys.set(true, 'boolean key');
mapWithVariousKeys.set({}, 'object key');
mapWithVariousKeys.set(() => {}, 'function key');

console.log('Map size:', mapWithVariousKeys.size); // 5
console.log('Different keys:', mapWithVariousKeys.get(1) !== mapWithVariousKeys.get('1')); // true

console.log('\n=== SIZE TRACKING ===');

// Object: manual counting
const objectExample = { a: 1, b: 2, c: 3 };
console.log('Object size:', Object.keys(objectExample).length); // 3

// Map: built-in size property
const mapExample = new Map([['a', 1], ['b', 2], ['c', 3]]);
console.log('Map size:', mapExample.size); // 3

console.log('\n=== ITERATION ===');

// Object iteration
console.log('Object iteration:');
for (const [key, value] of Object.entries(objectExample)) {
    console.log(`${key}: ${value}`);
}

// Map iteration (maintains insertion order)
console.log('Map iteration:');
for (const [key, value] of mapExample) {
    console.log(`${key}: ${value}`);
}

// Map-specific iteration methods
console.log('Map keys:', [...mapExample.keys()]);     // ['a', 'b', 'c']
console.log('Map values:', [...mapExample.values()]); // [1, 2, 3]

console.log('\n=== PERFORMANCE COMPARISON ===');

// Performance test function
function performanceTest(name, operation, iterations = 1000000) {
    const start = performance.now();
    for (let i = 0; i < iterations; i++) {
        operation(i);
    }
    const end = performance.now();
    console.log(`${name}: ${(end - start).toFixed(2)}ms`);
}

// Object operations
const testObj = {};
performanceTest('Object set', (i) => {
    testObj[i] = i;
});

// Map operations
const testMap = new Map();
performanceTest('Map set', (i) => {
    testMap.set(i, i);
});

console.log('\n=== PRACTICAL EXAMPLES ===');

// Use case 1: Caching with object keys
class ObjectKeyCache {
    constructor() {
        this.cache = new Map();
    }
    
    get(keyObj) {
        return this.cache.get(keyObj);
    }
    
    set(keyObj, value) {
        this.cache.set(keyObj, value);
    }
    
    has(keyObj) {
        return this.cache.has(keyObj);
    }
}

const cache = new ObjectKeyCache();
const user1 = { id: 1 };
const user2 = { id: 2 };

cache.set(user1, 'User 1 data');
cache.set(user2, 'User 2 data');

console.log('Cached data for user1:', cache.get(user1)); // User 1 data

// Use case 2: Counting occurrences
function countWithObject(items) {
    const count = {};
    for (const item of items) {
        count[item] = (count[item] || 0) + 1;
    }
    return count;
}

function countWithMap(items) {
    const count = new Map();
    for (const item of items) {
        count.set(item, (count.get(item) || 0) + 1);
    }
    return count;
}

const items = ['apple', 'banana', 'apple', 'orange', 'banana', 'apple'];
console.log('Count with object:', countWithObject(items));
console.log('Count with map:', countWithMap(items));

// Use case 3: Preserving key order
const orderedMap = new Map([
    ['first', 1],
    ['second', 2],
    ['third', 3]
]);

// Maps guarantee insertion order
console.log('Map order preserved:', [...orderedMap.keys()]); // ['first', 'second', 'third']

// Objects preserve order for string keys in modern JS, but historically didn't
const orderedObj = { first: 1, second: 2, third: 3 };
console.log('Object order:', Object.keys(orderedObj)); // ['first', 'second', 'third']

console.log('\n=== WHEN TO USE WHICH ===');

// Use Objects when:
console.log('Use Objects for:');
console.log('- Records with known string keys');
console.log('- JSON serialization');
console.log('- Property access with dot notation');
console.log('- Class instances');

// Use Maps when:
console.log('\nUse Maps for:');
console.log('- Keys that are not strings/symbols');
console.log('- Frequent additions and removals');
console.log('- Need to know size frequently');
console.log('- Need guaranteed iteration order');
console.log('- Key-value pairs where keys are unknown until runtime');
```


### 38. Differences between `Map`/`Set` and `WeakMap`/`WeakSet`

**Answer:**
WeakMap and WeakSet only accept objects as keys, allow garbage collection, and aren't iterable, unlike Map and Set.[^1]

**Example:**

```javascript
console.log('=== MAP vs WEAKMAP ===');

// Map - any keys, prevents garbage collection
const map = new Map();
let obj1 = { name: 'John' };
let obj2 = { name: 'Jane' };

map.set('string-key', 'value1');
map.set(42, 'value2');
map.set(obj1, 'value3');

console.log('Map size:', map.size); // 3
console.log('Map is iterable:', [...map.keys()]); // ['string-key', 42, { name: 'John' }]

// WeakMap - only object keys, allows garbage collection
const weakMap = new WeakMap();

// weakMap.set('string', 'value'); // TypeError: Invalid value used as weak map key
// weakMap.set(42, 'value');       // TypeError: Invalid value used as weak map key
weakMap.set(obj1, 'metadata1');
weakMap.set(obj2, 'metadata2');

console.log('WeakMap has obj1:', weakMap.has(obj1)); // true
console.log('WeakMap get obj1:', weakMap.get(obj1)); // metadata1

// console.log('WeakMap size:', weakMap.size);        // undefined (no size property)
// console.log('WeakMap keys:', [...weakMap.keys()]); // TypeError: weakMap.keys is not a function

// Garbage collection demonstration
obj1 = null; // Original reference removed
// The entry in WeakMap can now be garbage collected
// The entry in Map will NOT be garbage collected

console.log('\n=== SET vs WEAKSET ===');

// Set - any values, prevents garbage collection
const set = new Set();
let obj3 = { id: 1 };
let obj4 = { id: 2 };

set.add('string');
set.add(42);
set.add(obj3);
set.add(obj4);

console.log('Set size:', set.size); // 4
console.log('Set has obj3:', set.has(obj3)); // true
console.log('Set values:', [...set]); // ['string', 42, { id: 1 }, { id: 2 }]

// WeakSet - only objects, allows garbage collection
const weakSet = new WeakSet();

// weakSet.add('string'); // TypeError: Invalid value used in weak set
// weakSet.add(42);       // TypeError: Invalid value used in weak set
weakSet.add(obj3);
weakSet.add(obj4);

console.log('WeakSet has obj3:', weakSet.has(obj3)); // true

// console.log('WeakSet size:', weakSet.size);        // undefined
// console.log('WeakSet values:', [...weakSet]);      // TypeError: weakSet is not iterable

console.log('\n=== PRACTICAL USE CASES ===');

// Use case 1: DOM element metadata with WeakMap
class DOMMetadata {
    constructor() {
        this.elementData = new WeakMap();
    }
    
    setData(element, data) {
        this.elementData.set(element, data);
    }
    
    getData(element) {
        return this.elementData.get(element);
    }
    
    hasData(element) {
        return this.elementData.has(element);
    }
}

const domMeta = new DOMMetadata();

// Simulate DOM elements
let div1 = { tagName: 'DIV', id: 'myDiv' };
let div2 = { tagName: 'DIV', id: 'otherDiv' };

domMeta.setData(div1, { clicks: 0, lastClicked: null });
domMeta.setData(div2, { clicks: 5, lastClicked: new Date() });

console.log('Div1 metadata:', domMeta.getData(div1));

// When DOM element is removed, metadata is automatically cleaned up
div1 = null; // Metadata for div1 can now be garbage collected

// Use case 2: Private properties with WeakMap
const privateProps = new WeakMap();

class Person {
    constructor(name, ssn) {
        this.name = name; // Public property
        privateProps.set(this, { ssn }); // Private property
    }
    
    getSSN() {
        const private_ = privateProps.get(this);
        return private_.ssn;
    }
    
    setSSN(ssn) {
        const private_ = privateProps.get(this);
        private_.ssn = ssn;
    }
}

const person = new Person('John Doe', '123-45-6789');
console.log('Public name:', person.name);      // John Doe
console.log('Private SSN:', person.getSSN());  // 123-45-6789
// console.log('Direct access:', person.ssn);  // undefined

// Use case 3: Tracking object registration with WeakSet
class ObjectTracker {
    constructor() {
        this.tracked = new WeakSet();
    }
    
    track(obj) {
        this.tracked.add(obj);
    }
    
    isTracked(obj) {
        return this.tracked.has(obj);
    }
    
    untrack(obj) {
        this.tracked.delete(obj);
    }
}

const tracker = new ObjectTracker();
let trackedObj = { data: 'important' };

tracker.track(trackedObj);
console.log('Is tracked:', tracker.isTracked(trackedObj)); // true

trackedObj = null; // Object can be garbage collected, automatically removed from WeakSet

console.log('\n=== MEMORY MANAGEMENT COMPARISON ===');

// Memory leak prevention example
function createMemoryLeakExample() {
    const elements = [];
    const strongMap = new Map();
    const weakMap = new WeakMap();
    
    // Create many objects
    for (let i = 0; i < 1000; i++) {
        const element = { id: i, data: `data-${i}` };
        elements.push(element);
        
        // Strong reference - prevents garbage collection
        strongMap.set(element, `metadata-${i}`);
        
        // Weak reference - allows garbage collection
        weakMap.set(element, `metadata-${i}`);
    }
    
    console.log('Created 1000 objects');
    console.log('Strong map size:', strongMap.size); // 1000
    console.log('Weak map has first element:', weakMap.has(elements[^0])); // true
    
    // Clear array but keep maps
    elements.length = 0;
    
    // Force garbage collection (if possible)
    if (global.gc) {
        global.gc();
    }
    
    console.log('After clearing array:');
    console.log('Strong map size:', strongMap.size); // Still 1000 (memory leak!)
    // WeakMap entries may be garbage collected (no direct way to check size)
    
    return { strongMap, weakMap };
}

// Uncomment to run memory example
// createMemoryLeakExample();

console.log('\n=== FEATURE COMPARISON TABLE ===');

const features = {
    'Key Types': {
        'Map': 'Any type',
        'WeakMap': 'Objects only',
        'Set': 'Any type',
        'WeakSet': 'Objects only'
    },
    'Size Property': {
        'Map': 'Yes',
        'WeakMap': 'No',
        'Set': 'Yes',
        'WeakSet': 'No'
    },
    'Iterable': {
        'Map': 'Yes',
        'WeakMap': 'No',
        'Set': 'Yes',
        'WeakSet': 'No'
    },
    'Garbage Collection': {
        'Map': 'Prevents GC',
        'WeakMap': 'Allows GC',
        'Set': 'Prevents GC',
        'WeakSet': 'Allows GC'
    },
    'Use Cases': {
        'Map': 'General key-value storage',
        'WeakMap': 'Private properties, metadata',
        'Set': 'Unique values collection',
        'WeakSet': 'Object tracking, registration'
    }
};

for (const [feature, comparison] of Object.entries(features)) {
    console.log(`\n${feature}:`);
    for (const [type, description] of Object.entries(comparison)) {
        console.log(`  ${type}: ${description}`);
    }
}
```


### 39. Use cases for arrow function syntax

**Answer:**
Arrow functions provide concise syntax and lexical `this` binding, making them ideal for callbacks and functional programming.[^1]

**Example:**

```javascript
console.log('=== TRADITIONAL vs ARROW FUNCTIONS ===');

// Traditional function syntax
const numbers = [1, 2, 3, 4, 5];

const traditionalDoubled = numbers.map(function(number) {
    return number * 2;
});

// Arrow function syntax
const arrowDoubled = numbers.map(number => number * 2);
const explicitArrowDoubled = numbers.map((number) => {
    return number * 2;
});

console.log('Traditional result:', traditionalDoubled); // [2, 4, 6, 8, 10]
console.log('Arrow result:', arrowDoubled);           // [2, 4, 6, 8, 10]

console.log('\n=== ARROW FUNCTION VARIATIONS ===');

// No parameters
const sayHello = () => 'Hello!';
console.log(sayHello()); // Hello!

// One parameter (parentheses optional)
const square = x => x * x;
const squareExplicit = (x) => x * x;
console.log('Square of 5:', square(5)); // 25

// Multiple parameters (parentheses required)
const add = (a, b) => a + b;
console.log('Add 3 + 4:', add(3, 4)); // 7

// No return value
const logMessage = message => console.log(`Log: ${message}`);
logMessage('Testing arrow function');

// Multiple statements (braces required)
const complexOperation = (x, y) => {
    const sum = x + y;
    const product = x * y;
    return { sum, product };
};
console.log('Complex result:', complexOperation(3, 4)); // { sum: 7, product: 12 }

// Returning objects (wrap in parentheses)
const createPerson = (name, age) => ({ name, age });
console.log('Person object:', createPerson('John', 30)); // { name: 'John', age: 30 }

console.log('\n=== PRACTICAL USE CASES ===');

// 1. Array methods (most common use case)
const data = [
    { name: 'Alice', age: 25, active: true },
    { name: 'Bob', age: 30, active: false },
    { name: 'Charlie', age: 35, active: true }
];

// Filtering active users
const activeUsers = data.filter(user => user.active);
console.log('Active users:', activeUsers);

// Extracting names
const names = data.map(user => user.name);
console.log('Names:', names); // ['Alice', 'Bob', 'Charlie']

// Finding user by name
const alice = data.find(user => user.name === 'Alice');
console.log('Found Alice:', alice);

// Calculating total age
const totalAge = data.reduce((sum, user) => sum + user.age, 0);
console.log('Total age:', totalAge); // 90

// 2. Event handlers (lexical this binding)
class Counter {
    constructor() {
        this.count = 0;
        this.button = { addEventListener: (event, callback) => callback() }; // Mock button
    }
    
    setupTraditional() {
        // Traditional function loses 'this' context
        this.button.addEventListener('click', function() {
            // this.count++; // Error: 'this' refers to button, not Counter
            console.log('Traditional this:', this);
        });
    }
    
    setupArrow() {
        // Arrow function preserves 'this' context
        this.button.addEventListener('click', () => {
            this.count++;
            console.log('Arrow this.count:', this.count);
        });
    }
    
    setupBound() {
        // Traditional function with explicit binding
        this.button.addEventListener('click', function() {
            this.count++;
            console.log('Bound this.count:', this.count);
        }.bind(this));
    }
}

const counter = new Counter();
counter.setupArrow();
counter.button.addEventListener('click', () => {}); // Simulate click

// 3. Promise chains
console.log('\n=== PROMISE CHAINS ===');

// Traditional promise chain
fetch('https://jsonplaceholder.typicode.com/posts/1')
    .then(function(response) {
        return response.json();
    })
    .then(function(data) {
        return data.title.toUpperCase();
    })
    .then(function(title) {
        console.log('Traditional promise title:', title);
    })
    .catch(function(error) {
        console.error('Traditional error:', error);
    });

// Arrow function promise chain
fetch('https://jsonplaceholder.typicode.com/posts/1')
    .then(response => response.json())
    .then(data => data.title.toUpperCase())
    .then(title => console.log('Arrow promise title:', title))
    .catch(error => console.error('Arrow error:', error));

// 4. Functional programming patterns
console.log('\n=== FUNCTIONAL PROGRAMMING ===');

// Composition with arrows
const pipe = (...functions) => value => 
    functions.reduce((acc, fn) => fn(acc), value);

const addOne = x => x + 1;
const double = x => x * 2;
const square = x => x * x;

const pipeline = pipe(addOne, double, square);
console.log('Pipeline result (3):', pipeline(3)); // ((3 + 1) * 2)² = 64

// Currying
const multiply = a => b => a * b;
const multiplyByTwo = multiply(2);
const multiplyByThree = multiply(3);

console.log('Curried multiply:', multiplyByTwo(5)); // 10
console.log('Curried multiply:', multiplyByThree(4)); // 12

// 5. React component examples (conceptual)
console.log('\n=== REACT-STYLE EXAMPLES ===');

// Event handlers in React-style components
const ReactStyleComponent = {
    state: { count: 0 },
    
    // Arrow function preserves 'this'
    handleIncrement: function() {
        this.state.count++;
        console.log('Count incremented to:', this.state.count);
    },
    
    // Would need binding with traditional function
    handleIncrementTraditional: function() {
        return function() {
            // this.state.count++; // Error: 'this' is undefined or refers to wrong object
        };
    },
    
    render: function() {
        return {
            onClick: () => this.handleIncrement() // Arrow function preserves 'this'
        };
    }
};

// 6. Short conditional logic
const users = ['admin', 'user', 'guest'];

// Traditional
const traditionalRoles = users.map(function(user) {
    if (user === 'admin') {
        return 'Administrator';
    } else {
        return 'Regular User';
    }
});

// Arrow with ternary
const arrowRoles = users.map(user => 
    user === 'admin' ? 'Administrator' : 'Regular User'
);

console.log('Traditional roles:', traditionalRoles);
console.log('Arrow roles:', arrowRoles);

console.log('\n=== WHEN NOT TO USE ARROW FUNCTIONS ===');

// Don't use for object methods that need dynamic 'this'
const person = {
    name: 'John',
    
    // Good: traditional function
    greetTraditional: function() {
        console.log(`Hello, I'm ${this.name}`);
    },
    
    // Bad: arrow function
    greetArrow: () => {
        console.log(`Hello, I'm ${this.name}`); // 'this.name' is undefined
    },
    
    // Good: method shorthand
    greetShorthand() {
        console.log(`Hello, I'm ${this.name}`);
    }
};

person.greetTraditional(); // Hello, I'm John
person.greetArrow();       // Hello, I'm undefined
person.greetShorthand();   // Hello, I'm John

// Don't use for constructors
// const Person = (name) => {  // Error: Arrow functions cannot be constructors
//     this.name = name;
// };

// Don't use when you need 'arguments' object
function traditionalWithArguments() {
    console.log('Arguments length:', arguments.length);
    console

<div style="text-align: center">⁂</div>

[^1]: https://www.greatfrontend.com/blog/50-must-know-javascript-interview-questions-by-ex-interviewers
[^2]: https://www.interviewbit.com/javascript-interview-questions/
[^3]: https://www.geeksforgeeks.org/javascript/javascript-interview-questions/
[^4]: https://builtin.com/software-engineering-perspectives/javascript-interview-questions
[^5]: https://github.com/greatfrontend/top-javascript-interview-questions
[^6]: https://www.simplilearn.com/tutorials/javascript-tutorial/javascript-interview-questions
[^7]: https://www.keka.com/javascript-coding-interview-questions-and-answers
[^8]: https://roadmap.sh/questions/javascript
[^9]: https://www.youtube.com/watch?v=qTszFuibDEg
[^10]: https://www.h2kinfosys.com/blog/top-html-css-javascript-interview-questions-answers/```

