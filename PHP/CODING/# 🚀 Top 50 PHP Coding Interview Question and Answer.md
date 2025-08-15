# 🚀 Top 50 PHP Coding Interview Questions & Solutions

> *A comprehensive guide to ace your PHP coding interviews with detailed solutions and explanations*

---

## 📚 Table of Contents

1. [Array & String Problems](#-array--string-problems)
2. [Mathematical Problems](#-mathematical-problems)
3. [Sorting & Searching](#-sorting--searching)
4. [Data Structure Problems](#-data-structure-problems)
5. [Object-Oriented Programming](#-object-oriented-programming)
6. [Advanced Concepts](#-advanced-concepts)

---

## 🔢 Array & String Problems

### 1. 🎯 **Two Sum Problem**
*Find two numbers in an array that add up to a target sum*

```php
function twoSum($nums, $target) {
    $map = [];
    for ($i = 0; $i < count($nums); $i++) {
        $complement = $target - $nums[$i];
        if (isset($map[$complement])) {
            return [$map[$complement], $i];
        }
        $map[$nums[$i]] = $i;
    }
    return [];
}

// Example usage
$nums = [2, 7, 11, 15];
$target = 9;
print_r(twoSum($nums, $target)); // Output: [0, 1]
```

### 2. 🔄 **Reverse a String**
*Reverse a string without using built-in functions*

```php
function reverseString($str) {
    $left = 0;
    $right = strlen($str) - 1;
    $chars = str_split($str);
    
    while ($left < $right) {
        $temp = $chars[$left];
        $chars[$left] = $chars[$right];
        $chars[$right] = $temp;
        $left++;
        $right--;
    }
    
    return implode('', $chars);
}

// Example usage
echo reverseString("hello"); // Output: "olleh"
```

### 3. ✅ **Valid Palindrome**
*Check if a string is a palindrome*

```php
function isPalindrome($s) {
    $s = strtolower(preg_replace('/[^A-Za-z0-9]/', '', $s));
    $left = 0;
    $right = strlen($s) - 1;
    
    while ($left < $right) {
        if ($s[$left] !== $s[$right]) {
            return false;
        }
        $left++;
        $right--;
    }
    return true;
}

// Example usage
var_dump(isPalindrome("A man, a plan, a canal: Panama")); // Output: true
```

### 4. 🔍 **Find Maximum Element**
*Find the maximum element in an array*

```php
function findMax($arr) {
    if (empty($arr)) return null;
    
    $max = $arr[0];
    for ($i = 1; $i < count($arr); $i++) {
        if ($arr[$i] > $max) {
            $max = $arr[$i];
        }
    }
    return $max;
}

// Example usage
$arr = [3, 1, 4, 1, 5, 9, 2, 6];
echo findMax($arr); // Output: 9
```

### 5. 🔢 **Count Vowels**
*Count the number of vowels in a string*

```php
function countVowels($str) {
    $vowels = ['a', 'e', 'i', 'o', 'u'];
    $count = 0;
    $str = strtolower($str);
    
    for ($i = 0; $i < strlen($str); $i++) {
        if (in_array($str[$i], $vowels)) {
            $count++;
        }
    }
    return $count;
}

// Example usage
echo countVowels("Hello World"); // Output: 3
```

### 6. 🗑️ **Remove Duplicates**
*Remove duplicates from an array*

```php
function removeDuplicates(&$nums) {
    if (count($nums) <= 1) return count($nums);
    
    $writeIndex = 1;
    for ($i = 1; $i < count($nums); $i++) {
        if ($nums[$i] !== $nums[$i-1]) {
            $nums[$writeIndex] = $nums[$i];
            $writeIndex++;
        }
    }
    return $writeIndex;
}

// Example usage
$nums = [1, 1, 2, 2, 3, 4, 4];
$length = removeDuplicates($nums);
echo "New length: " . $length; // Output: 4
```

### 7. 🎪 **Anagram Check**
*Check if two strings are anagrams*

```php
function isAnagram($s, $t) {
    if (strlen($s) !== strlen($t)) return false;
    
    $charCount = [];
    
    // Count characters in first string
    for ($i = 0; $i < strlen($s); $i++) {
        $char = $s[$i];
        $charCount[$char] = ($charCount[$char] ?? 0) + 1;
    }
    
    // Decrement count for second string
    for ($i = 0; $i < strlen($t); $i++) {
        $char = $t[$i];
        if (!isset($charCount[$char]) || $charCount[$char] === 0) {
            return false;
        }
        $charCount[$char]--;
    }
    
    return true;
}

// Example usage
var_dump(isAnagram("listen", "silent")); // Output: true
```

### 8. 🔄 **Rotate Array**
*Rotate array to the right by k steps*

```php
function rotate(&$nums, $k) {
    $n = count($nums);
    $k = $k % $n;
    
    // Reverse entire array
    reverse($nums, 0, $n - 1);
    // Reverse first k elements
    reverse($nums, 0, $k - 1);
    // Reverse remaining elements
    reverse($nums, $k, $n - 1);
}

function reverse(&$nums, $start, $end) {
    while ($start < $end) {
        $temp = $nums[$start];
        $nums[$start] = $nums[$end];
        $nums[$end] = $temp;
        $start++;
        $end--;
    }
}

// Example usage
$nums = [1, 2, 3, 4, 5, 6, 7];
rotate($nums, 3);
print_r($nums); // Output: [5, 6, 7, 1, 2, 3, 4]
```

### 9. 📊 **Missing Number**
*Find the missing number in an array of consecutive integers*

```php
function missingNumber($nums) {
    $n = count($nums);
    $expectedSum = $n * ($n + 1) / 2;
    $actualSum = array_sum($nums);
    return $expectedSum - $actualSum;
}

// Alternative using XOR
function missingNumberXOR($nums) {
    $missing = count($nums);
    for ($i = 0; $i < count($nums); $i++) {
        $missing ^= $i ^ $nums[$i];
    }
    return $missing;
}

// Example usage
$nums = [3, 0, 1];
echo missingNumber($nums); // Output: 2
```

### 10. 🏠 **First Unique Character**
*Find the first non-repeating character in a string*

```php
function firstUniqChar($s) {
    $charCount = [];
    
    // Count frequency of each character
    for ($i = 0; $i < strlen($s); $i++) {
        $char = $s[$i];
        $charCount[$char] = ($charCount[$char] ?? 0) + 1;
    }
    
    // Find first character with count 1
    for ($i = 0; $i < strlen($s); $i++) {
        if ($charCount[$s[$i]] === 1) {
            return $i;
        }
    }
    
    return -1;
}

// Example usage
echo firstUniqChar("leetcode"); // Output: 0 (character 'l')
```

---

## ➕ Mathematical Problems

### 11. 🧮 **Fibonacci Sequence**
*Generate Fibonacci numbers*

```php
function fibonacci($n) {
    if ($n <= 1) return $n;
    
    $a = 0;
    $b = 1;
    
    for ($i = 2; $i <= $n; $i++) {
        $temp = $a + $b;
        $a = $b;
        $b = $temp;
    }
    
    return $b;
}

// Recursive approach
function fibonacciRecursive($n) {
    if ($n <= 1) return $n;
    return fibonacciRecursive($n - 1) + fibonacciRecursive($n - 2);
}

// Example usage
echo fibonacci(10); // Output: 55
```

### 12. 🔢 **Prime Number Check**
*Check if a number is prime*

```php
function isPrime($n) {
    if ($n <= 1) return false;
    if ($n <= 3) return true;
    if ($n % 2 === 0 || $n % 3 === 0) return false;
    
    for ($i = 5; $i * $i <= $n; $i += 6) {
        if ($n % $i === 0 || $n % ($i + 2) === 0) {
            return false;
        }
    }
    
    return true;
}

// Example usage
var_dump(isPrime(17)); // Output: true
var_dump(isPrime(15)); // Output: false
```

### 13. 🔄 **Factorial**
*Calculate factorial of a number*

```php
function factorial($n) {
    if ($n <= 1) return 1;
    return $n * factorial($n - 1);
}

// Iterative approach
function factorialIterative($n) {
    $result = 1;
    for ($i = 2; $i <= $n; $i++) {
        $result *= $i;
    }
    return $result;
}

// Example usage
echo factorial(5); // Output: 120
```

### 14. 🔢 **GCD (Greatest Common Divisor)**
*Find GCD of two numbers*

```php
function gcd($a, $b) {
    while ($b !== 0) {
        $temp = $b;
        $b = $a % $b;
        $a = $temp;
    }
    return $a;
}

// Recursive approach
function gcdRecursive($a, $b) {
    if ($b === 0) return $a;
    return gcdRecursive($b, $a % $b);
}

// Example usage
echo gcd(48, 18); // Output: 6
```

### 15. ⚡ **Power of Number**
*Calculate power efficiently*

```php
function power($base, $exponent) {
    if ($exponent === 0) return 1;
    if ($exponent < 0) return 1 / power($base, -$exponent);
    
    if ($exponent % 2 === 0) {
        $half = power($base, $exponent / 2);
        return $half * $half;
    } else {
        return $base * power($base, $exponent - 1);
    }
}

// Example usage
echo power(2, 10); // Output: 1024
```

---

## 🔍 Sorting & Searching

### 16. 🫧 **Bubble Sort**
*Sort array using bubble sort*

```php
function bubbleSort($arr) {
    $n = count($arr);
    for ($i = 0; $i < $n - 1; $i++) {
        $swapped = false;
        for ($j = 0; $j < $n - $i - 1; $j++) {
            if ($arr[$j] > $arr[$j + 1]) {
                // Swap elements
                $temp = $arr[$j];
                $arr[$j] = $arr[$j + 1];
                $arr[$j + 1] = $temp;
                $swapped = true;
            }
        }
        if (!$swapped) break; // Optimization
    }
    return $arr;
}

// Example usage
$arr = [64, 34, 25, 12, 22, 11, 90];
print_r(bubbleSort($arr));
```

### 17. 🎯 **Binary Search**
*Search element in sorted array*

```php
function binarySearch($arr, $target) {
    $left = 0;
    $right = count($arr) - 1;
    
    while ($left <= $right) {
        $mid = intval(($left + $right) / 2);
        
        if ($arr[$mid] === $target) {
            return $mid;
        } elseif ($arr[$mid] < $target) {
            $left = $mid + 1;
        } else {
            $right = $mid - 1;
        }
    }
    
    return -1;
}

// Example usage
$arr = [2, 3, 4, 10, 40];
echo binarySearch($arr, 10); // Output: 3
```

### 18. 🚀 **Quick Sort**
*Sort array using quick sort*

```php
function quickSort($arr) {
    if (count($arr) < 2) return $arr;
    
    $pivot = $arr[0];
    $less = [];
    $greater = [];
    
    for ($i = 1; $i < count($arr); $i++) {
        if ($arr[$i] <= $pivot) {
            $less[] = $arr[$i];
        } else {
            $greater[] = $arr[$i];
        }
    }
    
    return array_merge(quickSort($less), [$pivot], quickSort($greater));
}

// Example usage
$arr = [3, 1, 4, 1, 5, 9, 2, 6];
print_r(quickSort($arr));
```

### 19. 🔀 **Merge Sort**
*Sort array using merge sort*

```php
function mergeSort($arr) {
    if (count($arr) <= 1) return $arr;
    
    $mid = intval(count($arr) / 2);
    $left = array_slice($arr, 0, $mid);
    $right = array_slice($arr, $mid);
    
    return merge(mergeSort($left), mergeSort($right));
}

function merge($left, $right) {
    $result = [];
    $i = $j = 0;
    
    while ($i < count($left) && $j < count($right)) {
        if ($left[$i] <= $right[$j]) {
            $result[] = $left[$i++];
        } else {
            $result[] = $right[$j++];
        }
    }
    
    while ($i < count($left)) $result[] = $left[$i++];
    while ($j < count($right)) $result[] = $right[$j++];
    
    return $result;
}

// Example usage
$arr = [3, 1, 4, 1, 5, 9, 2, 6];
print_r(mergeSort($arr));
```

### 20. 📍 **Find Peak Element**
*Find peak element in array*

```php
function findPeakElement($nums) {
    $left = 0;
    $right = count($nums) - 1;
    
    while ($left < $right) {
        $mid = intval(($left + $right) / 2);
        
        if ($nums[$mid] > $nums[$mid + 1]) {
            $right = $mid;
        } else {
            $left = $mid + 1;
        }
    }
    
    return $left;
}

// Example usage
$nums = [1, 2, 3, 1];
echo findPeakElement($nums); // Output: 2
```

---

## 🏗️ Data Structure Problems

### 21. 📚 **Stack Implementation**
*Implement stack using array*

```php
class Stack {
    private $items = [];
    
    public function push($item) {
        array_push($this->items, $item);
    }
    
    public function pop() {
        if ($this->isEmpty()) {
            throw new Exception("Stack is empty");
        }
        return array_pop($this->items);
    }
    
    public function peek() {
        if ($this->isEmpty()) {
            throw new Exception("Stack is empty");
        }
        return end($this->items);
    }
    
    public function isEmpty() {
        return empty($this->items);
    }
    
    public function size() {
        return count($this->items);
    }
}

// Example usage
$stack = new Stack();
$stack->push(1);
$stack->push(2);
echo $stack->pop(); // Output: 2
```

### 22. 🚶 **Queue Implementation**
*Implement queue using array*

```php
class Queue {
    private $items = [];
    
    public function enqueue($item) {
        array_push($this->items, $item);
    }
    
    public function dequeue() {
        if ($this->isEmpty()) {
            throw new Exception("Queue is empty");
        }
        return array_shift($this->items);
    }
    
    public function front() {
        if ($this->isEmpty()) {
            throw new Exception("Queue is empty");
        }
        return reset($this->items);
    }
    
    public function isEmpty() {
        return empty($this->items);
    }
    
    public function size() {
        return count($this->items);
    }
}

// Example usage
$queue = new Queue();
$queue->enqueue(1);
$queue->enqueue(2);
echo $queue->dequeue(); // Output: 1
```

### 23. 🌳 **Binary Tree Node**
*Basic binary tree implementation*

```php
class TreeNode {
    public $val;
    public $left;
    public $right;
    
    public function __construct($val = 0, $left = null, $right = null) {
        $this->val = $val;
        $this->left = $left;
        $this->right = $right;
    }
}

function inorderTraversal($root) {
    $result = [];
    if ($root !== null) {
        $result = array_merge($result, inorderTraversal($root->left));
        $result[] = $root->val;
        $result = array_merge($result, inorderTraversal($root->right));
    }
    return $result;
}

// Example usage
$root = new TreeNode(1);
$root->right = new TreeNode(2);
$root->right->left = new TreeNode(3);
print_r(inorderTraversal($root)); // Output: [1, 3, 2]
```

### 24. 🔗 **Linked List Implementation**
*Basic linked list with operations*

```php
class ListNode {
    public $val;
    public $next;
    
    public function __construct($val = 0, $next = null) {
        $this->val = $val;
        $this->next = $next;
    }
}

class LinkedList {
    private $head;
    
    public function insert($val) {
        $newNode = new ListNode($val);
        $newNode->next = $this->head;
        $this->head = $newNode;
    }
    
    public function delete($val) {
        if ($this->head === null) return;
        
        if ($this->head->val === $val) {
            $this->head = $this->head->next;
            return;
        }
        
        $current = $this->head;
        while ($current->next !== null && $current->next->val !== $val) {
            $current = $current->next;
        }
        
        if ($current->next !== null) {
            $current->next = $current->next->next;
        }
    }
    
    public function display() {
        $values = [];
        $current = $this->head;
        while ($current !== null) {
            $values[] = $current->val;
            $current = $current->next;
        }
        return $values;
    }
}

// Example usage
$list = new LinkedList();
$list->insert(1);
$list->insert(2);
$list->insert(3);
print_r($list->display()); // Output: [3, 2, 1]
```

### 25. 🔄 **Reverse Linked List**
*Reverse a singly linked list*

```php
function reverseList($head) {
    $prev = null;
    $current = $head;
    
    while ($current !== null) {
        $next = $current->next;
        $current->next = $prev;
        $prev = $current;
        $current = $next;
    }
    
    return $prev;
}

// Recursive approach
function reverseListRecursive($head) {
    if ($head === null || $head->next === null) {
        return $head;
    }
    
    $reversedHead = reverseListRecursive($head->next);
    $head->next->next = $head;
    $head->next = null;
    
    return $reversedHead;
}
```

---

## 🎯 Object-Oriented Programming

### 26. 🏦 **Bank Account Class**
*Implement a bank account with basic operations*

```php
class BankAccount {
    private $accountNumber;
    private $balance;
    private $accountHolder;
    
    public function __construct($accountNumber, $accountHolder, $initialBalance = 0) {
        $this->accountNumber = $accountNumber;
        $this->accountHolder = $accountHolder;
        $this->balance = $initialBalance;
    }
    
    public function deposit($amount) {
        if ($amount > 0) {
            $this->balance += $amount;
            return "Deposited $amount. New balance: " . $this->balance;
        }
        return "Invalid deposit amount";
    }
    
    public function withdraw($amount) {
        if ($amount > 0 && $amount <= $this->balance) {
            $this->balance -= $amount;
            return "Withdrawn $amount. New balance: " . $this->balance;
        }
        return "Invalid withdrawal amount or insufficient funds";
    }
    
    public function getBalance() {
        return $this->balance;
    }
    
    public function getAccountInfo() {
        return "Account: {$this->accountNumber}, Holder: {$this->accountHolder}, Balance: {$this->balance}";
    }
}

// Example usage
$account = new BankAccount("12345", "John Doe", 1000);
echo $account->deposit(500) . "\n";
echo $account->withdraw(200) . "\n";
echo $account->getAccountInfo();
```

### 27. 🐕 **Animal Inheritance**
*Demonstrate inheritance with animal classes*

```php
abstract class Animal {
    protected $name;
    protected $age;
    
    public function __construct($name, $age) {
        $this->name = $name;
        $this->age = $age;
    }
    
    abstract public function makeSound();
    
    public function getInfo() {
        return "Name: {$this->name}, Age: {$this->age}";
    }
    
    public function sleep() {
        return "{$this->name} is sleeping";
    }
}

class Dog extends Animal {
    private $breed;
    
    public function __construct($name, $age, $breed) {
        parent::__construct($name, $age);
        $this->breed = $breed;
    }
    
    public function makeSound() {
        return "Woof! Woof!";
    }
    
    public function fetch() {
        return "{$this->name} is fetching the ball";
    }
}

class Cat extends Animal {
    public function makeSound() {
        return "Meow!";
    }
    
    public function climb() {
        return "{$this->name} is climbing a tree";
    }
}

// Example usage
$dog = new Dog("Buddy", 3, "Golden Retriever");
$cat = new Cat("Whiskers", 2);

echo $dog->makeSound() . "\n"; // Output: Woof! Woof!
echo $cat->makeSound() . "\n"; // Output: Meow!
```

### 28. 🛒 **Shopping Cart**
*Implement a shopping cart system*

```php
class Product {
    public $id;
    public $name;
    public $price;
    
    public function __construct($id, $name, $price) {
        $this->id = $id;
        $this->name = $name;
        $this->price = $price;
    }
}

class CartItem {
    public $product;
    public $quantity;
    
    public function __construct($product, $quantity) {
        $this->product = $product;
        $this->quantity = $quantity;
    }
    
    public function getTotal() {
        return $this->product->price * $this->quantity;
    }
}

class ShoppingCart {
    private $items = [];
    
    public function addItem($product, $quantity) {
        $productId = $product->id;
        
        if (isset($this->items[$productId])) {
            $this->items[$productId]->quantity += $quantity;
        } else {
            $this->items[$productId] = new CartItem($product, $quantity);
        }
    }
    
    public function removeItem($productId) {
        if (isset($this->items[$productId])) {
            unset($this->items[$productId]);
        }
    }
    
    public function getTotal() {
        $total = 0;
        foreach ($this->items as $item) {
            $total += $item->getTotal();
        }
        return $total;
    }
    
    public function getItems() {
        return $this->items;
    }
}

// Example usage
$product1 = new Product(1, "Laptop", 999.99);
$product2 = new Product(2, "Mouse", 29.99);

$cart = new ShoppingCart();
$cart->addItem($product1, 1);
$cart->addItem($product2, 2);

echo "Total: $" . $cart->getTotal(); // Output: Total: $1059.97
```

### 29. 🎨 **Shape Interface**
*Implement shapes with interface*

```php
interface ShapeInterface {
    public function calculateArea();
    public function calculatePerimeter();
}

class Rectangle implements ShapeInterface {
    private $width;
    private $height;
    
    public function __construct($width, $height) {
        $this->width = $width;
        $this->height = $height;
    }
    
    public function calculateArea() {
        return $this->width * $this->height;
    }
    
    public function calculatePerimeter() {
        return 2 * ($this->width + $this->height);
    }
}

class Circle implements ShapeInterface {
    private $radius;
    
    public function __construct($radius) {
        $this->radius = $radius;
    }
    
    public function calculateArea() {
        return pi() * $this->radius * $this->radius;
    }
    
    public function calculatePerimeter() {
        return 2 * pi() * $this->radius;
    }
}

// Example usage
$rectangle = new Rectangle(5, 3);
$circle = new Circle(4);

echo "Rectangle Area: " . $rectangle->calculateArea() . "\n"; // Output: 15
echo "Circle Area: " . round($circle->calculateArea(), 2) . "\n"; // Output: 50.27
```

### 30. 🏭 **Singleton Pattern**
*Implement singleton design pattern*

```php
class Database {
    private static $instance = null;
    private $connection;
    
    private function __construct() {
        // Private constructor to prevent direct instantiation
        $this->connection = "Database connection established";
    }
    
    private function __clone() {
        // Prevent cloning
    }
    
    public function __wakeup() {
        // Prevent unserialization
        throw new Exception("Cannot unserialize singleton");
    }
    
    public static function getInstance() {
        if (self::$instance === null) {
            self::$instance = new self();
        }
        return self::$instance;
    }
    
    public function getConnection() {
        return $this->connection;
    }
    
    public function query($sql) {
        return "Executing query: " . $sql;
    }
}

// Example usage
$db1 = Database::getInstance();
$db2 = Database::getInstance();

var_dump($db1 === $db2); // Output: true (same instance)
echo $db1->query("SELECT * FROM users");
```

---

## 🚀 Advanced Concepts

### 31. 📈 **LRU Cache**
*Implement Least Recently Used cache*

```php
class LRUCache {
    private $capacity;
    private $cache = [];
    private $order = [];
    
    public function __construct($capacity) {
        $this->capacity = $capacity;
    }
    
    public function get($key) {
        if (!isset($this->cache[$key])) {
            return -1;
        }
        
        // Move to end (most recently used)
        $this->moveToEnd($key);
        return $this->cache[$key];
    }
    
    public function put($key, $value) {
        if (isset($this->cache[$key])) {
            // Update existing key
            $this->cache[$key] = $value;
            $this->moveToEnd($key);
        } else {
            // Add new key
            if (count($this->cache) >= $this->capacity) {
                // Remove least recently used
                $lru = array_shift($this->order);
                unset($this->cache[$lru]);
            }
            
            $this->cache[$key] = $value;
            $this->order[] = $key;
        }
    }
    
    private function moveToEnd($key) {
        $index = array_search($key, $this->order);
        if ($index !== false) {
            unset($this->order[$index]);
            $this->order = array_values($this->order);
        }
        $this->order[] = $key;
    }
}

// Example usage
$lru = new LRUCache(2);
$lru->put(1, 1);
$lru->put(2, 2);
echo $lru->get(1) . "\n"; // Output: 1
$lru->put(3, 3); // Evicts key 2
echo $lru->get(2) . "\n"; // Output: -1 (not found)
```

### 32. 🌊 **Flood Fill Algorithm**
*Implement flood fill algorithm (like paint bucket tool)*

```php
function floodFill($image, $sr, $sc, $newColor) {
    if (empty($image) || empty($image[0])) return $image;
    
    $originalColor = $image[$sr][$sc];
    if ($originalColor === $newColor) return $image;
    
    floodFillDFS($image, $sr, $sc, $originalColor, $newColor);
    return $image;
}

function floodFillDFS(&$image, $row, $col, $originalColor, $newColor) {
    if ($row < 0 || $row >= count($image) || 
        $col < 0 || $col >= count($image[0]) || 
        $image[$row][$col] !== $originalColor) {
        return;
    }
    
    $image[$row][$col] = $newColor;
    
    // Check all 4 directions
    floodFillDFS($image, $row - 1, $col, $originalColor, $newColor); // Up
    floodFillDFS($image, $row + 1, $col, $originalColor, $newColor); // Down
    floodFillDFS($image, $row, $col - 1, $originalColor, $newColor); // Left
    floodFillDFS($image, $row, $col + 1, $originalColor, $newColor); // Right
}

// Example usage
$image = [
    [1, 1, 1],
    [1, 1, 0],
    [1, 0, 1]
];
$result = floodFill($image, 1, 1, 2);
print_r($result); // Changes connected 1s to 2s
```

### 33. 🔍 **Binary Tree Search**
*Search for a value in binary search tree*

```php
function searchBST($root, $val) {
    if ($root === null || $root->val === $val) {
        return $root;
    }
    
    if ($val < $root->val) {
        return searchBST($root->left, $val);
    } else {
        return searchBST($root->right, $val);
    }
}

// Iterative approach
function searchBSTIterative($root, $val) {
    while ($root !== null && $root->val !== $val) {
        if ($val < $root->val) {
            $root = $root->left;
        } else {
            $root = $root->right;
        }
    }
    return $root;
}

// Example usage with TreeNode class from previous examples
$root = new TreeNode(4);
$root->left = new TreeNode(2);
$root->right = new TreeNode(7);
$root->left->left = new TreeNode(1);
$root->left->right = new TreeNode(3);

$result = searchBST($root, 2);
echo $result ? $result->val : "Not found"; // Output: 2
```

### 34. 🎲 **Generate Parentheses**
*Generate all valid combinations of n pairs of parentheses*

```php
function generateParenthesis($n) {
    $result = [];
    backtrack($result, "", 0, 0, $n);
    return $result;
}

function backtrack(&$result, $current, $open, $close, $max) {
    if (strlen($current) === $max * 2) {
        $result[] = $current;
        return;
    }
    
    if ($open < $max) {
        backtrack($result, $current . "(", $open + 1, $close, $max);
    }
    
    if ($close < $open) {
        backtrack($result, $current . ")", $open, $close + 1, $max);
    }
}

// Example usage
print_r(generateParenthesis(3));
// Output: ["((()))", "(()())", "(())()", "()(())", "()()()"]
```

### 35. 🏔️ **Maximum Subarray Sum (Kadane's Algorithm)**
*Find maximum sum of contiguous subarray*

```php
function maxSubArray($nums) {
    if (empty($nums)) return 0;
    
    $maxSum = $nums[0];
    $currentSum = $nums[0];
    
    for ($i = 1; $i < count($nums); $i++) {
        $currentSum = max($nums[$i], $currentSum + $nums[$i]);
        $maxSum = max($maxSum, $currentSum);
    }
    
    return $maxSum;
}

// Return the actual subarray
function maxSubArrayWithIndices($nums) {
    if (empty($nums)) return [];
    
    $maxSum = $nums[0];
    $currentSum = $nums[0];
    $start = 0;
    $end = 0;
    $tempStart = 0;
    
    for ($i = 1; $i < count($nums); $i++) {
        if ($nums[$i] > $currentSum + $nums[$i]) {
            $currentSum = $nums[$i];
            $tempStart = $i;
        } else {
            $currentSum = $currentSum + $nums[$i];
        }
        
        if ($currentSum > $maxSum) {
            $maxSum = $currentSum;
            $start = $tempStart;
            $end = $i;
        }
    }
    
    return [
        'sum' => $maxSum,
        'subarray' => array_slice($nums, $start, $end - $start + 1)
    ];
}

// Example usage
$nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4];
echo maxSubArray($nums); // Output: 6 ([4, -1, 2, 1])
```

### 36. 🔄 **Valid Parentheses**
*Check if parentheses are valid and balanced*

```php
function isValid($s) {
    $stack = [];
    $mapping = [')' => '(', '}' => '{', ']' => '['];
    
    for ($i = 0; $i < strlen($s); $i++) {
        $char = $s[$i];
        
        if (isset($mapping[$char])) {
            // Closing bracket
            if (empty($stack) || array_pop($stack) !== $mapping[$char]) {
                return false;
            }
        } else {
            // Opening bracket
            array_push($stack, $char);
        }
    }
    
    return empty($stack);
}

// Example usage
var_dump(isValid("()[]{}"));    // Output: true
var_dump(isValid("([)]"));      // Output: false
var_dump(isValid("{[()]}"));    // Output: true
```

### 37. 🌟 **Longest Common Subsequence**
*Find longest common subsequence using dynamic programming*

```php
function longestCommonSubsequence($text1, $text2) {
    $m = strlen($text1);
    $n = strlen($text2);
    
    // Create DP table
    $dp = array_fill(0, $m + 1, array_fill(0, $n + 1, 0));
    
    for ($i = 1; $i <= $m; $i++) {
        for ($j = 1; $j <= $n; $j++) {
            if ($text1[$i - 1] === $text2[$j - 1]) {
                $dp[$i][$j] = $dp[$i - 1][$j - 1] + 1;
            } else {
                $dp[$i][$j] = max($dp[$i - 1][$j], $dp[$i][$j - 1]);
            }
        }
    }
    
    return $dp[$m][$n];
}

// Get the actual LCS string
function getLCS($text1, $text2) {
    $m = strlen($text1);
    $n = strlen($text2);
    $dp = array_fill(0, $m + 1, array_fill(0, $n + 1, 0));
    
    // Fill DP table
    for ($i = 1; $i <= $m; $i++) {
        for ($j = 1; $j <= $n; $j++) {
            if ($text1[$i - 1] === $text2[$j - 1]) {
                $dp[$i][$j] = $dp[$i - 1][$j - 1] + 1;
            } else {
                $dp[$i][$j] = max($dp[$i - 1][$j], $dp[$i][$j - 1]);
            }
        }
    }
    
    // Reconstruct LCS
    $lcs = "";
    $i = $m;
    $j = $n;
    
    while ($i > 0 && $j > 0) {
        if ($text1[$i - 1] === $text2[$j - 1]) {
            $lcs = $text1[$i - 1] . $lcs;
            $i--;
            $j--;
        } elseif ($dp[$i - 1][$j] > $dp[$i][$j - 1]) {
            $i--;
        } else {
            $j--;
        }
    }
    
    return $lcs;
}

// Example usage
echo longestCommonSubsequence("abcde", "ace"); // Output: 3
echo getLCS("abcde", "ace"); // Output: "ace"
```

### 38. 🏠 **House Robber**
*Maximum money you can rob without robbing adjacent houses*

```php
function rob($nums) {
    if (empty($nums)) return 0;
    if (count($nums) === 1) return $nums[0];
    
    $prev2 = 0; // rob[i-2]
    $prev1 = 0; // rob[i-1]
    
    foreach ($nums as $num) {
        $temp = $prev1;
        $prev1 = max($prev1, $prev2 + $num);
        $prev2 = $temp;
    }
    
    return $prev1;
}

// With DP array (easier to understand)
function robDP($nums) {
    if (empty($nums)) return 0;
    if (count($nums) === 1) return $nums[0];
    
    $n = count($nums);
    $dp = array_fill(0, $n, 0);
    $dp[0] = $nums[0];
    $dp[1] = max($nums[0], $nums[1]);
    
    for ($i = 2; $i < $n; $i++) {
        $dp[$i] = max($dp[$i - 1], $dp[$i - 2] + $nums[$i]);
    }
    
    return $dp[$n - 1];
}

// Example usage
$houses = [2, 7, 9, 3, 1];
echo rob($houses); // Output: 12 (rob houses 0, 2, 4)
```

### 39. 🎯 **Target Sum**
*Find number of ways to assign symbols to make target sum*

```php
function findTargetSumWays($nums, $target) {
    return dfs($nums, 0, 0, $target);
}

function dfs($nums, $index, $currentSum, $target) {
    if ($index === count($nums)) {
        return $currentSum === $target ? 1 : 0;
    }
    
    return dfs($nums, $index + 1, $currentSum + $nums[$index], $target) +
           dfs($nums, $index + 1, $currentSum - $nums[$index], $target);
}

// Optimized with memoization
function findTargetSumWaysDP($nums, $target) {
    $memo = [];
    return dfsWithMemo($nums, 0, 0, $target, $memo);
}

function dfsWithMemo($nums, $index, $sum, $target, &$memo) {
    $key = $index . ',' . $sum;
    
    if (isset($memo[$key])) {
        return $memo[$key];
    }
    
    if ($index === count($nums)) {
        return $sum === $target ? 1 : 0;
    }
    
    $memo[$key] = dfsWithMemo($nums, $index + 1, $sum + $nums[$index], $target, $memo) +
                  dfsWithMemo($nums, $index + 1, $sum - $nums[$index], $target, $memo);
    
    return $memo[$key];
}

// Example usage
$nums = [1, 1, 1, 1, 1];
echo findTargetSumWays($nums, 3); // Output: 5
```

### 40. 🔄 **Rotate Matrix**
*Rotate n×n matrix 90 degrees clockwise*

```php
function rotate(&$matrix) {
    $n = count($matrix);
    
    // Transpose the matrix
    for ($i = 0; $i < $n; $i++) {
        for ($j = $i; $j < $n; $j++) {
            $temp = $matrix[$i][$j];
            $matrix[$i][$j] = $matrix[$j][$i];
            $matrix[$j][$i] = $temp;
        }
    }
    
    // Reverse each row
    for ($i = 0; $i < $n; $i++) {
        $matrix[$i] = array_reverse($matrix[$i]);
    }
}

// Alternative: rotate in place with layers
function rotateInPlace(&$matrix) {
    $n = count($matrix);
    
    for ($layer = 0; $layer < $n / 2; $layer++) {
        $first = $layer;
        $last = $n - 1 - $layer;
        
        for ($i = $first; $i < $last; $i++) {
            $offset = $i - $first;
            
            // Save top element
            $top = $matrix[$first][$i];
            
            // Top = Left
            $matrix[$first][$i] = $matrix[$last - $offset][$first];
            
            // Left = Bottom
            $matrix[$last - $offset][$first] = $matrix[$last][$last - $offset];
            
            // Bottom = Right
            $matrix[$last][$last - $offset] = $matrix[$i][$last];
            
            // Right = Top
            $matrix[$i][$last] = $top;
        }
    }
}

// Example usage
$matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
];
rotate($matrix);
print_r($matrix);
// Output: [[7,4,1],[8,5,2],[9,6,3]]
```

### 41. 🎨 **Word Break**
*Check if string can be segmented into dictionary words*

```php
function wordBreak($s, $wordDict) {
    $n = strlen($s);
    $dp = array_fill(0, $n + 1, false);
    $dp[0] = true;
    $wordSet = array_flip($wordDict); // For O(1) lookup
    
    for ($i = 1; $i <= $n; $i++) {
        for ($j = 0; $j < $i; $j++) {
            if ($dp[$j] && isset($wordSet[substr($s, $j, $i - $j)])) {
                $dp[$i] = true;
                break;
            }
        }
    }
    
    return $dp[$n];
}

// Return all possible word breaks
function wordBreakII($s, $wordDict) {
    $memo = [];
    return wordBreakHelper($s, $wordDict, $memo);
}

function wordBreakHelper($s, $wordDict, &$memo) {
    if (isset($memo[$s])) {
        return $memo[$s];
    }
    
    if ($s === "") {
        return [""];
    }
    
    $result = [];
    foreach ($wordDict as $word) {
        if (strpos($s, $word) === 0) {
            $suffix = substr($s, strlen($word));
            $suffixBreaks = wordBreakHelper($suffix, $wordDict, $memo);
            
            foreach ($suffixBreaks as $suffixBreak) {
                $result[] = $word . ($suffixBreak === "" ? "" : " " . $suffixBreak);
            }
        }
    }
    
    $memo[$s] = $result;
    return $result;
}

// Example usage
$s = "leetcode";
$wordDict = ["leet", "code"];
var_dump(wordBreak($s, $wordDict)); // Output: true

$s2 = "catsanddog";
$wordDict2 = ["cat", "cats", "and", "sand", "dog"];
print_r(wordBreakII($s2, $wordDict2));
// Output: ["cats and dog", "cat sand dog"]
```

### 42. 🌈 **Trapping Rain Water**
*Calculate how much rain water can be trapped*

```php
function trap($height) {
    if (empty($height)) return 0;
    
    $left = 0;
    $right = count($height) - 1;
    $leftMax = 0;
    $rightMax = 0;
    $water = 0;
    
    while ($left < $right) {
        if ($height[$left] < $height[$right]) {
            if ($height[$left] >= $leftMax) {
                $leftMax = $height[$left];
            } else {
                $water += $leftMax - $height[$left];
            }
            $left++;
        } else {
            if ($height[$right] >= $rightMax) {
                $rightMax = $height[$right];
            } else {
                $water += $rightMax - $height[$right];
            }
            $right--;
        }
    }
    
    return $water;
}

// Alternative approach using arrays
function trapDP($height) {
    if (empty($height)) return 0;
    
    $n = count($height);
    $leftMax = array_fill(0, $n, 0);
    $rightMax = array_fill(0, $n, 0);
    
    // Fill leftMax array
    $leftMax[0] = $height[0];
    for ($i = 1; $i < $n; $i++) {
        $leftMax[$i] = max($leftMax[$i - 1], $height[$i]);
    }
    
    // Fill rightMax array
    $rightMax[$n - 1] = $height[$n - 1];
    for ($i = $n - 2; $i >= 0; $i--) {
        $rightMax[$i] = max($rightMax[$i + 1], $height[$i]);
    }
    
    // Calculate trapped water
    $water = 0;
    for ($i = 0; $i < $n; $i++) {
        $water += max(0, min($leftMax[$i], $rightMax[$i]) - $height[$i]);
    }
    
    return $water;
}

// Example usage
$height = [0, 1, 0, 2, 1, 0, 1, 3, 2, 1, 2, 1];
echo trap($height); // Output: 6
```

### 43. 🔤 **Longest Palindromic Substring**
*Find the longest palindromic substring*

```php
function longestPalindrome($s) {
    if (empty($s)) return "";
    
    $start = 0;
    $maxLen = 1;
    
    for ($i = 0; $i < strlen($s); $i++) {
        // Check for odd length palindromes
        $len1 = expandAroundCenter($s, $i, $i);
        // Check for even length palindromes
        $len2 = expandAroundCenter($s, $i, $i + 1);
        
        $len = max($len1, $len2);
        if ($len > $maxLen) {
            $maxLen = $len;
            $start = $i - intval(($len - 1) / 2);
        }
    }
    
    return substr($s, $start, $maxLen);
}

function expandAroundCenter($s, $left, $right) {
    while ($left >= 0 && $right < strlen($s) && $s[$left] === $s[$right]) {
        $left--;
        $right++;
    }
    return $right - $left - 1;
}

// Dynamic Programming approach
function longestPalindromeDP($s) {
    $n = strlen($s);
    if ($n < 2) return $s;
    
    $dp = array_fill(0, $n, array_fill(0, $n, false));
    $start = 0;
    $maxLen = 1;
    
    // Single characters are palindromes
    for ($i = 0; $i < $n; $i++) {
        $dp[$i][$i] = true;
    }
    
    // Check for two character palindromes
    for ($i = 0; $i < $n - 1; $i++) {
        if ($s[$i] === $s[$i + 1]) {
            $dp[$i][$i + 1] = true;
            $start = $i;
            $maxLen = 2;
        }
    }
    
    // Check for palindromes of length 3 and more
    for ($len = 3; $len <= $n; $len++) {
        for ($i = 0; $i < $n - $len + 1; $i++) {
            $j = $i + $len - 1;
            
            if ($s[$i] === $s[$j] && $dp[$i + 1][$j - 1]) {
                $dp[$i][$j] = true;
                $start = $i;
                $maxLen = $len;
            }
        }
    }
    
    return substr($s, $start, $maxLen);
}

// Example usage
echo longestPalindrome("babad"); // Output: "bab" or "aba"
echo longestPalindrome("cbbd");  // Output: "bb"
```

### 44. 🏁 **Course Schedule**
*Determine if you can finish all courses (topological sort)*

```php
function canFinish($numCourses, $prerequisites) {
    // Build adjacency list
    $graph = array_fill(0, $numCourses, []);
    $inDegree = array_fill(0, $numCourses, 0);
    
    foreach ($prerequisites as $prereq) {
        $course = $prereq[0];
        $dependency = $prereq[1];
        $graph[$dependency][] = $course;
        $inDegree[$course]++;
    }
    
    // Find courses with no dependencies
    $queue = [];
    for ($i = 0; $i < $numCourses; $i++) {
        if ($inDegree[$i] === 0) {
            $queue[] = $i;
        }
    }
    
    $completed = 0;
    
    while (!empty($queue)) {
        $course = array_shift($queue);
        $completed++;
        
        foreach ($graph[$course] as $nextCourse) {
            $inDegree[$nextCourse]--;
            if ($inDegree[$nextCourse] === 0) {
                $queue[] = $nextCourse;
            }
        }
    }
    
    return $completed === $numCourses;
}

// Return the actual course order
function findOrder($numCourses, $prerequisites) {
    $graph = array_fill(0, $numCourses, []);
    $inDegree = array_fill(0, $numCourses, 0);
    
    foreach ($prerequisites as $prereq) {
        $course = $prereq[0];
        $dependency = $prereq[1];
        $graph[$dependency][] = $course;
        $inDegree[$course]++;
    }
    
    $queue = [];
    for ($i = 0; $i < $numCourses; $i++) {
        if ($inDegree[$i] === 0) {
            $queue[] = $i;
        }
    }
    
    $order = [];
    
    while (!empty($queue)) {
        $course = array_shift($queue);
        $order[] = $course;
        
        foreach ($graph[$course] as $nextCourse) {
            $inDegree[$nextCourse]--;
            if ($inDegree[$nextCourse] === 0) {
                $queue[] = $nextCourse;
            }
        }
    }
    
    return count($order) === $numCourses ? $order : [];
}

// Example usage
$prerequisites = [[1,0],[2,0],[3,1],[3,2]];
var_dump(canFinish(4, $prerequisites)); // Output: true
print_r(findOrder(4, $prerequisites)); // Output: [0, 1, 2, 3] or [0, 2, 1, 3]
```

### 45. 🎪 **Permutations**
*Generate all permutations of an array*

```php
function permute($nums) {
    $result = [];
    backtrackPermute($result, [], $nums);
    return $result;
}

function backtrackPermute(&$result, $current, $nums) {
    if (count($current) === count($nums)) {
        $result[] = $current;
        return;
    }
    
    foreach ($nums as $num) {
        if (in_array($num, $current)) continue;
        
        $current[] = $num;
        backtrackPermute($result, $current, $nums);
        array_pop($current);
    }
}

// Handle duplicates
function permuteUnique($nums) {
    sort($nums);
    $result = [];
    $used = array_fill(0, count($nums), false);
    backtrackPermuteUnique($result, [], $nums, $used);
    return $result;
}

function backtrackPermuteUnique(&$result, $current, $nums, &$used) {
    if (count($current) === count($nums)) {
        $result[] = $current;
        return;
    }
    
    for ($i = 0; $i < count($nums); $i++) {
        if ($used[$i] || ($i > 0 && $nums[$i] === $nums[$i - 1] && !$used[$i - 1])) {
            continue;
        }
        
        $used[$i] = true;
        $current[] = $nums[$i];
        backtrackPermuteUnique($result, $current, $nums, $used);
        array_pop($current);
        $used[$i] = false;
    }
}

// Example usage
print_r(permute([1, 2, 3]));
// Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

### 46. 🔍 **Find All Anagrams**
*Find all anagrams of pattern in string*

```php
function findAnagrams($s, $p) {
    if (strlen($s) < strlen($p)) return [];
    
    $result = [];
    $pCount = array_count_values(str_split($p));
    $windowCount = [];
    $left = 0;
    
    for ($right = 0; $right < strlen($s); $right++) {
        $rightChar = $s[$right];
        $windowCount[$rightChar] = ($windowCount[$rightChar] ?? 0) + 1;
        
        // Shrink window if it's larger than pattern
        if ($right - $left + 1 > strlen($p)) {
            $leftChar = $s[$left];
            $windowCount[$leftChar]--;
            if ($windowCount[$leftChar] === 0) {
                unset($windowCount[$leftChar]);
            }
            $left++;
        }
        
        // Check if current window is an anagram
        if ($right - $left + 1 === strlen($p) && $windowCount === $pCount) {
            $result[] = $left;
        }
    }
    
    return $result;
}

// Example usage
$s = "cbaebabacd";
$p = "abc";
print_r(findAnagrams($s, $p)); // Output: [1, 6]
```

### 47. 🌊 **Sliding Window Maximum**
*Find maximum in each sliding window of size k*

```php
function maxSlidingWindow($nums, $k) {
    if (empty($nums) || $k === 0) return [];
    
    $result = [];
    $deque = []; // Store indices
    
    for ($i = 0; $i < count($nums); $i++) {
        // Remove indices that are out of current window
        while (!empty($deque) && $deque[0] < $i - $k + 1) {
            array_shift($deque);
        }
        
        // Remove indices whose corresponding values are smaller than current
        while (!empty($deque) && $nums[end($deque)] < $nums[$i]) {
            array_pop($deque);
        }
        
        $deque[] = $i;
        
        // Add to result when window size is k
        if ($i >= $k - 1) {
            $result[] = $nums[$deque[0]];
        }
    }
    
    return $result;
}

// Example usage
$nums = [1, 3, -1, -3, 5, 3, 6, 7];
$k = 3;
print_r(maxSlidingWindow($nums, $k));
// Output: [3, 3, 5, 5, 6, 7]
```

### 48. 🏗️ **Construct Binary Tree**
*Construct binary tree from preorder and inorder traversal*

```php
function buildTree($preorder, $inorder) {
    if (empty($preorder) || empty($inorder)) return null;
    
    $rootVal = $preorder[0];
    $root = new TreeNode($rootVal);
    
    $rootIndex = array_search($rootVal, $inorder);
    
    $leftInorder = array_slice($inorder, 0, $rootIndex);
    $rightInorder = array_slice($inorder, $rootIndex + 1);
    
    $leftPreorder = array_slice($preorder, 1, count($leftInorder));
    $rightPreorder = array_slice($preorder, count($leftInorder) + 1);
    
    $root->left = buildTree($leftPreorder, $leftInorder);
    $root->right = buildTree($rightPreorder, $rightInorder);
    
    return $root;
}

// Optimized with hashmap
function buildTreeOptimized($preorder, $inorder) {
    $inorderMap = array_flip($inorder);
    $preorderIndex = 0;
    
    return buildTreeHelper($preorder, $inorderMap, $preorderIndex, 0, count($inorder) - 1);
}

function buildTreeHelper($preorder, $inorderMap, &$preorderIndex, $left, $right) {
    if ($left > $right) return null;
    
    $rootVal = $preorder[$preorderIndex++];
    $root = new TreeNode($rootVal);
    
    $rootIndex = $inorderMap[$rootVal];
    
    $root->left = buildTreeHelper($preorder, $inorderMap, $preorderIndex, $left, $rootIndex - 1);
    $root->right = buildTreeHelper($preorder, $inorderMap, $preorderIndex, $rootIndex + 1, $right);
    
    return $root;
}

// Example usage
$preorder = [3, 9, 20, 15, 7];
$inorder = [9, 3, 15, 20, 7];
$root = buildTree($preorder, $inorder);
```

### 49. 🎯 **Combination Sum**
*Find all combinations that sum to target*

```php
function combinationSum($candidates, $target) {
    $result = [];
    sort($candidates);
    backtrackCombination($result, [], $candidates, $target, 0);
    return $result;
}

function backtrackCombination(&$result, $current, $candidates, $target, $start) {
    if ($target === 0) {
        $result[] = $current;
        return;
    }
    
    for ($i = $start; $i < count($candidates); $i++) {
        if ($candidates[$i] > $target) break;
        
        $current[] = $candidates[$i];
        backtrackCombination($result, $current, $candidates, $target - $candidates[$i], $i);
        array_pop($current);
    }
}

// Combination Sum II (with duplicates, each number used once)
function combinationSum2($candidates, $target) {
    $result = [];
    sort($candidates);
    backtrackCombination2($result, [], $candidates, $target, 0);
    return $result;
}

function backtrackCombination2(&$result, $current, $candidates, $target, $start) {
    if ($target === 0) {
        $result[] = $current;
        return;
    }
    
    for ($i = $start; $i < count($candidates); $i++) {
        if ($candidates[$i] > $target) break;
        if ($i > $start && $candidates[$i] === $candidates[$i - 1]) continue;
        
        $current[] = $candidates[$i];
        backtrackCombination2($result, $current, $candidates, $target - $candidates[$i], $i + 1);
        array_pop($current);
    }
}

// Example usage
$candidates = [2, 3, 6, 7];
$target = 7;
print_r(combinationSum($candidates, $target));
// Output: [[2,2,3],[7]]
```

### 50. 🧩 **N-Queens Problem**
*Place N queens on N×N chessboard so they don't attack each other*

```php
function solveNQueens($n) {
    $result = [];
    $board = array_fill(0, $n, array_fill(0, $n, '.'));
    backtrackQueens($result, $board, 0, $n);
    return $result;
}

function backtrackQueens(&$result, &$board, $row, $n) {
    if ($row === $n) {
        $solution = [];
        for ($i = 0; $i < $n; $i++) {
            $solution[] = implode('', $board[$i]);
        }
        $result[] = $solution;
        return;
    }
    
    for ($col = 0; $col < $n; $col++) {
        if (isValidQueenPosition($board, $row, $col, $n)) {
            $board[$row][$col] = 'Q';
            backtrackQueens($result, $board, $row + 1, $n);
            $board[$row][$col] = '.';
        }
    }
}

function isValidQueenPosition($board, $row, $col, $n) {
    // Check column
    for ($i = 0; $i < $row; $i++) {
        if ($board[$i][$col] === 'Q') return false;
    }
    
    // Check diagonal (top-left to bottom-right)
    for ($i = $row - 1, $j = $col - 1; $i >= 0 && $j >= 0; $i--, $j--) {
        if ($board[$i][$j] === 'Q') return false;
    }
    
    // Check diagonal (top-right to bottom-left)
    for ($i = $row - 1, $j = $col + 1; $i >= 0 && $j < $n; $i--, $j++) {
        if ($board[$i][$j] === 'Q') return false;
    }
    
    return true;
}

// Count total solutions
function totalNQueens($n) {
    $count = 0;
    $board = array_fill(0, $n, array_fill(0, $n, '.'));
    backtrackQueensCount($count, $board, 0, $n);
    return $count;
}

function backtrackQueensCount(&$count, &$board, $row, $n) {
    if ($row === $n) {
        $count++;
        return;
    }
    
    for ($col = 0; $col < $n; $col++) {
        if (isValidQueenPosition($board, $row, $col, $n)) {
            $board[$row][$col] = 'Q';
            backtrackQueensCount($count, $board, $row + 1, $n);
            $board[$row][$col] = '.';
        }
    }
}

// Example usage
print_r(solveNQueens(4));
// Output: Solution boards for 4-queens problem
echo "Total solutions for 8-queens: " . totalNQueens(8); // Output: 92
```

---

## 📋 **Bonus: Common Utility Functions**

### 🔧 **Array Manipulation Utilities**

```php
// Remove element in-place
function removeElement(&$nums, $val) {
    $writeIndex = 0;
    for ($i = 0; $i < count($nums); $i++) {
        if ($nums[$i] !== $val) {
            $nums[$writeIndex] = $nums[$i];
            $writeIndex++;
        }
    }
    return $writeIndex;
}

// Merge two sorted arrays
function merge(&$nums1, $m, $nums2, $n) {
    $i = $m - 1;
    $j = $n - 1;
    $k = $m + $n - 1;
    
    while ($i >= 0 && $j >= 0) {
        if ($nums1[$i] > $nums2[$j]) {
            $nums1[$k] = $nums1[$i];
            $i--;
        } else {
            $nums1[$k] = $nums2[$j];
            $j--;
        }
        $k--;
    }
    
    while ($j >= 0) {
        $nums1[$k] = $nums2[$j];
        $j--;
        $k--;
    }
}

// Find intersection of two arrays
function intersect($nums1, $nums2) {
    $map = [];
    $result = [];
    
    foreach ($nums1 as $num) {
        $map[$num] = ($map[$num] ?? 0) + 1;
    }
    
    foreach ($nums2 as $num) {
        if (isset($map[$num]) && $map[$num] > 0) {
            $result[] = $num;
            $map[$num]--;
        }
    }
    
    return $result;
}
```

### 🎲 **String Processing Utilities**

```php
// Check if string has all unique characters
function hasAllUniqueChars($str) {
    return strlen($str) === count(array_unique(str_split($str)));
}

// Compress string (aabcccccaaa -> a2b1c5a3)
function compressString($str) {
    if (empty($str)) return $str;
    
    $compressed = '';
    $count = 1;
    $prev = $str[0];
    
    for ($i = 1; $i < strlen($str); $i++) {
        if ($str[$i] === $prev) {
            $count++;
        } else {
            $compressed .= $prev . $count;
            $prev = $str[$i];
            $count = 1;
        }
    }
    
    $compressed .= $prev . $count;
    return strlen($compressed) < strlen($str) ? $compressed : $str;
}

// Check if one string is rotation of another
function isRotation($s1, $s2) {
    return strlen($s1) === strlen($s2) && strpos($s1 . $s1, $s2) !== false;
}
```

---

## 💡 **Interview Tips & Best Practices**

### 🎯 **Problem-Solving Approach**

1. **🤔 Clarify the Problem**
   - Ask about input constraints
   - Understand expected output format
   - Confirm edge cases

2. **🧠 Think Out Loud**
   - Explain your thought process
   - Discuss trade-offs between solutions
   - Consider time and space complexity

3. **📝 Start with Brute Force**
   - Get a working solution first
   - Then optimize if needed
   - Explain the optimization

4. **🧪 Test Your Solution**
   - Walk through examples
   - Consider edge cases
   - Check for off-by-one errors

### ⚡ **Time Complexity Quick Reference**

| Algorithm | Best Case | Average Case | Worst Case |
|-----------|-----------|--------------|------------|
| Binary Search | O(1) | O(log n) | O(log n) |
| Quick Sort | O(n log n) | O(n log n) | O(n²) |
| Merge Sort | O(n log n) | O(n log n) | O(n log n) |
| Heap Sort | O(n log n) | O(n log n) | O(n log n) |
| Bubble Sort | O(n) | O(n²) | O(n²) |
| Hash Table | O(1) | O(1) | O(n) |

### 🔍 **Common Patterns**

- **Two Pointers**: Array problems, palindromes
- **Sliding Window**: Substring/subarray problems
- **Fast & Slow Pointers**: Cycle detection
- **Backtracking**: Permutations, combinations
- **Dynamic Programming**: Optimization problems
- **BFS/DFS**: Tree/graph traversal

---

## 🎉 **Conclusion**

This comprehensive guide covers the most frequently asked PHP coding interview questions. Practice these problems regularly, understand the underlying algorithms, and you'll be well-prepared for your next technical interview!

### 🚀 **Next Steps**
1. Practice each problem multiple times
2. Implement variations and optimizations
3. Focus on explaining your approach clearly
4. Time yourself solving problems
5. Review PHP-specific features and best practices

**Good luck with your interviews! 💪**

---

*Created with ❤️ for PHP developers preparing for coding interviews*