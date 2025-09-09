## 📘 PHP, Laravel & SQL Interview Questions with Answers

---

### ❓ 4) What are Magic Methods in PHP?

**Definition:**
Magic methods in PHP are special methods that start with `__` (double underscore). They are predefined methods that are automatically called when certain actions are performed on an object.

**Examples:**
- `__construct()` → Called when an object is created.
- `__destruct()` → Called when an object is destroyed.
- `__get()` → Called when trying to access an undefined property.
- `__set()` → Called when trying to set an undefined property.
- `__toString()` → Called when an object is treated as a string.

**Code Example:**
```php
class MagicExample {
    private $data = [];

    public function __set($name, $value) {
        $this->data[$name] = $value;
    }

    public function __get($name) {
        return $this->data[$name] ?? null;
    }

    public function __toString() {
        return "MagicExample class";
    }
}

$obj = new MagicExample();
$obj->name = "John";
echo $obj->name; // John
echo $obj; // MagicExample class
```

---

### ❓ 5) Explain the Array Functions in PHP.

**Definition:**
Array functions are built-in functions in PHP that help to manipulate arrays.

**Common Array Functions:**
- `count($arr)` → Returns the number of elements.
- `array_merge($arr1, $arr2)` → Merges arrays.
- `array_push($arr, $val)` → Adds a value at the end.
- `array_pop($arr)` → Removes the last element.
- `array_shift($arr)` → Removes the first element.
- `array_slice($arr, 1, 3)` → Extracts a portion of array.

**Code Example:**
```php
$fruits = ["apple", "banana"];
array_push($fruits, "mango");
print_r($fruits);
// Output: [apple, banana, mango]
```

---

### ❓ 6) What is the CSRF Token in Laravel, and how does it work?

**Definition:**
CSRF (Cross-Site Request Forgery) token is a security feature in Laravel that prevents malicious attacks by ensuring that requests come from authenticated users.

**How it Works:**
- Laravel generates a unique token for each user session.
- This token must be included in all POST, PUT, DELETE requests.
- If the token is missing or invalid, Laravel rejects the request.

**Code Example:**
```blade
<form method="POST" action="/submit">
    @csrf
    <input type="text" name="name">
    <button type="submit">Submit</button>
</form>
```

---

### ❓ 7) How to Find Duplicate Salaries in the Employees Table?

**SQL Query:**
```sql
SELECT salary, COUNT(*) as count
FROM employees
GROUP BY salary
HAVING COUNT(*) > 1;
```

---

### ❓ 8) How to Find the Maximum Salary from the Employees Table?

**SQL Query:**
```sql
SELECT MAX(salary) as max_salary
FROM employees;
```

---

### ❓ 9) What is an Aggregate Function in SQL?

**Definition:**
Aggregate functions in SQL perform calculations on multiple rows and return a single value.

**Examples:**
- `SUM()` → Adds values.
- `AVG()` → Finds average.
- `COUNT()` → Counts rows.
- `MAX()` → Finds maximum value.
- `MIN()` → Finds minimum value.

**Code Example:**
```sql
SELECT AVG(salary) FROM employees;
```

---

### ❓ 10) Difference between `implode` and `explode` in PHP.

**Definition:**
- `implode()` → Converts an array into a string.
- `explode()` → Converts a string into an array.

**Code Example:**
```php
$arr = ["apple", "banana", "mango"];
$str = implode(", ", $arr);
// Output: apple, banana, mango

$newArr = explode(", ", $str);
// Output: [apple, banana, mango]
```

---

### ❓ 11) Caches in Laravel.

**Definition:**
Caching is storing frequently accessed data in memory for faster access.

**Supported Drivers:**
- `file`
- `database`
- `redis`
- `memcached`

**Code Example:**
```php
Cache::put('key', 'value', 600); // store for 10 mins
echo Cache::get('key'); // value
```

---

### ❓ 12) Difference between Hashing and Encrypting in Laravel.

**Hashing:**
- One-way process.
- Cannot be reversed.
- Used for storing passwords.

**Encrypting:**
- Two-way process.
- Can be decrypted.
- Used for sensitive data (API keys, tokens).

**Code Example:**
```php
// Hashing
$password = Hash::make('secret');

// Encrypting
$encrypted = Crypt::encryptString('secret');
$decrypted = Crypt::decryptString($encrypted);
```

---

### ❓ 13) Rotate the Array

**Question:**
Input = `[1, 2, 3, 4, 5, 6, 7]` → Output = `[7, 1, 2, 3, 4, 5, 6]`

**Solution 1: Using PHP Functions**
```php
$input = [1, 2, 3, 4, 5, 6, 7];
$last = array_pop($input);
array_unshift($input, $last);
print_r($input);
```

**Solution 2: Without PHP Functions (Manual)**
```php
$input = [1, 2, 3, 4, 5, 6, 7];
$n = count($input);
$last = $input[$n - 1];

for ($i = $n - 1; $i > 0; $i--) {
    $input[$i] = $input[$i - 1];
}
$input[0] = $last;

print_r($input);
```

---

