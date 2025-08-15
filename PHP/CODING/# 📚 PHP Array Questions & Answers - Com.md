# 📚 PHP Array Questions & Answers - Complete Guide

## 🔰 Basic to Intermediate PHP Array Questions

### 1. 🔢 Find the maximum value in an array

**Question:** Find the maximum value in an array.

**Answer:**
```php
function findMax($arr) {
    $max = $arr[0];
    for ($i = 1; $i < count($arr); $i++) {
        if ($arr[$i] > $max) {
            $max = $arr[$i];
        }
    }
    return $max;
}

// Example
$numbers = [3, 7, 2, 9, 1];
echo findMax($numbers); // Output: 9
```

**💡 Explanation:** We iterate through the array comparing each element with the current maximum, updating it when we find a larger value.

### 2. 🥈 Find the second largest value in an array

**Question:** Find the second largest value in an array.

**Answer:**
```php
function findSecondLargest($arr) {
    $first = $second = PHP_INT_MIN;
    
    foreach ($arr as $value) {
        if ($value > $first) {
            $second = $first;
            $first = $value;
        } elseif ($value > $second && $value != $first) {
            $second = $value;
        }
    }
    
    return $second;
}

// Example
$numbers = [3, 7, 2, 9, 1, 8];
echo findSecondLargest($numbers); // Output: 8
```

**💡 Explanation:** We maintain two variables to track the largest and second largest values, updating them as we traverse the array.

### 3. 🔄 Reverse an array without using array_reverse()

**Question:** Reverse an array without using array_reverse().

**Answer:**
```php
function reverseArray($arr) {
    $reversed = [];
    for ($i = count($arr) - 1; $i >= 0; $i--) {
        $reversed[] = $arr[$i];
    }
    return $reversed;
}

// Example
$numbers = [1, 2, 3, 4, 5];
print_r(reverseArray($numbers)); // Output: [5, 4, 3, 2, 1]
```

**💡 Explanation:** We iterate from the last index to the first, adding each element to a new array.

### 4. 🔍 Check if a value exists in an array without using in_array()

**Question:** Check if a value exists in an array without using in_array().

**Answer:**
```php
function valueExists($arr, $value) {
    foreach ($arr as $item) {
        if ($item === $value) {
            return true;
        }
    }
    return false;
}

// Example
$fruits = ['apple', 'banana', 'orange'];
echo valueExists($fruits, 'banana') ? 'Found' : 'Not found'; // Output: Found
```

**💡 Explanation:** We loop through each element and compare it with the target value using strict comparison.

### 5. 📊 Count how many times each element appears in an array (frequency map)

**Question:** Count how many times each element appears in an array (frequency map).

**Answer:**
```php
function getFrequency($arr) {
    $frequency = [];
    foreach ($arr as $item) {
        if (isset($frequency[$item])) {
            $frequency[$item]++;
        } else {
            $frequency[$item] = 1;
        }
    }
    return $frequency;
}

// Example
$letters = ['a', 'b', 'a', 'c', 'b', 'a'];
print_r(getFrequency($letters)); // Output: ['a' => 3, 'b' => 2, 'c' => 1]
```

**💡 Explanation:** We use an associative array to store each unique value as a key and increment its count.

### 6. ➕ Sum all values in an array

**Question:** Sum all values in an array.

**Answer:**
```php
function arraySum($arr) {
    $sum = 0;
    foreach ($arr as $value) {
        $sum += $value;
    }
    return $sum;
}

// Example
$numbers = [1, 2, 3, 4, 5];
echo arraySum($numbers); // Output: 15
```

**💡 Explanation:** We iterate through the array and accumulate the sum of all elements.

### 7. 🚫 Remove all even numbers from an array

**Question:** Remove all even numbers from an array.

**Answer:**
```php
function removeEvenNumbers($arr) {
    $oddNumbers = [];
    foreach ($arr as $number) {
        if ($number % 2 !== 0) {
            $oddNumbers[] = $number;
        }
    }
    return $oddNumbers;
}

// Example
$numbers = [1, 2, 3, 4, 5, 6, 7, 8];
print_r(removeEvenNumbers($numbers)); // Output: [1, 3, 5, 7]
```

**💡 Explanation:** We check if each number is odd using the modulo operator and add only odd numbers to the result.

### 8. 📈 Find the average of values in an array

**Question:** Find the average of values in an array.

**Answer:**
```php
function calculateAverage($arr) {
    if (empty($arr)) return 0;
    
    $sum = 0;
    foreach ($arr as $value) {
        $sum += $value;
    }
    return $sum / count($arr);
}

// Example
$numbers = [10, 20, 30, 40, 50];
echo calculateAverage($numbers); // Output: 30
```

**💡 Explanation:** We calculate the sum of all elements and divide by the count of elements.

### 9. 🎯 Find the median of an array

**Question:** Find the median of an array.

**Answer:**
```php
function findMedian($arr) {
    sort($arr);
    $count = count($arr);
    $middle = floor($count / 2);
    
    if ($count % 2 === 0) {
        // Even number of elements
        return ($arr[$middle - 1] + $arr[$middle]) / 2;
    } else {
        // Odd number of elements
        return $arr[$middle];
    }
}

// Example
$numbers = [3, 1, 4, 1, 5, 9, 2];
echo findMedian($numbers); // Output: 3
```

**💡 Explanation:** We sort the array first, then find the middle value(s). For even counts, we average the two middle values.

### 10. 🔗 Merge two arrays manually (without using array_merge())

**Question:** Merge two arrays manually (without using array_merge()).

**Answer:**
```php
function mergeArrays($arr1, $arr2) {
    $merged = [];
    
    // Add all elements from first array
    foreach ($arr1 as $value) {
        $merged[] = $value;
    }
    
    // Add all elements from second array
    foreach ($arr2 as $value) {
        $merged[] = $value;
    }
    
    return $merged;
}

// Example
$array1 = [1, 2, 3];
$array2 = [4, 5, 6];
print_r(mergeArrays($array1, $array2)); // Output: [1, 2, 3, 4, 5, 6]
```

**💡 Explanation:** We create a new array and add elements from both input arrays sequentially.

## 🔄 Duplicate & Uniqueness

### 11. 🗑️ Remove duplicates from an array without using array_unique()

**Question:** Remove duplicates from an array without using array_unique().

**Answer:**
```php
function removeDuplicates($arr) {
    $unique = [];
    $seen = [];
    
    foreach ($arr as $value) {
        if (!isset($seen[$value])) {
            $unique[] = $value;
            $seen[$value] = true;
        }
    }
    
    return $unique;
}

// Example
$numbers = [1, 2, 2, 3, 4, 4, 5];
print_r(removeDuplicates($numbers)); // Output: [1, 2, 3, 4, 5]
```

**💡 Explanation:** We use an associative array to track seen values and only add unseen values to the result.

### 12. ✅ Check if an array has duplicates

**Question:** Check if an array has duplicates.

**Answer:**
```php
function hasDuplicates($arr) {
    $seen = [];
    foreach ($arr as $value) {
        if (isset($seen[$value])) {
            return true;
        }
        $seen[$value] = true;
    }
    return false;
}

// Example
$numbers1 = [1, 2, 3, 4, 5];
$numbers2 = [1, 2, 2, 3, 4];
echo hasDuplicates($numbers1) ? 'Has duplicates' : 'No duplicates'; // Output: No duplicates
echo hasDuplicates($numbers2) ? 'Has duplicates' : 'No duplicates'; // Output: Has duplicates
```

**💡 Explanation:** We track seen values and return true immediately when we encounter a duplicate.

### 13. 🔍 Return only duplicated values

**Question:** Return only duplicated values.

**Answer:**
```php
function getDuplicates($arr) {
    $counts = [];
    $duplicates = [];
    
    // Count occurrences
    foreach ($arr as $value) {
        $counts[$value] = ($counts[$value] ?? 0) + 1;
    }
    
    // Find duplicates
    foreach ($counts as $value => $count) {
        if ($count > 1) {
            $duplicates[] = $value;
        }
    }
    
    return $duplicates;
}

// Example
$numbers = [1, 2, 2, 3, 4, 4, 5, 4];
print_r(getDuplicates($numbers)); // Output: [2, 4]
```

**💡 Explanation:** We first count all occurrences, then return values that appear more than once.

### 14. 📊 Count duplicate entries and their frequencies

**Question:** Count duplicate entries and their frequencies.

**Answer:**
```php
function getDuplicateFrequencies($arr) {
    $counts = [];
    $duplicates = [];
    
    // Count all occurrences
    foreach ($arr as $value) {
        $counts[$value] = ($counts[$value] ?? 0) + 1;
    }
    
    // Get only duplicates with their frequencies
    foreach ($counts as $value => $count) {
        if ($count > 1) {
            $duplicates[$value] = $count;
        }
    }
    
    return $duplicates;
}

// Example
$numbers = [1, 2, 2, 3, 4, 4, 5, 4];
print_r(getDuplicateFrequencies($numbers)); // Output: [2 => 2, 4 => 3]
```

**💡 Explanation:** Similar to the previous function, but we return the count for each duplicate value.

### 15. ⭐ Return unique values that occur only once

**Question:** Return unique values that occur only once.

**Answer:**
```php
function getUniqueOnce($arr) {
    $counts = [];
    $unique = [];
    
    // Count occurrences
    foreach ($arr as $value) {
        $counts[$value] = ($counts[$value] ?? 0) + 1;
    }
    
    // Find values that occur exactly once
    foreach ($counts as $value => $count) {
        if ($count === 1) {
            $unique[] = $value;
        }
    }
    
    return $unique;
}

// Example
$numbers = [1, 2, 2, 3, 4, 4, 5];
print_r(getUniqueOnce($numbers)); // Output: [1, 3, 5]
```

**💡 Explanation:** We count occurrences and return only values that appear exactly once.

## 📋 Sorting & Filtering

### 16. 📏 Sort an associative array by value length

**Question:** Sort an associative array by value length.

**Answer:**
```php
function sortByValueLength($arr) {
    $keys = array_keys($arr);
    $values = array_values($arr);
    
    // Bubble sort based on string length
    for ($i = 0; $i < count($values); $i++) {
        for ($j = 0; $j < count($values) - $i - 1; $j++) {
            if (strlen($values[$j]) > strlen($values[$j + 1])) {
                // Swap values
                $temp = $values[$j];
                $values[$j] = $values[$j + 1];
                $values[$j + 1] = $temp;
                
                // Swap corresponding keys
                $temp = $keys[$j];
                $keys[$j] = $keys[$j + 1];
                $keys[$j + 1] = $temp;
            }
        }
    }
    
    // Rebuild associative array
    $result = [];
    for ($i = 0; $i < count($keys); $i++) {
        $result[$keys[$i]] = $values[$i];
    }
    
    return $result;
}

// Example
$words = ['a' => 'apple', 'b' => 'hi', 'c' => 'banana', 'd' => 'cat'];
print_r(sortByValueLength($words)); // Output: ['b' => 'hi', 'd' => 'cat', 'a' => 'apple', 'c' => 'banana']
```

**💡 Explanation:** We use bubble sort to sort based on string length while maintaining key-value relationships.

### 17. 🏗️ Sort a multidimensional array by a key value

**Question:** Sort a multidimensional array by a key value.

**Answer:**
```php
function sortByKey($arr, $key) {
    // Bubble sort implementation
    for ($i = 0; $i < count($arr); $i++) {
        for ($j = 0; $j < count($arr) - $i - 1; $j++) {
            if ($arr[$j][$key] > $arr[$j + 1][$key]) {
                // Swap elements
                $temp = $arr[$j];
                $arr[$j] = $arr[$j + 1];
                $arr[$j + 1] = $temp;
            }
        }
    }
    return $arr;
}

// Example
$students = [
    ['name' => 'John', 'age' => 25],
    ['name' => 'Jane', 'age' => 22],
    ['name' => 'Bob', 'age' => 27]
];
print_r(sortByKey($students, 'age')); // Sorted by age
```

**💡 Explanation:** We use bubble sort to compare specific key values in multidimensional arrays.

### 18. 🎯 Filter all values greater than a given threshold

**Question:** Filter all values greater than a given threshold.

**Answer:**
```php
function filterGreaterThan($arr, $threshold) {
    $filtered = [];
    foreach ($arr as $value) {
        if ($value > $threshold) {
            $filtered[] = $value;
        }
    }
    return $filtered;
}

// Example
$numbers = [1, 5, 10, 15, 20, 3, 8];
print_r(filterGreaterThan($numbers, 10)); // Output: [15, 20]
```

**💡 Explanation:** We iterate through the array and include only values that meet the condition.

### 19. 🔢 Custom sort an array (e.g., all odd numbers first)

**Question:** Custom sort an array (e.g., all odd numbers first).

**Answer:**
```php
function sortOddFirst($arr) {
    $odds = [];
    $evens = [];
    
    // Separate odds and evens
    foreach ($arr as $value) {
        if ($value % 2 === 0) {
            $evens[] = $value;
        } else {
            $odds[] = $value;
        }
    }
    
    // Sort both arrays
    sort($odds);
    sort($evens);
    
    // Merge odds first, then evens
    return array_merge($odds, $evens);
}

// Example
$numbers = [4, 1, 6, 3, 8, 5, 2, 7];
print_r(sortOddFirst($numbers)); // Output: [1, 3, 5, 7, 2, 4, 6, 8]
```

**💡 Explanation:** We separate the array into odd and even numbers, sort each group, then merge them.

### 20. 📉 Sort array in descending order without built-in functions

**Question:** Sort array in descending order without built-in functions.

**Answer:**
```php
function sortDescending($arr) {
    // Bubble sort in descending order
    for ($i = 0; $i < count($arr); $i++) {
        for ($j = 0; $j < count($arr) - $i - 1; $j++) {
            if ($arr[$j] < $arr[$j + 1]) {
                // Swap elements
                $temp = $arr[$j];
                $arr[$j] = $arr[$j + 1];
                $arr[$j + 1] = $temp;
            }
        }
    }
    return $arr;
}

// Example
$numbers = [3, 1, 4, 1, 5, 9, 2, 6];
print_r(sortDescending($numbers)); // Output: [9, 6, 5, 4, 3, 2, 1, 1]
```

**💡 Explanation:** We implement bubble sort with the comparison reversed to sort in descending order.

## 🚀 Advanced Array Tricks

### 21. 🌊 Flatten a multidimensional array

**Question:** Flatten a multidimensional array.

**Answer:**
```php
function flattenArray($arr) {
    $flattened = [];
    
    foreach ($arr as $element) {
        if (is_array($element)) {
            // Recursively flatten nested arrays
            $flattened = array_merge($flattened, flattenArray($element));
        } else {
            $flattened[] = $element;
        }
    }
    
    return $flattened;
}

// Example
$nested = [1, [2, 3], [4, [5, 6]], 7];
print_r(flattenArray($nested)); // Output: [1, 2, 3, 4, 5, 6, 7]
```

**💡 Explanation:** We use recursion to handle nested arrays at any depth, merging all elements into a single-dimensional array.

### 22. 📊 Create an array of running totals

**Question:** Create an array of running totals.

**Answer:**
```php
function runningTotals($arr) {
    $totals = [];
    $sum = 0;
    
    foreach ($arr as $value) {
        $sum += $value;
        $totals[] = $sum;
    }
    
    return $totals;
}

// Example
$numbers = [1, 2, 3, 4, 5];
print_r(runningTotals($numbers)); // Output: [1, 3, 6, 10, 15]
```

**💡 Explanation:** We maintain a running sum and add it to the result array at each step.

### 23. 🎯 Find the first non-repeating value in an array

**Question:** Find the first non-repeating value in an array.

**Answer:**
```php
function firstNonRepeating($arr) {
    $counts = [];
    
    // Count occurrences
    foreach ($arr as $value) {
        $counts[$value] = ($counts[$value] ?? 0) + 1;
    }
    
    // Find first non-repeating
    foreach ($arr as $value) {
        if ($counts[$value] === 1) {
            return $value;
        }
    }
    
    return null;
}

// Example
$letters = ['a', 'b', 'a', 'c', 'b', 'd'];
echo firstNonRepeating($letters); // Output: c
```

**💡 Explanation:** We first count all occurrences, then iterate through the original array to find the first element that occurs only once.

### 24. 🔄 Rotate an array left/right by k steps

**Question:** Rotate an array left/right by k steps.

**Answer:**
```php
function rotateRight($arr, $k) {
    $n = count($arr);
    $k = $k % $n; // Handle k > array length
    
    if ($k === 0) return $arr;
    
    $rotated = [];
    
    // Add elements from position n-k to end
    for ($i = $n - $k; $i < $n; $i++) {
        $rotated[] = $arr[$i];
    }
    
    // Add elements from start to position n-k-1
    for ($i = 0; $i < $n - $k; $i++) {
        $rotated[] = $arr[$i];
    }
    
    return $rotated;
}

function rotateLeft($arr, $k) {
    $n = count($arr);
    $k = $k % $n;
    
    if ($k === 0) return $arr;
    
    $rotated = [];
    
    // Add elements from position k to end
    for ($i = $k; $i < $n; $i++) {
        $rotated[] = $arr[$i];
    }
    
    // Add elements from start to position k-1
    for ($i = 0; $i < $k; $i++) {
        $rotated[] = $arr[$i];
    }
    
    return $rotated;
}

// Example
$numbers = [1, 2, 3, 4, 5];
print_r(rotateRight($numbers, 2)); // Output: [4, 5, 1, 2, 3]
print_r(rotateLeft($numbers, 2));  // Output: [3, 4, 5, 1, 2]
```

**💡 Explanation:** We split the array at the rotation point and reassemble it in the new order.

### 25. ✂️ Split an array into chunks of size k

**Question:** Split an array into chunks of size k.

**Answer:**
```php
function arrayChunk($arr, $size) {
    $chunks = [];
    $chunk = [];
    
    foreach ($arr as $element) {
        $chunk[] = $element;
        
        if (count($chunk) === $size) {
            $chunks[] = $chunk;
            $chunk = [];
        }
    }
    
    // Add remaining elements as last chunk
    if (!empty($chunk)) {
        $chunks[] = $chunk;
    }
    
    return $chunks;
}

// Example
$numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9];
print_r(arrayChunk($numbers, 3)); // Output: [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
```

**💡 Explanation:** We build chunks by accumulating elements until we reach the desired size, then start a new chunk.

### 26. 🔄 Re-index an array after deletion of some elements

**Question:** Re-index an array after deletion of some elements.

**Answer:**
```php
function reindexArray($arr) {
    $reindexed = [];
    
    foreach ($arr as $value) {
        $reindexed[] = $value;
    }
    
    return $reindexed;
}

// Example with gaps
$numbers = [0 => 'a', 2 => 'b', 5 => 'c', 7 => 'd'];
print_r(reindexArray($numbers)); // Output: [0 => 'a', 1 => 'b', 2 => 'c', 3 => 'd']
```

**💡 Explanation:** We create a new array and add all values sequentially, which automatically creates consecutive numeric indices.

### 27. 🏷️ Group array items by type (int, string, etc.)

**Question:** Group array items by type (int, string, etc.).

**Answer:**
```php
function groupByType($arr) {
    $groups = [];
    
    foreach ($arr as $item) {
        $type = gettype($item);
        
        if (!isset($groups[$type])) {
            $groups[$type] = [];
        }
        
        $groups[$type][] = $item;
    }
    
    return $groups;
}

// Example
$mixed = [1, 'hello', 3.14, true, 'world', 42, false];
print_r(groupByType($mixed));
// Output: ['integer' => [1, 42], 'string' => ['hello', 'world'], 'double' => [3.14], 'boolean' => [true, false]]
```

**💡 Explanation:** We use PHP's gettype() function to determine the type of each element and group them accordingly.

### 28. ⚖️ Check if two arrays are equal (same elements, order doesn't matter)

**Question:** Check if two arrays are equal (same elements, order doesn't matter).

**Answer:**
```php
function arraysEqual($arr1, $arr2) {
    if (count($arr1) !== count($arr2)) {
        return false;
    }
    
    // Count frequencies in both arrays
    $freq1 = [];
    $freq2 = [];
    
    foreach ($arr1 as $item) {
        $freq1[$item] = ($freq1[$item] ?? 0) + 1;
    }
    
    foreach ($arr2 as $item) {
        $freq2[$item] = ($freq2[$item] ?? 0) + 1;
    }
    
    // Compare frequencies
    foreach ($freq1 as $item => $count) {
        if (!isset($freq2[$item]) || $freq2[$item] !== $count) {
            return false;
        }
    }
    
    return true;
}

// Example
$arr1 = [1, 2, 3, 2];
$arr2 = [2, 1, 2, 3];
$arr3 = [1, 2, 3];
echo arraysEqual($arr1, $arr2) ? 'Equal' : 'Not equal'; // Output: Equal
echo arraysEqual($arr1, $arr3) ? 'Equal' : 'Not equal'; // Output: Not equal
```

**💡 Explanation:** We compare the frequency of each element in both arrays to ensure they contain the same elements with the same counts.

### 29. 🎯 Find all pairs of numbers that sum to a specific value

**Question:** Find all pairs of numbers that sum to a specific value.

**Answer:**
```php
function findPairs($arr, $target) {
    $pairs = [];
    
    for ($i = 0; $i < count($arr); $i++) {
        for ($j = $i + 1; $j < count($arr); $j++) {
            if ($arr[$i] + $arr[$j] === $target) {
                $pairs[] = [$arr[$i], $arr[$j]];
            }
        }
    }
    
    return $pairs;
}

// Example
$numbers = [1, 2, 3, 4, 5, 6];
print_r(findPairs($numbers, 7)); // Output: [[1, 6], [2, 5], [3, 4]]
```

**💡 Explanation:** We use nested loops to check all possible pairs and collect those that sum to the target value.

### 30. 🔢 Find the longest consecutive sequence in an array

**Question:** Find the longest consecutive sequence in an array.

**Answer:**
```php
function longestConsecutive($arr) {
    if (empty($arr)) return [];
    
    $unique = array_unique($arr);
    sort($unique);
    
    $longestSeq = [];
    $currentSeq = [$unique[0]];
    
    for ($i = 1; $i < count($unique); $i++) {
        if ($unique[$i] === $unique[$i-1] + 1) {
            $currentSeq[] = $unique[$i];
        } else {
            if (count($currentSeq) > count($longestSeq)) {
                $longestSeq = $currentSeq;
            }
            $currentSeq = [$unique[$i]];
        }
    }
    
    // Check the last sequence
    if (count($currentSeq) > count($longestSeq)) {
        $longestSeq = $currentSeq;
    }
    
    return $longestSeq;
}

// Example
$numbers = [100, 4, 200, 1, 3, 2, 101, 102];
print_r(longestConsecutive($numbers)); // Output: [1, 2, 3, 4] or [100, 101, 102]
```

**💡 Explanation:** We remove duplicates, sort the array, then track consecutive sequences to find the longest one.

## 🔍 Search & Mapping

### 31. 🎯 Find the index of a value without using array_search()

**Question:** Find the index of a value without using array_search().

**Answer:**
```php
function findIndex($arr, $value) {
    for ($i = 0; $i < count($arr); $i++) {
        if ($arr[$i] === $value) {
            return $i;
        }
    }
    return false; // Not found
}

// Example
$fruits = ['apple', 'banana', 'orange', 'grape'];
echo findIndex($fruits, 'orange'); // Output: 2
echo findIndex($fruits, 'kiwi');   // Output: false
```

**💡 Explanation:** We iterate through the array with an index counter and return the index when we find a match.

### 32. 🔄 Write your own array_map() function

**Question:** Write your own array_map() function.

**Answer:**
```php
function customMap($callback, $arr) {
    $mapped = [];
    
    foreach ($arr as $key => $value) {
        $mapped[$key] = $callback($value);
    }
    
    return $mapped;
}

// Example
function double($x) {
    return $x * 2;
}

$numbers = [1, 2, 3, 4, 5];
print_r(customMap('double', $numbers)); // Output: [2, 4, 6, 8, 10]
```

**💡 Explanation:** We apply the callback function to each element and collect the results in a new array.

### 33. 🎣 Write your own array_filter() function

**Question:** Write your own array_filter() function.

**Answer:**
```php
function customFilter($arr, $callback = null) {
    $filtered = [];
    
    foreach ($arr as $key => $value) {
        if ($callback === null) {
            // Default behavior - filter falsy values
            if ($value) {
                $filtered[$key] = $value;
            }
        } else {
            // Use callback function
            if ($callback($value)) {
                $filtered[$key] = $value;
            }
        }
    }
    
    return $filtered;
}

// Example
function isEven($x) {
    return $x % 2 === 0;
}

$numbers = [1, 2, 3, 4, 5, 6];
print_r(customFilter($numbers, 'isEven')); // Output: [1 => 2, 3 => 4, 5 => 6]
```

**💡 Explanation:** We iterate through the array and include only elements that pass the callback test or are truthy if no callback is provided.

### 34. 🎯 Implement binary search on a sorted array

**Question:** Implement binary search on a sorted array.

**Answer:**
```php
function binarySearch($arr, $target) {
    $left = 0;
    $right = count($arr) - 1;
    
    while ($left <= $right) {
        $mid = floor(($left + $right) / 2);
        
        if ($arr[$mid] === $target) {
            return $mid;
        } elseif ($arr[$mid] < $target) {
            $left = $mid + 1;
        } else {
            $right = $mid - 1;
        }
    }
    
    return -1; // Not found
}

// Example
$sortedNumbers = [1, 3, 5, 7, 9, 11, 13, 15];
echo binarySearch($sortedNumbers, 7);  // Output: 3
echo binarySearch($sortedNumbers, 10); // Output: -1
```

**💡 Explanation:** We repeatedly divide the search space in half by comparing the target with the middle element.

### 35. 🔑 Get keys for a specific value in an associative array

**Question:** Get keys for a specific value in an associative array.

**Answer:**
```php
function getKeysForValue($arr, $searchValue) {
    $keys = [];
    
    foreach ($arr as $key => $value) {
        if ($value === $searchValue) {
            $keys[] = $key;
        }
    }
    
    return $keys;
}

// Example
$colors = [
    'apple' => 'red',
    'banana' => 'yellow',
    'cherry' => 'red',
    'orange' => 'orange',
    'strawberry' => 'red'
];
print_r(getKeysForValue($colors, 'red')); // Output: ['apple', 'cherry', 'strawberry']
```

**💡 Explanation:** We iterate through the associative array and collect all keys that have the matching value.

## 🌍 Real-World Scenarios

### 36. 📅 Group dates by month/year from an array of dates

**Question:** Group dates by month/year from an array of dates.

**Answer:**
```php
function groupDatesByMonth($dates) {
    $grouped = [];
    
    foreach ($dates as $date) {
        $timestamp = strtotime($date);
        $monthYear = date('Y-m', $timestamp);
        
        if (!isset($grouped[$monthYear])) {
            $grouped[$monthYear] = [];
        }
        
        $grouped[$monthYear][] = $date;
    }
    
    return $grouped;
}

// Example
$dates = ['2023-01-15', '2023-01-20', '2023-02-10', '2023-02-25', '2023-03-05'];
print_r(groupDatesByMonth($dates));
// Output: ['2023-01' => ['2023-01-15', '2023-01-20'], '2023-02' => ['2023-02-10', '2023-02-25'], '2023-03' => ['2023-03-05']]
```

**💡 Explanation:** We extract the year-month from each date and group the dates accordingly.

### 37. 📄 Paginate an array (return chunk by page number)

**Question:** Paginate an array (return chunk by page number).

**Answer:**
```php
function paginate($arr, $pageSize, $pageNumber) {
    $totalItems = count($arr);
    $totalPages = ceil($totalItems / $pageSize);
    
    if ($pageNumber < 1 || $pageNumber > $totalPages) {
        return [
            'data' => [],
            'current_page' => $pageNumber,
            'total_pages' => $totalPages,
            'total_items' => $totalItems
        ];
    }
    
    $offset = ($pageNumber - 1) * $pageSize;
    $data = [];
    
    for ($i = $offset; $i < $offset + $pageSize && $i < $totalItems; $i++) {
        $data[] = $arr[$i];
    }
    
    return [
        'data' => $data,
        'current_page' => $pageNumber,
        'total_pages' => $totalPages,
        'total_items' => $totalItems
    ];
}

// Example
$items = range(1, 25); // [1, 2, 3, ..., 25]
print_r(paginate($items, 5, 2));
// Output: ['data' => [6, 7, 8, 9, 10], 'current_page' => 2, 'total_pages' => 5, 'total_items' => 25]
```

**💡 Explanation:** We calculate the offset based on page number and size, then extract the appropriate slice of data.

### 38. 📊 Calculate percentage contribution of each element to total sum

**Question:** Calculate percentage contribution of each element to total sum.

**Answer:**
```php
function calculatePercentages($arr) {
    $total = array_sum($arr);
    $percentages = [];
    
    if ($total == 0) {
        return array_fill_keys(array_keys($arr), 0);
    }
    
    foreach ($arr as $key => $value) {
        $percentages[$key] = round(($value / $total) * 100, 2);
    }
    
    return $percentages;
}

// Example
$sales = ['Q1' => 1000, 'Q2' => 1500, 'Q3' => 2000, 'Q4' => 2500];
print_r(calculatePercentages($sales));
// Output: ['Q1' => 14.29, 'Q2' => 21.43, 'Q3' => 28.57, 'Q4' => 35.71]
```

**💡 Explanation:** We calculate each element's percentage of the total sum and round to 2 decimal places.

### 39. 🌳 Convert a flat array to a nested tree structure

**Question:** Convert a flat array to a nested tree structure.

**Answer:**
```php
function buildTree($flatArray, $parentKey = 'parent_id', $idKey = 'id', $parentId = null) {
    $tree = [];
    
    foreach ($flatArray as $item) {
        if ($item[$parentKey] == $parentId) {
            $children = buildTree($flatArray, $parentKey, $idKey, $item[$idKey]);
            if (!empty($children)) {
                $item['children'] = $children;
            }
            $tree[] = $item;
        }
    }
    
    return $tree;
}

// Example
$flat = [
    ['id' => 1, 'name' => 'Root', 'parent_id' => null],
    ['id' => 2, 'name' => 'Child 1', 'parent_id' => 1],
    ['id' => 3, 'name' => 'Child 2', 'parent_id' => 1],
    ['id' => 4, 'name' => 'Grandchild 1', 'parent_id' => 2]
];
print_r(buildTree($flat));
```

**💡 Explanation:** We recursively build a tree by finding children for each parent node.

### 40. 🔗 Convert a list of strings into a comma-separated string

**Question:** Convert a list of strings into a comma-separated string.

**Answer:**
```php
function arrayToString($arr, $separator = ', ') {
    if (empty($arr)) {
        return '';
    }
    
    $result = $arr[0];
    for ($i = 1; $i < count($arr); $i++) {
        $result .= $separator . $arr[$i];
    }
    
    return $result;
}

// Example
$fruits = ['apple', 'banana', 'orange', 'grape'];
echo arrayToString($fruits); // Output: apple, banana, orange, grape
echo arrayToString($fruits, ' | '); // Output: apple | banana | orange | grape
```

**💡 Explanation:** We concatenate array elements with the specified separator between them.

## 🧠 Logic-Based Challenges

### 41. 🔄 Check if an array is a palindrome

**Question:** Check if an array is a palindrome.

**Answer:**
```php
function isPalindrome($arr) {
    $length = count($arr);
    
    for ($i = 0; $i < $length / 2; $i++) {
        if ($arr[$i] !== $arr[$length - 1 - $i]) {
            return false;
        }
    }
    
    return true;
}

// Example
$palindrome1 = [1, 2, 3, 2, 1];
$palindrome2 = [1, 2, 3, 4, 5];
echo isPalindrome($palindrome1) ? 'Is palindrome' : 'Not palindrome'; // Output: Is palindrome
echo isPalindrome($palindrome2) ? 'Is palindrome' : 'Not palindrome'; // Output: Not palindrome
```

**💡 Explanation:** We compare elements from both ends moving toward the center.

### 42. 🔍 Find missing number in a sequence (e.g., 1 to 100)

**Question:** Find missing number in a sequence (e.g., 1 to 100).

**Answer:**
```php
function findMissingNumber($arr, $n) {
    $expectedSum = ($n * ($n + 1)) / 2;
    $actualSum = array_sum($arr);
    
    return $expectedSum - $actualSum;
}

// Alternative approach without using array_sum
function findMissingNumberManual($arr, $n) {
    $present = [];
    
    // Mark present numbers
    foreach ($arr as $num) {
        $present[$num] = true;
    }
    
    // Find missing number
    for ($i = 1; $i <= $n; $i++) {
        if (!isset($present[$i])) {
            return $i;
        }
    }
    
    return null;
}

// Example
$sequence = [1, 2, 3, 5, 6, 7, 8, 9, 10]; // Missing 4
echo findMissingNumber($sequence, 10); // Output: 4
echo findMissingNumberManual($sequence, 10); // Output: 4
```

**💡 Explanation:** We can use the mathematical formula for sum of consecutive numbers or mark present numbers and find the gap.

### 43. 🪞 Detect if two arrays are mirror images

**Question:** Detect if two arrays are mirror images.

**Answer:**
```php
function areMirrorImages($arr1, $arr2) {
    if (count($arr1) !== count($arr2)) {
        return false;
    }
    
    $length = count($arr1);
    
    for ($i = 0; $i < $length; $i++) {
        if ($arr1[$i] !== $arr2[$length - 1 - $i]) {
            return false;
        }
    }
    
    return true;
}

// Example
$array1 = [1, 2, 3, 4, 5];
$array2 = [5, 4, 3, 2, 1];
$array3 = [1, 2, 3, 4, 6];
echo areMirrorImages($array1, $array2) ? 'Mirror images' : 'Not mirror images'; // Output: Mirror images
echo areMirrorImages($array1, $array3) ? 'Mirror images' : 'Not mirror images'; // Output: Not mirror images
```

**💡 Explanation:** We compare each element of the first array with the corresponding element from the end of the second array.

### 44. 🎯 Check if any subarray has a zero sum

**Question:** Check if any subarray has a zero sum.

**Answer:**
```php
function hasZeroSumSubarray($arr) {
    $n = count($arr);
    
    // Check all possible subarrays
    for ($i = 0; $i < $n; $i++) {
        $sum = 0;
        for ($j = $i; $j < $n; $j++) {
            $sum += $arr[$j];
            if ($sum === 0) {
                return true;
            }
        }
    }
    
    return false;
}

// More efficient approach using cumulative sum
function hasZeroSumSubarrayEfficient($arr) {
    $cumulativeSum = 0;
    $sumSet = [0 => true]; // Include 0 for subarrays starting from index 0
    
    foreach ($arr as $num) {
        $cumulativeSum += $num;
        
        if (isset($sumSet[$cumulativeSum])) {
            return true;
        }
        
        $sumSet[$cumulativeSum] = true;
    }
    
    return false;
}

// Example
$array1 = [4, 2, -3, 1, 6];
$array2 = [4, 2, 0, 1, 6];
$array3 = [1, 2, 3, 4];
echo hasZeroSumSubarray($array1) ? 'Has zero sum subarray' : 'No zero sum subarray'; // Output: Has zero sum subarray
echo hasZeroSumSubarray($array3) ? 'Has zero sum subarray' : 'No zero sum subarray'; // Output: No zero sum subarray
```

**💡 Explanation:** We can check all subarrays or use the more efficient approach with cumulative sums.

### 45. 🔢 Detect if any subset sums to a target number

**Question:** Detect if any subset sums to a target number.

**Answer:**
```php
function hasSubsetSum($arr, $target, $index = 0) {
    // Base cases
    if ($target === 0) {
        return true; // Empty subset sums to 0
    }
    
    if ($index >= count($arr)) {
        return false; // No more elements to consider
    }
    
    // Recursive case: include current element or exclude it
    return hasSubsetSum($arr, $target - $arr[$index], $index + 1) || 
           hasSubsetSum($arr, $target, $index + 1);
}

// Iterative approach for better understanding
function hasSubsetSumIterative($arr, $target) {
    $n = count($arr);
    
    // Check all possible subsets (2^n possibilities)
    for ($i = 0; $i < (1 << $n); $i++) {
        $sum = 0;
        
        for ($j = 0; $j < $n; $j++) {
            // Check if j-th bit is set
            if ($i & (1 << $j)) {
                $sum += $arr[$j];
            }
        }
        
        if ($sum === $target) {
            return true;
        }
    }
    
    return false;
}

// Example
$numbers = [3, 34, 4, 12, 5, 2];
echo hasSubsetSum($numbers, 9) ? 'Subset exists' : 'No subset'; // Output: Subset exists (4 + 5 = 9)
echo hasSubsetSum($numbers, 30) ? 'Subset exists' : 'No subset'; // Output: No subset
```

**💡 Explanation:** We use recursion to try including/excluding each element or iterate through all possible subsets.

## 🔧 Data Manipulation

### 46. 🔄 Transpose a 2D array (rows to columns)

**Question:** Transpose a 2D array (rows to columns).

**Answer:**
```php
function transpose($matrix) {
    if (empty($matrix) || empty($matrix[0])) {
        return [];
    }
    
    $rows = count($matrix);
    $cols = count($matrix[0]);
    $transposed = [];
    
    for ($j = 0; $j < $cols; $j++) {
        $transposed[$j] = [];
        for ($i = 0; $i < $rows; $i++) {
            $transposed[$j][$i] = $matrix[$i][$j];
        }
    }
    
    return $transposed;
}

// Example
$matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
];
print_r(transpose($matrix));
// Output: [[1, 4, 7], [2, 5, 8], [3, 6, 9]]
```

**💡 Explanation:** We swap rows and columns by iterating through columns first, then rows.

### 47. 📄 Convert CSV string to array and back

**Question:** Convert CSV string to array and back.

**Answer:**
```php
function csvToArray($csvString) {
    $rows = explode("\n", trim($csvString));
    $array = [];
    
    foreach ($rows as $row) {
        if (trim($row) !== '') {
            $array[] = str_getcsv($row);
        }
    }
    
    return $array;
}

function arrayToCsv($array) {
    $csvString = '';
    
    foreach ($array as $row) {
        $escapedRow = [];
        foreach ($row as $field) {
            // Escape fields containing commas or quotes
            if (strpos($field, ',') !== false || strpos($field, '"') !== false) {
                $field = '"' . str_replace('"', '""', $field) . '"';
            }
            $escapedRow[] = $field;
        }
        $csvString .= implode(',', $escapedRow) . "\n";
    }
    
    return rtrim($csvString, "\n");
}

// Example
$csv = "Name,Age,City\nJohn,25,New York\nJane,30,London";
$array = csvToArray($csv);
print_r($array);
// Output: [['Name', 'Age', 'City'], ['John', '25', 'New York'], ['Jane', '30', 'London']]

echo arrayToCsv($array);
// Output: Name,Age,City\nJohn,25,New York\nJane,30,London
```

**💡 Explanation:** We split CSV by lines and use str_getcsv() for parsing, and properly escape fields when converting back.

### 48. 🧹 Remove null or empty strings from an array

**Question:** Remove null or empty strings from an array.

**Answer:**
```php
function removeNullAndEmpty($arr) {
    $cleaned = [];
    
    foreach ($arr as $key => $value) {
        if ($value !== null && $value !== '') {
            $cleaned[$key] = $value;
        }
    }
    
    return $cleaned;
}

// Recursive version for multidimensional arrays
function removeNullAndEmptyRecursive($arr) {
    $cleaned = [];
    
    foreach ($arr as $key => $value) {
        if (is_array($value)) {
            $value = removeNullAndEmptyRecursive($value);
        }
        
        if ($value !== null && $value !== '' && $value !== []) {
            $cleaned[$key] = $value;
        }
    }
    
    return $cleaned;
}

// Example
$mixed = ['hello', '', 'world', null, 0, false, 'test'];
print_r(removeNullAndEmpty($mixed)); // Output: ['hello', 'world', 0, false, 'test']
```

**💡 Explanation:** We filter out null values and empty strings while preserving other falsy values like 0 and false.

### 49. 🔄 Replace all values matching a pattern in an array

**Question:** Replace all values matching a pattern in an array.

**Answer:**
```php
function replacePattern($arr, $pattern, $replacement) {
    $result = [];
    
    foreach ($arr as $key => $value) {
        if (is_string($value) && preg_match($pattern, $value)) {
            $result[$key] = preg_replace($pattern, $replacement, $value);
        } else {
            $result[$key] = $value;
        }
    }
    
    return $result;
}

// Simple string replacement version
function replaceValue($arr, $search, $replacement) {
    $result = [];
    
    foreach ($arr as $key => $value) {
        if ($value === $search) {
            $result[$key] = $replacement;
        } else {
            $result[$key] = $value;
        }
    }
    
    return $result;
}

// Example
$data = ['hello@domain.com', 'test@example.org', 'user@site.net', 'regular text'];
$emailPattern = '/\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b/';
print_r(replacePattern($data, $emailPattern, '[EMAIL]'));
// Output: ['[EMAIL]', '[EMAIL]', '[EMAIL]', 'regular text']

$simple = ['apple', 'banana', 'apple', 'orange'];
print_r(replaceValue($simple, 'apple', 'fruit'));
// Output: ['fruit', 'banana', 'fruit', 'orange']
```

**💡 Explanation:** We can use regular expressions for pattern matching or simple string comparison for exact matches.

### 50. 🔧 Rebuild an array from keys and values (two separate arrays)

**Question:** Rebuild an array from keys and values (two separate arrays).

**Answer:**
```php
function combineArrays($keys, $values) {
    $combined = [];
    $minLength = min(count($keys), count($values));
    
    for ($i = 0; $i < $minLength; $i++) {
        $combined[$keys[$i]] = $values[$i];
    }
    
    return $combined;
}

// Handle mismatched lengths
function combineArraysSafe($keys, $values, $defaultValue = null) {
    $combined = [];
    $maxLength = max(count($keys), count($values));
    
    for ($i = 0; $i < $maxLength; $i++) {
        $key = isset($keys[$i]) ? $keys[$i] : $i;
        $value = isset($values[$i]) ? $values[$i] : $defaultValue;
        $combined[$key] = $value;
    }
    
    return $combined;
}

// Example
$keys = ['name', 'age', 'city'];
$values = ['John', 25, 'New York'];
print_r(combineArrays($keys, $values));
// Output: ['name' => 'John', 'age' => 25, 'city' => 'New York']

$keys2 = ['a', 'b', 'c', 'd'];
$values2 = [1, 2];
print_r(combineArraysSafe($keys2, $values2, 'N/A'));
// Output: ['a' => 1, 'b' => 2, 'c' => 'N/A', 'd' => 'N/A']
```

**💡 Explanation:** We pair up elements from both arrays using their positions, handling cases where array lengths don't match.

---

## 🎉 Conclusion

This comprehensive guide covers 50 essential PHP array questions ranging from basic operations to advanced algorithms. Each solution is implemented manually to help you understand the underlying concepts and improve your programming skills.

### 🚀 Key Takeaways:
- **Manual Implementation**: All solutions avoid built-in functions where possible to demonstrate core logic
- **Performance Considerations**: Some problems include multiple approaches (e.g., recursive vs iterative)
- **Real-World Applications**: Many examples reflect actual development scenarios
- **Code Quality**: Solutions include error handling and edge case considerations

### 📖 Practice Tips:
1. Try implementing these solutions without looking at the answers first
2. Optimize the solutions for better time/space complexity
3. Add unit tests for each function
4. Consider edge cases like empty arrays, null values, and large datasets
5. Practice explaining the logic in technical interviews

Happy coding! 🎯