# 🔐 Laravel Multi-Authentication Guide

## 📋 Table of Contents
- [Overview](#overview)
- [What is Multi-Authentication?](#what-is-multi-authentication)
- [Benefits](#benefits)
- [Implementation Methods](#implementation-methods)
- [Step-by-Step Setup](#step-by-step-setup)
- [Configuration Examples](#configuration-examples)
- [Code Examples](#code-examples)
- [Best Practices](#best-practices)
- [Troubleshooting](#troubleshooting)

## 🎯 Overview

Laravel Multi-Authentication allows you to have multiple user types (guards) in a single application, each with their own authentication logic, middleware, and access control. This is essential for applications that serve different user roles like admins, customers, vendors, delivery personnel, etc.

## 🔍 What is Multi-Authentication?

Multi-authentication in Laravel refers to the ability to authenticate different types of users using separate authentication systems. Each user type can have:

- 📊 **Separate database tables**
- 🔒 **Different login/registration forms**
- 🛡️ **Unique middleware protection**
- 🎨 **Distinct user interfaces**
- ⚙️ **Custom authentication logic**

### Common Use Cases:
- 👨‍💼 **Admin Panel**: Administrators managing the system
- 👤 **Regular Users**: End customers/clients
- 🏪 **Shop Owners**: Vendors managing their stores
- 🚚 **Delivery Personnel**: Drivers handling deliveries
- 📈 **Managers**: Department heads with specific permissions

## ✅ Benefits

| Benefit | Description |
|---------|-------------|
| 🔐 **Security** | Isolated authentication systems prevent cross-contamination |
| 🎯 **Role Clarity** | Clear separation of user types and responsibilities |
| 🛠️ **Flexibility** | Different authentication methods for different user types |
| 📱 **User Experience** | Tailored interfaces for each user type |
| 🔧 **Maintenance** | Easier to maintain and debug separate systems |

## 🛠️ Implementation Methods

### 1. 🏗️ **Laravel Guards (Recommended)**
- Built-in Laravel feature
- Uses `config/auth.php` configuration
- Supports multiple providers and drivers

### 2. 📦 **Third-party Packages**
- `spatie/laravel-permission`
- `tymon/jwt-auth`
- Custom authentication packages

### 3. 🔨 **Custom Implementation**
- Manual setup with custom logic
- More control but requires more work

## 📖 Step-by-Step Setup

### Step 1: 🗃️ Create Models and Migrations

```bash
# Create models
php artisan make:model Admin -m
php artisan make:model Shop -m
php artisan make:model Delivery -m
```

### Step 2: ⚙️ Configure Guards in `config/auth.php`

```php
'guards' => [
    'web' => [
        'driver' => 'session',
        'provider' => 'users',
    ],
    
    'admin' => [
        'driver' => 'session',
        'provider' => 'admins',
    ],
    
    'shop' => [
        'driver' => 'session',
        'provider' => 'shops',
    ],
    
    'delivery' => [
        'driver' => 'session',
        'provider' => 'deliveries',
    ],
],

'providers' => [
    'users' => [
        'driver' => 'eloquent',
        'model' => App\Models\User::class,
    ],
    
    'admins' => [
        'driver' => 'eloquent',
        'model' => App\Models\Admin::class,
    ],
    
    'shops' => [
        'driver' => 'eloquent',
        'model' => App\Models\Shop::class,
    ],
    
    'deliveries' => [
        'driver' => 'eloquent',
        'model' => App\Models\Delivery::class,
    ],
],
```

### Step 3: 🛡️ Create Middleware

```bash
php artisan make:middleware AdminAuth
php artisan make:middleware ShopAuth
php artisan make:middleware DeliveryAuth
```

### Step 4: 🎮 Create Controllers

```bash
php artisan make:controller Admin/AuthController
php artisan make:controller Shop/AuthController
php artisan make:controller Delivery/AuthController
```

## 📝 Configuration Examples

### 🗄️ Database Migration Example (Admin)

```php
// database/migrations/create_admins_table.php
Schema::create('admins', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->string('email')->unique();
    $table->timestamp('email_verified_at')->nullable();
    $table->string('password');
    $table->string('role')->default('admin');
    $table->boolean('is_active')->default(true);
    $table->rememberToken();
    $table->timestamps();
});
```

### 🏷️ Model Example (Admin)

```php
// app/Models/Admin.php
<?php

namespace App\Models;

use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Notifications\Notifiable;

class Admin extends Authenticatable
{
    use HasFactory, Notifiable;

    protected $guard = 'admin';

    protected $fillable = [
        'name',
        'email', 
        'password',
        'role',
        'is_active'
    ];

    protected $hidden = [
        'password',
        'remember_token',
    ];
}
```

## 💻 Code Examples

### 🔐 Login Controller Example

```php
// app/Http/Controllers/Admin/AuthController.php
<?php

namespace App\Http\Controllers\Admin;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\Hash;
use App\Models\Admin;

class AuthController extends Controller
{
    public function showLoginForm()
    {
        return view('admin.auth.login');
    }

    public function login(Request $request)
    {
        $credentials = $request->validate([
            'email' => 'required|email',
            'password' => 'required'
        ]);

        if (Auth::guard('admin')->attempt($credentials)) {
            return redirect()->intended('/admin/dashboard');
        }

        return back()->withErrors([
            'email' => 'Invalid credentials'
        ]);
    }

    public function logout()
    {
        Auth::guard('admin')->logout();
        return redirect('/admin/login');
    }
}
```

### 🛡️ Middleware Example

```php
// app/Http/Middleware/AdminAuth.php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;

class AdminAuth
{
    public function handle(Request $request, Closure $next)
    {
        if (!Auth::guard('admin')->check()) {
            return redirect('/admin/login');
        }

        return $next($request);
    }
}
```

### 🛣️ Routes Example

```php
// routes/web.php

// 👤 User Routes
Route::prefix('user')->group(function () {
    Route::get('/login', [UserAuthController::class, 'showLoginForm']);
    Route::post('/login', [UserAuthController::class, 'login']);
    
    Route::middleware('auth:web')->group(function () {
        Route::get('/dashboard', [UserController::class, 'dashboard']);
        Route::post('/logout', [UserAuthController::class, 'logout']);
    });
});

// 👨‍💼 Admin Routes  
Route::prefix('admin')->group(function () {
    Route::get('/login', [AdminAuthController::class, 'showLoginForm']);
    Route::post('/login', [AdminAuthController::class, 'login']);
    
    Route::middleware('auth:admin')->group(function () {
        Route::get('/dashboard', [AdminController::class, 'dashboard']);
        Route::post('/logout', [AdminAuthController::class, 'logout']);
    });
});

// 🏪 Shop Routes
Route::prefix('shop')->group(function () {
    Route::get('/login', [ShopAuthController::class, 'showLoginForm']);
    Route::post('/login', [ShopAuthController::class, 'login']);
    
    Route::middleware('auth:shop')->group(function () {
        Route::get('/dashboard', [ShopController::class, 'dashboard']);
        Route::post('/logout', [ShopAuthController::class, 'logout']);
    });
});

// 🚚 Delivery Routes
Route::prefix('delivery')->group(function () {
    Route::get('/login', [DeliveryAuthController::class, 'showLoginForm']);
    Route::post('/login', [DeliveryAuthController::class, 'login']);
    
    Route::middleware('auth:delivery')->group(function () {
        Route::get('/dashboard', [DeliveryController::class, 'dashboard']);
        Route::post('/logout', [DeliveryAuthController::class, 'logout']);
    });
});
```

### 🎨 Blade Template Example

```php
<!-- resources/views/admin/auth/login.blade.php -->
<!DOCTYPE html>
<html>
<head>
    <title>🔐 Admin Login</title>
</head>
<body>
    <div class="login-container">
        <h2>👨‍💼 Admin Login</h2>
        
        @if($errors->any())
            <div class="alert alert-danger">
                {{ $errors->first() }}
            </div>
        @endif

        <form method="POST" action="{{ route('admin.login') }}">
            @csrf
            
            <div class="form-group">
                <label>📧 Email:</label>
                <input type="email" name="email" required>
            </div>
            
            <div class="form-group">
                <label>🔒 Password:</label>
                <input type="password" name="password" required>
            </div>
            
            <button type="submit">🚀 Login</button>
        </form>
    </div>
</body>
</html>
```

## 🎯 Best Practices

### 🔒 Security Practices
- ✅ Use strong password policies
- ✅ Implement rate limiting on login attempts
- ✅ Use HTTPS in production
- ✅ Validate and sanitize all inputs
- ✅ Implement proper session management

### 🗂️ Code Organization
- ✅ Separate controllers by user type
- ✅ Use consistent naming conventions
- ✅ Group routes logically
- ✅ Create dedicated middleware for each guard
- ✅ Use form requests for validation

### 🎨 User Experience
- ✅ Design distinct interfaces for each user type
- ✅ Provide clear navigation between sections
- ✅ Implement proper error handling
- ✅ Add logout functionality
- ✅ Show appropriate success/error messages

### 📊 Database Design
- ✅ Use separate tables for each user type
- ✅ Implement proper indexing
- ✅ Add soft deletes where appropriate
- ✅ Use migrations for schema changes
- ✅ Follow Laravel naming conventions

## 🐛 Troubleshooting

### Common Issues:

#### ❌ **Guard Not Working**
```bash
# Clear config cache
php artisan config:clear
php artisan config:cache
```

#### ❌ **Middleware Not Applied**
```php
// Register middleware in app/Http/Kernel.php
protected $routeMiddleware = [
    'admin' => \App\Http\Middleware\AdminAuth::class,
    'shop' => \App\Http\Middleware\ShopAuth::class,
];
```

#### ❌ **Session Issues**
```bash
# Clear all caches
php artisan cache:clear
php artisan session:clear
php artisan view:clear
```

#### ❌ **Route Not Found**
```bash
# Clear route cache
php artisan route:clear
php artisan route:cache
```

### 🔍 Debug Commands:

```bash
# List all routes
php artisan route:list

# Check authentication status
Auth::guard('admin')->check()

# Get current user
Auth::guard('admin')->user()
```

## 🧪 Testing Multi-Auth

### Unit Test Example:

```php
// tests/Feature/AdminAuthTest.php
class AdminAuthTest extends TestCase
{
    public function test_admin_can_login()
    {
        $admin = Admin::factory()->create([
            'email' => 'admin@test.com',
            'password' => Hash::make('password')
        ]);

        $response = $this->post('/admin/login', [
            'email' => 'admin@test.com',
            'password' => 'password'
        ]);

        $response->assertRedirect('/admin/dashboard');
        $this->assertAuthenticatedAs($admin, 'admin');
    }
}
```

## 📚 Additional Resources

- 📖 [Laravel Authentication Documentation](https://laravel.com/docs/authentication)
- 🎥 [Laravel Multi-Auth Tutorial Videos](https://laracasts.com)
- 💬 [Laravel Community Forums](https://laravel.io)
- 🐙 [Laravel GitHub Repository](https://github.com/laravel/laravel)

---

## 🎉 Conclusion

Multi-authentication in Laravel provides a robust way to handle different user types in your application. By following this guide, you can implement a secure, maintainable, and user-friendly authentication system that serves various user roles effectively.

Remember to always test your authentication flows thoroughly and keep security as a top priority! 🔐✨