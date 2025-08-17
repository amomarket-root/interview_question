# 🚀 Laravel Models and Access Modifiers Guide

## 📋 Table of Contents
- [What is Laravel Model?](#-what-is-laravel-model)
- [Access Modifiers in Laravel Models](#-access-modifiers-in-laravel-models)
- [Public Access Modifier](#-public-access-modifier)
- [Private Access Modifier](#-private-access-modifier)
- [Protected Access Modifier](#-protected-access-modifier)
- [Best Practices](#-best-practices)
- [Complete Example](#-complete-example)

---

## 🏗️ What is Laravel Model?

A **Laravel Model** is an Eloquent ORM (Object-Relational Mapping) class that represents a database table and provides an interface for interacting with that table. Models handle database operations, relationships, and business logic.

### 🎯 Key Features of Laravel Models:
- **Database Abstraction**: Simplifies database operations
- **Eloquent ORM**: Provides ActiveRecord implementation
- **Relationships**: Defines connections between tables
- **Mass Assignment Protection**: Secures data input
- **Mutators & Accessors**: Transform data on save/retrieve
- **Query Scopes**: Reusable query constraints

### 📁 Model Location:
```
app/Models/YourModel.php
```

### 🔧 Basic Model Structure:
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    // Model properties and methods
}
```

---

## 🔐 Access Modifiers in Laravel Models

Access modifiers control the visibility and accessibility of class properties and methods. Laravel models use three main access modifiers:

| Modifier | Symbol | Visibility | Inheritance | External Access |
|----------|--------|------------|-------------|-----------------|
| Public | 🌍 | Everywhere | ✅ Yes | ✅ Yes |
| Protected | 🛡️ | Class + Subclasses | ✅ Yes | ❌ No |
| Private | 🔒 | Current Class Only | ❌ No | ❌ No |

---

## 🌍 Public Access Modifier

### 📖 Definition:
Public properties and methods can be accessed from **anywhere** - inside the class, outside the class, and in inherited classes.

### 🎯 Use Cases:
- **API Methods**: Methods that need to be called externally
- **Public Properties**: Configuration that should be accessible
- **Relationships**: Eloquent relationships
- **Scopes**: Query scopes for external use

### 💡 Examples:

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    // 🌍 Public property - accessible everywhere
    public $timestamps = true;
    
    // 🌍 Public method - can be called from controllers, etc.
    public function getFullNameAttribute()
    {
        return $this->first_name . ' ' . $this->last_name;
    }
    
    // 🌍 Public relationship - accessible in queries
    public function posts()
    {
        return $this->hasMany(Post::class);
    }
    
    // 🌍 Public scope - can be used in queries
    public function scopeActive($query)
    {
        return $query->where('status', 'active');
    }
}
```

### 🔄 Usage Example:
```php
$user = new User();
$user->timestamps = false; // ✅ Accessible
$fullName = $user->getFullNameAttribute(); // ✅ Accessible
$posts = $user->posts; // ✅ Accessible
$activeUsers = User::active()->get(); // ✅ Accessible
```

---

## 🔒 Private Access Modifier

### 📖 Definition:
Private properties and methods can **only** be accessed within the **same class**. They are not accessible in child classes or externally.

### 🎯 Use Cases:
- **Internal Logic**: Helper methods used only within the model
- **Sensitive Data**: Properties that shouldn't be exposed
- **Implementation Details**: Internal calculations or validations
- **Security**: Prevent external manipulation

### 💡 Examples:

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    // 🔒 Private property - only accessible within User class
    private $internalCache = [];
    
    // 🔒 Private method - internal helper
    private function calculateAge()
    {
        return now()->diffInYears($this->birth_date);
    }
    
    // 🔒 Private method - internal validation
    private function validateEmailDomain()
    {
        $allowedDomains = ['company.com', 'organization.org'];
        $domain = substr(strrchr($this->email, "@"), 1);
        return in_array($domain, $allowedDomains);
    }
    
    // 🌍 Public method using private methods
    public function getAgeAttribute()
    {
        return $this->calculateAge(); // ✅ Can call private method internally
    }
    
    // 🌍 Public method for email validation
    public function hasValidEmailDomain()
    {
        return $this->validateEmailDomain(); // ✅ Internal call works
    }
}
```

### ❌ Invalid Usage:
```php
$user = new User();
$user->internalCache = []; // ❌ Error: Cannot access private property
$age = $user->calculateAge(); // ❌ Error: Cannot access private method
```

---

## 🛡️ Protected Access Modifier

### 📖 Definition:
Protected properties and methods can be accessed within the **same class** and its **child classes** (inheritance), but not from external code.

### 🎯 Use Cases:
- **Inheritance Support**: Allow child classes to access/override
- **Framework Integration**: Laravel's internal methods
- **Shared Logic**: Common functionality for model hierarchy
- **Template Methods**: Base implementation for child classes

### 💡 Examples:

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    // 🛡️ Protected properties - Laravel conventions
    protected $fillable = ['name', 'email', 'password'];
    protected $hidden = ['password', 'remember_token'];
    protected $casts = ['email_verified_at' => 'datetime'];
    
    // 🛡️ Protected method - can be overridden in child classes
    protected function performInsert(Builder $query)
    {
        // Custom insert logic
        $this->setCreatedAt(now());
        return parent::performInsert($query);
    }
    
    // 🛡️ Protected helper method
    protected function formatName($name)
    {
        return ucwords(strtolower(trim($name)));
    }
    
    // 🌍 Public method using protected method
    public function setNameAttribute($value)
    {
        $this->attributes['name'] = $this->formatName($value);
    }
}

// Child class extending User
class AdminUser extends User
{
    // 🛡️ Can access protected properties and methods
    protected $fillable = ['name', 'email', 'password', 'role'];
    
    // ✅ Can override protected method
    protected function performInsert(Builder $query)
    {
        $this->role = 'admin';
        return parent::performInsert($query);
    }
    
    // ✅ Can use protected method from parent
    public function setNameAttribute($value)
    {
        $this->attributes['name'] = $this->formatName($value) . ' (Admin)';
    }
}
```

### 🔄 Usage Examples:

```php
$user = new User();
$admin = new AdminUser();

// ✅ Public methods work
$user->name = "john doe"; // Uses protected formatName() internally

// ❌ Cannot access protected members externally
$user->fillable = []; // ❌ Error: Cannot access protected property
$user->formatName("test"); // ❌ Error: Cannot access protected method

// ✅ But child class can access them internally
// (happens automatically when AdminUser methods run)
```

---

## ✨ Best Practices

### 🎯 General Guidelines:

1. **🌍 Use Public for:**
   - Eloquent relationships (`hasMany`, `belongsTo`, etc.)
   - Query scopes (`scopeActive`, `scopeRecent`, etc.)
   - Accessors and mutators that need external access
   - API methods for controllers

2. **🛡️ Use Protected for:**
   - Laravel model properties (`$fillable`, `$hidden`, `$casts`)
   - Methods that child classes might need to override
   - Internal helper methods shared with inheritance
   - Framework hook methods

3. **🔒 Use Private for:**
   - Purely internal helper methods
   - Sensitive calculations or validations
   - Implementation details that shouldn't be inherited
   - Cache or temporary data storage

### 📊 Common Laravel Model Properties:

```php
class ExampleModel extends Model
{
    // 🛡️ Protected - Laravel conventions
    protected $table = 'custom_table_name';
    protected $primaryKey = 'custom_id';
    protected $fillable = ['name', 'email'];
    protected $hidden = ['password'];
    protected $casts = ['settings' => 'array'];
    protected $dates = ['deleted_at'];
    
    // 🌍 Public - External access needed
    public $timestamps = true;
    
    // 🔒 Private - Internal use only
    private $internalState = [];
}
```

---

## 🏁 Complete Example

Here's a comprehensive example showing all access modifiers in action:

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;

class Product extends Model
{
    use SoftDeletes;
    
    // 🛡️ PROTECTED - Laravel model configuration
    protected $fillable = ['name', 'price', 'category_id', 'description'];
    protected $hidden = ['created_at', 'updated_at'];
    protected $casts = [
        'price' => 'decimal:2',
        'is_featured' => 'boolean',
        'metadata' => 'array'
    ];
    
    // 🌍 PUBLIC - External access required
    public $timestamps = true;
    
    // 🔒 PRIVATE - Internal calculations only
    private $taxRate = 0.18;
    private $discountCache = [];
    
    // 🔒 PRIVATE - Internal helper methods
    private function calculateTax($amount)
    {
        return $amount * $this->taxRate;
    }
    
    private function getCachedDiscount($code)
    {
        return $this->discountCache[$code] ?? null;
    }
    
    // 🛡️ PROTECTED - Can be overridden by child classes
    protected function applyBusinessRules()
    {
        if ($this->price < 0) {
            $this->price = 0;
        }
    }
    
    protected function formatProductName($name)
    {
        return ucwords(strtolower(trim($name)));
    }
    
    // 🌍 PUBLIC - Relationships (external access)
    public function category()
    {
        return $this->belongsTo(Category::class);
    }
    
    public function reviews()
    {
        return $this->hasMany(Review::class);
    }
    
    // 🌍 PUBLIC - Scopes (external queries)
    public function scopeFeatured($query)
    {
        return $query->where('is_featured', true);
    }
    
    public function scopeInPriceRange($query, $min, $max)
    {
        return $query->whereBetween('price', [$min, $max]);
    }
    
    // 🌍 PUBLIC - Accessors (external access)
    public function getPriceWithTaxAttribute()
    {
        return $this->price + $this->calculateTax($this->price);
    }
    
    public function getFormattedPriceAttribute()
    {
        return '$' . number_format($this->price, 2);
    }
    
    // 🌍 PUBLIC - Mutators (external setting)
    public function setNameAttribute($value)
    {
        $this->attributes['name'] = $this->formatProductName($value);
    }
    
    // 🌍 PUBLIC - Business methods (external calls)
    public function applyDiscount($code, $percentage)
    {
        $cachedDiscount = $this->getCachedDiscount($code);
        
        if ($cachedDiscount) {
            $this->price *= (1 - $cachedDiscount / 100);
        } else {
            $this->price *= (1 - $percentage / 100);
            $this->discountCache[$code] = $percentage;
        }
        
        $this->applyBusinessRules();
        return $this;
    }
    
    // 🛡️ PROTECTED - Model events (can be overridden)
    protected static function boot()
    {
        parent::boot();
        
        static::saving(function ($product) {
            $product->applyBusinessRules();
        });
    }
}

// Example child class
class DigitalProduct extends Product
{
    // ✅ Can access protected members
    protected $fillable = ['name', 'price', 'category_id', 'description', 'download_url'];
    
    // ✅ Can override protected methods
    protected function applyBusinessRules()
    {
        parent::applyBusinessRules();
        
        // Additional rules for digital products
        if (empty($this->download_url)) {
            throw new \Exception('Digital products must have download URL');
        }
    }
}
```

### 🔄 Usage Examples:

```php
// ✅ Valid usage
$product = new Product();
$product->name = "gaming laptop"; // Uses public mutator
$price = $product->price_with_tax; // Uses public accessor
$featured = Product::featured()->get(); // Uses public scope
$category = $product->category; // Uses public relationship

// ❌ Invalid usage
$product->taxRate = 0.20; // ❌ Cannot access private property
$product->calculateTax(100); // ❌ Cannot access private method
$product->fillable = []; // ❌ Cannot access protected property
```

---

## 🎓 Summary

Understanding access modifiers in Laravel models is crucial for:

- **🔒 Security**: Protecting sensitive internal logic
- **🛡️ Maintainability**: Proper encapsulation and inheritance
- **🌍 Usability**: Exposing necessary functionality
- **⚡ Performance**: Efficient code organization

Choose the right access modifier based on who needs access to your properties and methods!