# 🔐 Laravel API Authentication Guide

## 📋 Table of Contents
- [Overview](#overview)
- [What is API Authentication?](#what-is-api-authentication)
- [Authentication Methods Comparison](#authentication-methods-comparison)
- [Laravel Sanctum](#laravel-sanctum)
- [Laravel Passport (OAuth2)](#laravel-passport-oauth2)
- [JWT Authentication](#jwt-authentication)
- [Security Best Practices](#security-best-practices)
- [Implementation Examples](#implementation-examples)
- [Testing Authentication](#testing-authentication)
- [Troubleshooting](#troubleshooting)
- [Performance Considerations](#performance-considerations)

## 🎯 Overview

This comprehensive guide covers the three main API authentication methods in Laravel: Sanctum, Passport, and JWT. Each method has its own use cases, advantages, and implementation strategies.

## 🔍 What is API Authentication?

API Authentication is the process of verifying the identity of users or applications accessing your API endpoints. It ensures that only authorized entities can access protected resources.

### Key Concepts:
- 🎫 **Tokens**: Digital credentials for API access
- 🔒 **Guards**: Authentication mechanisms in Laravel
- 🛡️ **Middleware**: Protection layer for routes
- ⏰ **Expiration**: Token lifecycle management
- 🔄 **Refresh**: Token renewal mechanisms

## ⚖️ Authentication Methods Comparison

| Feature | 🛡️ Sanctum | 🎫 Passport | 🔑 JWT |
|---------|-------------|-------------|--------|
| **Best For** | Simple SPAs, Mobile Apps | OAuth2, Third-party integrations | Stateless APIs |
| **Token Type** | Personal Access Tokens | Bearer Tokens | JSON Web Tokens |
| **Database Storage** | ✅ Yes | ✅ Yes | ❌ No (Stateless) |
| **OAuth2 Support** | ❌ No | ✅ Full OAuth2 | ❌ No |
| **Setup Complexity** | 🟢 Simple | 🟡 Moderate | 🟠 Complex |
| **Performance** | 🟢 Good | 🟡 Moderate | 🟢 Excellent |
| **Third-party Apps** | ❌ Limited | ✅ Full Support | ❌ Limited |
| **Refresh Tokens** | ✅ Manual | ✅ Automatic | ✅ Manual |
| **Revocation** | ✅ Easy | ✅ Easy | 🟡 Complex |

## 🛡️ Laravel Sanctum

### 📖 Overview
Laravel Sanctum provides a lightweight authentication system for SPAs, mobile applications, and simple token-based APIs.

### ✅ Advantages
- 🚀 **Simple Setup**: Easy to install and configure
- 📱 **Mobile Friendly**: Perfect for mobile applications
- 🔒 **Secure**: Built-in CSRF protection
- ⚡ **Performance**: Lightweight and fast
- 🛠️ **Flexible**: Supports both session and token authentication

### ❌ Disadvantages
- 🚫 **No OAuth2**: Limited third-party integration
- 📊 **Basic Features**: Less advanced than Passport
- 🔄 **Manual Refresh**: No automatic token refresh

### 🚀 Installation & Setup

```bash
# Install Sanctum
composer require laravel/sanctum

# Publish configuration
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"

# Run migrations
php artisan migrate
```

### ⚙️ Configuration

```php
// config/sanctum.php
<?php

return [
    /*
    |--------------------------------------------------------------------------
    | Stateful Domains
    |--------------------------------------------------------------------------
    */
    'stateful' => explode(',', env('SANCTUM_STATEFUL_DOMAINS', sprintf(
        '%s%s',
        'localhost,localhost:3000,localhost:8080,127.0.0.1,127.0.0.1:8000,::1',
        env('APP_URL') ? ','.parse_url(env('APP_URL'), PHP_URL_HOST) : ''
    ))),

    /*
    |--------------------------------------------------------------------------
    | Sanctum Guards
    |--------------------------------------------------------------------------
    */
    'guard' => ['web'],

    /*
    |--------------------------------------------------------------------------
    | Expiration Minutes
    |--------------------------------------------------------------------------
    */
    'expiration' => 525600, // 1 year

    /*
    |--------------------------------------------------------------------------
    | Sanctum Middleware
    |--------------------------------------------------------------------------
    */
    'middleware' => [
        'verify_csrf_token' => App\Http\Middleware\VerifyCsrfToken::class,
        'encrypt_cookies' => App\Http\Middleware\EncryptCookies::class,
    ],
];
```

### 👤 User Model Setup

```php
// app/Models/User.php
<?php

namespace App\Models;

use Laravel\Sanctum\HasApiTokens;
use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Notifications\Notifiable;

class User extends Authenticatable
{
    use HasApiTokens, HasFactory, Notifiable;

    protected $fillable = [
        'name',
        'email',
        'password',
    ];

    protected $hidden = [
        'password',
        'remember_token',
    ];

    protected $casts = [
        'email_verified_at' => 'datetime',
        'password' => 'hashed',
    ];

    /**
     * 🎯 Create token with abilities
     */
    public function createApiToken($name = 'api-token', $abilities = ['*'])
    {
        return $this->createToken($name, $abilities);
    }
}
```

### 🎮 Authentication Controller

```php
// app/Http/Controllers/API/SanctumAuthController.php
<?php

namespace App\Http\Controllers\API;

use App\Http\Controllers\Controller;
use App\Models\User;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;
use Illuminate\Validation\ValidationException;
use App\Http\Requests\API\LoginRequest;
use App\Http\Requests\API\RegisterRequest;

class SanctumAuthController extends Controller
{
    /**
     * 🚀 User Registration
     */
    public function register(RegisterRequest $request)
    {
        $user = User::create([
            'name' => $request->name,
            'email' => $request->email,
            'password' => Hash::make($request->password),
        ]);

        $token = $user->createToken('auth-token')->plainTextToken;

        return response()->json([
            'success' => true,
            'message' => '🎉 Registration successful',
            'data' => [
                'user' => $user,
                'token' => $token,
                'token_type' => 'Bearer',
            ]
        ], 201);
    }

    /**
     * 🔐 User Login
     */
    public function login(LoginRequest $request)
    {
        $user = User::where('email', $request->email)->first();

        if (!$user || !Hash::check($request->password, $user->password)) {
            throw ValidationException::withMessages([
                'email' => ['🚫 The provided credentials are incorrect.'],
            ]);
        }

        // Revoke existing tokens (optional)
        $user->tokens()->delete();

        $token = $user->createToken('auth-token')->plainTextToken;

        return response()->json([
            'success' => true,
            'message' => '✅ Login successful',
            'data' => [
                'user' => $user,
                'token' => $token,
                'token_type' => 'Bearer',
            ]
        ]);
    }

    /**
     * 👤 Get User Profile
     */
    public function profile(Request $request)
    {
        return response()->json([
            'success' => true,
            'message' => '✅ Profile retrieved successfully',
            'data' => [
                'user' => $request->user(),
                'current_token' => $request->user()->currentAccessToken(),
            ]
        ]);
    }

    /**
     * 🚪 User Logout
     */
    public function logout(Request $request)
    {
        // Delete current token
        $request->user()->currentAccessToken()->delete();

        return response()->json([
            'success' => true,
            'message' => '👋 Logged out successfully'
        ]);
    }

    /**
     * 🗑️ Logout from all devices
     */
    public function logoutAll(Request $request)
    {
        // Delete all tokens
        $request->user()->tokens()->delete();

        return response()->json([
            'success' => true,
            'message' => '👋 Logged out from all devices'
        ]);
    }
}
```

### 🛣️ Routes Configuration

```php
// routes/api.php
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\API\SanctumAuthController;

// 🔓 Public routes
Route::prefix('sanctum')->group(function () {
    Route::post('register', [SanctumAuthController::class, 'register']);
    Route::post('login', [SanctumAuthController::class, 'login']);
});

// 🔒 Protected routes
Route::middleware('auth:sanctum')->prefix('sanctum')->group(function () {
    Route::get('profile', [SanctumAuthController::class, 'profile']);
    Route::post('logout', [SanctumAuthController::class, 'logout']);
    Route::post('logout-all', [SanctumAuthController::class, 'logoutAll']);
});
```

## 🎫 Laravel Passport (OAuth2)

### 📖 Overview
Laravel Passport provides a full OAuth2 server implementation for Laravel applications, perfect for APIs that need to support third-party applications.

### ✅ Advantages
- 🌐 **OAuth2 Compliant**: Full OAuth2 implementation
- 🔗 **Third-party Support**: Perfect for external integrations
- 🔄 **Refresh Tokens**: Automatic token refresh
- 🛡️ **Scopes**: Granular permission control
- 🏢 **Enterprise Ready**: Suitable for large applications

### ❌ Disadvantages
- ⚡ **Complex Setup**: More configuration required
- 📊 **Heavy**: More overhead than Sanctum
- 🗄️ **Database Intensive**: More database tables and queries

### 🚀 Installation & Setup

```bash
# Install Passport
composer require laravel/passport

# Run migrations
php artisan migrate

# Install Passport
php artisan passport:install

# Generate keys
php artisan passport:keys
```

### ⚙️ Configuration

```php
// config/auth.php
'guards' => [
    'api' => [
        'driver' => 'passport',
        'provider' => 'users',
    ],
],

// app/Providers/AuthServiceProvider.php
<?php

namespace App\Providers;

use Laravel\Passport\Passport;
use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;

class AuthServiceProvider extends ServiceProvider
{
    protected $policies = [
        // 'App\Models\Model' => 'App\Policies\ModelPolicy',
    ];

    public function boot()
    {
        $this->registerPolicies();

        // 🎯 Passport configuration
        Passport::tokensExpireIn(now()->addDays(15));
        Passport::refreshTokensExpireIn(now()->addDays(30));
        Passport::personalAccessTokensExpireIn(now()->addMonths(6));

        // 🔒 Token scopes
        Passport::tokensCan([
            'user-read' => 'Read user information',
            'user-write' => 'Modify user information',
            'admin' => 'Administrative access',
        ]);

        // 🎯 Default scope
        Passport::setDefaultScope(['user-read']);
    }
}
```

### 👤 User Model Setup

```php
// app/Models/User.php
<?php

namespace App\Models;

use Laravel\Passport\HasApiTokens;
use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Notifications\Notifiable;

class User extends Authenticatable
{
    use HasApiTokens, HasFactory, Notifiable;

    protected $fillable = [
        'name',
        'email',
        'password',
    ];

    protected $hidden = [
        'password',
        'remember_token',
    ];

    protected $casts = [
        'email_verified_at' => 'datetime',
        'password' => 'hashed',
    ];

    /**
     * 🎯 Get user scopes
     */
    public function getScopes()
    {
        return $this->is_admin ? ['admin', 'user-read', 'user-write'] : ['user-read'];
    }
}
```

### 🎮 Authentication Controller

```php
// app/Http/Controllers/API/PassportAuthController.php
<?php

namespace App\Http\Controllers\API;

use App\Http\Controllers\Controller;
use App\Models\User;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;
use Laravel\Passport\Client;
use App\Http\Requests\API\LoginRequest;
use App\Http\Requests\API\RegisterRequest;

class PassportAuthController extends Controller
{
    /**
     * 🚀 User Registration
     */
    public function register(RegisterRequest $request)
    {
        $user = User::create([
            'name' => $request->name,
            'email' => $request->email,
            'password' => Hash::make($request->password),
        ]);

        $token = $user->createToken('auth-token', $user->getScopes())->accessToken;

        return response()->json([
            'success' => true,
            'message' => '🎉 Registration successful',
            'data' => [
                'user' => $user,
                'access_token' => $token,
                'token_type' => 'Bearer',
            ]
        ], 201);
    }

    /**
     * 🔐 User Login with OAuth2
     */
    public function login(LoginRequest $request)
    {
        if (!auth()->attempt($request->only('email', 'password'))) {
            return response()->json([
                'success' => false,
                'message' => '🚫 Invalid credentials'
            ], 401);
        }

        $user = auth()->user();
        $token = $user->createToken('auth-token', $user->getScopes());

        return response()->json([
            'success' => true,
            'message' => '✅ Login successful',
            'data' => [
                'user' => $user,
                'access_token' => $token->accessToken,
                'refresh_token' => $token->refreshToken,
                'token_type' => 'Bearer',
                'expires_at' => $token->token->expires_at,
            ]
        ]);
    }

    /**
     * 🔄 Refresh Access Token
     */
    public function refresh(Request $request)
    {
        $request->validate([
            'refresh_token' => 'required',
        ]);

        // Custom refresh logic
        $client = Client::where('password_client', 1)->first();
        
        $response = Http::post(url('/oauth/token'), [
            'grant_type' => 'refresh_token',
            'refresh_token' => $request->refresh_token,
            'client_id' => $client->id,
            'client_secret' => $client->secret,
        ]);

        return response()->json([
            'success' => true,
            'message' => '🔄 Token refreshed successfully',
            'data' => $response->json()
        ]);
    }

    /**
     * 👤 Get User Profile
     */
    public function profile(Request $request)
    {
        return response()->json([
            'success' => true,
            'message' => '✅ Profile retrieved successfully',
            'data' => [
                'user' => $request->user(),
                'scopes' => $request->user()->token()->scopes,
            ]
        ]);
    }

    /**
     * 🚪 User Logout
     */
    public function logout(Request $request)
    {
        $request->user()->token()->revoke();

        return response()->json([
            'success' => true,
            'message' => '👋 Logged out successfully'
        ]);
    }
}
```

## 🔑 JWT Authentication

### 📖 Overview
JWT (JSON Web Token) is a stateless authentication method that stores user information in the token itself, eliminating the need for database lookups.

### ✅ Advantages
- ⚡ **High Performance**: No database lookups required
- 🌐 **Stateless**: Self-contained tokens
- 🚀 **Scalable**: Perfect for microservices
- 🔒 **Secure**: Cryptographically signed
- 🌍 **Cross-domain**: Works across different domains

### ❌ Disadvantages
- 🔄 **Token Revocation**: Difficult to revoke tokens
- 📊 **Token Size**: Larger than simple tokens
- ⚙️ **Complex Setup**: More configuration required
- 🛡️ **Security Concerns**: Token stealing risks

### 🚀 Installation & Setup

```bash
# Install JWT package
composer require tymon/jwt-auth

# Publish configuration
php artisan vendor:publish --provider="Tymon\JWTAuth\Providers\LaravelServiceProvider"

# Generate secret key
php artisan jwt:secret
```

### ⚙️ Configuration

```php
// config/auth.php
'guards' => [
    'api' => [
        'driver' => 'jwt',
        'provider' => 'users',
    ],
],

// config/jwt.php
<?php

return [
    /*
    |--------------------------------------------------------------------------
    | JWT Authentication Secret
    |--------------------------------------------------------------------------
    */
    'secret' => env('JWT_SECRET'),

    /*
    |--------------------------------------------------------------------------
    | JWT time to live
    |--------------------------------------------------------------------------
    */
    'ttl' => env('JWT_TTL', 60), // minutes

    /*
    |--------------------------------------------------------------------------
    | Refresh time to live
    |--------------------------------------------------------------------------
    */
    'refresh_ttl' => env('JWT_REFRESH_TTL', 20160), // minutes

    /*
    |--------------------------------------------------------------------------
    | JWT hashing algorithm
    |--------------------------------------------------------------------------
    */
    'algo' => env('JWT_ALGO', 'HS256'),

    /*
    |--------------------------------------------------------------------------
    | Required Claims
    |--------------------------------------------------------------------------
    */
    'required_claims' => [
        'iss',
        'iat',
        'exp',
        'nbf',
        'sub',
        'jti',
    ],

    /*
    |--------------------------------------------------------------------------
    | Persistent Claims
    |--------------------------------------------------------------------------
    */
    'persistent_claims' => [
        // 'foo',
        // 'bar',
    ],

    /*
    |--------------------------------------------------------------------------
    | Blacklist Enabled
    |--------------------------------------------------------------------------
    */
    'blacklist_enabled' => env('JWT_BLACKLIST_ENABLED', true),

    /*
    |--------------------------------------------------------------------------
    | Blacklist grace period
    |--------------------------------------------------------------------------
    */
    'blacklist_grace_period' => env('JWT_BLACKLIST_GRACE_PERIOD', 0),
];
```

### 👤 User Model Setup

```php
// app/Models/User.php
<?php

namespace App\Models;

use Tymon\JWTAuth\Contracts\JWTSubject;
use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Notifications\Notifiable;

class User extends Authenticatable implements JWTSubject
{
    use HasFactory, Notifiable;

    protected $fillable = [
        'name',
        'email',
        'password',
    ];

    protected $hidden = [
        'password',
        'remember_token',
    ];

    protected $casts = [
        'email_verified_at' => 'datetime',
        'password' => 'hashed',
    ];

    /**
     * 🎯 Get JWT identifier
     */
    public function getJWTIdentifier()
    {
        return $this->getKey();
    }

    /**
     * 🔒 Get JWT custom claims
     */
    public function getJWTCustomClaims()
    {
        return [
            'name' => $this->name,
            'email' => $this->email,
            'role' => $this->role ?? 'user',
            'permissions' => $this->permissions ?? [],
        ];
    }
}
```

### 🎮 Authentication Controller

```php
// app/Http/Controllers/API/JWTAuthController.php
<?php

namespace App\Http\Controllers\API;

use App\Http\Controllers\Controller;
use App\Models\User;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;
use Tymon\JWTAuth\Facades\JWTAuth;
use Tymon\JWTAuth\Exceptions\JWTException;
use App\Http\Requests\API\LoginRequest;
use App\Http\Requests\API\RegisterRequest;

class JWTAuthController extends Controller
{
    /**
     * 🚀 User Registration
     */
    public function register(RegisterRequest $request)
    {
        $user = User::create([
            'name' => $request->name,
            'email' => $request->email,
            'password' => Hash::make($request->password),
        ]);

        $token = JWTAuth::fromUser($user);

        return response()->json([
            'success' => true,
            'message' => '🎉 Registration successful',
            'data' => [
                'user' => $user,
                'access_token' => $token,
                'token_type' => 'bearer',
                'expires_in' => auth('api')->factory()->getTTL() * 60
            ]
        ], 201);
    }

    /**
     * 🔐 User Login
     */
    public function login(LoginRequest $request)
    {
        $credentials = $request->only('email', 'password');

        try {
            if (!$token = JWTAuth::attempt($credentials)) {
                return response()->json([
                    'success' => false,
                    'message' => '🚫 Invalid credentials'
                ], 401);
            }
        } catch (JWTException $e) {
            return response()->json([
                'success' => false,
                'message' => '💥 Could not create token'
            ], 500);
        }

        return response()->json([
            'success' => true,
            'message' => '✅ Login successful',
            'data' => [
                'user' => auth('api')->user(),
                'access_token' => $token,
                'token_type' => 'bearer',
                'expires_in' => auth('api')->factory()->getTTL() * 60
            ]
        ]);
    }

    /**
     * 👤 Get User Profile
     */
    public function profile()
    {
        try {
            if (!$user = JWTAuth::parseToken()->authenticate()) {
                return response()->json([
                    'success' => false,
                    'message' => '🚫 User not found'
                ], 404);
            }
        } catch (JWTException $e) {
            return response()->json([
                'success' => false,
                'message' => '🚫 Invalid token'
            ], 400);
        }

        return response()->json([
            'success' => true,
            'message' => '✅ Profile retrieved successfully',
            'data' => [
                'user' => $user,
                'token_payload' => JWTAuth::getPayload(),
            ]
        ]);
    }

    /**
     * 🔄 Refresh Token
     */
    public function refresh()
    {
        try {
            $token = JWTAuth::refresh();
            
            return response()->json([
                'success' => true,
                'message' => '🔄 Token refreshed successfully',
                'data' => [
                    'access_token' => $token,
                    'token_type' => 'bearer',
                    'expires_in' => auth('api')->factory()->getTTL() * 60
                ]
            ]);
        } catch (JWTException $e) {
            return response()->json([
                'success' => false,
                'message' => '💥 Token cannot be refreshed'
            ], 500);
        }
    }

    /**
     * 🚪 User Logout
     */
    public function logout()
    {
        try {
            JWTAuth::invalidate();
            
            return response()->json([
                'success' => true,
                'message' => '👋 Successfully logged out'
            ]);
        } catch (JWTException $e) {
            return response()->json([
                'success' => false,
                'message' => '💥 Failed to logout'
            ], 500);
        }
    }
}
```

## 🛡️ Security Best Practices

### 🔒 Token Security

```php
// app/Http/Middleware/TokenValidation.php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Carbon\Carbon;

class TokenValidation
{
    public function handle(Request $request, Closure $next)
    {
        $token = $request->bearerToken();

        if (!$token) {
            return response()->json([
                'success' => false,
                'message' => '🚫 Token required'
            ], 401);
        }

        // Check token format
        if (!$this->isValidTokenFormat($token)) {
            return response()->json([
                'success' => false,
                'message' => '🚫 Invalid token format'
            ], 401);
        }

        // Add security headers
        $response = $next($request);
        $response->headers->set('X-Content-Type-Options', 'nosniff');
        $response->headers->set('X-Frame-Options', 'DENY');
        $response->headers->set('X-XSS-Protection', '1; mode=block');

        return $response;
    }

    private function isValidTokenFormat($token)
    {
        // Basic token format validation
        return strlen($token) >= 32 && ctype_alnum(str_replace(['-', '_'], '', $token));
    }
}
```

### 🔐 Rate Limiting for Auth Endpoints

```php
// app/Providers/RouteServiceProvider.php
use Illuminate\Cache\RateLimiting\Limit;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\RateLimiter;

public function boot()
{
    RateLimiter::for('auth', function (Request $request) {
        return [
            Limit::perMinute(5)->by($request->ip())->response(function () {
                return response()->json([
                    'success' => false,
                    'message' => '🚦 Too many login attempts. Try again later.'
                ], 429);
            }),
            Limit::perDay(100)->by($request->ip()),
        ];
    });
}
```

## 🧪 Testing Authentication

### 🔬 Sanctum Testing

```php
// tests/Feature/SanctumAuthTest.php
<?php

namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;
use App\Models\User;
use Laravel\Sanctum\Sanctum;

class SanctumAuthTest extends TestCase
{
    use RefreshDatabase;

    public function test_user_can_register()
    {
        $response = $this->postJson('/api/sanctum/register', [
            'name' => 'Test User',
            'email' => 'test@example.com',
            'password' => 'password123',
            'password_confirmation' => 'password123'
        ]);

        $response->assertStatus(201)
                ->assertJsonStructure([
                    'success',
                    'message',
                    'data' => [
                        'user' => ['id', 'name', 'email'],
                        'token',
                        'token_type'
                    ]
                ]);

        $this->assertDatabaseHas('users', [
            'email' => 'test@example.com'
        ]);
    }

    public function test_user_can_login()
    {
        $user = User::factory()->create([
            'email' => 'test@example.com',
            'password' => bcrypt('password123')
        ]);

        $response = $this->postJson('/api/sanctum/login', [
            'email' => 'test@example.com',
            'password' => 'password123'
        ]);

        $response->assertStatus(200)
                ->assertJsonStructure([
                    'success',
                    'message',
                    'data' => [
                        'user',
                        'token',
                        'token_type'
                    ]
                ]);
    }

    public function test_authenticated_user_can_access_profile()
    {
        $user = User::factory()->create();
        Sanctum::actingAs($user);

        $response = $this->getJson('/api/sanctum/profile');

        $response->assertStatus(200)
                ->assertJson([
                    'success' => true,
                    'data' => [
                        'user' => [
                            'id' => $user->id,
                            'email' => $user->email
                        ]
                    ]
                ]);
    }
}
```

### 🔬 JWT Testing

```php
// tests/Feature/JWTAuthTest.php
<?php

namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;
use App\Models\User;
use Tymon\JWTAuth\Facades\JWTAuth;

class JWTAuthTest extends TestCase
{
    use RefreshDatabase;

    public function test_user_can_login_and_get_jwt_token()
    {
        $user = User::factory()->create([
            'email' => 'test@example.com',
            'password' => bcrypt('password123')
        ]);

        $response = $this->postJson('/api/jwt/login', [
            'email' => 'test@example.com',
            'password' => 'password123'
        ]);

        $response->assertStatus(200)
                ->assertJsonStructure([
                    'success',
                    'message',
                    'data' => [
                        'user',
                        'access_token',
                        'token_type',
                        'expires_in'
                    ]
                ]);

        $token = $response->json('data.access_token');
        $this->assertNotEmpty($token);
    }

    public function test_user_can_access_protected_route_with_jwt()
    {
        $user = User::factory()->create();
        $token = JWTAuth::fromUser($user);

        $response = $this->withHeaders([
            'Authorization' => 'Bearer ' . $token,
        ])->getJson('/api/jwt/profile');

        $response->assertStatus(200)
                ->assertJson([
                    'success' => true,
                    'data' => [
                        'user' => [
                            'id' => $user->id,
                            'email' => $user->email
                        ]
                    ]
                ]);
    }

    public function test_jwt_token_can_be_refreshed()
    {
        $user = User::factory()->create();
        $token = JWTAuth::fromUser($user);

        $response = $this->withHeaders([
            'Authorization' => 'Bearer ' . $token,
        ])->postJson('/api/jwt/refresh');

        $response->assertStatus(200)
                ->assertJsonStructure([
                    'success',
                    'message',
                    'data' => [
                        'access_token',
                        'token_type',
                        'expires_in'
                    ]
                ]);
    }

    public function test_invalid_jwt_token_returns_error()
    {
        $response = $this->withHeaders([
            'Authorization' => 'Bearer invalid-token',
        ])->getJson('/api/jwt/profile');

        $response->assertStatus(400)
                ->assertJson([
                    'success' => false
                ]);
    }
}
```

## 🛠️ Troubleshooting

### 🚨 Common Issues & Solutions

#### 🔴 **Sanctum Issues**

| Problem | Solution |
|---------|----------|
| **CORS Issues** | Configure `config/cors.php` properly |
| **Token Not Working** | Check middleware configuration |
| **Session Issues** | Clear cache: `php artisan config:clear` |

```php
// config/cors.php
<?php

return [
    'paths' => ['api/*', 'sanctum/csrf-cookie'],
    'allowed_methods' => ['*'],
    'allowed_origins' => ['http://localhost:3000', 'http://localhost:8080'],
    'allowed_origins_patterns' => [],
    'allowed_headers' => ['*'],
    'exposed_headers' => [],
    'max_age' => 0,
    'supports_credentials' => true,
];
```

#### 🟠 **Passport Issues**

| Problem | Solution |
|---------|----------|
| **Keys Not Found** | Run `php artisan passport:keys` |
| **Client Not Found** | Run `php artisan passport:client --personal` |
| **Token Expired** | Check token expiration settings |

```bash
# 🔧 Passport Troubleshooting Commands
php artisan passport:keys --force
php artisan passport:client --personal --name="Personal Access Client"
php artisan config:clear
php artisan route:clear
```

#### 🟡 **JWT Issues**

| Problem | Solution |
|---------|----------|
| **Secret Not Set** | Run `php artisan jwt:secret` |
| **Token Invalid** | Check token format and expiration |
| **Blacklist Issues** | Clear token blacklist |

```bash
# 🔧 JWT Troubleshooting Commands
php artisan jwt:secret --force
php artisan config:clear
php artisan cache:clear
```

### 🔍 Debug Helper Methods

```php
// app/Http/Controllers/API/DebugController.php
<?php

namespace App\Http\Controllers\API;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;
use Laravel\Sanctum\PersonalAccessToken;
use Tymon\JWTAuth\Facades\JWTAuth;

class DebugController extends Controller
{
    /**
     * 🔍 Debug Sanctum Token
     */
    public function debugSanctum(Request $request)
    {
        $token = $request->bearerToken();
        
        if (!$token) {
            return response()->json(['error' => 'No token provided']);
        }

        $accessToken = PersonalAccessToken::findToken($token);
        
        return response()->json([
            'token_exists' => $accessToken ? true : false,
            'token_id' => $accessToken?->id,
            'tokenable_type' => $accessToken?->tokenable_type,
            'tokenable_id' => $accessToken?->tokenable_id,
            'abilities' => $accessToken?->abilities,
            'created_at' => $accessToken?->created_at,
            'expires_at' => $accessToken?->expires_at,
        ]);
    }

    /**
     * 🔍 Debug JWT Token
     */
    public function debugJWT(Request $request)
    {
        try {
            $token = JWTAuth::parseToken();
            $payload = $token->getPayload();
            
            return response()->json([
                'token_valid' => true,
                'user_id' => $payload->get('sub'),
                'issued_at' => date('Y-m-d H:i:s', $payload->get('iat')),
                'expires_at' => date('Y-m-d H:i:s', $payload->get('exp')),
                'custom_claims' => $payload->get('custom_claims', []),
            ]);
        } catch (\Exception $e) {
            return response()->json([
                'token_valid' => false,
                'error' => $e->getMessage(),
            ]);
        }
    }
}
```

## ⚡ Performance Considerations

### 🚀 Optimization Strategies

#### 📊 **Database Optimization**

```php
// app/Models/User.php - Optimize token queries
public function activeTokens()
{
    return $this->tokens()
                ->where('expires_at', '>', now())
                ->orWhereNull('expires_at');
}

// Create database indexes
// In migration file:
Schema::table('personal_access_tokens', function (Blueprint $table) {
    $table->index(['tokenable_type', 'tokenable_id']);
    $table->index('expires_at');
});
```

#### 💾 **Caching Strategies**

```php
// app/Services/TokenCacheService.php
<?php

namespace App\Services;

use Illuminate\Support\Facades\Cache;
use Laravel\Sanctum\PersonalAccessToken;

class TokenCacheService
{
    /**
     * 💾 Cache token validation
     */
    public function validateToken(string $token): ?PersonalAccessToken
    {
        $cacheKey = "token_validation:{$token}";
        
        return Cache::remember($cacheKey, 300, function () use ($token) {
            return PersonalAccessToken::findToken($token);
        });
    }

    /**
     * 🗑️ Invalidate token cache
     */
    public function invalidateToken(string $token): void
    {
        Cache::forget("token_validation:{$token}");
    }
}
```

#### 🏃‍♂️ **Performance Benchmarks**

| Method | Requests/Second | Memory Usage | Database Queries |
|--------|----------------|--------------|------------------|
| **Sanctum** | ~2,000 | Medium | 1-2 per request |
| **Passport** | ~800 | High | 3-5 per request |
| **JWT** | ~5,000 | Low | 0 per request |

## 🔄 Token Management

### 🗂️ Token Cleanup Service

```php
// app/Console/Commands/CleanupTokens.php
<?php

namespace App\Console\Commands;

use Illuminate\Console\Command;
use Laravel\Sanctum\PersonalAccessToken;
use Laravel\Passport\Token;

class CleanupTokens extends Command
{
    protected $signature = 'auth:cleanup-tokens';
    protected $description = '🧹 Cleanup expired authentication tokens';

    public function handle()
    {
        // Sanctum token cleanup
        $expiredSanctumTokens = PersonalAccessToken::where('expires_at', '<', now())->count();
        PersonalAccessToken::where('expires_at', '<', now())->delete();
        
        // Passport token cleanup
        $expiredPassportTokens = Token::where('expires_at', '<', now())->count();
        Token::where('expires_at', '<', now())->delete();
        
        $this->info("🧹 Cleaned up {$expiredSanctumTokens} Sanctum tokens");
        $this->info("🧹 Cleaned up {$expiredPassportTokens} Passport tokens");
    }
}

// app/Console/Kernel.php
protected function schedule(Schedule $schedule)
{
    $schedule->command('auth:cleanup-tokens')->daily();
}
```

### 📊 Token Analytics

```php
// app/Services/TokenAnalyticsService.php
<?php

namespace App\Services;

use Laravel\Sanctum\PersonalAccessToken;
use Carbon\Carbon;

class TokenAnalyticsService
{
    /**
     * 📊 Get token statistics
     */
    public function getTokenStats(): array
    {
        return [
            'total_tokens' => PersonalAccessToken::count(),
            'active_tokens' => PersonalAccessToken::where('expires_at', '>', now())
                                                 ->orWhereNull('expires_at')
                                                 ->count(),
            'expired_tokens' => PersonalAccessToken::where('expires_at', '<', now())->count(),
            'tokens_created_today' => PersonalAccessToken::whereDate('created_at', today())->count(),
            'tokens_used_last_hour' => PersonalAccessToken::where('last_used_at', '>', now()->subHour())->count(),
        ];
    }

    /**
     * 👤 Get user token usage
     */
    public function getUserTokenUsage(int $userId): array
    {
        $tokens = PersonalAccessToken::where('tokenable_id', $userId)
                                   ->where('tokenable_type', 'App\Models\User')
                                   ->get();

        return [
            'total_tokens' => $tokens->count(),
            'active_tokens' => $tokens->where('expires_at', '>', now())->count(),
            'most_recent_token' => $tokens->sortByDesc('created_at')->first(),
            'last_used_token' => $tokens->sortByDesc('last_used_at')->first(),
        ];
    }
}
```

## 🎯 Advanced Features

### 🔒 Multi-Tenant Authentication

```php
// app/Models/Tenant.php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Tenant extends Model
{
    protected $fillable = ['name', 'domain', 'database'];

    public function users()
    {
        return $this->hasMany(User::class);
    }
}

// app/Http/Middleware/TenantAuth.php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use App\Models\Tenant;

class TenantAuth
{
    public function handle(Request $request, Closure $next)
    {
        $tenantId = $request->header('X-Tenant-ID') ?? $request->get('tenant_id');
        
        if (!$tenantId) {
            return response()->json([
                'success' => false,
                'message' => '🏢 Tenant ID required'
            ], 400);
        }

        $tenant = Tenant::find($tenantId);
        
        if (!$tenant) {
            return response()->json([
                'success' => false,
                'message' => '🏢 Invalid tenant'
            ], 404);
        }

        // Set tenant context
        app()->instance('tenant', $tenant);
        
        return $next($request);
    }
}
```

### 🎯 Role-Based Token Scopes

```php
// app/Services/ScopeService.php
<?php

namespace App\Services;

class ScopeService
{
    private const ROLE_SCOPES = [
        'admin' => ['*'],
        'manager' => ['users:read', 'users:update', 'reports:read'],
        'user' => ['profile:read', 'profile:update'],
        'guest' => ['profile:read'],
    ];

    /**
     * 🎯 Get scopes for role
     */
    public static function getScopesForRole(string $role): array
    {
        return self::ROLE_SCOPES[$role] ?? ['profile:read'];
    }

    /**
     * 🔍 Check if user has scope
     */
    public static function hasScope(array $userScopes, string $requiredScope): bool
    {
        return in_array('*', $userScopes) || in_array($requiredScope, $userScopes);
    }
}

// Usage in controller
public function createToken(User $user): string
{
    $scopes = ScopeService::getScopesForRole($user->role);
    return $user->createToken('auth-token', $scopes)->plainTextToken;
}
```

## 📚 Integration Examples

### 📱 Mobile App Integration

```dart
// Flutter/Dart example
class ApiService {
  static const String baseUrl = 'https://your-api.com/api';
  static String? authToken;

  // 🔐 Login method
  static Future<Map<String, dynamic>> login(String email, String password) async {
    final response = await http.post(
      Uri.parse('$baseUrl/sanctum/login'),
      headers: {'Content-Type': 'application/json'},
      body: json.encode({
        'email': email,
        'password': password,
      }),
    );

    if (response.statusCode == 200) {
      final data = json.decode(response.body);
      authToken = data['data']['token'];
      return data;
    } else {
      throw Exception('Failed to login');
    }
  }

  // 👤 Get profile
  static Future<Map<String, dynamic>> getProfile() async {
    final response = await http.get(
      Uri.parse('$baseUrl/sanctum/profile'),
      headers: {
        'Authorization': 'Bearer $authToken',
        'Content-Type': 'application/json',
      },
    );

    if (response.statusCode == 200) {
      return json.decode(response.body);
    } else {
      throw Exception('Failed to get profile');
    }
  }
}
```

### 🌐 Frontend Integration

```javascript
// JavaScript/React example
class AuthService {
  constructor() {
    this.baseURL = 'https://your-api.com/api';
    this.token = localStorage.getItem('auth_token');
  }

  // 🔐 Login method
  async login(email, password) {
    try {
      const response = await fetch(`${this.baseURL}/sanctum/login`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({ email, password }),
      });

      const data = await response.json();

      if (data.success) {
        this.token = data.data.token;
        localStorage.setItem('auth_token', this.token);
        return data;
      } else {
        throw new Error(data.message);
      }
    } catch (error) {
      console.error('Login error:', error);
      throw error;
    }
  }

  // 👤 Get profile
  async getProfile() {
    try {
      const response = await fetch(`${this.baseURL}/sanctum/profile`, {
        headers: {
          'Authorization': `Bearer ${this.token}`,
          'Content-Type': 'application/json',
        },
      });

      const data = await response.json();
      return data;
    } catch (error) {
      console.error('Profile error:', error);
      throw error;
    }
  }

  // 🚪 Logout
  async logout() {
    try {
      await fetch(`${this.baseURL}/sanctum/logout`, {
        method: 'POST',
        headers: {
          'Authorization': `Bearer ${this.token}`,
          'Content-Type': 'application/json',
        },
      });

      this.token = null;
      localStorage.removeItem('auth_token');
    } catch (error) {
      console.error('Logout error:', error);
    }
  }
}
```

## 📋 Decision Matrix

### 🤔 Which Authentication Method to Choose?

| Use Case | 🛡️ Sanctum | 🎫 Passport | 🔑 JWT |
|----------|-------------|-------------|--------|
| **Simple SPA** | ✅ Perfect | ❌ Overkill | 🟡 Good |
| **Mobile App** | ✅ Perfect | 🟡 Good | ✅ Perfect |
| **Third-party API** | ❌ Limited | ✅ Perfect | 🟡 Good |
| **Microservices** | 🟡 Good | ❌ Heavy | ✅ Perfect |
| **High Traffic** | 🟡 Good | ❌ Poor | ✅ Perfect |
| **Enterprise** | 🟡 Good | ✅ Perfect | 🟡 Good |
| **Quick Setup** | ✅ Perfect | ❌ Complex | 🟡 Moderate |

## 🔧 Useful Commands

```bash
# 🛡️ Sanctum Commands
composer require laravel/sanctum
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
php artisan migrate

# 🎫 Passport Commands
composer require laravel/passport
php artisan migrate
php artisan passport:install
php artisan passport:keys
php artisan passport:client --personal
php artisan passport:client --password

# 🔑 JWT Commands
composer require tymon/jwt-auth
php artisan vendor:publish --provider="Tymon\JWTAuth\Providers\LaravelServiceProvider"
php artisan jwt:secret

# 🧹 Maintenance Commands
php artisan auth:cleanup-tokens
php artisan sanctum:prune-expired --hours=24
php artisan passport:purge --revoked --expired

# 🔍 Debug Commands
php artisan route:list --name=api
php artisan config:show auth
php artisan tinker
```

## 📚 Additional Resources

- 📖 [Laravel Sanctum Documentation](https://laravel.com/docs/sanctum)
- 🎫 [Laravel Passport Documentation](https://laravel.com/docs/passport)
- 🔑 [JWT-Auth Package Documentation](https://jwt-auth.readthedocs.io/)
- 🎥 [Laravel Authentication Video Tutorials](https://laracasts.com)
- 🔐 [OAuth2 RFC Documentation](https://tools.ietf.org/html/rfc6749)
- 🛡️ [API Security Best Practices](https://owasp.org/www-project-api-security/)
- 📊 [JWT Best Practices](https://auth0.com/blog/a-look-at-the-latest-draft-for-jwt-bcp/)

---

## 🎉 Conclusion

Choosing the right authentication method depends on your specific needs:

- **🛡️ Choose Sanctum** for simple SPAs and mobile apps with straightforward requirements
- **🎫 Choose Passport** for complex applications requiring full OAuth2 support and third-party integrations  
- **🔑 Choose JWT** for high-performance, stateless APIs and microservices architectures

Remember to always prioritize security, implement proper token management, and test your authentication flows thoroughly! 🚀✨

### ✅ Security Checklist
- 🔒 Use HTTPS in production
- 🔐 Implement proper token expiration
- 🛡️ Add rate limiting to auth endpoints
- 🔍 Monitor authentication attempts
- 🧹 Regularly cleanup expired tokens
- 📝 Log authentication events
- 🔄 Implement token refresh mechanisms
- 🚫 Validate all inputs thoroughly