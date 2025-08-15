
# 📚 50 Most Asked Laravel Questions \& Answers

## 📋 Table of Contents

| \# | Question Category |
| :-- | :-- |
| [1-10](#basic-laravel-questions-) | Basic Laravel Questions |
| [11-20](#architecture-and-mvc-) | Architecture and MVC |
| [21-30](#routing-and-controllers-) | Routing and Controllers |
| [31-40](#database-and-eloquent-) | Database and Eloquent |
| [41-50](#advanced-concepts-) | Advanced Concepts |


***

## Basic Laravel Questions 📝

<details>
<summary><strong>1. What is Laravel and why is it popular among developers?</strong></summary>

**Answer:**
Laravel is an open-source PHP framework built to simplify common web development tasks like routing, authentication, sessions, and caching. Developers like Laravel because it has clean syntax, built-in tools, and strong community support. It speeds up development without sacrificing structure or flexibility.[^1]

**Example:**
```php
// Simple Laravel route
Route::get('/users', function () {
    return User::all();
});
```
</details>
<details>
<summary><strong>2. What is MVC architecture in Laravel?</strong></summary>

**Answer:**
MVC (Model–View–Controller) is a design pattern that separates an application into three main components:

- **Model** – Handles the data layer and business logic (e.g., interacting with the database, defining relationships).
- **View** – Manages the presentation layer, i.e., what the user sees (HTML, Blade templates).
- **Controller** – Acts as the intermediary between the Model and View, receiving user requests, processing them via the Model, and returning the appropriate View.

In Laravel, MVC helps keep the code organized, maintainable, and scalable by clearly separating concerns.

**Example:**
```php
// Controller
class UserController extends Controller
{
    public function index()
    {
        $users = User::all(); // Model
        return view('users.index', compact('users')); // View
    }
}
```
</details>
<details>
<summary><strong>3. What is Artisan in Laravel?</strong></summary>

**Answer:**
Artisan is Laravel's command-line tool that helps automate repetitive tasks like creating controllers, running migrations, seeding the database, and clearing caches. For example, `php artisan make:controller` generates a new controller class in seconds.[^1]

**Example:**
```bash
# Create a controller
php artisan make:controller UserController

# Run migrations
php artisan migrate

# Clear cache
php artisan cache:clear
```
</details>
<details>
<summary><strong>4. What are Laravel events?</strong></summary>

**Answer:**  
Events in Laravel are program-recognizable actions or occurrences within the application that can trigger specific responses.  

Laravel provides a simple **observer pattern** implementation for handling events, allowing developers to:

- **Listen** for certain events in the application.
- **Respond** to them with event listeners.

This makes it easy to decouple different parts of the application, improving maintainability and scalability.

**Example:**
```php
// Creating an event
php artisan make:event UserRegistered

// Event class
class UserRegistered
{
    public $user;
    
    public function __construct(User $user)
    {
        $this->user = $user;
    }
}

// Firing the event
event(new UserRegistered($user));
```
</details>
<details>
<summary><strong>5. What is the difference between get(), first(), and find() in Eloquent?</strong></summary>

**Answer:**
- `get()` returns a collection of records
- `first()` returns only the first result
- `find()` looks for a specific record by primary key[^1]

**Example:**
```php
// get() - returns collection of all users
$users = User::get();

// first() - returns first user
$user = User::first();

// find() - returns user with ID 1
$user = User::find(1);
```
</details>
<details>
<summary><strong>6. What are named routes in Laravel?</strong></summary>

**Answer:**
Named routes are an essential component of the Laravel framework, allowing URLs and redirects to specific routes to reference the routes by name. We can specify named routes by chaining the name method to the route definition.[^2]

**Example:**
```php
// Named route
Route::get('/users', [UserController::class, 'index'])->name('users.index');

// Generating URL
$url = route('users.index');

// Redirect
return redirect()->route('users.index');
```
</details>
<details>
<summary><strong>7. What are bundles/packages in Laravel?</strong></summary>

**Answer:**
Packages are the term to describe bundles in Laravel. These packages help enhance Laravel's functionality. A package can include tasks, views, configuration, migrations and routes.[^2]

**Example:**
```bash
# Install a package via Composer
composer require laravel/telescope

# Publish package assets
php artisan vendor:publish --provider="Laravel\Telescope\TelescopeServiceProvider"
```
</details>
<details>
<summary><strong>8. What is the difference between GET and POST methods?</strong></summary>

**Answer:**
GET method does not allow the transmission of large amounts of data because the request parameter is added to the URL. The POST method allows the transmission of large amounts of data as the request parameter is bound to the body of the POST method.[^2]

**Example:**
```php
// GET route
Route::get('/users', [UserController::class, 'index']);

// POST route
Route::post('/users', [UserController::class, 'store']);
```
</details>
<details>
<summary><strong>9. How do you define a controller in Laravel?</strong></summary>

**Answer:**
You can create a controller using the Artisan command `php artisan make:controller UserController`. Then you define methods inside the class for handling requests, like `index()`, `store()`, or `update()`. These methods are linked to routes in web.php or api.php.[^1]

**Example:**
```php
// Create controller
php artisan make:controller UserController

// Controller class
class UserController extends Controller
{
    public function index()
    {
        return User::all();
    }
    
    public function store(Request $request)
    {
        return User::create($request->all());
    }
}
```
</details>
<details>
<summary><strong>10. What is Laravel Blade templating engine?</strong></summary>

**Answer:**
Blade is Laravel's powerful templating engine that allows you to use plain PHP code in your templates. It provides convenient shortcuts for common PHP functionality while remaining lightweight and fast.

**Example:**
```blade
{{-- resources/views/users/index.blade.php --}}
@extends('layouts.app')

@section('content')
    <h1>Users</h1>
    @foreach($users as $user)
        <p>{{ $user->name }}</p>
    @endforeach
@endsection
```
</details>

***

## Architecture and MVC 🏗️

<details>
<summary><strong>11. Explain Laravel's service container</strong></summary>

**Answer:**
The service container is a powerful tool for managing class dependencies and performing dependency injection. It automatically resolves dependencies and can bind interfaces to implementations.

**Example:**
```php
// Binding in AppServiceProvider
public function register()
{
    $this->app->bind(UserRepositoryInterface::class, UserRepository::class);
}

// Automatic injection
class UserController extends Controller
{
    public function __construct(UserRepositoryInterface $repository)
    {
        $this->repository = $repository;
    }
}
```
</details>
<details>
<summary><strong>12. What are Laravel facades?</strong></summary>

**Answer:**
Facades provide a static interface to classes that are available in the application's service container. They serve as "static proxies" to underlying classes in the service container.

**Example:**
```php
// Using facade
use Illuminate\Support\Facades\Cache;

Cache::put('key', 'value', 60);

// Equivalent to
app('cache')->put('key', 'value', 60);
```
</details>
<details>
<summary><strong>13. What is dependency injection in Laravel?</strong></summary>

**Answer:**
Dependency injection is a technique where dependencies are provided to a class rather than the class creating them itself. Laravel's service container handles this automatically through constructor injection or method injection.

**Example:**
```php
class UserService
{
    protected $repository;
    
    // Constructor injection
    public function __construct(UserRepository $repository)
    {
        $this->repository = $repository;
    }
}
```
</details>
<details>
<summary><strong>14. What are Laravel service providers?</strong></summary>

**Answer:**
Service providers are the central place where Laravel application bootstrapping happens. They register services, bind classes into the service container, and configure the application.

**Example:**
```php
class UserServiceProvider extends ServiceProvider
{
    public function register()
    {
        $this->app->bind(UserRepositoryInterface::class, UserRepository::class);
    }
    
    public function boot()
    {
        // Bootstrap services
    }
}
```
</details>
<details>
<summary><strong>15. What is the Laravel application lifecycle?</strong></summary>

**Answer:**
The Laravel application lifecycle involves: HTTP request → public/index.php → Bootstrap → Kernel → Service Providers → Middleware → Route Resolution → Controller → Response.

**Example:**
```php
// public/index.php
$app = require_once __DIR__.'/../bootstrap/app.php';
$kernel = $app->make(Illuminate\Contracts\Http\Kernel::class);
$response = $kernel->handle($request = Illuminate\Http\Request::capture());
```
</details>
<details>
<summary><strong>16. What are contracts in Laravel?</strong></summary>

**Answer:**
Contracts are interfaces that define the core services provided by Laravel. They allow you to define explicit dependencies for your classes and provide low coupling.

**Example:**
```php
use Illuminate\Contracts\Cache\Repository as Cache;

class UserService
{
    public function __construct(Cache $cache)
    {
        $this->cache = $cache;
    }
}
```
</details>
<details>
<summary><strong>17. What is the Repository pattern in Laravel?</strong></summary>

**Answer:**
The Repository pattern abstracts the logic needed to access data sources. It centralizes common data access functionality and provides a separation layer between the data access layer and business logic.

**Example:**
```php
interface UserRepositoryInterface
{
    public function find($id);
    public function create(array $data);
}

class UserRepository implements UserRepositoryInterface
{
    public function find($id)
    {
        return User::find($id);
    }
    
    public function create(array $data)
    {
        return User::create($data);
    }
}
```
</details>
<details>
<summary><strong>18. What are Laravel form requests?</strong></summary>

**Answer:**
Form requests are custom request classes that contain validation logic. They help separate validation logic from controllers and provide a clean way to handle authorization and validation.

**Example:**
```php
// Create form request
php artisan make:request StoreUserRequest

class StoreUserRequest extends FormRequest
{
    public function authorize()
    {
        return true;
    }
    
    public function rules()
    {
        return [
            'name' => 'required|string|max:255',
            'email' => 'required|email|unique:users'
        ];
    }
}
```
</details>
<details>
<summary><strong>19. What is Laravel's IoC container?</strong></summary>

**Answer:**
The Inversion of Control (IoC) container is Laravel's service container that manages dependencies and performs dependency injection automatically. It resolves classes and their dependencies.

**Example:**
```php
// Binding
app()->bind('UserService', function () {
    return new UserService(new UserRepository());
});

// Resolving
$userService = app('UserService');
```
</details>
<details>
<summary><strong>20. What are Laravel macros?</strong></summary>

**Answer:**
Macros allow you to add custom methods to Laravel's core classes at runtime. They're useful for extending functionality without modifying core files.

**Example:**
```php
// In AppServiceProvider boot method
Collection::macro('toUpper', function () {
    return $this->map(function ($value) {
        return strtoupper($value);
    });
});

// Usage
collect(['foo', 'bar'])->toUpper(); // ['FOO', 'BAR']
```
</details>

***

## Routing and Controllers 🛣️

<details>
<summary><strong>21. How does Laravel handle routing?</strong></summary>

**Answer:**
Laravel uses a file called routes/web.php for web routes and routes/api.php for API routes. You define routes using expressive methods like Route::get(), Route::post(), etc. Routes can be grouped, named, and protected with middleware.[^1]

**Example:**
```php
// Basic routes
Route::get('/users', [UserController::class, 'index']);
Route::post('/users', [UserController::class, 'store']);

// Route groups
Route::middleware(['auth'])->group(function () {
    Route::get('/dashboard', [DashboardController::class, 'index']);
});
```
</details>
<details>
<summary><strong>22. What are route parameters in Laravel?</strong></summary>

**Answer:**
Route parameters allow you to capture segments of the URI within your route. They can be required or optional and can be constrained using regular expressions.

**Example:**
```php
// Required parameter
Route::get('/user/{id}', function ($id) {
    return "User ID: " . $id;
});

// Optional parameter
Route::get('/user/{id?}', function ($id = null) {
    return "User ID: " . ($id ?? 'None');
});

// Parameter constraints
Route::get('/user/{id}', function ($id) {
    return "User ID: " . $id;
})->where('id', '[0-9]+');
```
</details>
<details>
<summary><strong>23. What is route model binding in Laravel?</strong></summary>

**Answer:**
Route model binding automatically injects model instances into your routes based on route parameters. Laravel automatically resolves Eloquent models defined in routes or controller actions.

**Example:**
```php
// Implicit binding
Route::get('/user/{user}', function (User $user) {
    return $user->email;
});

// Custom key binding
Route::get('/user/{user:slug}', function (User $user) {
    return $user;
});
```
</details>
<details>
<summary><strong>24. What are route groups in Laravel?</strong></summary>

**Answer:**
Route groups allow you to share route attributes, such as middleware or namespaces, across a large number of routes without needing to define those attributes on each individual route.

**Example:**
```php
// Middleware group
Route::middleware(['auth', 'verified'])->group(function () {
    Route::get('/dashboard', [DashboardController::class, 'index']);
    Route::get('/profile', [ProfileController::class, 'show']);
});

// Prefix group
Route::prefix('admin')->group(function () {
    Route::get('/users', [AdminController::class, 'users']);
    Route::get('/posts', [AdminController::class, 'posts']);
});
```
</details>
<details>
<summary><strong>25. What is middleware in Laravel?</strong></summary>

**Answer:**
Middleware provides a convenient mechanism for filtering HTTP requests entering your application. They act as a bridge between a request and a response.

**Example:**
```php
// Create middleware
php artisan make:middleware CheckAge

class CheckAge
{
    public function handle($request, Closure $next)
    {
        if ($request->age <= 200) {
            return redirect('home');
        }
        
        return $next($request);
    }
}

// Apply to route
Route::get('admin/profile', function () {
    //
})->middleware('age');
```
</details>
<details>
<summary><strong>26. What are resource controllers in Laravel?</strong></summary>

**Answer:**
Resource controllers provide a convenient way to build RESTful controllers around resources. They automatically define routes for CRUD operations.

**Example:**
```php
// Create resource controller
php artisan make:controller PhotoController --resource

// Register resource routes
Route::resource('photos', PhotoController::class);

// This creates:
// GET /photos (index)
// GET /photos/create (create)
// POST /photos (store)
// GET /photos/{photo} (show)
// GET /photos/{photo}/edit (edit)
// PUT/PATCH /photos/{photo} (update)
// DELETE /photos/{photo} (destroy)
```
</details>
<details>
<summary><strong>27. How do you handle CORS in Laravel?</strong></summary>

**Answer:**
Laravel handles CORS through middleware. You can configure CORS settings in the config/cors.php file and apply the HandleCors middleware.

**Example:**
```php
// config/cors.php
return [
    'paths' => ['api/*'],
    'allowed_methods' => ['*'],
    'allowed_origins' => ['*'],
    'allowed_origins_patterns' => [],
    'allowed_headers' => ['*'],
    'exposed_headers' => [],
    'max_age' => 0,
    'supports_credentials' => false,
];
```
</details>
<details>
<summary><strong>28. What is rate limiting in Laravel?</strong></summary>

**Answer:**
Rate limiting restricts the number of requests a user can make within a given time period. Laravel provides built-in rate limiting through the throttle middleware.[^1]

**Example:**
```php
// Apply rate limiting
Route::middleware('throttle:60,1')->group(function () {
    Route::get('/api/users', [UserController::class, 'index']);
});

// Custom rate limiter
RateLimiter::for('api', function (Request $request) {
    return Limit::perMinute(60)->by(optional($request->user())->id ?: $request->ip());
});
```
</details>
<details>
<summary><strong>29. How do you handle API versioning in Laravel?</strong></summary>

**Answer:**
API versioning can be handled through route prefixes, subdirectories, or header-based versioning. The most common approach is using route prefixes.

**Example:**
```php
// Route prefixes for versioning
Route::prefix('v1')->group(function () {
    Route::get('/users', [V1\UserController::class, 'index']);
});

Route::prefix('v2')->group(function () {
    Route::get('/users', [V2\UserController::class, 'index']);
});
```
</details>
<details>
<summary><strong>30. What are API resources in Laravel?</strong></summary>

**Answer:**
API resources provide a transformation layer between your Eloquent models and JSON responses. They allow you to control exactly how your models are serialized.

**Example:**
```php
// Create resource
php artisan make:resource UserResource

class UserResource extends JsonResource
{
    public function toArray($request)
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'email' => $this->email,
            'created_at' => $this->created_at,
        ];
    }
}

// Usage
return new UserResource($user);
```
</details>

***

## Database and Eloquent 🗄️

<details>
<summary><strong>31. What are Laravel migrations?</strong></summary>

**Answer:**
Migrations are version control for your database, allowing you to modify your database schema in a structured way. They work with Laravel's schema builder to manage your database schema.

**Example:**
```php
// Create migration
php artisan make:migration create_users_table

class CreateUsersTable extends Migration
{
    public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('email')->unique();
            $table->timestamps();
        });
    }
    
    public function down()
    {
        Schema::dropIfExists('users');
    }
}
```
</details>
<details>
<summary><strong>32. What is Laravel Eloquent ORM?</strong></summary>

**Answer:**
Eloquent is Laravel's Object-Relational Mapping (ORM) that provides an elegant ActiveRecord implementation for working with your database. Each database table has a corresponding Model.

**Example:**
```php
class User extends Model
{
    protected $fillable = ['name', 'email'];
    
    public function posts()
    {
        return $this->hasMany(Post::class);
    }
}

// Usage
$user = User::create(['name' => 'John', 'email' => 'john@example.com']);
$posts = $user->posts;
```
</details>
<details>
<summary><strong>33. How do you avoid N+1 query problems in Eloquent?</strong></summary>

**Answer:**
Use eager loading with `with()` to load relationships ahead of time. For example, `Post::with('comments')->get()` prevents multiple queries for each comment. You can also use `load()` when you already have the parent model.[^1]

**Example:**
```php
// N+1 problem
$posts = Post::all();
foreach ($posts as $post) {
    echo $post->user->name; // This creates N+1 queries
}

// Solution: Eager loading
$posts = Post::with('user')->get();
foreach ($posts as $post) {
    echo $post->user->name; // Only 2 queries total
}
```
</details>
<details>
<summary><strong>34. What are Eloquent relationships?</strong></summary>

**Answer:**
Eloquent relationships define how models are related to each other. Laravel supports various relationship types including one-to-one, one-to-many, many-to-many, and polymorphic relationships.

**Example:**
```php
class User extends Model
{
    // One-to-many
    public function posts()
    {
        return $this->hasMany(Post::class);
    }
    
    // Many-to-many
    public function roles()
    {
        return $this->belongsToMany(Role::class);
    }
}

class Post extends Model
{
    // One-to-one (inverse)
    public function user()
    {
        return $this->belongsTo(User::class);
    }
}
```
</details>
<details>
<summary><strong>35. What is the difference between hasOneThrough and hasManyThrough?</strong></summary>

**Answer:**
`hasOneThrough` defines a one-to-one relationship across two models, while `hasManyThrough` defines one-to-many. For example, if a country has many users and users have posts, then Country can access posts via `hasManyThrough`.[^1]

**Example:**
```php
class Country extends Model
{
    public function posts()
    {
        return $this->hasManyThrough(Post::class, User::class);
    }
    
    public function latestPost()
    {
        return $this->hasOneThrough(Post::class, User::class)->latest();
    }
}
```
</details>
<details>
<summary><strong>36. What are database seeders in Laravel?</strong></summary>

**Answer:**
Seeders allow you to populate your database with test data. Laravel includes a simple method for seeding your database with test data using seed classes.

**Example:**
```php
// Create seeder
php artisan make:seeder UserSeeder

class UserSeeder extends Seeder
{
    public function run()
    {
        User::factory(50)->create();
        
        User::create([
            'name' => 'Admin User',
            'email' => 'admin@example.com',
        ]);
    }
}

// Run seeder
php artisan db:seed --class=UserSeeder
```
</details>
<details>
<summary><strong>37. What are model factories in Laravel?</strong></summary>

**Answer:**
Model factories provide a convenient way to generate fake data for testing and seeding. They define the default set of attributes for each of your Eloquent models.

**Example:**
```php
// Create factory
php artisan make:factory UserFactory

class UserFactory extends Factory
{
    public function definition()
    {
        return [
            'name' => $this->faker->name(),
            'email' => $this->faker->unique()->safeEmail(),
            'password' => bcrypt('password'),
        ];
    }
}

// Usage
$user = User::factory()->create();
$users = User::factory(10)->create();
```
</details>
<details>
<summary><strong>38. What are accessors and mutators in Eloquent?</strong></summary>

**Answer:**
Accessors transform Eloquent attribute values when you access them, while mutators transform Eloquent attribute values when you set them.

**Example:**
```php
class User extends Model
{
    // Accessor
    public function getFullNameAttribute()
    {
        return $this->first_name . ' ' . $this->last_name;
    }
    
    // Mutator
    public function setPasswordAttribute($value)
    {
        $this->attributes['password'] = bcrypt($value);
    }
}

// Usage
$user->password = 'secret'; // Automatically hashed
echo $user->full_name; // John Doe
```
</details>
<details>
<summary><strong>39. What are Laravel model scopes?</strong></summary>

**Answer:**
Scopes allow you to define common sets of constraints that you may easily re-use throughout your application. There are global scopes and local scopes.

**Example:**
```php
class User extends Model
{
    // Local scope
    public function scopeActive($query)
    {
        return $query->where('active', 1);
    }
    
    public function scopePopular($query)
    {
        return $query->where('votes', '>', 100);
    }
}

// Usage
$users = User::active()->popular()->get();
```
</details>
<details>
<summary><strong>40. How do you handle soft deletes in Laravel?</strong></summary>

**Answer:**
Soft deletes allow you to "delete" models without actually removing them from the database. Laravel sets a deleted_at timestamp instead of removing the record.

**Example:**
```php
use Illuminate\Database\Eloquent\SoftDeletes;

class User extends Model
{
    use SoftDeletes;
    
    protected $dates = ['deleted_at'];
}

// Usage
$user->delete(); // Soft delete
$user->restore(); // Restore
$user->forceDelete(); // Permanent delete

// Query soft deleted records
$users = User::withTrashed()->get();
$users = User::onlyTrashed()->get();
```
</details>

***

## Advanced Concepts 🚀

<details>
<summary><strong>41. What is Laravel Octane?</strong></summary>

**Answer:**
Laravel Octane speeds up applications by serving requests through Swoole or RoadRunner. It keeps the app in memory between requests, which reduces boot time. Use Octane for high-performance apps with many requests per second, especially when working with APIs or real-time services.[^1]

**Example:**
```bash
# Install Octane
composer require laravel/octane

# Install Swoole
php artisan octane:install --server=swoole

# Start Octane server
php artisan octane:start
```
</details>
<details>
<summary><strong>42. How do you implement caching strategies in Laravel?</strong></summary>

**Answer:**
Laravel provides various caching strategies including route, view, and config caching with Artisan commands. For data caching, use Redis or Memcached. Cache frequently accessed queries using `Cache::remember()` and apply tags to manage cache groups.[^1]

**Example:**
```php
// Cache data
Cache::put('users', $users, 3600);

// Cache with remember
$users = Cache::remember('users', 3600, function () {
    return User::all();
});

// Cache tags
Cache::tags(['users', 'posts'])->put('stats', $data, 3600);
Cache::tags(['users'])->flush();
```
</details>
<details>
<summary><strong>43. What are Laravel queues and jobs?</strong></summary>

**Answer:**
Queues allow you to defer time-consuming tasks to be processed in the background. Jobs are the tasks that are queued for background processing.

**Example:**
```php
// Create job
php artisan make:job SendWelcomeEmail

class SendWelcomeEmail implements ShouldQueue
{
    public $user;
    
    public function __construct(User $user)
    {
        $this->user = $user;
    }
    
    public function handle()
    {
        Mail::to($this->user->email)->send(new WelcomeEmail($this->user));
    }
}

// Dispatch job
SendWelcomeEmail::dispatch($user);
```
</details>
<details>
<summary><strong>44. How do you debug failed jobs in Laravel queues?</strong></summary>

**Answer:**
First, check the failed_jobs table to see the error message. You can also log errors inside the job's `failed()` method. Laravel supports retrying failed jobs with `php artisan queue:retry`. Use Horizon for monitoring when Redis is the queue driver.[^1]

**Example:**
```php
class SendWelcomeEmail implements ShouldQueue
{
    public function handle()
    {
        // Job logic
    }
    
    public function failed(Exception $exception)
    {
        Log::error('Job failed: ' . $exception->getMessage());
    }
}

// Retry failed jobs
php artisan queue:retry all
php artisan queue:retry 5 // Retry specific job ID
```
</details>
<details>
<summary><strong>45. How do you handle file uploads securely in Laravel?</strong></summary>

**Answer:**
Validate files using the `mimes` or `file` rule and restrict file size. Then store uploads using `store()` or `storeAs()` in Laravel's storage system. Never trust client file names and always store files outside the public directory unless explicitly needed.[^1]

**Example:**
```php
public function store(Request $request)
{
    $request->validate([
        'avatar' => 'required|image|mimes:jpeg,png,jpg|max:2048'
    ]);
    
    $path = $request->file('avatar')->store('avatars', 'private');
    
    auth()->user()->update(['avatar' => $path]);
    
    return back()->with('success', 'Avatar uploaded successfully!');
}
```
</details>
<details>
<summary><strong>46. How do you handle CSRF protection in Laravel?</strong></summary>

**Answer:**
Laravel uses a CSRF token stored in a session and injected into forms via `@csrf`. When a POST, PUT, PATCH, or DELETE request is made, Laravel compares the token with the session to prevent cross-site attacks.[^1]

**Example:**
```blade
{{-- In Blade template --}}
<form method="POST" action="/user">
    @csrf
    <input type="text" name="name">
    <button type="submit">Submit</button>
</form>

{{-- Or manually --}}
<meta name="csrf-token" content="{{ csrf_token() }}">

<script>
$.ajaxSetup({
    headers: {
        'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
    }
});
</script>
```
</details>
<details>
<summary><strong>47. How do you create custom Artisan commands?</strong></summary>

**Answer:**
Use `php artisan make:command MyCommand`. Then, set a signature and logic inside the `handle()` method. Register it in Kernel.php under the commands array. Run it using `php artisan my:command`.[^1]

**Example:**
```php
// Create command
php artisan make:command SendEmails

class SendEmails extends Command
{
    protected $signature = 'email:send {user}';
    protected $description = 'Send emails to users';
    
    public function handle()
    {
        $userId = $this->argument('user');
        $user = User::find($userId);
        
        $this->info("Sending email to {$user->email}");
        // Send email logic
    }
}

// Run command
php artisan email:send 1
```
</details>
<details>
<summary><strong>48. How do you implement API authentication in Laravel?</strong></summary>

**Answer:**
For authentication, use Laravel Sanctum or Passport. Rate limiting is managed using throttle middleware, and can be configured in RouteServiceProvider or directly on routes using `->middleware('throttle:60,1')`.[^1]

**Example:**
```php
// Install Sanctum
composer require laravel/sanctum

// Generate token
$token = $user->createToken('API Token')->plainTextToken;

// Protect routes
Route::middleware('auth:sanctum')->get('/user', function (Request $request) {
    return $request->user();
});

// Use token in requests
// Authorization: Bearer {token}
```
</details>
<details>
<summary><strong>49. How do you validate deeply nested array inputs?</strong></summary>

**Answer:**
Use dot notation or wildcard rules like `'items.*.name' => 'required|string'` in form request validation[^1]. This works well when dealing with arrays of objects from the frontend[^1].

**Example:**
```php
$request->validate([
    'items' => 'required|array|min:1',
    'items.*.product_id' => 'required|integer|exists:products,id',
    'items.*.quantity' => 'required|integer|min:1',
    'items.*.options' => 'array',
    'items.*.options.*.name' => 'required|string',
    'items.*.options.*.value' => 'required|string'
]);
```
</details>
<details>
<summary><strong>50. How do you implement role-based permissions in Laravel?</strong></summary>

**Answer:**
Define roles and permissions in database tables. Then use Laravel Gates or Policies to check permissions at runtime. Usually create a `hasPermission()` method on the User model and check permissions through middleware or inside controllers.[^1]

**Example:**
```php
// User model
class User extends Model
{
    public function roles()
    {
        return $this->belongsToMany(Role::class);
    }
    
    public function hasPermission($permission)
    {
        return $this->roles()->whereHas('permissions', function ($query) use ($permission) {
            $query->where('name', $permission);
        })->exists();
    }
}

// Gate definition
Gate::define('edit-posts', function ($user) {
    return $user->hasPermission('edit-posts');
});

// Usage
if (Gate::allows('edit-posts')) {
    // User can edit posts
}
```
</details>

***

## 🎯 Quick Reference

### Most Important Commands

```bash
# Project setup
composer create-project laravel/laravel myapp
php artisan serve

# Database
php artisan migrate
php artisan migrate:rollback
php artisan db:seed

# Generate files
php artisan make:controller UserController
php artisan make:model User -m
php artisan make:middleware CheckAge
php artisan make:request StoreUserRequest

# Cache and optimization
php artisan cache:clear
php artisan config:cache
php artisan route:cache
php artisan view:cache
```


### Essential Concepts to Master

- 🏗️ **MVC Architecture** - Understanding Models, Views, Controllers
- 🛣️ **Routing** - Named routes, resource routes, route model binding
- 🗄️ **Eloquent ORM** - Relationships, eager loading, scopes
- 🔧 **Artisan** - Custom commands, migrations, seeders
- 🔐 **Security** - CSRF protection, authentication, authorization
- ⚡ **Performance** - Caching strategies, queue jobs, optimization

***

*💡 **Pro Tip:** Practice building real projects using these concepts. The best way to learn Laravel is by doing!*

<div style="text-align: center">⁂</div>

[^1]: https://www.hirist.tech/blog/top-35-laravel-interview-questions-and-answers/

[^2]: https://in.indeed.com/career-advice/interviewing/laravel-interview-questions

[^3]: https://www.interviewbit.com/laravel-interview-questions/

[^4]: https://www.vinsys.com/blog/top-40-laravel-interview-questions

[^5]: https://www.usebraintrust.com/hire/interview-questions/laravel-developers

[^6]: https://github.com/Devinterview-io/laravel-interview-questions

[^7]: https://www.revelo.com/interview-questions/laravel-developer

[^8]: https://www.simplilearn.com/laravel-interview-questions-answers-article

