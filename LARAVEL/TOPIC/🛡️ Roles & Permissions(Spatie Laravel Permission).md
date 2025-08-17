# 🛡️ Spatie Laravel Permission - Complete Guide

## 📋 Overview

Spatie Laravel Permission is the most popular and robust package for managing user roles and permissions in Laravel applications. It provides a flexible way to implement role-based access control (RBAC) with support for multiple guards, teams, and hierarchical permissions.

---

## 🌟 What is Spatie Laravel Permission?

Laravel Permission is a powerful package that allows you to manage user permissions and roles in a database. It's designed to be flexible and easy to use, supporting complex permission structures while maintaining simplicity for basic use cases.

### ✨ Key Features

- 🔐 **Role-Based Access Control (RBAC)**
- 👥 **User Permissions Management**
- 🏢 **Team/Multi-tenancy Support**
- 🛡️ **Multiple Guard Support**
- 🔄 **Permission Inheritance**
- 📊 **Hierarchical Roles**
- ⚡ **Performance Optimized**
- 🧪 **Extensive Testing**

---

## 🛠️ Installation & Setup

### 📦 Install the Package

```bash
# Install via Composer
composer require spatie/laravel-permission

# Publish the migration
php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider"

# Run the migrations
php artisan migrate

# Clear application cache
php artisan optimize:clear
# or
php artisan config:cache
```

### ⚙️ Configuration

The package will automatically register its service provider. You can publish the config file:

```bash
php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider" --tag="config"
```

---

## 🏗️ Basic Setup

### 👤 User Model Configuration

Add the `HasRoles` trait to your User model:

```php
<?php

namespace App\Models;

use Illuminate\Foundation\Auth\User as Authenticatable;
use Spatie\Permission\Traits\HasRoles;

class User extends Authenticatable
{
    use HasRoles;

    // ... rest of your User model
}
```

### 🗄️ Database Structure

The package creates these tables:
- `roles` - Stores role definitions
- `permissions` - Stores permission definitions  
- `model_has_permissions` - User-specific permissions
- `model_has_roles` - User-role assignments
- `role_has_permissions` - Role-permission assignments

---

## 🎯 Core Concepts

### 🔑 Permissions

Permissions are the most granular level of access control:

```php
use Spatie\Permission\Models\Permission;

// Create permissions
Permission::create(['name' => 'edit articles']);
Permission::create(['name' => 'delete articles']);
Permission::create(['name' => 'publish articles']);
Permission::create(['name' => 'unpublish articles']);
```

### 👑 Roles

Roles are collections of permissions:

```php
use Spatie\Permission\Models\Role;

// Create roles
$role = Role::create(['name' => 'writer']);
$role = Role::create(['name' => 'editor']);
$role = Role::create(['name' => 'admin']);

// Assign permissions to role
$role = Role::findByName('writer');
$role->givePermissionTo('edit articles');

// Multiple permissions at once
$role->givePermissionTo(['edit articles', 'delete articles']);
```

---

## 👥 User Management

### 🎭 Assigning Roles

```php
// Assign role to user
$user = User::find(1);
$user->assignRole('writer');

// Multiple roles
$user->assignRole(['writer', 'editor']);

// Using role object
$role = Role::findByName('writer');
$user->assignRole($role);
```

### 🔐 Assigning Permissions

```php
// Direct permission assignment
$user->givePermissionTo('edit articles');

// Multiple permissions
$user->givePermissionTo(['edit articles', 'delete articles']);

// Using permission object
$permission = Permission::findByName('edit articles');
$user->givePermissionTo($permission);
```

### ❌ Revoking Access

```php
// Revoke role
$user->removeRole('writer');

// Revoke permission
$user->revokePermissionTo('edit articles');

// Revoke all roles
$user->syncRoles([]);

// Revoke all permissions
$user->syncPermissions([]);
```

---

## 🔍 Checking Permissions

### ✅ Permission Checks

```php
// Check if user has permission
if ($user->can('edit articles')) {
    // User can edit articles
}

// Check if user has role
if ($user->hasRole('writer')) {
    // User is a writer
}

// Check any role
if ($user->hasAnyRole(['writer', 'editor'])) {
    // User has at least one of these roles
}

// Check all roles
if ($user->hasAllRoles(['writer', 'editor'])) {
    // User has both roles
}

// Check exact roles
if ($user->hasExactRoles(['writer', 'editor'])) {
    // User has exactly these roles and no others
}
```

### 🔗 Permission via Role

```php
// Check permission via role
if ($user->hasPermissionTo('edit articles')) {
    // User has permission either directly or via role
}

// Get all permissions
$permissions = $user->getAllPermissions();

// Get permissions via roles
$permissions = $user->getPermissionsViaRoles();

// Get direct permissions
$permissions = $user->getDirectPermissions();
```

---

## 🛡️ Middleware Protection

### 🚧 Built-in Middleware

Register middleware in `app/Http/Kernel.php`:

```php
protected $routeMiddleware = [
    // ...
    'role' => \Spatie\Permission\Middlewares\RoleMiddleware::class,
    'permission' => \Spatie\Permission\Middlewares\PermissionMiddleware::class,
    'role_or_permission' => \Spatie\Permission\Middlewares\RoleOrPermissionMiddleware::class,
];
```

### 🛤️ Using Middleware in Routes

```php
// Protect routes with roles
Route::group(['middleware' => ['role:admin']], function () {
    Route::get('/admin', 'AdminController@index');
});

// Protect with permissions
Route::group(['middleware' => ['permission:edit articles']], function () {
    Route::get('/articles/edit', 'ArticleController@edit');
});

// Multiple roles (OR condition)
Route::group(['middleware' => ['role:admin|editor']], function () {
    Route::get('/dashboard', 'DashboardController@index');
});

// Multiple permissions (AND condition)
Route::group(['middleware' => ['permission:edit articles,delete articles']], function () {
    Route::delete('/articles', 'ArticleController@destroy');
});

// Role OR Permission
Route::group(['middleware' => ['role_or_permission:admin|edit articles']], function () {
    Route::get('/articles', 'ArticleController@index');
});
```

---

## 🎨 Blade Directives

### 🏷️ Template Protection

```blade
{{-- Check roles --}}
@role('admin')
    <p>You are an admin!</p>
@endrole

@hasrole('admin')
    <p>You are an admin!</p>
@endhasrole

{{-- Check permissions --}}
@can('edit articles')
    <a href="/articles/edit">Edit Articles</a>
@endcan

@cannot('delete articles')
    <p>You cannot delete articles</p>
@endcannot

{{-- Check any role --}}
@hasanyrole('admin|editor')
    <p>You are either admin or editor</p>
@endhasanyrole

{{-- Check all roles --}}
@hasallroles('admin|editor')
    <p>You are both admin and editor</p>
@endhasallroles

{{-- Unless directives --}}
@unlessrole('admin')
    <p>You are not an admin</p>
@endunlessrole
```

---

## 🏢 Multi-tenancy / Teams

### 🏗️ Setup for Teams

```php
// Migration for team-based permissions
Schema::table('roles', function (Blueprint $table) {
    $table->unsignedBigInteger('team_id')->nullable();
    $table->index('team_id', 'roles_team_id_index');
});

Schema::table('permissions', function (Blueprint $table) {
    $table->unsignedBigInteger('team_id')->nullable();
    $table->index('team_id', 'permissions_team_id_index');
});
```

### 🎯 Team-based Operations

```php
// Create team-specific role
$role = Role::create([
    'name' => 'editor',
    'team_id' => 1
]);

// Create team-specific permission
$permission = Permission::create([
    'name' => 'edit articles',
    'team_id' => 1
]);

// Set user's current team
auth()->user()->setCurrentTeam($team);

// Assign role for specific team
$user->assignRole('editor', 1);

// Check permission for current team
if ($user->can('edit articles')) {
    // User can edit articles for current team
}
```

---

## 🔧 Advanced Usage

### 📊 Permission Hierarchies

```php
// Create hierarchical permissions
Permission::create(['name' => 'articles']);
Permission::create(['name' => 'articles.create']);
Permission::create(['name' => 'articles.edit']);
Permission::create(['name' => 'articles.delete']);

// Using wildcards (requires custom implementation)
if ($user->can('articles.*')) {
    // User has all article permissions
}
```

### 🎭 Role Hierarchies

```php
// Define role hierarchy in a service provider
Gate::define('admin-level', function ($user) {
    return $user->hasRole(['super-admin', 'admin']);
});

Gate::define('editor-level', function ($user) {
    return $user->hasRole(['super-admin', 'admin', 'editor']);
});
```

### 🔄 Dynamic Permissions

```php
// Create permissions dynamically
$permissions = [
    'users.create',
    'users.edit',
    'users.delete',
    'users.view'
];

foreach ($permissions as $permission) {
    Permission::firstOrCreate(['name' => $permission]);
}

// Assign all CRUD permissions
$role = Role::findByName('admin');
$role->givePermissionTo($permissions);
```

---

## 📋 Common Patterns

### 🏭 Factory Pattern for Seeding

```php
// Database Seeder
use Spatie\Permission\Models\Role;
use Spatie\Permission\Models\Permission;

class RolesAndPermissionsSeeder extends Seeder
{
    public function run()
    {
        // Reset cached roles and permissions
        app()[\Spatie\Permission\PermissionRegistrar::class]->forgetCachedPermissions();

        // Create permissions
        Permission::create(['name' => 'edit articles']);
        Permission::create(['name' => 'delete articles']);
        Permission::create(['name' => 'publish articles']);
        Permission::create(['name' => 'unpublish articles']);

        // Create roles and assign permissions
        $role = Role::create(['name' => 'writer']);
        $role->givePermissionTo(['edit articles']);

        $role = Role::create(['name' => 'editor']);
        $role->givePermissionTo(['edit articles', 'delete articles']);

        $role = Role::create(['name' => 'admin']);
        $role->givePermissionTo(Permission::all());

        // Create users and assign roles
        $user = User::factory()->create([
            'name' => 'Admin User',
            'email' => 'admin@example.com',
        ]);
        $user->assignRole('admin');
    }
}
```

### 🎨 Form Helper Methods

```php
// Controller method for role/permission management
public function editUserPermissions(User $user)
{
    $roles = Role::all();
    $permissions = Permission::all();
    $userRoles = $user->roles->pluck('name')->toArray();
    $userPermissions = $user->permissions->pluck('name')->toArray();

    return view('admin.users.permissions', compact(
        'user', 'roles', 'permissions', 'userRoles', 'userPermissions'
    ));
}

public function updateUserPermissions(Request $request, User $user)
{
    $user->syncRoles($request->roles ?? []);
    $user->syncPermissions($request->permissions ?? []);

    return redirect()->back()->with('success', 'Permissions updated!');
}
```

---

## 🧪 Testing

### 🔬 Testing Roles and Permissions

```php
use Spatie\Permission\Models\Role;
use Spatie\Permission\Models\Permission;

/** @test */
public function user_can_edit_articles_with_permission()
{
    $user = User::factory()->create();
    $permission = Permission::create(['name' => 'edit articles']);
    
    $user->givePermissionTo($permission);
    
    $this->assertTrue($user->can('edit articles'));
}

/** @test */
public function user_with_editor_role_can_edit_articles()
{
    $user = User::factory()->create();
    $role = Role::create(['name' => 'editor']);
    $permission = Permission::create(['name' => 'edit articles']);
    
    $role->givePermissionTo($permission);
    $user->assignRole($role);
    
    $this->assertTrue($user->hasRole('editor'));
    $this->assertTrue($user->can('edit articles'));
}
```

---

## ⚡ Performance Optimization

### 🚀 Caching

```php
// Clear permission cache when roles/permissions change
app()[\Spatie\Permission\PermissionRegistrar::class]->forgetCachedPermissions();

// In config/permission.php
'cache' => [
    'expiration_time' => \DateInterval::createFromDateString('24 hours'),
    'key' => 'spatie.permission.cache',
    'model_key' => 'name',
    'store' => 'default',
],
```

### 🔍 Eager Loading

```php
// Load roles and permissions with users
$users = User::with(['roles', 'permissions'])->get();

// Load specific relationships
$user = User::with('roles.permissions')->find(1);

// Check permissions without additional queries
if ($user->roles->contains('name', 'admin')) {
    // User is admin
}
```

---

## 🎯 Real-World Examples

### 📝 Blog System Permissions

```php
// Blog permissions structure
$permissions = [
    // Posts
    'posts.create',
    'posts.edit.own',
    'posts.edit.any',
    'posts.delete.own',
    'posts.delete.any',
    'posts.publish',
    
    // Categories
    'categories.create',
    'categories.edit',
    'categories.delete',
    
    // Users
    'users.create',
    'users.edit',
    'users.delete',
    'users.view',
    
    // Settings
    'settings.edit',
];

// Create roles with specific permissions
$author = Role::create(['name' => 'author']);
$author->givePermissionTo([
    'posts.create',
    'posts.edit.own',
    'posts.delete.own'
]);

$editor = Role::create(['name' => 'editor']);
$editor->givePermissionTo([
    'posts.create',
    'posts.edit.any',
    'posts.delete.any',
    'posts.publish',
    'categories.create',
    'categories.edit'
]);

$admin = Role::create(['name' => 'admin']);
$admin->givePermissionTo(Permission::all());
```

### 🏪 E-commerce Permissions

```php
// E-commerce permission structure
$permissions = [
    // Products
    'products.create',
    'products.edit',
    'products.delete',
    'products.view',
    
    // Orders
    'orders.view',
    'orders.edit',
    'orders.fulfill',
    'orders.cancel',
    
    // Customers
    'customers.view',
    'customers.edit',
    'customers.delete',
    
    // Reports
    'reports.sales',
    'reports.inventory',
    'reports.customers',
    
    // Settings
    'settings.store',
    'settings.payment',
    'settings.shipping',
];
```

---

## 🛠️ Custom Guard Support

### 🔒 Multiple Guards

```php
// Create permissions for specific guard
Permission::create([
    'name' => 'edit articles',
    'guard_name' => 'api'
]);

// Assign role with specific guard
$user->assignRole('writer', 'api');

// Check permission for specific guard
if ($user->hasPermissionTo('edit articles', 'api')) {
    // User has API permission
}
```

---

## 🚨 Common Issues & Solutions

### ❌ Common Problems

#### Cache Issues
```php
// Always clear cache after role/permission changes
app()[\Spatie\Permission\PermissionRegistrar::class]->forgetCachedPermissions();
```

#### Migration Issues
```php
// Ensure proper order in migrations
// 1. Create permissions table
// 2. Create roles table  
// 3. Create pivot tables
// 4. Seed data
```

#### Performance Issues
```php
// Use eager loading
$users = User::with(['roles', 'permissions'])->get();

// Avoid N+1 queries in loops
foreach ($users as $user) {
    // This won't trigger additional queries if eager loaded
    echo $user->roles->pluck('name')->implode(', ');
}
```

---

## 📊 Package Comparison

| Feature | Spatie Permission | Other Packages |
|---------|-------------------|----------------|
| **Popularity** | ⭐ Most Popular | Various |
| **Documentation** | ✅ Excellent | Variable |
| **Multi-tenancy** | ✅ Built-in | Limited |
| **Performance** | ⚡ Optimized | Variable |
| **Testing** | 🧪 Comprehensive | Limited |
| **Community** | 👥 Large | Smaller |
| **Maintenance** | 🔄 Active | Variable |

---

## 📚 Advanced Topics

### 🏗️ Custom Models

```php
// Use custom Role model
use Spatie\Permission\Models\Role as SpatieRole;

class Role extends SpatieRole
{
    protected $fillable = ['name', 'guard_name', 'team_id'];
    
    public function team()
    {
        return $this->belongsTo(Team::class);
    }
}

// Register in config/permission.php
'models' => [
    'permission' => Spatie\Permission\Models\Permission::class,
    'role' => App\Models\Role::class,
],
```

### 🎭 Dynamic Role Assignment

```php
// Middleware for dynamic role assignment
class AssignRoleMiddleware
{
    public function handle($request, Closure $next, $role)
    {
        if (!auth()->user()->hasRole($role)) {
            abort(403, 'Unauthorized');
        }
        
        return $next($request);
    }
}
```

---

## 📈 Best Practices

### ✅ Do's

- 🎯 **Use descriptive permission names** (`users.edit` not `edit`)
- 🏗️ **Structure permissions hierarchically**
- 🔄 **Clear cache after changes**
- 📊 **Eager load relationships**
- 🧪 **Write tests for permissions**
- 📝 **Document your permission structure**

### ❌ Don'ts  

- 🚫 **Don't create too many roles**
- 🚫 **Avoid circular dependencies**
- 🚫 **Don't forget to clear cache**
- 🚫 **Don't ignore performance implications**
- 🚫 **Don't hardcode role/permission names**

---

## 📚 Additional Resources

- 📖 **Official Documentation**: [https://spatie.be/docs/laravel-permission](https://spatie.be/docs/laravel-permission)
- 🔧 **GitHub Repository**: [https://github.com/spatie/laravel-permission](https://github.com/spatie/laravel-permission)
- 🎥 **Video Tutorials**: Search for "Spatie Laravel Permission" on YouTube
- 💬 **Community Support**: Laravel Discord, Reddit r/laravel
- 📊 **Package Stats**: [Packagist](https://packagist.org/packages/spatie/laravel-permission)

---

## 🎯 Quick Reference

### 🚀 Quick Commands

```php
// Create
Permission::create(['name' => 'permission-name']);
Role::create(['name' => 'role-name']);

// Assign
$user->assignRole('role-name');
$user->givePermissionTo('permission-name');
$role->givePermissionTo('permission-name');

// Check
$user->hasRole('role-name');
$user->hasPermissionTo('permission-name');
$user->can('permission-name');

// Remove
$user->removeRole('role-name');
$user->revokePermissionTo('permission-name');
$role->revokePermissionTo('permission-name');

// Sync
$user->syncRoles(['role1', 'role2']);
$user->syncPermissions(['perm1', 'perm2']);
```

---

*🛡️ This comprehensive guide covers Spatie Laravel Permission package. Always refer to the official documentation for the most current information and updates.*