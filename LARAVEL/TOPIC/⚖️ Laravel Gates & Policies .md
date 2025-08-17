# ⚖️ Laravel Gates & Policies - Complete Guide

## 📋 Overview

Laravel's authorization system provides two primary approaches for fine-grained access control: **Gates** and **Policies**. These powerful tools allow you to define complex authorization logic that goes beyond simple role-based permissions, enabling you to create sophisticated access control systems based on user attributes, resource ownership, and custom business logic.

---

## 🌟 What are Gates & Policies?

### 🚪 Gates
Gates are simple closures or class methods that determine if a user is authorized to perform a given action. They're perfect for actions that aren't necessarily related to a specific model or resource.

### 📋 Policies
Policies are classes that organize authorization logic around a particular model or resource. They're ideal when you need to authorize actions against specific model instances.

### 🎯 Key Differences

| Feature | 🚪 Gates | 📋 Policies |
|---------|----------|-------------|
| **Use Case** | General actions | Model-specific actions |
| **Structure** | Simple closures/methods | Organized classes |
| **Best For** | Global permissions | Resource-based auth |
| **Complexity** | Simple logic | Complex authorization |

---

## 🚪 Gates Deep Dive

### 🏗️ Defining Gates

Gates are typically defined in the `AuthServiceProvider`:

```php
<?php

namespace App\Providers;

use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;
use Illuminate\Support\Facades\Gate;
use App\Models\User;

class AuthServiceProvider extends ServiceProvider
{
    public function boot()
    {
        $this->registerPolicies();

        // Simple gate with closure
        Gate::define('update-settings', function (User $user) {
            return $user->is_admin;
        });

        // Gate with additional parameters
        Gate::define('view-salary', function (User $user, User $employee) {
            return $user->id === $employee->manager_id;
        });

        // Gate that doesn't require authentication
        Gate::define('view-public-content', function (?User $user) {
            return true; // Anyone can view public content
        });

        // Gate with multiple conditions
        Gate::define('delete-post', function (User $user, $post) {
            return $user->id === $post->user_id || $user->hasRole('moderator');
        });
    }
}
```

### 🎭 Using Class-Based Gates

For complex logic, use class-based gates:

```php
// Create gate class
<?php

namespace App\Gates;

use App\Models\User;

class AdminGate
{
    public function manage(User $user)
    {
        return $user->is_admin && $user->email_verified_at;
    }

    public function viewReports(User $user)
    {
        return $user->hasRole(['admin', 'manager']) 
               && $user->department === 'finance';
    }
}

// Register in AuthServiceProvider
Gate::define('admin.manage', [AdminGate::class, 'manage']);
Gate::define('admin.view-reports', [AdminGate::class, 'viewReports']);
```

### ✅ Checking Gates

```php
// In controllers
public function updateSettings()
{
    if (Gate::allows('update-settings')) {
        // User can update settings
    }

    if (Gate::denies('update-settings')) {
        // User cannot update settings
        abort(403);
    }

    // With parameters
    if (Gate::allows('view-salary', $employee)) {
        return view('employee.salary', compact('employee'));
    }
}

// Using authorize helper
public function destroy(Post $post)
{
    $this->authorize('delete-post', $post);
    
    $post->delete();
    return redirect()->route('posts.index');
}

// In models or services
if (auth()->user()->can('update-settings')) {
    // Perform action
}
```

### 🔄 Gate Responses

Return more detailed responses:

```php
Gate::define('edit-post', function (User $user, Post $post) {
    if ($user->id !== $post->user_id) {
        return Gate::deny('You can only edit your own posts.');
    }

    if ($post->published_at && !$user->can('edit-published-posts')) {
        return Gate::deny('Published posts cannot be edited.');
    }

    return Gate::allow();
});

// Using the response
$response = Gate::inspect('edit-post', $post);

if ($response->allowed()) {
    // Edit the post
} else {
    echo $response->message(); // "You can only edit your own posts."
}
```

---

## 📋 Policies Deep Dive

### 🏗️ Creating Policies

Generate a policy using Artisan:

```bash
# Generate policy for Post model
php artisan make:policy PostPolicy

# Generate policy with model methods
php artisan make:policy PostPolicy --model=Post

# Generate policy in subdirectory
php artisan make:policy Admin/UserPolicy
```

### 📝 Policy Structure

```php
<?php

namespace App\Policies;

use App\Models\User;
use App\Models\Post;
use Illuminate\Auth\Access\HandlesAuthorization;
use Illuminate\Auth\Access\Response;

class PostPolicy
{
    use HandlesAuthorization;

    /**
     * Determine whether the user can view any posts.
     */
    public function viewAny(User $user)
    {
        return $user->hasRole(['admin', 'editor', 'author']);
    }

    /**
     * Determine whether the user can view the post.
     */
    public function view(?User $user, Post $post)
    {
        // Anyone can view published posts
        if ($post->published) {
            return true;
        }

        // Only authenticated users can view drafts they own
        return $user && $user->id === $post->user_id;
    }

    /**
     * Determine whether the user can create posts.
     */
    public function create(User $user)
    {
        return $user->hasRole(['admin', 'editor', 'author']);
    }

    /**
     * Determine whether the user can update the post.
     */
    public function update(User $user, Post $post)
    {
        if ($user->hasRole('admin')) {
            return Response::allow();
        }

        if ($user->id !== $post->user_id) {
            return Response::deny('You can only edit your own posts.');
        }

        if ($post->published && !$user->hasRole('editor')) {
            return Response::deny('Authors cannot edit published posts.');
        }

        return Response::allow();
    }

    /**
     * Determine whether the user can delete the post.
     */
    public function delete(User $user, Post $post)
    {
        return $user->hasRole('admin') || 
               ($user->id === $post->user_id && !$post->published);
    }

    /**
     * Determine whether the user can restore the post.
     */
    public function restore(User $user, Post $post)
    {
        return $user->hasRole(['admin', 'editor']);
    }

    /**
     * Determine whether the user can permanently delete the post.
     */
    public function forceDelete(User $user, Post $post)
    {
        return $user->hasRole('admin');
    }

    /**
     * Custom method for publishing posts.
     */
    public function publish(User $user, Post $post)
    {
        return $user->hasRole(['admin', 'editor']) ||
               ($user->id === $post->user_id && $user->hasRole('author'));
    }
}
```

### 🔗 Registering Policies

In `AuthServiceProvider`:

```php
<?php

namespace App\Providers;

use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;
use App\Models\Post;
use App\Policies\PostPolicy;

class AuthServiceProvider extends ServiceProvider
{
    /**
     * The policy mappings for the application.
     */
    protected $policies = [
        Post::class => PostPolicy::class,
        'App\Models\Comment' => 'App\Policies\CommentPolicy',
    ];

    public function boot()
    {
        $this->registerPolicies();

        // Auto-discover policies (Laravel 8+)
        Gate::guessPolicyNamesUsing(function ($modelClass) {
            return 'App\\Policies\\' . class_basename($modelClass) . 'Policy';
        });
    }
}
```

### ✅ Using Policies

```php
// In controllers
class PostController extends Controller
{
    public function index()
    {
        $this->authorize('viewAny', Post::class);
        
        return view('posts.index', [
            'posts' => Post::published()->get()
        ]);
    }

    public function show(Post $post)
    {
        $this->authorize('view', $post);
        
        return view('posts.show', compact('post'));
    }

    public function edit(Post $post)
    {
        $this->authorize('update', $post);
        
        return view('posts.edit', compact('post'));
    }

    public function update(Request $request, Post $post)
    {
        $this->authorize('update', $post);
        
        $post->update($request->validated());
        
        return redirect()->route('posts.show', $post);
    }

    public function publish(Post $post)
    {
        $this->authorize('publish', $post);
        
        $post->update(['published' => true, 'published_at' => now()]);
        
        return back()->with('success', 'Post published successfully!');
    }
}
```

---

## 🎨 Blade Directives

### 🏷️ Using Authorization in Templates

```blade
{{-- Basic can/cannot directives --}}
@can('create', App\Models\Post::class)
    <a href="{{ route('posts.create') }}" class="btn btn-primary">
        Create New Post
    </a>
@endcan

@cannot('update', $post)
    <p class="text-muted">You cannot edit this post.</p>
@endcannot

{{-- Using else clauses --}}
@can('delete', $post)
    <button type="submit" class="btn btn-danger">Delete Post</button>
@else
    <button type="button" class="btn btn-secondary" disabled>
        Cannot Delete
    </button>
@endcan

{{-- Multiple conditions --}}
@canany(['update', 'delete'], $post)
    <div class="post-actions">
        @can('update', $post)
            <a href="{{ route('posts.edit', $post) }}">Edit</a>
        @endcan
        
        @can('delete', $post)
            <form method="POST" action="{{ route('posts.destroy', $post) }}">
                @csrf @method('DELETE')
                <button type="submit">Delete</button>
            </form>
        @endcan
    </div>
@endcanany

{{-- Using gates in templates --}}
@can('admin-panel')
    <a href="/admin">Admin Panel</a>
@endcan

{{-- Unless directives --}}
@unless(auth()->user()->can('view', $post))
    <p>This post is private.</p>
@endunless
```

---

## 🔧 Advanced Patterns

### 🎭 Policy Filters

Policy filters run before all other authorization checks:

```php
class PostPolicy
{
    /**
     * Filter that runs before all other methods.
     */
    public function before(User $user, $ability)
    {
        // Super admins can do anything
        if ($user->hasRole('super-admin')) {
            return true;
        }

        // Blocked users can't do anything
        if ($user->is_blocked) {
            return false;
        }

        // Continue with normal policy checks
        return null;
    }

    // ... other policy methods
}
```

### 🏢 Resource-Based Authorization

```php
class ProjectPolicy
{
    public function view(User $user, Project $project)
    {
        return $user->id === $project->owner_id ||
               $project->members->contains($user->id) ||
               $project->is_public;
    }

    public function update(User $user, Project $project)
    {
        return $user->id === $project->owner_id ||
               $project->members()
                   ->wherePivot('role', 'admin')
                   ->get()
                   ->contains($user);
    }

    public function invite(User $user, Project $project)
    {
        return $this->update($user, $project) &&
               $project->members()->count() < $project->max_members;
    }
}
```

### 🔄 Dynamic Policies

```php
class DynamicPolicy
{
    public function manage(User $user, $resource, $action)
    {
        $permission = strtolower(class_basename($resource)) . '.' . $action;
        
        return $user->hasPermissionTo($permission);
    }
}

// Register dynamic policy
Gate::define('dynamic.manage', [DynamicPolicy::class, 'manage']);

// Usage
$this->authorize('dynamic.manage', [$post, 'update']);
```

---

## 🏗️ Model Integration

### 🎯 Authorization in Models

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;

class Post extends Model
{
    use SoftDeletes;

    /**
     * Check if the current user can edit this post.
     */
    public function canBeEditedBy(?User $user = null)
    {
        $user = $user ?? auth()->user();
        
        return $user && $user->can('update', $this);
    }

    /**
     * Check if this post is visible to the current user.
     */
    public function isVisibleTo(?User $user = null)
    {
        return auth()->user()?->can('view', $this) ?? $this->published;
    }

    /**
     * Scope for posts that the current user can view.
     */
    public function scopeViewable($query, ?User $user = null)
    {
        $user = $user ?? auth()->user();

        if (!$user) {
            return $query->where('published', true);
        }

        return $query->where(function ($query) use ($user) {
            $query->where('published', true)
                  ->orWhere('user_id', $user->id);
        });
    }

    /**
     * Get the route key name for Laravel.
     */
    public function getRouteKeyName()
    {
        return 'slug';
    }
}
```

### 🔐 Automatic Authorization

```php
// In RouteServiceProvider or web.php
Route::model('post', Post::class);

// Middleware for automatic authorization
class AuthorizeResourceMiddleware
{
    public function handle($request, Closure $next, $resource, $ability = null)
    {
        $ability = $ability ?: $this->mapRouteToAbility($request->route()->getActionMethod());
        
        $model = $request->route($resource);
        
        if ($model) {
            $request->user()->can($ability, $model) ?: abort(403);
        }
        
        return $next($request);
    }

    private function mapRouteToAbility($method)
    {
        return [
            'index' => 'viewAny',
            'show' => 'view',
            'create' => 'create',
            'store' => 'create',
            'edit' => 'update',
            'update' => 'update',
            'destroy' => 'delete',
        ][$method] ?? 'view';
    }
}
```

---

## 🧪 Testing Authorization

### 🔬 Testing Gates

```php
<?php

namespace Tests\Feature;

use Tests\TestCase;
use App\Models\User;
use App\Models\Post;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Support\Facades\Gate;

class AuthorizationTest extends TestCase
{
    use RefreshDatabase;

    /** @test */
    public function admin_can_update_settings()
    {
        $admin = User::factory()->admin()->create();
        
        $this->assertTrue(Gate::forUser($admin)->allows('update-settings'));
    }

    /** @test */
    public function regular_user_cannot_update_settings()
    {
        $user = User::factory()->create();
        
        $this->assertTrue(Gate::forUser($user)->denies('update-settings'));
    }

    /** @test */
    public function manager_can_view_employee_salary()
    {
        $manager = User::factory()->create();
        $employee = User::factory()->create(['manager_id' => $manager->id]);
        
        $this->assertTrue(
            Gate::forUser($manager)->allows('view-salary', $employee)
        );
    }
}
```

### 🔬 Testing Policies

```php
<?php

namespace Tests\Unit;

use Tests\TestCase;
use App\Models\User;
use App\Models\Post;
use App\Policies\PostPolicy;
use Illuminate\Foundation\Testing\RefreshDatabase;

class PostPolicyTest extends TestCase
{
    use RefreshDatabase;

    private PostPolicy $policy;

    protected function setUp(): void
    {
        parent::setUp();
        $this->policy = new PostPolicy();
    }

    /** @test */
    public function owner_can_update_own_post()
    {
        $user = User::factory()->create();
        $post = Post::factory()->create(['user_id' => $user->id]);

        $this->assertTrue($this->policy->update($user, $post));
    }

    /** @test */
    public function user_cannot_update_others_post()
    {
        $user = User::factory()->create();
        $otherUser = User::factory()->create();
        $post = Post::factory()->create(['user_id' => $otherUser->id]);

        $response = $this->policy->update($user, $post);
        
        $this->assertFalse($response->allowed());
        $this->assertEquals('You can only edit your own posts.', $response->message());
    }

    /** @test */
    public function admin_can_update_any_post()
    {
        $admin = User::factory()->admin()->create();
        $post = Post::factory()->create();

        $this->assertTrue($this->policy->update($admin, $post));
    }

    /** @test */
    public function anyone_can_view_published_posts()
    {
        $post = Post::factory()->published()->create();

        $this->assertTrue($this->policy->view(null, $post));
    }

    /** @test */
    public function only_owner_can_view_draft_posts()
    {
        $user = User::factory()->create();
        $otherUser = User::factory()->create();
        $post = Post::factory()->draft()->create(['user_id' => $user->id]);

        $this->assertTrue($this->policy->view($user, $post));
        $this->assertFalse($this->policy->view($otherUser, $post));
        $this->assertFalse($this->policy->view(null, $post));
    }
}
```

### 🎭 Feature Testing

```php
<?php

namespace Tests\Feature;

use Tests\TestCase;
use App\Models\User;
use App\Models\Post;
use Illuminate\Foundation\Testing\RefreshDatabase;

class PostControllerTest extends TestCase
{
    use RefreshDatabase;

    /** @test */
    public function authorized_user_can_create_post()
    {
        $user = User::factory()->author()->create();

        $response = $this->actingAs($user)
            ->post('/posts', [
                'title' => 'Test Post',
                'content' => 'Test content'
            ]);

        $response->assertRedirect();
        $this->assertDatabaseHas('posts', ['title' => 'Test Post']);
    }

    /** @test */
    public function unauthorized_user_cannot_create_post()
    {
        $user = User::factory()->create(); // Regular user without author role

        $response = $this->actingAs($user)
            ->post('/posts', [
                'title' => 'Test Post',
                'content' => 'Test content'
            ]);

        $response->assertStatus(403);
    }

    /** @test */
    public function owner_can_edit_own_post()
    {
        $user = User::factory()->author()->create();
        $post = Post::factory()->create(['user_id' => $user->id]);

        $response = $this->actingAs($user)
            ->get("/posts/{$post->id}/edit");

        $response->assertOk();
    }

    /** @test */
    public function user_cannot_edit_others_post()
    {
        $user = User::factory()->author()->create();
        $otherUser = User::factory()->author()->create();
        $post = Post::factory()->create(['user_id' => $otherUser->id]);

        $response = $this->actingAs($user)
            ->get("/posts/{$post->id}/edit");

        $response->assertStatus(403);
    }
}
```

---

## 🏢 Real-World Examples

### 📝 Blog System Authorization

```php
class BlogPolicy
{
    public function viewAny(User $user)
    {
        return true; // Anyone can view the blog list
    }

    public function view(?User $user, Post $post)
    {
        if ($post->status === 'published') {
            return true;
        }

        if (!$user) {
            return false;
        }

        // Authors can view their own drafts
        if ($user->id === $post->author_id) {
            return true;
        }

        // Editors can view all drafts
        return $user->hasRole(['editor', 'admin']);
    }

    public function create(User $user)
    {
        return $user->hasVerifiedEmail() && 
               $user->hasRole(['author', 'editor', 'admin']);
    }

    public function update(User $user, Post $post)
    {
        // Admin can edit anything
        if ($user->hasRole('admin')) {
            return true;
        }

        // Editors can edit published posts
        if ($user->hasRole('editor') && $post->status === 'published') {
            return true;
        }

        // Authors can only edit their own drafts
        return $user->id === $post->author_id && $post->status === 'draft';
    }

    public function publish(User $user, Post $post)
    {
        return $user->hasRole(['editor', 'admin']) ||
               ($user->id === $post->author_id && $user->can_publish);
    }

    public function delete(User $user, Post $post)
    {
        return $user->hasRole('admin') ||
               ($user->id === $post->author_id && $post->status === 'draft');
    }
}
```

### 🏪 E-commerce Authorization

```php
class OrderPolicy
{
    public function viewAny(User $user)
    {
        return $user->hasRole(['admin', 'staff']);
    }

    public function view(User $user, Order $order)
    {
        return $user->id === $order->user_id ||
               $user->hasRole(['admin', 'staff']);
    }

    public function create(User $user)
    {
        return $user->hasVerifiedEmail() && !$user->is_suspended;
    }

    public function cancel(User $user, Order $order)
    {
        // Can't cancel if already processing
        if (in_array($order->status, ['processing', 'shipped', 'delivered'])) {
            return Response::deny('Order cannot be cancelled at this stage.');
        }

        // Users can cancel their own orders within time limit
        if ($user->id === $order->user_id) {
            $timeLimit = $order->created_at->addMinutes(30);
            
            if (now()->lessThan($timeLimit)) {
                return Response::allow();
            } else {
                return Response::deny('Cancellation period has expired.');
            }
        }

        // Staff can cancel any order
        return $user->hasRole(['admin', 'staff']);
    }

    public function refund(User $user, Order $order)
    {
        return $user->hasRole(['admin', 'manager']) &&
               $order->status === 'delivered' &&
               $order->payment_status === 'paid';
    }
}

class ProductPolicy
{
    public function viewAny(?User $user)
    {
        return true; // Public catalog
    }

    public function view(?User $user, Product $product)
    {
        return $product->is_active || 
               ($user && $user->hasRole(['admin', 'staff']));
    }

    public function create(User $user)
    {
        return $user->hasRole(['admin', 'manager']);
    }

    public function update(User $user, Product $product)
    {
        return $user->hasRole(['admin', 'manager']) ||
               ($user->hasRole('staff') && $user->department === 'catalog');
    }

    public function delete(User $user, Product $product)
    {
        if ($product->orders()->count() > 0) {
            return Response::deny('Cannot delete product with existing orders.');
        }

        return $user->hasRole(['admin', 'manager']);
    }
}
```

### 🏢 Project Management System

```php
class ProjectPolicy
{
    public function viewAny(User $user)
    {
        return $user->hasRole(['admin', 'manager', 'employee']);
    }

    public function view(User $user, Project $project)
    {
        return $this->userBelongsToProject($user, $project) ||
               $user->hasRole(['admin', 'manager']);
    }

    public function create(User $user)
    {
        return $user->hasRole(['admin', 'manager']);
    }

    public function update(User $user, Project $project)
    {
        return $user->id === $project->owner_id ||
               $this->userHasProjectRole($user, $project, ['admin', 'lead']) ||
               $user->hasRole('admin');
    }

    public function delete(User $user, Project $project)
    {
        return $user->id === $project->owner_id || $user->hasRole('admin');
    }

    public function addMember(User $user, Project $project)
    {
        return $this->update($user, $project);
    }

    public function removeMember(User $user, Project $project, User $member)
    {
        // Can't remove project owner
        if ($member->id === $project->owner_id) {
            return Response::deny('Cannot remove project owner.');
        }

        return $this->update($user, $project);
    }

    public function assignTask(User $user, Project $project)
    {
        return $this->userHasProjectRole($user, $project, ['admin', 'lead', 'member']);
    }

    private function userBelongsToProject(User $user, Project $project)
    {
        return $project->members()->where('user_id', $user->id)->exists();
    }

    private function userHasProjectRole(User $user, Project $project, array $roles)
    {
        return $project->members()
            ->where('user_id', $user->id)
            ->whereIn('role', $roles)
            ->exists();
    }
}
```

---

## ⚡ Performance Optimization

### 🚀 Caching Authorization Results

```php
class CachedAuthorizationService
{
    public function canUserPerformAction(User $user, string $action, $resource = null)
    {
        $cacheKey = $this->getCacheKey($user, $action, $resource);
        
        return Cache::remember($cacheKey, 300, function () use ($user, $action, $resource) {
            return $user->can($action, $resource);
        });
    }

    private function getCacheKey(User $user, string $action, $resource)
    {
        $resourceKey = $resource ? get_class($resource) . ':' . $resource->getKey() : 'null';
        
        return "auth:{$user->id}:{$action}:{$resourceKey}";
    }

    public function clearUserCache(User $user)
    {
        Cache::flush(); // Or use tags if supported
    }
}
```

### 🔍 Eager Loading for Authorization

```php
class PostController extends Controller
{
    public function index()
    {
        // Eager load relationships needed for authorization
        $posts = Post::with(['user', 'category'])
            ->viewable()
            ->paginate(15);

        return view('posts.index', compact('posts'));
    }

    public function show(Post $post)
    {
        // Load relationships before authorization checks
        $post->load(['user', 'comments.user', 'tags']);
        
        $this->authorize('view', $post);
        
        return view('posts.show', compact('post'));
    }
}
```

---

## 🛠️ Advanced Techniques

### 🎭 Custom Authorization Responses

```php
class CustomPolicy
{
    public function update(User $user, Post $post)
    {
        if ($user->is_suspended) {
            return Response::deny('Your account is suspended.', 423);
        }

        if (!$user->hasVerifiedEmail()) {
            return Response::deny('Please verify your email first.', 403);
        }

        if ($user->id !== $post->user_id) {
            return Response::deny('You can only edit your own posts.', 403);
        }

        return Response::allow();
    }
}

// Handle in exception handler
class Handler extends ExceptionHandler
{
    public function render($request, Throwable $exception)
    {
        if ($exception instanceof AuthorizationException) {
            if ($request->expectsJson()) {
                return response()->json([
                    'message' => $exception->getMessage(),
                    'code' => $exception->getCode() ?: 403
                ], $exception->getCode() ?: 403);
            }
            
            return redirect()->back()
                ->with('error', $exception->getMessage());
        }

        return parent::render($request, $exception);
    }
}
```

### 🔄 Dynamic Policy Registration

```php
class DynamicPolicyProvider extends ServiceProvider
{
    public function boot()
    {
        $this->registerDynamicPolicies();
    }

    private function registerDynamicPolicies()
    {
        // Auto-register policies based on models
        $modelPath = app_path('Models');
        $policyPath = app_path('Policies');

        collect(File::allFiles($modelPath))
            ->map(function ($file) use ($policyPath) {
                $modelClass = 'App\\Models\\' . $file->getFilenameWithoutExtension();
                $policyClass = 'App\\Policies\\' . $file->getFilenameWithoutExtension() . 'Policy';
                
                if (class_exists($policyClass)) {
                    Gate::policy($modelClass, $policyClass);
                }
            });
    }
}
```

---

## 🚨 Common Pitfalls & Solutions

### ❌ Common Mistakes

#### 1. Forgetting Guest Users
```php
// ❌ Wrong - will throw error for guest users
public function view(User $user, Post $post)
{
    return $user->id === $post->user_id;
}

// ✅ Correct - handle guest users
public function view(?User $user, Post $post)
{
    if ($post->is_public) {
        return true;
    }
    
    return $user && $user->id === $post->user_id;
}
```

#### 2. N+1 Query Problems
```php
// ❌ Wrong - causes N+1 queries
@foreach($posts as $post)
    @can('update', $post)
        <a href="/posts/{{ $post->id }}/edit">Edit</a>
    @endcan
@endforeach

// ✅ Correct - eager load relationships
$posts = Post::with('user')->get();
```

#### 3. Not Using Policy Filters Properly
```php
// ❌ Wrong - filter doesn't return null
public function before(User $user, $ability)
{
    if ($user->hasRole('admin')) {
        return true;
    }
    
    return false; // This blocks all other checks!
}

// ✅ Correct - return null to continue
public function before(User $user, $ability)
{
    if ($user->hasRole('admin')) {
        return true;
    }
    
    return null; // Let other methods handle the check
}
```

#### 4. Authorization Logic in Controllers
```php
// ❌ Wrong - logic in controller
public function update(Request $request, Post $post)
{
    if (auth()->user()->id !== $post->user_id && !auth()->user()->hasRole('admin')) {
        abort(403);
    }
    
    $post->update($request->all());
}

// ✅ Correct - use policies
public function update(Request $request, Post $post)
{
    $this->authorize('update', $post);
    
    $post->update($request->validated());
}
```

---

## 📊 Best Practices

### ✅ Do's

- 🎯 **Use descriptive method names** (`canEditOwnDrafts` vs `check1`)
- 🔄 **Return null in before() filters** to continue normal authorization flow
- 📊 **Eager load relationships** needed for authorization checks
- 🧪 **Write comprehensive tests** for all authorization logic
- 📝 **Document complex authorization rules** in code comments
- 🔐 **Handle guest users explicitly** with nullable User parameters
- ⚡ **Cache expensive authorization checks** when appropriate
- 🎭 **Use policy filters for cross-cutting concerns** (super admin, banned users)

### ❌ Don'ts

- 🚫 **Don't put business logic in authorization** (use services/actions)
- 🚫 **Don't ignore the return value of authorize()** - it throws exceptions
- 🚫 **Don't hardcode role names** - use constants or config
- 🚫 **Don't forget to register policies** in AuthServiceProvider
- 🚫 **Don't mix authorization with validation** - they serve different purposes
- 🚫 **Don't create overly complex policies** - break them down into smaller methods
- 🚫 **Don't skip authorization in API endpoints** - they need protection too

---

## 🔧 Integration with Other Systems

### 🛡️ Integration with Spatie Permission

```php
class IntegratedPolicy
{
    public function before(User $user, $ability)
    {
        // Super admin can do everything
        if ($user->hasRole('super-admin')) {
            return true;
        }
        
        return null;
    }
    
    public function view(User $user, Post $post)
    {
        // Check specific permission first
        if ($user->hasPermissionTo('posts.view.any')) {
            return true;
        }
        
        // Then check ownership
        return $user->id === $post->user_id;
    }
    
    public function update(User $user, Post $post)
    {
        // Combine role-based and resource-based authorization
        return $user->hasPermissionTo('posts.edit.any') ||
               ($user->hasPermissionTo('posts.edit.own') && $user->id === $post->user_id);
    }
}
```

### 🔑 API Token Authorization

```php
class APIPolicy
{
    public function view(User $user, Post $post)
    {
        // Check if using API token
        if ($user->currentAccessToken()) {
            $tokenAbilities = $user->currentAccessToken()->abilities;
            
            if (!in_array('posts:read', $tokenAbilities)) {
                return Response::deny('Token lacks required scope.');
            }
        }
        
        // Regular authorization logic
        return $this->regularViewCheck($user, $post);
    }
}
```

---

## 📱 API Authorization

### 🔐 API-Specific Policies

```php
class APIPostPolicy extends PostPolicy
{
    public function viewAny(User $user)
    {
        return $this->hasRequiredScope($user, 'posts:read');
    }
    
    public function create(User $user)
    {
        return $this->hasRequiredScope($user, 'posts:write') &&
               parent::create($user);
    }
    
    public function update(User $user, Post $post)
    {
        return $this->hasRequiredScope($user, 'posts:write') &&
               parent::update($user, $post);
    }
    
    private function hasRequiredScope(User $user, string $scope)
    {
        $token = $user->currentAccessToken();
        
        return $token && in_array($scope, $token->abilities);
    }
}

// Register API-specific policies
class AuthServiceProvider extends ServiceProvider
{
    public function boot()
    {
        $this->registerPolicies();
        
        // Use different policies for API requests
        if (request()->is('api/*')) {
            Gate::policy(Post::class, APIPostPolicy::class);
        }
    }
}
```

### 🔒 Scoped Authorization Middleware

```php
class ScopeMiddleware
{
    public function handle($request, Closure $next, ...$scopes)
    {
        if (!$request->user()) {
            return response()->json(['error' => 'Unauthenticated'], 401);
        }
        
        $token = $request->user()->currentAccessToken();
        
        if (!$token) {
            return response()->json(['error' => 'Invalid token'], 401);
        }
        
        foreach ($scopes as $scope) {
            if (!in_array($scope, $token->abilities)) {
                return response()->json([
                    'error' => "Insufficient scope. Required: {$scope}"
                ], 403);
            }
        }
        
        return $next($request);
    }
}

// Usage in routes
Route::middleware(['auth:sanctum', 'scope:posts:write'])
    ->post('/api/posts', [PostController::class, 'store']);
```

---

## 🧪 Advanced Testing Strategies

### 🎭 Testing Complex Scenarios

```php
class ComplexAuthorizationTest extends TestCase
{
    /** @test */
    public function project_member_with_admin_role_can_delete_tasks()
    {
        $user = User::factory()->create();
        $project = Project::factory()->create();
        $task = Task::factory()->create(['project_id' => $project->id]);
        
        // Add user to project with admin role
        $project->members()->attach($user->id, ['role' => 'admin']);
        
        $this->assertTrue($user->can('delete', $task));
    }
    
    /** @test */
    public function suspended_user_cannot_perform_any_actions()
    {
        $user = User::factory()->suspended()->create();
        $post = Post::factory()->create(['user_id' => $user->id]);
        
        $this->assertFalse($user->can('view', $post));
        $this->assertFalse($user->can('update', $post));
        $this->assertFalse($user->can('delete', $post));
    }
    
    /** @test */
    public function authorization_respects_time_constraints()
    {
        Carbon::setTestNow('2023-01-01 10:00:00');
        
        $user = User::factory()->create();
        $order = Order::factory()->create([
            'user_id' => $user->id,
            'created_at' => now()
        ]);
        
        // Can cancel within 30 minutes
        $this->assertTrue($user->can('cancel', $order));
        
        // Cannot cancel after 30 minutes
        Carbon::setTestNow('2023-01-01 10:31:00');
        $this->assertFalse($user->can('cancel', $order));
        
        Carbon::setTestNow();
    }
}
```

### 🔬 Testing Authorization Responses

```php
/** @test */
public function authorization_returns_meaningful_error_messages()
{
    $user = User::factory()->create();
    $publishedPost = Post::factory()->published()->create();
    
    $response = Gate::forUser($user)->inspect('update', $publishedPost);
    
    $this->assertFalse($response->allowed());
    $this->assertEquals('You cannot edit published posts.', $response->message());
}

/** @test */
public function policy_filter_prevents_suspended_users_from_any_action()
{
    $suspendedUser = User::factory()->suspended()->create();
    $post = Post::factory()->create(['user_id' => $suspendedUser->id]);
    
    // Even though they own the post, suspended users can't access it
    $this->assertFalse($suspendedUser->can('view', $post));
}
```

---

## 🎯 Real-World Implementation Guide

### 🏗️ Step-by-Step Implementation

#### Step 1: Define Your Authorization Requirements
```php
// Document your authorization matrix
/*
Posts Authorization Matrix:
- view: public posts (anyone), draft posts (owner + editors + admin)
- create: authenticated users with author role
- update: owner (if draft), editors (any), admin (any)
- delete: owner (if draft), admin (any)
- publish: editors + admin, authors with publish permission
- unpublish: editors + admin
*/
```

#### Step 2: Create Base Policy Structure
```php
php artisan make:policy PostPolicy --model=Post

// Add to AuthServiceProvider
protected $policies = [
    Post::class => PostPolicy::class,
];
```

#### Step 3: Implement Policy Methods
```php
class PostPolicy
{
    use HandlesAuthorization;
    
    public function before(User $user, $ability)
    {
        if ($user->isSuspended()) {
            return Response::deny('Account suspended.');
        }
        
        if ($user->hasRole('super-admin')) {
            return Response::allow();
        }
        
        return null;
    }
    
    // Implement each method based on your authorization matrix
}
```

#### Step 4: Add Authorization to Controllers
```php
class PostController extends Controller
{
    public function __construct()
    {
        $this->authorizeResource(Post::class, 'post');
    }
    
    // Controllers methods will automatically check corresponding policy methods
}
```

#### Step 5: Update Views with Authorization
```blade
@can('create', App\Models\Post::class)
    <a href="{{ route('posts.create') }}">New Post</a>
@endcan

@can('update', $post)
    <a href="{{ route('posts.edit', $post) }}">Edit</a>
@endcan
```

#### Step 6: Write Comprehensive Tests
```php
// Test every scenario in your authorization matrix
```

---

## 📚 Additional Resources

### 📖 Official Documentation
- **Laravel Authorization**: [https://laravel.com/docs/authorization](https://laravel.com/docs/authorization)
- **Gates Documentation**: [https://laravel.com/docs/authorization#gates](https://laravel.com/docs/authorization#gates)
- **Policies Documentation**: [https://laravel.com/docs/authorization#creating-policies](https://laravel.com/docs/authorization#creating-policies)

### 🛠️ Useful Packages
- **Laravel Permission**: [https://github.com/spatie/laravel-permission](https://github.com/spatie/laravel-permission)
- **Laravel Bouncer**: [https://github.com/JosephSilber/bouncer](https://github.com/JosephSilber/bouncer)
- **Laravel Authorization**: [https://github.com/illuminated/authorization](https://github.com/illuminated/authorization)

### 📺 Learning Resources
- **Laracasts**: Authorization series
- **Laravel Daily**: Authorization tutorials
- **YouTube**: "Laravel Authorization" tutorials
- **Laravel News**: Authorization articles

---

## 🎯 Quick Reference

### 🚀 Quick Commands

```php
// Generate Policy
php artisan make:policy PostPolicy --model=Post

// Check Authorization
$user->can('update', $post);
Gate::allows('admin-panel');
$this->authorize('view', $post);

// In Blade
@can('update', $post) ... @endcan
@cannot('delete', $post) ... @endcannot

// Policy Methods
public function view(?User $user, Post $post) { }
public function create(User $user) { }
public function update(User $user, Post $post) { }
public function delete(User $user, Post $post) { }

// Responses
return Response::allow();
return Response::deny('Custom message');
return true; // Allow
return false; // Deny
return null; // Continue to next check
```

### 📊 Authorization Flow

```
Request → Middleware → Controller → authorize() → Policy/Gate → Response
                                        ↓
                              before() filter (if exists)
                                        ↓
                              Specific method check
                                        ↓
                              Allow/Deny/Continue
```

---

## 🔍 Debugging Authorization

### 🐛 Debug Techniques

```php
// Enable query logging to see authorization queries
DB::enableQueryLog();

// Use Gate::inspect() to get detailed responses
$response = Gate::forUser($user)->inspect('update', $post);
dd($response->allowed(), $response->message(), $response->code());

// Log authorization checks
Gate::after(function ($user, $ability, $result, $arguments) {
    Log::debug('Authorization check', [
        'user' => $user->id ?? 'guest',
        'ability' => $ability,
        'result' => $result,
        'arguments' => $arguments
    ]);
});

// Use debugbar to monitor authorization
if (app()->environment('local')) {
    Gate::before(function ($user, $ability) {
        debugbar()->info("Checking {$ability} for user " . ($user->id ?? 'guest'));
    });
}
```

---

*⚖️ This comprehensive guide covers Laravel Gates & Policies for fine-grained access control. Always test your authorization logic thoroughly and refer to the official Laravel documentation for the latest updates and best practices.*