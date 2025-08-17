# 🚀 Laravel API Versioning Guide

## 📋 Table of Contents
- [What is API Versioning?](#-what-is-api-versioning)
- [Why Version APIs?](#-why-version-apis)
- [Versioning Strategies](#-versioning-strategies)
- [Laravel Implementation Methods](#-laravel-implementation-methods)
- [Route-Based Versioning](#-route-based-versioning)
- [Controller Organization](#-controller-organization)
- [Middleware for Versioning](#-middleware-for-versioning)
- [Response Transformers](#-response-transformers)
- [Common Issues & Solutions](#-common-issues--solutions)
- [Best Practices](#-best-practices)

## 🔍 What is API Versioning?

API Versioning is a practice of managing different versions of your API to ensure backward compatibility while introducing new features or breaking changes. It allows you to maintain multiple versions simultaneously, giving clients time to migrate to newer versions.

## ❓ Why Version APIs?

- **🛡️ Backward Compatibility**: Existing clients continue to work
- **📱 Mobile App Support**: Different app versions need different API versions
- **🔄 Gradual Migration**: Smooth transition for API consumers
- **🚀 Feature Evolution**: Introduce new features without breaking existing ones
- **🐛 Bug Fixes**: Fix issues in specific versions

## 📊 Versioning Strategies

### 1. 🌐 URL Path Versioning
```
GET /api/v1/users
GET /api/v2/users
```

### 2. 📋 Query Parameter Versioning
```
GET /api/users?version=v1
GET /api/users?version=v2
```

### 3. 📮 Header Versioning
```
GET /api/users
Headers: Accept: application/vnd.api+json;version=1
```

### 4. 🏷️ Subdomain Versioning
```
GET https://v1.api.example.com/users
GET https://v2.api.example.com/users
```

## 🏗️ Laravel Implementation Methods

### Method 1: Route Groups with Prefixes

#### 📁 routes/api.php
```php
<?php

use App\Http\Controllers\Api\V1\UserController as V1UserController;
use App\Http\Controllers\Api\V2\UserController as V2UserController;

// Version 1 Routes
Route::prefix('v1')->group(function () {
    Route::apiResource('users', V1UserController::class);
    Route::get('posts', [V1PostController::class, 'index']);
});

// Version 2 Routes
Route::prefix('v2')->group(function () {
    Route::apiResource('users', V2UserController::class);
    Route::get('posts', [V2PostController::class, 'index']);
});
```

### Method 2: Separate Route Files

#### 📁 routes/api_v1.php
```php
<?php

use App\Http\Controllers\Api\V1\UserController;

Route::apiResource('users', UserController::class);
Route::get('dashboard', [DashboardController::class, 'index']);
```

#### 📁 routes/api_v2.php
```php
<?php

use App\Http\Controllers\Api\V2\UserController;

Route::apiResource('users', UserController::class);
Route::get('dashboard', [DashboardController::class, 'index']);
```

#### 📁 app/Providers/RouteServiceProvider.php
```php
<?php

namespace App\Providers;

use Illuminate\Foundation\Support\Providers\RouteServiceProvider as ServiceProvider;
use Illuminate\Support\Facades\Route;

class RouteServiceProvider extends ServiceProvider
{
    public function boot()
    {
        $this->configureRateLimiting();

        $this->routes(function () {
            // API V1 Routes
            Route::prefix('api/v1')
                ->middleware('api')
                ->group(base_path('routes/api_v1.php'));

            // API V2 Routes
            Route::prefix('api/v2')
                ->middleware('api')
                ->group(base_path('routes/api_v2.php'));

            // Web Routes
            Route::middleware('web')
                ->group(base_path('routes/web.php'));
        });
    }
}
```

## 🎯 Route-Based Versioning

### Advanced Route Configuration

#### 📁 routes/api.php
```php
<?php

// API Version 1
Route::group([
    'prefix' => 'v1',
    'namespace' => 'App\Http\Controllers\Api\V1',
    'middleware' => ['api', 'api.version:v1']
], function () {
    Route::apiResource('users', 'UserController');
    Route::apiResource('posts', 'PostController');
    Route::get('stats', 'StatsController@index');
});

// API Version 2
Route::group([
    'prefix' => 'v2',
    'namespace' => 'App\Http\Controllers\Api\V2',
    'middleware' => ['api', 'api.version:v2']
], function () {
    Route::apiResource('users', 'UserController');
    Route::apiResource('posts', 'PostController');
    Route::get('analytics', 'AnalyticsController@index'); // New endpoint
});
```

## 🎮 Controller Organization

### Directory Structure
```
app/
├── Http/
│   ├── Controllers/
│   │   ├── Api/
│   │   │   ├── V1/
│   │   │   │   ├── UserController.php
│   │   │   │   ├── PostController.php
│   │   │   │   └── StatsController.php
│   │   │   ├── V2/
│   │   │   │   ├── UserController.php
│   │   │   │   ├── PostController.php
│   │   │   │   └── AnalyticsController.php
│   │   │   └── BaseController.php
```

### 📁 app/Http/Controllers/Api/BaseController.php
```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use Illuminate\Http\JsonResponse;

class BaseController extends Controller
{
    protected string $version;

    public function __construct()
    {
        $this->version = request()->route()->getPrefix();
    }

    protected function successResponse($data, string $message = 'Success', int $code = 200): JsonResponse
    {
        return response()->json([
            'status' => 'success',
            'message' => $message,
            'data' => $data,
            'version' => $this->version
        ], $code);
    }

    protected function errorResponse(string $message, int $code = 400): JsonResponse
    {
        return response()->json([
            'status' => 'error',
            'message' => $message,
            'version' => $this->version
        ], $code);
    }
}
```

### 📁 app/Http/Controllers/Api/V1/UserController.php
```php
<?php

namespace App\Http\Controllers\Api\V1;

use App\Http\Controllers\Api\BaseController;
use App\Models\User;
use App\Http\Resources\V1\UserResource;
use Illuminate\Http\Request;

class UserController extends BaseController
{
    public function index()
    {
        $users = User::paginate(10);
        
        return $this->successResponse(
            UserResource::collection($users),
            'Users retrieved successfully'
        );
    }

    public function show(User $user)
    {
        return $this->successResponse(
            new UserResource($user),
            'User retrieved successfully'
        );
    }

    public function store(Request $request)
    {
        $request->validate([
            'name' => 'required|string|max:255',
            'email' => 'required|email|unique:users',
            'password' => 'required|min:6'
        ]);

        $user = User::create([
            'name' => $request->name,
            'email' => $request->email,
            'password' => bcrypt($request->password)
        ]);

        return $this->successResponse(
            new UserResource($user),
            'User created successfully',
            201
        );
    }
}
```

### 📁 app/Http/Controllers/Api/V2/UserController.php
```php
<?php

namespace App\Http\Controllers\Api\V2;

use App\Http\Controllers\Api\BaseController;
use App\Models\User;
use App\Http\Resources\V2\UserResource;
use Illuminate\Http\Request;

class UserController extends BaseController
{
    public function index(Request $request)
    {
        $query = User::query();
        
        // V2 includes filtering capabilities
        if ($request->has('status')) {
            $query->where('status', $request->status);
        }
        
        if ($request->has('role')) {
            $query->where('role', $request->role);
        }
        
        $users = $query->paginate($request->get('per_page', 15));
        
        return $this->successResponse(
            UserResource::collection($users),
            'Users retrieved successfully'
        );
    }

    public function show(User $user)
    {
        // V2 includes additional user relationships
        $user->load(['profile', 'posts', 'roles']);
        
        return $this->successResponse(
            new UserResource($user),
            'User retrieved successfully'
        );
    }
}
```

## 🛡️ Middleware for Versioning

### 📁 app/Http/Middleware/ApiVersionMiddleware.php
```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class ApiVersionMiddleware
{
    public function handle(Request $request, Closure $next, string $version)
    {
        // Set the API version in the request
        $request->merge(['api_version' => $version]);
        
        // Add version header to response
        $response = $next($request);
        
        if (method_exists($response, 'header')) {
            $response->header('X-API-Version', $version);
        }
        
        return $response;
    }
}
```

### Register Middleware in 📁 app/Http/Kernel.php
```php
<?php

protected $routeMiddleware = [
    // ... other middleware
    'api.version' => \App\Http\Middleware\ApiVersionMiddleware::class,
];
```

## 🔄 Response Transformers

### 📁 app/Http/Resources/V1/UserResource.php
```php
<?php

namespace App\Http\Resources\V1;

use Illuminate\Http\Resources\Json\JsonResource;

class UserResource extends JsonResource
{
    public function toArray($request)
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'email' => $this->email,
            'created_at' => $this->created_at->format('Y-m-d H:i:s'),
            // V1 basic structure
        ];
    }
}
```

### 📁 app/Http/Resources/V2/UserResource.php
```php
<?php

namespace App\Http\Resources\V2;

use Illuminate\Http\Resources\Json\JsonResource;

class UserResource extends JsonResource
{
    public function toArray($request)
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'email' => $this->email,
            'status' => $this->status,
            'role' => $this->role,
            'profile' => $this->whenLoaded('profile'),
            'posts_count' => $this->whenLoaded('posts', function () {
                return $this->posts->count();
            }),
            'created_at' => $this->created_at->toISOString(),
            'updated_at' => $this->updated_at->toISOString(),
            // V2 enhanced structure
        ];
    }
}
```

## 🐛 Common Issues & Solutions

### Issue 1: Route Conflicts
**Problem**: Routes from different versions conflict
```php
// ❌ This causes conflicts
Route::get('/users/{id}', 'V1\UserController@show');
Route::get('/users/{slug}', 'V2\UserController@showBySlug');
```

**Solution**: Use different route patterns or middleware
```php
// ✅ Better approach
Route::prefix('v1')->group(function () {
    Route::get('/users/{id}', 'V1\UserController@show')->where('id', '[0-9]+');
});

Route::prefix('v2')->group(function () {
    Route::get('/users/{identifier}', 'V2\UserController@show');
});
```

### Issue 2: Shared Code Duplication
**Problem**: Duplicate logic across versions

**Solution**: Create base classes and traits
```php
// 📁 app/Http/Controllers/Api/Traits/UserActions.php
<?php

namespace App\Http\Controllers\Api\Traits;

use App\Models\User;
use Illuminate\Http\Request;

trait UserActions
{
    protected function validateUserData(Request $request, ?User $user = null): array
    {
        $rules = [
            'name' => 'required|string|max:255',
            'email' => 'required|email|unique:users,email' . ($user ? ',' . $user->id : ''),
        ];
        
        if (!$user) {
            $rules['password'] = 'required|min:6';
        }
        
        return $request->validate($rules);
    }
}
```

### Issue 3: Database Migration Conflicts
**Problem**: Different versions need different data structures

**Solution**: Use API transformers and database views
```php
// Create database views for different versions
DB::statement('CREATE VIEW users_v1 AS SELECT id, name, email, created_at FROM users');
DB::statement('CREATE VIEW users_v2 AS SELECT * FROM users');
```

## ✅ Best Practices

### 1. 📝 Semantic Versioning
- Use semantic versioning: `v1.0.0`, `v1.1.0`, `v2.0.0`
- Major version for breaking changes
- Minor version for new features
- Patch version for bug fixes

### 2. 🔄 Version Lifecycle Management
```php
// 📁 config/api.php
<?php

return [
    'versions' => [
        'v1' => [
            'status' => 'deprecated',
            'sunset_date' => '2024-12-31',
            'supported_until' => '2025-06-30'
        ],
        'v2' => [
            'status' => 'current',
            'sunset_date' => null,
            'supported_until' => null
        ],
        'v3' => [
            'status' => 'beta',
            'sunset_date' => null,
            'supported_until' => null
        ]
    ]
];
```

### 3. 📊 Version Usage Tracking
```php
// 📁 app/Http/Middleware/ApiVersionTracking.php
<?php

namespace App\Http\Middleware;

use Illuminate\Support\Facades\Log;
use Closure;

class ApiVersionTracking
{
    public function handle($request, Closure $next)
    {
        $version = $request->route()->getPrefix();
        
        Log::channel('api_usage')->info('API Version Used', [
            'version' => $version,
            'endpoint' => $request->path(),
            'user_agent' => $request->userAgent(),
            'ip' => $request->ip()
        ]);
        
        return $next($request);
    }
}
```

### 4. 🚨 Deprecation Warnings
```php
// Add to base controller
protected function addDeprecationWarning($response, string $version, string $sunsetDate)
{
    return $response->header('Warning', "299 - \"Version {$version} is deprecated. Sunset date: {$sunsetDate}\"");
}
```

### 5. 📚 API Documentation per Version
Create separate documentation for each version:
- `/docs/v1/` - Version 1 documentation
- `/docs/v2/` - Version 2 documentation

### 6. 🧪 Testing Strategy
```php
// 📁 tests/Feature/Api/V1/UserTest.php
<?php

namespace Tests\Feature\Api\V1;

use Tests\TestCase;

class UserTest extends TestCase
{
    protected string $apiVersion = 'v1';
    
    public function test_can_get_users()
    {
        $response = $this->getJson("/api/{$this->apiVersion}/users");
        
        $response->assertStatus(200)
                ->assertJsonStructure([
                    'status',
                    'message',
                    'data' => [
                        '*' => ['id', 'name', 'email', 'created_at']
                    ]
                ]);
    }
}
```

## 🎯 Example Implementation Checklist

- [ ] ✅ Set up route versioning structure
- [ ] 🎮 Create version-specific controllers
- [ ] 🔄 Implement response transformers/resources
- [ ] 🛡️ Add version middleware
- [ ] 📊 Set up version tracking
- [ ] 🚨 Implement deprecation warnings
- [ ] 🧪 Create version-specific tests
- [ ] 📚 Document each version separately
- [ ] 🔧 Configure version lifecycle management
- [ ] 🎯 Plan migration strategy for clients

## 🚀 Conclusion

Laravel API versioning ensures your API can evolve while maintaining backward compatibility. Choose the strategy that best fits your needs, implement proper organization, and follow best practices for a maintainable and scalable API architecture.

Remember to:
- Keep versions simple and intuitive
- Provide clear migration guides
- Monitor version usage
- Plan deprecation timelines
- Test thoroughly across versions

Happy coding! 🎉