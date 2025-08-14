<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# 🟨 50 Most Asked JavaScript Interview Questions \& Answers

## 📋 Table of Contents

| \# | Question Category | Quick Link |
| :-- | :-- | :-- |
| [1-10](#basic-javascript-concepts-) | Basic JavaScript Concepts | Foundation knowledge |
| [11-20](#data-types-and-variables-) | Data Types \& Variables | Core language features |
| [21-30](#functions-and-scope-) | Functions \& Scope | Programming fundamentals |
| [31-40](#objects-and-arrays-) | Objects \& Arrays | Data structures |
| [41-50](#advanced-concepts-) | Advanced Concepts | Expert-level topics |


***

## Basic JavaScript Concepts 🚀

<details>
<summary><strong>1. What is JavaScript and how does it work?</strong></summary>

**Answer:**
JavaScript is a high-level, interpreted programming language that is used to create interactive web pages and web applications. It runs in the browser and can also run server-side with Node.js. JavaScript is dynamically typed, supports both object-oriented and functional programming paradigms.

**Example:**
```javascript
// Basic JavaScript example
console.log("Hello, World!"); // Output: Hello, World!

// JavaScript can run in browser or Node.js
if (typeof window !== 'undefined') {
    console.log("Running in browser");
} else {
    console.log("Running in Node.js");
}

// Dynamic typing example
let variable = "I'm a string";
console.log(typeof variable); // string

variable = 42;
console.log(typeof variable); // number

variable = true;
console.log(typeof variable); // boolean
```
</details>
<details>
<summary><strong>2. What are the different data types in JavaScript?</strong></summary>

**Answer:**
JavaScript has two categories of data types: Primitive and Non-primitive (Reference) types. Primitive types include string, number, boolean, undefined, null, symbol, and bigint. Non-primitive types include objects, arrays, and functions.

**Example:**
```javascript
// Primitive data types
let str = "Hello World";           // string
let num = 42;                      // number
let bool = true;                   // boolean
let undef = undefined;             // undefined
let n = null;                      // null
let sym = Symbol('id');            // symbol
let bigInt = 1234567890123456789n; // bigint

// Non-primitive data types
let obj = { name: "John", age: 30 }; // object
let arr = [1, 2, 3, 4, 5];          // array (special type of object)
let func = function() { return "Hello"; }; // function

// Checking data types
console.log(typeof str);    // "string"
console.log(typeof num);    // "number"
console.log(typeof bool);   // "boolean"
console.log(typeof undef);  // "undefined"
console.log(typeof n);      // "object" (this is a known quirk)
console.log(typeof sym);    // "symbol"
console.log(typeof bigInt); // "bigint"
console.log(typeof obj);    // "object"
console.log(typeof arr);    // "object"
console.log(typeof func);   // "function"

// Better way to check arrays
console.log(Array.isArray(arr)); // true
```
</details>
<details>
<summary><strong>3. What is the difference between == and === operators?</strong></summary>

**Answer:**
The == operator performs loose equality comparison with type coercion, while === performs strict equality comparison without type coercion. The === operator checks both value and data type, making it more predictable and safer to use.

**Example:**
```javascript
// Loose equality (==) - performs type coercion
console.log(5 == "5");        // true (string "5" is converted to number)
console.log(true == 1);       // true (boolean true is converted to 1)
console.log(false == 0);      // true (boolean false is converted to 0)
console.log(null == undefined); // true (special case)
console.log(0 == false);      // true (false is converted to 0)
console.log("" == false);     // true (empty string is converted to 0)

// Strict equality (===) - no type coercion
console.log(5 === "5");       // false (different types)
console.log(true === 1);      // false (different types)
console.log(false === 0);     // false (different types)
console.log(null === undefined); // false (different types)
console.log(0 === false);     // false (different types)
console.log("" === false);    // false (different types)

// Same type comparisons work the same way
console.log(5 === 5);         // true
console.log("hello" === "hello"); // true

// Best practice: Always use === unless you specifically need type coercion
function safeEquals(a, b) {
    return a === b;
}

console.log(safeEquals(5, 5));     // true
console.log(safeEquals(5, "5"));   // false
```
</details>
<details>
<summary><strong>4. What is hoisting in JavaScript?</strong></summary>

**Answer:**
Hoisting is JavaScript's behavior of moving variable and function declarations to the top of their containing scope during compilation. This means you can use variables and functions before they are declared in the code.

**Example:**
```javascript
// Variable hoisting example
console.log(x); // undefined (not ReferenceError)
var x = 5;
console.log(x); // 5

// What actually happens (conceptually):
// var x; // hoisted to top, initialized with undefined
// console.log(x); // undefined
// x = 5;
// console.log(x); // 5

// Function hoisting
sayHello(); // "Hello World!" - works because function is hoisted

function sayHello() {
    console.log("Hello World!");
}

// let and const hoisting (Temporal Dead Zone)
// console.log(y); // ReferenceError: Cannot access 'y' before initialization
let y = 10;

// console.log(z); // ReferenceError: Cannot access 'z' before initialization
const z = 20;

// Function expressions are not hoisted
// sayGoodbye(); // TypeError: sayGoodbye is not a function

var sayGoodbye = function() {
    console.log("Goodbye!");
};

// Arrow functions are also not hoisted
// greet(); // ReferenceError: Cannot access 'greet' before initialization

const greet = () => {
    console.log("Greetings!");
};

// Example showing the difference
function hoistingExample() {
    console.log(a); // undefined
    // console.log(b); // ReferenceError
    
    var a = 1;
    let b = 2;
}
```
</details>
<details>
<summary><strong>5. What is the difference between var, let, and const?</strong></summary>

**Answer:**
var has function scope and is hoisted with undefined initialization. let and const have block scope and are hoisted but not initialized (temporal dead zone). const requires initialization and cannot be reassigned, while let can be reassigned.

**Example:**
```javascript
// Scope differences
function scopeExample() {
    if (true) {
        var varVariable = "I'm var";
        let letVariable = "I'm let";
        const constVariable = "I'm const";
    }
    
    console.log(varVariable);    // "I'm var" (function scoped)
    // console.log(letVariable);   // ReferenceError (block scoped)
    // console.log(constVariable); // ReferenceError (block scoped)
}

// Hoisting differences
console.log(varHoisted);  // undefined
// console.log(letHoisted);  // ReferenceError
// console.log(constHoisted); // ReferenceError

var varHoisted = "var value";
let letHoisted = "let value";
const constHoisted = "const value";

// Reassignment
var varValue = "initial";
varValue = "changed"; // ✅ Allowed

let letValue = "initial";
letValue = "changed"; // ✅ Allowed

const constValue = "initial";
// constValue = "changed"; // ❌ TypeError: Assignment to constant variable

// const with objects (reference is constant, not content)
const person = { name: "John", age: 30 };
person.age = 31;        // ✅ Allowed (modifying property)
person.city = "NYC";    // ✅ Allowed (adding property)
// person = {};         // ❌ TypeError (reassigning reference)

const numbers = [1, 2, 3];
numbers.push(4);        // ✅ Allowed (modifying array)
// numbers = [];        // ❌ TypeError (reassigning reference)

// Block scope example
for (let i = 0; i < 3; i++) {
    setTimeout(() => console.log(i), 100); // 0, 1, 2
}

for (var j = 0; j < 3; j++) {
    setTimeout(() => console.log(j), 100); // 3, 3, 3
}
```
</details>
<details>
<summary><strong>6. What are closures in JavaScript?</strong></summary>

**Answer:**
A closure is a function that has access to variables from its outer (enclosing) scope even after the outer function has finished executing. Closures are created when a function is defined inside another function and the inner function references variables from the outer function.

**Example:**
```javascript
// Basic closure example
function outerFunction(x) {
    // This is the outer function's scope
    
    function innerFunction(y) {
        // This inner function has access to x
        return x + y;
    }
    
    return innerFunction;
}

const addFive = outerFunction(5);
console.log(addFive(3)); // 8 (5 + 3)

// Private variables using closures
function createCounter() {
    let count = 0; // Private variable
    
    return {
        increment: function() {
            count++;
            return count;
        },
        decrement: function() {
            count--;
            return count;
        },
        getCount: function() {
            return count;
        }
    };
}

const counter = createCounter();
console.log(counter.increment()); // 1
console.log(counter.increment()); // 2
console.log(counter.getCount()); // 2
console.log(counter.decrement()); // 1
// console.log(counter.count); // undefined (private)

// Module pattern using closures
const Calculator = (function() {
    let result = 0; // Private variable
    
    return {
        add: function(x) {
            result += x;
            return this;
        },
        subtract: function(x) {
            result -= x;
            return this;
        },
        multiply: function(x) {
            result *= x;
            return this;
        },
        getResult: function() {
            return result;
        },
        reset: function() {
            result = 0;
            return this;
        }
    };
})();

// Method chaining with closures
const calculation = Calculator
    .add(10)
    .multiply(2)
    .subtract(5)
    .getResult();

console.log(calculation); // 15

// Common closure pitfall and solution
function createFunctions() {
    var functions = [];
    
    // Problem: All functions will log 3
    for (var i = 0; i < 3; i++) {
        functions[i] = function() {
            console.log(i);
        };
    }
    
    return functions;
}

const funcs = createFunctions();
funcs[0](); // 3 (not 0!)
funcs[1](); // 3 (not 1!)
funcs[2](); // 3 (not 2!)

// Solution 1: Use let instead of var
function createFunctionsFixed1() {
    var functions = [];
    
    for (let i = 0; i < 3; i++) {
        functions[i] = function() {
            console.log(i);
        };
    }
    
    return functions;
}

// Solution 2: Use IIFE (Immediately Invoked Function Expression)
function createFunctionsFixed2() {
    var functions = [];
    
    for (var i = 0; i < 3; i++) {
        functions[i] = (function(index) {
            return function() {
                console.log(index);
            };
        })(i);
    }
    
    return functions;
}

const fixedFuncs = createFunctionsFixed1();
fixedFuncs[0](); // 0
fixedFuncs[1](); // 1
fixedFuncs[2](); // 2
```
</details>
<details>
<summary><strong>7. What is the difference between null and undefined?</strong></summary>

**Answer:**
undefined means a variable has been declared but not assigned a value, or a function doesn't return anything. null is an intentional assignment representing "no value" or "empty value". Both are falsy but represent different states.

**Example:**
```javascript
// undefined examples
let declaredButNotAssigned;
console.log(declaredButNotAssigned); // undefined

function noReturn() {
    // No return statement
}
console.log(noReturn()); // undefined

const obj = { name: "John" };
console.log(obj.age); // undefined (property doesn't exist)

function withParams(a, b, c) {
    console.log(a, b, c);
}
withParams(1, 2); // 1 2 undefined (c is not provided)

// null examples
let intentionallyEmpty = null;
console.log(intentionallyEmpty); // null

// API response example
const user = {
    name: "John",
    avatar: null, // User has no profile picture
    email: "john@example.com"
};

// Type checking
console.log(typeof undefined); // "undefined"
console.log(typeof null);      // "object" (this is a known bug in JavaScript)

// Equality comparisons
console.log(null == undefined);  // true (loose equality)
console.log(null === undefined); // false (strict equality)

// Truthiness
console.log(!!null);      // false
console.log(!!undefined); // false

// JSON behavior
console.log(JSON.stringify({ a: undefined, b: null })); 
// {"b":null} - undefined properties are omitted

// Checking for null or undefined
function isNullOrUndefined(value) {
    return value == null; // Checks for both null and undefined
}

function isNullOrUndefinedStrict(value) {
    return value === null || value === undefined;
}

// Nullish coalescing operator (??)
const defaultValue = null ?? undefined ?? "default";
console.log(defaultValue); // "default"

const userName = user.name ?? "Guest";
console.log(userName); // "John"

const avatarUrl = user.avatar ?? "/default-avatar.png";
console.log(avatarUrl); // "/default-avatar.png"

// Optional chaining with null/undefined
const userProfile = {
    user: {
        details: null
    }
};

console.log(userProfile.user?.details?.name); // undefined
console.log(userProfile.user?.avatar?.url);   // undefined
```
</details>
<details>
<summary><strong>8. What are template literals in JavaScript?</strong></summary>

**Answer:**
Template literals are a way to define strings using backticks (`) instead of quotes. They support embedded expressions using ${} syntax, multi-line strings, and tagged templates for advanced string processing.

**Example:**
```javascript
// Basic template literals
const name = "John";
const age = 30;

// Old way with concatenation
const greeting1 = "Hello, my name is " + name + " and I'm " + age + " years old.";

// New way with template literals
const greeting2 = `Hello, my name is ${name} and I'm ${age} years old.`;

console.log(greeting2); // "Hello, my name is John and I'm 30 years old."

// Multi-line strings
const multiLine = `
    This is a multi-line string.
    No need for \n characters.
    It preserves line breaks and    spacing.
`;

console.log(multiLine);

// Expressions in template literals
const a = 10;
const b = 20;
console.log(`The sum of ${a} and ${b} is ${a + b}`); // "The sum of 10 and 20 is 30"

// Function calls in template literals
function formatCurrency(amount) {
    return `$${amount.toFixed(2)}`;
}

const price = 19.99;
console.log(`Total: ${formatCurrency(price)}`); // "Total: $19.99"

// Conditional expressions
const user = { name: "Alice", isAdmin: true };
const message = `Welcome ${user.name}! ${user.isAdmin ? 'You have admin access.' : 'You have regular access.'}`;
console.log(message); // "Welcome Alice! You have admin access."

// Nested template literals
const colors = ['red', 'green', 'blue'];
const html = `
    <ul>
        ${colors.map(color => `<li style="color: ${color}">${color}</li>`).join('')}
    </ul>
`;

console.log(html);

// Tagged templates
function highlight(strings, ...values) {
    return strings.reduce((result, string, i) => {
        const value = values[i] ? `<mark>${values[i]}</mark>` : '';
        return result + string + value;
    }, '');
}

const term = "JavaScript";
const description = "programming language";
const tagged = highlight`${term} is a ${description} for web development.`;
console.log(tagged); // "<mark>JavaScript</mark> is a <mark>programming language</mark> for web development."

// Raw strings
function raw(strings, ...values) {
    console.log(strings.raw); // Access to raw strings
    return strings.raw.join('');
}

const path = raw`C:\Users\John\Documents`;
console.log(path); // "C:\Users\John\Documents" (backslashes preserved)

// HTML generation
function createCard(title, content, imageUrl) {
    return `
        <div class="card">
            <img src="${imageUrl}" alt="${title}">
            <div class="card-content">
                <h3>${title}</h3>
                <p>${content}</p>
            </div>
        </div>
    `;
}

// URL building
function buildApiUrl(baseUrl, endpoint, params = {}) {
    const queryString = Object.entries(params)
        .map(([key, value]) => `${key}=${encodeURIComponent(value)}`)
        .join('&');
    
    return `${baseUrl}/${endpoint}${queryString ? `?${queryString}` : ''}`;
}

const apiUrl = buildApiUrl('https://api.example.com', 'users', { 
    page: 1, 
    limit: 10, 
    search: 'john doe' 
});
console.log(apiUrl); // "https://api.example.com/users?page=1&limit=10&search=john%20doe"
```
</details>
<details>
<summary><strong>9. What is event bubbling and event capturing?</strong></summary>

**Answer:**
Event bubbling is when an event starts from the target element and bubbles up to parent elements. Event capturing (trickling) is the opposite - events start from the root and capture down to the target. You can control this behavior using the third parameter of addEventListener.

**Example:**
```javascript
// HTML structure for example:
// <div id="outer">
//   <div id="middle">
//     <button id="inner">Click me</button>
//   </div>
// </div>

// Event bubbling (default behavior)
document.getElementById('outer').addEventListener('click', function() {
    console.log('Outer div clicked (bubbling)');
});

document.getElementById('middle').addEventListener('click', function() {
    console.log('Middle div clicked (bubbling)');
});

document.getElementById('inner').addEventListener('click', function() {
    console.log('Button clicked (bubbling)');
});

// When button is clicked, output:
// "Button clicked (bubbling)"
// "Middle div clicked (bubbling)"
// "Outer div clicked (bubbling)"

// Event capturing
document.getElementById('outer').addEventListener('click', function() {
    console.log('Outer div clicked (capturing)');
}, true); // true enables capturing

document.getElementById('middle').addEventListener('click', function() {
    console.log('Middle div clicked (capturing)');
}, true);

document.getElementById('inner').addEventListener('click', function() {
    console.log('Button clicked (capturing)');
}, true);

// When button is clicked, output:
// "Outer div clicked (capturing)"
// "Middle div clicked (capturing)"
// "Button clicked (capturing)"

// Stopping event propagation
document.getElementById('middle').addEventListener('click', function(event) {
    console.log('Middle div clicked - stopping propagation');
    event.stopPropagation(); // Prevents further bubbling/capturing
});

// Preventing default behavior
document.getElementById('myLink').addEventListener('click', function(event) {
    event.preventDefault(); // Prevents link from navigating
    console.log('Link clicked but navigation prevented');
});

// Event delegation using bubbling
document.getElementById('todoList').addEventListener('click', function(event) {
    if (event.target.classList.contains('delete-btn')) {
        // Handle delete button click
        const todoItem = event.target.closest('.todo-item');
        todoItem.remove();
    } else if (event.target.classList.contains('edit-btn')) {
        // Handle edit button click
        const todoItem = event.target.closest('.todo-item');
        editTodo(todoItem);
    }
});

// Real-world example: Modal dialog
class Modal {
    constructor(modalId) {
        this.modal = document.getElementById(modalId);
        this.overlay = this.modal.querySelector('.modal-overlay');
        this.content = this.modal.querySelector('.modal-content');
        
        this.setupEventListeners();
    }
    
    setupEventListeners() {
        // Close modal when clicking overlay (bubbling)
        this.overlay.addEventListener('click', (event) => {
            if (event.target === this.overlay) {
                this.close();
            }
        });
        
        // Prevent modal from closing when clicking content
        this.content.addEventListener('click', (event) => {
            event.stopPropagation();
        });
        
        // Close with Escape key
        document.addEventListener('keydown', (event) => {
            if (event.key === 'Escape' && this.isOpen()) {
                this.close();
            }
        });
    }
    
    open() {
        this.modal.classList.add('active');
    }
    
    close() {
        this.modal.classList.remove('active');
    }
    
    isOpen() {
        return this.modal.classList.contains('active');
    }
}
```
</details>
<details>
<summary><strong>10. What is the difference between synchronous and asynchronous programming?</strong></summary>

**Answer:**
Synchronous programming executes code line by line, blocking execution until each operation completes. Asynchronous programming allows multiple operations to run concurrently without blocking, using callbacks, promises, or async/await to handle completion.

**Example:**
```javascript
// Synchronous example (blocking)
console.log("Start");

function syncOperation() {
    // Simulate time-consuming operation
    const start = Date.now();
    while (Date.now() - start < 2000) {
        // Block for 2 seconds
    }
    return "Sync operation completed";
}

console.log(syncOperation()); // Blocks for 2 seconds
console.log("End");

// Output:
// "Start"
// (2 second delay)
// "Sync operation completed"
// "End"

// Asynchronous example (non-blocking)
console.log("Start");

function asyncOperation() {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve("Async operation completed");
        }, 2000);
    });
}

asyncOperation().then(result => {
    console.log(result);
});

console.log("End");

// Output:
// "Start"
// "End"
// (2 second delay)
// "Async operation completed"

// Callback pattern (older async approach)
function fetchData(callback) {
    setTimeout(() => {
        const data = { id: 1, name: "John Doe" };
        callback(null, data); // error-first callback
    }, 1000);
}

fetchData((error, data) => {
    if (error) {
        console.error("Error:", error);
    } else {
        console.log("Data received:", data);
    }
});

// Promise-based solution
function promiseBasedApproach() {
    getUserIdPromise()
        .then(userId => getUserPromise(userId))
        .then(user => getUserPostsPromise(user.id))
        .then(posts => getPostCommentsPromise(posts[0].id))
        .then(comments => console.log("Comments:", comments))
        .catch(error => console.error("Error:", error));
}

// Async/await solution (modern approach)
async function asyncAwaitApproach() {
    try {
        const userId = await getUserIdPromise();
        const user = await getUserPromise(userId);
        const posts = await getUserPostsPromise(user.id);
        const comments = await getPostCommentsPromise(posts[0].id);
        console.log("Comments:", comments);
    } catch (error) {
        console.error("Error:", error);
    }
}

// Multiple async operations
async function handleMultipleOperations() {
    // Sequential execution (slower)
    const user1 = await fetchUser(1);
    const user2 = await fetchUser(2);
    const user3 = await fetchUser(3);
    
    // Parallel execution (faster)
    const [userA, userB, userC] = await Promise.all([
        fetchUser(1),
        fetchUser(2),
        fetchUser(3)
    ]);
    
    // Race condition (first to complete wins)
    const fastestResponse = await Promise.race([
        fetchFromServer1(),
        fetchFromServer2(),
        fetchFromServer3()
    ]);
    
    return { sequential: [user1, user2, user3], parallel: [userA, userB, userC], fastest: fastestResponse };
}

// Real-world example: API calls with loading states
class ApiService {
    constructor() {
        this.loading = false;
        this.cache = new Map();
    }
    
    async fetchUser(userId, useCache = true) {
        const cacheKey = `user_${userId}`;
        
        // Check cache first
        if (useCache && this.cache.has(cacheKey)) {
            return this.cache.get(cacheKey);
        }
        
        this.loading = true;
        
        try {
            const response = await fetch(`/api/users/${userId}`);
            
            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }
            
            const user = await response.json();
            
            // Cache the result
            this.cache.set(cacheKey, user);
            
            return user;
        } catch (error) {
            console.error('Failed to fetch user:', error);
            throw error;
        } finally {
            this.loading = false;
        }
    }
}

// Usage example
const apiService = new ApiService();

async function displayUserProfile(userId) {
    const loadingElement = document.getElementById('loading');
    const profileElement = document.getElementById('profile');
    const errorElement = document.getElementById('error');
    
    try {
        loadingElement.style.display = 'block';
        errorElement.style.display = 'none';
        
        const user = await apiService.fetchUser(userId);
        
        profileElement.innerHTML = `
            <h2>${user.name}</h2>
            <p>Email: ${user.email}</p>
            <p>Joined: ${new Date(user.createdAt).toLocaleDateString()}</p>
        `;
        
        profileElement.style.display = 'block';
    } catch (error) {
        errorElement.textContent = 'Failed to load user profile';
        errorElement.style.display = 'block';
    } finally {
        loadingElement.style.display = 'none';
    }
}
```
</details>

***

## Data Types and Variables 📊

<details>
<summary><strong>11. How do you check the type of a variable in JavaScript?</strong></summary>

**Answer:**
JavaScript provides several ways to check variable types: typeof operator for primitive types, instanceof for objects, Array.isArray() for arrays, and Object.prototype.toString.call() for precise type checking.

**Example:**
```javascript
// typeof operator
console.log(typeof "hello");      // "string"
console.log(typeof 42);           // "number"
console.log(typeof true);         // "boolean"
console.log(typeof undefined);    // "undefined"
console.log(typeof null);         // "object" (known quirk)
console.log(typeof {});           // "object"
console.log(typeof []);           // "object"
console.log(typeof function(){}); // "function"

// More precise type checking
function getType(value) {
    return Object.prototype.toString.call(value).slice(8, -1).toLowerCase();
}

console.log(getType("hello"));       // "string"
console.log(getType(42));            // "number"
console.log(getType(true));          // "boolean"
console.log(getType(undefined));     // "undefined"
console.log(getType(null));          // "null"
console.log(getType({}));            // "object"
console.log(getType([]));            // "array"
console.log(getType(new Date()));    // "date"
console.log(getType(/regex/));       // "regexp"

// Specific type checking functions
function isString(value) {
    return typeof value === 'string';
}

function isNumber(value) {
    return typeof value === 'number' && !isNaN(value);
}

function isArray(value) {
    return Array.isArray(value);
}

function isObject(value) {
    return value !== null && typeof value === 'object' && !Array.isArray(value);
}

function isFunction(value) {
    return typeof value === 'function';
}

// instanceof operator
class Person {
    constructor(name) {
        this.name = name;
    }
}

const john = new Person("John");
console.log(john instanceof Person);  // true
console.log(john instanceof Object);  // true
console.log([] instanceof Array);     // true
console.log([] instanceof Object);    // true

// Comprehensive type checker
class TypeChecker {
    static getDetailedType(value) {
        const type = typeof value;
        
        if (type === 'object') {
            if (value === null) return 'null';
            if (Array.isArray(value)) return 'array';
            if (value instanceof Date) return 'date';
            if (value instanceof RegExp) return 'regexp';
            if (value instanceof Error) return 'error';
            return 'object';
        }
        
        if (type === 'number') {
            if (isNaN(value)) return 'nan';
            if (!isFinite(value)) return 'infinity';
            return 'number';
        }
        
        return type;
    }
    
    static is(value, expectedType) {
        return this.getDetailedType(value) === expectedType.toLowerCase();
    }
    
    static isNumeric(value) {
        return !isNaN(parseFloat(value)) && isFinite(value);
    }
    
    static isEmpty(value) {
        if (value == null) return true;
        if (typeof value === 'string') return value.length === 0;
        if (Array.isArray(value)) return value.length === 0;
        if (typeof value === 'object') return Object.keys(value).length === 0;
        return false;
    }
}

// Usage examples
const testValues = [
    "hello",
    42,
    true,
    null,
    undefined,
    {},
    [],
    new Date(),
    /regex/,
    function() {},
    NaN,
    Infinity
];

testValues.forEach(value => {
    console.log(`${JSON.stringify(value)} is ${TypeChecker.getDetailedType(value)}`);
});

// Validation helper functions
function validateInput(value, expectedType, fieldName) {
    const actualType = TypeChecker.getDetailedType(value);
    
    if (actualType !== expectedType) {
        throw new TypeError(
            `Expected ${fieldName} to be ${expectedType}, but got ${actualType}`
        );
    }
    
    return true;
}

function createUser(name, age, email) {
    validateInput(name, 'string', 'name');
    validateInput(age, 'number', 'age');
    validateInput(email, 'string', 'email');
    
    return { name, age, email };
}
```
</details>
<details>
<summary><strong>12. What is type coercion in JavaScript?</strong></summary>

**Answer:**
Type coercion is JavaScript's automatic conversion of values from one data type to another. It can be implicit (automatic) or explicit (manual). Understanding coercion is crucial for avoiding unexpected behavior in comparisons and operations.

**Example:**
```javascript
// Implicit type coercion examples

// String coercion
console.log("5" + 3);        // "53" (number 3 converted to string)
console.log("5" + true);     // "5true" (boolean converted to string)
console.log("5" + null);     // "5null" (null converted to string)
console.log("5" + undefined); // "5undefined"

// Numeric coercion
console.log("5" - 3);        // 2 (string "5" converted to number)
console.log("5" * 2);        // 10
console.log("5" / 2);        // 2.5
console.log("5" % 2);        // 1
console.log(+"5");           // 5 (unary + converts to number)
console.log(-"5");           // -5

// Boolean coercion
console.log(!!1);            // true
console.log(!!0);            // false
console.log(!!"hello");      // true
console.log(!!"");           // false
console.log(!!null);         // false
console.log(!!undefined);    // false

// Comparison coercion
console.log("5" == 5);       // true (string converted to number)
console.log(true == 1);      // true (boolean converted to number)
console.log(false == 0);     // true
console.log(null == undefined); // true (special case)
console.log("" == 0);        // true (empty string converted to 0)

// Complex coercion examples
console.log([] + []);        // "" (arrays converted to strings)
console.log([] + {});        // "[object Object]"
console.log({} + []);        // "[object Object]"
console.log({} + {});        // "[object Object][object Object]"

// Array to string coercion
console.log([1, 2, 3] + "");   // "1,2,3"
console.log([1] + [2]);        // "12"
console.log(["a", "b"] + "c"); // "a,bc"

// Explicit type coercion
const value = "123";

// To number
const num1 = Number(value);        // 123
const num2 = parseInt(value);      // 123
const num3 = parseFloat(value);    // 123
const num4 = +value;               // 123
const num5 = value * 1;            // 123

// To string
const str1 = String(123);          // "123"
const str2 = (123).toString();     // "123"
const str3 = 123 + "";             // "123"

// To boolean
const bool1 = Boolean(1);          // true
const bool2 = !!1;                 // true

// Falsy values in JavaScript
const falsyValues = [
    false,
    0,
    -0,
    0n,          // BigInt zero
    "",          // empty string
    null,
    undefined,
    NaN
];

falsyValues.forEach(value => {
    console.log(`${JSON.stringify(value)} is ${Boolean(value)}`);
});

// Truthy values (everything else)
const truthyValues = [
    true,
    1,
    -1,
    "0",         // string "0" is truthy!
    "false",     // string "false" is truthy!
    [],          // empty array is truthy!
    {},          // empty object is truthy!
    function(){} // functions are truthy
];

truthyValues.forEach(value => {
    console.log(`${JSON.stringify(value)} is ${Boolean(value)}`);
});

// Common coercion gotchas
console.log(0.1 + 0.2 === 0.3);     // false (floating point precision)
console.log("2" > "10");             // true (string comparison, not numeric)
console.log("2" > 10);               // false (string "2" converted to number)
console.log(null + 1);               // 1 (null converted to 0)
console.log(undefined + 1);          // NaN
console.log([] == 0);                // true (array converted to empty string, then to 0)
console.log([1] == 1);               // true
console.log([1, 2] == "1,2");        // true

// Safe comparison and coercion functions
function safeEquals(a, b) {
    return a === b;
}

function safeAdd(a, b) {
    if (typeof a !== 'number' || typeof b !== 'number') {
        throw new TypeError('Both arguments must be numbers');
    }
    return a + b;
}

function toNumber(value) {
    if (typeof value === 'number') return value;
    if (typeof value === 'string') {
        const num = Number(value);
        if (isNaN(num)) {
            throw new Error(`Cannot convert "${value}" to number`);
        }
        return num;
    }
    throw new Error(`Cannot convert ${typeof value} to number`);
}

// Object to primitive conversion
const obj = {
    valueOf() {
        console.log('valueOf called');
        return 42;
    },
    toString() {
        console.log('toString called');
        return 'Hello';
    }
};

console.log(obj + 1);    // "valueOf called", 43
console.log(obj + "");   // "valueOf called", "42"
console.log(String(obj)); // "toString called", "Hello"

// Best practices for avoiding coercion issues
const TypeSafe = {
    isEqual: (a, b) => a === b,
    
    concatenate: (...values) => {
        return values.map(v => String(v)).join('');
    },
    
    add: (...numbers) => {
        numbers.forEach(n => {
            if (typeof n !== 'number' || isNaN(n)) {
                throw new TypeError('All arguments must be valid numbers');
            }
        });
        return numbers.reduce((sum, num) => sum + num, 0);
    }
};
```
</details>
<details>
<summary><strong>13. What are JavaScript symbols and when would you use them?</strong></summary>

**Answer:**
Symbols are a primitive data type that creates unique identifiers. They're primarily used for creating private object properties, defining unique constants, and implementing well-known symbols for customizing object behavior.

**Example:**
```javascript
// Creating symbols
const sym1 = Symbol();
const sym2 = Symbol();
const sym3 = Symbol('description');

console.log(sym1 === sym2);  // false (each symbol is unique)
console.log(sym3.toString()); // "Symbol(description)"
console.log(sym3.description); // "description"

// Symbols as object properties
const user = {
    name: 'John',
    age: 30,
    [Symbol('id')]: 'user_123' // Private-like property
};

// Symbol properties are not enumerated
console.log(Object.keys(user));        // ['name', 'age']
console.log(Object.values(user));      // ['John', 30]
console.log(JSON.stringify(user));     // {"name":"John","age":30}

// Accessing symbol properties
const idSymbol = Symbol('id');
const userData = {
    name: 'Alice',
    [idSymbol]: 'alice_456'
};

console.log(userData[idSymbol]); // 'alice_456'
console.log(userData.idSymbol);  // undefined

// Global symbol registry
const globalSym1 = Symbol.for('app.id');
const globalSym2 = Symbol.for('app.id');

console.log(globalSym1 === globalSym2); // true (same symbol from registry)
console.log(Symbol.keyFor(globalSym1)); // 'app.id'

// Well-known symbols
class Collection {
    constructor(items) {
        this.items = items;
    }
    
    // Symbol.iterator makes object iterable
    *[Symbol.iterator]() {
        for (let item of this.items) {
            yield item;
        }
    }
    
    // Symbol.toStringTag customizes toString behavior
    get [Symbol.toStringTag]() {
        return 'Collection';
    }
    
    // Symbol.toPrimitive customizes type conversion
    [Symbol.toPrimitive](hint) {
        if (hint === 'number') {
            return this.items.length;
        }
        if (hint === 'string') {
            return this.items.join(', ');
        }
        return this.items;
    }
}

const myCollection = new Collection(['a', 'b', 'c']);

// Using Symbol.iterator
for (let item of myCollection) {
    console.log(item); // 'a', 'b', 'c'
}

// Using Symbol.toStringTag
console.log(Object.prototype.toString.call(myCollection)); // "[object Collection]"

// Using Symbol.toPrimitive
console.log(+myCollection);        // 3 (length)
console.log(String(myCollection)); // "a, b, c"

// Private properties using symbols
const _privateData = Symbol('privateData');

class BankAccount {
    constructor(balance) {
        this[_privateData] = {
            balance: balance,
            transactions: []
        };
    }
    
    getBalance() {
        return this[_privateData].balance;
    }
    
    deposit(amount) {
        this[_privateData].balance += amount;
        this[_privateData].transactions.push({
            type: 'deposit',
            amount,
            date: new Date()
        });
    }
    
    withdraw(amount) {
        if (amount <= this[_privateData].balance) {
            this[_privateData].balance -= amount;
            this[_privateData].transactions.push({
                type: 'withdrawal',
                amount,
                date: new Date()
            });
            return true;
        }
        return false;
    }
    
    getTransactions() {
        return [...this[_privateData].transactions]; // Return copy
    }
}

const account = new BankAccount(1000);
account.deposit(500);
account.withdraw(200);

console.log(account.getBalance()); // 1300
console.log(Object.keys(account)); // [] (no enumerable properties)

// Constants using symbols (better than strings)
const STATUS = {
    PENDING: Symbol('pending'),
    APPROVED: Symbol('approved'),
    REJECTED: Symbol('rejected')
};

class Application {
    constructor() {
        this.status = STATUS.PENDING;
    }
    
    approve() {
        this.status = STATUS.APPROVED;
    }
    
    reject() {
        this.status = STATUS.REJECTED;
    }
    
    getStatusName() {
        switch (this.status) {
            case STATUS.PENDING: return 'Pending';
            case STATUS.APPROVED: return 'Approved';
            case STATUS.REJECTED: return 'Rejected';
        }
    }
}

// Event system using symbols
const EVENT_TYPES = {
    USER_LOGIN: Symbol('userLogin'),
    USER_LOGOUT: Symbol('userLogout'),
    DATA_LOADED: Symbol('dataLoaded')
};

class EventEmitter {
    constructor() {
        this.listeners = new Map();
    }
    
    on(eventType, callback) {
        if (!this.listeners.has(eventType)) {
            this.listeners.set(eventType, []);
        }
        this.listeners.get(eventType).push(callback);
    }
    
    emit(eventType, data) {
        const callbacks = this.listeners.get(eventType);
        if (callbacks) {
            callbacks.forEach(callback => callback(data));
        }
    }
}

const emitter = new EventEmitter();

emitter.on(EVENT_TYPES.USER_LOGIN, (user) => {
    console.log(`User ${user.name} logged in`);
});

emitter.emit(EVENT_TYPES.USER_LOGIN, { name: 'John' });
```
</details>
<details>
<summary><strong>14. What is the difference between primitive and reference data types?</strong></summary>

**Answer:**
Primitive types (string, number, boolean, null, undefined, symbol, bigint) are stored by value and immutable. Reference types (objects, arrays, functions) are stored by reference and mutable. This affects how they're passed to functions and compared.

**Example:**
```javascript
// Primitive types are stored by value
let a = 5;
let b = a; // b gets a copy of a's value
a = 10;
console.log(a); // 10
console.log(b); // 5 (unchanged)

let str1 = "hello";
let str2 = str1; // str2 gets a copy of str1's value
str1 = "world";
console.log(str1); // "world"
console.log(str2); // "hello" (unchanged)

// Reference types are stored by reference
let obj1 = { name: "John", age: 30 };
let obj2 = obj1; // obj2 gets a reference to the same object
obj1.age = 31;
console.log(obj1.age); // 31
console.log(obj2.age); // 31 (same object, so both reflect the change)

let arr1 = [1, 2, 3];
let arr2 = arr1; // arr2 references the same array
arr1.push(4);
console.log(arr1); // [1, 2, 3, 4]
console.log(arr2); // [1, 2, 3, 4] (same array)

// Function parameters behavior
function modifyPrimitive(x) {
    x = 100;
    console.log("Inside function:", x); // 100
}

function modifyReference(obj) {
    obj.name = "Modified";
    console.log("Inside function:", obj.name); // "Modified"
}

function reassignReference(obj) {
    obj = { name: "New Object" }; // This doesn't affect the original
    console.log("Inside function:", obj.name); // "New Object"
}

let num = 50;
modifyPrimitive(num);
console.log("Outside function:", num); // 50 (unchanged)

let person = { name: "Original" };
modifyReference(person);
console.log("Outside function:", person.name); // "Modified"

let person2 = { name: "Original" };
reassignReference(person2);
console.log("Outside function:", person2.name); // "Original" (unchanged)

// Comparison behavior
// Primitives compared by value
console.log(5 === 5);         // true
console.log("hello" === "hello"); // true

// References compared by reference, not content
console.log({} === {});       // false (different objects)
console.log([] === []);       // false (different arrays)

let obj3 = { x: 1 };
let obj4 = { x: 1 };
let obj5 = obj3;

console.log(obj3 === obj4);   // false (different objects with same content)
console.log(obj3 === obj5);   // true (same reference)

// Creating copies
// Primitive copies are automatic
let original = 42;
let copy = original; // Creates a copy

// Reference types need explicit copying
let originalArray = [1, 2, 3];
let shallowCopy = [...originalArray]; // Shallow copy
let alsoShallowCopy = originalArray.slice(); // Another shallow copy

originalArray.push(4);
console.log(originalArray); // [1, 2, 3, 4]
console.log(shallowCopy);   // [1, 2, 3] (independent copy)

// Shallow vs Deep copying for objects
let originalObj = {
    name: "John",
    hobbies: ["reading", "coding"],
    address: {
        city: "New York",
        zip: "10001"
    }
};

// Shallow copy methods
let shallowCopyObj1 = { ...originalObj };
let shallowCopyObj2 = Object.assign({}, originalObj);

// Deep copy methods
let deepCopy1 = JSON.parse(JSON.stringify(originalObj)); // Limited method
// let deepCopy2 = structuredClone(originalObj); // Modern method (newer browsers)

// Testing shallow vs deep copy
originalObj.name = "Jane";           // Primitive property
originalObj.hobbies.push("gaming");  // Nested array
originalObj.address.city = "Boston"; // Nested object

console.log("Original:", originalObj);
console.log("Shallow copy:", shallowCopyObj1);
console.log("Deep copy:", deepCopy1);

// Utility functions for working with types
function isPrimitive(value) {
    return value !== Object(value);
}

function isReference(value) {
    return value === Object(value);
}

function deepEqual(obj1, obj2) {
    if (obj1 === obj2) return true;
    
    if (isPrimitive(obj1) || isPrimitive(obj2)) {
        return obj1 === obj2;
    }
    
    if (Array.isArray(obj1) !== Array.isArray(obj2)) {
        return false;
    }
    
    const keys1 = Object.keys(obj1);
    const keys2 = Object.keys(obj2);
    
    if (keys1.length !== keys2.length) {
        return false;
    }
    
    for (let key of keys1) {
        if (!keys2.includes(key)) return false;
        if (!deepEqual(obj1[key], obj2[key])) return false;
    }
    
    return true;
}

function deepClone(obj) {
    if (isPrimitive(obj)) return obj;
    
    if (Array.isArray(obj)) {
        return obj.map(item => deepClone(item));
    }
    
    if (obj instanceof Date) {
        return new Date(obj.getTime());
    }
    
    if (obj instanceof RegExp) {
        return new RegExp(obj);
    }
    
    const cloned = {};
    for (let key in obj) {
        if (obj.hasOwnProperty(key)) {
            cloned[key] = deepClone(obj[key]);
        }
    }
    
    return cloned;
}

// Testing the utilities
const testObj1 = { a: 1, b: { c: 2 } };
const testObj2 = { a: 1, b: { c: 2 } };
const testObj3 = testObj1;

console.log(deepEqual(testObj1, testObj2)); // true (same content)
console.log(testObj1 === testObj2);         // false (different references)
console.log(testObj1 === testObj3);         // true (same reference)

const cloned = deepClone(testObj1);
testObj1.b.c = 999;
console.log(testObj1.b.c); // 999
console.log(cloned.b.c);   // 2 (unaffected by deep clone)
```
</details>
<details>
<summary><strong>15. What is the temporal dead zone in JavaScript?</strong></summary>

**Answer:**
The Temporal Dead Zone (TDZ) is the period between entering a scope and the actual declaration of let and const variables. During this time, the variables exist but cannot be accessed, resulting in a ReferenceError if you try to use them.

**Example:**
```javascript
// Temporal Dead Zone demonstration
console.log(varVariable);   // undefined (hoisted and initialized)
// console.log(letVariable);   // ReferenceError: Cannot access 'letVariable' before initialization
// console.log(constVariable); // ReferenceError: Cannot access 'constVariable' before initialization

var varVariable = "I'm a var";
let letVariable = "I'm a let";
const constVariable = "I'm a const";

// Function scope vs block scope TDZ
function scopeExample() {
    console.log(x); // undefined (var is hoisted)
    // console.log(y); // ReferenceError (let is in TDZ)
    
    var x = 1;
    let y = 2;
    
    if (true) {
        // console.log(z); // ReferenceError (let is in TDZ within this block)
        let z = 3;
        console.log(z); // 3 (now accessible)
    }
}

// TDZ in loops
for (let i = 0; i < 3; i++) {
    setTimeout(() => {
        console.log(i); // 0, 1, 2 (each iteration has its own i)
    }, 100);
}

// Compare with var (no TDZ)
for (var j = 0; j < 3; j++) {
    setTimeout(() => {
        console.log(j); // 3, 3, 3 (all refer to the same j)
    }, 100);
}

// TDZ with function parameters and default values
function parameterTDZ(a = b, b = 2) {
    // ReferenceError: Cannot access 'b' before initialization
    return a + b;
}

// This works because b is initialized first
function parameterFixed(b = 2, a = b) {
    return a + b;
}

console.log(parameterFixed()); // 4

// TDZ with destructuring
function destructuringTDZ() {
    // These all throw ReferenceError due to TDZ
    // console.log(a);
    // console.log(b);
    // console.log(c);
    
    let [a, b] = [1, 2];
    let {c} = {c: 3};
    
    console.log(a, b, c); // 1, 2, 3
}

// Class declarations and TDZ
// console.log(MyClass); // ReferenceError: Cannot access 'MyClass' before initialization

class MyClass {
    constructor(name) {
        this.name = name;
    }
}

const instance = new MyClass("Test");

// Function declarations vs function expressions
// This works (function declarations are fully hoisted)
console.log(regularFunction()); // "I'm hoisted!"

function regularFunction() {
    return "I'm hoisted!";
}

// This doesn't work (function expressions have TDZ)
// console.log(functionExpression()); // ReferenceError

const functionExpression = function() {
    return "I'm not hoisted!";
};

// Arrow functions also have TDZ
// console.log(arrowFunction()); // ReferenceError

const arrowFunction = () => {
    return "I'm not hoisted either!";
};

// Block scoping and TDZ
function blockScopeExample() {
    // Variables are hoisted to block scope, but in TDZ
    {
        // console.log(blockVar); // ReferenceError
        let blockVar = "I'm block scoped";
        console.log(blockVar); // Works fine
    }
    
    // console.log(blockVar); // ReferenceError: blockVar is not defined
}

// Switch statement TDZ
function switchTDZ(option) {
    switch (option) {
        case 'a':
            let message = "Case A";
            break;
        case 'b':
            // console.log(message); // ReferenceError if case 'a' didn't run
            let message2 = "Case B"; // Can't use same name due to block scope
            break;
    }
}

// Better switch statement
function betterSwitchTDZ(option) {
    switch (option) {
        case 'a': {
            let message = "Case A";
            console.log(message);
            break;
        }
        case 'b': {
            let message = "Case B"; // Different block scope, so OK
            console.log(message);
            break;
        }
    }
}

// Best practices to avoid TDZ issues
function safePractices() {
    // 1. Declare variables at the top of their scope
    let userName;
    let userAge;
    const API_URL = "https://api.example.com";
    
    // 2. Initialize variables when declaring if possible
    let isLoading = false;
    let data = null;
    
    // 3. Use function declarations for functions you want to hoist
    function helper() {
        return "Available throughout the scope";
    }
    
    // 4. Group related declarations
    let x = 0, y = 0, z = 0;
    
    // Now use the variables
    userName = "John";
    userAge = 30;
    
    return helper();
}

// TDZ Best Practices Summary
const TDZBestPractices = {
    // 1. Always declare variables before using them
    declareFirst() {
        let result;
        result = computeValue();
        return result;
    },
    
    // 2. Use const by default, let when reassignment needed
    useConstByDefault() {
        const CONFIG = { timeout: 5000 };
        let currentStep = 1;
        
        currentStep = 2; // OK
    },
    
    // 3. Group variable declarations
    groupDeclarations() {
        const apiEndpoint = '/api/users';
        const defaultLimit = 10;
        
        let users = [];
        let loading = false;
        let error = null;
    },
    
    // 4. Initialize variables when possible
    initializeWhenPossible() {
        const startTime = Date.now();
        let endTime = null;
        
        // ... some operation ...
        
        endTime = Date.now();
        return endTime - startTime;
    }
};

function computeValue() {
    return 42;
}
```
</details>

***

## Functions and Scope 🔧

<details>
<summary><strong>16. What are arrow functions and how do they differ from regular functions?</strong></summary>

**Answer:**
Arrow functions are a concise way to write functions introduced in ES6. They differ from regular functions in several ways: they don't have their own `this` binding, cannot be used as constructors, don't have `arguments` object, and have implicit return for single expressions.

**Example:**
```javascript
// Regular function vs Arrow function syntax
// Regular function
function regularFunction(a, b) {
    return a + b;
}

// Arrow function
const arrowFunction = (a, b) => a + b;

// Multiple ways to write arrow functions
const singleParam = name => `Hello ${name}`;
const noParams = () => "Hello World";
const multipleParams = (a, b, c) => a + b + c;
const blockBody = (x, y) => {
    const sum = x + y;
    return sum * 2;
};

// 'this' binding differences
const obj = {
    name: 'MyObject',
    
    // Regular function - 'this' refers to the object
    regularMethod: function() {
        console.log('Regular:', this.name); // 'MyObject'
        
        setTimeout(function() {
            console.log('Regular timeout:', this.name); // undefined (or global object)
        }, 100);
    },
    
    // Arrow function - 'this' is lexically bound
    arrowMethod: () => {
        console.log('Arrow:', this.name); // undefined (this refers to global scope)
    },
    
    // Correct usage of arrow functions
    correctUsage: function() {
        console.log('Correct:', this.name); // 'MyObject'
        
        setTimeout(() => {
            console.log('Arrow timeout:', this.name); // 'MyObject' (inherits this)
        }, 100);
    }
};

obj.regularMethod();
obj.arrowMethod();
obj.correctUsage();

// Constructor differences
// Regular function can be used as constructor
function RegularConstructor(name) {
    this.name = name;
}

const instance1 = new RegularConstructor('John');
console.log(instance1.name); // 'John'

// Arrow function cannot be used as constructor
const ArrowConstructor = (name) => {
    this.name = name;
};

// const instance2 = new ArrowConstructor('Jane'); // TypeError: ArrowConstructor is not a constructor

// Arguments object
function regularWithArguments() {
    console.log('Regular arguments:', arguments); // Arguments object available
    return Array.from(arguments).reduce((sum, num) => sum + num, 0);
}

const arrowWithRest = (...args) => {
    console.log('Arrow rest:', args); // Array of arguments
    return args.reduce((sum, num) => sum + num, 0);
};

console.log(regularWithArguments(1, 2, 3, 4)); // 10
console.log(arrowWithRest(1, 2, 3, 4)); // 10

// Hoisting differences
// Regular functions are fully hoisted
console.log(hoistedFunction()); // Works! Returns "I'm hoisted"

function hoistedFunction() {
    return "I'm hoisted";
}

// Arrow functions are not hoisted (they're variables)
// console.log(notHoisted()); // ReferenceError: Cannot access 'notHoisted' before initialization

const notHoisted = () => "I'm not hoisted";

// Practical examples

// 1. Array methods with arrow functions
const numbers = [1, 2, 3, 4, 5];

// Clean and concise
const doubled = numbers.map(n => n * 2);
const evens = numbers.filter(n => n % 2 === 0);
const sum = numbers.reduce((acc, n) => acc + n, 0);

console.log(doubled); // [2, 4, 6, 8, 10]
console.log(evens);   // [2, 4]
console.log(sum);     // 15

// 2. Event handlers preserving context
class Counter {
    constructor() {
        this.count = 0;
        this.element = document.createElement('button');
        this.element.textContent = 'Count: 0';
        
        // Right way - preserves context
        this.element.addEventListener('click', () => this.increment());
    }
    
    increment() {
        this.count++;
        this.element.textContent = `Count: ${this.count}`;
    }
}

// 3. Promise chains
const fetchUserData = (userId) => {
    return fetch(`/api/users/${userId}`)
        .then(response => response.json()) // Arrow function in promise chain
        .then(user => ({ // Transform data
            ...user,
            displayName: `${user.firstName} ${user.lastName}`
        }))
        .catch(error => {
            console.error('Error fetching user:', error);
            return null;
        });
};

// 4. Async arrow functions
const fetchData = async (url) => {
    try {
        const response = await fetch(url);
        return await response.json();
    } catch (error) {
        console.error('Fetch error:', error);
        throw error;
    }
};

// 5. Higher-order functions
const createMultiplier = (multiplier) => (number) => number * multiplier;

const double = createMultiplier(2);
const triple = createMultiplier(3);

console.log(double(5)); // 10
console.log(triple(5)); // 15

// 6. Functional programming patterns
const users = [
    { name: 'John', age: 30, active: true },
    { name: 'Jane', age: 25, active: false },
    { name: 'Bob', age: 35, active: true }
];

const activeUserNames = users
    .filter(user => user.active)
    .map(user => user.name)
    .sort();

console.log(activeUserNames); // ['Bob', 'John']

// When NOT to use arrow functions

// 1. Object methods (when you need 'this')
const calculator = {
    value: 0,
    
    // Don't use arrow function here
    add: function(num) {
        this.value += num;
        return this;
    },
    
    // Or use method shorthand
    subtract(num) {
        this.value -= num;
        return this;
    }
};

// 2. Prototype methods
function Person(name) {
    this.name = name;
}

// Don't use arrow function for prototype methods
Person.prototype.greet = function() {
    return `Hello, I'm ${this.name}`;
};

// 3. Event handlers that need 'this' to refer to the element
// document.getElementById('myButton').addEventListener('click', function() {
//     this.style.backgroundColor = 'red'; // 'this' refers to the button
// });

// Best practices
const ArrowFunctionBestPractices = {
    // Use for short, single-expression functions
    shortFunctions: () => numbers.map(n => n * 2),
    
    // Use for functional programming
    functionalStyle: () => users.filter(u => u.active).map(u => u.name),
    
    // Use for preserving lexical 'this'
    preserveContext: function() {
        return new Promise((resolve) => {
            setTimeout(() => {
                resolve(this.value); // 'this' is preserved
            }, 100);
        });
    },
    
    // Don't use for object methods
    objectMethod: function() { // Use regular function
        return this.value;
    },
    
    // Use parentheses for clarity with single parameters
    clearSyntax: (param) => param * 2, // Better than param => param * 2
    
    // Use block body for complex logic
    complexLogic: (data) => {
        const processed = data.filter(item => item.valid);
        const transformed = processed.map(item => ({
            ...item,
            timestamp: Date.now()
        }));
        return transformed;
    }
};
```
</details>
<details>
<summary><strong>17. What are higher-order functions in JavaScript?</strong></summary>

**Answer:**
Higher-order functions are functions that either take other functions as arguments, return functions, or both. They enable functional programming patterns and code reusability. Common examples include map(), filter(), reduce(), and custom function builders.

**Example:**
```javascript
// Functions that take other functions as arguments

// Basic example with array methods
const numbers = [1, 2, 3, 4, 5];

// map() is a higher-order function
const doubled = numbers.map(function(num) {
    return num * 2;
});

// Using arrow functions for cleaner syntax
const tripled = numbers.map(num => num * 3);
const evens = numbers.filter(num => num % 2 === 0);
const sum = numbers.reduce((acc, num) => acc + num, 0);

console.log(doubled); // [2, 4, 6, 8, 10]
console.log(tripled); // [3, 6, 9, 12, 15]
console.log(evens);   // [2, 4]
console.log(sum);     // 15

// Custom higher-order functions
function forEach(array, callback) {
    for (let i = 0; i < array.length; i++) {
        callback(array[i], i, array);
    }
}

function map(array, callback) {
    const result = [];
    for (let i = 0; i < array.length; i++) {
        result.push(callback(array[i], i, array));
    }
    return result;
}

function filter(array, predicate) {
    const result = [];
    for (let i = 0; i < array.length; i++) {
        if (predicate(array[i], i, array)) {
            result.push(array[i]);
        }
    }
    return result;
}

// Using custom higher-order functions
const fruits = ['apple', 'banana', 'cherry'];

forEach(fruits, (fruit, index) => {
    console.log(`${index}: ${fruit}`);
});

const uppercased = map(fruits, fruit => fruit.toUpperCase());
const longFruits = filter(fruits, fruit => fruit.length > 5);

console.log(uppercased); // ['APPLE', 'BANANA', 'CHERRY']
console.log(longFruits); // ['banana', 'cherry']

// Functions that return other functions (closures)
function createMultiplier(multiplier) {
    return function(number) {
        return number * multiplier;
    };
}

const double = createMultiplier(2);
const triple = createMultiplier(3);

console.log(double(5)); // 10
console.log(triple(4)); // 12

// Function factories
function createValidator(validationRule) {
    return function(value) {
        return validationRule(value);
    };
}

const isEmail = createValidator(value => /\S+@\S+\.\S+/.test(value));
const isPhone = createValidator(value => /^\d{10}$/.test(value));
const isRequired = createValidator(value => value != null && value !== '');

console.log(isEmail('test@example.com')); // true
console.log(isPhone('1234567890'));       // true
console.log(isRequired(''));              // false

// Decorators (functions that enhance other functions)
function withLogging(fn) {
    return function(...args) {
        console.log(`Calling ${fn.name} with arguments:`, args);
        const result = fn.apply(this, args);
        console.log(`${fn.name} returned:`, result);
        return result;
    };
}

function withTiming(fn) {
    return function(...args) {
        const start = performance.now();
        const result = fn.apply(this, args);
        const end = performance.now();
        console.log(`${fn.name} took ${end - start} milliseconds`);
        return result;
    };
}

function withCaching(fn) {
    const cache = new Map();
    return function(...args) {
        const key = JSON.stringify(args);
        if (cache.has(key)) {
            console.log('Cache hit!');
            return cache.get(key);
        }
        const result = fn.apply(this, args);
        cache.set(key, result);
        return result;
    };
}

// Applying decorators
function fibonacci(n) {
    if (n <= 1) return n;
    return fibonacci(n - 1) + fibonacci(n - 2);
}

const loggedFibonacci = withLogging(fibonacci);
const timedFibonacci = withTiming(fibonacci);
const cachedFibonacci = withCaching(fibonacci);

// Composition of decorators
const enhancedFibonacci = withLogging(withTiming(withCaching(fibonacci)));

console.log(enhancedFibonacci(10)); // Will log, time, and cache

// Function composition
function compose(...functions) {
    return function(value) {
        return functions.reduceRight((acc, fn) => fn(acc), value);
    };
}

function pipe(...functions) {
    return function(value) {
        return functions.reduce((acc, fn) => fn(acc), value);
    };
}

// Example functions for composition
const addOne = x => x + 1;
const double = x => x * 2;
const square = x => x * x;

const composedFunction = compose(square, double, addOne);
const pipedFunction = pipe(addOne, double, square);

console.log(composedFunction(3)); // square(double(addOne(3))) = square(double(4)) = square(8) = 64
console.log(pipedFunction(3));    // square(double(addOne(3))) = square(double(4)) = square(8) = 64

// Practical examples

// 1. Event handling system
function createEventEmitter() {
    const events = {};
    
    return {
        on(event, callback) {
            if (!events[event]) {
                events[event] = [];
            }
            events[event].push(callback);
        },
        
        emit(event, data) {
            if (events[event]) {
                events[event].forEach(callback => callback(data));
            }
        },
        
        off(event, callback) {
            if (events[event]) {
                events[event] = events[event].filter(cb => cb !== callback);
            }
        }
    };
}

const emitter = createEventEmitter();

emitter.on('userLogin', user => console.log(`${user.name} logged in`));
emitter.on('userLogin', user => console.log(`Welcome ${user.name}!`));

emitter.emit('userLogin', { name: 'John' });

// 2. API request builder
function createApiClient(baseURL) {
    return function(endpoint) {
        return function(options = {}) {
            return fetch(`${baseURL}${endpoint}`, {
                method: 'GET',
                headers: {
                    'Content-Type': 'application/json',
                    ...options.headers
                },
                ...options
            }).then(response => response.json());
        };
    };
}

const apiClient = createApiClient('https://api.example.com');
const getUsers = apiClient('/users');
const createUser = apiClient('/users');

// Usage
getUsers().then(users => console.log(users));
createUser({ 
    method: 'POST', 
    body: JSON.stringify({ name: 'John' }) 
}).then(user => console.log(user));

// 3. Currying
function curry(fn) {
    return function curried(...args) {
        if (args.length >= fn.length) {
            return fn.apply(this, args);
        } else {
            return function(...args2) {
                return curried.apply(this, args.concat(args2));
            };
        }
    };
}

function add(a, b, c) {
    return a + b + c;
}

const curriedAdd = curry(add);

console.log(curriedAdd(1)(2)(3)); // 6
console.log(curriedAdd(1, 2)(3)); // 6
console.log(curriedAdd(1)(2, 3)); // 6

// 4. Partial application
function partial(fn, ...presetArgs) {
    return function partiallyApplied(...laterArgs) {
        return fn(...presetArgs, ...laterArgs);
    };
}

function greet(greeting, name, punctuation) {
    return `${greeting}, ${name}${punctuation}`;
}

const sayHello = partial(greet, 'Hello');
const sayHelloJohn = partial(greet, 'Hello', 'John');

console.log(sayHello('Alice', '!')); // "Hello, Alice!"
console.log(sayHelloJohn('?'));      // "Hello, John?"

// 5. Debouncing
function debounce(fn, delay) {
    let timeoutId;
    return function(...args) {
        clearTimeout(timeoutId);
        timeoutId = setTimeout(() => fn.apply(this, args), delay);
    };
}

const debouncedSearch = debounce((query) => {
    console.log(`Searching for: ${query}`);
}, 300);

// Usage in event handler
// input.addEventListener('input', (e) => debouncedSearch(e.target.value));

// 6. Throttling
function throttle(fn, limit) {
    let inThrottle;
    return function(...args) {
        if (!inThrottle) {
            fn.apply(this, args);
            inThrottle = true;
            setTimeout(() => inThrottle = false, limit);
        }
    };
}

const throttledScroll = throttle(() => {
    console.log('Scroll event handler');
}, 100);

// Usage
// window.addEventListener('scroll', throttledScroll);

// 7. Memoization
function memoize(fn) {
    const cache = new Map();
    
    return function(...args) {
        const key = JSON.stringify(args);
        
        if (cache.has(key)) {
            return cache.get(key);
        }
        
        const result = fn.apply(this, args);
        cache.set(key, result);
        return result;
    };
}

const memoizedFibonacci = memoize((n) => {
    console.log(`Computing fibonacci(${n})`);
    if (n <= 1) return n;
    return memoizedFibonacci(n - 1) + memoizedFibonacci(n - 2);
});

console.log(memoizedFibonacci(10)); // Will compute and cache intermediate values
console.log(memoizedFibonacci(10)); // Will use cached result
```
</details>
<details>
<summary><strong>18. What is scope and the scope chain in JavaScript?</strong></summary>

**Answer:**
Scope determines the accessibility of variables in different parts of the code. JavaScript has function scope, block scope (with let/const), and global scope. The scope chain is how JavaScript looks for variables, starting from the current scope and moving up to outer scopes.

**Example:**

```javascript
// Global scope
var globalVar = "I'm global";
let globalLet = "I'm also global";
const globalConst = "I'm global too";

function demonstrateScope() {
    // Function scope
    var functionScoped = "I'm function scoped";
    let functionLet = "I'm also function scoped";
    const functionConst = "I'm function scoped too";
    
    console.log(globalVar);    // Accessible
    console.log(globalLet);    // Accessible
    console.log(globalConst);  // Accessible
    
    if (true) {
        // Block scope
        var blockVar = "I'm still function scoped (var)";
        let blockLet = "I'm block scoped";
        const blockConst = "I'm also block scoped";
        
        console.log(functionScoped); // Accessible (parent scope)
        console.log(blockLet);       // Accessible (current scope)
        console.log(blockConst);     // Accessible (current scope)
    }
    
    console.log(blockVar);     // Accessible (var has function scope)
    // console.log(blockLet);     // ReferenceError (block scoped)
    // console.log(blockConst);   // ReferenceError (block scoped)
}

// Scope chain example
var outerVar = "outer";

function outerFunction() {
    var middleVar = "middle";
    
    function innerFunction() {
        var innerVar = "inner";
        
        // JavaScript looks for variables in this order:
        // 1. Current scope (innerFunction)
        // 2. Parent scope (outerFunction)
        // 3. Grandparent scope (global)
        console.log(innerVar);  // Found in current scope
        console.log(middleVar); // Found in parent scope
        console.log(outerVar);  // Found in global scope
        
        // Variable shadowing
        var outerVar = "shadowed"; // Shadows the global outerVar
        console.log(outerVar); // "shadowed" (local version)
    }
    
    innerFunction();
}

outerFunction();

// Lexical scope (scope is determined by where variables are declared)
function lexicalScopeExample() {
    var message = "Hello from lexical scope";
    
    function inner() {
        console.log(message); // Can access parent scope
    }
    
    return inner;
}

const innerFunc = lexicalScopeExample();
innerFunc(); // "Hello from lexical scope" - even though lexicalScopeExample has finished

// Closures and scope
function createCounter() {
    let count = 0; // Private variable in closure
    
    return {
        increment: function() {
            count++; // Accesses parent scope variable
            return count;
        },
        decrement: function() {
            count--; // Accesses same parent scope variable
            return count;
        },
        getCount: function() {
            return count; // Accesses same parent scope variable
        }
    };
}

const counter1 = createCounter();
const counter2 = createCounter();

console.log(counter1.increment()); // 1
console.log(counter1.increment()); // 2
console.log(counter2.increment()); // 1 (different closure, different count)
console.log(counter1.getCount()); // 2

// Module pattern using scope
const Calculator = (function() {
    // Private variables and functions (not accessible from outside)
    let result = 0;
    
    function log(operation, value) {
        console.log(`${operation}: ${value}`);
    }
    
    // Public interface (returned object)
    return {
        add: function(x) {
            result += x;
            log('Add', x);
            return this;
        },
        subtract: function(x) {
            result -= x;
            log('Subtract', x);
            return this;
        },
        multiply: function(x) {
            result *= x;
            log('Multiply', x);
            return this;
        },
        getResult: function() {
            return result;
        },
        reset: function() {
            result = 0;
            log('Reset', result);
            return this;
        }
    };
})();

// Usage
Calculator.add(10).subtract(3).multiply(2);
console.log(Calculator.getResult()); // 14

// Variable hoisting and scope
function hoistingAndScope() {
    console.log(x); // undefined (not ReferenceError)
    console.log(y); // ReferenceError: Cannot access 'y' before initialization
    console.log(z); // ReferenceError: Cannot access 'z' before initialization
    
    var x = 1;
    let y = 2;
    const z = 3;
    
    // Function hoisting
    console.log(hoistedFunc()); // "I'm hoisted!"
    
    function hoistedFunc() {
        return "I'm hoisted!";
    }
}

// Block scope with let and const
function blockScopeDemo() {
    // Loop with var (function scoped)
    for (var i = 0; i < 3; i++) {
        setTimeout(function() {
            console.log('var i:', i); // 3, 3, 3
        }, 100);
    }
    
    // Loop with let (block scoped)
    for (let j = 0; j < 3; j++) {
        setTimeout(function() {
            console.log('let j:', j); // 0, 1, 2
        }, 100);
    }
    
    console.log('i after loop:', i); // 3 (accessible)
    // console.log('j after loop:', j); // ReferenceError (not accessible)
}

// Scope and 'this' binding
const scopeAndThis = {
    name: 'ScopeObject',
    
    regularMethod: function() {
        console.log('Regular method this.name:', this.name); // 'ScopeObject'
        
        function innerFunction() {
            console.log('Inner function this.name:', this.name); // undefined (this = global/window)
        }
        
        const arrowFunction = () => {
            console.log('Arrow function this.name:', this.name); // 'ScopeObject' (inher```

