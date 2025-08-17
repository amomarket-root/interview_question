# 🔥 Laravel Advanced Eloquent Guide

## 📋 Table of Contents
- [What is Eloquent ORM?](#-what-is-eloquent-orm)
- [Attribute Casting](#-attribute-casting)
- [Mutators & Accessors](#-mutators--accessors)
- [Query Scopes](#-query-scopes)
- [Advanced Relationships](#-advanced-relationships)
- [Polymorphic Relations](#-polymorphic-relations)
- [Many-to-Many Relationships](#-many-to-many-relationships)
- [Eager Loading & Performance](#-eager-loading--performance)
- [Custom Collections](#-custom-collections)
- [Model Events](#-model-events)
- [Advanced Query Techniques](#-advanced-query-techniques)
- [Database Transactions](#-database-transactions)
- [Model Serialization](#-model-serialization)
- [Testing Eloquent Models](#-testing-eloquent-models)
- [Performance Optimization](#-performance-optimization)
- [Common Issues & Solutions](#-common-issues--solutions)
- [Best Practices](#-best-practices)

## 🔍 What is Eloquent ORM?

Eloquent ORM is Laravel's built-in Object-Relational Mapping (ORM) that provides a beautiful, simple ActiveRecord implementation for working with your database. Each database table has a corresponding "Model" which is used to interact with that table.

**Key Features:**
- **🎯 ActiveRecord Pattern**: Models represent database records
- **🔗 Relationship Management**: Easy definition of table relationships
- **⚡ Query Builder Integration**: Fluent query interface
- **🛡️ Mass Assignment Protection**: Secure data handling
- **🔄 Automatic Timestamps**: Created/updated timestamp management
- **📊 Soft Deletes**: Mark records as deleted without removing them

## 🎭 Attribute Casting

Attribute casting allows you to convert database values to common data types automatically.

### Built-in Cast Types

#### 📁 app/Models/User.php
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Casts\Attribute;
use Carbon\Carbon;

class User extends Model
{
    protected $fillable = [
        'name', 'email', 'password', 'preferences', 'metadata', 
        'birth_date', 'is_active', 'balance', 'tags'
    ];

    /**
     * Basic casting examples
     */
    protected $casts = [
        // String casting
        'email_verified_at' => 'datetime',
        'birth_date' => 'date',
        
        // Boolean casting
        'is_active' => 'boolean',
        'is_admin' => 'bool',
        
        // Numeric casting
        'balance' => 'decimal:2',
        'score' => 'integer',
        'rating' => 'float',
        'priority' => 'real',
        
        // Array and Object casting
        'preferences' => 'array',
        'metadata' => 'object',
        'tags' => 'json',
        
        // Collection casting
        'permissions' => 'collection',
        
        // Custom casting
        'encrypted_data' => 'encrypted',
        'hashed_password' => 'hashed',
    ];

    /**
     * Custom attribute casting using Attribute class (Laravel 9+)
     */
    protected function firstName(): Attribute
    {
        return Attribute::make(
            get: fn ($value) => ucfirst($value),
            set: fn ($value) => strtolower($value),
        );
    }

    protected function fullName(): Attribute
    {
        return Attribute::make(
            get: fn ($value, $attributes) => $attributes['first_name'] . ' ' . $attributes['last_name'],
        );
    }

    protected function birthDate(): Attribute
    {
        return Attribute::make(
            get: fn ($value) => $value ? Carbon::parse($value) : null,
            set: fn ($value) => $value ? Carbon::parse($value)->format('Y-m-d') : null,
        );
    }
}
```

### Custom Cast Classes

#### 📁 app/Casts/MoneyCast.php
```php
<?php

namespace App\Casts;

use Illuminate\Contracts\Database\Eloquent\CastsAttributes;
use InvalidArgumentException;

class MoneyCast implements CastsAttributes
{
    /**
     * Cast the given value for storage.
     */
    public function get($model, string $key, $value, array $attributes): ?array
    {
        if ($value === null) {
            return null;
        }

        // Convert cents to dollars
        return [
            'amount' => $value / 100,
            'formatted' => '$' . number_format($value / 100, 2),
            'cents' => $value,
        ];
    }

    /**
     * Prepare the given value for storage.
     */
    public function set($model, string $key, $value, array $attributes): ?int
    {
        if ($value === null) {
            return null;
        }

        // Convert different input formats to cents
        if (is_array($value)) {
            return isset($value['cents']) ? $value['cents'] : ($value['amount'] * 100);
        }

        if (is_string($value)) {
            // Remove currency symbols and convert to float
            $cleaned = preg_replace('/[^\d.-]/', '', $value);
            return (int) (floatval($cleaned) * 100);
        }

        if (is_numeric($value)) {
            return (int) ($value * 100);
        }

        throw new InvalidArgumentException('Invalid money value');
    }
}
```

#### 📁 app/Casts/AddressCast.php
```php
<?php

namespace App\Casts;

use App\ValueObjects\Address;
use Illuminate\Contracts\Database\Eloquent\CastsAttributes;

class AddressCast implements CastsAttributes
{
    public function get($model, string $key, $value, array $attributes): ?Address
    {
        if (!$value) {
            return null;
        }

        $data = json_decode($value, true);
        
        return new Address(
            street: $data['street'] ?? '',
            city: $data['city'] ?? '',
            state: $data['state'] ?? '',
            zipCode: $data['zip_code'] ?? '',
            country: $data['country'] ?? '',
        );
    }

    public function set($model, string $key, $value, array $attributes): ?string
    {
        if (!$value instanceof Address) {
            return null;
        }

        return json_encode([
            'street' => $value->street,
            'city' => $value->city,
            'state' => $value->state,
            'zip_code' => $value->zipCode,
            'country' => $value->country,
        ]);
    }
}
```

#### 📁 app/ValueObjects/Address.php
```php
<?php

namespace App\ValueObjects;

class Address
{
    public function __construct(
        public string $street,
        public string $city,
        public string $state,
        public string $zipCode,
        public string $country
    ) {}

    public function getFullAddress(): string
    {
        return "{$this->street}, {$this->city}, {$this->state} {$this->zipCode}, {$this->country}";
    }

    public function isInternational(): bool
    {
        return strtoupper($this->country) !== 'US';
    }
}
```

#### Using Custom Casts
```php
<?php

namespace App\Models;

use App\Casts\MoneyCast;
use App\Casts\AddressCast;
use Illuminate\Database\Eloquent\Model;

class Order extends Model
{
    protected $fillable = ['total', 'shipping_address', 'billing_address'];

    protected $casts = [
        'total' => MoneyCast::class,
        'shipping_address' => AddressCast::class,
        'billing_address' => AddressCast::class,
    ];
}

// Usage example
$order = Order::create([
    'total' => 29.99, // Stored as 2999 cents
    'shipping_address' => new Address(
        '123 Main St',
        'Anytown',
        'CA',
        '90210',
        'US'
    )
]);

echo $order->total['formatted']; // $29.99
echo $order->shipping_address->getFullAddress(); // 123 Main St, Anytown, CA 90210, US
```

## 🔄 Mutators & Accessors

Mutators and accessors allow you to format Eloquent attribute values when you retrieve or set them on model instances.

### Traditional Mutators & Accessors (Laravel 8 and below)

#### 📁 app/Models/User.php
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Support\Str;

class User extends Model
{
    /**
     * ACCESSORS - Format attributes when retrieving
     */

    // Get attribute format: get{AttributeName}Attribute
    public function getFirstNameAttribute($value)
    {
        return ucfirst($value);
    }

    public function getLastNameAttribute($value)
    {
        return ucfirst($value);
    }

    // Virtual attribute (doesn't exist in database)
    public function getFullNameAttribute()
    {
        return $this->first_name . ' ' . $this->last_name;
    }

    public function getAvatarUrlAttribute()
    {
        if ($this->avatar) {
            return asset('storage/avatars/' . $this->avatar);
        }
        return asset('images/default-avatar.png');
    }

    public function getInitialsAttribute()
    {
        $names = explode(' ', $this->full_name);
        $initials = '';
        foreach ($names as $name) {
            $initials .= strtoupper(substr($name, 0, 1));
        }
        return $initials;
    }

    /**
     * MUTATORS - Format attributes when storing
     */

    // Set attribute format: set{AttributeName}Attribute
    public function setFirstNameAttribute($value)
    {
        $this->attributes['first_name'] = strtolower($value);
    }

    public function setLastNameAttribute($value)
    {
        $this->attributes['last_name'] = strtolower($value);
    }

    public function setEmailAttribute($value)
    {
        $this->attributes['email'] = strtolower(trim($value));
    }

    public function setPasswordAttribute($value)
    {
        $this->attributes['password'] = bcrypt($value);
    }

    public function setPhoneAttribute($value)
    {
        // Remove all non-numeric characters
        $this->attributes['phone'] = preg_replace('/[^0-9]/', '', $value);
    }
}
```

### Modern Attribute Methods (Laravel 9+)

#### 📁 app/Models/Product.php
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Casts\Attribute;
use Illuminate\Support\Str;
use Carbon\Carbon;

class Product extends Model
{
    protected $fillable = [
        'name', 'description', 'price', 'sku', 'status', 
        'published_at', 'metadata', 'tags'
    ];

    /**
     * Modern attribute accessors and mutators
     */
    protected function name(): Attribute
    {
        return Attribute::make(
            get: fn ($value) => ucwords($value),
            set: fn ($value) => strtolower($value),
        );
    }

    protected function price(): Attribute
    {
        return Attribute::make(
            get: fn ($value) => $value / 100, // Convert cents to dollars
            set: fn ($value) => $value * 100, // Convert dollars to cents
        );
    }

    protected function sku(): Attribute
    {
        return Attribute::make(
            get: fn ($value) => strtoupper($value),
            set: fn ($value) => strtoupper(trim($value)),
        );
    }

    protected function slug(): Attribute
    {
        return Attribute::make(
            get: fn ($value, $attributes) => $value ?: Str::slug($attributes['name']),
        );
    }

    protected function formattedPrice(): Attribute
    {
        return Attribute::make(
            get: fn ($value, $attributes) => '$' . number_format($attributes['price'] / 100, 2),
        );
    }

    protected function description(): Attribute
    {
        return Attribute::make(
            get: fn ($value) => $value ? strip_tags($value) : null,
            set: function ($value) {
                // Auto-generate excerpt if description is long
                if (strlen($value) > 500) {
                    $this->attributes['excerpt'] = Str::limit(strip_tags($value), 150);
                }
                return $value;
            },
        );
    }

    protected function publishedAt(): Attribute
    {
        return Attribute::make(
            get: fn ($value) => $value ? Carbon::parse($value) : null,
            set: fn ($value) => $value instanceof Carbon ? $value : Carbon::parse($value),
        );
    }

    protected function status(): Attribute
    {
        return Attribute::make(
            get: fn ($value) => ucfirst($value),
            set: fn ($value) => strtolower($value),
        );
    }

    protected function isPublished(): Attribute
    {
        return Attribute::make(
            get: fn ($value, $attributes) => 
                $attributes['status'] === 'published' && 
                isset($attributes['published_at']) &&
                Carbon::parse($attributes['published_at'])->isPast(),
        );
    }

    protected function tags(): Attribute
    {
        return Attribute::make(
            get: fn ($value) => $value ? explode(',', $value) : [],
            set: fn ($value) => is_array($value) ? implode(',', $value) : $value,
        );
    }
}
```

### Advanced Mutator Examples

#### 📁 app/Models/BlogPost.php
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Casts\Attribute;
use Illuminate\Support\Str;
use Carbon\Carbon;

class BlogPost extends Model
{
    protected $fillable = [
        'title', 'content', 'excerpt', 'slug', 'meta_description',
        'reading_time', 'word_count', 'status', 'published_at'
    ];

    /**
     * Auto-generate slug from title
     */
    protected function slug(): Attribute
    {
        return Attribute::make(
            set: function ($value, $attributes) {
                if (empty($value) && isset($attributes['title'])) {
                    $baseSlug = Str::slug($attributes['title']);
                    $slug = $baseSlug;
                    $counter = 1;
                    
                    // Ensure unique slug
                    while (static::where('slug', $slug)->where('id', '!=', $this->id ?? 0)->exists()) {
                        $slug = $baseSlug . '-' . $counter;
                        $counter++;
                    }
                    
                    return $slug;
                }
                return $value ? Str::slug($value) : null;
            }
        );
    }

    /**
     * Auto-generate excerpt from content
     */
    protected function excerpt(): Attribute
    {
        return Attribute::make(
            get: fn ($value, $attributes) => $value ?: $this->generateExcerpt($attributes['content'] ?? ''),
            set: fn ($value) => $value ?: $this->generateExcerpt($this->content ?? ''),
        );
    }

    /**
     * Calculate reading time based on content
     */
    protected function readingTime(): Attribute
    {
        return Attribute::make(
            get: function ($value, $attributes) {
                if ($value) return $value;
                
                $content = $attributes['content'] ?? '';
                $wordCount = str_word_count(strip_tags($content));
                return max(1, ceil($wordCount / 200)); // 200 words per minute
            }
        );
    }

    /**
     * Calculate word count
     */
    protected function wordCount(): Attribute
    {
        return Attribute::make(
            get: function ($value, $attributes) {
                if ($value) return $value;
                
                return str_word_count(strip_tags($attributes['content'] ?? ''));
            }
        );
    }

    /**
     * Auto-generate meta description
     */
    protected function metaDescription(): Attribute
    {
        return Attribute::make(
            get: function ($value, $attributes) {
                if ($value) return $value;
                
                $content = strip_tags($attributes['content'] ?? '');
                return Str::limit($content, 155);
            }
        );
    }

    private function generateExcerpt(string $content): string
    {
        $plainText = strip_tags($content);
        return Str::limit($plainText, 200);
    }
}
```

## 🎯 Query Scopes

Query scopes allow you to define common sets of constraints that you can easily re-use throughout your application.

### Local Scopes

#### 📁 app/Models/User.php
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Builder;
use Carbon\Carbon;

class User extends Model
{
    /**
     * Basic local scopes
     */
    public function scopeActive(Builder $query): Builder
    {
        return $query->where('is_active', true);
    }

    public function scopeInactive(Builder $query): Builder
    {
        return $query->where('is_active', false);
    }

    public function scopeVerified(Builder $query): Builder
    {
        return $query->whereNotNull('email_verified_at');
    }

    public function scopeUnverified(Builder $query): Builder
    {
        return $query->whereNull('email_verified_at');
    }

    /**
     * Parameterized scopes
     */
    public function scopeOfType(Builder $query, string $type): Builder
    {
        return $query->where('type', $type);
    }

    public function scopeCreatedBetween(Builder $query, $startDate, $endDate): Builder
    {
        return $query->whereBetween('created_at', [
            Carbon::parse($startDate)->startOfDay(),
            Carbon::parse($endDate)->endOfDay()
        ]);
    }

    public function scopeSearch(Builder $query, string $term): Builder
    {
        return $query->where(function ($query) use ($term) {
            $query->where('name', 'like', "%{$term}%")
                  ->orWhere('email', 'like', "%{$term}%")
                  ->orWhere('username', 'like', "%{$term}%");
        });
    }

    public function scopeWithRole(Builder $query, string $role): Builder
    {
        return $query->whereHas('roles', function ($query) use ($role) {
            $query->where('name', $role);
        });
    }

    public function scopeHasPostsCount(Builder $query, int $count, string $operator = '>='): Builder
    {
        return $query->withCount('posts')
                    ->having('posts_count', $operator, $count);
    }

    /**
     * Complex scopes
     */
    public function scopePopular(Builder $query): Builder
    {
        return $query->withCount(['posts', 'comments'])
                    ->orderByDesc('posts_count')
                    ->orderByDesc('comments_count');
    }

    public function scopeRecentlyActive(Builder $query, int $days = 30): Builder
    {
        return $query->where(function ($query) use ($days) {
            $date = Carbon::now()->subDays($days);
            
            $query->where('last_login_at', '>=', $date)
                  ->orWhereHas('posts', function ($query) use ($date) {
                      $query->where('created_at', '>=', $date);
                  })
                  ->orWhereHas('comments', function ($query) use ($date) {
                      $query->where('created_at', '>=', $date);
                  });
        });
    }

    public function scopeInfluencers(Builder $query, int $followerThreshold = 1000): Builder
    {
        return $query->withCount('followers')
                    ->having('followers_count', '>=', $followerThreshold)
                    ->orderByDesc('followers_count');
    }

    /**
     * Conditional scopes
     */
    public function scopeFilter(Builder $query, array $filters): Builder
    {
        return $query->when($filters['status'] ?? false, function ($query, $status) {
            return $query->where('status', $status);
        })
        ->when($filters['role'] ?? false, function ($query, $role) {
            return $query->withRole($role);
        })
        ->when($filters['search'] ?? false, function ($query, $search) {
            return $query->search($search);
        })
        ->when($filters['created_from'] ?? false, function ($query, $date) {
            return $query->where('created_at', '>=', $date);
        })
        ->when($filters['created_to'] ?? false, function ($query, $date) {
            return $query->where('created_at', '<=', $date);
        });
    }
}

// Usage examples:
$activeUsers = User::active()->get();
$recentUsers = User::createdBetween('2024-01-01', '2024-01-31')->get();
$searchResults = User::search('john')->verified()->get();
$influencers = User::influencers(5000)->take(10)->get();

$filteredUsers = User::filter([
    'status' => 'active',
    'role' => 'author',
    'search' => 'john',
    'created_from' => '2024-01-01'
])->paginate(15);
```

### Global Scopes

#### 📁 app/Models/Scopes/ActiveScope.php
```php
<?php

namespace App\Models\Scopes;

use Illuminate\Database\Eloquent\Builder;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Scope;

class ActiveScope implements Scope
{
    public function apply(Builder $builder, Model $model): void
    {
        $builder->where('is_active', true);
    }
}
```

#### 📁 app/Models/Scopes/PublishedScope.php
```php
<?php

namespace App\Models\Scopes;

use Illuminate\Database\Eloquent\Builder;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Scope;
use Carbon\Carbon;

class PublishedScope implements Scope
{
    public function apply(Builder $builder, Model $model): void
    {
        $builder->where('status', 'published')
                ->where('published_at', '<=', Carbon::now());
    }
}
```

#### Using Global Scopes
```php
<?php

namespace App\Models;

use App\Models\Scopes\ActiveScope;
use App\Models\Scopes\PublishedScope;
use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    protected static function booted()
    {
        static::addGlobalScope(new PublishedScope);
    }
}

class User extends Model
{
    protected static function booted()
    {
        static::addGlobalScope('active', function (Builder $builder) {
            $builder->where('is_active', true);
        });
    }
}

// Usage:
$posts = Post::all(); // Only published posts
$allPosts = Post::withoutGlobalScopes()->get(); // All posts
$drafts = Post::withoutGlobalScope(PublishedScope::class)->where('status', 'draft')->get();
```

## 🔗 Advanced Relationships

### One-to-One Relationships

#### 📁 app/Models/User.php
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\HasOne;

class User extends Model
{
    /**
     * Basic one-to-one relationship
     */
    public function profile(): HasOne
    {
        return $this->hasOne(Profile::class);
    }

    /**
     * One-to-one with custom keys
     */
    public function settings(): HasOne
    {
        return $this->hasOne(UserSettings::class, 'user_uuid', 'uuid');
    }

    /**
     * Conditional one-to-one
     */
    public function latestOrder(): HasOne
    {
        return $this->hasOne(Order::class)->latestOfMany();
    }

    public function oldestOrder(): HasOne
    {
        return $this->hasOne(Order::class)->oldestOfMany();
    }

    public function largestOrder(): HasOne
    {
        return $this->hasOne(Order::class)->ofMany('total', 'max');
    }
}
```

### One-to-Many Relationships

#### 📁 app/Models/Post.php
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\HasMany;
use Illuminate\Database\Eloquent\Relations\BelongsTo;

class Post extends Model
{
    /**
     * Basic one-to-many relationship
     */
    public function comments(): HasMany
    {
        return $this->hasMany(Comment::class);
    }

    /**
     * Conditional one-to-many relationships
     */
    public function approvedComments(): HasMany
    {
        return $this->hasMany(Comment::class)->where('status', 'approved');
    }

    public function recentComments(): HasMany
    {
        return $this->hasMany(Comment::class)
                    ->where('created_at', '>=', now()->subDays(7))
                    ->orderBy('created_at', 'desc');
    }

    public function topComments(): HasMany
    {
        return $this->hasMany(Comment::class)
                    ->orderBy('likes_count', 'desc')
                    ->limit(5);
    }

    /**
     * Inverse relationship
     */
    public function author(): BelongsTo
    {
        return $this->belongsTo(User::class, 'user_id');
    }

    public function category(): BelongsTo
    {
        return $this->belongsTo(Category::class);
    }
}
```

### Has One Through & Has Many Through

#### 📁 app/Models/Country.php
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\HasManyThrough;
use Illuminate\Database\Eloquent\Relations\HasOneThrough;

class Country extends Model
{
    /**
     * Get all posts from users in this country
     */
    public function posts(): HasManyThrough
    {
        return $this->hasManyThrough(Post::class, User::class);
    }

    /**
     * Get the latest post from users in this country
     */
    public function latestPost(): HasOneThrough
    {
        return $this->hasOneThrough(Post::class, User::class)->latest();
    }

    /**
     * Has many through with custom keys
     */
    public function orders(): HasManyThrough
    {
        return $this->hasManyThrough(
            Order::class,
            User::class,
            'country_id', // Foreign key on users table
            'user_id',    // Foreign key on orders table
            'id',         // Local key on countries table
            'id'          // Local key on users table
        );
    }
}
```

### Morph Relationships (Polymorphic)

#### 📁 app/Models/Comment.php
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\MorphTo;

class Comment extends Model
{
    protected $fillable = ['content', 'commentable_id', 'commentable_type', 'user_id'];

    /**
     * Get the commentable model (post, video, etc.)
     */
    public function commentable(): MorphTo
    {
        return $this->morphTo();
    }

    public function user(): BelongsTo
    {
        return $this->belongsTo(User::class);
    }
}
```

#### 📁 app/Models/Post.php
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\MorphMany;

class Post extends Model
{
    /**
     * Get all comments for the post
     */
    public function comments(): MorphMany
    {
        return $this->morphMany(Comment::class, 'commentable');
    }

    /**
     * Get all likes for the post
     */
    public function likes(): MorphMany
    {
        return $this->morphMany(Like::class, 'likeable');
    }

    /**
     * Get all tags for the post (morph to many)
     */
    public function tags(): MorphToMany
    {
        return $this->morphToMany(Tag::class, 'taggable');
    }
}
```

## 🔄 Polymorphic Relations

### One-to-Many Polymorphic Relations

#### 📁 app/Models/Image.php
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\MorphTo;

class Image extends Model
{
    protected $fillable = ['url', 'alt_text', 'imageable_id', 'imageable_type'];

    /**
     * Get the owning imageable model
     */
    public function imageable(): MorphTo
    {
        return $this->morphTo();
    }
}
```

#### 📁 app/Models/User.php
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\MorphMany;
use Illuminate\Database\Eloquent\Relations\MorphOne;

class User extends Model
{
    /**
     * Get all images for the user
     */
    public function images(): MorphMany
    {
        return $this->morphMany(Image::class, 'imageable');
    }

    /**
     * Get the user's avatar image
     */
    public function avatar(): MorphOne
    {
        return $this->morphOne(Image::class, 'imageable')
                    ->where('type', 'avatar');
    }
}
```

### Many-to-Many Polymorphic Relations

#### 📁 app/Models/Tag.php
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\MorphToMany;

class Tag extends Model
{
    protected $fillable = ['name', 'slug'];

    /**
     * Get all posts that are assigned this tag
     */
    public function posts(): MorphToMany
    {
        return $this->morphedByMany(Post::class, 'taggable');
    }

    /**
     * Get all videos that are assigned this tag
     */
    public function videos(): MorphToMany
    {
        return $this->morphedByMany(Video::class, 'taggable');
    }

    /**
     * Get all users that are assigned this tag
     */
    public function users(): MorphToMany
    {
        return $this->morphedByMany(User::class, 'taggable');
    }

    /**
     * Get all taggable items
     */
    public function taggables(): MorphToMany
    {
        return $this->morphToMany(Model::class, 'taggable');
    }
}
```

### Custom Polymorphic Types

#### 📁 app/Providers/AppServiceProvider.php
```php
<?php

namespace App\Providers;

use Illuminate\Database\Eloquent\Relations\Relation;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        // Map polymorphic types to avoid storing full class names
        Relation::morphMap([
            'post' => 'App\Models\Post',
            'video' => 'App\Models\Video',
            'user' => 'App\Models\User',
            'comment' => 'App\Models\Comment',
        ]);
    }
}
```

### Complex Polymorphic Example

#### 📁 app/Models/Activity.php
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\MorphTo;
use Illuminate\Database\Eloquent\Relations\BelongsTo;

class Activity extends Model
{
    protected $fillable = [
        'user_id', 'action', 'subject_type', 'subject_id', 
        'properties', 'ip_address', 'user_agent'
    ];

    protected $casts = [
        'properties' => 'array',
    ];

    /**
     * Get the user who performed the activity
     */
    public function user(): BelongsTo
    {
        return $this->belongsTo(User::class);
    }

    /**
     * Get the subject of the activity
     */
    public function subject(): MorphTo
    {
        return $this->morphTo();
    }

    /**
     * Scope for specific actions
     */
    public function scopeForAction($query, string $action)
    {
        return $query->where('action', $action);
    }

    /**
     * Scope for activities by user
     */
    public function scopeByUser($query, User $user)
    {
        return $query->where('user_id', $user->id);
    }
}
```

## 🔗 Many-to-Many Relationships

### Basic Many-to-Many

#### 📁 app/Models/User.php
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsToMany;

class User extends Model
{
    /**
     * Basic many-to-many relationship
     */
    public function roles(): BelongsToMany
    {
        return $this->belongsToMany(Role::class);
    }

    /**
     * Many-to-many with custom table and keys
     */
    public function permissions(): BelongsToMany
    {
        return $this->belongsToMany(
            Permission::class,
            'user_permissions', // Custom table name
            'user_id',          // Foreign key in pivot table
            'permission_id'     // Related key in pivot table
        );
    }

    /**
     * Many-to-many with pivot data
     */
    public function projects(): BelongsToMany
    {
        return $this->belongsToMany(Project::class)
                    ->withPivot(['role', 'joined_at', 'is_active'])
                    ->withTimestamps();
    }

    /**
     * Many-to-many with conditions
     */
    public function activeProjects(): BelongsToMany
    {
        return $this->belongsToMany(Project::class)
                    ->withPivot(['role', 'joined_at', 'is_active'])
                    ->wherePivot('is_active', true);
    }

    /**
     * Many-to-many with ordering
     */
    public function orderedRoles(): BelongsToMany
    {
        return $this->belongsToMany(Role::class)
                    ->orderByPivot('created_at', 'desc');
    }

    /**
     * Self-referencing many-to-many (followers/following)
     */
    public function followers(): BelongsToMany
    {
        return $this->belongsToMany(User::class, 'user_followers', 'following_id', 'follower_id')
                    ->withTimestamps();
    }

    public function following(): BelongsToMany
    {
        return $this->belongsToMany(User::class, 'user_followers', 'follower_id', 'following_id')
                    ->withTimestamps();
    }
}
```

### Custom Pivot Model

#### 📁 app/Models/ProjectUser.php
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Relations\Pivot;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
use Carbon\Carbon;

class ProjectUser extends Pivot
{
    protected $table = 'project_user';

    protected $fillable = ['user_id', 'project_id', 'role', 'joined_at', 'is_active'];

    protected $casts = [
        'joined_at' => 'datetime',
        'is_active' => 'boolean',
    ];

    /**
     * Get the user
     */
    public function user(): BelongsTo
    {
        return $this->belongsTo(User::class);
    }

    /**
     * Get the project
     */
    public function project(): BelongsTo
    {
        return $this->belongsTo(Project::class);
    }

    /**
     * Check if user is project admin
     */
    public function isAdmin(): bool
    {
        return $this->role === 'admin';
    }

    /**
     * Get days since joined
     */
    public function daysSinceJoined(): int
    {
        return $this->joined_at->diffInDays(Carbon::now());
    }
}
```

#### Using Custom Pivot Model
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsToMany;

class Project extends Model
{
    public function users(): BelongsToMany
    {
        return $this->belongsToMany(User::class)
                    ->using(ProjectUser::class)
                    ->withPivot(['role', 'joined_at', 'is_active'])
                    ->withTimestamps();
    }
}

// Usage:
$project = Project::with('users')->first();
foreach ($project->users as $user) {
    echo $user->name . ' - ' . $user->pivot->role;
    echo ' (joined ' . $user->pivot->daysSinceJoined() . ' days ago)';
}
```

## ⚡ Eager Loading & Performance

### Basic Eager Loading

#### 📁 app/Http/Controllers/PostController.php
```php
<?php

namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Controller;

class PostController extends Controller
{
    public function index()
    {
        // Bad: N+1 problem
        $posts = Post::all();
        foreach ($posts as $post) {
            echo $post->author->name; // Each iteration hits the database
        }

        // Good: Eager loading
        $posts = Post::with('author')->get();
        foreach ($posts as $post) {
            echo $post->author->name; // No additional queries
        }

        // Multiple relationships
        $posts = Post::with(['author', 'comments', 'tags'])->get();

        // Nested relationships
        $posts = Post::with(['author.profile', 'comments.user'])->get();

        // Conditional eager loading
        $posts = Post::with(['author', 'comments' => function ($query) {
            $query->where('approved', true)->orderBy('created_at', 'desc');
        }])->get();
    }
}
```

### Advanced Eager Loading Techniques

#### 📁 app/Models/Post.php
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\HasMany;
use Illuminate\Database\Eloquent\Relations\BelongsTo;

class Post extends Model
{
    /**
     * Default relationships to eager load
     */
    protected $with = ['author'];

    /**
     * Relationships
     */
    public function author(): BelongsTo
    {
        return $this->belongsTo(User::class, 'user_id');
    }

    public function comments(): HasMany
    {
        return $this->hasMany(Comment::class);
    }

    public function approvedComments(): HasMany
    {
        return $this->hasMany(Comment::class)->where('approved', true);
    }

    /**
     * Load counts efficiently
     */
    public function scopeWithCounts($query)
    {
        return $query->withCount([
            'comments',
            'approvedComments as approved_comments_count',
            'likes',
            'views'
        ]);
    }

    /**
     * Load aggregates
     */
    public function scopeWithAggregates($query)
    {
        return $query->withAvg('ratings', 'score')
                    ->withSum('orders', 'amount')
                    ->withMax('comments', 'created_at')
                    ->withMin('comments', 'created_at');
    }
}
```

### Lazy Eager Loading

```php
// Load relationships after model retrieval
$posts = Post::all();

if ($includeComments) {
    $posts->load('comments');
}

if ($includeAuthorProfile) {
    $posts->load('author.profile');
}

// Load with conditions
$posts->load(['comments' => function ($query) {
    $query->where('approved', true);
}]);

// Load counts
$posts->loadCount(['comments', 'likes']);

// Load missing relationships
$posts->loadMissing(['author', 'tags']);
```

### Preventing Lazy Loading in Production

#### 📁 app/Providers/AppServiceProvider.php
```php
<?php

namespace App\Providers;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        // Prevent lazy loading in production
        Model::preventLazyLoading(!app()->isProduction());
        
        // Prevent silently discarding attributes
        Model::preventSilentlyDiscardingAttributes(!app()->isProduction());
        
        // Prevent accessing missing attributes
        Model::preventAccessingMissingAttributes(!app()->isProduction());
    }
}
```

## 📦 Custom Collections

### Creating Custom Collection

#### 📁 app/Collections/UserCollection.php
```php
<?php

namespace App\Collections;

use Illuminate\Database\Eloquent\Collection;

class UserCollection extends Collection
{
    /**
     * Get only active users
     */
    public function active()
    {
        return $this->filter(function ($user) {
            return $user->is_active;
        });
    }

    /**
     * Get only verified users
     */
    public function verified()
    {
        return $this->filter(function ($user) {
            return $user->email_verified_at !== null;
        });
    }

    /**
     * Get users by role
     */
    public function withRole(string $role)
    {
        return $this->filter(function ($user) use ($role) {
            return $user->roles->pluck('name')->contains($role);
        });
    }

    /**
     * Calculate total balance
     */
    public function totalBalance()
    {
        return $this->sum('balance');
    }

    /**
     * Get users by country
     */
    public function fromCountry(string $country)
    {
        return $this->filter(function ($user) use ($country) {
            return $user->profile && $user->profile->country === $country;
        });
    }

    /**
     * Get top users by posts count
     */
    public function topPosters(int $limit = 10)
    {
        return $this->sortByDesc('posts_count')->take($limit);
    }

    /**
     * Export to CSV format
     */
    public function toCsvArray()
    {
        return $this->map(function ($user) {
            return [
                'id' => $user->id,
                'name' => $user->name,
                'email' => $user->email,
                'created_at' => $user->created_at->format('Y-m-d H:i:s'),
                'posts_count' => $user->posts_count ?? 0,
                'is_active' => $user->is_active ? 'Yes' : 'No',
            ];
        })->toArray();
    }
}
```

#### 📁 app/Models/User.php
```php
<?php

namespace App\Models;

use App\Collections\UserCollection;
use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    /**
     * Create a new Eloquent Collection instance
     */
    public function newCollection(array $models = [])
    {
        return new UserCollection($models);
    }
}

// Usage:
$users = User::all();
$activeUsers = $users->active();
$verifiedAdmins = $users->verified()->withRole('admin');
$totalBalance = $users->totalBalance();
$csvData = $users->toCsvArray();
```

### Collection Macros

#### 📁 app/Providers/AppServiceProvider.php
```php
<?php

namespace App\Providers;

use Illuminate\Database\Eloquent\Collection;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        // Add custom collection methods
        Collection::macro('toSelectArray', function ($value = 'id', $label = 'name') {
            return $this->pluck($label, $value)->toArray();
        });

        Collection::macro('whereBetweenDates', function ($field, $start, $end) {
            return $this->filter(function ($item) use ($field, $start, $end) {
                $date = $item->{$field};
                return $date >= $start && $date <= $end;
            });
        });

        Collection::macro('groupByMonth', function ($field = 'created_at') {
            return $this->groupBy(function ($item) use ($field) {
                return $item->{$field}->format('Y-m');
            });
        });
    }
}

// Usage:
$users = User::all();
$selectOptions = $users->toSelectArray('id', 'name');
$recentUsers = $users->whereBetweenDates('created_at', now()->subMonth(), now());
$usersByMonth = $users->groupByMonth();
```

## 🎪 Model Events

### Basic Model Events

#### 📁 app/Models/User.php
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Support\Str;

class User extends Model
{
    protected static function booted()
    {
        // Creating event
        static::creating(function ($user) {
            $user->uuid = Str::uuid();
            $user->slug = Str::slug($user->name);
        });

        // Created event
        static::created(function ($user) {
            // Send welcome email
            $user->sendWelcomeEmail();
            
            // Create default profile
            $user->profile()->create([
                'bio' => 'New user',
                'avatar' => 'default-avatar.png'
            ]);
        });

        // Updating event
        static::updating(function ($user) {
            if ($user->isDirty('email')) {
                $user->email_verified_at = null;
            }
        });

        // Updated event
        static::updated(function ($user) {
            if ($user->wasChanged('email')) {
                $user->sendEmailVerificationNotification();
            }
        });

        // Deleting event
        static::deleting(function ($user) {
            // Soft delete related records
            $user->posts()->delete();
            $user->comments()->delete();
        });

        // Deleted event
        static::deleted(function ($user) {
            // Clean up files
            if ($user->avatar) {
                Storage::delete('avatars/' . $user->avatar);
            }
        });
    }
}
```

### Custom Model Events

#### 📁 app/Models/Post.php
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    /**
     * The event map for the model
     */
    protected $dispatchesEvents = [
        'published' => PostPublished::class,
        'unpublished' => PostUnpublished::class,
    ];

    protected static function booted()
    {
        // Handle status changes
        static::updated(function ($post) {
            if ($post->wasChanged('status')) {
                if ($post->status === 'published' && $post->getOriginal('status') !== 'published') {
                    $post->fireModelEvent('published');
                } elseif ($post->status !== 'published' && $post->getOriginal('status') === 'published') {
                    $post->fireModelEvent('unpublished');
                }
            }
        });
    }

    /**
     * Publish the post
     */
    public function publish()
    {
        $this->update([
            'status' => 'published',
            'published_at' => now()
        ]);
    }

    /**
     * Unpublish the post
     */
    public function unpublish()
    {
        $this->update([
            'status' => 'draft',
            'published_at' => null
        ]);
    }
}
```

### Event Listeners

#### 📁 app/Listeners/PostPublishedListener.php
```php
<?php

namespace App\Listeners;

use App\Events\PostPublished;
use App\Notifications\NewPostNotification;
use App\Models\User;

class PostPublishedListener
{
    public function handle(PostPublished $event)
    {
        $post = $event->post;
        
        // Notify followers
        $followers = $post->author->followers;
        foreach ($followers as $follower) {
            $follower->notify(new NewPostNotification($post));
        }
        
        // Update search index
        $this->updateSearchIndex($post);
        
        // Generate sitemap
        $this->generateSitemap();
    }
    
    private function updateSearchIndex($post)
    {
        // Implementation for search index update
    }
    
    private function generateSitemap()
    {
        // Implementation for sitemap generation
    }
}
```

## 🔍 Advanced Query Techniques

### Subqueries and Raw Queries

#### 📁 app/Http/Controllers/AnalyticsController.php
```php
<?php

namespace App\Http\Controllers;

use App\Models\User;
use App\Models\Post;
use App\Models\Comment;
use Illuminate\Http\Controller;
use Illuminate\Support\Facades\DB;

class AnalyticsController extends Controller
{
    public function userStatistics()
    {
        // Subquery to get user post counts
        $users = User::select('users.*')
            ->selectSub(function ($query) {
                $query->from('posts')
                      ->whereColumn('posts.user_id', 'users.id')
                      ->selectRaw('count(*)');
            }, 'posts_count')
            ->selectSub(function ($query) {
                $query->from('posts')
                      ->whereColumn('posts.user_id', 'users.id')
                      ->where('status', 'published')
                      ->selectRaw('count(*)');
            }, 'published_posts_count')
            ->get();

        // Using subquery in where clause
        $activeAuthors = User::whereIn('id', function ($query) {
            $query->select('user_id')
                  ->from('posts')
                  ->where('created_at', '>=', now()->subDays(30))
                  ->groupBy('user_id')
                  ->havingRaw('count(*) >= 5');
        })->get();

        return response()->json([
            'users' => $users,
            'active_authors' => $activeAuthors
        ]);
    }

    public function complexAnalytics()
    {
        // Raw SQL with parameter binding
        $results = DB::select(
            "SELECT 
                u.name,
                COUNT(p.id) as post_count,
                AVG(c.rating) as avg_rating,
                SUM(CASE WHEN p.status = 'published' THEN 1 ELSE 0 END) as published_count
            FROM users u
            LEFT JOIN posts p ON u.id = p.user_id
            LEFT JOIN comments c ON p.id = c.post_id
            WHERE u.created_at >= ?
            GROUP BY u.id, u.name
            HAVING post_count > ?
            ORDER BY avg_rating DESC",
            [now()->subMonths(6), 10]
        );

        // Using query builder for complex queries
        $topUsers = DB::table('users')
            ->join('posts', 'users.id', '=', 'posts.user_id')
            ->join('comments', 'posts.id', '=', 'comments.post_id')
            ->select(
                'users.name',
                DB::raw('COUNT(DISTINCT posts.id) as post_count'),
                DB::raw('COUNT(comments.id) as comment_count'),
                DB::raw('AVG(comments.rating) as avg_rating')
            )
            ->where('posts.status', 'published')
            ->where('users.created_at', '>=', now()->subYear())
            ->groupBy('users.id', 'users.name')
            ->havingRaw('post_count >= 10')
            ->orderByDesc('avg_rating')
            ->limit(20)
            ->get();

        return response()->json($results);
    }
}
```

### Conditional Queries

#### 📁 app/Http/Controllers/SearchController.php
```php
<?php

namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;
use Illuminate\Http\Controller;

class SearchController extends Controller
{
    public function search(Request $request)
    {
        $query = Post::query();

        // Conditional where clauses
        $query->when($request->search, function ($query, $search) {
            return $query->where('title', 'like', "%{$search}%")
                        ->orWhere('content', 'like', "%{$search}%");
        });

        $query->when($request->category, function ($query, $category) {
            return $query->where('category_id', $category);
        });

        $query->when($request->author, function ($query, $author) {
            return $query->whereHas('author', function ($query) use ($author) {
                $query->where('name', 'like', "%{$author}%");
            });
        });

        $query->when($request->date_from, function ($query, $date) {
            return $query->where('created_at', '>=', $date);
        });

        $query->when($request->date_to, function ($query, $date) {
            return $query->where('created_at', '<=', $date);
        });

        $query->when($request->status, function ($query, $status) {
            return $query->where('status', $status);
        }, function ($query) {
            // Default condition when status is not provided
            return $query->where('status', 'published');
        });

        // Dynamic ordering
        $query->when($request->sort, function ($query, $sort) {
            switch ($sort) {
                case 'title':
                    return $query->orderBy('title');
                case 'date':
                    return $query->orderBy('created_at', 'desc');
                case 'popularity':
                    return $query->withCount('comments')->orderBy('comments_count', 'desc');
                default:
                    return $query->orderBy('created_at', 'desc');
            }
        });

        return $query->with(['author', 'category'])->paginate(15);
    }
}
```

### Advanced Where Clauses

```php
// Multiple OR conditions
Post::where(function ($query) {
    $query->where('status', 'published')
          ->orWhere('status', 'featured');
})->where('created_at', '>', now()->subDays(30))->get();

// Nested conditions
Post::where(function ($query) {
    $query->where('title', 'like', '%Laravel%')
          ->orWhere('content', 'like', '%Laravel%');
})->where(function ($query) {
    $query->where('status', 'published')
          ->where('published_at', '<=', now());
})->get();

// JSON column queries
User::whereJsonContains('preferences->languages', 'en')
    ->whereJsonLength('preferences->skills', '>', 3)
    ->get();

// Date queries
Post::whereDate('created_at', '2024-01-01')
    ->whereTime('created_at', '>=', '10:00:00')
    ->whereMonth('created_at', 1)
    ->whereYear('created_at', 2024)
    ->get();

// Relationship existence
Post::has('comments', '>', 10)
    ->whereHas('author', function ($query) {
        $query->where('is_verified', true);
    })
    ->whereDoesntHave('reports')
    ->get();
```

## 💾 Database Transactions

### Basic Transactions

#### 📁 app/Services/OrderService.php
```php
<?php

namespace App\Services;

use App\Models\Order;
use App\Models\Product;
use App\Models\User;
use Illuminate\Support\Facades\DB;
use Exception;

class OrderService
{
    public function createOrder(User $user, array $items)
    {
        return DB::transaction(function () use ($user, $items) {
            // Create order
            $order = Order::create([
                'user_id' => $user->id,
                'status' => 'pending',
                'total' => 0
            ]);

            $total = 0;

            foreach ($items as $item) {
                $product = Product::findOrFail($item['product_id']);
                
                // Check stock
                if ($product->stock < $item['quantity']) {
                    throw new Exception("Insufficient stock for {$product->name}");
                }

                // Reduce stock
                $product->decrement('stock', $item['quantity']);

                // Create order item
                $order->items()->create([
                    'product_id' => $product->id,
                    'quantity' => $item['quantity'],
                    'price' => $product->price,
                    'total' => $product->price * $item['quantity']
                ]);

                $total += $product->price * $item['quantity'];
            }

            // Update order total
            $order->update(['total' => $total]);

            // Deduct from user balance
            if ($user->balance < $total) {
                throw new Exception('Insufficient balance');
            }

            $user->decrement('balance', $total);

            return $order->load('items.product');
        });
    }

    public function processPayment(Order $order, array $paymentData)
    {
        DB::transaction(function () use ($order, $paymentData) {
            // Process payment with external service
            $paymentResult = $this->processExternalPayment($paymentData);
            
            if (!$paymentResult['success']) {
                throw new Exception('Payment failed: ' . $paymentResult['message']);
            }

            // Update order status
            $order->update([
                'status' => 'paid',
                'payment_id' => $paymentResult['payment_id'],
                'paid_at' => now()
            ]);

            // Create payment record
            $order->payments()->create([
                'amount' => $order->total,
                'method' => $paymentData['method'],
                'status' => 'completed',
                'external_id' => $paymentResult['payment_id']
            ]);

            // Send confirmation email
            $order->user->notify(new OrderConfirmationNotification($order));

        }, 3); // Retry 3 times on deadlock
    }

    private function processExternalPayment(array $paymentData): array
    {
        // Mock external payment processing
        return [
            'success' => true,
            'payment_id' => 'pay_' . uniqid(),
            'message' => 'Payment processed successfully'
        ];
    }
}
```

### Manual Transaction Control

```php
use Illuminate\Support\Facades\DB;

try {
    DB::beginTransaction();
    
    // Perform database operations
    $user = User::create($userData);
    $profile = $user->profile()->create($profileData);
    $user->roles()->attach($roleIds);
    
    // Everything succeeded, commit the transaction
    DB::commit();
    
    return $user;
} catch (Exception $e) {
    // Something went wrong, rollback the transaction
    DB::rollback();
    
    throw $e;
}
```

### Savepoints (Nested Transactions)

```php
use Illuminate\Support\Facades\DB;

DB::transaction(function () {
    // Main transaction
    $user = User::create($userData);
    
    try {
        DB::transaction(function () use ($user) {
            // Nested transaction (savepoint)
            $user->profile()->create($profileData);
            $user->sendWelcomeEmail();
            
            // This might fail
            $this->chargeUserCard($user);
        });
    } catch (Exception $e) {
        // Nested transaction failed, but main transaction continues
        // User is created but without profile
        Log::warning('Profile creation failed', ['user_id' => $user->id, 'error' => $e->getMessage()]);
    }
    
    // Main transaction continues
    $user->markAsRegistered();
});
```

# 📤 Model Serialization

### Custom Serialization

#### 📁 app/Models/User.php
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    /**
     * Hidden attributes in serialization
     */
    protected $hidden = [
        'password', 'remember_token', 'email_verified_at'
    ];

    /**
     * Always visible attributes
     */
    protected $visible = [
        'id', 'name', 'email', 'created_at'
    ];

    /**
     * Appended attributes (virtual attributes)
     */
    protected $appends = [
        'full_name', 'avatar_url', 'is_online'
    ];

    /**
     * Date attributes
     */
    protected $dates = [
        'email_verified_at', 'last_login_at', 'trial_ends_at'
    ];

    /**
     * Accessor for appended attribute
     */
    public function getFullNameAttribute()
    {
        return $this->first_name . ' ' . $this->last_name;
    }

    public function getAvatarUrlAttribute()
    {
        return $this->avatar 
            ? asset('storage/avatars/' . $this->avatar)
            : asset('images/default-avatar.png');
    }

    public function getIsOnlineAttribute()
    {
        return $this->last_seen_at && $this->last_seen_at->gt(now()->subMinutes(5));
    }

    /**
     * Custom array conversion
     */
    public function toArray()
    {
        $array = parent::toArray();
        
        // Add custom fields
        $array['posts_count'] = $this->posts()->count();
        $array['account_age_days'] = $this->created_at->diffInDays(now());
        
        // Remove sensitive data based on context
        if (!auth()->user() || auth()->user()->id !== $this->id) {
            unset($array['email']);
        }
        
        return $array;
    }

    /**
     * Custom JSON serialization
     */
    public function jsonSerialize()
    {
        return array_merge(parent::jsonSerialize(), [
            'reputation' => $this->calculateReputation(),
            'badges' => $this->badges->pluck('name')->toArray(),
            'social_links' => $this->getSocialLinks(),
        ]);
    }

    /**
     * Different serialization for different contexts
     */
    public function toApiArray()
    {
        return [
            'id' => $this->id,
            'username' => $this->username,
            'display_name' => $this->name,
            'avatar' => $this->avatar_url,
            'joined_date' => $this->created_at->toISOString(),
            'stats' => [
                'posts' => $this->posts_count,
                'followers' => $this->followers_count,
                'reputation' => $this->reputation_score,
            ],
            'is_verified' => (bool) $this->email_verified_at,
            'is_premium' => $this->hasActivePremiumSubscription(),
        ];
    }

    public function toPublicArray()
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'avatar' => $this->avatar_url,
            'bio' => $this->profile->bio ?? null,
            'joined' => $this->created_at->format('M Y'),
        ];
    }

    private function calculateReputation()
    {
        return $this->posts()->sum('votes') + $this->comments()->sum('votes');
    }

    private function getSocialLinks()
    {
        return [
            'twitter' => $this->twitter_url,
            'github' => $this->github_url,
            'linkedin' => $this->linkedin_url,
        ];
    }
}
```

### API Resources

#### 📁 app/Http/Resources/UserResource.php
```php
<?php

namespace App\Http\Resources;

use Illuminate\Http\Resources\Json\JsonResource;

class UserResource extends JsonResource
{
    /**
     * Transform the resource into an array.
     */
    public function toArray($request)
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'email' => $this->when($this->showEmail($request), $this->email),
            'avatar' => $this->avatar_url,
            'bio' => $this->whenLoaded('profile', function () {
                return $this->profile->bio;
            }),
            'stats' => [
                'posts_count' => $this->whenCounted('posts'),
                'followers_count' => $this->whenCounted('followers'),
                'following_count' => $this->whenCounted('following'),
            ],
            'roles' => RoleResource::collection($this->whenLoaded('roles')),
            'latest_post' => new PostResource($this->whenLoaded('latestPost')),
            'created_at' => $this->created_at->toISOString(),
            'updated_at' => $this->updated_at->toISOString(),
            
            // Conditional attributes
            'is_following' => $this->when(
                auth()->check(),
                function () {
                    return auth()->user()->isFollowing($this->resource);
                }
            ),
            
            // Pivot data
            'pivot' => $this->whenPivotLoaded('user_project', function () {
                return [
                    'role' => $this->pivot->role,
                    'joined_at' => $this->pivot->joined_at,
                ];
            }),
        ];
    }

    /**
     * Add additional metadata
     */
    public function with($request)
    {
        return [
            'meta' => [
                'version' => '1.0',
                'generated_at' => now()->toISOString(),
            ],
        ];
    }

    private function showEmail($request)
    {
        return auth()->check() && 
               (auth()->user()->id === $this->id || auth()->user()->isAdmin());
    }
}
```

#### 📁 app/Http/Resources/PostCollection.php
```php
<?php

namespace App\Http\Resources;

use Illuminate\Http\Resources\Json\ResourceCollection;

class PostCollection extends ResourceCollection
{
    /**
     * Transform the resource collection into an array.
     */
    public function toArray($request)
    {
        return [
            'data' => $this->collection,
            'meta' => [
                'total_posts' => $this->collection->count(),
                'published_posts' => $this->collection->where('status', 'published')->count(),
                'draft_posts' => $this->collection->where('status', 'draft')->count(),
                'total_words' => $this->collection->sum('word_count'),
            ],
            'links' => [
                'create_post' => route('posts.create'),
                'my_posts' => route('user.posts'),
            ],
        ];
    }
}
```

## 🧪 Testing Eloquent Models

### Model Testing

#### 📁 tests/Unit/UserModelTest.php
```php
<?php

namespace Tests\Unit;

use App\Models\User;
use App\Models\Post;
use App\Models\Role;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class UserModelTest extends TestCase
{
    use RefreshDatabase;

    /** @test */
    public function it_can_have_many_posts()
    {
        $user = User::factory()->create();
        $posts = Post::factory(3)->create(['user_id' => $user->id]);

        $this->assertInstanceOf('Illuminate\Database\Eloquent\Collection', $user->posts);
        $this->assertCount(3, $user->posts);
        $this->assertTrue($user->posts->contains($posts->first()));
    }

    /** @test */
    public function it_can_have_many_roles()
    {
        $user = User::factory()->create();
        $roles = Role::factory(2)->create();
        
        $user->roles()->attach($roles->pluck('id'));

        $this->assertCount(2, $user->roles);
        $this->assertTrue($user->roles->contains($roles->first()));
    }

    /** @test */
    public function full_name_accessor_works_correctly()
    {
        $user = User::factory()->create([
            'first_name' => 'John',
            'last_name' => 'Doe'
        ]);

        $this->assertEquals('John Doe', $user->full_name);
    }

    /** @test */
    public function email_is_lowercased_when_set()
    {
        $user = User::factory()->create(['email' => 'JOHN@EXAMPLE.COM']);

        $this->assertEquals('john@example.com', $user->email);
    }

    /** @test */
    public function it_can_check_if_user_has_role()
    {
        $user = User::factory()->create();
        $role = Role::factory()->create(['name' => 'admin']);
        
        $user->roles()->attach($role);

        $this->assertTrue($user->hasRole('admin'));
        $this->assertFalse($user->hasRole('editor'));
    }

    /** @test */
    public function active_scope_only_returns_active_users()
    {
        User::factory(5)->create(['is_active' => true]);
        User::factory(3)->create(['is_active' => false]);

        $activeUsers = User::active()->get();

        $this->assertCount(5, $activeUsers);
        $this->assertTrue($activeUsers->every(fn($user) => $user->is_active));
    }

    /** @test */
    public function search_scope_works_correctly()
    {
        User::factory()->create(['name' => 'John Doe', 'email' => 'john@example.com']);
        User::factory()->create(['name' => 'Jane Smith', 'email' => 'jane@example.com']);
        User::factory()->create(['name' => 'Bob Johnson', 'email' => 'bob@test.com']);

        $results = User::search('john')->get();

        $this->assertCount(2, $results); // John Doe and Bob Johnson
    }

    /** @test */
    public function it_creates_slug_from_name()
    {
        $user = User::factory()->create(['name' => 'John Doe']);

        $this->assertEquals('john-doe', $user->slug);
    }

    /** @test */
    public function it_hides_sensitive_attributes_in_array()
    {
        $user = User::factory()->create([
            'password' => bcrypt('password'),
            'remember_token' => 'some-token'
        ]);

        $array = $user->toArray();

        $this->assertArrayNotHasKey('password', $array);
        $this->assertArrayNotHasKey('remember_token', $array);
        $this->assertArrayHasKey('full_name', $array);
    }
}
```

### Factory Testing

#### 📁 database/factories/UserFactory.php
```php
<?php

namespace Database\Factories;

use App\Models\User;
use Illuminate\Database\Eloquent\Factories\Factory;
use Illuminate\Support\Str;

class UserFactory extends Factory
{
    protected $model = User::class;

    public function definition()
    {
        return [
            'name' => $this->faker->name(),
            'email' => $this->faker->unique()->safeEmail(),
            'email_verified_at' => now(),
            'password' => bcrypt('password'),
            'remember_token' => Str::random(10),
            'is_active' => true,
            'balance' => $this->faker->randomFloat(2, 0, 1000),
        ];
    }

    /**
     * Factory states
     */
    public function unverified()
    {
        return $this->state(function (array $attributes) {
            return [
                'email_verified_at' => null,
            ];
        });
    }

    public function inactive()
    {
        return $this->state(function (array $attributes) {
            return [
                'is_active' => false,
            ];
        });
    }

    public function admin()
    {
        return $this->afterCreating(function (User $user) {
            $adminRole = Role::firstOrCreate(['name' => 'admin']);
            $user->roles()->attach($adminRole);
        });
    }

    public function withPosts($count = 3)
    {
        return $this->has(Post::factory()->count($count));
    }

    public function withProfile()
    {
        return $this->has(Profile::factory());
    }
}
```

### Feature Testing

#### 📁 tests/Feature/PostManagementTest.php
```php
<?php

namespace Tests\Feature;

use App\Models\User;
use App\Models\Post;
use App\Models\Category;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class PostManagementTest extends TestCase
{
    use RefreshDatabase;

    /** @test */
    public function user_can_create_post()
    {
        $user = User::factory()->create();
        $category = Category::factory()->create();

        $postData = [
            'title' => 'Test Post',
            'content' => 'This is test content',
            'category_id' => $category->id,
            'status' => 'published'
        ];

        $response = $this->actingAs($user)
            ->post('/posts', $postData);

        $response->assertRedirect();
        $this->assertDatabaseHas('posts', [
            'title' => 'Test Post',
            'user_id' => $user->id
        ]);
    }

    /** @test */
    public function user_cannot_edit_others_posts()
    {
        $author = User::factory()->create();
        $otherUser = User::factory()->create();
        $post = Post::factory()->create(['user_id' => $author->id]);

        $response = $this->actingAs($otherUser)
            ->put("/posts/{$post->id}", ['title' => 'Updated Title']);

        $response->assertStatus(403);
    }

    /** @test */
    public function deleting_user_soft_deletes_posts()
    {
        $user = User::factory()->withPosts(3)->create();
        
        $user->delete();

        $this->assertSoftDeleted($user);
        $this->assertEquals(3, Post::onlyTrashed()->where('user_id', $user->id)->count());
    }
}
```

## ⚡ Performance Optimization

### Database Query Optimization

#### 📁 app/Services/OptimizedQueryService.php
```php
<?php

namespace App\Services;

use App\Models\Post;
use App\Models\User;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Cache;

class OptimizedQueryService
{
    /**
     * Optimized post listing with eager loading
     */
    public function getPostsOptimized($perPage = 15)
    {
        return Post::with([
                'author:id,name,avatar',
                'category:id,name,slug',
                'tags:id,name'
            ])
            ->withCount(['comments', 'likes'])
            ->select(['id', 'title', 'slug', 'excerpt', 'user_id', 'category_id', 'created_at'])
            ->published()
            ->latest()
            ->paginate($perPage);
    }

    /**
     * Bulk operations for better performance
     */
    public function bulkUpdatePostViews(array $postIds)
    {
        // Instead of individual updates, use bulk increment
        Post::whereIn('id', $postIds)->increment('views_count');
    }

    /**
     * Efficient user statistics with single query
     */
    public function getUserStatistics($userId)
    {
        return Cache::remember("user_stats_{$userId}", 3600, function () use ($userId) {
            return DB::table('users')
                ->leftJoin('posts', 'users.id', '=', 'posts.user_id')
                ->leftJoin('comments', 'users.id', '=', 'comments.user_id')
                ->select([
                    'users.id',
                    'users.name',
                    DB::raw('COUNT(DISTINCT posts.id) as posts_count'),
                    DB::raw('COUNT(DISTINCT comments.id) as comments_count'),
                    DB::raw('SUM(posts.views_count) as total_views'),
                    DB::raw('AVG(posts.rating) as avg_rating')
                ])
                ->where('users.id', $userId)
                ->groupBy('users.id', 'users.name')
                ->first();
        });
    }

    /**
     * Memory-efficient chunk processing
     */
    public function processLargeDataset()
    {
        Post::with('author')
            ->where('status', 'published')
            ->chunk(1000, function ($posts) {
                foreach ($posts as $post) {
                    // Process each post
                    $this->processPost($post);
                }
            });
    }

    /**
     * Optimized search with full-text index
     */
    public function searchPosts($query, $filters = [])
    {
        $posts = Post::whereRaw('MATCH(title, content) AGAINST(? IN BOOLEAN MODE)', [$query])
            ->when($filters['category'] ?? null, function ($query, $category) {
                return $query->where('category_id', $category);
            })
            ->when($filters['author'] ?? null, function ($query, $author) {
                return $query->whereHas('author', function ($q) use ($author) {
                    $q->where('name', 'like', "%{$author}%");
                });
            })
            ->with(['author:id,name', 'category:id,name'])
            ->select(['id', 'title', 'slug', 'excerpt', 'user_id', 'category_id'])
            ->paginate(20);

        return $posts;
    }

    private function processPost($post)
    {
        // Processing logic here
    }
}
```

### Caching Strategies

#### 📁 app/Models/Post.php
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Support\Facades\Cache;

class Post extends Model
{
    protected static function booted()
    {
        // Clear cache when post is updated
        static::saved(function ($post) {
            Cache::tags(['posts', "post.{$post->id}"])->flush();
            Cache::forget("user_posts_{$post->user_id}");
            Cache::forget('popular_posts');
        });

        static::deleted(function ($post) {
            Cache::tags(['posts', "post.{$post->id}"])->flush();
        });
    }

    /**
     * Cached relationship
     */
    public function getCachedCommentsAttribute()
    {
        return Cache::tags("post.{$this->id}")
            ->remember("post_comments_{$this->id}", 3600, function () {
                return $this->comments()->with('author')->get();
            });
    }

    /**
     * Cache popular posts
     */
    public static function getPopularPosts($limit = 10)
    {
        return Cache::remember('popular_posts', 1800, function () use ($limit) {
            return static::withCount(['comments', 'likes'])
                ->orderByDesc('views_count')
                ->orderByDesc('likes_count')
                ->limit($limit)
                ->get();
        });
    }
}
```

### Database Indexing

#### 📁 database/migrations/add_indexes_to_posts_table.php
```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class AddIndexesToPostsTable extends Migration
{
    public function up()
    {
        Schema::table('posts', function (Blueprint $table) {
            // Composite indexes for common queries
            $table->index(['status', 'published_at']);
            $table->index(['user_id', 'status']);
            $table->index(['category_id', 'status', 'published_at']);
            
            // Full-text search index
            $table->fullText(['title', 'content']);
            
            // Foreign key indexes
            $table->index('user_id');
            $table->index('category_id');
        });
    }

    public function down()
    {
        Schema::table('posts', function (Blueprint $table) {
            $table->dropIndex(['status', 'published_at']);
            $table->dropIndex(['user_id', 'status']);
            $table->dropIndex(['category_id', 'status', 'published_at']);
            $table->dropFullText(['title', 'content']);
            $table->dropIndex(['user_id']);
            $table->dropIndex(['category_id']);
        });
    }
}
```

## 🐛 Common Issues & Solutions

### N+1 Query Problem

```php
// ❌ Bad: N+1 Problem
$posts = Post::all();
foreach ($posts as $post) {
    echo $post->author->name; // Each iteration hits database
}

// ✅ Good: Eager Loading
$posts = Post::with('author')->get();
foreach ($posts as $post) {
    echo $post->author->name; // No additional queries
}

// ✅ Better: Selective Loading
$posts = Post::with('author:id,name')->get();

// ✅ Best: Load only what you need
$posts = Post::select(['id', 'title', 'user_id'])
    ->with('author:id,name')
    ->get();
```

### Mass Assignment Issues

```php
// ❌ Bad: Mass assignment vulnerability
User::create($request->all());

// ✅ Good: Protected with fillable
class User extends Model
{
    protected $fillable = ['name', 'email'];
}

// ✅ Better: Explicit assignment
User::create([
    'name' => $request->name,
    'email' => $request->email,
]);

// ✅ Best: Form request validation
class CreateUserRequest extends FormRequest
{
    public function rules()
    {
        return [
            'name' => 'required|string|max:255',
            'email' => 'required|email|unique:users',
        ];
    }
}
```

### Memory Issues with Large Datasets

```php
// ❌ Bad: Loading all records into memory
$users = User::all();
foreach ($users as $user) {
    $this->processUser($user);
}

// ✅ Good: Use chunking
User::chunk(1000, function ($users) {
    foreach ($users as $user) {
        $this->processUser($user);
    }
});

// ✅ Better: Use cursor for large datasets
foreach (User::cursor() as $user) {
    $this->processUser($user);
}

// ✅ Best: Lazy collections for memory efficiency
User::lazy()->each(function ($user) {
    $this->processUser($user);
});
```

### Soft Delete Issues

```php
// Issue: Related models not handling soft deletes properly
class Post extends Model
{
    use SoftDeletes;
    
    public function comments()
    {
        return $this->hasMany(Comment::class);
    }
}

// ✅ Solution: Include soft deletes in related model
class Comment extends Model
{
    use SoftDeletes;
    
    public function post()
    {
        // This will automatically exclude soft deleted posts
        return $this->belongsTo(Post::class);
    }
}

// Or explicitly handle soft deletes
class Comment extends Model
{
    public function post()
    {
        return $this->belongsTo(Post::class)->withTrashed();
    }
    
    public function postWithoutTrashed()
    {
        return $this->belongsTo(Post::class)->withoutTrashed();
    }
}
```

## ✨ Best Practices

### Model Organization

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;
use Illuminate\Database\Eloquent\Factories\HasFactory;

class Post extends Model
{
    use HasFactory, SoftDeletes;

    // 1. Table and primary key
    protected $table = 'posts';
    protected $primaryKey = 'id';
    public $incrementing = true;
    protected $keyType = 'int';
    public $timestamps = true;

    // 2. Mass assignment
    protected $fillable = [
        'title', 'content', 'excerpt', 'slug', 'status', 'published_at'
    ];

    protected $guarded = ['id'];

    // 3. Attribute casting
    protected $casts = [
        'published_at' => 'datetime',
        'metadata' => 'json',
        'is_featured' => 'boolean',
    ];

    // 4. Date attributes
    protected $dates = ['published_at', 'featured_at'];

    // 5. Hidden/Visible attributes
    protected $hidden = ['deleted_at'];
    protected $appends = ['reading_time', 'word_count'];

    // 6. Default values
    protected $attributes = [
        'status' => 'draft',
        'is_featured' => false,
    ];

    // 7. Boot method and events
    protected static function booted()
    {
        static::creating(function ($post) {
            $post->slug = $post->generateSlug($post->title);
        });
    }

    // 8. Accessors and Mutators
    protected function title(): Attribute
    {
        return Attribute::make(
            get: fn ($value) => ucwords($value),
            set: fn ($value) => strtolower($value),
        );
    }

    // 9. Relationships
    public function author(): BelongsTo
    {
        return $this->belongsTo(User::class, 'user_id');
    }

    public function comments(): HasMany
    {
        return $this->hasMany(Comment::class);
    }

    // 10. Scopes
    public function scopePublished($query)
    {
        return $query->where('status', 'published')
                    ->where('published_at', '<=', now());
    }

    // 11. Helper methods
    public function isPublished(): bool
    {
        return $this->status === 'published' && 
               $this->published_at && 
               $this->published_at->isPast();
    }

    private function generateSlug(string $title): string
    {
        return Str::slug($title);
    }
}
```

### Repository Pattern (Optional)

#### 📁 app/Repositories/PostRepository.php
```php
<?php

namespace App\Repositories;

use App\Models\Post;
use Illuminate\Database\Eloquent\Collection;
use Illuminate\Pagination\LengthAwarePaginator;

class PostRepository
{
    protected $model;

    public function __construct(Post $model)
    {
        $this->model = $model;
    }

    public function all(): Collection
    {
        return $this->model->with(['author', 'category'])->get();
    }

    public function find(int $id): ?Post
    {
        return $this->model->with(['author', 'category', 'comments'])
                          ->find($id);
    }

    public function published(int $perPage = 15): LengthAwarePaginator
    {
        return $this->model->published()
                          ->with(['author:id,name', 'category:id,name'])
                          ->latest('published_at')
                          ->paginate($perPage);
    }

    public function create(array $data): Post
    {
        return $this->model->create($data);
    }

    public function update(Post $post, array $data): bool
    {
        return $post->update($data);
    }

    public function delete(Post $post): bool
    {
        return $post->delete();
    }

    public function findBySlug(string $slug): ?Post
    {
        return $this->model->where('slug', $slug)
                          ->with(['author', 'category', 'comments.author'])
                          ->first();
    }
}
```

### Service Layer

#### 📁 app/Services/PostService.php
```php
<?php

namespace App\Services;

use App\Models\Post;
use App\Models\User;
use App\Repositories\PostRepository;
use Illuminate\Support\Facades\DB;

class PostService
{
    protected $postRepository;

    public function __construct(PostRepository $postRepository)
    {
        $this->postRepository = $postRepository;
    }

    public function createPost(User $author, array $data): Post
    {
        return DB::transaction(function () use ($author, $data) {
            $post = $this->postRepository->create(array_merge($data, [
                'user_id' => $author->id,
                'slug' => $this->generateUniqueSlug($data['title'])
            ]));

            // Handle tags
            if (isset($data['tags'])) {
                $post->tags()->sync($data['tags']);
            }

            // Handle featured image
            if (isset($data['featured_image'])) {
                $post->addMediaFromRequest('featured_image')
                     ->toMediaCollection('featured');
            }

            return $post->load(['author', 'tags']);
        });
    }

    public function publishPost(Post $post): bool
    {
        if ($post->status !== 'draft') {
            throw new \InvalidArgumentException('Only draft posts can be published');
        }

        return $this->postRepository->update($post, [
            'status' => 'published',
            'published_at' => now()
        ]);
    }

    private function generateUniqueSlug(string $title): string
    {
        $baseSlug = Str::slug($title);
        $slug = $baseSlug;
        $counter = 1;

        while (Post::where('slug', $slug)->exists()) {
            $slug = $baseSlug . '-' . $counter;
            $counter++;
        }

        return $slug;
    }
}
```

---

## 🎯 Conclusion

This comprehensive guide covers advanced Eloquent ORM concepts that will help you build robust, maintainable Laravel applications. Remember to:

- **Always use eager loading** to prevent N+1 queries
- **Implement proper caching** strategies for better performance
- **Use query scopes** to keep your queries organized and reusable
- **Test your models** thoroughly to ensure reliability
- **Follow consistent naming** conventions and code organization
- **Monitor query performance** using Laravel Telescope or similar tools

By mastering these advanced Eloquent concepts, you'll be able to build scalable applications that perform well even with large datasets and complex relationships.

### 📚 Additional Resources

- [Laravel Documentation](https://laravel.com/docs)
- [Eloquent ORM Documentation](https://laravel.com/docs/eloquent)
- [Laravel Query Builder](https://laravel.com/docs/queries)
- [Database Testing](https://laravel.com/docs/database-testing)
- [Laravel Telescope](https://laravel.com/docs/telescope)

### 🚀 Performance Tips Summary

1. **Use `select()` to limit columns**: Only fetch what you need
2. **Implement database indexes**: Speed up common queries
3. **Cache frequently accessed data**: Reduce database load
4. **Use chunking for large datasets**: Prevent memory issues
5. **Monitor query performance**: Use debugging tools
6. **Optimize relationships**: Use appropriate relationship types
7. **Implement queue jobs**: For heavy processing tasks

### 🔧 Advanced Techniques

#### Dynamic Relationships
```php
class User extends Model
{
    public function posts()
    {
        return $this->hasMany(Post::class);
    }
    
    public function publishedPosts()
    {
        return $this->posts()->where('status', 'published');
    }
    
    public function recentPosts($days = 30)
    {
        return $this->posts()->where('created_at', '>=', now()->subDays($days));
    }
}
```

#### Model Observers
```php
<?php

namespace App\Observers;

use App\Models\Post;

class PostObserver
{
    public function creating(Post $post)
    {
        $post->uuid = Str::uuid();
    }

    public function created(Post $post)
    {
        // Clear cache, send notifications, etc.
        Cache::tags('posts')->flush();
    }

    public function updated(Post $post)
    {
        if ($post->wasChanged('status')) {
            // Handle status change
            event(new PostStatusChanged($post));
        }
    }

    public function deleted(Post $post)
    {
        // Cleanup related data
        $post->comments()->delete();
        $post->tags()->detach();
    }
}
```

#### Global Query Constraints
```php
<?php

namespace App\Models;

class Post extends Model
{
    protected static function booted()
    {
        // Apply global constraints
        static::addGlobalScope('published', function (Builder $builder) {
            if (!auth()->check() || !auth()->user()->isAdmin()) {
                $builder->where('status', 'published');
            }
        });
        
        // Tenant-specific data
        static::addGlobalScope('tenant', function (Builder $builder) {
            if ($tenantId = session('tenant_id')) {
                $builder->where('tenant_id', $tenantId);
            }
        });
    }
}
```

#### Advanced Pivot Relationships
```php
class User extends Model
{
    public function projects()
    {
        return $this->belongsToMany(Project::class)
                    ->using(ProjectUser::class)
                    ->withPivot([
                        'role', 'joined_at', 'permissions', 
                        'is_active', 'hourly_rate'
                    ])
                    ->withTimestamps();
    }
    
    public function activeProjects()
    {
        return $this->projects()->wherePivot('is_active', true);
    }
    
    public function adminProjects()
    {
        return $this->projects()->wherePivot('role', 'admin');
    }
}

// Using pivot data
$user = User::with('projects')->first();
foreach ($user->projects as $project) {
    echo "Role: " . $project->pivot->role;
    echo "Joined: " . $project->pivot->joined_at->format('Y-m-d');
    echo "Rate: $" . $project->pivot->hourly_rate;
}
```

#### Complex Validation Rules
```php
<?php

namespace App\Rules;

use Illuminate\Contracts\Validation\Rule;
use App\Models\Post;

class UniqueSlugForUser implements Rule
{
    private $userId;
    private $postId;

    public function __construct($userId, $postId = null)
    {
        $this->userId = $userId;
        $this->postId = $postId;
    }

    public function passes($attribute, $value)
    {
        $query = Post::where('slug', $value)
                     ->where('user_id', $this->userId);
                     
        if ($this->postId) {
            $query->where('id', '!=', $this->postId);
        }
        
        return !$query->exists();
    }

    public function message()
    {
        return 'You already have a post with this slug.';
    }
}

// Usage in FormRequest
class CreatePostRequest extends FormRequest
{
    public function rules()
    {
        return [
            'title' => 'required|string|max:255',
            'slug' => [
                'required', 
                'string', 
                'max:255',
                new UniqueSlugForUser(auth()->id())
            ],
            'content' => 'required|string|min:100',
        ];
    }
}
```

#### Database Seeder with Relationships
```php
<?php

namespace Database\Seeders;

use App\Models\User;
use App\Models\Post;
use App\Models\Category;
use App\Models\Tag;
use App\Models\Comment;
use Illuminate\Database\Seeder;

class BlogSeeder extends Seeder
{
    public function run()
    {
        // Create categories
        $categories = Category::factory(10)->create();
        
        // Create tags
        $tags = Tag::factory(20)->create();
        
        // Create users with posts
        User::factory(50)
            ->has(
                Post::factory(3)
                    ->has(Comment::factory(5), 'comments')
                    ->state(function (array $attributes, User $user) use ($categories, $tags) {
                        return [
                            'category_id' => $categories->random()->id,
                        ];
                    })
            )
            ->create()
            ->each(function (User $user) use ($tags) {
                // Attach random tags to each post
                $user->posts->each(function (Post $post) use ($tags) {
                    $post->tags()->attach(
                        $tags->random(rand(1, 5))->pluck('id')->toArray()
                    );
                });
            });
    }
}
```

#### API Resource Transformations
```php
<?php

namespace App\Http\Resources;

use Illuminate\Http\Resources\Json\JsonResource;

class PostResource extends JsonResource
{
    public function toArray($request)
    {
        return [
            'id' => $this->id,
            'title' => $this->title,
            'slug' => $this->slug,
            'excerpt' => $this->excerpt,
            'content' => $this->when($request->routeIs('posts.show'), $this->content),
            'status' => $this->status,
            'reading_time' => $this->reading_time . ' min read',
            'published_at' => $this->published_at?->toISOString(),
            'updated_at' => $this->updated_at->toISOString(),
            
            // Relationships
            'author' => new UserResource($this->whenLoaded('author')),
            'category' => new CategoryResource($this->whenLoaded('category')),
            'tags' => TagResource::collection($this->whenLoaded('tags')),
            
            // Counts
            'comments_count' => $this->whenCounted('comments'),
            'likes_count' => $this->whenCounted('likes'),
            'views_count' => $this->views_count,
            
            // Conditional data based on authentication
            'can_edit' => $this->when(
                auth()->check(),
                auth()->user()->can('update', $this->resource)
            ),
            
            // Pivot data for many-to-many relationships
            'tag_metadata' => $this->whenPivotLoaded('post_tag', function () {
                return $this->pivot->metadata;
            }),
            
            // URLs
            'url' => route('posts.show', $this->slug),
            'edit_url' => $this->when(
                auth()->check() && auth()->user()->can('update', $this->resource),
                route('posts.edit', $this->id)
            ),
        ];
    }

    public function with($request)
    {
        return [
            'meta' => [
                'generated_at' => now()->toISOString(),
                'version' => config('app.api_version', '1.0'),
            ],
        ];
    }
}
```

#### Event-Driven Architecture
```php
<?php

namespace App\Events;

use App\Models\Post;
use Illuminate\Broadcasting\Channel;
use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Broadcasting\PresenceChannel;
use Illuminate\Broadcasting\PrivateChannel;
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;

class PostPublished implements ShouldBroadcast
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    public $post;

    public function __construct(Post $post)
    {
        $this->post = $post;
    }

    public function broadcastOn()
    {
        return [
            new PrivateChannel('user.' . $this->post->user_id),
            new Channel('posts'),
        ];
    }

    public function broadcastWith()
    {
        return [
            'post' => [
                'id' => $this->post->id,
                'title' => $this->post->title,
                'author' => $this->post->author->name,
                'published_at' => $this->post->published_at->toISOString(),
            ],
        ];
    }
}

// Listener
<?php

namespace App\Listeners;

use App\Events\PostPublished;
use App\Jobs\NotifySubscribers;
use App\Jobs\UpdateSearchIndex;
use App\Jobs\GenerateSitemap;

class HandlePostPublished
{
    public function handle(PostPublished $event)
    {
        $post = $event->post;
        
        // Dispatch jobs for async processing
        NotifySubscribers::dispatch($post);
        UpdateSearchIndex::dispatch($post);
        GenerateSitemap::dispatch();
        
        // Update cache
        cache()->tags(['posts', 'feed'])->flush();
        
        // Log activity
        activity()
            ->causedBy($post->author)
            ->performedOn($post)
            ->log('published a new post');
    }
}
```

#### Model Traits for Reusability
```php
<?php

namespace App\Traits;

use Illuminate\Support\Str;

trait HasSlug
{
    protected static function bootHasSlug()
    {
        static::creating(function ($model) {
            if (empty($model->slug)) {
                $model->slug = $model->generateSlug();
            }
        });

        static::updating(function ($model) {
            if ($model->isDirty($model->getSlugSource()) && empty($model->slug)) {
                $model->slug = $model->generateSlug();
            }
        });
    }

    public function generateSlug(): string
    {
        $source = $this->{$this->getSlugSource()};
        $baseSlug = Str::slug($source);
        $slug = $baseSlug;
        $counter = 1;

        while (static::where('slug', $slug)
                     ->where('id', '!=', $this->id ?? 0)
                     ->exists()) {
            $slug = $baseSlug . '-' . $counter;
            $counter++;
        }

        return $slug;
    }

    public function getSlugSource(): string
    {
        return $this->slugSource ?? 'title';
    }

    public function getRouteKeyName()
    {
        return 'slug';
    }
}

// Usage in models
class Post extends Model
{
    use HasSlug;

    protected $slugSource = 'title';
}

class Category extends Model
{
    use HasSlug;

    protected $slugSource = 'name';
}
```

This completes the comprehensive Laravel Eloquent ORM guide with advanced techniques, best practices, and real-world examples that you can use to build robust, scalable applications.