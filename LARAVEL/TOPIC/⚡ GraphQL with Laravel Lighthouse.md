# ⚡ GraphQL with Laravel Lighthouse Guide

## 📋 Table of Contents
- [What is GraphQL?](#-what-is-graphql)
- [What is Laravel Lighthouse?](#-what-is-laravel-lighthouse)
- [Installation & Setup](#-installation--setup)
- [Schema Definition](#-schema-definition)
- [Queries](#-queries)
- [Mutations](#-mutations)
- [Subscriptions](#-subscriptions)
- [Directives](#-directives)
- [Authentication & Authorization](#-authentication--authorization)
- [Custom Resolvers](#-custom-resolvers)
- [Database Relations](#-database-relations)
- [File Uploads](#-file-uploads)
- [Error Handling](#-error-handling)
- [Testing](#-testing)
- [Performance Optimization](#-performance-optimization)
- [Common Issues & Solutions](#-common-issues--solutions)
- [Best Practices](#-best-practices)

## 🔍 What is GraphQL?

GraphQL is a query language and runtime for APIs that allows clients to request exactly the data they need. Unlike REST APIs, GraphQL provides:

- **📊 Single Endpoint**: One URL for all operations
- **🎯 Precise Data Fetching**: Request only what you need
- **📝 Strong Type System**: Self-documenting schema
- **🔄 Real-time Subscriptions**: Live data updates
- **🚀 Introspection**: Explore API structure programmatically

## ⚡ What is Laravel Lighthouse?

Laravel Lighthouse is a GraphQL framework for Laravel that allows you to build GraphQL APIs using schema-first approach. It provides:

- **📋 Schema-First Development**: Define your API with GraphQL Schema Definition Language (SDL)
- **🎯 Automatic Query Resolution**: Built-in resolvers for common operations
- **🛡️ Authentication & Authorization**: Seamless Laravel integration
- **📊 Database Integration**: Eloquent model integration
- **🔌 Extensible**: Custom directives and resolvers

## 🚀 Installation & Setup

### Step 1: Install Lighthouse
```bash
composer require pusher/pusher-php-server
composer require pusher/pusher-php-server
composer require nuwave/lighthouse
```

### Step 2: Publish Configuration
```bash
php artisan vendor:publish --tag=lighthouse-config
php artisan vendor:publish --tag=lighthouse-schema
```

### Step 3: Install GraphQL Playground (Optional)
```bash
composer require pusher/pusher-php-server --dev
```

### 📁 config/lighthouse.php
```php
<?php

return [
    'route' => [
        'uri' => '/graphql',
        'middleware' => [
            'api',
            // 'auth:sanctum', // Uncomment for authentication
        ],
        'name' => 'graphql',
    ],

    'schema' => [
        'register' => base_path('graphql/schema.graphql'),
    ],

    'namespaces' => [
        'models' => 'App\\Models',
        'queries' => 'App\\GraphQL\\Queries',
        'mutations' => 'App\\GraphQL\\Mutations',
        'subscriptions' => 'App\\GraphQL\\Subscriptions',
        'interfaces' => 'App\\GraphQL\\Interfaces',
        'unions' => 'App\\GraphQL\\Unions',
        'scalars' => 'App\\GraphQL\\Scalars',
        'directives' => 'App\\GraphQL\\Directives',
    ],

    'security' => [
        'max_query_complexity' => 3000,
        'max_query_depth' => 15,
        'disable_introspection' => false,
    ],
];
```

### 📁 .env Configuration
```env
LIGHTHOUSE_CACHE_ENABLE=true
LIGHTHOUSE_QUERY_CACHE_ENABLE=true

# For subscriptions (optional)
PUSHER_APP_ID=your-pusher-app-id
PUSHER_APP_KEY=your-pusher-key
PUSHER_APP_SECRET=your-pusher-secret
PUSHER_APP_CLUSTER=mt1
```

## 📋 Schema Definition

### 📁 graphql/schema.graphql
```graphql
"A datetime string with format `Y-m-d H:i:s`, e.g. `2018-05-23 13:43:32`."
scalar DateTime @scalar(class: "Nuwave\\Lighthouse\\Schema\\Types\\Scalars\\DateTime")

"Indicates what fields are available at the top level of a query operation."
type Query {
    "Find a single user by ID."
    user(
        "Search by primary key."
        id: ID! @eq @rules(apply: ["required"])
    ): User @find

    "List multiple users."
    users(
        "Filters by name. Accepts SQL LIKE wildcards `%` and `_`."
        name: String @where(operator: "like")
        "Filter by email"
        email: String @where(operator: "like")
        "Order by created_at"
        orderBy: _ @orderBy(columns: ["created_at", "name"])
    ): [User!]! @paginate(defaultCount: 10)

    "Get all posts"
    posts: [Post!]! @all
    
    "Find a post by ID"
    post(id: ID! @eq): Post @find

    "Get authenticated user"
    me: User @auth
}

"Account mutation operations."
type Mutation {
    "Create a new user"
    createUser(input: CreateUserInput! @spread): User @create

    "Update an existing user"
    updateUser(
        id: ID! @rules(apply: ["required"])
        input: UpdateUserInput! @spread
    ): User @update

    "Delete a user"
    deleteUser(id: ID! @rules(apply: ["required"])): User @delete

    "Create a new post"
    createPost(input: CreatePostInput! @spread): Post @create @guard

    "Update a post"
    updatePost(
        id: ID! @rules(apply: ["required"])
        input: UpdatePostInput! @spread
    ): Post @update @can(ability: "update", find: "id")

    "Delete a post"
    deletePost(id: ID! @rules(apply: ["required"])): Post @delete @can(ability: "delete", find: "id")

    "Login user"
    login(input: LoginInput! @spread): AuthPayload! @field(resolver: "App\\GraphQL\\Mutations\\Auth@login")

    "Logout user"
    logout: LogoutResponse! @field(resolver: "App\\GraphQL\\Mutations\\Auth@logout") @guard
}

"Available subscriptions"
type Subscription {
    "Subscribe to post updates"
    postUpdated(id: ID): Post
        @subscription(class: "App\\GraphQL\\Subscriptions\\PostUpdated")

    "Subscribe to new posts"
    postCreated: Post
        @subscription(class: "App\\GraphQL\\Subscriptions\\PostCreated")
}

"User account information"
type User {
    "Unique primary key"
    id: ID!
    
    "Non-unique name"
    name: String!
    
    "Unique email address"
    email: String!
    
    "Email verification timestamp"
    email_verified_at: DateTime
    
    "Account status"
    status: UserStatus!
    
    "User profile"
    profile: Profile @hasOne
    
    "User posts"
    posts: [Post!]! @hasMany
    
    "Posts count"
    posts_count: Int! @count(relation: "posts")
    
    "User roles"
    roles: [Role!]! @belongsToMany
    
    "When the account was created"
    created_at: DateTime!
    
    "When the account was last updated"
    updated_at: DateTime!
}

"User profile information"
type Profile {
    id: ID!
    user_id: ID!
    avatar: String
    bio: String
    website: String
    location: String
    birth_date: DateTime
    user: User! @belongsTo
    created_at: DateTime!
    updated_at: DateTime!
}

"Blog post"
type Post {
    "Unique primary key"
    id: ID!
    
    "Post title"
    title: String!
    
    "Post content"
    content: String!
    
    "Post slug"
    slug: String!
    
    "Post status"
    status: PostStatus!
    
    "Featured image"
    featured_image: String
    
    "Post author"
    author: User! @belongsTo(relation: "user")
    
    "Post comments"
    comments: [Comment!]! @hasMany
    
    "Comments count"
    comments_count: Int! @count(relation: "comments")
    
    "Post tags"
    tags: [Tag!]! @belongsToMany
    
    "SEO meta data"
    meta: PostMeta @hasOne
    
    "Publication date"
    published_at: DateTime
    
    "When the post was created"
    created_at: DateTime!
    
    "When the post was last updated"
    updated_at: DateTime!
}

"Post comment"
type Comment {
    id: ID!
    post_id: ID!
    user_id: ID!
    content: String!
    status: CommentStatus!
    parent_id: ID
    post: Post! @belongsTo
    user: User! @belongsTo
    parent: Comment @belongsTo(relation: "parent")
    replies: [Comment!]! @hasMany(relation: "replies")
    created_at: DateTime!
    updated_at: DateTime!
}

"Post tag"
type Tag {
    id: ID!
    name: String!
    slug: String!
    description: String
    posts: [Post!]! @belongsToMany
    posts_count: Int! @count(relation: "posts")
    created_at: DateTime!
    updated_at: DateTime!
}

"User role"
type Role {
    id: ID!
    name: String!
    slug: String!
    description: String
    users: [User!]! @belongsToMany
    permissions: [Permission!]! @belongsToMany
    created_at: DateTime!
    updated_at: DateTime!
}

"Permission"
type Permission {
    id: ID!
    name: String!
    slug: String!
    description: String
    roles: [Role!]! @belongsToMany
    created_at: DateTime!
    updated_at: DateTime!
}

"Post meta information"
type PostMeta {
    id: ID!
    post_id: ID!
    meta_title: String
    meta_description: String
    meta_keywords: String
    og_title: String
    og_description: String
    og_image: String
    post: Post! @belongsTo
    created_at: DateTime!
    updated_at: DateTime!
}

"Authentication payload"
type AuthPayload {
    "JWT access token"
    access_token: String!
    
    "Token type (Bearer)"
    token_type: String!
    
    "Token expiration time in seconds"
    expires_in: Int!
    
    "Authenticated user"
    user: User!
}

"Logout response"
type LogoutResponse {
    "Success message"
    message: String!
    
    "Success status"
    success: Boolean!
}

# Enums
"User account status"
enum UserStatus {
    ACTIVE @enum(value: "active")
    INACTIVE @enum(value: "inactive")
    SUSPENDED @enum(value: "suspended")
}

"Post status"
enum PostStatus {
    DRAFT @enum(value: "draft")
    PUBLISHED @enum(value: "published")
    ARCHIVED @enum(value: "archived")
}

"Comment status"
enum CommentStatus {
    PENDING @enum(value: "pending")
    APPROVED @enum(value: "approved")
    REJECTED @enum(value: "rejected")
}

# Input Types
"Create user input"
input CreateUserInput {
    "User name"
    name: String! @rules(apply: ["required", "string", "max:255"])
    
    "User email"
    email: String! @rules(apply: ["required", "email", "unique:users,email"])
    
    "User password"
    password: String! @rules(apply: ["required", "min:8", "confirmed"])
    
    "Password confirmation"
    password_confirmation: String!
    
    "User status"
    status: UserStatus = ACTIVE
}

"Update user input"
input UpdateUserInput {
    "User name"
    name: String @rules(apply: ["string", "max:255"])
    
    "User email"
    email: String @rules(apply: ["email"])
    
    "User status"
    status: UserStatus
}

"Create post input"
input CreatePostInput {
    "Post title"
    title: String! @rules(apply: ["required", "string", "max:255"])
    
    "Post content"
    content: String! @rules(apply: ["required", "string"])
    
    "Post slug"
    slug: String @rules(apply: ["string", "max:255", "unique:posts,slug"])
    
    "Post status"
    status: PostStatus = DRAFT
    
    "Featured image"
    featured_image: String @rules(apply: ["url"])
    
    "Tag IDs"
    tag_ids: [ID!] @rules(apply: ["array"])
}

"Update post input"
input UpdatePostInput {
    "Post title"
    title: String @rules(apply: ["string", "max:255"])
    
    "Post content"
    content: String @rules(apply: ["string"])
    
    "Post slug"
    slug: String @rules(apply: ["string", "max:255"])
    
    "Post status"
    status: PostStatus
    
    "Featured image"
    featured_image: String @rules(apply: ["url"])
    
    "Tag IDs"
    tag_ids: [ID!] @rules(apply: ["array"])
}

"Login input"
input LoginInput {
    "User email"
    email: String! @rules(apply: ["required", "email"])
    
    "User password"
    password: String! @rules(apply: ["required"])
    
    "Remember me"
    remember: Boolean = false
}
```

## 🔍 Queries

### Basic Query Examples

#### Get Single User
```graphql
query GetUser($id: ID!) {
    user(id: $id) {
        id
        name
        email
        status
        created_at
        profile {
            avatar
            bio
            website
        }
        posts {
            id
            title
            status
            published_at
        }
        posts_count
    }
}
```

#### Get Paginated Users with Filtering
```graphql
query GetUsers($name: String, $first: Int = 10, $page: Int = 1) {
    users(name: $name, first: $first, page: $page) {
        paginatorInfo {
            currentPage
            lastPage
            total
            count
            perPage
            hasMorePages
        }
        data {
            id
            name
            email
            status
            posts_count
            created_at
        }
    }
}
```

#### Complex Nested Query
```graphql
query GetPostsWithDetails($orderBy: [PostOrderByOrderByClause!]) {
    posts(orderBy: $orderBy) {
        id
        title
        content
        slug
        status
        featured_image
        published_at
        author {
            id
            name
            email
            profile {
                avatar
                bio
            }
        }
        comments(orderBy: [{column: CREATED_AT, order: DESC}]) {
            id
            content
            status
            user {
                name
                profile {
                    avatar
                }
            }
            created_at
        }
        comments_count
        tags {
            id
            name
            slug
        }
        meta {
            meta_title
            meta_description
            og_image
        }
    }
}
```

### 📁 app/GraphQL/Queries/CustomQuery.php
```php
<?php

namespace App\GraphQL\Queries;

use App\Models\Post;
use Illuminate\Database\Eloquent\Builder;

final class PostSearch
{
    public function __invoke($_, array $args): Builder
    {
        $query = Post::query()->with(['author', 'tags']);
        
        if (isset($args['search'])) {
            $search = $args['search'];
            $query->where(function ($q) use ($search) {
                $q->where('title', 'LIKE', "%{$search}%")
                  ->orWhere('content', 'LIKE', "%{$search}%")
                  ->orWhereHas('tags', function ($tagQuery) use ($search) {
                      $tagQuery->where('name', 'LIKE', "%{$search}%");
                  });
            });
        }
        
        if (isset($args['status'])) {
            $query->where('status', $args['status']);
        }
        
        if (isset($args['author_id'])) {
            $query->where('user_id', $args['author_id']);
        }
        
        return $query;
    }
}
```

## 🔄 Mutations

### 📁 app/GraphQL/Mutations/Auth.php
```php
<?php

namespace App\GraphQL\Mutations;

use App\Models\User;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\Hash;
use Illuminate\Validation\ValidationException;
use Laravel\Sanctum\PersonalAccessToken;

final class Auth
{
    public function login($_, array $args): array
    {
        $user = User::where('email', $args['email'])->first();
        
        if (!$user || !Hash::check($args['password'], $user->password)) {
            throw ValidationException::withMessages([
                'email' => ['The provided credentials are incorrect.'],
            ]);
        }
        
        // Check if user is active
        if ($user->status !== 'active') {
            throw ValidationException::withMessages([
                'status' => ['Your account is not active.'],
            ]);
        }
        
        // Revoke existing tokens if needed
        if (!$args['remember']) {
            $user->tokens()->delete();
        }
        
        $token = $user->createToken('api-token')->plainTextToken;
        
        return [
            'access_token' => $token,
            'token_type' => 'Bearer',
            'expires_in' => config('sanctum.expiration', 60 * 24), // minutes
            'user' => $user->load(['profile', 'roles']),
        ];
    }
    
    public function logout(): array
    {
        $user = Auth::user();
        
        // Revoke current token
        $user->currentAccessToken()->delete();
        
        return [
            'message' => 'Successfully logged out',
            'success' => true,
        ];
    }
}
```

### 📁 app/GraphQL/Mutations/PostMutations.php
```php
<?php

namespace App\GraphQL\Mutations;

use App\Models\Post;
use Illuminate\Support\Str;
use Illuminate\Support\Facades\Auth;

final class PostMutations
{
    public function create($_, array $args): Post
    {
        $input = $args['input'];
        
        // Generate slug if not provided
        if (empty($input['slug'])) {
            $input['slug'] = Str::slug($input['title']);
        }
        
        // Ensure slug is unique
        $baseSlug = $input['slug'];
        $counter = 1;
        while (Post::where('slug', $input['slug'])->exists()) {
            $input['slug'] = $baseSlug . '-' . $counter;
            $counter++;
        }
        
        // Add user_id from authenticated user
        $input['user_id'] = Auth::id();
        
        $post = Post::create($input);
        
        // Attach tags if provided
        if (!empty($input['tag_ids'])) {
            $post->tags()->attach($input['tag_ids']);
        }
        
        return $post->load(['author', 'tags', 'meta']);
    }
    
    public function update($_, array $args): Post
    {
        $post = Post::findOrFail($args['id']);
        $input = $args['input'];
        
        // Update slug if title changed
        if (isset($input['title']) && $post->title !== $input['title']) {
            $input['slug'] = Str::slug($input['title']);
            
            // Ensure slug is unique
            $baseSlug = $input['slug'];
            $counter = 1;
            while (Post::where('slug', $input['slug'])->where('id', '!=', $post->id)->exists()) {
                $input['slug'] = $baseSlug . '-' . $counter;
                $counter++;
            }
        }
        
        $post->update($input);
        
        // Sync tags if provided
        if (isset($input['tag_ids'])) {
            $post->tags()->sync($input['tag_ids']);
        }
        
        return $post->load(['author', 'tags', 'meta']);
    }
}
```

## 📡 Subscriptions

### 📁 app/GraphQL/Subscriptions/PostCreated.php
```php
<?php

namespace App\GraphQL\Subscriptions;

use App\Models\Post;
use Nuwave\Lighthouse\Schema\Types\GraphQLSubscription;
use Nuwave\Lighthouse\Subscriptions\Subscriber;

class PostCreated extends GraphQLSubscription
{
    public function authorize(Subscriber $subscriber, Request $request): bool
    {
        // Allow all authenticated users to subscribe
        return $request->user() !== null;
    }
    
    public function filter(Subscriber $subscriber, $root): bool
    {
        // Only send published posts
        return $root->status === 'published';
    }
}
```

### 📁 app/GraphQL/Subscriptions/PostUpdated.php
```php
<?php

namespace App\GraphQL\Subscriptions;

use App\Models\Post;
use Nuwave\Lighthouse\Schema\Types\GraphQLSubscription;
use Nuwave\Lighthouse\Subscriptions\Subscriber;

class PostUpdated extends GraphQLSubscription
{
    public function authorize(Subscriber $subscriber, Request $request): bool
    {
        $user = $request->user();
        $postId = $subscriber->args['id'] ?? null;
        
        if (!$user || !$postId) {
            return false;
        }
        
        // Allow subscription if user is author or admin
        $post = Post::find($postId);
        return $post && ($post->user_id === $user->id || $user->hasRole('admin'));
    }
    
    public function filter(Subscriber $subscriber, $root): bool
    {
        // Filter by post ID if specified
        if ($postId = $subscriber->args['id'] ?? null) {
            return $root->id == $postId;
        }
        
        return true;
    }
}
```

### Triggering Subscriptions in Models
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Nuwave\Lighthouse\Execution\Utils\Subscription;

class Post extends Model
{
    protected static function boot()
    {
        parent::boot();
        
        static::created(function ($post) {
            Subscription::broadcast('postCreated', $post);
        });
        
        static::updated(function ($post) {
            Subscription::broadcast('postUpdated', $post);
        });
    }
}
```

## 🎯 Directives

### Built-in Directives Examples

#### Validation Directives
```graphql
type Mutation {
    createUser(
        name: String! @rules(apply: ["required", "string", "max:255"])
        email: String! @rules(apply: ["required", "email", "unique:users"])
        password: String! @rules(apply: ["required", "min:8"])
        age: Int @rules(apply: ["integer", "min:18", "max:120"])
    ): User @create
}
```

#### Database Directives
```graphql
type Query {
    # Simple find
    user(id: ID! @eq): User @find
    
    # With relationships
    users: [User!]! @all
    
    # Paginated with filtering
    posts(
        title: String @where(operator: "like")
        status: PostStatus @eq
        orderBy: _ @orderBy(columns: ["created_at", "title"])
    ): [Post!]! @paginate(defaultCount: 15)
    
    # Complex filtering
    searchPosts(
        search: String @where(operator: "like", key: "title")
        authorEmail: String @whereHas(relation: "author", operator: "like", key: "email")
    ): [Post!]! @all
}
```

### 📁 app/GraphQL/Directives/UpperCaseDirective.php
```php
<?php

namespace App\GraphQL\Directives;

use Nuwave\Lighthouse\Schema\Types\GraphQLScalar;
use Nuwave\Lighthouse\Schema\Directives\BaseDirective;
use Nuwave\Lighthouse\Support\Contracts\FieldTransformer;
use Nuwave\Lighthouse\Support\Contracts\GraphQLContext;
use GraphQL\Language\AST\FieldDefinitionNode;
use GraphQL\Language\AST\ObjectTypeDefinitionNode;

class UpperCaseDirective extends BaseDirective implements FieldTransformer
{
    public static function definition(): string
    {
        return /** @lang GraphQL */ '
            """
            Transform the field value to uppercase.
            """
            directive @upperCase on FIELD_DEFINITION
        ';
    }
    
    public function transformField(FieldDefinitionNode $fieldDefinition, ObjectTypeDefinitionNode $parentType): FieldDefinitionNode
    {
        // Transform the field resolver
        return $fieldDefinition;
    }
    
    public function __invoke($root, array $args, GraphQLContext $context, ResolveInfo $resolveInfo)
    {
        $value = $root->{$resolveInfo->fieldName};
        
        return is_string($value) ? strtoupper($value) : $value;
    }
}
```

### 📁 app/GraphQL/Directives/CacheDirective.php
```php
<?php

namespace App\GraphQL\Directives;

use Illuminate\Support\Facades\Cache;
use Nuwave\Lighthouse\Schema\Directives\BaseDirective;
use Nuwave\Lighthouse\Support\Contracts\FieldResolver;
use Nuwave\Lighthouse\Support\Contracts\GraphQLContext;
use GraphQL\Type\Definition\ResolveInfo;

class CacheDirective extends BaseDirective implements FieldResolver
{
    public static function definition(): string
    {
        return /** @lang GraphQL */ '
            """
            Cache the field result for specified time.
            """
            directive @cache(
                """
                Cache key prefix.
                """
                key: String
                
                """
                Cache time in minutes.
                """
                ttl: Int = 60
            ) on FIELD_DEFINITION
        ';
    }
    
    public function resolveField($root, array $args, GraphQLContext $context, ResolveInfo $resolveInfo)
    {
        $keyPrefix = $this->directiveArgValue('key', $resolveInfo->fieldName);
        $ttl = $this->directiveArgValue('ttl', 60);
        
        $cacheKey = $this->generateCacheKey($keyPrefix, $args, $root);
        
        return Cache::remember($cacheKey, now()->addMinutes($ttl), function () use ($root, $args, $context, $resolveInfo) {
            return $this->getResolver()->resolveField($root, $args, $context, $resolveInfo);
        });
    }
    
    private function generateCacheKey(string $prefix, array $args, $root): string
    {
        $hash = md5(serialize([$args, $root->id ?? null]));
        return "{$prefix}:{$hash}";
    }
}
```

## 🛡️ Authentication & Authorization

### 📁 app/GraphQL/Directives/RoleDirective.php
```php
<?php

namespace App\GraphQL\Directives;

use Closure;
use GraphQL\Type\Definition\ResolveInfo;
use Nuwave\Lighthouse\Exceptions\AuthorizationException;
use Nuwave\Lighthouse\Schema\Directives\BaseDirective;
use Nuwave\Lighthouse\Support\Contracts\GraphQLContext;
use Nuwave\Lighthouse\Support\Contracts\FieldMiddleware;

class RoleDirective extends BaseDirective implements FieldMiddleware
{
    public static function definition(): string
    {
        return /** @lang GraphQL */ '
            """
            Limit field access to users with specific roles.
            """
            directive @role(
                """
                Required roles (any of these roles will grant access).
                """
                roles: [String!]!
            ) on FIELD_DEFINITION
        ';
    }
    
    public function handleField($root, array $args, GraphQLContext $context, ResolveInfo $resolveInfo, Closure $next)
    {
        $user = $context->user();
        
        if (!$user) {
            throw new AuthorizationException('You must be authenticated to access this field.');
        }
        
        $requiredRoles = $this->directiveArgValue('roles');
        
        if (!$user->hasAnyRole($requiredRoles)) {
            throw new AuthorizationException('You do not have permission to access this field.');
        }
        
        return $next($root, $args, $context, $resolveInfo);
    }
}
```

### Schema with Authorization
```graphql
type Query {
    # Public queries
    posts: [Post!]! @all
    
    # Authenticated only
    me: User @auth
    
    # Admin only
    adminStats: AdminStats @auth @role(roles: ["admin"])
    
    # Multiple roles
    moderatorContent: [Post!]! @auth @role(roles: ["admin", "moderator"])
}

type Mutation {
    # Authenticated required
    createPost(input: CreatePostInput! @spread): Post @create @guard
    
    # Can only update own posts
    updatePost(
        id: ID! @rules(apply: ["required"])
        input: UpdatePostInput! @spread
    ): Post @update @can(ability: "update", find: "id")
    
    # Admin only mutation
    deleteAnyPost(id: ID!): Post @delete @role(roles: ["admin"])
}
```

### 📁 app/Policies/PostPolicy.php
```php
<?php

namespace App\Policies;

use App\Models\Post;
use App\Models\User;
use Illuminate\Auth\Access\HandlesAuthorization;

class PostPolicy
{
    use HandlesAuthorization;
    
    public function viewAny(User $user): bool
    {
        return true;
    }
    
    public function view(User $user, Post $post): bool
    {
        return $post->status === 'published' || $user->id === $post->user_id || $user->hasRole('admin');
    }
    
    public function create(User $user): bool
    {
        return $user->hasAnyRole(['author', 'admin']);
    }
    
    public function update(User $user, Post $post): bool
    {
        return $user->id === $post->user_id || $user->hasRole('admin');
    }
    
    public function delete(User $user, Post $post): bool
    {
        return $user->id === $post->user_id || $user->hasRole('admin');
    }
}
```

## 🔧 Custom Resolvers

### 📁 app/GraphQL/Queries/UserStats.php
```php
<?php

namespace App\GraphQL\Queries;

use App\Models\User;
use Carbon\Carbon;
use Illuminate\Support\Facades\DB;

final class UserStats
{
    public function __invoke($_, array $args)
    {
        $userId = $args['user_id'];
        $user = User::findOrFail($userId);
        
        return [
            'user' => $user,
            'total_posts' => $user->posts()->count(),
            'published_posts' => $user->posts()->where('status', 'published')->count(),
            'draft_posts' => $user->posts()->where('status', 'draft')->count(),
            'total_comments_received' => $user->posts()
                ->withCount('comments')
                ->get()
                ->sum('comments_count'),
            'posts_this_month' => $user->posts()
                ->whereMonth('created_at', Carbon::now()->month)
                ->whereYear('created_at', Carbon::now()->year)
                ->count(),
            'most_popular_post' => $user->posts()
                ->withCount('comments')
                ->orderBy('comments_count', 'desc')
                ->first(),
            'posting_streak' => $this->calculatePostingStreak($user),
        ];
    }
    
    private function calculatePostingStreak(User $user): int
    {
        $posts = $user->posts()
            ->select(DB::raw('DATE(created_at) as post_date'))
            ->groupBy('post_date')
            ->orderBy('post_date', 'desc')
            ->pluck('post_date')
            ->toArray();
        
        if (empty($posts)) {
            return 0;
        }
        
        $streak = 1;
        $currentDate = Carbon::parse($posts[0]);
        
        for ($i = 1; $i < count($posts); $i++) {
            $nextDate = Carbon::parse($posts[$i]);
            
            if ($currentDate->diffInDays($nextDate) === 1) {
                $streak++;
                $currentDate = $nextDate;
            } else {
                break;
            }
        }
        
        return $streak;
    }
}
```

### 📁 app/GraphQL/Mutations/BulkOperations.php
```php
<?php

namespace App\GraphQL\Mutations;

use App\Models\Post;
use App\Models\User;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Auth;

final class BulkOperations
{
    public function bulkUpdatePosts($_, array $args): array
    {
        $postIds = $args['post_ids'];
        $updates = $args['updates'];
        
        $user = Auth::user();
        
        return DB::transaction(function () use ($postIds, $updates, $user) {
            // Verify user can update all posts
            $posts = Post::whereIn('id', $postIds)->get();
            
            foreach ($posts as $post) {
                if (!$user->can('update', $post)) {
                    throw new \Exception("You cannot update post with ID: {$post->id}");
                }
            }
            
            // Perform bulk update
            $updatedCount = Post::whereIn('id', $postIds)->update($updates);
            
            // Return updated posts
            $updatedPosts = Post::whereIn('id', $postIds)->get();
            
            return [
                'success' => true,
                'updated_count' => $updatedCount,
                'posts' => $updatedPosts,
                'message' => "Successfully updated {$updatedCount} posts."
            ];
        });
    }
    
    public function bulkDeletePosts($_, array $args): array
    {
        $postIds = $args['post_ids'];
        $user = Auth::user();
        
        return DB::transaction(function () use ($postIds, $user) {
            // Verify user can delete all posts
            $posts = Post::whereIn('id', $postIds)->get();
            
            foreach ($posts as $post) {
                if (!$user->can('delete', $post)) {
                    throw new \Exception("You cannot delete post with ID: {$post->id}");
                }
            }
            
            // Store post info before deletion
            $deletedPosts = $posts->toArray();
            
            // Perform bulk delete
            $deletedCount = Post::whereIn('id', $postIds)->delete();
            
            return [
                'success' => true,
                'deleted_count' => $deletedCount,
                'deleted_posts' => $deletedPosts,
                'message' => "Successfully deleted {$deletedCount} posts."
            ];
        });
    }
}
```

## 📊 Database Relations

### Advanced Relationship Examples

```graphql
# Add to schema.graphql
extend type Query {
    # Get posts with complex filtering on relationships
    postsWithAuthors(
        authorName: String @whereHas(relation: "author", operator: "like", key: "name")
        authorStatus: UserStatus @whereHas(relation: "author", operator: "eq", key: "status")
        hasComments: Boolean @whereHas(relation: "comments")
        tagName: String @whereHas(relation: "tags", operator: "like", key: "name")
        minCommentsCount: Int @where(operator: ">=", key: "comments_count")
    ): [Post!]! @all
    
    # Get users with post statistics
    usersWithStats: [UserWithStats!]! @field(resolver: "App\\GraphQL\\Queries\\UsersWithStats")
}

type UserWithStats {
    id: ID!
    name: String!
    email: String!
    posts_count: Int!
    published_posts_count: Int!
    draft_posts_count: Int!
    total_views: Int!
    average_comments_per_post: Float!
    last_post_date: DateTime
    created_at: DateTime!
}
```

### 📁 app/GraphQL/Queries/UsersWithStats.php
```php
<?php

namespace App\GraphQL\Queries;

use App\Models\User;
use Illuminate\Support\Facades\DB;

final class UsersWithStats
{
    public function __invoke($_, array $args)
    {
        return User::select([
            'users.*',
            DB::raw('COUNT(posts.id) as posts_count'),
            DB::raw('COUNT(CASE WHEN posts.status = "published" THEN 1 END) as published_posts_count'),
            DB::raw('COUNT(CASE WHEN posts.status = "draft" THEN 1 END) as draft_posts_count'),
            DB::raw('COALESCE(SUM(posts.views), 0) as total_views'),
            DB::raw('COALESCE(AVG(post_comments.comments_count), 0) as average_comments_per_post'),
            DB::raw('MAX(posts.created_at) as last_post_date')
        ])
        ->leftJoin('posts', 'users.id', '=', 'posts.user_id')
        ->leftJoin(
            DB::raw('(SELECT post_id, COUNT(*) as comments_count FROM comments GROUP BY post_id) as post_comments'),
            'posts.id', '=', 'post_comments.post_id'
        )
        ->groupBy('users.id')
        ->orderBy('posts_count', 'desc')
        ->get()
        ->map(function ($user) {
            $user->average_comments_per_post = round($user->average_comments_per_post, 2);
            return $user;
        });
    }
}
```

## 📁 File Uploads

### Schema Definition for File Uploads
```graphql
# Add to schema.graphql
scalar Upload @scalar(class: "Nuwave\\Lighthouse\\Schema\\Types\\Scalars\\Upload")

extend type Mutation {
    # Single file upload
    uploadAvatar(
        user_id: ID! @rules(apply: ["required", "exists:users,id"])
        avatar: Upload! @rules(apply: ["required", "image", "max:2048"])
    ): User @field(resolver: "App\\GraphQL\\Mutations\\FileUpload@uploadAvatar")
    
    # Multiple file uploads
    uploadPostImages(
        post_id: ID! @rules(apply: ["required", "exists:posts,id"])
        images: [Upload!]! @rules(apply: ["required", "array", "max:5"])
    ): Post @field(resolver: "App\\GraphQL\\Mutations\\FileUpload@uploadPostImages")
    
    # Create post with file upload
    createPostWithImages(
        title: String! @rules(apply: ["required", "string", "max:255"])
        content: String! @rules(apply: ["required", "string"])
        featured_image: Upload @rules(apply: ["image", "max:2048"])
        gallery_images: [Upload!] @rules(apply: ["array", "max:10"])
    ): Post @field(resolver: "App\\GraphQL\\Mutations\\FileUpload@createPostWithImages")
}

type PostImage {
    id: ID!
    post_id: ID!
    url: String!
    alt_text: String
    size: Int!
    mime_type: String!
    created_at: DateTime!
}

extend type Post {
    images: [PostImage!]! @hasMany
}
```

### 📁 app/GraphQL/Mutations/FileUpload.php
```php
<?php

namespace App\GraphQL\Mutations;

use App\Models\Post;
use App\Models\User;
use App\Models\PostImage;
use Illuminate\Http\UploadedFile;
use Illuminate\Support\Facades\Storage;
use Illuminate\Support\Str;

final class FileUpload
{
    public function uploadAvatar($_, array $args): User
    {
        $user = User::findOrFail($args['user_id']);
        $avatar = $args['avatar'];
        
        // Delete old avatar if exists
        if ($user->profile && $user->profile->avatar) {
            Storage::disk('public')->delete($user->profile->avatar);
        }
        
        // Store new avatar
        $path = $this->storeFile($avatar, 'avatars');
        
        // Update user profile
        $user->profile()->updateOrCreate(
            ['user_id' => $user->id],
            ['avatar' => $path]
        );
        
        return $user->load('profile');
    }
    
    public function uploadPostImages($_, array $args): Post
    {
        $post = Post::findOrFail($args['post_id']);
        $images = $args['images'];
        
        foreach ($images as $image) {
            $path = $this->storeFile($image, 'posts/images');
            
            PostImage::create([
                'post_id' => $post->id,
                'url' => Storage::url($path),
                'size' => $image->getSize(),
                'mime_type' => $image->getMimeType(),
            ]);
        }
        
        return $post->load('images');
    }
    
    public function createPostWithImages($_, array $args): Post
    {
        $postData = [
            'title' => $args['title'],
            'content' => $args['content'],
            'slug' => Str::slug($args['title']),
            'user_id' => auth()->id(),
            'status' => 'draft',
        ];
        
        // Handle featured image
        if (isset($args['featured_image'])) {
            $featuredPath = $this->storeFile($args['featured_image'], 'posts/featured');
            $postData['featured_image'] = Storage::url($featuredPath);
        }
        
        $post = Post::create($postData);
        
        // Handle gallery images
        if (isset($args['gallery_images'])) {
            foreach ($args['gallery_images'] as $image) {
                $path = $this->storeFile($image, 'posts/gallery');
                
                PostImage::create([
                    'post_id' => $post->id,
                    'url' => Storage::url($path),
                    'size' => $image->getSize(),
                    'mime_type' => $image->getMimeType(),
                ]);
            }
        }
        
        return $post->load(['author', 'images']);
    }
    
    private function storeFile(UploadedFile $file, string $directory): string
    {
        $filename = time() . '_' . Str::random(10) . '.' . $file->getClientOriginalExtension();
        return $file->storeAs($directory, $filename, 'public');
    }
}
```

## ⚠️ Error Handling

### 📁 app/GraphQL/Exceptions/CustomException.php
```php
<?php

namespace App\GraphQL\Exceptions;

use Exception;
use Nuwave\Lighthouse\Exceptions\RendersErrorsExtensions;

class CustomGraphQLException extends Exception implements RendersErrorsExtensions
{
    private string $reason;
    
    public function __construct(string $message, string $reason = 'BAD_USER_INPUT')
    {
        parent::__construct($message);
        $this->reason = $reason;
    }
    
    public function isClientSafe(): bool
    {
        return true;
    }
    
    public function getCategory(): string
    {
        return 'custom';
    }
    
    public function extensionsContent(): array
    {
        return [
            'reason' => $this->reason,
            'timestamp' => now()->toISOString(),
        ];
    }
}
```

### 📁 app/GraphQL/Mutations/ErrorHandlingExample.php
```php
<?php

namespace App\GraphQL\Mutations;

use App\Models\Post;
use App\GraphQL\Exceptions\CustomGraphQLException;
use Illuminate\Support\Facades\DB;
use Exception;

final class ErrorHandlingExample
{
    public function createPostSafely($_, array $args): Post
    {
        try {
            return DB::transaction(function () use ($args) {
                $input = $args['input'];
                
                // Custom validation
                if (strlen($input['content']) < 100) {
                    throw new CustomGraphQLException(
                        'Post content must be at least 100 characters long.',
                        'CONTENT_TOO_SHORT'
                    );
                }
                
                // Check for profanity (example)
                if ($this->containsProfanity($input['title']) || $this->containsProfanity($input['content'])) {
                    throw new CustomGraphQLException(
                        'Content contains inappropriate language.',
                        'INAPPROPRIATE_CONTENT'
                    );
                }
                
                $post = Post::create([
                    'title' => $input['title'],
                    'content' => $input['content'],
                    'slug' => Str::slug($input['title']),
                    'user_id' => auth()->id(),
                    'status' => $input['status'] ?? 'draft',
                ]);
                
                return $post->load('author');
            });
        } catch (CustomGraphQLException $e) {
            throw $e;
        } catch (Exception $e) {
            throw new CustomGraphQLException(
                'An unexpected error occurred while creating the post.',
                'INTERNAL_ERROR'
            );
        }
    }
    
    private function containsProfanity(string $text): bool
    {
        $profanityWords = ['badword1', 'badword2']; // Add your list
        
        foreach ($profanityWords as $word) {
            if (stripos($text, $word) !== false) {
                return true;
            }
        }
        
        return false;
    }
}
```

### Global Error Handler
```php
// 📁 app/Exceptions/Handler.php
<?php

namespace App\Exceptions;

use Illuminate\Foundation\Exceptions\Handler as ExceptionHandler;
use Nuwave\Lighthouse\Exceptions\ValidationException;
use GraphQL\Error\Error;
use Throwable;

class Handler extends ExceptionHandler
{
    public function render($request, Throwable $exception)
    {
        // Handle GraphQL validation errors
        if ($exception instanceof ValidationException) {
            return response()->json([
                'errors' => [
                    [
                        'message' => 'Validation failed',
                        'extensions' => [
                            'validation' => $exception->getValidator()->errors()->toArray(),
                            'category' => 'validation'
                        ]
                    ]
                ]
            ], 422);
        }
        
        return parent::render($request, $exception);
    }
}
```

## 🧪 Testing

### 📁 tests/Feature/GraphQL/UserQueriesTest.php
```php
<?php

namespace Tests\Feature\GraphQL;

use App\Models\User;
use Tests\TestCase;
use Nuwave\Lighthouse\Testing\MakesGraphQLRequests;
use Illuminate\Foundation\Testing\RefreshDatabase;

class UserQueriesTest extends TestCase
{
    use RefreshDatabase, MakesGraphQLRequests;
    
    public function test_can_fetch_user_by_id(): void
    {
        $user = User::factory()->create([
            'name' => 'John Doe',
            'email' => 'john@example.com'
        ]);
        
        $response = $this->graphQL('
            query GetUser($id: ID!) {
                user(id: $id) {
                    id
                    name
                    email
                    created_at
                }
            }
        ', [
            'id' => $user->id
        ]);
        
        $response->assertJson([
            'data' => [
                'user' => [
                    'id' => (string) $user->id,
                    'name' => 'John Doe',
                    'email' => 'john@example.com'
                ]
            ]
        ]);
    }
    
    public function test_can_paginate_users(): void
    {
        User::factory()->count(25)->create();
        
        $response = $this->graphQL('
            query GetUsers($first: Int, $page: Int) {
                users(first: $first, page: $page) {
                    paginatorInfo {
                        currentPage
                        lastPage
                        total
                        perPage
                    }
                    data {
                        id
                        name
                        email
                    }
                }
            }
        ', [
            'first' => 10,
            'page' => 1
        ]);
        
        $response->assertJsonStructure([
            'data' => [
                'users' => [
                    'paginatorInfo' => [
                        'currentPage',
                        'lastPage',
                        'total',
                        'perPage'
                    ],
                    'data' => [
                        '*' => ['id', 'name', 'email']
                    ]
                ]
            ]
        ]);
        
        $this->assertEquals(1, $response->json('data.users.paginatorInfo.currentPage'));
        $this->assertEquals(25, $response->json('data.users.paginatorInfo.total'));
    }
    
    public function test_requires_authentication_for_me_query(): void
    {
        $response = $this->graphQL('
            query {
                me {
                    id
                    name
                }
            }
        ');
        
        $response->assertGraphQLErrorMessage('Unauthenticated.');
    }
    
    public function test_authenticated_user_can_access_me_query(): void
    {
        $user = User::factory()->create();
        
        $response = $this->actingAs($user)->graphQL('
            query {
                me {
                    id
                    name
                    email
                }
            }
        ');
        
        $response->assertJson([
            'data' => [
                'me' => [
                    'id' => (string) $user->id,
                    'name' => $user->name,
                    'email' => $user->email
                ]
            ]
        ]);
    }
}
```

### 📁 tests/Feature/GraphQL/PostMutationsTest.php
```php
<?php

namespace Tests\Feature\GraphQL;

use App\Models\User;
use App\Models\Post;
use Tests\TestCase;
use Nuwave\Lighthouse\Testing\MakesGraphQLRequests;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Laravel\Sanctum\Sanctum;

class PostMutationsTest extends TestCase
{
    use RefreshDatabase, MakesGraphQLRequests;
    
    public function test_can_create_post(): void
    {
        $user = User::factory()->create();
        Sanctum::actingAs($user);
        
        $response = $this->graphQL('
            mutation CreatePost($input: CreatePostInput!) {
                createPost(input: $input) {
                    id
                    title
                    content
                    slug
                    status
                    author {
                        id
                        name
                    }
                }
            }
        ', [
            'input' => [
                'title' => 'Test Post',
                'content' => 'This is test content for the post.',
                'status' => 'DRAFT'
            ]
        ]);
        
        $response->assertJson([
            'data' => [
                'createPost' => [
                    'title' => 'Test Post',
                    'content' => 'This is test content for the post.',
                    'slug' => 'test-post',
                    'status' => 'DRAFT',
                    'author' => [
                        'id' => (string) $user->id,
                        'name' => $user->name
                    ]
                ]
            ]
        ]);
        
        $this->assertDatabaseHas('posts', [
            'title' => 'Test Post',
            'user_id' => $user->id
        ]);
    }
    
    public function test_cannot_create_post_without_authentication(): void
    {
        $response = $this->graphQL('
            mutation CreatePost($input: CreatePostInput!) {
                createPost(input: $input) {
                    id
                }
            }
        ', [
            'input' => [
                'title' => 'Test Post',
                'content' => 'Test content'
            ]
        ]);
        
        $response->assertGraphQLErrorMessage('Unauthenticated.');
    }
    
    public function test_can_update_own_post(): void
    {
        $user = User::factory()->create();
        $post = Post::factory()->create(['user_id' => $user->id]);
        
        Sanctum::actingAs($user);
        
        $response = $this->graphQL('
            mutation UpdatePost($id: ID!, $input: UpdatePostInput!) {
                updatePost(id: $id, input: $input) {
                    id
                    title
                    content
                }
            }
        ', [
            'id' => $post->id,
            'input' => [
                'title' => 'Updated Title',
                'content' => 'Updated content'
            ]
        ]);
        
        $response->assertJson([
            'data' => [
                'updatePost' => [
                    'id' => (string) $post->id,
                    'title' => 'Updated Title',
                    'content' => 'Updated content'
                ]
            ]
        ]);
    }
    
    public function test_cannot_update_other_users_post(): void
    {
        $user = User::factory()->create();
        $otherUser = User::factory()->create();
        $post = Post::factory()->create(['user_id' => $otherUser->id]);
        
        Sanctum::actingAs($user);
        
        $response = $this->graphQL('
            mutation UpdatePost($id: ID!, $input: UpdatePostInput!) {
                updatePost(id: $id, input: $input) {
                    id
                }
            }
        ', [
            'id' => $post->id,
            'input' => [
                'title' => 'Hacked Title'
            ]
        ]);
        
        $response->assertGraphQLErrorMessage('This action is unauthorized.');
    }
}
```

## ⚡ Performance Optimization

### N+1 Query Prevention
```php
<?php

namespace App\GraphQL\Queries;

use App\Models\Post;
use Nuwave\Lighthouse\Support\Contracts\GraphQLContext;

final class OptimizedPostQuery
{
    public function __invoke($_, array $args, GraphQLContext $context)
    {
        // Eager load relationships to prevent N+1 queries
        return Post::with([
            'author:id,name,email',
            'author.profile:user_id,avatar',
            'tags:id,name,slug',
            'comments' => function ($query) {
                $query->latest()->limit(5);
            },
            'comments.user:id,name',
            'meta:post_id,meta_title,meta_description'
        ])
        ->withCount(['comments', 'views'])
        ->orderBy('created_at', 'desc')
        ->get();
    }
}
```

### 📁 app/GraphQL/Directives/DataLoaderDirective.php
```php
<?php

namespace App\GraphQL\Directives;

use Nuwave\Lighthouse\Schema\Directives\BaseDirective;
use Nuwave\Lighthouse\Support\Contracts\FieldResolver;
use Nuwave\Lighthouse\Support\Contracts\GraphQLContext;
use GraphQL\Type\Definition\ResolveInfo;

class DataLoaderDirective extends BaseDirective implements FieldResolver
{
    public static function definition(): string
    {
        return /** @lang GraphQL */ '
            """
            Batch load related data to prevent N+1 queries.
            """
            directive @dataLoader(
                relation: String!
            ) on FIELD_DEFINITION
        ';
    }
    
    public function resolveField($root, array $args, GraphQLContext $context, ResolveInfo $resolveInfo)
    {
        $relation = $this->directiveArgValue('relation');
        $batchKey = "batch_{$relation}";
        
        // Get or create batch loader for this relation
        $loader = $context->batchLoader($batchKey, function ($ids) use ($relation) {
            // This would be called once for all batched requests
            return $this->batchLoadRelation($relation, $ids);
        });
        
        // Add current ID to batch and return promise
        return $loader->load($root->getKey());
    }
    
    private function batchLoadRelation(string $relation, array $ids): array
    {
        // Implementation would depend on the specific relation type
        // This is a simplified example
        $results = collect();
        
        // Batch load all related data at once
        $relatedData = app("App\\Models\\{$relation}")
            ->whereIn('parent_id', $ids)
            ->get()
            ->groupBy('parent_id');
        
        // Return data indexed by parent ID
        return $ids->mapWithKeys(function ($id) use ($relatedData) {
            return [$id => $relatedData->get($id, collect())];
        })->toArray();
    }
}
```

### Query Complexity Analysis
```php
// 📁 config/lighthouse.php
'security' => [
    'max_query_complexity' => 3000,
    'max_query_depth' => 15,
    'disable_introspection' => env('LIGHTHOUSE_DISABLE_INTROSPECTION', false),
],
```

### Caching Strategy
```php
// 📁 app/GraphQL/Queries/CachedQuery.php
<?php

namespace App\GraphQL\Queries;

use Illuminate\Support\Facades\Cache;
use App\Models\Post;

final class CachedPopularPosts
{
    public function __invoke($_, array $args)
    {
        $cacheKey = 'popular_posts_' . md5(serialize($args));
        
        return Cache::remember($cacheKey, now()->addHours(1), function () use ($args) {
            return Post::with(['author', 'tags'])
                ->withCount(['comments', 'views'])
                ->orderBy('views_count', 'desc')
                ->limit($args['limit'] ?? 10)
                ->get();
        });
    }
}
```

## 🐛 Common Issues & Solutions

### Issue 1: CORS Problems
**Problem**: GraphQL requests blocked by CORS policy

**Solution**: Configure CORS in `config/cors.php`
```php
<?php

return [
    'paths' => ['api/*', 'graphql', 'sanctum/csrf-cookie'],
    'allowed_methods' => ['*'],
    'allowed_origins' => ['*'], // Configure for production
    'allowed_origins_patterns' => [],
    'allowed_headers' => ['*'],
    'exposed_headers' => [],
    'max_age' => 0,
    'supports_credentials' => true,
];
```

### Issue 2: Schema Caching Issues
**Problem**: Schema changes not reflected

**Solution**: Clear schema cache
```bash
php artisan lighthouse:clear-cache
php artisan config:clear
php artisan route:clear
```

### Issue 3: Memory Issues with Large Queries
**Problem**: Out of memory errors with large result sets

**Solution**: Implement pagination and limits
```graphql
type Query {
    posts(
        first: Int = 15 @rules(apply: ["integer", "max:100"])
        page: Int = 1
    ): [Post!]! @paginate
    
    # Alternative: Use cursor-based pagination
    postsFeed(
        first: Int = 15 @rules(apply: ["integer", "max:100"])
        after: String
    ): PostConnection! @paginate(type: CURSOR)
}
```

### Issue 4: Authentication Token Issues
**Problem**: Token not being recognized

**Solution**: Ensure proper middleware and token handling
```php
// 📁 config/lighthouse.php
'route' => [
    'middleware' => [
        'api',
        // Make sure this is correct for your auth setup
        'auth:sanctum', // or 'auth:api' for Passport
    ],
],
```

### Issue 5: Validation Error Format
**Problem**: Validation errors not user-friendly

**Solution**: Custom validation error formatter
```php
// 📁 app/GraphQL/ErrorHandlers/ValidationErrorHandler.php
<?php

namespace App\GraphQL\ErrorHandlers;

use Nuwave\Lighthouse\Exceptions\ValidationException;
use GraphQL\Error\Error;

class ValidationErrorHandler
{
    public function __invoke(ValidationException $exception, \Closure $next): array
    {
        return [
            'message' => 'Validation failed',
            'extensions' => [
                'validation' => $exception->getValidator()->errors()->toArray(),
                'category' => 'validation'
            ]
        ];
    }
}
```

## ✅ Best Practices

### 1. 📝 Schema Design
- Keep schema flat and avoid deep nesting
- Use meaningful names for types and fields
- Group related functionality in schema modules
- Always provide descriptions for types and fields
- Use enums for limited value sets

```graphql
# Good: Descriptive and well-documented
"""
Represents a blog post in the system.
"""
type Post {
    """
    Unique identifier for the post.
    """
    id: ID!
    
    """
    The title of the post, used for display and SEO.
    """
    title: String!
    
    """
    Current publication status of the post.
    """
    status: PostStatus!
}

# Bad: No descriptions, unclear names
type P {
    i: ID!
    t: String!
    s: String!
}
```

### 2. 🔐 Security Best Practices
- Always validate input data
- Implement proper authorization
- Use query complexity analysis
- Sanitize user inputs
- Implement rate limiting

```php
// 📁 app/GraphQL/Mutations/SecureMutation.php
<?php

namespace App\GraphQL\Mutations;

use Illuminate\Support\Facades\RateLimiter;
use Illuminate\Support\Facades\Auth;

final class SecureMutation
{
    public function createPost($_, array $args)
    {
        // Rate limiting
        $key = 'create-post:' . Auth::id();
        if (RateLimiter::tooManyAttempts($key, 10)) {
            throw new \Exception('Too many attempts. Please try again later.');
        }
        RateLimiter::hit($key, 60);
        
        // Authorization check
        if (!Auth::user()->can('create', Post::class)) {
            throw new \Exception('Unauthorized action.');
        }
        
        // Input validation and sanitization
        $input = $this->validateAndSanitizeInput($args['input']);
        
        // Create post logic...
    }
    
    private function validateAndSanitizeInput(array $input): array
    {
        // Remove any dangerous HTML tags
        $input['content'] = strip_tags($input['content'], '<p><br><strong><em><ul><ol><li>');
        
        // Validate required fields
        if (empty($input['title']) || strlen($input['title']) < 3) {
            throw new \Exception('Title must be at least 3 characters long.');
        }
        
        return $input;
    }
}
```

### 3. 🚀 Performance Best Practices
- Use eager loading to prevent N+1 queries
- Implement caching strategies
- Use database indexes properly
- Limit query depth and complexity
- Use pagination for large datasets

```php
// 📁 app/Models/Post.php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Builder;

class Post extends Model
{
    // Define eager loading relationships
    protected $with = ['author:id,name'];
    
    // Scope for optimized queries
    public function scopeWithOptimizedRelations(Builder $query): Builder
    {
        return $query->with([
            'author:id,name,email',
            'author.profile:user_id,avatar',
            'tags:id,name,slug',
            'comments' => function ($query) {
                $query->approved()->latest()->limit(5);
            }
        ])->withCount(['comments', 'likes']);
    }
    
    // Add database indexes in migration
    public function up()
    {
        Schema::table('posts', function (Blueprint $table) {
            $table->index(['status', 'published_at']);
            $table->index(['user_id', 'created_at']);
            $table->fullText(['title', 'content']); // For search
        });
    }
}
```

### 4. 🧪 Testing Strategy
- Test all queries, mutations, and subscriptions
- Test authorization rules
- Mock external services
- Use factories for test data
- Test error conditions

```php
// 📁 tests/Feature/GraphQL/BaseGraphQLTest.php
<?php

namespace Tests\Feature\GraphQL;

use Tests\TestCase;
use App\Models\User;
use Nuwave\Lighthouse\Testing\MakesGraphQLRequests;
use Illuminate\Foundation\Testing\RefreshDatabase;

abstract class BaseGraphQLTest extends TestCase
{
    use RefreshDatabase, MakesGraphQLRequests;
    
    protected function setUp(): void
    {
        parent::setUp();
        $this->artisan('lighthouse:clear-cache');
    }
    
    protected function createAuthenticatedUser(array $attributes = []): User
    {
        $user = User::factory()->create($attributes);
        $this->actingAs($user);
        return $user;
    }
    
    protected function assertGraphQLValidationError(string $field, $response): void
    {
        $response->assertGraphQLValidationKeys([$field]);
    }
    
    protected function assertGraphQLSuccess($response): void
    {
        $response->assertGraphQLValidationPasses();
        $this->assertNull($response->json('errors'));
    }
}
```

### 5. 📊 Monitoring & Logging
- Log GraphQL operations
- Monitor query performance
- Track error rates
- Set up alerts for issues

```php
// 📁 app/GraphQL/Middleware/GraphQLLoggingMiddleware.php
<?php

namespace App\GraphQL\Middleware;

use Closure;
use Illuminate\Support\Facades\Log;
use GraphQL\Type\Definition\ResolveInfo;
use Nuwave\Lighthouse\Support\Contracts\GraphQLContext;

class GraphQLLoggingMiddleware
{
    public function handle($root, array $args, GraphQLContext $context, ResolveInfo $resolveInfo, Closure $next)
    {
        $start = microtime(true);
        
        try {
            $result = $next($root, $args, $context, $resolveInfo);
            
            $this->logSuccess($resolveInfo, $args, $start);
            
            return $result;
        } catch (\Exception $exception) {
            $this->logError($resolveInfo, $args, $exception, $start);
            throw $exception;
        }
    }
    
    private function logSuccess(ResolveInfo $resolveInfo, array $args, float $start): void
    {
        $executionTime = (microtime(true) - $start) * 1000;
        
        Log::channel('graphql')->info('GraphQL Query Executed', [
            'field' => $resolveInfo->fieldName,
            'type' => $resolveInfo->parentType->name,
            'execution_time_ms' => $executionTime,
            'args' => $args,
            'user_id' => auth()->id(),
        ]);
    }
    
    private function logError(ResolveInfo $resolveInfo, array $args, \Exception $exception, float $start): void
    {
        $executionTime = (microtime(true) - $start) * 1000;
        
        Log::channel('graphql')->error('GraphQL Query Failed', [
            'field' => $resolveInfo->fieldName,
            'type' => $resolveInfo->parentType->name,
            'execution_time_ms' => $executionTime,
            'error' => $exception->getMessage(),
            'args' => $args,
            'user_id' => auth()->id(),
        ]);
    }
}
```

### 6. 🔄 API Versioning with GraphQL
- Use schema evolution instead of versioning
- Deprecate fields instead of removing them
- Use field descriptions to indicate deprecation

```graphql
type User {
    id: ID!
    name: String!
    
    # Deprecated field with migration path
    username: String! @deprecated(reason: "Use 'name' field instead. This field will be removed in v2.0")
    
    # New field with default for backward compatibility
    display_name: String
    
    # Field with version-specific behavior
    profile_url: String @deprecated(reason: "Use 'profile.url' instead")
    
    profile: Profile
}

type Profile {
    id: ID!
    url: String
    avatar: String
}
```

### 7. 📱 Frontend Integration Tips
- Use fragments for reusable query parts
- Implement proper error handling
- Cache queries when appropriate
- Use subscriptions for real-time features

```javascript
// Frontend GraphQL fragments example
const USER_FRAGMENT = gql`
  fragment UserInfo on User {
    id
    name
    email
    profile {
      avatar
    }
  }
`;

const GET_POSTS = gql`
  query GetPosts($first: Int, $page: Int) {
    posts(first: $first, page: $page) {
      paginatorInfo {
        currentPage
        lastPage
        total
      }
      data {
        id
        title
        content
        author {
          ...UserInfo
        }
      }
    }
  }
  ${USER_FRAGMENT}
`;

// Subscription example
const POST_CREATED_SUBSCRIPTION = gql`
  subscription PostCreated {
    postCreated {
      id
      title
      author {
        ...UserInfo
      }
    }
  }
  ${USER_FRAGMENT}
`;
```

### 8. 🔧 Development Workflow
- Use GraphQL Playground for testing
- Keep schema in version control
- Use schema linting tools
- Document breaking changes

```bash
# Development commands
php artisan lighthouse:print-schema > schema.graphql  # Export schema
php artisan lighthouse:validate-schema                # Validate schema
php artisan lighthouse:clear-cache                    # Clear schema cache
php artisan lighthouse:ide-helper                     # Generate IDE helpers
```

### 9. 🚀 Deployment Considerations
- Disable introspection in production
- Set up proper error handling
- Configure caching appropriately
- Monitor performance metrics

```php
// 📁 config/lighthouse.php (Production settings)
return [
    'route' => [
        'middleware' => [
            'api',
            'throttle:api', // Rate limiting
            'auth:sanctum'
        ],
    ],
    
    'security' => [
        'max_query_complexity' => 1000,  // Lower in production
        'max_query_depth' => 10,         // Lower in production
        'disable_introspection' => true, // Disable in production
    ],
    
    'cache' => [
        'enable' => true,
        'store' => 'redis', // Use Redis in production
    ],
];
```

### 10. 📚 Documentation Best Practices
- Keep schema documentation up to date
- Provide query examples
- Document authentication requirements
- Include error code references

```graphql
"""
Blog post management operations.

Authentication: Bearer token required for all mutations.
Permissions: 
- create: requires 'author' or 'admin' role
- update: post owner or 'admin' role
- delete: post owner or 'admin' role

Example query:
```
query GetPost($id: ID!) {
  post(id: $id) {
    id
    title
    content
    author {
      name
    }
  }
}
```

Error codes:
- UNAUTHENTICATED: User not authenticated
- UNAUTHORIZED: Insufficient permissions
- NOT_FOUND: Post not found
- VALIDATION_ERROR: Input validation failed
"""
type Post {
    id: ID!
    title: String!
    content: String!
    # ... other fields
}
```

## 🎯 Implementation Checklist

- [ ] ✅ Install Laravel Lighthouse package
- [ ] 📋 Configure basic schema structure
- [ ] 🔍 Implement basic queries (find, paginate)
- [ ] 🔄 Create mutations (CRUD operations)
- [ ] 🛡️ Set up authentication middleware
- [ ] 🎯 Implement custom directives
- [ ] 📊 Configure database relationships
- [ ] 📁 Handle file uploads (if needed)
- [ ] 📡 Set up subscriptions (if needed)
- [ ] ⚠️ Implement error handling
- [ ] 🧪 Write comprehensive tests
- [ ] ⚡ Optimize for performance
- [ ] 📱 Test frontend integration
- [ ] 🚀 Configure for production deployment
- [ ] 📚 Document API usage

## 🌟 Advanced Features

### Custom Scalar Types
```php
// 📁 app/GraphQL/Scalars/EmailType.php
<?php

namespace App\GraphQL\Scalars;

use GraphQL\Type\Definition\ScalarType;
use GraphQL\Error\Error;
use GraphQL\Language\AST\StringValueNode;

class EmailType extends ScalarType
{
    public function serialize($value)
    {
        if (!filter_var($value, FILTER_VALIDATE_EMAIL)) {
            throw new Error("Invalid email format: {$value}");
        }
        
        return $value;
    }
    
    public function parseValue($value)
    {
        if (!filter_var($value, FILTER_VALIDATE_EMAIL)) {
            throw new Error("Invalid email format: {$value}");
        }
        
        return $value;
    }
    
    public function parseLiteral($valueNode, array $variables = null)
    {
        if (!$valueNode instanceof StringValueNode) {
            throw new Error('Can only parse string values');
        }
        
        if (!filter_var($valueNode->value, FILTER_VALIDATE_EMAIL)) {
            throw new Error("Invalid email format: {$valueNode->value}");
        }
        
        return $valueNode->value;
    }
}
```

### Federation Support (Multi-Service GraphQL)
```php
// 📁 config/lighthouse.php
return [
    'federation' => [
        'entities' => [
            'User' => 'App\\Models\\User',
            'Post' => 'App\\Models\\Post',
        ],
    ],
];
```

```graphql
# Schema with federation
extend type Query {
    _entities(representations: [_Any!]!): [_Entity]!
    _service: _Service!
}

type User @key(fields: "id") {
    id: ID! @external
    posts: [Post!]! @hasMany
}

type Post @key(fields: "id") {
    id: ID!
    title: String!
    author: User! @belongsTo
}
```

## 🚀 Conclusion

GraphQL with Laravel Lighthouse provides a powerful, flexible way to build modern APIs. By following this comprehensive guide, you can:

- 🏗️ Build scalable GraphQL APIs with Laravel
- 🔐 Implement proper security and authorization
- ⚡ Optimize for performance and scalability  
- 🧪 Test your GraphQL implementation thoroughly
- 📱 Provide excellent developer experience

**Key Benefits of GraphQL with Lighthouse:**
- Single endpoint for all operations
- Client-controlled data fetching
- Strong type system and introspection
- Real-time capabilities with subscriptions
- Excellent Laravel integration
- Rich ecosystem and tooling

Remember to:
- Keep your schema well-documented
- Implement proper caching strategies
- Monitor query performance
- Follow security best practices
- Write comprehensive tests

Happy GraphQL development! 🎉

## 📖 Additional Resources

- **Laravel Lighthouse Documentation**: https://lighthouse-php.com/
- **GraphQL Specification**: https://graphql.org/learn/
- **GraphQL Best Practices**: https://graphql.org/learn/best-practices/
- **Laravel Sanctum**: https://laravel.com/docs/sanctum
- **GraphQL Playground**: https://github.com/graphql/graphql-playground