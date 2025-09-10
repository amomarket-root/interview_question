# Complete CSRF Protection Guide for Laravel

## What is CSRF?

**Cross-Site Request Forgery (CSRF)** is a security vulnerability that allows an attacker to trick users into performing unwanted actions on a web application where they're authenticated. The attacker exploits the trust that a web application has in a user's browser.

### How CSRF Attacks Work

1. **User logs into a legitimate website** (e.g., banking site)
2. **User visits a malicious website** while still logged in
3. **Malicious site sends a request** to the legitimate site using the user's session
4. **Legitimate site processes the request** thinking it came from the user

### Example CSRF Attack

```html
<!-- Malicious website contains this hidden form -->
<form action="https://bank.com/transfer" method="POST" id="malicious">
    <input type="hidden" name="to_account" value="attacker_account">
    <input type="hidden" name="amount" value="10000">
</form>
<script>
    document.getElementById('malicious').submit();
</script>
```

## What CSRF Protection Looks Like

### CSRF Token Structure
```
_token: "7lKq9pJxE8mF2nR5vC3hS6wX1zY4tU0oP"
```

### In HTML Forms
```html
<form method="POST" action="/profile">
    @csrf
    <input type="text" name="name">
    <button type="submit">Update</button>
</form>
```

### Generated HTML
```html
<form method="POST" action="/profile">
    <input type="hidden" name="_token" value="7lKq9pJxE8mF2nR5vC3hS6wX1zY4tU0oP">
    <input type="text" name="name">
    <button type="submit">Update</button>
</form>
```

## How to Use CSRF Protection

### 1. In Blade Templates

**Method 1: Using @csrf directive**
```html
<form method="POST" action="/users">
    @csrf
    <input type="text" name="name" required>
    <button type="submit">Create User</button>
</form>
```

**Method 2: Using csrf_field() helper**
```html
<form method="POST" action="/users">
    {{ csrf_field() }}
    <input type="text" name="name" required>
    <button type="submit">Create User</button>
</form>
```

**Method 3: Manual token insertion**
```html
<form method="POST" action="/users">
    <input type="hidden" name="_token" value="{{ csrf_token() }}">
    <input type="text" name="name" required>
    <button type="submit">Create User</button>
</form>
```

### 2. In AJAX Requests

**Method 1: Meta tag approach**
```html
<!-- In your layout head -->
<meta name="csrf-token" content="{{ csrf_token() }}">

<script>
$.ajaxSetup({
    headers: {
        'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
    }
});

// Your AJAX request
$.post('/api/users', {
    name: 'John Doe',
    email: 'john@example.com'
});
</script>
```

**Method 2: Direct token in request**
```javascript
fetch('/api/users', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json',
        'X-CSRF-TOKEN': document.querySelector('meta[name="csrf-token"]').getAttribute('content')
    },
    body: JSON.stringify({
        name: 'John Doe',
        email: 'john@example.com'
    })
});
```

**Method 3: Including token in request body**
```javascript
$.post('/api/users', {
    _token: $('meta[name="csrf-token"]').attr('content'),
    name: 'John Doe',
    email: 'john@example.com'
});
```

### 3. In Vue.js/React Applications

**Vue.js example:**
```javascript
// In your main.js or app.js
window.axios.defaults.headers.common['X-CSRF-TOKEN'] = 
    document.querySelector('meta[name="csrf-token"]').getAttribute('content');

// In your component
export default {
    methods: {
        submitForm() {
            axios.post('/api/users', {
                name: this.name,
                email: this.email
            });
        }
    }
}
```

## Why Use CSRF Protection?

### 1. **Prevents Unauthorized Actions**
- Stops attackers from performing actions on behalf of users
- Protects sensitive operations (transfers, deletions, updates)

### 2. **Maintains Data Integrity**
- Ensures requests originate from your application
- Prevents data manipulation from external sources

### 3. **Compliance Requirements**
- Required for security standards (PCI DSS, OWASP)
- Essential for financial and healthcare applications

### 4. **User Trust**
- Protects user accounts and data
- Maintains application reputation

## CSRF Implementation in Laravel

### 1. Middleware Location

**File:** `app/Http/Middleware/VerifyCsrfToken.php`

```php
<?php

namespace App\Http\Middleware;

use Illuminate\Foundation\Http\Middleware\VerifyCsrfToken as Middleware;

class VerifyCsrfToken extends Middleware
{
    /**
     * The URIs that should be excluded from CSRF verification.
     */
    protected $except = [
        'api/*',
        'webhook/*',
        'stripe/*'
    ];
}
```

### 2. Kernel Registration

**File:** `app/Http/Kernel.php`

```php
protected $middlewareGroups = [
    'web' => [
        \App\Http\Middleware\EncryptCookies::class,
        \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
        \Illuminate\Session\Middleware\StartSession::class,
        \Illuminate\View\Middleware\ShareErrorsFromSession::class,
        \App\Http\Middleware\VerifyCsrfToken::class, // CSRF Middleware
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
    ],
];
```

### 3. Configuration Files

**File:** `config/session.php`
```php
'lifetime' => env('SESSION_LIFETIME', 120),
'expire_on_close' => false,
'encrypt' => false,
'files' => storage_path('framework/sessions'),
'connection' => env('SESSION_CONNECTION', null),
'table' => 'sessions',
'store' => env('SESSION_STORE', null),
'lottery' => [2, 100],
'cookie' => env('SESSION_COOKIE', Str::slug(env('APP_NAME', 'laravel'), '_').'_session'),
'path' => '/',
'domain' => env('SESSION_DOMAIN', null),
'secure' => env('SESSION_SECURE_COOKIE'),
'http_only' => true,
'same_site' => 'lax',
```

## CSRF Workflow in Laravel

### 1. Token Generation Flow

```
User Request → Session Start → Generate CSRF Token → Store in Session → Return Token
```

**Detailed Process:**
1. **Session Initialization**: Laravel starts a session for the user
2. **Token Generation**: A random token is generated using Laravel's `Str::random(40)`
3. **Session Storage**: Token stored in session as `_token`
4. **Template Rendering**: Token embedded in forms via `@csrf` directive
5. **Client Storage**: Token available in HTML and can be accessed via `csrf_token()`

### 2. Token Validation Flow

```
Form Submit → Extract Token → Compare with Session → Allow/Deny Request
```

**Detailed Process:**
1. **Request Received**: Laravel receives POST/PUT/PATCH/DELETE request
2. **Middleware Check**: `VerifyCsrfToken` middleware intercepts
3. **Token Extraction**: Token extracted from:
   - `_token` field in request body
   - `X-CSRF-TOKEN` header
   - `X-XSRF-TOKEN` header (from cookie)
4. **Validation**: Token compared with session-stored token
5. **Response**: Request proceeds if valid, returns 419 error if invalid

### 3. Code Flow Example

```php
// 1. Token Generation (in blade template)
{{ csrf_token() }} // Calls Session::token()

// 2. Token Validation (in VerifyCsrfToken middleware)
public function handle($request, Closure $next)
{
    if ($this->isReading($request) ||
        $this->runningUnitTests() ||
        $this->inExceptArray($request) ||
        $this->tokensMatch($request)) {
        return tap($next($request), function ($response) use ($request) {
            if ($this->shouldAddXsrfTokenCookie()) {
                $this->addCookieToResponse($request, $response);
            }
        });
    }

    throw new TokenMismatchException('CSRF token mismatch.');
}

// 3. Token Comparison
protected function tokensMatch($request)
{
    $token = $this->getTokenFromRequest($request);
    
    return is_string($request->session()->token()) &&
           is_string($token) &&
           hash_equals($request->session()->token(), $token);
}
```

## Common CSRF Issues and Fixes

### 1. **TokenMismatchException**

**Error:** `419 | Page Expired`

**Causes:**
- Missing `@csrf` directive in forms
- AJAX requests without CSRF token
- Session expired
- Cached forms with old tokens

**Solutions:**

```php
// Fix 1: Add CSRF to forms
<form method="POST">
    @csrf
    <!-- form fields -->
</form>

// Fix 2: Add to AJAX
$.ajaxSetup({
    headers: {
        'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
    }
});

// Fix 3: Exclude specific routes
// In VerifyCsrfToken middleware
protected $except = [
    'api/webhook',
    'payment/callback'
];

// Fix 4: Extend session lifetime
// In config/session.php
'lifetime' => env('SESSION_LIFETIME', 240), // 4 hours
```

### 2. **AJAX CSRF Issues**

**Problem:** AJAX requests failing with 419 error

**Solution:**
```javascript
// Complete AJAX setup
<script>
    // Set up CSRF for all AJAX requests
    $.ajaxSetup({
        headers: {
            'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
        }
    });

    // Alternative: Add to each request
    $.post('/api/endpoint', {
        _token: '{{ csrf_token() }}',
        data: 'value'
    });

    // For fetch API
    fetch('/api/endpoint', {
        method: 'POST',
        headers: {
            'X-CSRF-TOKEN': document.querySelector('meta[name="csrf-token"]').content,
            'Content-Type': 'application/json'
        },
        body: JSON.stringify({data: 'value'})
    });
</script>
```

### 3. **SPA (Single Page Application) Issues**

**Problem:** Token refresh in SPAs

**Solution:**
```javascript
// Method 1: Refresh token periodically
setInterval(() => {
    fetch('/csrf-token')
        .then(response => response.json())
        .then(data => {
            document.querySelector('meta[name="csrf-token"]').setAttribute('content', data.token);
            axios.defaults.headers.common['X-CSRF-TOKEN'] = data.token;
        });
}, 300000); // Refresh every 5 minutes

// Create route to get fresh token
Route::get('/csrf-token', function () {
    return response()->json(['token' => csrf_token()]);
});
```

### 4. **File Upload Issues**

**Problem:** File uploads with CSRF protection

**Solution:**
```html
<form method="POST" enctype="multipart/form-data" action="/upload">
    @csrf
    <input type="file" name="document">
    <button type="submit">Upload</button>
</form>
```

```javascript
// AJAX file upload
const formData = new FormData();
formData.append('file', fileInput.files[0]);
formData.append('_token', document.querySelector('meta[name="csrf-token"]').content);

fetch('/upload', {
    method: 'POST',
    body: formData
});
```

### 5. **API Route Protection**

**Problem:** Protecting API routes

**Solution:**
```php
// routes/api.php - Usually excluded from CSRF
Route::middleware(['auth:sanctum'])->group(function () {
    Route::post('/users', [UserController::class, 'store']);
});

// routes/web.php - CSRF protected by default
Route::post('/users', [UserController::class, 'store'])->middleware('auth');

// Custom API with CSRF (if needed)
Route::middleware(['web'])->group(function () {
    Route::post('/api/internal/users', [UserController::class, 'store']);
});
```

## Advanced CSRF Configuration

### 1. **Custom Token Validation**

```php
// Custom VerifyCsrfToken middleware
class VerifyCsrfToken extends Middleware
{
    protected function tokensMatch($request)
    {
        $token = $this->getTokenFromRequest($request);
        
        // Custom validation logic
        if ($this->isApiRoute($request)) {
            return $this->validateApiToken($request, $token);
        }
        
        return parent::tokensMatch($request);
    }
    
    private function isApiRoute($request)
    {
        return $request->is('api/*');
    }
    
    private function validateApiToken($request, $token)
    {
        // Custom API token validation
        return hash_equals($request->session()->token(), $token);
    }
}
```

### 2. **Environment-Specific Configuration**

```php
// .env configuration
SESSION_LIFETIME=120
SESSION_SECURE_COOKIE=true
SESSION_HTTP_ONLY=true
SESSION_SAME_SITE=strict

// config/session.php
'secure' => env('SESSION_SECURE_COOKIE', false),
'http_only' => env('SESSION_HTTP_ONLY', true),
'same_site' => env('SESSION_SAME_SITE', 'lax'),
```

### 3. **Testing CSRF Protection**

```php
// Feature test example
class UserCreationTest extends TestCase
{
    public function test_user_creation_requires_csrf_token()
    {
        $response = $this->post('/users', [
            'name' => 'John Doe',
            'email' => 'john@example.com'
        ]);
        
        $response->assertStatus(419); // Token mismatch
    }
    
    public function test_user_creation_with_valid_csrf_token()
    {
        $response = $this->withSession(['_token' => 'test-token'])
                         ->post('/users', [
                             '_token' => 'test-token',
                             'name' => 'John Doe',
                             'email' => 'john@example.com'
                         ]);
        
        $response->assertStatus(201);
    }
}
```

## Best Practices

### 1. **Always Use CSRF Protection**
- Enable for all state-changing operations
- Don't disable globally unless absolutely necessary

### 2. **Proper Token Management**
- Use Laravel's built-in helpers
- Don't expose tokens in URLs
- Regenerate tokens after login

### 3. **AJAX Implementation**
- Set up global AJAX headers
- Handle token refresh for SPAs
- Provide user feedback for expired tokens

### 4. **Error Handling**
- Customize 419 error pages
- Provide clear user instructions
- Log CSRF failures for monitoring

### 5. **Testing**
- Test CSRF protection in feature tests
- Verify token generation and validation
- Test error scenarios

## Conclusion

CSRF protection is a critical security feature that Laravel handles elegantly through its middleware system. By understanding how CSRF tokens work, where they're implemented, and how to troubleshoot common issues, you can ensure your Laravel applications are secure against cross-site request forgery attacks.

Remember to always include CSRF tokens in your forms, properly configure AJAX requests, and regularly test your CSRF implementation to maintain robust security.