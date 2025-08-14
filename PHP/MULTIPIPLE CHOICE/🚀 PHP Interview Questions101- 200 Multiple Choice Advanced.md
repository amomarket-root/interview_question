
# 🚀 PHP Interview Questions - Advanced Level (101-200)

> **📚 Advanced PHP Concepts:** Deep dive into complex PHP features
> **⏱️ Test Duration:** Flexible
> **🎯 Total Questions:** 100 (Questions 101-200)

***

## 📖 How to Use This Document

1. **Read each question carefully** 📝
2. **Choose your answer** (A, B, C, or D) 🤔
3. **Click on the details section** to reveal the correct answer ✅
4. **Track your progress** and learn from explanations 📈

***

## 📋 Section 3: Advanced PHP Concepts \& Expert Level (Questions 101-200)

### 🔥 **Question 101:** What is the output of `var_dump(0 == "0abc");` in PHP?

**A)** `bool(false)`
**B)** `bool(true)`
**C)** `int(0)`
**D)** Error

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) bool(true)**

_Explanation: PHP converts "0abc" to integer 0 during comparison, making 0 == 0 true._

</details>

***

### 🔥 **Question 102:** Which magic method is called when a non-existent method is invoked?

**A)** `__call()`
**B)** `__callStatic()`
**C)** `__invoke()`
**D)** Both A and B

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: D) Both A and B**

_Explanation: __call() for instance methods, __callStatic() for static methods that don't exist._

</details>

***

### 🔥 **Question 103:** What does the `spaceship operator` (`<=>`) return?

**A)** -1, 0, or 1
**B)** true or false
**C)** 0 or 1
**D)** null or false

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) -1, 0, or 1**

_Explanation: Returns -1 if left is less, 0 if equal, 1 if left is greater than right operand._

</details>

***

### 🔥 **Question 104:** In which PHP version was the `null coalescing assignment operator` (`??=`) introduced?

**A)** PHP 7.0
**B)** PHP 7.2
**C)** PHP 7.4
**D)** PHP 8.0

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: C) PHP 7.4**

_Explanation: The ??= operator was introduced in PHP 7.4 for null coalescing assignment._

</details>

***

### 🔥 **Question 105:** What is the purpose of `WeakMap` class in PHP 8?

**A)** Creates weak references to objects
**B)** Maps with automatic garbage collection
**C)** Both A and B
**D)** Creates temporary arrays

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: C) Both A and B**

_Explanation: WeakMap holds weak references to objects as keys, allowing automatic garbage collection._

</details>

***

### 🔥 **Question 106:** Which function creates a deep copy of an array containing objects?

**A)** `array_copy()`
**B)** `serialize()` and `unserialize()`
**C)** `clone`
**D)** `array_merge()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) serialize() and unserialize()**

_Explanation: Serializing and unserializing creates a deep copy of complex data structures._

</details>

***

### 🔥 **Question 107:** What is the difference between `SplObjectStorage` and regular arrays?

**A)** No difference
**B)** SplObjectStorage uses objects as keys
**C)** SplObjectStorage is faster
**D)** SplObjectStorage is deprecated

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) SplObjectStorage uses objects as keys**

_Explanation: SplObjectStorage provides object-to-data mapping using objects as keys._

</details>

***

### 🔥 **Question 108:** Which method is used to implement custom serialization logic?

**A)** `__serialize()`
**B)** `__sleep()`
**C)** `__wakeup()`
**D)** Both A and B

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: D) Both A and B**

_Explanation: __serialize() (PHP 7.4+) and __sleep() control which properties are serialized._

</details>

***

### 🔥 **Question 109:** What does `ReflectionClass::newInstanceWithoutConstructor()` do?

**A)** Creates instance normally
**B)** Creates instance without calling constructor
**C)** Creates static instance
**D)** Creates abstract instance

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) Creates instance without calling constructor**

_Explanation: This method bypasses the constructor when creating class instances._

</details>

***

### 🔥 **Question 110:** Which SPL iterator allows modification during iteration?

**A)** `ArrayIterator`
**B)** `RecursiveIteratorIterator`
**C)** `CachingIterator`
**D)** `FilterIterator`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) ArrayIterator**

_Explanation: ArrayIterator allows safe modification of the underlying array during iteration._

</details>

***

### 🔥 **Question 111:** What is the purpose of `Fiber` class in PHP 8.1?

**A)** Network programming
**B)** Cooperative multitasking
**C)** File operations
**D)** String manipulation

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) Cooperative multitasking**

_Explanation: Fibers enable cooperative multitasking by allowing functions to be interrupted and resumed._

</details>

***

### 🔥 **Question 112:** Which operator has the highest precedence in PHP?

**A)** `++` and `--`
**B)** `new`
**C)** `clone`
**D)** `[]` (array access)

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: D) [] (array access)**

_Explanation: Array/object access operators have the highest precedence in PHP._

</details>

***

### 🔥 **Question 113:** What does `get_declared_classes()` return?

**A)** All defined classes
**B)** Only user-defined classes
**C)** Only built-in classes
**D)** Only loaded classes

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) All defined classes**

_Explanation: Returns an array of all currently declared classes (built-in and user-defined)._

</details>

***

### 🔥 **Question 114:** Which function can be used to check if a class uses a specific trait?

**A)** `class_uses()`
**B)** `trait_exists()`
**C)** `uses_trait()`
**D)** `has_trait()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) class_uses()**

_Explanation: class_uses() returns an array of traits used by a class._

</details>

***

### 🔥 **Question 115:** What is the maximum recursion depth for `json_decode()` by default?

**A)** 256
**B)** 512
**C)** 1024
**D)** Unlimited

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) 512**

_Explanation: The default maximum depth for json_decode() recursion is 512 levels._

</details>

***

### 🔥 **Question 116:** Which method must be implemented when creating a custom `SessionHandler`?

**A)** `open()`, `close()`, `read()`, `write()`
**B)** `destroy()`, `gc()`
**C)** Both A and B
**D)** Only `read()` and `write()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: C) Both A and B**

_Explanation: SessionHandlerInterface requires all six methods: open, close, read, write, destroy, gc._

</details>

***

### 🔥 **Question 117:** What does `memory_get_peak_usage()` return?

**A)** Current memory usage
**B)** Maximum memory used
**C)** Available memory
**D)** Memory limit

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) Maximum memory used**

_Explanation: Returns the peak memory usage during script execution._

</details>

***

### 🔥 **Question 118:** Which function can convert a callback to a `Closure` object?

**A)** `Closure::fromCallable()`
**B)** `create_closure()`
**C)** `callback_to_closure()`
**D)** `make_closure()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) Closure::fromCallable()**

_Explanation: This static method converts any valid callable to a Closure object._

</details>

***

### 🔥 **Question 119:** What is the purpose of `__debugInfo()` magic method?

**A)** Controls output of `var_dump()`
**B)** Controls output of `print_r()`
**C)** Controls debugging information
**D)** All of the above

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) Controls output of var_dump()**

_Explanation: __debugInfo() controls what properties are shown when var_dump() is called on an object._

</details>

***

### 🔥 **Question 120:** Which function returns the calling function's name?

**A)** `debug_backtrace()`
**B)** `__FUNCTION__`
**C)** `get_calling_function()`
**D)** Both A and B

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) debug_backtrace()**

_Explanation: debug_backtrace() provides the call stack; __FUNCTION__ gives current function name._

</details>

***

### 🔥 **Question 121:** What does `stream_context_create()` do?

**A)** Creates file streams
**B)** Creates HTTP contexts
**C)** Creates stream contexts with options
**D)** Creates database connections

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: C) Creates stream contexts with options**

_Explanation: Creates a context resource with options for stream operations like file_get_contents()._

</details>

***

### 🔥 **Question 122:** Which attribute controls the maximum execution time for specific functions?

**A)** `#[TimeLimit]`
**B)** There's no such attribute
**C)** `#[MaxTime]`
**D)** `#[ExecutionTime]`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) There's no such attribute**

_Explanation: PHP doesn't have a built-in attribute for function execution time limits._

</details>

***

### 🔥 **Question 123:** What is the difference between `get_class()` and `static::class`?

**A)** No difference
**B)** get_class() returns runtime class, static::class returns compile-time class
**C)** static::class is faster
**D)** get_class() is deprecated

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) get_class() returns runtime class, static::class returns compile-time class**

_Explanation: get_class() resolves at runtime, static::class resolves at compile time using late static binding._

</details>

***

### 🔥 **Question 124:** Which function can be used to measure memory usage of specific variables?

**A)** `memory_get_usage()`
**B)** `sizeof()`
**C)** `strlen()`
**D)** None of the above

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: D) None of the above**

_Explanation: PHP doesn't have a built-in function to measure memory usage of specific variables._

</details>

***

### 🔥 **Question 125:** What does `opcache_compile_file()` do?

**A)** Compiles PHP file without execution
**B)** Executes compiled file
**C)** Clears compiled file from cache
**D)** Checks if file is compiled

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) Compiles PHP file without execution**

_Explanation: Compiles and caches a PHP script without executing it._

</details>

***

### 🔥 **Question 126:** Which SPL exception is thrown when an invalid argument is passed?

**A)** `InvalidArgumentException`
**B)** `BadMethodCallException`
**C)** `LogicException`
**D)** `RuntimeException`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) InvalidArgumentException**

_Explanation: InvalidArgumentException is specifically for invalid function/method arguments._

</details>

***

### 🔥 **Question 127:** What is the purpose of `WeakReference` class?

**A)** Creates weak object references
**B)** Prevents circular references
**C)** Allows garbage collection
**D)** All of the above

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: D) All of the above**

_Explanation: WeakReference allows referencing objects without preventing garbage collection._

</details>

***

### 🔥 **Question 128:** Which function can execute code in a different namespace context?

**A)** `eval()`
**B)** There's no such function
**C)** `namespace_exec()`
**D)** `call_user_func()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) There's no such function**

_Explanation: PHP doesn't have a function to execute code in a different namespace context._

</details>

***

### 🔥 **Question 129:** What does `get_declared_interfaces()` return?

**A)** All defined interfaces
**B)** Only user-defined interfaces
**C)** Only built-in interfaces
**D)** Only implemented interfaces

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) All defined interfaces**

_Explanation: Returns an array of all currently declared interfaces._

</details>

***

### 🔥 **Question 130:** Which method allows binding a closure to a specific object and scope?

**A)** `Closure::bind()`
**B)** `Closure::bindTo()`
**C)** Both A and B
**D)** `Closure::attach()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: C) Both A and B**

_Explanation: Both methods bind closures to objects, with bind() being static and bindTo() being instance method._

</details>

***

### 🔥 **Question 131:** What is the maximum number of parameters a PHP function can have?

**A)** 1024
**B)** 2048
**C)** No practical limit
**D)** 256

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: C) No practical limit**

_Explanation: PHP doesn't impose a specific limit on function parameters (limited by available memory)._

</details>

***

### 🔥 **Question 132:** Which function can be used to get the line number where an error occurred?

**A)** `debug_backtrace()`
**B)** `__LINE__`
**C)** `error_get_last()`
**D)** All of the above

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: D) All of the above**

_Explanation: All can provide line numbers: debug_backtrace() for call stack, __LINE__ for current line, error_get_last() for last error._

</details>

***

### 🔥 **Question 133:** What does `stream_get_meta_data()` return?

**A)** Stream content
**B)** Stream metadata and headers
**C)** Stream size
**D)** Stream type

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) Stream metadata and headers**

_Explanation: Returns metadata about a stream including headers, wrapper data, and stream information._

</details>

***

### 🔥 **Question 134:** Which magic method is called when an object is treated as a string?

**A)** `__toString()`
**B)** `__string()`
**C)** `__convert()`
**D)** `__cast()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) __toString()**

_Explanation: __toString() is automatically called when an object is used in string context._

</details>

***

### 🔥 **Question 135:** What is the purpose of `ReflectionGenerator` class?

**A)** Reflects generator functions
**B)** Gets generator state
**C)** Inspects generator properties
**D)** All of the above

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: D) All of the above**

_Explanation: ReflectionGenerator provides information about generator function state and properties._

</details>

***

### 🔥 **Question 136:** Which function can modify the include path at runtime?

**A)** `set_include_path()`
**B)** `get_include_path()`
**C)** `ini_set('include_path')`
**D)** Both A and C

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: D) Both A and C**

_Explanation: Both set_include_path() and ini_set() can modify the include path during execution._

</details>

***

### 🔥 **Question 137:** What does `parse_str()` do when passed a URL query string?

**A)** Parses into array
**B)** Parses into variables
**C)** Both A and B
**D)** Returns string

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: C) Both A and B**

_Explanation: parse_str() can parse query strings into an array or extract variables into current scope._

</details>

***

### 🔥 **Question 138:** Which function returns the current working directory?

**A)** `pwd()`
**B)** `getcwd()`
**C)** `dirname(__FILE__)`
**D)** `__DIR__`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) getcwd()**

_Explanation: getcwd() returns the current working directory; __DIR__ returns directory of current file._

</details>

***

### 🔥 **Question 139:** What is the difference between `is_callable()` and `method_exists()`?

**A)** No difference
**B)** is_callable() checks if method can be called, method_exists() checks if method exists
**C)** method_exists() is faster
**D)** is_callable() is deprecated

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) is_callable() checks if method can be called, method_exists() checks if method exists**

_Explanation: is_callable() considers visibility and magic methods, method_exists() only checks existence._

</details>

***

### 🔥 **Question 140:** Which function can be used to get all defined constants?

**A)** `get_defined_constants()`
**B)** `get_constants()`
**C)** `list_constants()`
**D)** `show_constants()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) get_defined_constants()**

_Explanation: get_defined_constants() returns an array of all currently defined constants._

</details>

***

### 🔥 **Question 141:** What does `register_tick_function()` do?

**A)** Registers function to be called on each tick
**B)** Registers timer function
**C)** Registers clock function
**D)** Registers callback function

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) Registers function to be called on each tick**

_Explanation: Registers a function to be executed on each tick event (when declare(ticks=N) is used)._

</details>

***

### 🔥 **Question 142:** Which SPL class provides a doubly linked list implementation?

**A)** `SplDoublyLinkedList`
**B)** `SplQueue`
**C)** `SplStack`
**D)** All of the above

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: D) All of the above**

_Explanation: SplQueue and SplStack extend SplDoublyLinkedList, providing queue and stack implementations._

</details>

***

### 🔥 **Question 143:** What is the purpose of `highlight_string()` function?

**A)** Highlights PHP syntax
**B)** Highlights strings only
**C)** Creates syntax highlighting
**D)** Both A and C

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: D) Both A and C**

_Explanation: highlight_string() outputs PHP code with syntax highlighting using HTML tags._

</details>

***

### 🔥 **Question 144:** Which function can be used to get the size of a file without loading it into memory?

**A)** `filesize()`
**B)** `strlen()`
**C)** `sizeof()`
**D)** `memory_get_usage()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) filesize()**

_Explanation: filesize() returns the size of a file in bytes without reading the file content._

</details>

***

### 🔥 **Question 145:** What does `get_object_vars()` return for private properties?

**A)** All properties
**B)** Only public properties
**C)** Depends on calling context
**D)** Returns null

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: C) Depends on calling context**

_Explanation: Returns private properties only when called from within the same class context._

</details>

***

### 🔥 **Question 146:** Which function can create a temporary file that's automatically deleted?

**A)** `tmpfile()`
**B)** `tempnam()`
**C)** `mktemp()`
**D)** `create_temp()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) tmpfile()**

_Explanation: tmpfile() creates a temporary file that's automatically deleted when closed or script ends._

</details>

***

### 🔥 **Question 147:** What is the maximum size of a PHP array?

**A)** 2^31 elements
**B)** 2^32 elements
**C)** Limited by available memory
**D)** 1 million elements

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: C) Limited by available memory**

_Explanation: Array size is limited by available memory, not by a specific count limit._

</details>

***

### 🔥 **Question 148:** Which function can be used to compress data using gzip?

**A)** `gzencode()`
**B)** `gzcompress()`
**C)** `gzdeflate()`
**D)** All of the above

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) gzencode()**

_Explanation: gzencode() creates gzip-formatted compressed data; others use different compression formats._

</details>

***

### 🔥 **Question 149:** What does `php_sapi_name()` return?

**A)** PHP version
**B)** Server API type
**C)** Operating system
**D)** Web server type

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) Server API type**

_Explanation: Returns the type of interface between web server and PHP (cli, apache2handler, etc.)._

</details>

***

### 🔥 **Question 150:** Which function can be used to set a custom error handler for fatal errors?

**A)** `set_error_handler()`
**B)** `register_shutdown_function()`
**C)** `set_exception_handler()`
**D)** Both A and B

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) register_shutdown_function()**

_Explanation: Fatal errors can't be handled by set_error_handler(); use register_shutdown_function() to catch them._

</details>

***

### 🔥 **Question 151:** What is the purpose of `__halt_compiler()` function?

**A)** Stops PHP compilation at that point
**B)** Halts script execution
**C)** Stops function execution
**D)** Pauses execution

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) Stops PHP compilation at that point**

_Explanation: __halt_compiler() stops the compiler, allowing binary data after the call._

</details>

***

### 🔥 **Question 152:** Which function returns the number of bytes written to a stream?

**A)** `fwrite()`
**B)** `fputs()`
**C)** Both A and B
**D)** `fgetc()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: C) Both A and B**

_Explanation: Both fwrite() and fputs() (alias) return the number of bytes written._

</details>

***

### 🔥 **Question 153:** What does `get_extension_funcs()` return?

**A)** All PHP functions
**B)** Functions provided by a specific extension
**C)** User-defined functions
**D)** Built-in functions

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) Functions provided by a specific extension**

_Explanation: Returns an array of function names provided by the specified extension._

</details>

***

### 🔥 **Question 154:** Which magic method is called when `isset()` is used on inaccessible properties?

**A)** `__isset()`
**B)** `__get()`
**C)** `__exists()`
**D)** `__check()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) __isset()**

_Explanation: __isset() is triggered when isset() or empty() is called on inaccessible properties._

</details>

***

### 🔥 **Question 155:** What is the difference between `array_walk()` and `array_map()`?

**A)** No difference
**B)** array_walk() modifies original array, array_map() returns new array
**C)** array_map() is faster
**D)** array_walk() is deprecated

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) array_walk() modifies original array, array_map() returns new array**

_Explanation: array_walk() applies function to existing array, array_map() creates new array with results._

</details>

***

### 🔥 **Question 156:** Which function can be used to get the current include path?

**A)** `get_include_path()`
**B)** `ini_get('include_path')`
**C)** Both A and B
**D)** `show_include_path()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: C) Both A and B**

_Explanation: Both functions return the current include path setting._

</details>

***

### 🔥 **Question 157:** What does `stream_socket_server()` create?

**A)** TCP server socket
**B)** UDP server socket
**C)** Unix domain socket
**D)** All of the above

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: D) All of the above**

_Explanation: Can create various types of server sockets depending on the transport specified._

</details>

***

### 🔥 **Question 158:** Which function can be used to check if a resource is valid?

**A)** `is_resource()`
**B)** `get_resource_type()`
**C)** Both A and B
**D)** `resource_exists()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: C) Both A and B**

_Explanation: is_resource() checks if variable is a resource, get_resource_type() returns the type._

</details>

***

### 🔥 **Question 159:** What is the purpose of `ReflectionProperty::setAccessible()`?

**A)** Makes private properties accessible
**B)** Makes properties public
**C)** Sets property visibility
**D)** Enables property access

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) Makes private properties accessible**

_Explanation: Allows access to private/protected properties through reflection._

</details>

***

### 🔥 **Question 160:** Which function can measure the time taken by code execution with microsecond precision?

**A)** `microtime()`
**B)** `hrtime()`
**C)** Both A and B
**D)** `time()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: C) Both A and B**

_Explanation: Both provide high-precision timing, with hrtime() being more accurate._

</details>

***

### 🔥 **Question 161:** What does `get_loaded_extensions()` return?

**A)** All available extensions
**B)** Currently loaded extensions
**C)** Extension versions
**D)** Extension paths

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) Currently loaded extensions**

_Explanation: Returns an array of names of all currently loaded PHP extensions._

</details>

***

### 🔥 **Question 162:** Which SPL exception should be thrown when a method is not implemented?

**A)** `BadMethodCallException`
**B)** `LogicException`
**C)** `RuntimeException`
**D)** `InvalidArgumentException`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) BadMethodCallException**

_Explanation: BadMethodCallException is specifically for methods that are not implemented or not callable._

</details>

***

### 🔥 **Question 163:** What is the purpose of `SplFileInfo` class?

**A)** File system information
**B)** File content reading
**C)** File writing operations
**D)** File compression

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) File system information**

_Explanation: SplFileInfo provides an object-oriented interface for getting file/directory information._

</details>

***

### 🔥 **Question 164:** Which function can be used to create a hash of a file without loading it into memory?

**A)** `hash_file()`
**B)** `md5_file()`
**C)** `sha1_file()`
**D)** All of the above

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: D) All of the above**

_Explanation: All these functions can hash file contents directly without loading into memory._

</details>

***

### 🔥 **Question 165:** What does `get_parent_class()` return for a class with no parent?

**A)** `null`
**B)** `false`
**C)** Empty string
**D)** Exception

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) false**

_Explanation: get_parent_class() returns false when the class has no parent class._

</details>

***

### 🔥 **Question 166:** Which function can be used to change the current working directory?

**A)** `chdir()`
**B)** `cd()`
**C)** `setcwd()`
**D)** `changedir()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) chdir()**

_Explanation: chdir() changes the current directory to the specified path._

</details>

***

### 🔥 **Question 167:** What is the purpose of `DOMDocument::loadHTML()` vs `DOMDocument::loadXML()`?

**A)** No difference
**B)** loadHTML() is more tolerant of malformed markup
**C)** loadXML() is faster
**D)** loadHTML() only loads HTML5

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) loadHTML() is more tolerant of malformed markup**

_Explanation: loadHTML() can parse malformed HTML, while loadXML() requires well-formed XML._

</details>

***

### 🔥 **Question 168:** Which function can be used to send raw HTTP headers?

**A)** `header()`
**B)** `headers_sent()`
**C)** `http_response_code()`
**D)** Both A and C

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: D) Both A and C**

_Explanation: header() sends custom headers, http_response_code() sends response status codes._

</details>

***

### 🔥 **Question 169:** What does `ReflectionFunction::getNumberOfParameters()` return?

**A)** Required parameters only
**B)** Optional parameters only
**C)** Total number of parameters
**D)** Variable parameters

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: C) Total number of parameters**

_Explanation: Returns the total count of all parameters (required + optional) defined for the function._

</details>

***

### 🔥 **Question 170:** Which function can be used to check if output buffering is active?

**A)** `ob_get_level()`
**B)** `ob_get_status()`
**C)** Both A and B
**D)** `is_ob_active()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: C) Both A and B**

_Explanation: ob_get_level() returns nesting level, ob_get_status() returns detailed status information._

</details>

***

### 🔥 **Question 171:** What is the difference between `require` and `include` regarding return values?

**A)** No difference
**B)** require can't return values, include can
**C)** Both can return values
**D)** require returns boolean

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: C) Both can return values**

_Explanation: Both require and include can return values from the included file using return statement._

</details>

***

### 🔥 **Question 172:** Which function can be used to get the MIME type of a file?

**A)** `mime_content_type()`
**B)** `finfo_file()`
**C)** Both A and B
**D)** `get_mime_type()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: C) Both A and B**

_Explanation: Both functions can detect MIME types, with finfo_file() being the preferred modern approach._

</details>

***

### 🔥 **Question 173:** What does `serialize()` do with private properties?

**A)** Ignores them
**B)** Includes them with name mangling
**C)** Throws exception
**D)** Converts to public

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) Includes them with name mangling**

_Explanation: Private properties are serialized with class name prepended to property name._

</details>

***

### 🔥 **Question 174:** Which function can be used to check if a class implements a specific interface?

**A)** `class_implements()`
**B)** `implements_interface()`
**C)** `has_interface()`
**D)** `interface_exists()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) class_implements()**

_Explanation: class_implements() returns an array of interfaces implemented by a class._

</details>

***

### 🔥 **Question 175:** What is the purpose of `SplSubject` and `SplObserver` interfaces?

**A)** Implements Observer pattern
**B)** Implements Strategy pattern
**C)** Implements Factory pattern
**D)** Implements Singleton pattern

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) Implements Observer pattern**

_Explanation: These interfaces provide a standard implementation of the Observer design pattern._

</details>

***

### 🔥 **Question 176:** Which function can convert binary data to hexadecimal representation?

**A)** `bin2hex()`
**B)** `hex2bin()`
**C)** `base64_encode()`
**D)** `urlencode()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) bin2hex()**

_Explanation: bin2hex() converts binary data to hexadecimal string representation._

</details>

***

### 🔥 **Question 177:** What does `get_magic_quotes_gpc()` check for?

**A)** Current magic quotes setting
**B)** Nothing (deprecated function)
**C)** Quote escaping status
**D)** String encoding

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) Nothing (deprecated function)**

_Explanation: Magic quotes were deprecated in PHP 5.3 and removed in PHP 5.4._

</details>

***

### 🔥 **Question 178:** Which function can be used to get the last modification time of a file?

**A)** `filemtime()`
**B)** `filectime()`
**C)** `fileatime()`
**D)** All return different times

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) filemtime()**

_Explanation: filemtime() returns last modification time; filectime() returns inode change time; fileatime() returns last access time._

</details>

***

### 🔥 **Question 179:** What is the purpose of `stream_filter_register()`?

**A)** Registers custom stream filters
**B)** Filters stream content
**C)** Registers stream wrappers
**D)** Creates stream contexts

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) Registers custom stream filters**

_Explanation: Allows registration of custom stream filters that can be applied to streams._

</details>

***

### 🔥 **Question 180:** Which function can be used to get detailed information about the last JSON error?

**A)** `json_last_error()`
**B)** `json_last_error_msg()`
**C)** Both A and B
**D)** `json_get_error()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: C) Both A and B**

_Explanation: json_last_error() returns error code, json_last_error_msg() returns human-readable error message._

</details>

***

### 🔥 **Question 181:** What does `get_class_methods()` return for abstract methods?

**A)** Excludes abstract methods
**B)** Includes abstract methods
**C)** Returns only abstract methods
**D)** Throws exception

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) Includes abstract methods**

_Explanation: get_class_methods() returns all methods including abstract ones._

</details>

***

### 🔥 **Question 182:** Which function can be used to check if a stream supports seeking?

**A)** `stream_get_meta_data()`
**B)** `fseek()`
**C)** `stream_supports_seek()`
**D)** Both A and B

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) stream_get_meta_data()**

_Explanation: Check the 'seekable' key in the metadata array to determine if stream supports seeking._

</details>

***

### 🔥 **Question 183:** What is the purpose of `__clone()` magic method?

**A)** Called automatically when cloning objects
**B)** Creates object copies
**C)** Handles clone operations
**D)** All of the above

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) Called automatically when cloning objects**

_Explanation: __clone() is automatically called when an object is cloned, allowing custom clone behavior._

</details>

***

### 🔥 **Question 184:** Which function can be used to get the number of affected rows in a MySQL query?

**A)** `mysql_affected_rows()`
**B)** `mysqli_affected_rows()`
**C)** `PDOStatement::rowCount()`
**D)** Both B and C

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: D) Both B and C**

_Explanation: mysqli_affected_rows() and PDOStatement::rowCount() are modern ways (mysql_* is deprecated)._

</details>

***

### 🔥 **Question 185:** What does `array_column()` extract from a multidimensional array?

**A)** A single column
**B)** Multiple columns
**C)** Array keys
**D)** Array values

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) A single column**

_Explanation: array_column() extracts a single column of values from a multidimensional array._

</details>

***

### 🔥 **Question 186:** Which function can be used to register a function to be called on script shutdown?

**A)** `register_shutdown_function()`
**B)** `shutdown_register()`
**C)** `on_shutdown()`
**D)** `exit_handler()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) register_shutdown_function()**

_Explanation: Registers a function to be executed when script execution finishes._

</details>

***

### 🔥 **Question 187:** What is the difference between `array_key_exists()` and `isset()`?

**A)** No difference
**B)** array_key_exists() checks for key existence, isset() checks if value is not null
**C)** isset() is faster
**D)** array_key_exists() is deprecated

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) array_key_exists() checks for key existence, isset() checks if value is not null**

_Explanation: array_key_exists() returns true even if value is null, isset() returns false for null values._

</details>

***

### 🔥 **Question 188:** Which function can create a stream context with specific options?

**A)** `stream_context_create()`
**B)** `stream_context_set_option()`
**C)** Both A and B
**D)** `create_context()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: C) Both A and B**

_Explanation: stream_context_create() creates context with options, stream_context_set_option() modifies existing context._

</details>

***

### 🔥 **Question 189:** What does `ReflectionClass::getInterfaces()` return?

**A)** Interface names as strings
**B)** ReflectionClass objects for interfaces
**C)** Interface methods
**D)** Boolean if implements interfaces

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) ReflectionClass objects for interfaces**

_Explanation: Returns an array of ReflectionClass objects representing the interfaces._

</details>

***

### 🔥 **Question 190:** Which function can be used to get the current script's filename?

**A)** `__FILE__`
**B)** `basename(__FILE__)`
**C)** `$_SERVER['SCRIPT_NAME']`
**D)** All of the above

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: D) All of the above**

_Explanation: Different ways to get script filename: __FILE__ (full path), basename() (filename only), $_SERVER (web context)._

</details>

***

### 🔥 **Question 191:** What is the purpose of `SplPriorityQueue` class?

**A)** Implements priority queue data structure
**B)** Sorts arrays by priority
**C)** Manages task priorities
**D)** Creates priority lists

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) Implements priority queue data structure**

_Explanation: SplPriorityQueue provides a priority queue implementation where elements are extracted in priority order._

</details>

***

### 🔥 **Question 192:** Which function can be used to convert a string to a specific encoding?

**A)** `iconv()`
**B)** `mb_convert_encoding()`
**C)** Both A and B
**D)** `utf8_encode()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: C) Both A and B**

_Explanation: Both iconv() and mb_convert_encoding() can convert string encodings._

</details>

***

### 🔥 **Question 193:** What does `get_class_vars()` return when called from outside a class?

**A)** All properties
**B)** Only public properties
**C)** Only static properties
**D)** Returns null

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) Only public properties**

_Explanation: get_class_vars() only returns properties visible from the calling scope._

</details>

***

### 🔥 **Question 194:** Which function can be used to check if a file or directory is writable?

**A)** `is_writable()`
**B)** `is_writeable()`
**C)** Both A and B
**D)** `can_write()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: C) Both A and B**

_Explanation: is_writeable() is an alias for is_writable(); both check write permissions._

</details>

***

### 🔥 **Question 195:** What is the purpose of `__autoload()` function?

**A)** Automatically loads classes
**B)** Deprecated in favor of spl_autoload_register()
**C)** Both A and B
**D)** Loads configuration files

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: C) Both A and B**

_Explanation: __autoload() was used for automatic class loading but is deprecated; use spl_autoload_register() instead._

</details>

***

### 🔥 **Question 196:** Which function can be used to remove whitespace and specific characters from the end of a string?

**A)** `rtrim()`
**B)** `trim()`
**C)** `chop()`
**D)** Both A and C

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: D) Both A and C**

_Explanation: rtrim() removes from right side; chop() is an alias for rtrim()._

</details>

***

### 🔥 **Question 197:** What does `ReflectionMethod::getModifiers()` return?

**A)** Method visibility as string
**B)** Numeric bitmask of modifiers
**C)** Array of modifier names
**D)** Boolean for each modifier

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) Numeric bitmask of modifiers**

_Explanation: Returns a bitmask of modifiers (public, private, static, etc.) that can be checked with Reflection::getModifierNames()._

</details>

***

### 🔥 **Question 198:** Which function can be used to generate cryptographically secure random bytes?

**A)** `random_bytes()`
**B)** `openssl_random_pseudo_bytes()`
**C)** Both A and B
**D)** `rand()`

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: C) Both A and B**

_Explanation: Both provide cryptographically secure random data, with random_bytes() being the preferred modern approach._

</details>

***

### 🔥 **Question 199:** What is the difference between `parse_url()` and `pathinfo()`?

**A)** No difference
**B)** parse_url() parses URLs, pathinfo() parses file paths
**C)** pathinfo() is faster
**D)** parse_url() is deprecated

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: B) parse_url() parses URLs, pathinfo() parses file paths**

_Explanation: parse_url() breaks down URLs into components, pathinfo() provides information about file paths._

</details>

***

### 🔥 **Question 200:** Which feature makes PHP 8's JIT (Just-In-Time) compiler most effective?

**A)** CPU-intensive mathematical operations
**B)** Database operations
**C)** File I/O operations
**D)** String manipulations

<details>
<summary>🎯 Click to see the answer</summary>

**✅ Correct Answer: A) CPU-intensive mathematical operations**

_Explanation: JIT compiler provides the most benefit for CPU-intensive code with mathematical computations and loops._

</details>

***

## 🏆 Congratulations!

You've completed all 100 advanced PHP interview questions (101-200)!

### 📊 **Advanced Level Scoring Guide:**

- **90-100 correct:** 🌟 **PHP Expert/Architect** - You have mastery-level knowledge!
- **80-89 correct:** 🔥 **Senior PHP Developer** - Strong advanced concepts understanding!
- **70-79 correct:** 💪 **Mid-Level Developer** - Good grasp of advanced features!
- **60-69 correct:** 📚 **Junior-Mid Level** - Continue studying advanced concepts!
- **Below 60:** 🎯 **Review Advanced Topics** - Focus on complex PHP features!


### 🎯 **Advanced PHP Mastery Tips:**

1. **Master PHP internals** and memory management
2. **Understand design patterns** and their PHP implementations
3. **Practice with SPL classes** and advanced data structures
4. **Learn about PHP extensions** and C API
5. **Stay current** with latest PHP RFCs and features
6. **Contribute to open source** PHP projects
7. **Understand performance optimization** techniques

### 📚 **Next Level Learning:**

- **PHP Internals Book:** Deep dive into how PHP works
- **PHP RFCs:** Stay updated with language evolution
- **Xdebug Profiling:** Master performance analysis
- **OPcache Configuration:** Optimize production performance
- **PHP Extensions Development:** Learn C API basics

***

**🚀 You're now ready for expert-level PHP challenges!**

> _Advanced PHP mastery unlocked - Keep pushing the boundaries! 🎓_

<div style="text-align: center">⁂</div>

[^1]: paste.txt

