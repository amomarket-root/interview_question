# 📝 PHP String Questions & Answers - Complete Guide

## 🔰 Basic String Handling

### 1. 🔄 Reverse a string without using strrev()

**Question:** Reverse a string without using strrev().

**Answer:**
```php
function reverseString($str) {
    $reversed = '';
    $length = strlen($str);
    
    for ($i = $length - 1; $i >= 0; $i--) {
        $reversed .= $str[$i];
    }
    
    return $reversed;
}

// Alternative approach using recursion
function reverseStringRecursive($str) {
    if (strlen($str) <= 1) {
        return $str;
    }
    
    return substr($str, -1) . reverseStringRecursive(substr($str, 0, -1));
}

// Example
echo reverseString("hello world"); // Output: dlrow olleh
echo reverseStringRecursive("PHP"); // Output: PHP
```

**💡 Explanation:** We iterate from the last character to the first, building the reversed string character by character.

### 2. 🪞 Check if a string is a palindrome

**Question:** Check if a string is a palindrome.

**Answer:**
```php
function isPalindrome($str) {
    $str = strtolower($str);
    $length = strlen($str);
    
    for ($i = 0; $i < $length / 2; $i++) {
        if ($str[$i] !== $str[$length - 1 - $i]) {
            return false;
        }
    }
    
    return true;
}

// Ignore spaces and punctuation version
function isPalindromeClean($str) {
    $cleaned = strtolower(preg_replace('/[^a-zA-Z0-9]/', '', $str));
    $length = strlen($cleaned);
    
    for ($i = 0; $i < $length / 2; $i++) {
        if ($cleaned[$i] !== $cleaned[$length - 1 - $i]) {
            return false;
        }
    }
    
    return true;
}

// Example
echo isPalindrome("racecar") ? 'Palindrome' : 'Not palindrome'; // Output: Palindrome
echo isPalindromeClean("A man, a plan, a canal: Panama") ? 'Palindrome' : 'Not palindrome'; // Output: Palindrome
```

**💡 Explanation:** We compare characters from both ends moving toward the center, optionally cleaning the string first.

### 3. 🅰️ Count vowels and consonants in a string

**Question:** Count vowels and consonants in a string.

**Answer:**
```php
function countVowelsConsonants($str) {
    $vowels = 0;
    $consonants = 0;
    $vowelChars = 'aeiouAEIOU';
    
    for ($i = 0; $i < strlen($str); $i++) {
        $char = $str[$i];
        
        if (ctype_alpha($char)) {
            if (strpos($vowelChars, $char) !== false) {
                $vowels++;
            } else {
                $consonants++;
            }
        }
    }
    
    return ['vowels' => $vowels, 'consonants' => $consonants];
}

// Example
$text = "Hello World";
$result = countVowelsConsonants($text);
echo "Vowels: " . $result['vowels'] . ", Consonants: " . $result['consonants'];
// Output: Vowels: 3, Consonants: 7
```

**💡 Explanation:** We iterate through each character, checking if it's alphabetic and then whether it's a vowel or consonant.

### 4. 🧹 Remove all whitespace characters from a string

**Question:** Remove all whitespace characters from a string.

**Answer:**
```php
function removeWhitespace($str) {
    $result = '';
    
    for ($i = 0; $i < strlen($str); $i++) {
        if (!ctype_space($str[$i])) {
            $result .= $str[$i];
        }
    }
    
    return $result;
}

// Manual approach without ctype_space
function removeWhitespaceManual($str) {
    $result = '';
    $whitespaces = [' ', "\t", "\n", "\r", "\0", "\x0B"];
    
    for ($i = 0; $i < strlen($str); $i++) {
        if (!in_array($str[$i], $whitespaces)) {
            $result .= $str[$i];
        }
    }
    
    return $result;
}

// Example
echo removeWhitespace("Hello  World\t\nTest"); // Output: HelloWorldTest
```

**💡 Explanation:** We build a new string by including only non-whitespace characters.

### 5. 📏 Find the length of the longest word in a sentence

**Question:** Find the length of the longest word in a sentence.

**Answer:**
```php
function longestWordLength($sentence) {
    $words = explode(' ', $sentence);
    $maxLength = 0;
    
    foreach ($words as $word) {
        // Clean word of punctuation
        $cleanWord = preg_replace('/[^a-zA-Z0-9]/', '', $word);
        $length = strlen($cleanWord);
        
        if ($length > $maxLength) {
            $maxLength = $length;
        }
    }
    
    return $maxLength;
}

// Manual word extraction without explode
function longestWordLengthManual($sentence) {
    $maxLength = 0;
    $currentLength = 0;
    
    for ($i = 0; $i < strlen($sentence); $i++) {
        if (ctype_alnum($sentence[$i])) {
            $currentLength++;
        } else {
            if ($currentLength > $maxLength) {
                $maxLength = $currentLength;
            }
            $currentLength = 0;
        }
    }
    
    // Check last word
    if ($currentLength > $maxLength) {
        $maxLength = $currentLength;
    }
    
    return $maxLength;
}

// Example
echo longestWordLength("The quick brown fox jumps"); // Output: 5 (quick, brown, jumps)
```

**💡 Explanation:** We split the sentence into words and track the maximum length, cleaning punctuation from each word.

### 6. 📝 Convert a string to title case (hello world → Hello World)

**Question:** Convert a string to title case (hello world → Hello World).

**Answer:**
```php
function toTitleCase($str) {
    $result = '';
    $capitalizeNext = true;
    
    for ($i = 0; $i < strlen($str); $i++) {
        $char = $str[$i];
        
        if (ctype_alpha($char)) {
            if ($capitalizeNext) {
                $result .= strtoupper($char);
                $capitalizeNext = false;
            } else {
                $result .= strtolower($char);
            }
        } else {
            $result .= $char;
            $capitalizeNext = true;
        }
    }
    
    return $result;
}

// Example
echo toTitleCase("hello world from php"); // Output: Hello World From Php
echo toTitleCase("the quick-brown fox"); // Output: The Quick-Brown Fox
```

**💡 Explanation:** We track when to capitalize the next letter (after non-alphabetic characters) and apply appropriate casing.

### 7. 🔢 Count occurrences of a character in a string

**Question:** Count occurrences of a character in a string.

**Answer:**
```php
function countCharOccurrences($str, $char) {
    $count = 0;
    
    for ($i = 0; $i < strlen($str); $i++) {
        if ($str[$i] === $char) {
            $count++;
        }
    }
    
    return $count;
}

// Case-insensitive version
function countCharOccurrencesIgnoreCase($str, $char) {
    $count = 0;
    $str = strtolower($str);
    $char = strtolower($char);
    
    for ($i = 0; $i < strlen($str); $i++) {
        if ($str[$i] === $char) {
            $count++;
        }
    }
    
    return $count;
}

// Example
echo countCharOccurrences("hello world", "l"); // Output: 3
echo countCharOccurrencesIgnoreCase("Hello World", "L"); // Output: 3
```

**💡 Explanation:** We iterate through the string and increment a counter whenever we find the target character.

### 8. 🗑️ Remove duplicate characters from a string

**Question:** Remove duplicate characters from a string.

**Answer:**
```php
function removeDuplicates($str) {
    $result = '';
    $seen = [];
    
    for ($i = 0; $i < strlen($str); $i++) {
        $char = $str[$i];
        
        if (!isset($seen[$char])) {
            $result .= $char;
            $seen[$char] = true;
        }
    }
    
    return $result;
}

// Case-insensitive version
function removeDuplicatesIgnoreCase($str) {
    $result = '';
    $seen = [];
    
    for ($i = 0; $i < strlen($str); $i++) {
        $char = $str[$i];
        $lowerChar = strtolower($char);
        
        if (!isset($seen[$lowerChar])) {
            $result .= $char;
            $seen[$lowerChar] = true;
        }
    }
    
    return $result;
}

// Example
echo removeDuplicates("programming"); // Output: progamin
echo removeDuplicatesIgnoreCase("Hello World"); // Output: Helo Wrd
```

**💡 Explanation:** We use an associative array to track seen characters and only add unseen characters to the result.

### 9. 📏 Replace multiple spaces with a single space

**Question:** Replace multiple spaces with a single space.

**Answer:**
```php
function replaceMultipleSpaces($str) {
    $result = '';
    $previousWasSpace = false;
    
    for ($i = 0; $i < strlen($str); $i++) {
        $char = $str[$i];
        
        if ($char === ' ') {
            if (!$previousWasSpace) {
                $result .= $char;
                $previousWasSpace = true;
            }
        } else {
            $result .= $char;
            $previousWasSpace = false;
        }
    }
    
    return $result;
}

// Example
echo replaceMultipleSpaces("Hello    world   from     PHP"); // Output: Hello world from PHP
```

**💡 Explanation:** We track whether the previous character was a space and only add spaces when the previous character wasn't a space.

### 10. ✅ Check if a string contains only alphabets

**Question:** Check if a string contains only alphabets.

**Answer:**
```php
function containsOnlyAlphabets($str) {
    if (strlen($str) === 0) {
        return false;
    }
    
    for ($i = 0; $i < strlen($str); $i++) {
        if (!ctype_alpha($str[$i])) {
            return false;
        }
    }
    
    return true;
}

// Manual version without ctype_alpha
function containsOnlyAlphabetsManual($str) {
    if (strlen($str) === 0) {
        return false;
    }
    
    for ($i = 0; $i < strlen($str); $i++) {
        $char = $str[$i];
        if (!(($char >= 'a' && $char <= 'z') || ($char >= 'A' && $char <= 'Z'))) {
            return false;
        }
    }
    
    return true;
}

// Example
echo containsOnlyAlphabets("HelloWorld") ? 'Only alphabets' : 'Contains non-alphabets'; // Output: Only alphabets
echo containsOnlyAlphabets("Hello123") ? 'Only alphabets' : 'Contains non-alphabets'; // Output: Contains non-alphabets
```

**💡 Explanation:** We check each character to ensure it falls within the alphabetic range.

## 🔍 Searching & Matching

### 11. 🎯 Check if a substring exists in a string (without strpos())

**Question:** Check if a substring exists in a string (without strpos()).

**Answer:**
```php
function substringExists($haystack, $needle) {
    $haystackLen = strlen($haystack);
    $needleLen = strlen($needle);
    
    if ($needleLen === 0) return true;
    if ($needleLen > $haystackLen) return false;
    
    for ($i = 0; $i <= $haystackLen - $needleLen; $i++) {
        $match = true;
        
        for ($j = 0; $j < $needleLen; $j++) {
            if ($haystack[$i + $j] !== $needle[$j]) {
                $match = false;
                break;
            }
        }
        
        if ($match) {
            return true;
        }
    }
    
    return false;
}

// Case-insensitive version
function substringExistsIgnoreCase($haystack, $needle) {
    return substringExists(strtolower($haystack), strtolower($needle));
}

// Example
echo substringExists("Hello World", "World") ? 'Found' : 'Not found'; // Output: Found
echo substringExists("PHP Programming", "python") ? 'Found' : 'Not found'; // Output: Not found
```

**💡 Explanation:** We use a sliding window approach to check if the needle matches at each position in the haystack.

### 12. ⭐ Find the first non-repeating character in a string

**Question:** Find the first non-repeating character in a string.

**Answer:**
```php
function firstNonRepeatingChar($str) {
    $charCount = [];
    
    // Count occurrences of each character
    for ($i = 0; $i < strlen($str); $i++) {
        $char = $str[$i];
        $charCount[$char] = ($charCount[$char] ?? 0) + 1;
    }
    
    // Find first character with count 1
    for ($i = 0; $i < strlen($str); $i++) {
        if ($charCount[$str[$i]] === 1) {
            return $str[$i];
        }
    }
    
    return null; // No non-repeating character found
}

// Example
echo firstNonRepeatingChar("abccba"); // Output: null (all characters repeat)
echo firstNonRepeatingChar("abcabc"); // Output: null
echo firstNonRepeatingChar("abccbad"); // Output: d
```

**💡 Explanation:** We first count all character occurrences, then find the first character that appears exactly once.

### 13. 📊 Count the number of words in a sentence

**Question:** Count the number of words in a sentence.

**Answer:**
```php
function countWords($sentence) {
    $wordCount = 0;
    $inWord = false;
    
    for ($i = 0; $i < strlen($sentence); $i++) {
        $char = $sentence[$i];
        
        if (ctype_alnum($char)) {
            if (!$inWord) {
                $wordCount++;
                $inWord = true;
            }
        } else {
            $inWord = false;
        }
    }
    
    return $wordCount;
}

// Alternative approach
function countWordsAlternative($sentence) {
    $words = preg_split('/\s+/', trim($sentence), -1, PREG_SPLIT_NO_EMPTY);
    return count($words);
}

// Example
echo countWords("Hello world from PHP"); // Output: 4
echo countWords("  Multiple   spaces   between  words  "); // Output: 4
```

**💡 Explanation:** We track when we're inside a word and increment the counter each time we encounter the start of a new word.

### 14. 🔤 Check if two strings are anagrams

**Question:** Check if two strings are anagrams.

**Answer:**
```php
function areAnagrams($str1, $str2) {
    // Remove spaces and convert to lowercase
    $str1 = strtolower(str_replace(' ', '', $str1));
    $str2 = strtolower(str_replace(' ', '', $str2));
    
    if (strlen($str1) !== strlen($str2)) {
        return false;
    }
    
    $charCount1 = [];
    $charCount2 = [];
    
    // Count characters in both strings
    for ($i = 0; $i < strlen($str1); $i++) {
        $charCount1[$str1[$i]] = ($charCount1[$str1[$i]] ?? 0) + 1;
        $charCount2[$str2[$i]] = ($charCount2[$str2[$i]] ?? 0) + 1;
    }
    
    // Compare character counts
    foreach ($charCount1 as $char => $count) {
        if (!isset($charCount2[$char]) || $charCount2[$char] !== $count) {
            return false;
        }
    }
    
    return true;
}

// Example
echo areAnagrams("listen", "silent") ? 'Anagrams' : 'Not anagrams'; // Output: Anagrams
echo areAnagrams("hello", "world") ? 'Anagrams' : 'Not anagrams'; // Output: Not anagrams
```

**💡 Explanation:** We count character frequencies in both strings and compare them to determine if they're anagrams.

### 15. 📍 Get all indexes of a substring in a string

**Question:** Get all indexes of a substring in a string.

**Answer:**
```php
function getAllIndexes($haystack, $needle) {
    $indexes = [];
    $haystackLen = strlen($haystack);
    $needleLen = strlen($needle);
    
    if ($needleLen === 0) return $indexes;
    
    for ($i = 0; $i <= $haystackLen - $needleLen; $i++) {
        $match = true;
        
        for ($j = 0; $j < $needleLen; $j++) {
            if ($haystack[$i + $j] !== $needle[$j]) {
                $match = false;
                break;
            }
        }
        
        if ($match) {
            $indexes[] = $i;
        }
    }
    
    return $indexes;
}

// Example
$text = "the cat in the hat sits on the mat";
print_r(getAllIndexes($text, "the")); // Output: [0, 11, 27]
print_r(getAllIndexes($text, "at")); // Output: [6, 16, 32, 35]
```

**💡 Explanation:** We use the same sliding window approach as substring search but collect all matching positions.

## 🔄 Modification & Transformation

### 16. 🔄 Swap the case of each character (HeLLo → hEllO)

**Question:** Swap the case of each character (HeLLo → hEllO).

**Answer:**
```php
function swapCase($str) {
    $result = '';
    
    for ($i = 0; $i < strlen($str); $i++) {
        $char = $str[$i];
        
        if ($char >= 'a' && $char <= 'z') {
            $result .= strtoupper($char);
        } elseif ($char >= 'A' && $char <= 'Z') {
            $result .= strtolower($char);
        } else {
            $result .= $char;
        }
    }
    
    return $result;
}

// Example
echo swapCase("HeLLo WoRLd"); // Output: hEllO wOrld
echo swapCase("PHP 8.0"); // Output: php 8.0
```

**💡 Explanation:** We check each character and convert uppercase to lowercase and vice versa, leaving non-alphabetic characters unchanged.

### 17. 📝 Capitalize the first letter of each sentence

**Question:** Capitalize the first letter of each sentence.

**Answer:**
```php
function capitalizeSentences($text) {
    $result = '';
    $capitalizeNext = true;
    $sentenceEnders = ['.', '!', '?'];
    
    for ($i = 0; $i < strlen($text); $i++) {
        $char = $text[$i];
        
        if (ctype_alpha($char) && $capitalizeNext) {
            $result .= strtoupper($char);
            $capitalizeNext = false;
        } else {
            $result .= $char;
            
            if (in_array($char, $sentenceEnders)) {
                $capitalizeNext = true;
            }
        }
    }
    
    return $result;
}

// Example
echo capitalizeSentences("hello world. how are you? i am fine!");
// Output: Hello world. How are you? I am fine!
```

**💡 Explanation:** We track when to capitalize the next letter (after sentence-ending punctuation) and apply capitalization accordingly.

### 18. 🔐 Mask a string except last 4 characters (********1234)

**Question:** Mask a string except last 4 characters (********1234).

**Answer:**
```php
function maskString($str, $maskChar = '*', $visibleCount = 4) {
    $length = strlen($str);
    
    if ($length <= $visibleCount) {
        return $str; // Don't mask if string is too short
    }
    
    $masked = str_repeat($maskChar, $length - $visibleCount);
    $visible = substr($str, -$visibleCount);
    
    return $masked . $visible;
}

// Manual implementation
function maskStringManual($str, $maskChar = '*', $visibleCount = 4) {
    $length = strlen($str);
    $result = '';
    
    if ($length <= $visibleCount) {
        return $str;
    }
    
    for ($i = 0; $i < $length; $i++) {
        if ($i < $length - $visibleCount) {
            $result .= $maskChar;
        } else {
            $result .= $str[$i];
        }
    }
    
    return $result;
}

// Example
echo maskString("1234567890123456"); // Output: ************3456
echo maskString("hello@example.com", '*', 6); // Output: *********ple.com
```

**💡 Explanation:** We replace all characters except the last N with a mask character, useful for credit cards, passwords, etc.

### 19. ❌ Remove a specific character from a string

**Question:** Remove a specific character from a string.

**Answer:**
```php
function removeCharacter($str, $charToRemove) {
    $result = '';
    
    for ($i = 0; $i < strlen($str); $i++) {
        if ($str[$i] !== $charToRemove) {
            $result .= $str[$i];
        }
    }
    
    return $result;
}

// Case-insensitive version
function removeCharacterIgnoreCase($str, $charToRemove) {
    $result = '';
    $charToRemove = strtolower($charToRemove);
    
    for ($i = 0; $i < strlen($str); $i++) {
        if (strtolower($str[$i]) !== $charToRemove) {
            $result .= $str[$i];
        }
    }
    
    return $result;
}

// Example
echo removeCharacter("Hello World", "l"); // Output: Heo Word
echo removeCharacterIgnoreCase("Hello World", "L"); // Output: Heo Word
```

**💡 Explanation:** We build a new string by including only characters that don't match the target character.

### 20. 🔁 Repeat a string n times without using str_repeat()

**Question:** Repeat a string n times without using str_repeat().

**Answer:**
```php
function repeatString($str, $times) {
    $result = '';
    
    for ($i = 0; $i < $times; $i++) {
        $result .= $str;
    }
    
    return $result;
}

// Recursive version
function repeatStringRecursive($str, $times) {
    if ($times <= 0) {
        return '';
    }
    
    if ($times === 1) {
        return $str;
    }
    
    return $str . repeatStringRecursive($str, $times - 1);
}

// Example
echo repeatString("PHP", 3); // Output: PHPPHPPHP
echo repeatStringRecursive("Hello ", 2); // Output: Hello Hello 
```

**💡 Explanation:** We use a simple loop to concatenate the string the specified number of times.

## 🚀 Advanced String Patterns

### 21. 🔍 Find the longest repeated substring

**Question:** Find the longest repeated substring.

**Answer:**
```php
function longestRepeatedSubstring($str) {
    $length = strlen($str);
    $longest = '';
    
    // Check all possible substrings
    for ($i = 0; $i < $length; $i++) {
        for ($j = $i + 1; $j < $length; $j++) {
            $substring = substr($str, $i, $j - $i + 1);
            
            // Check if this substring appears later in the string
            for ($k = $j + 1; $k <= $length - strlen($substring); $k++) {
                if (substr($str, $k, strlen($substring)) === $substring) {
                    if (strlen($substring) > strlen($longest)) {
                        $longest = $substring;
                    }
                }
            }
        }
    }
    
    return $longest;
}

// Example
echo longestRepeatedSubstring("abcabcabc"); // Output: abcabc
echo longestRepeatedSubstring("aabaaaba"); // Output: aaba
```

**💡 Explanation:** We generate all possible substrings and check if they appear multiple times in the string.

### 22. 🤝 Find the longest common prefix of two strings

**Question:** Find the longest common prefix of two strings.

**Answer:**
```php
function longestCommonPrefix($str1, $str2) {
    $prefix = '';
    $minLength = min(strlen($str1), strlen($str2));
    
    for ($i = 0; $i < $minLength; $i++) {
        if ($str1[$i] === $str2[$i]) {
            $prefix .= $str1[$i];
        } else {
            break;
        }
    }
    
    return $prefix;
}

// For multiple strings
function longestCommonPrefixMultiple($strings) {
    if (empty($strings)) {
        return '';
    }
    
    if (count($strings) === 1) {
        return $strings[0];
    }
    
    $prefix = $strings[0];
    
    for ($i = 1; $i < count($strings); $i++) {
        $prefix = longestCommonPrefix($prefix, $strings[$i]);
        if ($prefix === '') {
            break;
        }
    }
    
    return $prefix;
}

// Example
echo longestCommonPrefix("flower", "flight"); // Output: fl
echo longestCommonPrefix("dog", "racecar"); // Output: (empty)

$words = ["flower", "flow", "flight"];
echo longestCommonPrefixMultiple($words); // Output: fl
```

**💡 Explanation:** We compare characters at the same position in both strings until we find a mismatch.

### 23. 🪞 Find all palindromic substrings in a string

**Question:** Find all palindromic substrings in a string.

**Answer:**
```php
function findPalindromicSubstrings($str) {
    $palindromes = [];
    $length = strlen($str);
    
    // Check all possible substrings
    for ($i = 0; $i < $length; $i++) {
        for ($j = $i; $j < $length; $j++) {
            $substring = substr($str, $i, $j - $i + 1);
            
            if (isPalindromeSimple($substring)) {
                $palindromes[] = $substring;
            }
        }
    }
    
    return array_unique($palindromes);
}

function isPalindromeSimple($str) {
    $length = strlen($str);
    
    for ($i = 0; $i < $length / 2; $i++) {
        if ($str[$i] !== $str[$length - 1 - $i]) {
            return false;
        }
    }
    
    return true;
}

// Example
print_r(findPalindromicSubstrings("ababa"));
// Output: ["a", "b", "aba", "bab", "ababa"]
```

**💡 Explanation:** We generate all possible substrings and check each one to see if it's a palindrome.

### 24. 🔄 Check if a string is a rotation of another (abcde and cdeab)

**Question:** Check if a string is a rotation of another (abcde and cdeab).

**Answer:**
```php
function isRotation($str1, $str2) {
    if (strlen($str1) !== strlen($str2)) {
        return false;
    }
    
    if (strlen($str1) === 0) {
        return true;
    }
    
    // Check if str2 is a substring of str1 + str1
    $doubled = $str1 . $str1;
    return substringExists($doubled, $str2);
}

// Manual approach
function isRotationManual($str1, $str2) {
    if (strlen($str1) !== strlen($str2)) {
        return false;
    }
    
    $length = strlen($str1);
    
    // Try all possible rotation points
    for ($i = 0; $i < $length; $i++) {
        $rotation = substr($str1, $i) . substr($str1, 0, $i);
        if ($rotation === $str2) {
            return true;
        }
    }
    
    return false;
}

// Example
echo isRotation("abcde", "cdeab") ? 'Is rotation' : 'Not rotation'; // Output: Is rotation
echo isRotation("abcde", "abced") ? 'Is rotation' : 'Not rotation'; // Output: Not rotation
```

**💡 Explanation:** A rotation of a string is a substring of the string concatenated with itself.

### 25. 🗜️ Compress a string using run-length encoding (aaabb → a3b2)

**Question:** Compress a string using run-length encoding (aaabb → a3b2).

**Answer:**
```php
function runLengthEncode($str) {
    if (strlen($str) === 0) {
        return '';
    }
    
    $result = '';
    $currentChar = $str[0];
    $count = 1;
    
    for ($i = 1; $i < strlen($str); $i++) {
        if ($str[$i] === $currentChar) {
            $count++;
        } else {
            $result .= $currentChar . $count;
            $currentChar = $str[$i];
            $count = 1;
        }
    }
    
    // Add the last group
    $result .= $currentChar . $count;
    
    return $result;
}

// Decode function
function runLengthDecode($encoded) {
    $result = '';
    $i = 0;
    
    while ($i < strlen($encoded)) {
        $char = $encoded[$i];
        $i++;
        
        $countStr = '';
        while ($i < strlen($encoded) && ctype_digit($encoded[$i])) {
            $countStr .= $encoded[$i];
            $i++;
        }
        
        $count = intval($countStr);
        $result .= str_repeat($char, $count);
    }
    
    return $result;
}

// Example
echo runLengthEncode("aaabbbccdaa"); // Output: a3b3c2d1a2
echo runLengthDecode("a3b3c2d1a2"); // Output: aaabbbccdaa
```

**💡 Explanation:** We track consecutive identical characters and encode them as character + count pairs.

## 🔤 Regex Practice

### 26. 🔢 Extract all numbers from a string using regex

**Question:** Extract all numbers from a string using regex.

**Answer:**
```php
function extractNumbers($str) {
    $numbers = [];
    preg_match_all('/\d+/', $str, $matches);
    return $matches[0];
}

// Manual approach without regex
function extractNumbersManual($str) {
    $numbers = [];
    $currentNumber = '';
    
    for ($i = 0; $i < strlen($str); $i++) {
        if (ctype_digit($str[$i])) {
            $currentNumber .= $str[$i];
        } else {
            if ($currentNumber !== '') {
                $numbers[] = $currentNumber;
                $currentNumber = '';
            }
        }
    }
    
    // Add last number if exists
    if ($currentNumber !== '') {
        $numbers[] = $currentNumber;
    }
    
    return $numbers;
}

// Example
print_r(extractNumbers("I have 5 apples and 10 oranges, total 15 fruits"));
// Output: ["5", "10", "15"]
```

**💡 Explanation:** We use regex to find digit sequences or manually scan for consecutive digits.

### 27. 📧 Validate an email address manually

**Question:** Validate an email address manually.

**Answer:**
```php
function isValidEmail($email) {
    // Basic checks
    if (strlen($email) === 0 || strlen($email) > 254) {
        return false;
    }
    
    // Must contain exactly one @
    $atCount = 0;
    $atPos = -1;
    for ($i = 0; $i < strlen($email); $i++) {
        if ($email[$i] === '@') {
            $atCount++;
            $atPos = $i;
        }
    }
    
    if ($atCount !== 1 || $atPos === 0 || $atPos === strlen($email) - 1) {
        return false;
    }
    
    $localPart = substr($email, 0, $atPos);
    $domainPart = substr($email, $atPos + 1);
    
    // Validate local part
    if (!isValidEmailLocal($localPart)) {
        return false;
    }
    
    // Validate domain part
    if (!isValidEmailDomain($domainPart)) {
        return false;
    }
    
    return true;
}

function isValidEmailLocal($local) {
    if (strlen($local) === 0 || strlen($local) > 64) {
        return false;
    }
    
    // Check allowed characters
    for ($i = 0; $i < strlen($local); $i++) {
        $char = $local[$i];
        if (!ctype_alnum($char) && !in_array($char, ['.', '_', '-', '+'])) {
            return false;
        }
    }
    
    // Cannot start or end with dot
    if ($local[0] === '.' || $local[strlen($local) - 1] === '.') {
        return false;
    }
    
    return true;
}

function isValidEmailDomain($domain) {
    if (strlen($domain) === 0 || strlen($domain) > 253) {
        return false;
    }
    
    // Must contain at least one dot
    if (strpos($domain, '.') === false) {
        return false;
    }
    
    $parts = explode('.', $domain);
    
    foreach ($parts as $part) {
        if (strlen($part) === 0 || strlen($part) > 63) {
            return false;
        }
        
        // Check allowed characters
        for ($i = 0; $i < strlen($part); $i++) {
            $char = $part[$i];
            if (!ctype_alnum($char) && $char !== '-') {
                return false;
            }
        }
        
        // Cannot start or end with hyphen
        if ($part[0] === '-' || $part[strlen($part) - 1] === '-') {
            return false;
        }
    }
    
    return true;
}

// Example
echo isValidEmail("user@example.com") ? 'Valid' : 'Invalid'; // Output: Valid
echo isValidEmail("invalid.email") ? 'Valid' : 'Invalid'; // Output: Invalid
```

**💡 Explanation:** We manually validate email structure by checking the @ symbol, local part, and domain part according to basic email rules.

### 28. 🔤 Find all words starting with a capital letter

**Question:** Find all words starting with a capital letter.

**Answer:**
```php
function findCapitalWords($text) {
    $words = [];
    $currentWord = '';
    
    for ($i = 0; $i < strlen($text); $i++) {
        $char = $text[$i];
        
        if (ctype_alpha($char)) {
            $currentWord .= $char;
        } else {
            if ($currentWord !== '' && ctype_upper($currentWord[0])) {
                $words[] = $currentWord;
            }
            $currentWord = '';
        }
    }
    
    // Check last word
    if ($currentWord !== '' && ctype_upper($currentWord[0])) {
        $words[] = $currentWord;
    }
    
    return $words;
}

// Using regex
function findCapitalWordsRegex($text) {
    preg_match_all('/\b[A-Z][a-zA-Z]*\b/', $text, $matches);
    return $matches[0];
}

// Example
print_r(findCapitalWords("Hello World from PHP Programming"));
// Output: ["Hello", "World", "PHP", "Programming"]
```

**💡 Explanation:** We extract words and check if they start with an uppercase letter.

### 29. 🔄 Replace all non-alphanumeric characters with _

**Question:** Replace all non-alphanumeric characters with _.

**Answer:**
```php
function replaceNonAlphanumeric($str, $replacement = '_') {
    $result = '';
    
    for ($i = 0; $i < strlen($str); $i++) {
        $char = $str[$i];
        
        if (ctype_alnum($char)) {
            $result .= $char;
        } else {
            $result .= $replacement;
        }
    }
    
    return $result;
}

// Using regex
function replaceNonAlphanumericRegex($str, $replacement = '_') {
    return preg_replace('/[^a-zA-Z0-9]/', $replacement, $str);
}

// Example
echo replaceNonAlphanumeric("Hello, World! How are you?");
// Output: Hello__World__How_are_you_
```

**💡 Explanation:** We replace any character that isn't alphanumeric with the specified replacement character.

### 30. ✂️ Split a string by multiple delimiters (e.g. , and ;)

**Question:** Split a string by multiple delimiters (e.g. , and ;).

**Answer:**
```php
function splitByDelimiters($str, $delimiters) {
    $parts = [];
    $currentPart = '';
    
    for ($i = 0; $i < strlen($str); $i++) {
        $char = $str[$i];
        
        if (in_array($char, $delimiters)) {
            if ($currentPart !== '') {
                $parts[] = trim($currentPart);
                $currentPart = '';
            }
        } else {
            $currentPart .= $char;
        }
    }
    
    // Add last part
    if ($currentPart !== '') {
        $parts[] = trim($currentPart);
    }
    
    return $parts;
}

// Using regex
function splitByDelimitersRegex($str, $delimiters) {
    $pattern = '/[' . preg_quote(implode('', $delimiters), '/') . ']+/';
    return array_filter(array_map('trim', preg_split($pattern, $str)));
}

// Example
$text = "apple,banana;orange,grape;kiwi";
print_r(splitByDelimiters($text, [',', ';']));
// Output: ["apple", "banana", "orange", "grape", "kiwi"]
```

**💡 Explanation:** We manually check each character against our list of delimiters and split accordingly.

## 🔐 Encoding & Conversion

### 31. 🔢 Convert a string to ASCII values

**Question:** Convert a string to ASCII values.

**Answer:**
```php
function stringToAscii($str) {
    $asciiValues = [];
    
    for ($i = 0; $i < strlen($str); $i++) {
        $asciiValues[] = ord($str[$i]);
    }
    
    return $asciiValues;
}

// As string format
function stringToAsciiString($str, $separator = ' ') {
    $asciiValues = [];
    
    for ($i = 0; $i < strlen($str); $i++) {
        $asciiValues[] = ord($str[$i]);
    }
    
    return implode($separator, $asciiValues);
}

// Example
print_r(stringToAscii("Hello")); // Output: [72, 101, 108, 108, 111]
echo stringToAsciiString("Hi"); // Output: 72 105
```

**💡 Explanation:** We use PHP's ord() function to get the ASCII value of each character.

### 32. 🔤 Convert ASCII values back to characters

**Question:** Convert ASCII values back to characters.

**Answer:**
```php
function asciiToString($asciiArray) {
    $str = '';
    
    foreach ($asciiArray as $ascii) {
        $str .= chr($ascii);
    }
    
    return $str;
}

// From space-separated string
function asciiStringToString($asciiString, $separator = ' ') {
    $asciiValues = explode($separator, $asciiString);
    $str = '';
    
    foreach ($asciiValues as $ascii) {
        $str .= chr(intval($ascii));
    }
    
    return $str;
}

// Example
echo asciiToString([72, 101, 108, 108, 111]); // Output: Hello
echo asciiStringToString("72 105"); // Output: Hi
```

**💡 Explanation:** We use PHP's chr() function to convert ASCII values back to characters.

### 33. ⚖️ Check if two strings are equal ignoring cases

**Question:** Check if two strings are equal ignoring cases.

**Answer:**
```php
function isEqualIgnoreCase($str1, $str2) {
    if (strlen($str1) !== strlen($str2)) {
        return false;
    }
    
    for ($i = 0; $i < strlen($str1); $i++) {
        if (strtolower($str1[$i]) !== strtolower($str2[$i])) {
            return false;
        }
    }
    
    return true;
}

// Manual case conversion
function isEqualIgnoreCaseManual($str1, $str2) {
    if (strlen($str1) !== strlen($str2)) {
        return false;
    }
    
    for ($i = 0; $i < strlen($str1); $i++) {
        $char1 = $str1[$i];
        $char2 = $str2[$i];
        
        // Convert to lowercase manually
        if ($char1 >= 'A' && $char1 <= 'Z') {
            $char1 = chr(ord($char1) + 32);
        }
        if ($char2 >= 'A' && $char2 <= 'Z') {
            $char2 = chr(ord($char2) + 32);
        }
        
        if ($char1 !== $char2) {
            return false;
        }
    }
    
    return true;
}

// Example
echo isEqualIgnoreCase("Hello", "HELLO") ? 'Equal' : 'Not equal'; // Output: Equal
echo isEqualIgnoreCase("World", "word") ? 'Equal' : 'Not equal'; // Output: Not equal
```

**💡 Explanation:** We compare characters after converting them to lowercase, either using built-in functions or manual ASCII manipulation.

### 34. 🔐 Encrypt a string using Caesar cipher (shift by k)

**Question:** Encrypt a string using Caesar cipher (shift by k).

**Answer:**
```php
function caesarEncrypt($str, $shift) {
    $result = '';
    $shift = $shift % 26; // Handle shifts greater than 26
    
    for ($i = 0; $i < strlen($str); $i++) {
        $char = $str[$i];
        
        if ($char >= 'a' && $char <= 'z') {
            $newChar = chr(((ord($char) - ord('a') + $shift) % 26) + ord('a'));
            $result .= $newChar;
        } elseif ($char >= 'A' && $char <= 'Z') {
            $newChar = chr(((ord($char) - ord('A') + $shift) % 26) + ord('A'));
            $result .= $newChar;
        } else {
            $result .= $char; // Non-alphabetic characters remain unchanged
        }
    }
    
    return $result;
}

function caesarDecrypt($str, $shift) {
    return caesarEncrypt($str, -$shift);
}

// Example
$original = "Hello World";
$encrypted = caesarEncrypt($original, 3);
echo $encrypted; // Output: Khoor Zruog

$decrypted = caesarDecrypt($encrypted, 3);
echo $decrypted; // Output: Hello World
```

**💡 Explanation:** We shift each alphabetic character by the specified amount, wrapping around the alphabet.

### 35. 🌐 URL encode and decode a string manually

**Question:** URL encode and decode a string manually.

**Answer:**
```php
function urlEncodeManual($str) {
    $encoded = '';
    $unreserved = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789-._~';
    
    for ($i = 0; $i < strlen($str); $i++) {
        $char = $str[$i];
        
        if (strpos($unreserved, $char) !== false) {
            $encoded .= $char;
        } else {
            $encoded .= '%' . sprintf('%02X', ord($char));
        }
    }
    
    return $encoded;
}

function urlDecodeManual($str) {
    $decoded = '';
    $i = 0;
    
    while ($i < strlen($str)) {
        if ($str[$i] === '%' && $i + 2 < strlen($str)) {
            $hex = substr($str, $i + 1, 2);
            if (ctype_xdigit($hex)) {
                $decoded .= chr(hexdec($hex));
                $i += 3;
            } else {
                $decoded .= $str[$i];
                $i++;
            }
        } elseif ($str[$i] === '+') {
            $decoded .= ' ';
            $i++;
        } else {
            $decoded .= $str[$i];
            $i++;
        }
    }
    
    return $decoded;
}

// Example
$original = "Hello World!";
$encoded = urlEncodeManual($original);
echo $encoded; // Output: Hello%20World%21

$decoded = urlDecodeManual($encoded);
echo $decoded; // Output: Hello World!
```

**💡 Explanation:** We encode special characters as percent-encoded values and decode them back to their original characters.

## 🌍 Practical Real-World Strings

### 36. 📧 Extract domain from an email address

**Question:** Extract domain from an email address.

**Answer:**
```php
function extractDomain($email) {
    $atPos = -1;
    
    // Find the @ symbol
    for ($i = 0; $i < strlen($email); $i++) {
        if ($email[$i] === '@') {
            $atPos = $i;
            break;
        }
    }
    
    if ($atPos === -1 || $atPos === strlen($email) - 1) {
        return null; // Invalid email
    }
    
    return substr($email, $atPos + 1);
}

// Example
echo extractDomain("user@example.com"); // Output: example.com
echo extractDomain("test@gmail.org"); // Output: gmail.org
```

**💡 Explanation:** We find the @ symbol and return everything after it as the domain.

### 37. 📁 Extract file extension from filename

**Question:** Extract file extension from filename.

**Answer:**
```php
function getFileExtension($filename) {
    $lastDotPos = -1;
    
    // Find the last dot
    for ($i = strlen($filename) - 1; $i >= 0; $i--) {
        if ($filename[$i] === '.') {
            $lastDotPos = $i;
            break;
        }
    }
    
    if ($lastDotPos === -1 || $lastDotPos === strlen($filename) - 1) {
        return ''; // No extension or dot at end
    }
    
    return substr($filename, $lastDotPos + 1);
}

// Example
echo getFileExtension("document.pdf"); // Output: pdf
echo getFileExtension("image.jpeg"); // Output: jpeg
echo getFileExtension("noextension"); // Output: (empty)
```

**💡 Explanation:** We find the last dot in the filename and return everything after it.

### 38. 🔗 Generate a URL slug from a title

**Question:** Generate a URL slug from a title.

**Answer:**
```php
function createSlug($title) {
    // Convert to lowercase
    $slug = strtolower($title);
    
    // Replace spaces and special characters with hyphens
    $result = '';
    for ($i = 0; $i < strlen($slug); $i++) {
        $char = $slug[$i];
        
        if (ctype_alnum($char)) {
            $result .= $char;
        } elseif ($char === ' ' || $char === '-' || $char === '_') {
            if (strlen($result) > 0 && $result[strlen($result) - 1] !== '-') {
                $result .= '-';
            }
        }
        // Skip other special characters
    }
    
    // Remove trailing hyphen
    if (strlen($result) > 0 && $result[strlen($result) - 1] === '-') {
        $result = substr($result, 0, -1);
    }
    
    return $result;
}

// Example
echo createSlug("Hello World - PHP Tutorial!"); // Output: hello-world-php-tutorial
echo createSlug("What's New in PHP 8.0?"); // Output: whats-new-in-php-80
```

**💡 Explanation:** We convert to lowercase, replace spaces/special chars with hyphens, and clean up multiple hyphens.

### 39. ✅ Validate if a string is a valid JSON

**Question:** Validate if a string is a valid JSON.

**Answer:**
```php
function isValidJson($jsonString) {
    // Basic structure validation
    $trimmed = trim($jsonString);
    
    if (strlen($trimmed) === 0) {
        return false;
    }
    
    // Must start with { or [
    if ($trimmed[0] !== '{' && $trimmed[0] !== '[') {
        return false;
    }
    
    // Must end with } or ]
    $lastChar = $trimmed[strlen($trimmed) - 1];
    if ($lastChar !== '}' && $lastChar !== ']') {
        return false;
    }
    
    // Check balanced brackets
    $stack = [];
    $inString = false;
    $escape = false;
    
    for ($i = 0; $i < strlen($trimmed); $i++) {
        $char = $trimmed[$i];
        
        if ($escape) {
            $escape = false;
            continue;
        }
        
        if ($char === '\\') {
            $escape = true;
            continue;
        }
        
        if ($char === '"') {
            $inString = !$inString;
            continue;
        }
        
        if (!$inString) {
            if ($char === '{' || $char === '[') {
                $stack[] = $char;
            } elseif ($char === '}') {
                if (empty($stack) || array_pop($stack) !== '{') {
                    return false;
                }
            } elseif ($char === ']') {
                if (empty($stack) || array_pop($stack) !== '[') {
                    return false;
                }
            }
        }
    }
    
    return empty($stack) && !$inString;
}

// Example
echo isValidJson('{"name": "John", "age": 30}') ? 'Valid JSON' : 'Invalid JSON'; // Output: Valid JSON
echo isValidJson('{"name": "John", "age":}') ? 'Valid JSON' : 'Invalid JSON'; // Output: Invalid JSON
```

**💡 Explanation:** We check basic JSON structure requirements including balanced brackets and proper string handling.

### 40. ✂️ Shorten long strings with ellipsis (hello world → he...ld)

**Question:** Shorten long strings with ellipsis (hello world → he...ld).

**Answer:**
```php
function shortenWithEllipsis($str, $maxLength, $ellipsis = '...') {
    if (strlen($str) <= $maxLength) {
        return $str;
    }
    
    if ($maxLength <= strlen($ellipsis)) {
        return substr($ellipsis, 0, $maxLength);
    }
    
    $availableLength = $maxLength - strlen($ellipsis);
    $halfLength = intval($availableLength / 2);
    
    $start = substr($str, 0, $halfLength);
    $end = substr($str, -($availableLength - $halfLength));
    
    return $start . $ellipsis . $end;
}

// Traditional truncation with ellipsis at end
function truncateWithEllipsis($str, $maxLength, $ellipsis = '...') {
    if (strlen($str) <= $maxLength) {
        return $str;
    }
    
    return substr($str, 0, $maxLength - strlen($ellipsis)) . $ellipsis;
}

// Example
echo shortenWithEllipsis("hello world", 8); // Output: he...ld
echo truncateWithEllipsis("This is a very long sentence", 15); // Output: This is a ve...
```

**💡 Explanation:** We take characters from the beginning and end, connecting them with ellipsis to show truncation.

## 🧠 Logical/String Puzzle Challenges

### 41. 📊 Find the most frequent character in a string

**Question:** Find the most frequent character in a string.

**Answer:**
```php
function mostFrequentChar($str) {
    if (strlen($str) === 0) {
        return null;
    }
    
    $charCount = [];
    
    // Count character frequencies
    for ($i = 0; $i < strlen($str); $i++) {
        $char = $str[$i];
        $charCount[$char] = ($charCount[$char] ?? 0) + 1;
    }
    
    // Find character with maximum frequency
    $maxChar = '';
    $maxCount = 0;
    
    foreach ($charCount as $char => $count) {
        if ($count > $maxCount) {
            $maxCount = $count;
            $maxChar = $char;
        }
    }
    
    return ['char' => $maxChar, 'count' => $maxCount];
}

// Example
$result = mostFrequentChar("programming");
echo "Most frequent: " . $result['char'] . " (" . $result['count'] . " times)";
// Output: Most frequent: m (2 times)
```

**💡 Explanation:** We count character frequencies and track the character with the highest count.

### 42. 🏷️ Remove all HTML tags from a string

**Question:** Remove all HTML tags from a string.

**Answer:**
```php
function removeHtmlTags($html) {
    $result = '';
    $insideTag = false;
    
    for ($i = 0; $i < strlen($html); $i++) {
        $char = $html[$i];
        
        if ($char === '<') {
            $insideTag = true;
        } elseif ($char === '>') {
            $insideTag = false;
        } elseif (!$insideTag) {
            $result .= $char;
        }
    }
    
    return $result;
}

// Example
$html = "<p>Hello <strong>World</strong>!</p><br><div>PHP</div>";
echo removeHtmlTags($html); // Output: Hello World!PHP
```

**💡 Explanation:** We track when we're inside HTML tags and only include characters that are outside of tags.

### 43. ⚖️ Check if string has balanced parentheses

**Question:** Check if string has balanced parentheses.

**Answer:**
```php
function hasBalancedParentheses($str) {
    $stack = 0;
    
    for ($i = 0; $i < strlen($str); $i++) {
        $char = $str[$i];
        
        if ($char === '(') {
            $stack++;
        } elseif ($char === ')') {
            $stack--;
            if ($stack < 0) {
                return false; // More closing than opening
            }
        }
    }
    
    return $stack === 0; // All parentheses matched
}

// Support multiple bracket types
function hasBalancedBrackets($str) {
    $stack = [];
    $pairs = ['(' => ')', '[' => ']', '{' => '}'];
    
    for ($i = 0; $i < strlen($str); $i++) {
        $char = $str[$i];
        
        if (isset($pairs[$char])) {
            $stack[] = $char;
        } elseif (in_array($char, $pairs)) {
            if (empty($stack)) {
                return false;
            }
            $last = array_pop($stack);
            if ($pairs[$last] !== $char) {
                return false;
            }
        }
    }
    
    return empty($stack);
}

// Example
echo hasBalancedParentheses("(hello (world))") ? 'Balanced' : 'Not balanced'; // Output: Balanced
echo hasBalancedBrackets("{[()]}") ? 'Balanced' : 'Not balanced'; // Output: Balanced
```

**💡 Explanation:** We use a counter/stack to track opening and closing brackets and ensure they're properly matched.

### 44. 🔄 Rearrange characters so no two adjacent characters are same

**Question:** Rearrange characters so no two adjacent characters are same.

**Answer:**
```php
function rearrangeString($str) {
    // Count character frequencies
    $charCount = [];
    for ($i = 0; $i < strlen($str); $i++) {
        $char = $str[$i];
        $charCount[$char] = ($charCount[$char] ?? 0) + 1;
    }
    
    // Sort by frequency (descending)
    arsort($charCount);
    
    $chars = array_keys($charCount);
    $counts = array_values($charCount);
    
    // Check if arrangement is possible
    $maxFreq = $counts[0];
    $totalLength = strlen($str);
    
    if ($maxFreq > ($totalLength + 1) / 2) {
        return null; // Not possible to arrange
    }
    
    // Create result array
    $result = array_fill(0, $totalLength, '');
    $index = 0;
    
    // Place characters with highest frequency first at even positions
    foreach ($chars as $i => $char) {
        $count = $counts[$i];
        
        for ($j = 0; $j < $count; $j++) {
            if ($index >= $totalLength) {
                $index = 1; // Switch to odd positions
            }
            $result[$index] = $char;
            $index += 2;
        }
    }
    
    return implode('', $result);
}

// Example
echo rearrangeString("aab"); // Output: aba
echo rearrangeString("aaab"); // Output: null (not possible)
```

**💡 Explanation:** We use a greedy approach, placing the most frequent character first at even positions, then filling odd positions.

### 45. 🔍 Find the longest substring with all unique characters

**Question:** Find the longest substring with all unique characters.

**Answer:**
```php
function longestUniqueSubstring($str) {
    $maxLength = 0;
    $maxSubstring = '';
    $length = strlen($str);
    
    for ($i = 0; $i < $length; $i++) {
        $seen = [];
        $currentSubstring = '';
        
        for ($j = $i; $j < $length; $j++) {
            $char = $str[$j];
            
            if (isset($seen[$char])) {
                break; // Duplicate found
            }
            
            $seen[$char] = true;
            $currentSubstring .= $char;
            
function longestUniqueSubstring($str) {
    $maxLength = 0;
    $maxSubstring = '';
    $length = strlen($str);
    
    for ($i = 0; $i < $length; $i++) {
        $seen = [];
        $currentSubstring = '';
        
        for ($j = $i; $j < $length; $j++) {
            $char = $str[$j];
            
            if (isset($seen[$char])) {
                break; // Duplicate found
            }
            
            $seen[$char] = true;
            $currentSubstring .= $char;
            
            if (strlen($currentSubstring) > $maxLength) {
                $maxLength = strlen($currentSubstring);
                $maxSubstring = $currentSubstring;
            }
        }
    }
    
    return $maxSubstring;
}

// Optimized sliding window approach
function longestUniqueSubstringOptimized($str) {
    $maxLength = 0;
    $maxSubstring = '';
    $left = 0;
    $charIndex = [];
    
    for ($right = 0; $right < strlen($str); $right++) {
        $char = $str[$right];
        
        // If character is already seen and within current window
        if (isset($charIndex[$char]) && $charIndex[$char] >= $left) {
            $left = $charIndex[$char] + 1;
        }
        
        $charIndex[$char] = $right;
        $currentLength = $right - $left + 1;
        
        if ($currentLength > $maxLength) {
            $maxLength = $currentLength;
            $maxSubstring = substr($str, $left, $currentLength);
        }
    }
    
    return $maxSubstring;
}

// Example
echo longestUniqueSubstring("abcabcbb"); // Output: abc
echo longestUniqueSubstring("bbbbb"); // Output: b
echo longestUniqueSubstring("pwwkew"); // Output: wke
```

**💡 Explanation:** We use a sliding window technique to find the longest substring without repeating characters.

## 🎨 Creative & Edge Case Challenges

### 46. ✂️ Split a string into equal parts

**Question:** Split a string into equal parts.

**Answer:**
```php
function splitIntoEqualParts($str, $parts) {
    $length = strlen($str);
    
    if ($length % $parts !== 0) {
        return null; // Cannot split into equal parts
    }
    
    $partSize = $length / $parts;
    $result = [];
    
    for ($i = 0; $i < $parts; $i++) {
        $start = $i * $partSize;
        $result[] = substr($str, $start, $partSize);
    }
    
    return $result;
}

// Split with padding if necessary
function splitIntoEqualPartsWithPadding($str, $parts, $padChar = ' ') {
    $length = strlen($str);
    $partSize = ceil($length / $parts);
    $paddedLength = $partSize * $parts;
    
    // Pad string if necessary
    $paddedStr = str_pad($str, $paddedLength, $padChar);
    
    $result = [];
    for ($i = 0; $i < $parts; $i++) {
        $start = $i * $partSize;
        $result[] = substr($paddedStr, $start, $partSize);
    }
    
    return $result;
}

// Example
print_r(splitIntoEqualParts("abcdefgh", 4)); // Output: ["ab", "cd", "ef", "gh"]
print_r(splitIntoEqualParts("hello", 2)); // Output: null (cannot split equally)
print_r(splitIntoEqualPartsWithPadding("hello", 2)); // Output: ["hel", "lo "]
```

**💡 Explanation:** We divide the string length by the number of parts and extract substrings of equal size.

### 47. 🔄 Reverse words in a sentence, not characters

**Question:** Reverse words in a sentence, not characters.

**Answer:**
```php
function reverseWords($sentence) {
    $words = [];
    $currentWord = '';
    
    // Extract words manually
    for ($i = 0; $i < strlen($sentence); $i++) {
        $char = $sentence[$i];
        
        if ($char === ' ') {
            if ($currentWord !== '') {
                $words[] = $currentWord;
                $currentWord = '';
            }
        } else {
            $currentWord .= $char;
        }
    }
    
    // Add last word
    if ($currentWord !== '') {
        $words[] = $currentWord;
    }
    
    // Reverse the order of words
    $reversed = [];
    for ($i = count($words) - 1; $i >= 0; $i--) {
        $reversed[] = $words[$i];
    }
    
    return implode(' ', $reversed);
}

// Example
echo reverseWords("Hello World PHP"); // Output: PHP World Hello
echo reverseWords("The quick brown fox"); // Output: fox brown quick The
```

**💡 Explanation:** We extract words from the sentence and then reverse their order while keeping the characters within each word intact.

### 48. 📊 Count how many times a word appears in a paragraph

**Question:** Count how many times a word appears in a paragraph.

**Answer:**
```php
function countWordOccurrences($paragraph, $targetWord, $caseSensitive = false) {
    $count = 0;
    $words = [];
    $currentWord = '';
    
    // Convert to lowercase if case insensitive
    if (!$caseSensitive) {
        $paragraph = strtolower($paragraph);
        $targetWord = strtolower($targetWord);
    }
    
    // Extract words
    for ($i = 0; $i < strlen($paragraph); $i++) {
        $char = $paragraph[$i];
        
        if (ctype_alpha($char)) {
            $currentWord .= $char;
        } else {
            if ($currentWord !== '') {
                $words[] = $currentWord;
                $currentWord = '';
            }
        }
    }
    
    // Add last word
    if ($currentWord !== '') {
        $words[] = $currentWord;
    }
    
    // Count occurrences
    foreach ($words as $word) {
        if ($word === $targetWord) {
            $count++;
        }
    }
    
    return $count;
}

// Example
$text = "The cat sat on the mat. The cat was happy.";
echo countWordOccurrences($text, "the", false); // Output: 3
echo countWordOccurrences($text, "cat", true); // Output: 2
```

**💡 Explanation:** We extract all words from the paragraph and count how many match the target word.

### 49. ➕ Insert a string into another at a specific position

**Question:** Insert a string into another at a specific position.

**Answer:**
```php
function insertStringAt($original, $insert, $position) {
    if ($position < 0 || $position > strlen($original)) {
        return $original; // Invalid position
    }
    
    $before = substr($original, 0, $position);
    $after = substr($original, $position);
    
    return $before . $insert . $after;
}

// Manual implementation without substr
function insertStringAtManual($original, $insert, $position) {
    if ($position < 0 || $position > strlen($original)) {
        return $original;
    }
    
    $result = '';
    
    // Add characters before insertion point
    for ($i = 0; $i < $position; $i++) {
        $result .= $original[$i];
    }
    
    // Add insertion string
    $result .= $insert;
    
    // Add remaining characters
    for ($i = $position; $i < strlen($original); $i++) {
        $result .= $original[$i];
    }
    
    return $result;
}

// Example
echo insertStringAt("Hello World", "Beautiful ", 6); // Output: Hello Beautiful World
echo insertStringAt("PHP", " is awesome", 3); // Output: PHP is awesome
```

**💡 Explanation:** We split the original string at the insertion point and concatenate the parts with the inserted string.

### 50. ⌨️ Simulate typing effect: print one char at a time with delay

**Question:** Simulate typing effect: print one char at a time with delay.

**Answer:**
```php
function typeEffect($text, $delayMicroseconds = 100000) {
    for ($i = 0; $i < strlen($text); $i++) {
        echo $text[$i];
        flush(); // Force output to be displayed immediately
        usleep($delayMicroseconds); // Delay in microseconds
    }
    echo "\n"; // New line at the end
}

// With callback for custom output handling
function typeEffectWithCallback($text, $callback, $delayMicroseconds = 100000) {
    $output = '';
    
    for ($i = 0; $i < strlen($text); $i++) {
        $output .= $text[$i];
        $callback($output, $text[$i], $i);
        usleep($delayMicroseconds);
    }
    
    return $output;
}

// Simulate without actual delay (for testing)
function simulateTypeEffect($text) {
    $steps = [];
    $current = '';
    
    for ($i = 0; $i < strlen($text); $i++) {
        $current .= $text[$i];
        $steps[] = $current;
    }
    
    return $steps;
}

// Example usage:
// typeEffect("Hello, World!");

// For testing without delay:
print_r(simulateTypeEffect("Hello!"));
// Output: ["H", "He", "Hel", "Hell", "Hello", "Hello!"]
```

**💡 Explanation:** We output characters one by one with delays, using flush() to ensure immediate display and usleep() for timing.

---

## 🎉 Conclusion

This comprehensive guide covers 50 essential PHP string manipulation questions ranging from basic operations to advanced algorithms and real-world applications. Each solution demonstrates fundamental string processing concepts while avoiding over-reliance on built-in functions.

### 🚀 Key Takeaways:

- **Manual Implementation**: Solutions show core logic without always relying on built-in functions
- **Performance Awareness**: Multiple approaches provided for optimization opportunities
- **Real-World Applications**: Examples reflect common development scenarios
- **Edge Case Handling**: Solutions include proper validation and error handling
- **Security Considerations**: Functions like email validation and input sanitization are covered

### 📚 Categories Covered:

1. **Basic String Handling** - Fundamental operations like reversing, case conversion
2. **Searching & Matching** - Finding patterns, substrings, and character analysis
3. **Modification & Transformation** - Altering strings for different purposes
4. **Advanced Patterns** - Complex algorithms like palindrome detection and compression
5. **Regex Practice** - Pattern matching and validation techniques
6. **Encoding & Conversion** - ASCII, URL encoding, and cipher implementations
7. **Real-World Scenarios** - Practical applications like URL slugs and email parsing
8. **Logic Puzzles** - Algorithm challenges and optimization problems
9. **Creative Challenges** - Unique string manipulation tasks and edge cases

### 🎯 Practice Recommendations:

1. **Implement without looking** at solutions first to test understanding
2. **Optimize for performance** - consider time and space complexity
3. **Add input validation** and comprehensive error handling
4. **Write unit tests** for each function with various test cases
5. **Consider edge cases** like empty strings, special characters, and large inputs
6. **Practice explaining algorithms** for technical interviews
7. **Combine multiple techniques** to solve complex problems
8. **Focus on code readability** and maintainability

### 🔧 Advanced Extensions:

- Implement Unicode support for international characters
- Add multibyte string handling capabilities
- Create performance benchmarks comparing different approaches
- Build comprehensive test suites with edge cases
- Extend functions to handle streams for large text processing

Happy coding with strings! 🎯📝