<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# 🚀 50 Most Asked PHP Interview Questions \& Answers

## 📋 Table of Contents

| \# | Question Category | Quick Link |
| :-- | :-- | :-- |
| [1-10](#basic-php-concepts-) | Basic PHP Concepts | Essential for beginners |
| [11-20](#syntax-and-operators-) | Syntax and Operators | Core language features |
| [21-30](#functions-and-control-structures-) | Functions \& Control Structures | Programming fundamentals |
| [31-40](#object-oriented-programming-) | Object-Oriented Programming | Advanced PHP concepts |
| [41-50](#security-and-best-practices-) | Security \& Best Practices | Professional development |


***

## Basic PHP Concepts 🔰

<details>
<summary><strong>1. What is PHP and what does it stand for?</strong></summary>

**Answer:**
PHP originally meant "Personal Home Page" but now stands for "PHP: Hypertext Preprocessor" (recursive acronym). It's a server-side scripting language designed specifically for web development. PHP runs on the server and generates HTML or other content that's sent to the client's browser.[^1]

**Example:**
```php
<?php
echo "Hello, World! Today is " . date('Y-m-d');
?>
```
</details>
<details>
<summary><strong>2. What are the different data types in PHP?</strong></summary>

**Answer:**
PHP supports eight primary data types:[^2]
- **String** - text data
- **Integer** - whole numbers
- **Float/Double** - decimal numbers  
- **Boolean** - true/false
- **Array** - ordered maps
- **Object** - instances of classes
- **NULL** - represents no value
- **Resource** - references to external resources

**Example:**
```php
<?php
$string = "Hello World";        // String
$integer = 42;                  // Integer
$float = 3.14;                  // Float
$boolean = true;                // Boolean
$array = [1, 2, 3];            // Array
$null_var = null;              // NULL
?>
```
</details>
<details>
<summary><strong>3. What is the difference between echo and print?</strong></summary>

**Answer:**
Both `echo` and `print` output data, but they have key differences:[^2]
- **echo**: Can take multiple parameters, has no return value, slightly faster
- **print**: Takes only one parameter, returns 1 (can be used in expressions)

**Example:**
```php
<?php
// echo - multiple parameters
echo "Hello", " ", "World";

// print - single parameter, returns 1
$result = print "Hello World";
echo $result; // Outputs: 1
?>
```
</details>
<details>
<summary><strong>4. How do you declare variables in PHP?</strong></summary>

**Answer:**
Variables in PHP start with a dollar sign ($) followed by the variable name. Variable names must start with a letter or underscore, followed by letters, numbers, or underscores. They are case-sensitive.[^1]

**Example:**
```php
<?php
$name = "John";           // String variable
$age = 25;               // Integer variable
$_private = "hidden";    // Starting with underscore
$userName = "john123";   // CamelCase naming

// Variables are case-sensitive
$Name = "Jane";          // Different from $name
?>
```
</details>
<details>
<summary><strong>5. What are PHP superglobals?</strong></summary>

**Answer:**
Superglobals are built-in variables that are available in all scopes throughout the script. They can be accessed from any function, class, or file without using the `global` keyword.[^2]

**Example:**
```php
<?php
// Common superglobals
$_GET     // Data from URL parameters
$_POST    // Data from forms
$_SESSION // Session data
$_COOKIE  // Cookie values
$_SERVER  // Server information
$_FILES   // File upload information
$_REQUEST // Combination of GET, POST, and COOKIE
$GLOBALS  // References all variables in global scope

// Usage example
function displayUserInfo() {
    echo "User: " . $_SESSION['username'];
    echo "IP: " . $_SERVER['REMOTE_ADDR'];
}
?>
```
</details>
<details>
<summary><strong>6. What is the difference between include and require?</strong></summary>

**Answer:**
Both include files in PHP, but handle errors differently:[^2]
- **include**: Shows warning if file not found, continues execution
- **require**: Shows fatal error if file not found, stops execution
- **include_once/require_once**: Ensures file is included only once

**Example:**
```php
<?php
// include - continues if file missing
include 'header.php';
echo "This will still execute even if header.php is missing";

// require - stops if file missing
require 'config.php';
echo "This won't execute if config.php is missing";

// include_once - includes only once
include_once 'functions.php';
include_once 'functions.php'; // Won't include again
?>
```
</details>
<details>
<summary><strong>7. How do you define constants in PHP?</strong></summary>

**Answer:**
Constants are defined using the `define()` function. Once defined, they cannot be changed or undefined. Constants don't use the $ sign and are accessible from anywhere in the script.[^1]

**Example:**
```php
<?php
// Define constants
define("SITE_NAME", "My Website");
define("VERSION", "1.0.0");
define("DEBUG", true);

// Using constants
echo SITE_NAME;
echo "Version: " . VERSION;

// Magic constants (predefined)
echo __FILE__;      // Current file path
echo __LINE__;      // Current line number
echo __DIR__;       // Current directory
?>
```
</details>
<details>
<summary><strong>8. What are PHP magic constants?</strong></summary>

**Answer:**
Magic constants are special predefined constants that change depending on where they are used. They start and end with double underscores and provide information about the current context.[^2]

**Example:**
```php
<?php
class MyClass {
    public function showInfo() {
        echo "File: " . __FILE__ . "\n";
        echo "Line: " . __LINE__ . "\n";
        echo "Directory: " . __DIR__ . "\n";
        echo "Function: " . __FUNCTION__ . "\n";
        echo "Class: " . __CLASS__ . "\n";
        echo "Method: " . __METHOD__ . "\n";
    }
}

$obj = new MyClass();
$obj->showInfo();
?>
```
</details>
<details>
<summary><strong>9. How does PHP handle sessions?</strong></summary>

**Answer:**
Sessions allow data to be stored across multiple pages for a single user. PHP creates a unique session ID and stores it in a cookie on the user's browser. Session data is stored on the server.[^2]

**Example:**
```php
<?php
// Start session
session_start();

// Store data in session
$_SESSION['username'] = 'john';
$_SESSION['user_id'] = 123;

// Access session data
echo "Welcome " . $_SESSION['username'];

// Check if session variable exists
if (isset($_SESSION['username'])) {
    echo "User is logged in";
}

// Destroy session
session_destroy();
?>
```
</details>
<details>
<summary><strong>10. What is the difference between cookies and sessions?</strong></summary>

**Answer:**
Cookies and sessions both store user data but in different ways:[^2]

| Feature | Cookies | Sessions |
|---------|---------|----------|
| Storage Location | Client browser | Server |
| Security | Less secure | More secure |
| Data Size | Limited (4KB) | No limit |
| Expiration | Set expiration time | Expires when browser closes |

**Example:**
```php
<?php
// Setting a cookie
setcookie("user_preference", "dark_theme", time() + 3600);

// Reading a cookie
if (isset($_COOKIE['user_preference'])) {
    echo $_COOKIE['user_preference'];
}

// Session alternative
session_start();
$_SESSION['user_preference'] = 'dark_theme';
echo $_SESSION['user_preference'];
?>
```
</details>

***

## Syntax and Operators ⚙️

<details>
<summary><strong>11. What is the difference between == and === operators?</strong></summary>

**Answer:**
These are comparison operators with different levels of strictness:[^1]
- **==** (loose comparison): Compares values only, allows type conversion
- **===** (strict comparison): Compares both value and data type

**Example:**
```php
<?php
$a = 5;
$b = "5";

var_dump($a == $b);   // bool(true) - values are equal
var_dump($a === $b);  // bool(false) - types are different

// More examples
var_dump(0 == false);    // true
var_dump(0 === false);   // false
var_dump(null == "");    // true  
var_dump(null === "");   // false
?>
```
</details>
<details>
<summary><strong>12. How do you handle errors in PHP?</strong></summary>

**Answer:**
PHP has different error types and handling mechanisms. You can use error reporting, custom error handlers, and exception handling to manage errors effectively.[^1]

**Example:**
```php
<?php
// Error reporting
error_reporting(E_ALL);
ini_set('display_errors', 1);

// Custom error handler
function customError($errno, $errstr, $errfile, $errline) {
    echo "Error [$errno]: $errstr in $errfile on line $errline";
}
set_error_handler("customError");

// Exception handling
try {
    throw new Exception("Something went wrong!");
} catch (Exception $e) {
    echo "Caught exception: " . $e->getMessage();
} finally {
    echo "This always executes";
}
?>
```
</details>
<details>
<summary><strong>13. What are the main types of errors in PHP?</strong></summary>

**Answer:**
PHP has four main error types:[^1]
- **Parse/Syntax Error**: Code syntax issues
- **Fatal Error**: Critical errors that stop execution
- **Warning**: Non-critical issues that don't stop execution
- **Notice**: Minor issues like undefined variables

**Example:**
```php
<?php
// Parse Error (syntax error)
// echo "Hello World" // Missing semicolon

// Fatal Error
// $obj->method(); // Calling method on non-object

// Warning
// include 'nonexistent.php'; // File doesn't exist

// Notice
// echo $undefined_variable; // Variable not defined

// Proper error handling
if (isset($variable)) {
    echo $variable;
} else {
    echo "Variable not set";
}
?>
```
</details>
<details>
<summary><strong>14. How do you work with strings in PHP?</strong></summary>

**Answer:**
PHP provides many built-in functions for string manipulation. Strings can be created with single quotes, double quotes, or heredoc/nowdoc syntax.[^1]

**Example:**
```php
<?php
$string = "Hello World";

// String functions
echo strlen($string);              // Length: 11
echo strtoupper($string);          // HELLO WORLD
echo strtolower($string);          // hello world
echo substr($string, 0, 5);       // Hello
echo str_replace("World", "PHP", $string); // Hello PHP

// String concatenation
$first = "Hello";
$second = "World";
echo $first . " " . $second;       // Hello World

// Variable interpolation (double quotes only)
$name = "John";
echo "Hello $name";                // Hello John
echo "Hello {$name}";              // Hello John
?>
```
</details>
<details>
<summary><strong>15. How do you work with arrays in PHP?</strong></summary>

**Answer:**
PHP supports both indexed arrays (numeric keys) and associative arrays (string keys). Arrays are very flexible and can hold mixed data types.[^2]

**Example:**
```php
<?php
// Indexed array
$fruits = ["apple", "banana", "orange"];
$colors = array("red", "green", "blue");

// Associative array
$person = [
    "name" => "John",
    "age" => 30,
    "city" => "New York"
];

// Array functions
echo count($fruits);                    // 3
array_push($fruits, "grape");          // Add element
print_r($fruits);

// Loop through array
foreach ($person as $key => $value) {
    echo "$key: $value\n";
}

// Multidimensional array
$users = [
    ["name" => "John", "age" => 30],
    ["name" => "Jane", "age" => 25]
];
?>
```
</details>
<details>
<summary><strong>16. What is the difference between array_push() and $array[] = ?</strong></summary>

**Answer:**
Both add elements to arrays, but they work differently:[^2]
- **array_push()**: Function that can add multiple elements, returns new count
- **$array[]**: Direct assignment, slightly faster for single elements

**Example:**
```php
<?php
$fruits = ["apple", "banana"];

// Using array_push
$count = array_push($fruits, "orange", "grape");
echo "New count: $count\n";

// Using direct assignment
$colors = ["red", "green"];
$colors[] = "blue";
$colors[] = "yellow";

// Performance comparison for single element
$array1 = [];
$array2 = [];

// Faster for single elements
for ($i = 0; $i < 1000; $i++) {
    $array1[] = $i;
}

// Slightly slower but more explicit
for ($i = 0; $i < 1000; $i++) {
    array_push($array2, $i);
}
?>
```
</details>
<details>
<summary><strong>17. How do you validate form data in PHP?</strong></summary>

**Answer:**
Form validation involves checking user input for correctness and security. You should validate on both client and server side, but never trust client-side validation alone.[^1]

**Example:**
```php
<?php
if ($_POST) {
    $name = trim($_POST['name']);
    $email = trim($_POST['email']);
    $age = (int)$_POST['age'];
    
    $errors = [];
    
    // Validate name
    if (empty($name)) {
        $errors[] = "Name is required";
    } elseif (strlen($name) < 2) {
        $errors[] = "Name must be at least 2 characters";
    }
    
    // Validate email
    if (empty($email)) {
        $errors[] = "Email is required";
    } elseif (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
        $errors[] = "Invalid email format";
    }
    
    // Validate age
    if ($age < 18 || $age > 120) {
        $errors[] = "Age must be between 18 and 120";
    }
    
    if (empty($errors)) {
        echo "Form is valid!";
    } else {
        foreach ($errors as $error) {
            echo $error . "<br>";
        }
    }
}
?>
```
</details>
<details>
<summary><strong>18. What is the isset() function used for?</strong></summary>

**Answer:**
The `isset()` function checks if a variable is set and is not NULL. It returns `true` if the variable exists and has a value other than NULL, otherwise `false`.[^2]

**Example:**
```php
<?php
$username = "john";
$password = "";
$age = null;

// isset() checks
var_dump(isset($username));    // bool(true)
var_dump(isset($password));    // bool(true) - empty string is still set
var_dump(isset($age));         // bool(false) - null is not set
var_dump(isset($undefined));   // bool(false) - variable doesn't exist

// Practical usage
if (isset($_POST['username']) && isset($_POST['password'])) {
    $user = $_POST['username'];
    $pass = $_POST['password'];
    
    // Process login
    if (!empty($user) && !empty($pass)) {
        echo "Processing login...";
    }
}

// Multiple variables
if (isset($username, $password, $age)) {
    echo "All variables are set";
}
?>
```
</details>
<details>
<summary><strong>19. What is the difference between unset() and unlink()?</strong></summary>

**Answer:**
These functions serve completely different purposes:[^1]
- **unset()**: Removes variables from memory
- **unlink()**: Deletes files from the filesystem

**Example:**
```php
<?php
// unset() - removes variables
$name = "John";
$age = 30;

echo $name; // John
unset($name);
// echo $name; // Would cause error - variable no longer exists

// Can unset multiple variables
unset($age, $city, $country);

// unset() with arrays
$fruits = ["apple", "banana", "orange"];
unset($fruits[^1]); // Removes "banana"
print_r($fruits);  // [^0] => apple, [^2] => orange

// unlink() - deletes files
file_put_contents('temp.txt', 'Hello World');
if (file_exists('temp.txt')) {
    unlink('temp.txt'); // Delete the file
    echo "File deleted";
}
?>
```
</details>
<details>
<summary><strong>20. How do you work with dates and times in PHP?</strong></summary>

**Answer:**
PHP provides extensive date and time functionality through built-in functions and the DateTime class. You can format, manipulate, and calculate with dates easily.[^1]

**Example:**
```php
<?php
// Current date and time
echo date('Y-m-d H:i:s');          // 2025-01-15 14:30:45
echo date('F j, Y');               // January 15, 2025

// Create specific dates
$date1 = new DateTime('2025-01-01');
$date2 = new DateTime('2025-12-31');

// Format dates
echo $date1->format('Y-m-d');      // 2025-01-01
echo $date2->format('M j, Y');     // Dec 31, 2025

// Date calculations
$date1->add(new DateInterval('P1M')); // Add 1 month
echo $date1->format('Y-m-d');         // 2025-02-01

// Time difference
$now = new DateTime();
$birthday = new DateTime('1990-05-15');
$age = $now->diff($birthday);
echo $age->y . ' years old';

// Timestamps
echo time();                       // Current Unix timestamp
echo strtotime('next Monday');     // Timestamp for next Monday
?>
```
</details>

***

## Functions and Control Structures 🔧

<details>
<summary><strong>21. How do you create and use functions in PHP?</strong></summary>

**Answer:**
Functions in PHP are defined using the `function` keyword. They can accept parameters, have default values, and return data. Functions help organize code and promote reusability.[^2]

**Example:**
```php
<?php
// Basic function
function greet($name) {
    return "Hello, " . $name . "!";
}

// Function with default parameter
function introduce($name, $age = 25) {
    return "$name is $age years old";
}

// Function with multiple return types
function calculate($a, $b, $operation = 'add') {
    switch ($operation) {
        case 'add':
            return $a + $b;
        case 'subtract':
            return $a - $b;
        case 'multiply':
            return $a * $b;
        default:
            return "Unknown operation";
    }
}

// Usage
echo greet("John");                    // Hello, John!
echo introduce("Jane");                // Jane is 25 years old
echo introduce("Bob", 30);             // Bob is 30 years old
echo calculate(10, 5, 'multiply');     // 50
?>
```
</details>
<details>
<summary><strong>22. What are the different types of loops in PHP?</strong></summary>

**Answer:**
PHP supports four main loop types: `for`, `while`, `do-while`, and `foreach`. Each is suited for different scenarios depending on your iteration needs.[^1]

**Example:**
```php
<?php
// for loop - when you know iteration count
for ($i = 1; $i <= 5; $i++) {
    echo "Number: $i\n";
}

// while loop - condition checked first
$count = 1;
while ($count <= 3) {
    echo "Count: $count\n";
    $count++;
}

// do-while loop - executes at least once
$x = 6;
do {
    echo "X is: $x\n";
    $x++;
} while ($x <= 5); // Still executes once even though condition is false

// foreach loop - for arrays
$fruits = ["apple", "banana", "orange"];
foreach ($fruits as $fruit) {
    echo "Fruit: $fruit\n";
}

// foreach with key-value pairs
$person = ["name" => "John", "age" => 30, "city" => "NYC"];
foreach ($person as $key => $value) {
    echo "$key: $value\n";
}
?>
```
</details>
<details>
<summary><strong>23. What is the purpose of break and continue statements?</strong></summary>

**Answer:**
Break and continue control loop execution flow:[^1]
- **break**: Immediately exits the loop completely
- **continue**: Skips current iteration and moves to next one

**Example:**
```php
<?php
// break example - exit loop when condition met
for ($i = 1; $i <= 10; $i++) {
    if ($i == 5) {
        break; // Exit loop when $i equals 5
    }
    echo "Number: $i\n";
}
// Output: 1, 2, 3, 4

// continue example - skip specific iterations
for ($i = 1; $i <= 5; $i++) {
    if ($i == 3) {
        continue; // Skip when $i equals 3
    }
    echo "Number: $i\n";
}
// Output: 1, 2, 4, 5

// Nested loops with break
$found = false;
for ($i = 1; $i <= 3; $i++) {
    for ($j = 1; $j <= 3; $j++) {
        if ($i == 2 && $j == 2) {
            $found = true;
            break 2; // Break out of both loops
        }
        echo "i=$i, j=$j\n";
    }
}
?>
```
</details>
<details>
<summary><strong>24. How do you handle file operations in PHP?</strong></summary>

**Answer:**
PHP provides various functions for file operations like reading, writing, and manipulating files. Always check if files exist and handle errors appropriately.[^2]

**Example:**
```php
<?php
// Write to file
$content = "Hello, World!\nThis is a test file.";
file_put_contents('test.txt', $content);

// Read entire file
$data = file_get_contents('test.txt');
echo $data;

// Read file line by line
$file = fopen('test.txt', 'r');
if ($file) {
    while (($line = fgets($file)) !== false) {
        echo "Line: " . $line;
    }
    fclose($file);
}

// File information
if (file_exists('test.txt')) {
    echo "File size: " . filesize('test.txt') . " bytes\n";
    echo "Last modified: " . date('Y-m-d H:i:s', filemtime('test.txt'));
}

// Directory operations
$files = scandir('.');
foreach ($files as $file) {
    if ($file != '.' && $file != '..') {
        echo "File: $file\n";
    }
}

// Delete file
unlink('test.txt');
?>
```
</details>
<details>
<summary><strong>25. What are PHP filters and how do you use them?</strong></summary>

**Answer:**
PHP filters are used to validate and sanitize data. They provide a safer way to handle user input and external data by applying predefined or custom filtering rules.[^1]

**Example:**
```php
<?php
// Validate email
$email = "john@example.com";
if (filter_var($email, FILTER_VALIDATE_EMAIL)) {
    echo "Valid email: $email\n";
}

// Validate URL
$url = "https://www.example.com";
if (filter_var($url, FILTER_VALIDATE_URL)) {
    echo "Valid URL: $url\n";
}

// Validate integer
$int = "100";
if (filter_var($int, FILTER_VALIDATE_INT)) {
    echo "Valid integer: $int\n";
}

// Sanitize string
$string = "<script>alert('xss')</script>Hello World";
$clean = filter_var($string, FILTER_SANITIZE_STRING);
echo "Cleaned: $clean\n"; // Hello World

// Sanitize email
$dirty_email = "john@@example.com";
$clean_email = filter_var($dirty_email, FILTER_SANITIZE_EMAIL);
echo "Cleaned email: $clean_email\n"; // john@example.com

// Filter array
$data = [
    "name" => "John<script>",
    "email" => "john@example.com",
    "age" => "30"
];

$filters = [
    "name" => FILTER_SANITIZE_STRING,
    "email" => FILTER_VALIDATE_EMAIL,
    "age" => FILTER_VALIDATE_INT
];

$filtered = filter_var_array($data, $filters);
print_r($filtered);
?>
```
</details>
<details>
<summary><strong>26. How do you upload files in PHP?</strong></summary>

**Answer:**
File uploads use the `$_FILES` superglobal and require proper HTML form setup. Always validate file types, sizes, and move uploaded files securely to prevent security issues.[^2]

**Example:**
```php
<!-- HTML Form -->
<form action="upload.php" method="post" enctype="multipart/form-data">
    <input type="file" name="fileToUpload" id="fileToUpload">
    <input type="submit" value="Upload File" name="submit">
</form>

<?php
// upload.php
if ($_SERVER['REQUEST_METHOD'] == 'POST' && isset($_FILES['fileToUpload'])) {
    $target_dir = "uploads/";
    $target_file = $target_dir . basename($_FILES["fileToUpload"]["name"]);
    $uploadOk = 1;
    $imageFileType = strtolower(pathinfo($target_file, PATHINFO_EXTENSION));
    
    // Check if file is actual image
    $check = getimagesize($_FILES["fileToUpload"]["tmp_name"]);
    if ($check !== false) {
        echo "File is an image - " . $check["mime"] . ".";
        $uploadOk = 1;
    } else {
        echo "File is not an image.";
        $uploadOk = 0;
    }
    
    // Check file size (5MB limit)
    if ($_FILES["fileToUpload"]["size"] > 5000000) {
        echo "Sorry, your file is too large.";
        $uploadOk = 0;
    }
    
    // Allow certain file formats
    $allowed = ["jpg", "jpeg", "png", "gif"];
    if (!in_array($imageFileType, $allowed)) {
        echo "Sorry, only JPG, JPEG, PNG & GIF files are allowed.";
        $uploadOk = 0;
    }
    
    // Upload file
    if ($uploadOk == 1) {
        if (move_uploaded_file($_FILES["fileToUpload"]["tmp_name"], $target_file)) {
            echo "The file " . basename($_FILES["fileToUpload"]["name"]) . " has been uploaded.";
        } else {
            echo "Sorry, there was an error uploading your file.";
        }
    }
}
?>
```
</details>
<details>
<summary><strong>27. What is the count() function and how is it used?</strong></summary>

**Answer:**
The `count()` function returns the number of elements in an array or properties in an object. It's essential for array operations and loop control.[^1]

**Example:**
```php
<?php
// Basic array counting
$fruits = ["apple", "banana", "orange"];
echo count($fruits); // Output: 3

// Multidimensional arrays
$matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
];
echo count($matrix);           // Output: 3 (outer array)
echo count($matrix, COUNT_RECURSIVE); // Output: 12 (all elements)

// Associative arrays
$person = [
    "name" => "John",
    "age" => 30,
    "city" => "New York"
];
echo count($person); // Output: 3

// Empty arrays
$empty = [];
echo count($empty); // Output: 0

// Using in loops
$data = ["a", "b", "c", "d", "e"];
for ($i = 0; $i < count($data); $i++) {
    echo "Index $i: " . $data[$i] . "\n";
}

// Performance tip: store count in variable for loops
$total = count($data);
for ($i = 0; $i < $total; $i++) {
    // Process data
}
?>
```
</details>
<details>
<summary><strong>28. How do you work with JSON in PHP?</strong></summary>

**Answer:**
PHP provides `json_encode()` and `json_decode()` functions to work with JSON data. This is essential for APIs, data storage, and client-server communication.[^2]

**Example:**
```php
<?php
// PHP array to JSON
$data = [
    "name" => "John Doe",
    "age" => 30,
    "skills" => ["PHP", "MySQL", "JavaScript"],
    "active" => true
];

$json = json_encode($data);
echo $json;
// Output: {"name":"John Doe","age":30,"skills":["PHP","MySQL","JavaScript"],"active":true}

// Pretty print JSON
$json_pretty = json_encode($data, JSON_PRETTY_PRINT);
echo $json_pretty;

// JSON to PHP array
$json_string = '{"name":"Jane","age":25,"city":"NYC"}';
$array = json_decode($json_string, true); // true for associative array
print_r($array);

// JSON to PHP object
$object = json_decode($json_string);
echo $object->name; // Jane

// Handle JSON errors
$invalid_json = '{"name":"John",age:30}'; // Invalid JSON
$result = json_decode($invalid_json);
if (json_last_error() !== JSON_ERROR_NONE) {
    echo "JSON Error: " . json_last_error_msg();
}

// Working with files
file_put_contents('data.json', $json);
$loaded_data = json_decode(file_get_contents('data.json'), true);
?>
```
</details>
<details>
<summary><strong>29. What is the explode() and implode() functions?</strong></summary>

**Answer:**
These functions convert between strings and arrays:[^2]
- **explode()**: Splits a string into an array using a delimiter
- **implode()**: Joins array elements into a string using a separator

**Example:**
```php
<?php
// explode() - string to array
$email = "john.doe@example.com";
$parts = explode("@", $email);
print_r($parts);
// Output: Array([^0] => john.doe [^1] => example.com)

$csv_data = "apple,banana,orange,grape";
$fruits = explode(",", $csv_data);
print_r($fruits);

// explode with limit
$text = "one-two-three-four";
$limited = explode("-", $text, 2);
print_r($limited);
// Output: Array([^0] => one [^1] => two-three-four)

// implode() - array to string
$words = ["Hello", "World", "from", "PHP"];
$sentence = implode(" ", $words);
echo $sentence; // Hello World from PHP

$numbers = [1, 2, 3, 4, 5];
$csv = implode(",", $numbers);
echo $csv; // 1,2,3,4,5

// Real-world example: URL building
$url_parts = ["https://", "www.example.com", "path", "to", "page"];
$url = implode("", $url_parts);
echo $url; // https://www.example.com/path/to/page

// Processing form data
if (isset($_POST['hobbies'])) {
    $hobbies = $_POST['hobbies']; // Array from checkboxes
    $hobby_string = implode(", ", $hobbies);
    echo "Your hobbies: " . $hobby_string;
}
?>
```
</details>
<details>
<summary><strong>30. How do you handle regular expressions in PHP?</strong></summary>

**Answer:**
PHP uses PCRE (Perl Compatible Regular Expressions) functions like `preg_match()`, `preg_replace()`, and `preg_split()`. Regular expressions are powerful for pattern matching and text manipulation.[^1]

**Example:**
```php
<?php
// preg_match - find pattern
$text = "My phone number is 555-123-4567";
$pattern = '/\d{3}-\d{3}-\d{4}/';

if (preg_match($pattern, $text, $matches)) {
    echo "Found phone: " . $matches[^0]; // 555-123-4567
}

// Validate email
$email = "john@example.com";
$email_pattern = '/^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/';
if (preg_match($email_pattern, $email)) {
    echo "Valid email format";
}

// preg_replace - replace pattern
$text = "Hello World! This is a test.";
$clean = preg_replace('/[^a-zA-Z0-9\s]/', '', $text);
echo $clean; // Hello World This is a test

// Extract all matches
$html = '<p>First paragraph</p><p>Second paragraph</p>';
preg_match_all('/<p>(.*?)<\/p>/', $html, $matches);
print_r($matches[^1]); // Array of paragraph contents

// preg_split - split by pattern
$text = "apple,banana;orange:grape";
$fruits = preg_split('/[,;:]/', $text);
print_r($fruits);

// Validation function
function validatePassword($password) {
    // At least 8 chars, 1 upper, 1 lower, 1 number
    $pattern = '/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)[a-zA-Z\d@$!%*?&]{8,}$/';
    return preg_match($pattern, $password);
}

var_dump(validatePassword("MyPass123")); // true
var_dump(validatePassword("weak"));      // false
?>
```
</details>

***

## Object-Oriented Programming 🏗️

<details>
<summary><strong>31. What are classes and objects in PHP?</strong></summary>

**Answer:**
Classes are blueprints for creating objects, while objects are instances of classes. PHP supports full object-oriented programming with properties, methods, inheritance, and more advanced OOP features.[^1]

**Example:**
```php
<?php
class Car {
    // Properties
    public $brand;
    public $model;
    private $price;
    protected $engine;
    
    // Constructor
    public function __construct($brand, $model, $price) {
        $this->brand = $brand;
        $this->model = $model;
        $this->price = $price;
        $this->engine = "V6";
    }
    
    // Methods
    public function start() {
        return "The {$this->brand} {$this->model} is starting...";
    }
    
    public function getPrice() {
        return $this->price;
    }
    
    public function setPrice($price) {
        if ($price > 0) {
            $this->price = $price;
        }
    }
}

// Create objects
$car1 = new Car("Toyota", "Camry", 25000);
$car2 = new Car("Honda", "Civic", 22000);

// Use objects
echo $car1->start();           // The Toyota Camry is starting...
echo $car1->brand;             // Toyota
echo $car1->getPrice();        // 25000
$car1->setPrice(26000);
?>
```
</details>
<details>
<summary><strong>32. What is inheritance in PHP?</strong></summary>

**Answer:**
Inheritance allows a class to inherit properties and methods from another class using the `extends` keyword. The child class can override parent methods and add new functionality while maintaining the parent's interface.[^1]

**Example:**
```php
<?php
// Parent class
class Vehicle {
    protected $brand;
    protected $year;
    
    public function __construct($brand, $year) {
        $this->brand = $brand;
        $this->year = $year;
    }
    
    public function start() {
        return "Vehicle is starting...";
    }
    
    public function getInfo() {
        return "{$this->brand} ({$this->year})";
    }
}

// Child class
class Car extends Vehicle {
    private $doors;
    
    public function __construct($brand, $year, $doors) {
        parent::__construct($brand, $year); // Call parent constructor
        $this->doors = $doors;
    }
    
    // Override parent method
    public function start() {
        return "Car engine is starting with a roar!";
    }
    
    // Add new method
    public function honk() {
        return "Beep! Beep!";
    }
    
    public function getInfo() {
        return parent::getInfo() . " - {$this->doors} doors";
    }
}

// Usage
$vehicle = new Vehicle("Generic", 2020);
$car = new Car("BMW", 2023, 4);

echo $vehicle->start();    // Vehicle is starting...
echo $car->start();        // Car engine is starting with a roar!
echo $car->getInfo();      // BMW (2023) - 4 doors
echo $car->honk();         // Beep! Beep!
?>
```
</details>
<details>
<summary><strong>33. What are abstract classes and interfaces?</strong></summary>

**Answer:**
Abstract classes provide partial implementation and cannot be instantiated. Interfaces define contracts that classes must implement. A class can extend one abstract class but implement multiple interfaces.[^1]

**Example:**
```php
<?php
// Abstract class
abstract class Animal {
    protected $name;
    
    public function __construct($name) {
        $this->name = $name;
    }
    
    // Concrete method
    public function getName() {
        return $this->name;
    }
    
    // Abstract method - must be implemented by child classes
    abstract public function makeSound();
    abstract public function move();
}

// Interface
interface Flyable {
    public function fly();
}

// Interface
interface Swimmable {
    public function swim();
}

// Concrete class extending abstract class
class Dog extends Animal {
    public function makeSound() {
        return "{$this->name} says Woof!";
    }
    
    public function move() {
        return "{$this->name} is running";
    }
}

// Class implementing multiple interfaces
class Duck extends Animal implements Flyable, Swimmable {
    public function makeSound() {
        return "{$this->name} says Quack!";
    }
    
    public function move() {
        return "{$this->name} is waddling";
    }
    
    public function fly() {
        return "{$this->name} is flying";
    }
    
    public function swim() {
        return "{$this->name} is swimming";
    }
}

// Usage
$dog = new Dog("Buddy");
$duck = new Duck("Donald");

echo $dog->makeSound();  // Buddy says Woof!
echo $duck->fly();       // Donald is flying
echo $duck->swim();      // Donald is swimming
?>
```
</details>
<details>
<summary><strong>34. What are PHP traits?</strong></summary>

**Answer:**
Traits enable code reuse in single inheritance languages like PHP. They allow you to include methods in multiple classes without using inheritance, solving the problem of code duplication across unrelated classes.[^2]

**Example:**
```php
<?php
// Define traits
trait Logger {
    public function log($message) {
        echo "[" . date('Y-m-d H:i:s') . "] " . $message . "\n";
    }
}

trait Validator {
    public function validate($data) {
        return !empty($data);
    }
    
    public function sanitize($data) {
        return htmlspecialchars(trim($data));
    }
}

// Use traits in classes
class User {
    use Logger, Validator;
    
    private $name;
    private $email;
    
    public function __construct($name, $email) {
        if ($this->validate($name) && $this->validate($email)) {
            $this->name = $this->sanitize($name);
            $this->email = $this->sanitize($email);
            $this->log("User created: {$this->name}");
        }
    }
    
    public function getName() {
        return $this->name;
    }
}

class Product {
    use Logger, Validator;
    
    private $title;
    private $price;
    
    public function __construct($title, $price) {
        if ($this->validate($title) && $price > 0) {
            $this->title = $this->sanitize($title);
            $this->price = $price;
            $this->log("Product created: {$this->title}");
        }
    }
}

// Usage
$user = new User("John Doe", "john@example.com");
$product = new Product("Laptop", 999.99);
?>
```
</details>
<details>
<summary><strong>35. What are magic methods in PHP?</strong></summary>

**Answer:**
Magic methods are special methods that start with double underscores. They are automatically called when certain events occur, such as object creation, property access, or method calls.[^2]

**Example:**
```php
<?php
class MagicExample {
    private $data = [];
    
    // Called when object is created
    public function __construct($name = "Unknown") {
        $this->data['name'] = $name;
        echo "Object created for: {$name}\n";
    }
    
    // Called when object is destroyed
    public function __destruct() {
        echo "Object destroyed\n";
    }
    
    // Called when accessing non-existent property
    public function __get($property) {
        if (isset($this->data[$property])) {
            return $this->data[$property];
        }
        return null;
    }
    
    // Called when setting non-existent property
    public function __set($property, $value) {
        $this->data[$property] = $value;
    }
    
    // Called when checking if property exists
    public function __isset($property) {
        return isset($this->data[$property]);
    }
    
    // Called when unsetting a property
    public function __unset($property) {
        unset($this->data[$property]);
    }
    
    // Called when object is treated as string
    public function __toString() {
        return "MagicExample: " . json_encode($this->data);
    }
    
    // Called when calling non-existent method
    public function __call($method, $args) {
        echo "Method {$method} called with args: " . implode(', ', $args) . "\n";
    }
    
    // Called when calling non-existent static method
    public static function __callStatic($method, $args) {
        echo "Static method {$method} called\n";
    }
}

// Usage
$obj = new MagicExample("John");

$obj->age = 30;              // Calls __set()
echo $obj->age;              // Calls __get()
echo isset($obj->age);       // Calls __isset()

echo $obj;                   // Calls __toString()
$obj->nonExistentMethod("test"); // Calls __call()

MagicExample::staticMethod(); // Calls __callStatic()
?>
```
</details>
<details>
<summary><strong>36. What is method overloading and overriding?</strong></summary>

**Answer:**
PHP doesn't support traditional method overloading (same method name with different parameters) but achieves similar results through magic methods. Method overriding allows child classes to provide specific implementations of parent methods.[^1]

**Example:**
```php
<?php
// Method Overriding Example
class Animal {
    public function speak() {
        return "Animal makes a sound";
    }
    
    public function info() {
        return "This is an animal";
    }
}

class Dog extends Animal {
    // Override parent method
    public function speak() {
        return "Dog barks: Woof! Woof!";
    }
    
    // Override parent method
    public function info() {
        return parent::info() . " - specifically a dog";
    }
}

// Method "Overloading" using __call magic method
class Calculator {
    public function __call($method, $args) {
        if ($method === 'add') {
            return $this->handleAdd($args);
        }
        throw new Exception("Method {$method} not found");
    }
    
    private function handleAdd($args) {
        $count = count($args);
        
        switch ($count) {
            case 1:
                return $args[^0];
            case 2:
                return $args[^0] + $args[^1];
            case 3:
                return $args[^0] + $args[^1] + $args[^2];
            default:
                return array_sum($args);
        }
    }
}

// Usage
$animal = new Animal();
$dog = new Dog();

echo $animal->speak();  // Animal makes a sound
echo $dog->speak();     // Dog barks: Woof! Woof!
echo $dog->info();      // This is an animal - specifically a dog

$calc = new Calculator();
echo $calc->add(5);         // 5
echo $calc->add(3, 7);      // 10
echo $calc->add(1, 2, 3);   // 6
echo $calc->add(1, 2, 3, 4, 5); // 15
?>
```
</details>
<details>
<summary><strong>37. What are static properties and methods?</strong></summary>

**Answer:**
Static properties and methods belong to the class rather than instances. They can be accessed without creating an object using the scope resolution operator (::). Static members are shared across all instances.[^1]

**Example:**
```php
<?php
class Counter {
    // Static property
    private static $count = 0;
    private static $instances = [];
    
    // Instance property
    private $id;
    
    public function __construct() {
        self::$count++;
        $this->id = self::$count;
        self::$instances[] = $this;
    }
    
    // Static method
    public static function getCount() {
        return self::$count;
    }
    
    // Static method
    public static function getInstances() {
        return self::$instances;
    }
    
    // Instance method
    public function getId() {
        return $this->id;
    }
    
    // Static method accessing static property
    public static function reset() {
        self::$count = 0;
        self::$instances = [];
    }
}

class DatabaseConnection {
    private static $instance = null;
    private $connection;
    
    // Private constructor (Singleton pattern)
    private function __construct() {
        $this->connection = "Database connected";
    }
    
    // Static method to get instance
    public static function getInstance() {
        if (self::$instance === null) {
            self::$instance = new self();
        }
        return self::$instance;
    }
    
    public function query($sql) {
        return "Executing: " . $sql;
    }
}

// Usage
echo Counter::getCount();  // 0 (no instances yet)

$c1 = new Counter();
$c2 = new Counter();
$c3 = new Counter();

echo Counter::getCount();  // 3
echo $c2->getId();         // 2

// Singleton pattern
$db1 = DatabaseConnection::getInstance();
$db2 = DatabaseConnection::getInstance();
var_dump($db1 === $db2);   // true (same instance)
?>
```
</details>
<details>
<summary><strong>38. What is the difference between public, private, and protected?</strong></summary>

**Answer:**
These are visibility modifiers that control access to properties and methods:[^1]
- **public**: Accessible from anywhere
- **private**: Only accessible within the same class
- **protected**: Accessible within the class and its subclasses

**Example:**
```php
<?php
class BankAccount {
    public $accountNumber;      // Accessible from anywhere
    protected $balance;         // Accessible in this class and subclasses
    private $pin;              // Only accessible within this class
    
    public function __construct($accountNumber, $initialBalance, $pin) {
        $this->accountNumber = $accountNumber;
        $this->balance = $initialBalance;
        $this->pin = $pin;
    }
    
    // Public method - accessible from anywhere
    public function getAccountInfo() {
        return "Account: " . $this->accountNumber;
    }
    
    // Protected method - accessible in subclasses
    protected function validatePin($inputPin) {
        return $this->pin === $inputPin;
    }
    
    // Private method - only within this class
    private function logTransaction($type, $amount) {
        echo "Log: {$type} of {$amount} on account {$this->accountNumber}\n";
    }
    
    public function withdraw($amount, $inputPin) {
        if ($this->validatePin($inputPin)) {
            if ($this->balance >= $amount) {
                $this->balance -= $amount;
                $this->logTransaction("Withdrawal", $amount);
                return true;
            }
        }
        return false;
    }
}

class SavingsAccount extends BankAccount {
    private $interestRate;
    
    public function __construct($accountNumber, $initialBalance, $pin, $interestRate) {
        parent::__construct($accountNumber, $initialBalance, $pin);
        $this->interestRate = $interestRate;
    }
    
    public function addInterest() {
        // Can access protected property and method
        $interest = $this->balance * $this->interestRate;
        $this->balance += $interest;
        
        // Can access protected method
        if ($this->validatePin("1234")) {
            echo "Interest added\n";
        }
        
        // Cannot access private property or method
        // echo $this->pin;  // Error
        // $this->logTransaction(); // Error
    }
}

// Usage
$account = new BankAccount("12345", 1000, "1234");

echo $account->accountNumber;    // OK - public
echo $account->getAccountInfo(); // OK - public method
// echo $account->balance;       // Error - protected
// echo $account->pin;           // Error - private
?>
```
</details>
<details>
<summary><strong>39. What are namespaces in PHP?</strong></summary>

**Answer:**
Namespaces provide a way to group related classes, interfaces, functions, and constants. They prevent naming conflicts and help organize code, especially in large applications or when using multiple libraries.[^1]

**Example:**
```php
<?php
// File: Database/Connection.php
namespace Database;

class Connection {
    public function connect() {
        return "Database connection established";
    }
}

class User {
    public function find($id) {
        return "Finding user {$id} in database";
    }
}

// File: Cache/Connection.php
namespace Cache;

class Connection {
    public function connect() {
        return "Cache connection established";
    }
}

// File: main.php
namespace App;

// Import specific class
use Database\Connection as DatabaseConnection;
use Cache\Connection as CacheConnection;
use Database\User;

// Or import entire namespace
use Database;

// Usage
$dbConn = new DatabaseConnection();
$cacheConn = new CacheConnection();
$user = new User();

echo $dbConn->connect();     // Database connection established
echo $cacheConn->connect();  // Cache connection established

// Alternative usage with full namespace
$dbConn2 = new Database\Connection();
$user2 = new Database\User();

// Global namespace
$dateTime = new \DateTime(); // \ refers to global namespace

// Define functions in namespace
namespace Utils;

function formatName($name) {
    return ucfirst(strtolower($name));
}

// Use the function
echo Utils\formatName("JOHN DOE");
?>
```
</details>
<details>
<summary><strong>40. What is autoloading in PHP?</strong></summary>

**Answer:**
Autoloading automatically loads PHP classes when they are first used, eliminating the need for manual include/require statements. This is typically achieved using `spl_autoload_register()` or Composer's PSR-4 autoloading.[^2]

**Example:**
```php
<?php
// Manual autoloader
spl_autoload_register(function ($className) {
    // Convert namespace separators to directory separators
    $classFile = str_replace('\\', DIRECTORY_SEPARATOR, $className) . '.php';
    
    // Define possible directories
    $directories = [
        'classes/',
        'lib/',
        'src/'
    ];
    
    foreach ($directories as $directory) {
        $file = $directory . $classFile;
        if (file_exists($file)) {
            require_once $file;
            return;
        }
    }
    
    throw new Exception("Class {$className} not found");
});

// PSR-4 compliant autoloader
spl_autoload_register(function ($className) {
    $prefix = 'App\\';
    $baseDir = __DIR__ . '/src/';
    
    // Check if class uses the namespace prefix
    $len = strlen($prefix);
    if (strncmp($prefix, $className, $len) !== 0) {
        return; // Not our responsibility
    }
    
    // Get relative class name
    $relativeClass = substr($className, $len);
    
    // Replace namespace separators with directory separators
    $file = $baseDir . str_replace('\\', '/', $relativeClass) . '.php';
    
    if (file_exists($file)) {
        require $file;
    }
});

// Composer autoloader (composer.json)
/*
{
    "autoload": {
        "psr-4": {
            "App\\": "src/",
            "Database\\": "src/Database/",
            "Utils\\": "src/Utils/"
        }
    }
}
*/

// After running "composer dump-autoload"
// require_once 'vendor/autoload.php';

// Now classes are loaded automatically
$user = new App\Models\User();        // Auto-loads src/Models/User.php
$db = new Database\Connection();      // Auto-loads src/Database/Connection.php
$helper = new Utils\StringHelper();  // Auto-loads src/Utils/StringHelper.php
?>
```
</details>

***

## Security and Best Practices 🔐

<details>
<summary><strong>41. How do you prevent SQL injection in PHP?</strong></summary>

**Answer:**
SQL injection is prevented by using prepared statements with parameter binding. Never concatenate user input directly into SQL queries. Use PDO or MySQLi with prepared statements to safely handle user data.[^2]

**Example:**
```php
<?php
// WRONG - Vulnerable to SQL injection
function getUserWrong($username, $password) {
    $pdo = new PDO('mysql:host=localhost;dbname=test', 'user', 'pass');
    $sql = "SELECT * FROM users WHERE username = '$username' AND password = '$password'";
    return $pdo->query($sql)->fetch();
}

// RIGHT - Using prepared statements
function getUserSafe($username, $password) {
    $pdo = new PDO('mysql:host=localhost;dbname=test', 'user', 'pass');
    
    // Prepare statement with placeholders
    $stmt = $pdo->prepare("SELECT * FROM users WHERE username = ? AND password = ?");
    
    // Execute with parameters
    $stmt->execute([$username, hash('sha256', $password)]);
    
    return $stmt->fetch();
}

// Named parameters (alternative)
function getUserSafeNamed($username, $password) {
    $pdo = new PDO('mysql:host=localhost;dbname=test', 'user', 'pass');
    
    $stmt = $pdo->prepare("SELECT * FROM users WHERE username = :username AND password = :password");
    
    $stmt->bindParam(':username', $username, PDO::PARAM_STR);
    $stmt->bindParam(':password', hash('sha256', $password), PDO::PARAM_STR);
    
    $stmt->execute();
    return $stmt->fetch();
}

// INSERT example
function createUser($username, $email, $password) {
    $pdo = new PDO('mysql:host=localhost;dbname=test', 'user', 'pass');
    
    $stmt = $pdo->prepare("INSERT INTO users (username, email, password, created_at) VALUES (?, ?, ?, NOW())");
    
    return $stmt->execute([
        $username,
        $email,
        password_hash($password, PASSWORD_DEFAULT)
    ]);
}
?>
```
</details>
<details>
<summary><strong>42. How do you handle Cross-Site Scripting (XSS) prevention?</strong></summary>

**Answer:**
XSS is prevented by properly escaping output and validating input. Never trust user data and always escape it when displaying in HTML. Use appropriate functions like `htmlspecialchars()` and validate input on the server side.[^1]

**Example:**
```php
<?php
// Prevent XSS when displaying user data
function escapeOutput($data) {
    return htmlspecialchars($data, ENT_QUOTES, 'UTF-8');
}

// Safe output
$userInput = "<script>alert('XSS');</script>";
echo escapeOutput($userInput); // &lt;script&gt;alert(&#039;XSS&#039;);&lt;/script&gt;

// Input validation and sanitization
function sanitizeInput($input) {
    // Remove HTML tags
    $input = strip_tags($input);
    
    // Remove special characters
    $input = htmlspecialchars($input, ENT_QUOTES, 'UTF-8');
    
    // Trim whitespace
    $input = trim($input);
    
    return $input;
}

// Content Security Policy header
function setCSPHeader() {
    header("Content-Security-Policy: default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline'");
}

// Form handling with XSS prevention
if ($_POST) {
    $name = sanitizeInput($_POST['name']);
    $comment = sanitizeInput($_POST['comment']);
    
    // Validate
    if (strlen($name) < 2) {
        $errors[] = "Name is too short";
    }
    
    if (empty($errors)) {
        // Store safely in database using prepared statements
        $stmt = $pdo->prepare("INSERT INTO comments (name, comment) VALUES (?, ?)");
        $stmt->execute([$name, $comment]);
    }
}

// Display comments safely
function displayComments($pdo) {
    $stmt = $pdo->query("SELECT name, comment, created_at FROM comments ORDER BY created_at DESC");
    
    while ($row = $stmt->fetch()) {
        echo "<div class='comment'>";
        echo "<strong>" . escapeOutput($row['name']) . "</strong>";
        echo "<p>" . nl2br(escapeOutput($row['comment'])) . "</p>";
        echo "<small>" . escapeOutput($row['created_at']) . "</small>";
        echo "</div>";
    }
}
?>
```
</details>
<details>
<summary><strong>43. How do you implement secure password handling?</strong></summary>

**Answer:**
Use PHP's built-in password hashing functions `password_hash()` and `password_verify()`. Never store plain text passwords. Always hash passwords with a strong algorithm like bcrypt, which is the default in PHP.[^2]

**Example:**
```php
<?php
class UserAuth {
    private $pdo;
    
    public function __construct($pdo) {
        $this->pdo = $pdo;
    }
    
    // Register new user with secure password
    public function register($username, $email, $password) {
        // Validate password strength
        if (!$this->isPasswordStrong($password)) {
            throw new Exception("Password must be at least 8 characters with uppercase, lowercase, and number");
        }
        
        // Hash password securely
        $hashedPassword = password_hash($password, PASSWORD_DEFAULT);
        
        // Store in database
        $stmt = $this->pdo->prepare("INSERT INTO users (username, email, password) VALUES (?, ?, ?)");
        return $stmt->execute([$username, $email, $hashedPassword]);
    }
    
    // Login with secure password verification
    public function login($username, $password) {
        $stmt = $this->pdo->prepare("SELECT id, username, password FROM users WHERE username = ?");
        $stmt->execute([$username]);
        $user = $stmt->fetch();
        
        if ($user && password_verify($password, $user['password'])) {
            // Check if password needs rehashing (algorithm updated)
            if (password_needs_rehash($user['password'], PASSWORD_DEFAULT)) {
                $newHash = password_hash($password, PASSWORD_DEFAULT);
                $updateStmt = $this->pdo->prepare("UPDATE users SET password = ? WHERE id = ?");
                $updateStmt->execute([$newHash, $user['id']]);
            }
            
            return $user;
        }
        
        return false;
    }
    
    // Password strength validation
    private function isPasswordStrong($password) {
        return preg_match('/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)[a-zA-Z\d@$!%*?&]{8,}$/', $password);
    }
    
    // Change password
    public function changePassword($userId, $oldPassword, $newPassword) {
        // Verify old password first
        $stmt = $this->pdo->prepare("SELECT password FROM users WHERE id = ?");
        $stmt->execute([$userId]);
        $user = $stmt->fetch();
        
        if (!$user || !password_verify($oldPassword, $user['password'])) {
            return false;
        }
        
        // Validate new password
        if (!$this->isPasswordStrong($newPassword)) {
            throw new Exception("New password doesn't meet requirements");
        }
        
        // Hash and update
        $hashedPassword = password_hash($newPassword, PASSWORD_DEFAULT);
        $updateStmt = $this->pdo->prepare("UPDATE users SET password = ? WHERE id = ?");
        return $updateStmt->execute([$hashedPassword, $userId]);
    }
}

// Usage example
try {
    $pdo = new PDO('mysql:host=localhost;dbname=test', 'user', 'pass');
    $auth = new UserAuth($pdo);
    
    // Register
    $auth->register('john', 'john@example.com', 'MySecure123');
    
    // Login
    $user = $auth->login('john', 'MySecure123');
    if ($user) {
        echo "Login successful!";
        session_start();
        $_SESSION['user_id'] = $user['id'];
    } else {
        echo "Invalid credentials";
    }
} catch (Exception $e) {
    echo "Error: " . $e->getMessage();
}
?>
```
</details>
<details>
<summary><strong>44. How do you implement CSRF protection?</strong></summary>

**Answer:**
CSRF (Cross-Site Request Forgery) protection requires generating and validating tokens for forms. Each form should include a unique token that's verified on submission to ensure the request came from your application.[^1]

**Example:**
```php
<?php
session_start();

class CSRFProtection {
    // Generate CSRF token
    public static function generateToken() {
        if (empty($_SESSION['csrf_token'])) {
            $_SESSION['csrf_token'] = bin2hex(random_bytes(32));
        }
        return $_SESSION['csrf_token'];
    }
    
    // Validate CSRF token
    public static function validateToken($token) {
        return isset($_SESSION['csrf_token']) && 
               hash_equals($_SESSION['csrf_token'], $token);
    }
    
    // Generate HTML input field
    public static function getTokenField() {
        $token = self::generateToken();
        return "<input type='hidden' name='csrf_token' value='{$token}'>";
    }
    
    // Middleware to check CSRF token
    public static function checkToken() {
        if ($_SERVER['REQUEST_METHOD'] === 'POST') {
            $token = $_POST['csrf_token'] ?? '';
            
            if (!self::validateToken($token)) {
                http_response_code(403);
                die('CSRF token mismatch. Request blocked for security.');
            }
        }
    }
    
    // Regenerate token after use (for extra security)
    public static function regenerateToken() {
        $_SESSION['csrf_token'] = bin2hex(random_bytes(32));
        return $_SESSION['csrf_token'];
    }
}

// Protected form handler
if ($_POST) {
    CSRFProtection::checkToken();
    
    // Process form safely
    $name = htmlspecialchars($_POST['name']);
    $email = filter_var($_POST['email'], FILTER_VALIDATE_EMAIL);
    
    if ($email) {
        echo "Form processed successfully!";
        // Optionally regenerate token
        CSRFProtection::regenerateToken();
    } else {
        echo "Invalid email address";
    }
}
?>

<!-- HTML Form with CSRF protection -->
<form method="POST" action="">
    <?= CSRFProtection::getTokenField(); ?>
    
    <label for="name">Name:</label>
    <input type="text" name="name" required>
    
    <label for="email">Email:</label>
    <input type="email" name="email" required>
    
    <button type="submit">Submit</button>
</form>

<?php
// AJAX CSRF protection
if (isset($_GET['get_token'])) {
    header('Content-Type: application/json');
    echo json_encode(['csrf_token' => CSRFProtection::generateToken()]);
    exit;
}
?>

<script>
// For AJAX requests
function makeSecureAjaxCall(data) {
    fetch('?get_token=1')
        .then(response => response.json())
        .then(tokenData => {
            data.csrf_token = tokenData.csrf_token;
            
            return fetch('process.php', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json',
                },
                body: JSON.stringify(data)
            });
        })
        .then(response => response.text())
        .then(result => console.log(result));
}
</script>
```
</details>
<details>
<summary><strong>45. How do you secure file uploads in PHP?</strong></summary>

**Answer:**
Secure file uploads require validating file types, sizes, and contents. Never trust the file extension or MIME type sent by the client. Store uploads outside the web root and use proper file validation techniques.[^2]

**Example:**
```php
<?php
class SecureFileUpload {
    private $uploadDir;
    private $allowedTypes;
    private $maxSize;
    
    public function __construct($uploadDir = 'uploads/', $maxSize = 5242880) { // 5MB
        $this->uploadDir = $uploadDir;
        $this->maxSize = $maxSize;
        $this->allowedTypes = [
            'image/jpeg' => 'jpg',
            'image/png' => 'png',
            'image/gif' => 'gif',
            'application/pdf' => 'pdf',
            'text/plain' => 'txt'
        ];
        
        // Ensure upload directory exists
        if (!is_dir($this->uploadDir)) {
            mkdir($this->uploadDir, 0755, true);
        }
    }
    
    public function upload($fileInput) {
        // Check if file was uploaded
        if (!isset($_FILES[$fileInput]) || $_FILES[$fileInput]['error'] !== UPLOAD_ERR_OK) {
            throw new Exception('No file uploaded or upload error occurred');
        }
        
        $file = $_FILES[$fileInput];
        
        // Validate file size
        if ($file['size'] > $this->maxSize) {
            throw new Exception('File size exceeds maximum allowed size');
        }
        
        // Get real MIME type
        $finfo = finfo_open(FILEINFO_MIME_TYPE);
        $mimeType = finfo_file($finfo, $file['tmp_name']);
        finfo_close($finfo);
        
        // Validate MIME type
        if (!array_key_exists($mimeType, $this->allowedTypes)) {
            throw new Exception('File type not allowed');
        }
        
        // Additional validation for images
        if (strpos($mimeType, 'image/') === 0) {
            $imageInfo = getimagesize($file['tmp_name']);
            if ($imageInfo === false) {
                throw new Exception('Invalid image file');
            }
            
            // Check image dimensions (optional)
            if ($imageInfo[^0] > 2000 || $imageInfo[^1] > 2000) {
                throw new Exception('Image dimensions too large');
            }
        }
        
        // Generate secure filename
        $extension = $this->allowedTypes[$mimeType];
        $filename = $this->generateSecureFilename($extension);
        $filepath = $this->uploadDir . $filename;
        
        // Move uploaded file
        if (!move_uploaded_file($file['tmp_name'], $filepath)) {
            throw new Exception('Failed to save uploaded file');
        }
        
        // Set proper permissions
        chmod($filepath, 0644);
        
        return [
            'filename' => $filename,
            'filepath' => $filepath,
            'size' => $file['size'],
            'type' => $mimeType
        ];
    }
    
    private function generateSecureFilename($extension) {
        return uniqid('file_', true) . '.' . $extension;
    }
    
    // Serve files securely (outside web root)
    public function serveFile($filename) {
        $filepath = $this->uploadDir . $filename;
        
        if (!file_exists($filepath)) {
            http_response_code(404);
            die('File not found');
        }
        
        // Get MIME type
        $finfo = finfo_open(FILEINFO_MIME_TYPE);
        $mimeType = finfo_file($finfo, $filepath);
        finfo_close($finfo);
        
        // Set appropriate headers
        header('Content-Type: ' . $mimeType);
        header('Content-Disposition: inline; filename="' . basename($filename) . '"');
        header('Content-Length: ' . filesize($filepath));
        
        // Serve file
        readfile($filepath);
        exit;
    }
}

// Usage
try {
    $uploader = new SecureFileUpload();
    
    if ($_POST && isset($_FILES['file'])) {
        $result = $uploader->upload('file');
        echo "File uploaded successfully: " . $result['filename'];
    }
    
} catch (Exception $e) {
    echo "Upload error: " . $e->getMessage();
}
?>

<!-- HTML Form -->
<form method="POST" enctype="multipart/form-data">
    <label for="file">Choose file (max 5MB):</label>
    <input type="file" name="file" accept=".jpg,.jpeg,.png,.gif,.pdf,.txt" required>
    <button type="submit">Upload</button>
</form>
```
</details>
<details>
<summary><strong>46. How do you implement secure session management?</strong></summary>

**Answer:**
Secure session management involves proper session configuration, regenerating session IDs, setting appropriate timeouts, and protecting against session hijacking. Always use HTTPS for sensitive applications.[^2]

**Example:**
```php
<?php
class SecureSession {
    private static $sessionTimeout = 1800; // 30 minutes
    private static $regenerateIdInterval = 300; // 5 minutes
    
    public static function start() {
        // Secure session configuration
        ini_set('session.cookie_httponly', 1);
        ini_set('session.use_only_cookies', 1);
        ini_set('session.cookie_secure', 1); // HTTPS only
        ini_set('session.cookie_samesite', 'Strict');
        
        session_start();
        
        // Validate session
        if (!self::isValidSession()) {
            self::destroy();
            session_start();
        }
        
        // Regenerate ID periodically
        if (self::shouldRegenerateId()) {
            self::regenerateId();
        }
        
        // Update last activity
        $_SESSION['last_activity'] = time();
    }
    
    private static function isValidSession() {
        // Check if session has required data
        if (!isset($_SESSION['created_at'])) {
            $_SESSION['created_at'] = time();
            return true;
        }
        
        // Check for session timeout
        if (isset($_SESSION['last_activity']) && 
            (time() - $_SESSION['last_activity']) > self::$sessionTimeout) {
            return false;
        }
        
        // Check for session hijacking
        if (isset($_SESSION['user_agent']) && 
            $_SESSION['user_agent'] !== $_SERVER['HTTP_USER_AGENT']) {
            return false;
        }
        
        // Check IP address (optional, can cause issues with proxies)
        if (isset($_SESSION['ip_address']) && 
            $_SESSION['ip_address'] !== $_SERVER['REMOTE_ADDR']) {
            return false;
        }
        
        return true;
    }
    
    private static function shouldRegenerateId() {
        return !isset($_SESSION['last_regenerate']) || 
               (time() - $_SESSION['last_regenerate']) > self::$regenerateIdInterval;
    }
    
    public static function regenerateId() {
        session_regenerate_id(true);
        $_SESSION['last_regenerate'] = time();
    }
    
    public static function login($userId, $userData = []) {
        // Regenerate session ID on login
        self::regenerateId();
        
        // Store user data
        $_SESSION['user_id'] = $userId;
        $_SESSION['user_data'] = $userData;
        $_SESSION['login_time'] = time();
        $_SESSION['user_agent'] = $_SERVER['HTTP_USER_AGENT'];
        $_SESSION['ip_address'] = $_SERVER['REMOTE_ADDR'];
        
        // Optional: store in database for tracking
        self::logSession($userId, session_id());
    }
    
    public static function logout() {
        // Clear session data
        if (isset($_SESSION['user_id'])) {
            self::logLogout($_SESSION['user_id'], session_id());
        }
        
        self::destroy();
    }
    
    public static function destroy() {
        session_unset();
        session_destroy();
        
        // Delete session cookie
        if (ini_get("session.use_cookies")) {
            $params = session_get_cookie_params();
            setcookie(session_name(), '', time() - 42000,
                $params["path"], $params["domain"],
                $params["secure"], $params["httponly"]
            );
        }
    }
    
    public static function isLoggedIn() {
        return isset($_SESSION['user_id']) && 
               isset($_SESSION['login_time']);
    }
    
    public static function getUserId() {
        return $_SESSION['user_id'] ?? null;
    }
    
    public static function getSessionInfo() {
        if (!self::isLoggedIn()) {
            return null;
        }
        
        return [
            'user_id' => $_SESSION['user_id'],
            'login_time' => $_SESSION['login_time'],
            'last_activity' => $_SESSION['last_activity'],
            'session_duration' => time() - $_SESSION['login_time']
        ];
    }
    
    private static function logSession($userId, $sessionId) {
        // Log session start (implement database logging)
        // $pdo->prepare("INSERT INTO user_sessions (user_id, session_id, started_at, ip_address, user_agent) VALUES (?, ?, NOW(), ?, ?)");
    }
    
    private static function logLogout($userId, $sessionId) {
        // Log session end (implement database logging)
        // $pdo->prepare("UPDATE user_sessions SET ended_at = NOW() WHERE user_id = ? AND session_id = ?");
    }
}

// Usage
SecureSession::start();

// Login
if ($_POST && isset($_POST['username'], $_POST['password'])) {
    // Authenticate user (implementation depends on your auth system)
    $userId = authenticateUser($_POST['username'], $_POST['password']);
    
    if ($userId) {
        SecureSession::login($userId, ['username' => $_POST['username']]);
        header('Location: dashboard.php');
        exit;
    } else {
        $error = "Invalid credentials";
    }
}

// Logout
if (isset($_GET['logout'])) {
    SecureSession::logout();
    header('Location: login.php');
    exit;
}

// Check authentication
if (!SecureSession::isLoggedIn()) {
    header('Location: login.php');
    exit;
}

// Display session info
$sessionInfo = SecureSession::getSessionInfo();
echo "Welcome user " . $sessionInfo['user_id'];
echo "Session duration: " . $sessionInfo['session_duration'] . " seconds";
?>
```
</details>
<details>
<summary><strong>47. How do you validate and sanitize input data?</strong></summary>

**Answer:**
Input validation and sanitization are crucial for security. Always validate data type, format, and range. Sanitize data by removing or escaping harmful characters. Use PHP's filter functions and create custom validation rules as needed.[^2]

**Example:**
```php
<?php
class InputValidator {
    private $errors = [];
    
    // Validate and sanitize string
    public function validateString($value, $minLength = 1, $maxLength = 255, $required = true) {
        if ($required && empty($value)) {
            $this->errors[] = "Field is required";
            return null;
        }
        
        // Sanitize
        $value = trim($value);
        $value = htmlspecialchars($value, ENT_QUOTES, 'UTF-8');
        
        // Validate length
        if (strlen($value) < $minLength) {
            $this->errors[] = "Minimum length is {$minLength} characters";
        }
        
        if (strlen($value) > $maxLength) {
            $this->errors[] = "Maximum length is {$maxLength} characters";
        }
        
        return $value;
    }
    
    // Validate email
    public function validateEmail($email, $required = true) {
        if ($required && empty($email)) {
            $this->errors[] = "Email is required";
            return null;
        }
        
        // Sanitize
        $email = filter_var($email, FILTER_SANITIZE_EMAIL);
        
        // Validate
        if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
            $this->errors[] = "Invalid email format";
            return null;
        }
        
        return $email;
    }
    
    // Validate phone number
    public function validatePhone($phone, $required = true) {
        if ($required && empty($phone)) {
            $this->errors[] = "Phone number is required";
            return null;
        }
        
        // Remove all non-digits
        $phone = preg_replace('/[^0-9]/', '', $phone);
        
        // Validate US phone format
        if (strlen($phone) !== 10) {
            $this->errors[] = "Phone number must be 10 digits";
            return null;
        }
        
        // Format: (123) 456-7890
        return sprintf("(%s) %s-%s", 
            substr($phone, 0, 3),
            substr($phone, 3, 3),
            substr($phone, 6, 4)
        );
    }
    
    // Validate integer
    public function validateInteger($value, $min = null, $max = null, $required = true) {
        if ($required && ($value === '' || $value === null)) {
            $this->errors[] = "Field is required";
            return null;
        }
        
        // Convert and validate
        $value = filter_var($value, FILTER_VALIDATE_INT);
        
        if ($value === false) {
            $this->errors[] = "Must be a valid integer";
            return null;
        }
        
        if ($min !== null && $value < $min) {
            $this->errors[] = "Minimum value is {$min}";
        }
        
        if ($max !== null && $value > $max) {
            $this->errors[] = "Maximum value is {$max}";
        }
        
        return $value;
    }
    
    // Validate URL
    public function validateUrl($url, $required = true) {
        if ($required && empty($url)) {
            $this->errors[] = "URL is required";
            return null;
        }
        
        // Add protocol if missing
        if (!preg_match('/^https?:\/\//', $url)) {
            $url = 'http://' . $url;
        }
        
        // Validate
        if (!filter_var($url, FILTER_VALIDATE_URL)) {
            $this->errors[] = "Invalid URL format";
            return null;
        }
        
        return $url;
    }
    
    // Validate date
    public function validateDate($date, $format = 'Y-m-d', $required = true) {
        if ($required && empty($date)) {
            $this->errors[] = "Date is required";
            return null;
        }
        
        $dateTime = DateTime::createFromFormat($format, $date);
        
        if (!$dateTime || $dateTime->format($format) !== $date) {
            $this->errors[] = "Invalid date format. Expected: {$format}";
            return null;
        }
        
        return $dateTime->format($format);
    }
    
    // Validate password strength
    public function validatePassword($password, $minLength = 8) {
        if (empty($password)) {
            $this->errors[] = "Password is required";
            return null;
        }
        
        if (strlen($password) < $minLength) {
            $this->errors[] = "Password must be at least {$minLength} characters";
        }
        
        if (!preg_match('/[A-Z]/', $password)) {
            $this->errors[] = "Password must contain at least one uppercase letter";
        }
        
        if (!preg_match('/[a-z]/', $password)) {
            $this->errors[] = "Password must contain at least one lowercase letter";
        }
        
        if (!preg_match('/\d/', $password)) {
            $this->errors[] = "Password must contain at least one number";
        }
        
        if (!preg_match('/[^a-zA-Z\d]/', $password)) {
            $this->errors[] = "Password must contain at least one special character";
        }
        
        return empty($this->errors) ? $password : null;
    }
    
    // Get validation errors
    public function getErrors() {
        return $this->errors;
    }
    
    // Check if validation passed
    public function isValid() {
        return empty($this->errors);
    }
    
    // Clear errors
    public function clearErrors() {
        $this->errors = [];
    }
}

// Usage example
if ($_POST) {
    $validator = new InputValidator();
    
    $name = $validator->validateString($_POST['name'], 2, 50);
    $email = $validator->validateEmail($_POST['email']);
    $phone = $validator->validatePhone($_POST['phone']);
    $age = $validator->validateInteger($_POST['age'], 18, 120);
    $website = $validator->validateUrl($_POST['website'], false); // Optional
    $birthday = $validator->validateDate($_POST['birthday']);
    $password = $validator->validatePassword($_POST['password']);
    
    if ($validator->isValid()) {
        echo "All data is valid!";
        
        // Process the validated data
        $userData = [
            'name' => $name,
            'email' => $email,
            'phone' => $phone,
            'age' => $age,
            'website' => $website,
            'birthday' => $birthday,
            'password' => password_hash($password, PASSWORD_DEFAULT)
        ];
        
        // Save to database using prepared statements
    } else {
        $errors = $validator->getErrors();
        foreach ($errors as $error) {
            echo "<div class='error'>{$error}</div>";
        }
    }
}
?>
```
</details>
<details>
<summary><strong>48. How do you implement rate limiting in PHP?</strong></summary>

**Answer:**
Rate limiting prevents abuse by limiting the number of requests a user can make within a time period. This can be implemented using session data, database storage, or caching systems like Redis.[^1]

**Example:**

```php
<?php
class RateLimiter {
    private $storage;
    private $maxRequests;
    private $timeWindow;
    
    public function __construct($maxRequests = 100, $timeWindow = 3600) { // 100 requests per hour
        $this->maxRequests = $maxRequests;
        $this->timeWindow = $timeWindow;
        $this->storage = new ArrayStorage(); // Use Redis or database in production
    }
    
    // Check if request is allowed
    public function isAllowed($identifier) {
        $key = "rate_limit:" . $identifier;
        $current = time();
        
        // Get existing requests
        $requests = $this->storage->get($key, []);
        
        // Remove old requests outside time window
        $requests = array_filter($requests, function($timestamp) use ($current) {
            return ($current - $timestamp) < $this->timeWindow;
        });
        
        // Check if limit exceeded
        if (count($requests) >= $this->maxRequests) {
            return false;
        }
        
        // Add current request
        $requests[] = $current;
        $this->storage->set($key, $requests, $this->timeWindow);
        
        return true;
    }
    
    // Get rate limit status
    public function getStatus($identifier) {
        $key = "rate_limit:" . $identifier;
        $current = time();
        $requests = $this->storage->get($key, []);
        
        // Remove old requests
        $requests = array_filter($requests, function($timestamp) use ($current) {
            return ($current - $timestamp) < $this->timeWindow;
        });
        
        $remaining = max(0, $this->maxRequests - count($requests));
        $resetTime = empty($requests) ? $current : min($requests) + $this->timeWindow;
        
        return [
            'requests_made' => count($requests),
            'requests_remaining' => $remaining,
            'reset_time' => $resetTime,
            'reset_in_seconds' => $resetTime - $current
        ];
    }
    
    // Block request and send appropriate headers
    public function blockRequest($identifier

<div style="text-align: center">⁂</div>

[^1]: https://www.geeksforgeeks.org/php/php-interview-questions-and-answers/
[^2]: https://www.interviewbit.com/php-interview-questions/
[^3]: https://www.simplilearn.com/php-interview-questions-article
[^4]: https://www.toptal.com/php/interview-questions
[^5]: https://www.hirist.tech/blog/top-50-php-interview-questions-and-answers/
[^6]: https://www.vskills.in/certification/blog/top-100-php-interview-questions-2025/
[^7]: https://www.iotaacademy.in/post/top-50-php-interview-questions-and-answers
[^8]: https://dev.to/robin-ivi/top-50-php-interview-questions-4p69
[^9]: https://blog.imocha.io/php-interview-questions-and-answers```

