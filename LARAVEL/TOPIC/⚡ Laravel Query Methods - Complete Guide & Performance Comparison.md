# ⚡ Laravel Query Methods - Complete Guide & Performance Comparison

## 📋 Table of Contents
- [Overview](#-overview)
- [Single Record Methods](#-single-record-methods)
- [Multiple Records Methods](#-multiple-records-methods)
- [Collection Methods](#-collection-methods)
- [Performance Comparison](#-performance-comparison)
- [When to Use Which Method](#-when-to-use-which-method)
- [Best Practices](#-best-practices)
- [Common Mistakes](#-common-mistakes)

---

## 📖 Overview

Laravel provides multiple methods to retrieve data from the database. Each method has specific use cases, performance characteristics, and return types.

### 🔍 Method Categories:
- **Single Record**: `find()`, `first()`, `firstOrFail()`, `sole()`
- **Multiple Records**: `get()`, `all()`, `chunk()`, `cursor()`
- **Collection Methods**: `pluck()`, `value()`, `exists()`, `count()`
- **Aggregation**: `sum()`, `avg()`, `max()`, `min()`

---

## 🎯 Single Record Methods

### 1. 🔑 `find()` Method

#### Purpose
Finds a record by its **primary key** (usually `id`). Returns `null` if not found.

#### Syntax & Examples
```php
// Basic usage
$user = User::find(1);                    // Returns User model or null
$user = User::find([1, 2, 3]);           // Returns Collection of models

// With relationships
$user = User::with('posts')->find(1);

// Find or fail
$user = User::findOrFail(1);             // Throws ModelNotFoundException if not found

// Find multiple with specific columns
$users = User::find([1, 2, 3], ['name', 'email']);
```

#### Generated SQL
```sql
-- User::find(1)
SELECT * FROM users WHERE id = 1 LIMIT 1;

-- User::find([1, 2, 3])
SELECT * FROM users WHERE id IN (1, 2, 3);
```

#### Performance
- ⚡ **Fastest** for primary key lookups
- Uses index on primary key
- Direct WHERE clause with LIMIT 1

#### When to Use
```php
// ✅ Good: Finding by ID
$user = User::find($userId);

// ✅ Good: Finding multiple by IDs
$users = User::find([1, 2, 3]);

// ❌ Bad: Don't use for non-primary key searches
$user = User::find($email); // Wrong! Use where() instead
```

---

### 2. 🥇 `first()` Method

#### Purpose
Returns the **first record** that matches the query conditions. Returns `null` if no records found.

#### Syntax & Examples
```php
// Basic usage
$user = User::first();                           // First user in table
$user = User::where('status', 'active')->first(); // First active user
$user = User::orderBy('created_at', 'desc')->first(); // Latest user

// With specific columns
$user = User::first(['name', 'email']);

// With relationships
$user = User::with('posts')->where('age', '>', 18)->first();
```

#### Generated SQL
```sql
-- User::first()
SELECT * FROM users LIMIT 1;

-- User::where('status', 'active')->first()
SELECT * FROM users WHERE status = 'active' LIMIT 1;
```

#### Performance
- 🚀 **Fast** with proper indexing
- Adds LIMIT 1 automatically
- Performance depends on WHERE conditions

#### When to Use
```php
// ✅ Good: Getting first record with conditions
$latestPost = Post::orderBy('created_at', 'desc')->first();

// ✅ Good: Checking if any record exists with conditions
$activeUser = User::where('status', 'active')->first();

// ✅ Good: Getting oldest/newest record
$oldestUser = User::orderBy('created_at')->first();
```

---

### 3. 💥 `firstOrFail()` Method

#### Purpose
Same as `first()` but **throws exception** if no record found.

#### Syntax & Examples
```php
// Basic usage
$user = User::firstOrFail();                          // Throws if no users exist
$user = User::where('email', $email)->firstOrFail(); // Throws if email not found

// In routes (returns 404 if not found)
Route::get('/user/{email}', function ($email) {
    return User::where('email', $email)->firstOrFail();
});
```

#### Exception Handling
```php
try {
    $user = User::where('status', 'premium')->firstOrFail();
} catch (ModelNotFoundException $e) {
    return response()->json(['error' => 'No premium users found'], 404);
}
```

#### When to Use
```php
// ✅ Good: In APIs where you need to return 404
$user = User::where('uuid', $uuid)->firstOrFail();

// ✅ Good: When record MUST exist
$admin = User::where('role', 'admin')->firstOrFail();

// ❌ Avoid: When null is acceptable
$user = User::where('optional_field', $value)->firstOrFail(); // Use first() instead
```

---

### 4. 🎯 `sole()` Method

#### Purpose
Ensures **exactly one** record matches. Throws exception if 0 or more than 1 record found.

#### Syntax & Examples
```php
// Basic usage
$user = User::where('email', $email)->sole(); // Throws if 0 or >1 records

// Use case: Unique constraints
$setting = Setting::where('key', 'app_name')->sole();
```

#### Exceptions Thrown
```php
// NoRecordsFoundException - if 0 records
// MultipleRecordsFoundException - if >1 records
try {
    $user = User::where('email', $email)->sole();
} catch (NoRecordsFoundException $e) {
    // Handle no records
} catch (MultipleRecordsFoundException $e) {
    // Handle multiple records (data integrity issue)
}
```

#### When to Use
```php
// ✅ Good: Unique settings or configurations
$config = Config::where('environment', 'production')->sole();

// ✅ Good: Ensuring data integrity
$primaryAddress = Address::where('user_id', $userId)
                         ->where('is_primary', true)
                         ->sole();

// ❌ Avoid: When multiple records are expected
$posts = Post::where('status', 'published')->sole(); // Wrong! Use get()
```

---

## 📊 Multiple Records Methods

### 1. 🗂️ `get()` Method

#### Purpose
Retrieves **all records** that match the query conditions as a Collection.

#### Syntax & Examples
```php
// Basic usage
$users = User::get();                              // All users
$users = User::where('status', 'active')->get();  // All active users

// With specific columns
$users = User::get(['name', 'email']);

// With relationships
$users = User::with('posts')->get();

// With ordering and limiting
$users = User::orderBy('name')->limit(10)->get();
```

#### Generated SQL
```sql
-- User::get()
SELECT * FROM users;

-- User::where('status', 'active')->get()
SELECT * FROM users WHERE status = 'active';
```

#### Performance
- 🐌 **Slow** for large datasets
- Loads all records into memory
- Good for small to medium datasets

#### When to Use
```php
// ✅ Good: Small datasets
$categories = Category::get(); // Usually few categories

// ✅ Good: With pagination
$users = User::paginate(15);

// ✅ Good: With limits
$recentPosts = Post::orderBy('created_at', 'desc')->limit(5)->get();

// ❌ Avoid: Large datasets without limits
$users = User::get(); // Could be millions of records!
```

---

### 2. 🌍 `all()` Method

#### Purpose
Retrieves **all records** from the table. No query conditions can be applied.

#### Syntax & Examples
```php
// Basic usage (static method)
$users = User::all();                    // All users
$users = User::all(['name', 'email']);  // All users with specific columns

// Cannot chain query methods
// User::where('status', 'active')->all(); // ❌ Error!
```

#### Generated SQL
```sql
-- User::all()
SELECT * FROM users;
```

#### Performance
- 🐌 **Slowest** for large tables
- Always retrieves ALL records
- No optimization possible

#### When to Use
```php
// ✅ Good: Small lookup tables
$roles = Role::all(); // Usually few roles
$permissions = Permission::all();

// ✅ Good: Cached data
$categories = Cache::remember('categories', 3600, function () {
    return Category::all();
});

// ❌ Avoid: Large tables
$users = User::all(); // Could crash server!
$orders = Order::all(); // Potentially millions of records
```

---

### 3. 🔄 `chunk()` Method

#### Purpose
Processes large datasets in **small chunks** to avoid memory issues.

#### Syntax & Examples
```php
// Basic usage
User::chunk(100, function ($users) {
    foreach ($users as $user) {
        // Process each user
        $user->update(['processed' => true]);
    }
});

// With conditions
User::where('status', 'inactive')
    ->chunk(50, function ($users) {
        // Send notification to inactive users
        Mail::to($users->pluck('email'))->send(new ReactivationEmail());
    });

// Return false to stop processing
User::chunk(100, function ($users) {
    foreach ($users as $user) {
        if ($user->email === 'stop@example.com') {
            return false; // Stop chunking
        }
        // Process user
    }
});
```

#### Performance
- ⚡ **Excellent** for large datasets
- Constant memory usage
- Database-friendly

#### When to Use
```php
// ✅ Good: Large data processing
User::chunk(1000, function ($users) {
    foreach ($users as $user) {
        // Send welcome email
    }
});

// ✅ Good: Data migration
Post::chunk(500, function ($posts) {
    foreach ($posts as $post) {
        $post->update(['migrated' => true]);
    }
});

// ✅ Good: Bulk operations
Order::where('status', 'pending')
     ->chunk(100, function ($orders) {
         // Process pending orders
     });
```

---

### 4. 🎭 `cursor()` Method

#### Purpose
Returns a **lazy collection** that queries the database one record at a time.

#### Syntax & Examples
```php
// Basic usage
foreach (User::cursor() as $user) {
    // Process one user at a time
    echo $user->name;
}

// With conditions
foreach (User::where('status', 'active')->cursor() as $user) {
    // Process active users one by one
    $user->sendNotification();
}

// Convert to regular collection (defeats the purpose)
$users = User::cursor()->toArray(); // ❌ Don't do this!
```

#### Performance
- 🎯 **Memory efficient** for large datasets
- One query, one record at a time
- Slower than chunk() for bulk operations

#### When to Use
```php
// ✅ Good: Memory-constrained environments
foreach (User::cursor() as $user) {
    // Process millions of users without memory issues
}

// ✅ Good: One-time data processing
foreach (Log::where('created_at', '<', now()->subMonth())->cursor() as $log) {
    // Archive old logs
    $log->archive();
}

// ❌ Avoid: When you need bulk operations
foreach (User::cursor() as $user) {
    User::where('id', $user->id)->update(['processed' => true]); // Inefficient!
}
```

---

## 🧮 Collection Methods

### 1. 🎯 `pluck()` Method

#### Purpose
Retrieves **single column values** as a Collection.

#### Syntax & Examples
```php
// Single column
$names = User::pluck('name');                    // Collection of names
$emails = User::where('status', 'active')->pluck('email');

// Key-value pairs
$users = User::pluck('name', 'id');             // ['1' => 'John', '2' => 'Jane']
$emails = User::pluck('email', 'name');         // ['John' => 'john@email.com']

// From relationships
$postTitles = User::find(1)->posts()->pluck('title');
```

#### Generated SQL
```sql
-- User::pluck('name')
SELECT name FROM users;

-- User::pluck('name', 'id')  
SELECT name, id FROM users;
```

#### Performance
- ⚡ **Very fast** - only selects needed columns
- Minimal memory usage
- Perfect for dropdowns and select lists

#### When to Use
```php
// ✅ Good: Form dropdowns
$categories = Category::pluck('name', 'id');
// Result: ['1' => 'Technology', '2' => 'Sports']

// ✅ Good: Getting IDs for bulk operations
$userIds = User::where('status', 'inactive')->pluck('id');
User::whereIn('id', $userIds)->delete();

// ✅ Good: Validation lists
$existingEmails = User::pluck('email')->toArray();
```

---

### 2. 💎 `value()` Method

#### Purpose
Retrieves a **single column value** from the first record.

#### Syntax & Examples
```php
// Basic usage
$name = User::where('id', 1)->value('name');           // Returns string or null
$email = User::orderBy('created_at', 'desc')->value('email'); // Latest user's email

// Equivalent to
$name = User::where('id', 1)->first()->name ?? null;   // More verbose
```

#### Generated SQL
```sql
-- User::where('id', 1)->value('name')
SELECT name FROM users WHERE id = 1 LIMIT 1;
```

#### Performance
- ⚡ **Fastest** for single values
- No model instantiation
- Direct value return

#### When to Use
```php
// ✅ Good: Getting single values
$userCount = User::where('status', 'active')->count();
$latestPostTitle = Post::orderBy('created_at', 'desc')->value('title');

// ✅ Good: Quick existence checks
$username = User::where('email', $email)->value('username');
if ($username) {
    // User exists
}

// ❌ Avoid: When you need the full model
$user = User::where('id', 1)->value('name'); // Only gets name, not full user
```

---

### 3. ✅ `exists()` Method

#### Purpose
Checks if **any records exist** matching the query. Returns boolean.

#### Syntax & Examples
```php
// Basic usage
$hasUsers = User::exists();                           // true/false
$hasActiveUsers = User::where('status', 'active')->exists();

// With complex conditions
$hasRecentPosts = Post::where('created_at', '>', now()->subDay())->exists();

// In conditional logic
if (User::where('email', $email)->exists()) {
    throw new Exception('Email already taken');
}
```

#### Generated SQL
```sql
-- User::where('status', 'active')->exists()
SELECT EXISTS(SELECT 1 FROM users WHERE status = 'active' LIMIT 1);
```

#### Performance
- ⚡ **Fastest** for existence checks
- Uses SQL EXISTS clause
- Stops at first match

#### When to Use
```php
// ✅ Good: Validation
if (User::where('email', $email)->exists()) {
    return 'Email already taken';
}

// ✅ Good: Conditional logic
if (Post::where('user_id', $userId)->exists()) {
    // User has posts
}

// ✅ Good: Authorization checks
if (!Permission::where('user_id', $userId)->where('action', 'delete')->exists()) {
    abort(403);
}

// ❌ Avoid: When you need the record
if (User::where('id', $id)->exists()) {
    $user = User::find($id); // Two queries instead of one!
}
```

---

### 4. 🔢 `count()` Method

#### Purpose
Returns the **number of records** matching the query.

#### Syntax & Examples
```php
// Basic usage
$totalUsers = User::count();                          // Total users
$activeUsers = User::where('status', 'active')->count();

// With grouping
$postsByUser = Post::groupBy('user_id')->count();

// With relationships
$usersWithPosts = User::has('posts')->count();
```

#### Generated SQL
```sql
-- User::count()
SELECT COUNT(*) FROM users;

-- User::where('status', 'active')->count()
SELECT COUNT(*) FROM users WHERE status = 'active';
```

#### Performance
- ⚡ **Very fast** with proper indexing
- Database-level counting
- No data transfer

#### When to Use
```php
// ✅ Good: Statistics and dashboards
$stats = [
    'total_users' => User::count(),
    'active_users' => User::where('status', 'active')->count(),
    'posts_today' => Post::whereDate('created_at', today())->count()
];

// ✅ Good: Pagination info
$total = Post::count();
$perPage = 15;
$totalPages = ceil($total / $perPage);

// ✅ Good: Conditional logic based on count
if (User::where('role', 'admin')->count() === 0) {
    // Create default admin
}
```

---

## ⚡ Performance Comparison

### 🏆 Speed Ranking (Fastest to Slowest)

| Rank | Method | Use Case | Performance | Memory Usage |
|------|--------|----------|-------------|--------------|
| 1 | `find()` | Single record by ID | ⚡⚡⚡⚡⚡ | Very Low |
| 2 | `value()` | Single column value | ⚡⚡⚡⚡⚡ | Very Low |
| 3 | `exists()` | Check existence | ⚡⚡⚡⚡⚡ | Very Low |
| 4 | `count()` | Count records | ⚡⚡⚡⚡⚡ | Very Low |
| 5 | `pluck()` | Column values | ⚡⚡⚡⚡ | Low |
| 6 | `first()` | First record | ⚡⚡⚡⚡ | Low |
| 7 | `sole()` | Exactly one record | ⚡⚡⚡ | Low |
| 8 | `firstOrFail()` | First or exception | ⚡⚡⚡ | Low |
| 9 | `chunk()` | Large datasets | ⚡⚡⚡ | Low (constant) |
| 10 | `cursor()` | Lazy loading | ⚡⚡ | Very Low |
| 11 | `get()` | Multiple records | ⚡⚡ | High |
| 12 | `all()` | All records | ⚡ | Very High |

### 📊 Detailed Performance Analysis

#### Primary Key Lookups
```php
// 🏆 Fastest - Uses primary key index
$user = User::find(1);                     // ~0.001ms

// 🐌 Slower - Full table scan possible  
$user = User::where('id', 1)->first();     // ~0.005ms

// 🐌 Slowest - No index utilization
$user = User::where('name', 'John')->first(); // ~50ms (large table)
```

#### Existence Checks
```php
// 🏆 Fastest - SQL EXISTS
$exists = User::where('email', $email)->exists();     // ~0.002ms

// 🐌 Slower - Loads full record
$exists = User::where('email', $email)->first() !== null; // ~0.010ms

// 🐌 Slowest - Counts all records  
$exists = User::where('email', $email)->count() > 0;  // ~0.015ms
```

#### Large Dataset Processing
```php
// 🏆 Best for memory - Constant memory usage
User::chunk(1000, function ($users) { /* process */ }); // 50MB RAM

// 🐌 Memory hungry - Loads all at once
$users = User::get();                                    // 2GB+ RAM
foreach ($users as $user) { /* process */ }

// 🐌 Terrible - Loads everything
$users = User::all();                                    // 3GB+ RAM
```

---

## 🎯 When to Use Which Method

### 🔍 Single Record Scenarios

#### Finding by Primary Key
```php
// ✅ Best choice
$user = User::find($id);

// ❌ Unnecessary overhead
$user = User::where('id', $id)->first();
```

#### Finding with Conditions
```php
// ✅ Good for optional results
$user = User::where('email', $email)->first();

// ✅ Good for required results
$user = User::where('email', $email)->firstOrFail();

// ✅ Good for unique constraints
$setting = Setting::where('key', 'app_name')->sole();
```

### 📊 Multiple Records Scenarios

#### Small Datasets
```php
// ✅ Good - Small lookup tables
$categories = Category::get();
$roles = Role::all();

// ✅ Good - With pagination
$users = User::paginate(15);
```

#### Large Datasets
```php
// ✅ Best - Memory efficient
User::chunk(1000, function ($users) {
    foreach ($users as $user) {
        // Process user
    }
});

// ✅ Alternative - Lazy loading
foreach (User::cursor() as $user) {
    // Process user
}

// ❌ Avoid - Memory killer
$users = User::get(); // Could be millions!
```

### 🎯 Specific Value Scenarios

#### Getting Single Values
```php
// ✅ Best - Direct value
$name = User::where('id', $id)->value('name');

// ❌ Inefficient - Full model load
$name = User::where('id', $id)->first()->name;
```

#### Getting Multiple Values
```php
// ✅ Best - Only needed columns
$emails = User::where('status', 'active')->pluck('email');

// ❌ Inefficient - Full models
$emails = User::where('status', 'active')->get()->pluck('email');
```

#### Existence Checks
```php
// ✅ Best - Optimized query
if (User::where('email', $email)->exists()) {
    // Handle duplicate
}

// ❌ Inefficient - Loads record
if (User::where('email', $email)->first()) {
    // Handle duplicate  
}
```

---

## ✅ Best Practices

### 1. 🎯 Choose the Right Method

#### For Single Records
```php
// Primary key lookup
$user = User::find($id);                              // ✅ Best

// With conditions (optional result)
$user = User::where('status', 'active')->first();    // ✅ Good

// With conditions (required result)  
$user = User::where('email', $email)->firstOrFail(); // ✅ Good

// Unique constraint validation
$user = User::where('email', $email)->sole();        // ✅ Best for uniqueness
```

#### For Multiple Records
```php
// Small datasets
$categories = Category::get();                        // ✅ Good

// Large datasets  
User::chunk(1000, function ($users) { });           // ✅ Best

// Specific columns only
$emails = User::pluck('email');                      // ✅ Efficient
```

### 2. 📈 Optimize with Indexes

#### Create Proper Indexes
```php
// Migration
Schema::table('users', function (Blueprint $table) {
    $table->index('email');        // For email lookups
    $table->index('status');       // For status filtering
    $table->index(['status', 'created_at']); // Compound index
});
```

#### Use Indexed Columns in Queries
```php
// ✅ Good - Uses index
$users = User::where('status', 'active')->get();

// ❌ Avoid - No index on 'bio'
$users = User::where('bio', 'like', '%developer%')->get();
```

### 3. 🚀 Use Eager Loading

#### Prevent N+1 Problems
```php
// ❌ N+1 Problem
$users = User::get();
foreach ($users as $user) {
    echo $user->posts->count(); // Query for each user!
}

// ✅ Eager Loading
$users = User::with('posts')->get();
foreach ($users as $user) {
    echo $user->posts->count(); // No additional queries
}
```

### 4. 🎭 Select Only Needed Columns

```php
// ❌ Selects all columns
$users = User::get();

// ✅ Selects only needed columns
$users = User::get(['id', 'name', 'email']);

// ✅ Even better for specific values
$names = User::pluck('name');
```

### 5. 🔄 Use Caching for Repeated Queries

```php
// ✅ Cache expensive queries
$categories = Cache::remember('categories', 3600, function () {
    return Category::with('posts')->get();
});

// ✅ Cache counts
$userCount = Cache::remember('user_count', 1800, function () {
    return User::count();
});
```

---

## ❌ Common Mistakes

### 1. 🚫 Wrong Method for Use Case

```php
// ❌ Using get() for single record
$user = User::where('id', $id)->get()->first();

// ✅ Use find() or first()
$user = User::find($id);
$user = User::where('id', $id)->first();
```

### 2. 🚫 Loading Unnecessary Data

```php
// ❌ Loading full models for simple checks
if (User::where('email', $email)->first()) {
    // Only checking existence
}

// ✅ Use exists() method
if (User::where('email', $email)->exists()) {
    // Much faster
}
```

### 3. 🚫 N+1 Query Problems

```php
// ❌ N+1 queries
$posts = Post::get();
foreach ($posts as $post) {
    echo $post->user->name; // Query for each post!
}

// ✅ Eager loading
$posts = Post::with('user')->get();
foreach ($posts as $post) {
    echo $post->user->name; // Single query
}
```

### 4. 🚫 Memory Issues with Large Datasets

```php
// ❌ Memory exhaustion
$users = User::get(); // Could be millions!
foreach ($users as $user) {
    // Process user
}

// ✅ Use chunking
User::chunk(1000, function ($users) {
    foreach ($users as $user) {
        // Process user
    }
});
```

### 5. 🚫 Inefficient Counting

```php
// ❌ Loading all records to count
$count = User::get()->count();

// ✅ Database-level counting
$count = User::count();
```

---

## 🎯 Quick Reference Cheat Sheet

### Single Record
```php
User::find($id)                    // By primary key
User::first()                      // First record
User::firstOrFail()               // First or 404
User::sole()                      // Exactly one record
```

### Multiple Records
```php
User::get()                       // All matching records
User::all()                       // All records (no conditions)
User::chunk(100, $callback)       // Process in chunks
User::cursor()                    // Lazy collection
```

### Values & Checks
```php
User::pluck('email')              // Column values
User::value('name')               // Single value
User::exists()                    // Boolean check
User::count()                     // Record count
```

### Aggregations
```php
Order::sum('amount')              // Sum of column
Product::avg('price')             // Average value
User::max('age')                  // Maximum value
User::min('created_at')           // Minimum value
```

---

*Last Updated: August 2025*
*Laravel Version: 11.x*