# 🚀 Laravel 11 vs Laravel 10 - Complete Comparison Guide

## 📋 Table of Contents
- [Version Overview](#-version-overview)
- [Key Differences](#-key-differences)
- [New Features in Laravel 11](#-new-features-in-laravel-11)
- [Breaking Changes](#-breaking-changes)
- [Performance Improvements](#-performance-improvements)
- [Migration Guide](#-migration-guide)
- [Deprecated Features](#-deprecated-features)

---

## 📊 Version Overview

| Aspect | Laravel 10 | Laravel 11 |
|--------|------------|-------------|
| **Release Date** | February 14, 2023 | March 12, 2024 |
| **PHP Requirement** | PHP 8.1+ | PHP 8.2+ |
| **Support Type** | LTS (Long Term Support) | LTS (Long Term Support) |
| **Support Until** | February 2026 | March 2027 |
| **Laravel Breeze** | Standard | Enhanced with React/Vue options |
| **Default Database** | MySQL | SQLite (development) |
| **Directory Structure** | Traditional | Streamlined |

---

## 🔑 Key Differences

### 🏗️ Application Structure

#### Laravel 10 Structure
```
app/
├── Console/
│   └── Kernel.php
├── Exceptions/
│   └── Handler.php
├── Http/
│   ├── Kernel.php
│   └── Middleware/
├── Models/
└── Providers/
    ├── AppServiceProvider.php
    ├── AuthServiceProvider.php
    ├── BroadcastServiceProvider.php
    ├── EventServiceProvider.php
    └── RouteServiceProvider.php
```

**Laravel 10 Structure Explanation:**

1. **📁 Console/Kernel.php**
   - Handles all Artisan commands registration
   - Manages command scheduling via the `schedule()` method
   - Contains command-specific logic and configurations
   ```php
   protected function schedule(Schedule $schedule)
   {
       $schedule->command('inspire')->hourly();
   }
   ```

2. **📁 Exceptions/Handler.php**
   - Central exception handling for the entire application
   - Customizes how exceptions are logged and rendered
   - Defines which input should not be flashed during validation errors
   ```php
   protected $dontFlash = [
       'current_password', 'password', 'password_confirmation',
   ];
   ```

3. **📁 Http/Kernel.php**
   - Manages HTTP middleware stack
   - Defines global middleware, middleware groups, and route middleware
   - Controls the HTTP request pipeline
   ```php
   protected $middleware = [
       \App\Http\Middleware\TrustProxies::class,
       \Fruitcake\Cors\HandleCors::class,
   ];
   ```

4. **📁 Providers/ (Multiple Files)**
   - **AppServiceProvider**: General application services
   - **AuthServiceProvider**: Authentication and authorization
   - **BroadcastServiceProvider**: Real-time broadcasting
   - **EventServiceProvider**: Event listeners mapping
   - **RouteServiceProvider**: Route configuration and caching

#### Laravel 11 Structure
```
app/
├── Http/
│   └── Middleware/
├── Models/
└── Providers/
    └── AppServiceProvider.php
```

**Laravel 11 Structure Explanation:**

1. **📁 Http/Middleware/ Only**
   - Contains only custom middleware classes
   - HTTP kernel functionality moved to framework internals
   - Cleaner separation of concerns

2. **📁 Models/**
   - Remains unchanged - contains Eloquent models
   - Same functionality as Laravel 10

3. **📁 Providers/AppServiceProvider.php (Consolidated)**
   - **Single provider** handles all service registration
   - Combines functionality of multiple Laravel 10 providers
   - Simplified configuration management
   ```php
   public function boot(): void
   {
       // All provider logic consolidated here
       $this->bootRoutes();
       $this->bootAuth();
       $this->bootEvents();
       $this->bootBroadcasting();
   }
   ```

### 🗂️ What Moved Where in Laravel 11

#### Console Commands Management
```php
// Laravel 10: app/Console/Kernel.php
protected function schedule(Schedule $schedule)
{
    $schedule->command('inspire')->hourly();
}

// Laravel 11: routes/console.php or AppServiceProvider
use Illuminate\Support\Facades\Schedule;

Schedule::command('inspire')->hourly();
```

#### Exception Handling
```php
// Laravel 10: app/Exceptions/Handler.php
class Handler extends ExceptionHandler
{
    protected $dontFlash = ['password'];
}

// Laravel 11: AppServiceProvider or use default
public function register(): void
{
    $this->app->singleton(ExceptionHandler::class, CustomHandler::class);
}
```

#### Middleware Registration
```php
// Laravel 10: app/Http/Kernel.php
protected $middleware = [
    \App\Http\Middleware\TrustProxies::class,
];

// Laravel 11: bootstrap/app.php
return Application::configure(basePath: dirname(__DIR__))
    ->withMiddleware(function (Middleware $middleware) {
        $middleware->append(\App\Http\Middleware\TrustProxies::class);
    })
    ->create();
```

#### Service Provider Consolidation
```php
// Laravel 10: Multiple files in app/Providers/
- AppServiceProvider.php
- AuthServiceProvider.php  
- BroadcastServiceProvider.php
- EventServiceProvider.php
- RouteServiceProvider.php

// Laravel 11: Single AppServiceProvider.php
class AppServiceProvider extends ServiceProvider
{
    public function boot(): void
    {
        // Authentication logic (from AuthServiceProvider)
        Gate::define('update-post', function (User $user, Post $post) {
            return $user->id === $post->user_id;
        });
        
        // Event listeners (from EventServiceProvider)
        Event::listen(OrderShipped::class, SendShipmentNotification::class);
        
        // Broadcasting (from BroadcastServiceProvider)
        Broadcast::routes();
        
        // Routes (from RouteServiceProvider)
        $this->loadRoutesFrom(base_path('routes/web.php'));
    }
}
```

### 🎯 Benefits of Laravel 11 Structure

#### 1. **Reduced Complexity**
- **50% fewer files** in the app directory
- Easier for beginners to understand
- Less decision fatigue about where to put code

#### 2. **Improved Performance**
- Fewer files to load and parse
- Reduced memory footprint
- Faster application bootstrap

#### 3. **Better Organization**
- Single place to manage most application concerns
- Clearer separation between framework and application code
- Reduced cognitive overhead

#### 4. **Maintained Flexibility**
- Can still create separate providers when needed
- All Laravel 10 functionality remains available
- Optional complexity - add files only when required

### 🔄 Migration Impact

#### Files You Can Delete (Laravel 10 → 11)
```bash
# Safe to remove in Laravel 11
rm app/Console/Kernel.php
rm app/Exceptions/Handler.php  
rm app/Http/Kernel.php
rm app/Providers/AuthServiceProvider.php
rm app/Providers/BroadcastServiceProvider.php
rm app/Providers/EventServiceProvider.php
rm app/Providers/RouteServiceProvider.php
```

#### New Files Created (Laravel 11)
```bash
# Laravel 11 creates these automatically
bootstrap/app.php  # New application configuration
routes/console.php # Command definitions (replaces Console/Kernel.php)
```

---

## 🌟 New Features in Laravel 11

### 1. 🎯 Streamlined Application Structure
Laravel 11 introduces a much cleaner directory structure with fewer default files.

### 2. 🗄️ SQLite as Default Database
```php
// Laravel 10 - .env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=root
DB_PASSWORD=

// Laravel 11 - .env
DB_CONNECTION=sqlite
# DB_HOST=127.0.0.1
# DB_PORT=3306
# DB_DATABASE=laravel
# DB_USERNAME=root
# DB_PASSWORD=
```

### 3. ⚡ Per-Second Rate Limiting
```php
// Laravel 10
Route::middleware('throttle:60,1')->group(function () {
    // Routes limited to 60 requests per minute
});

// Laravel 11 - New per-second limiting
Route::middleware('throttle:10,1,second')->group(function () {
    // Routes limited to 10 requests per second
});
```

### 4. 🔄 Health Check Routing
```php
// Laravel 11 - Built-in health checks
Route::get('/up', function () {
    return response()->json(['status' => 'ok']);
})->middleware('throttle:5,1,minute');
```

### 5. 📧 Improved Mail Testing
```php
// Laravel 11 - Enhanced mail testing
use Illuminate\Support\Facades\Mail;

Mail::fake();

// New assertion methods
Mail::assertQueued(OrderConfirmation::class, function ($mail) {
    return $mail->hasTo('customer@example.com') &&
           $mail->hasSubject('Order Confirmed');
});
```

### 6. 🎨 Dumpable Trait
```php
// Laravel 11 - New Dumpable trait
use Illuminate\Support\Traits\Dumpable;

class User extends Model
{
    use Dumpable;
}

// Usage
$user = User::find(1);
$user->dump(); // Dumps the model data
$user->dd();   // Dumps and dies
```

### 7. 🔀 Once Method for Closures
```php
// Laravel 11 - Ensure closure runs only once
use function Illuminate\Support\once;

$value = once(function () {
    return expensive_operation();
});
```

---

## ⚠️ Breaking Changes

### 1. 🐘 PHP Version Requirement
```bash
# Laravel 10
PHP 8.1.0 or higher

# Laravel 11
PHP 8.2.0 or higher
```

### 2. 🗃️ Database Changes
```php
// Laravel 10 - Default migrations
Schema::create('cache', function (Blueprint $table) {
    // Traditional cache table structure
});

// Laravel 11 - Updated cache structure
Schema::create('cache', function (Blueprint $table) {
    $table->string('key')->primary();
    $table->mediumText('value');
    $table->integer('expiration');
});
```

### 3. 🚫 Removed Features
- **Console Kernel**: Moved to service provider
- **Exception Handler**: Simplified error handling
- **HTTP Kernel**: Streamlined middleware handling
- **Multiple Service Providers**: Consolidated into AppServiceProvider

### 4. 📝 Configuration Changes
```php
// Laravel 10 - config/app.php
'providers' => [
    App\Providers\AppServiceProvider::class,
    App\Providers\AuthServiceProvider::class,
    App\Providers\BroadcastServiceProvider::class,
    App\Providers\EventServiceProvider::class,
    App\Providers\RouteServiceProvider::class,
],

// Laravel 11 - config/app.php (simplified)
'providers' => [
    App\Providers\AppServiceProvider::class,
],
```

---

## 🚀 Performance Improvements

### 1. ⚡ Faster Startup Time
- Reduced file loading with streamlined structure
- Optimized service provider loading
- Improved autoloading performance

### 2. 🗄️ Database Performance
```php
// Laravel 11 - Enhanced query performance
User::query()
    ->when($search, fn($query) => $query->where('name', 'like', "%{$search}%"))
    ->when($status, fn($query) => $query->where('status', $status))
    ->paginate();
```

### 3. 📦 Memory Usage
- Reduced memory footprint with fewer default classes
- Optimized container resolution
- Improved garbage collection

---

## 🔄 Migration Guide

### Step 1: Update Composer Requirements
```json
{
    "require": {
        "php": "^8.2",
        "laravel/framework": "^11.0"
    }
}
```

### Step 2: Update PHP Version
```bash
# Ubuntu/Debian
sudo apt update
sudo apt install php8.2 php8.2-cli php8.2-fpm

# Check version
php -v
```

### Step 3: Run Composer Update
```bash
composer update
```

### Step 4: Update Configuration Files
```bash
# Publish updated config files
php artisan config:publish

# Clear caches
php artisan config:clear
php artisan cache:clear
php artisan view:clear
```

### Step 5: Handle Breaking Changes

#### Update Exception Handling
```php
// Laravel 10 - app/Exceptions/Handler.php
class Handler extends ExceptionHandler
{
    protected $dontFlash = ['current_password', 'password', 'password_confirmation'];
}

// Laravel 11 - Move to AppServiceProvider
// app/Providers/AppServiceProvider.php
public function boot()
{
    $this->app->singleton(ExceptionHandler::class, function () {
        return new class extends ExceptionHandler {
            protected $dontFlash = ['current_password', 'password', 'password_confirmation'];
        };
    });
}
```

#### Update Console Commands
```php
// Laravel 10 - app/Console/Kernel.php
protected function schedule(Schedule $schedule)
{
    $schedule->command('inspire')->hourly();
}

// Laravel 11 - Move to AppServiceProvider or use closure
// In routes/console.php or AppServiceProvider
use Illuminate\Support\Facades\Schedule;

Schedule::command('inspire')->hourly();
```

### Step 6: Test Your Application
```bash
# Run tests
php artisan test

# Check for deprecated features
composer require --dev larastan/larastan
./vendor/bin/phpstan analyse
```

---

## 🗑️ Deprecated Features

### Laravel 10 Features Removed in Laravel 11

#### 1. Multiple Service Providers
```php
// ❌ No longer needed by default
App\Providers\AuthServiceProvider::class,
App\Providers\BroadcastServiceProvider::class,
App\Providers\EventServiceProvider::class,
App\Providers\RouteServiceProvider::class,
```

#### 2. Separate Kernel Classes
```php
// ❌ Removed files
app/Console/Kernel.php
app/Http/Kernel.php
app/Exceptions/Handler.php
```

#### 3. Traditional Database Configuration
```php
// ❌ MySQL as default (now SQLite for development)
DB_CONNECTION=mysql
```

---

## 🎯 Quick Migration Checklist

### ✅ Pre-Migration
- [ ] Backup your application
- [ ] Update PHP to 8.2+
- [ ] Review custom middleware
- [ ] Check third-party packages compatibility

### ✅ During Migration
- [ ] Update composer.json
- [ ] Run composer update
- [ ] Update configuration files
- [ ] Move custom kernel logic
- [ ] Update environment files

### ✅ Post-Migration
- [ ] Run all tests
- [ ] Check error handling
- [ ] Verify middleware functionality
- [ ] Test database connections
- [ ] Review logs for deprecation warnings

---

## 🔗 Additional Resources

### Official Documentation
- [Laravel 11 Upgrade Guide](https://laravel.com/docs/11.x/upgrade)
- [Laravel 10 Documentation](https://laravel.com/docs/10.x)

### Community Resources
- Laravel News
- Laracasts Migration Videos
- Laravel Discord Community

### Tools & Packages
- Laravel Shift (Automated upgrades)
- Rector (PHP code modernization)
- PHPStan (Static analysis)

---

## 🏁 Conclusion

Laravel 11 represents a significant step toward simplification while maintaining all the powerful features Laravel is known for. The streamlined structure makes it easier for new developers to understand the framework while providing experienced developers with a cleaner foundation to build upon.

**Key Takeaways:**
- 🎯 Simpler project structure
- ⚡ Better performance
- 🗄️ SQLite as development default
- 🔄 Enhanced developer experience
- 📱 Improved testing capabilities

---

*Last Updated: August 2025*
*Covering Laravel 10.x to Laravel 11.x Migration*