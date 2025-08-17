# ⏳ Laravel Rate Limiting & Throttling Guide

## 📋 Table of Contents
- [Overview](#overview)
- [What is Rate Limiting?](#what-is-rate-limiting)
- [Benefits & Use Cases](#benefits--use-cases)
- [Types of Rate Limiting](#types-of-rate-limiting)
- [Laravel Built-in Throttling](#laravel-built-in-throttling)
- [Custom Rate Limiting](#custom-rate-limiting)
- [Advanced Rate Limiting](#advanced-rate-limiting)
- [Database-based Rate Limiting](#database-based-rate-limiting)
- [Redis Rate Limiting](#redis-rate-limiting)
- [API Rate Limiting](#api-rate-limiting)
- [Middleware Implementation](#middleware-implementation)
- [Response Headers](#response-headers)
- [Testing Rate Limits](#testing-rate-limits)
- [Monitoring & Analytics](#monitoring--analytics)
- [Best Practices](#best-practices)
- [Troubleshooting](#troubleshooting)

## 🎯 Overview

Rate limiting and throttling are essential techniques for controlling the frequency of requests to your Laravel application. This guide covers built-in Laravel features, custom implementations, and advanced strategies for protecting your application from abuse and ensuring fair resource usage.

## 🔍 What is Rate Limiting?

Rate limiting is a technique used to control the rate at which users can make requests to an application or API. It prevents abuse, ensures fair usage, and protects system resources from being overwhelmed.

### Key Concepts:
- 🚦 **Throttling**: Controlling request frequency
- ⏰ **Time Windows**: Fixed or sliding time periods
- 🎯 **Limits**: Maximum requests allowed
- 🔑 **Keys**: Unique identifiers (IP, User, API key)
- 🛡️ **Protection**: Defense against abuse and DoS attacks

## ✅ Benefits & Use Cases

| Benefit | Description | Use Case |
|---------|-------------|----------|
| 🛡️ **Security** | Prevents brute force attacks | Login attempts, password resets |
| ⚡ **Performance** | Maintains system stability | High-traffic APIs |
| 💰 **Cost Control** | Manages resource usage | Third-party API costs |
| 🎯 **Fair Usage** | Ensures equal access | Public APIs |
| 📊 **Quality of Service** | Prevents system overload | Database-heavy operations |

### Common Scenarios:
- 🔐 **Authentication**: Login/registration attempts
- 📧 **Email Sending**: Newsletter subscriptions, contact forms
- 🔍 **Search Queries**: Preventing search spam
- 💬 **Comments/Posts**: Anti-spam measures
- 📱 **API Endpoints**: External API usage control
- 💳 **Payment Processing**: Transaction limits

## 🏷️ Types of Rate Limiting

### 🪣 **Token Bucket**
- Fixed capacity bucket with tokens
- Tokens replenish at steady rate
- Allows burst traffic

### 📊 **Fixed Window**
- Fixed time periods (e.g., per minute/hour)
- Counter resets at window boundary
- Simple but can allow bursts

### 🔄 **Sliding Window**
- Rolling time window
- More accurate than fixed window
- Higher memory usage

### 🚰 **Leaky Bucket**
- Requests processed at constant rate
- Excess requests discarded
- Smooth traffic flow

## 🚦 Laravel Built-in Throttling

### ⚙️ Basic Configuration

Laravel provides built-in rate limiting through the `throttle` middleware:

```php
// config/app.php - Default rate limiters
// app/Providers/RouteServiceProvider.php

use Illuminate\Cache\RateLimiting\Limit;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\RateLimiter;

public function boot()
{
    $this->configureRateLimiting();
    // ... other boot logic
}

protected function configureRateLimiting()
{
    // 🌐 API Rate Limiting
    RateLimiter::for('api', function (Request $request) {
        return Limit::perMinute(60)->by(optional($request->user())->id ?: $request->ip());
    });

    // 🔐 Login Rate Limiting
    RateLimiter::for('login', function (Request $request) {
        return [
            Limit::perMinute(5)->by($request->email.$request->ip()),
            Limit::perHour(20)->by($request->ip()),
        ];
    });

    // 📧 Email Rate Limiting
    RateLimiter::for('emails', function (Request $request) {
        return Limit::perMinute(10)->by($request->user()->id);
    });

    // 🔍 Search Rate Limiting
    RateLimiter::for('search', function (Request $request) {
        return Limit::perMinute(30)->by($request->ip());
    });
}
```

### 🛣️ Route-level Throttling

```php
// routes/web.php
Route::middleware(['throttle:60,1'])->group(function () {
    Route::get('/api/users', [UserController::class, 'index']);
    Route::post('/api/users', [UserController::class, 'store']);
});

// routes/api.php
Route::middleware(['throttle:api'])->group(function () {
    Route::apiResource('posts', PostController::class);
});

// Named rate limiter
Route::middleware(['throttle:login'])->group(function () {
    Route::post('/login', [AuthController::class, 'login']);
    Route::post('/register', [AuthController::class, 'register']);
});

// Dynamic rate limiting
Route::get('/download/{file}', [DownloadController::class, 'show'])
     ->middleware('throttle:downloads');
```

### 🎯 Dynamic Rate Limiting

```php
// app/Providers/RouteServiceProvider.php
protected function configureRateLimiting()
{
    // 👥 User-based dynamic limits
    RateLimiter::for('user-requests', function (Request $request) {
        $user = $request->user();
        
        if (!$user) {
            return Limit::perMinute(10)->by($request->ip());
        }

        // Different limits based on user type
        return match ($user->subscription_tier) {
            'premium' => Limit::perMinute(1000)->by($user->id),
            'pro' => Limit::perMinute(500)->by($user->id),
            'basic' => Limit::perMinute(100)->by($user->id),
            default => Limit::perMinute(50)->by($user->id),
        };
    });

    // 🕐 Time-based dynamic limits
    RateLimiter::for('time-based', function (Request $request) {
        $hour = now()->hour;
        
        // Higher limits during business hours
        $limit = ($hour >= 9 && $hour <= 17) ? 100 : 50;
        
        return Limit::perMinute($limit)->by($request->user()->id ?: $request->ip());
    });

    // 🏢 Company-based limits
    RateLimiter::for('company-api', function (Request $request) {
        $company = $request->user()?->company;
        
        if (!$company) {
            return Limit::perMinute(10)->by($request->ip());
        }

        return Limit::perMinute($company->api_limit)->by($company->id)
                    ->response(function () use ($company) {
                        return response()->json([
                            'error' => '🚦 Company API limit exceeded',
                            'limit' => $company->api_limit,
                            'reset_time' => now()->addMinute()->toISOString()
                        ], 429);
                    });
    });
}
```

## 🛠️ Custom Rate Limiting

### 🎨 Custom Rate Limiter Service

```php
// app/Services/RateLimiterService.php
<?php

namespace App\Services;

use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\Redis;
use Carbon\Carbon;

class RateLimiterService
{
    protected $cache;

    public function __construct()
    {
        $this->cache = Cache::store(config('rate_limiter.store', 'redis'));
    }

    /**
     * 🚦 Check if request is allowed
     */
    public function attempt(string $key, int $maxAttempts, int $decayMinutes): bool
    {
        $attempts = $this->getAttempts($key);
        
        if ($attempts >= $maxAttempts) {
            return false;
        }

        $this->incrementAttempts($key, $decayMinutes);
        return true;
    }

    /**
     * 📊 Get current attempts
     */
    public function getAttempts(string $key): int
    {
        return $this->cache->get($this->getAttemptKey($key), 0);
    }

    /**
     * ⬆️ Increment attempts
     */
    public function incrementAttempts(string $key, int $decayMinutes): int
    {
        $attemptKey = $this->getAttemptKey($key);
        
        $attempts = $this->cache->get($attemptKey, 0) + 1;
        
        $this->cache->put($attemptKey, $attempts, now()->addMinutes($decayMinutes));
        
        return $attempts;
    }

    /**
     * 🔄 Reset attempts
     */
    public function resetAttempts(string $key): void
    {
        $this->cache->forget($this->getAttemptKey($key));
    }

    /**
     * ⏰ Get time until reset
     */
    public function getTimeUntilReset(string $key): int
    {
        $resetTime = $this->cache->get($this->getResetTimeKey($key));
        
        if (!$resetTime) {
            return 0;
        }

        return max(0, Carbon::parse($resetTime)->diffInSeconds(now()));
    }

    /**
     * 🔑 Generate attempt key
     */
    protected function getAttemptKey(string $key): string
    {
        return "rate_limit:attempts:{$key}";
    }

    /**
     * 🕐 Generate reset time key
     */
    protected function getResetTimeKey(string $key): string
    {
        return "rate_limit:reset:{$key}";
    }

    /**
     * 🪣 Token bucket implementation
     */
    public function tokenBucket(string $key, int $capacity, int $refillRate, int $refillPeriod): bool
    {
        $bucketKey = "token_bucket:{$key}";
        $bucket = $this->cache->get($bucketKey, [
            'tokens' => $capacity,
            'last_refill' => now()->timestamp,
        ]);

        // Calculate tokens to add
        $now = now()->timestamp;
        $timePassed = $now - $bucket['last_refill'];
        $tokensToAdd = floor($timePassed / $refillPeriod) * $refillRate;

        // Refill bucket
        $bucket['tokens'] = min($capacity, $bucket['tokens'] + $tokensToAdd);
        $bucket['last_refill'] = $now;

        // Check if request allowed
        if ($bucket['tokens'] < 1) {
            $this->cache->put($bucketKey, $bucket, now()->addHour());
            return false;
        }

        // Consume token
        $bucket['tokens']--;
        $this->cache->put($bucketKey, $bucket, now()->addHour());
        
        return true;
    }

    /**
     * 🔄 Sliding window implementation
     */
    public function slidingWindow(string $key, int $limit, int $windowSeconds): bool
    {
        $windowKey = "sliding_window:{$key}";
        $now = now()->timestamp;
        $windowStart = $now - $windowSeconds;

        // Get current requests in window
        $requests = $this->cache->get($windowKey, []);
        
        // Filter out old requests
        $requests = array_filter($requests, fn($timestamp) => $timestamp > $windowStart);

        // Check limit
        if (count($requests) >= $limit) {
            return false;
        }

        // Add current request
        $requests[] = $now;
        
        // Store updated requests
        $this->cache->put($windowKey, $requests, now()->addSeconds($windowSeconds));
        
        return true;
    }
}
```

### 🛡️ Custom Rate Limiting Middleware

```php
// app/Http/Middleware/CustomRateLimit.php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use App\Services\RateLimiterService;

class CustomRateLimit
{
    protected $rateLimiter;

    public function __construct(RateLimiterService $rateLimiter)
    {
        $this->rateLimiter = $rateLimiter;
    }

    public function handle(Request $request, Closure $next, string $type = 'default')
    {
        $key = $this->generateKey($request, $type);
        $config = $this->getConfig($type);

        if (!$this->rateLimiter->attempt($key, $config['max_attempts'], $config['decay_minutes'])) {
            return $this->buildResponse($key, $config);
        }

        $response = $next($request);
        
        return $this->addRateLimitHeaders($response, $key, $config);
    }

    /**
     * 🔑 Generate rate limit key
     */
    protected function generateKey(Request $request, string $type): string
    {
        return match ($type) {
            'login' => $request->ip() . '|' . $request->input('email', ''),
            'api' => $request->user()?->id ?: $request->ip(),
            'ip' => $request->ip(),
            'user' => $request->user()?->id ?: $request->ip(),
            default => $request->ip(),
        };
    }

    /**
     * ⚙️ Get rate limit configuration
     */
    protected function getConfig(string $type): array
    {
        return match ($type) {
            'login' => ['max_attempts' => 5, 'decay_minutes' => 15],
            'api' => ['max_attempts' => 60, 'decay_minutes' => 1],
            'email' => ['max_attempts' => 10, 'decay_minutes' => 60],
            'search' => ['max_attempts' => 30, 'decay_minutes' => 1],
            default => ['max_attempts' => 20, 'decay_minutes' => 1],
        };
    }

    /**
     * 🚫 Build rate limit exceeded response
     */
    protected function buildResponse(string $key, array $config)
    {
        $timeUntilReset = $this->rateLimiter->getTimeUntilReset($key);
        
        return response()->json([
            'success' => false,
            'message' => '🚦 Rate limit exceeded',
            'error' => 'Too many requests. Please try again later.',
            'retry_after' => $timeUntilReset,
            'limit' => $config['max_attempts'],
            'reset_time' => now()->addSeconds($timeUntilReset)->toISOString(),
        ], 429)->withHeaders([
            'X-RateLimit-Limit' => $config['max_attempts'],
            'X-RateLimit-Remaining' => 0,
            'X-RateLimit-Reset' => now()->addSeconds($timeUntilReset)->timestamp,
            'Retry-After' => $timeUntilReset,
        ]);
    }

    /**
     * 📊 Add rate limit headers
     */
    protected function addRateLimitHeaders($response, string $key, array $config)
    {
        $attempts = $this->rateLimiter->getAttempts($key);
        $remaining = max(0, $config['max_attempts'] - $attempts);
        $resetTime = now()->addMinutes($config['decay_minutes'])->timestamp;

        return $response->withHeaders([
            'X-RateLimit-Limit' => $config['max_attempts'],
            'X-RateLimit-Remaining' => $remaining,
            'X-RateLimit-Reset' => $resetTime,
        ]);
    }
}
```

## 🚀 Advanced Rate Limiting

### 🎯 Multi-tier Rate Limiting

```php
// app/Services/MultiTierRateLimiter.php
<?php

namespace App\Services;

use Illuminate\Http\Request;
use App\Models\User;

class MultiTierRateLimiter
{
    protected $rateLimiter;

    public function __construct(RateLimiterService $rateLimiter)
    {
        $this->rateLimiter = $rateLimiter;
    }

    /**
     * 🏆 Check multiple rate limit tiers
     */
    public function checkLimits(Request $request, array $limits): array
    {
        $user = $request->user();
        $results = [];

        foreach ($limits as $name => $config) {
            $key = $this->generateTieredKey($request, $user, $name);
            $allowed = $this->rateLimiter->attempt(
                $key,
                $config['limit'],
                $config['window']
            );

            $results[$name] = [
                'allowed' => $allowed,
                'attempts' => $this->rateLimiter->getAttempts($key),
                'limit' => $config['limit'],
                'window' => $config['window'],
                'reset_time' => $this->rateLimiter->getTimeUntilReset($key),
            ];

            if (!$allowed) {
                break; // Stop checking if any limit is exceeded
            }
        }

        return $results;
    }

    /**
     * 🔑 Generate tiered key based on user and request
     */
    protected function generateTieredKey(Request $request, ?User $user, string $tier): string
    {
        $baseKey = $user ? "user:{$user->id}" : "ip:{$request->ip()}";
        return "{$baseKey}:tier:{$tier}";
    }

    /**
     * 📊 Get rate limit configuration for user
     */
    public function getUserLimits(User $user): array
    {
        return match ($user->subscription_tier ?? 'free') {
            'enterprise' => [
                'per_second' => ['limit' => 10, 'window' => 1/60], // 1 second
                'per_minute' => ['limit' => 500, 'window' => 1],
                'per_hour' => ['limit' => 10000, 'window' => 60],
                'per_day' => ['limit' => 100000, 'window' => 1440],
            ],
            'premium' => [
                'per_second' => ['limit' => 5, 'window' => 1/60],
                'per_minute' => ['limit' => 200, 'window' => 1],
                'per_hour' => ['limit' => 5000, 'window' => 60],
                'per_day' => ['limit' => 50000, 'window' => 1440],
            ],
            'basic' => [
                'per_minute' => ['limit' => 100, 'window' => 1],
                'per_hour' => ['limit' => 2000, 'window' => 60],
                'per_day' => ['limit' => 10000, 'window' => 1440],
            ],
            default => [
                'per_minute' => ['limit' => 20, 'window' => 1],
                'per_hour' => ['limit' => 500, 'window' => 60],
                'per_day' => ['limit' => 2000, 'window' => 1440],
            ],
        };
    }
}
```

### 🌍 Geographic Rate Limiting

```php
// app/Services/GeographicRateLimiter.php
<?php

namespace App\Services;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Http;

class GeographicRateLimiter
{
    protected $rateLimiter;

    public function __construct(RateLimiterService $rateLimiter)
    {
        $this->rateLimiter = $rateLimiter;
    }

    /**
     * 🌍 Apply geographic-based rate limiting
     */
    public function checkGeographicLimits(Request $request): bool
    {
        $ip = $request->ip();
        $country = $this->getCountryFromIP($ip);
        
        $limits = $this->getCountryLimits($country);
        $key = "geo:{$country}:{$ip}";

        return $this->rateLimiter->attempt(
            $key,
            $limits['max_attempts'],
            $limits['decay_minutes']
        );
    }

    /**
     * 🗺️ Get country from IP address
     */
    protected function getCountryFromIP(string $ip): string
    {
        try {
            // Using a free IP geolocation service
            $response = Http::timeout(2)->get("http://ip-api.com/json/{$ip}");
            
            if ($response->successful()) {
                $data = $response->json();
                return $data['countryCode'] ?? 'UNKNOWN';
            }
        } catch (\Exception $e) {
            \Log::warning("Failed to get country for IP {$ip}: " . $e->getMessage());
        }

        return 'UNKNOWN';
    }

    /**
     * 🏳️ Get rate limits by country
     */
    protected function getCountryLimits(string $country): array
    {
        // High-risk countries with stricter limits
        $highRiskCountries = ['CN', 'RU', 'IR', 'KP'];
        
        // Trusted countries with relaxed limits
        $trustedCountries = ['US', 'CA', 'GB', 'DE', 'FR', 'JP', 'AU'];

        if (in_array($country, $highRiskCountries)) {
            return ['max_attempts' => 10, 'decay_minutes' => 5];
        }

        if (in_array($country, $trustedCountries)) {
            return ['max_attempts' => 100, 'decay_minutes' => 1];
        }

        // Default limits
        return ['max_attempts' => 50, 'decay_minutes' => 1];
    }
}
```

## 🗄️ Database-based Rate Limiting

### 📊 Database Schema

```php
// database/migrations/create_rate_limits_table.php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up()
    {
        Schema::create('rate_limits', function (Blueprint $table) {
            $table->id();
            $table->string('key')->index();
            $table->string('identifier'); // IP, user_id, etc.
            $table->string('type'); // login, api, email, etc.
            $table->integer('attempts')->default(0);
            $table->integer('max_attempts');
            $table->timestamp('window_start');
            $table->timestamp('window_end');
            $table->timestamp('reset_at');
            $table->json('metadata')->nullable();
            $table->timestamps();

            $table->unique(['key', 'window_start']);
            $table->index(['identifier', 'type']);
            $table->index('reset_at');
        });
    }

    public function down()
    {
        Schema::dropIfExists('rate_limits');
    }
};
```

### 🏗️ Database Rate Limiter Model

```php
// app/Models/RateLimit.php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Carbon\Carbon;

class RateLimit extends Model
{
    protected $fillable = [
        'key',
        'identifier',
        'type',
        'attempts',
        'max_attempts',
        'window_start',
        'window_end',
        'reset_at',
        'metadata',
    ];

    protected $casts = [
        'window_start' => 'datetime',
        'window_end' => 'datetime',
        'reset_at' => 'datetime',
        'metadata' => 'array',
    ];

    /**
     * 🔍 Find or create rate limit record
     */
    public static function findOrCreateForKey(string $key, string $identifier, string $type, int $maxAttempts, int $windowMinutes): self
    {
        $now = now();
        $windowStart = $now->startOfMinute();
        $windowEnd = $windowStart->copy()->addMinutes($windowMinutes);

        return static::firstOrCreate(
            [
                'key' => $key,
                'window_start' => $windowStart,
            ],
            [
                'identifier' => $identifier,
                'type' => $type,
                'attempts' => 0,
                'max_attempts' => $maxAttempts,
                'window_end' => $windowEnd,
                'reset_at' => $windowEnd,
            ]
        );
    }

    /**
     * ⬆️ Increment attempts
     */
    public function incrementAttempts(): bool
    {
        if ($this->attempts >= $this->max_attempts) {
            return false;
        }

        $this->increment('attempts');
        return true;
    }

    /**
     * ⏰ Check if rate limit is active
     */
    public function isActive(): bool
    {
        return now()->lt($this->reset_at) && $this->attempts >= $this->max_attempts;
    }

    /**
     * 🔄 Reset attempts
     */
    public function reset(): void
    {
        $this->update(['attempts' => 0]);
    }

    /**
     * 🧹 Clean expired records
     */
    public static function cleanExpired(): int
    {
        return static::where('reset_at', '<', now())->delete();
    }
}
```

### 📊 Database Rate Limiter Service

```php
// app/Services/DatabaseRateLimiter.php
<?php

namespace App\Services;

use App\Models\RateLimit;
use Illuminate\Http\Request;

class DatabaseRateLimiter
{
    /**
     * 🚦 Check rate limit
     */
    public function attempt(string $identifier, string $type, int $maxAttempts, int $windowMinutes): array
    {
        $key = $this->generateKey($identifier, $type);
        
        $rateLimit = RateLimit::findOrCreateForKey(
            $key,
            $identifier,
            $type,
            $maxAttempts,
            $windowMinutes
        );

        $allowed = !$rateLimit->isActive();

        if ($allowed) {
            $success = $rateLimit->incrementAttempts();
            $allowed = $success;
        }

        return [
            'allowed' => $allowed,
            'attempts' => $rateLimit->attempts,
            'max_attempts' => $rateLimit->max_attempts,
            'reset_at' => $rateLimit->reset_at,
            'seconds_until_reset' => max(0, $rateLimit->reset_at->diffInSeconds(now())),
        ];
    }

    /**
     * 📊 Get rate limit status
     */
    public function status(string $identifier, string $type): ?array
    {
        $key = $this->generateKey($identifier, $type);
        
        $rateLimit = RateLimit::where('key', $key)
            ->where('reset_at', '>', now())
            ->latest()
            ->first();

        if (!$rateLimit) {
            return null;
        }

        return [
            'attempts' => $rateLimit->attempts,
            'max_attempts' => $rateLimit->max_attempts,
            'remaining' => max(0, $rateLimit->max_attempts - $rateLimit->attempts),
            'reset_at' => $rateLimit->reset_at,
            'is_active' => $rateLimit->isActive(),
        ];
    }

    /**
     * 🔑 Generate rate limit key
     */
    protected function generateKey(string $identifier, string $type): string
    {
        $window = now()->format('Y-m-d H:i');
        return "rate_limit:{$type}:{$identifier}:{$window}";
    }

    /**
     * 🔄 Reset rate limit
     */
    public function reset(string $identifier, string $type): bool
    {
        $key = $this->generateKey($identifier, $type);
        
        return RateLimit::where('key', $key)->delete() > 0;
    }
}
```

## 🔴 Redis Rate Limiting

### ⚡ Redis-based Implementation

```php
// app/Services/RedisRateLimiter.php
<?php

namespace App\Services;

use Illuminate\Support\Facades\Redis;
use Carbon\Carbon;

class RedisRateLimiter
{
    protected $redis;
    protected $prefix = 'rate_limit:';

    public function __construct()
    {
        $this->redis = Redis::connection();
    }

    /**
     * 🪣 Token bucket with Redis
     */
    public function tokenBucket(string $key, int $capacity, int $refillRate, int $refillInterval): bool
    {
        $bucketKey = $this->prefix . "bucket:{$key}";
        
        // Lua script for atomic token bucket operation
        $luaScript = "
            local bucket_key = KEYS[1]
            local capacity = tonumber(ARGV[1])
            local refill_rate = tonumber(ARGV[2])
            local refill_interval = tonumber(ARGV[3])
            local now = tonumber(ARGV[4])
            
            local bucket = redis.call('HMGET', bucket_key, 'tokens', 'last_refill')
            local tokens = tonumber(bucket[1]) or capacity
            local last_refill = tonumber(bucket[2]) or now
            
            -- Calculate tokens to add
            local time_passed = now - last_refill
            local intervals = math.floor(time_passed / refill_interval)
            local tokens_to_add = intervals * refill_rate
            
            -- Refill bucket
            tokens = math.min(capacity, tokens + tokens_to_add)
            
            -- Check if request allowed
            if tokens < 1 then
                redis.call('HMSET', bucket_key, 'tokens', tokens, 'last_refill', now)
                redis.call('EXPIRE', bucket_key, 3600)
                return {0, tokens, capacity}
            end
            
            -- Consume token
            tokens = tokens - 1
            redis.call('HMSET', bucket_key, 'tokens', tokens, 'last_refill', now)
            redis.call('EXPIRE', bucket_key, 3600)
            
            return {1, tokens, capacity}
        ";

        $result = $this->redis->eval(
            $luaScript,
            1,
            $bucketKey,
            $capacity,
            $refillRate,
            $refillInterval,
            now()->timestamp
        );

        return $result[0] == 1;
    }

    /**
     * 🔄 Sliding window counter with Redis
     */
    public function slidingWindow(string $key, int $limit, int $windowSeconds): bool
    {
        $windowKey = $this->prefix . "window:{$key}";
        $now = now()->timestamp;
        $windowStart = $now - $windowSeconds;

        // Lua script for atomic sliding window operation
        $luaScript = "
            local window_key = KEYS[1]
            local window_start = tonumber(ARGV[1])
            local now = tonumber(ARGV[2])
            local limit = tonumber(ARGV[3])
            local window_seconds = tonumber(ARGV[4])
            
            -- Remove expired entries
            redis.call('ZREMRANGEBYSCORE', window_key, '-inf', window_start)
            
            -- Count current requests
            local current_count = redis.call('ZCARD', window_key)
            
            if current_count >= limit then
                return {0, current_count, limit}
            end
            
            -- Add current request
            redis.call('ZADD', window_key, now, now)
            redis.call('EXPIRE', window_key, window_seconds)
            
            return {1, current_count + 1, limit}
        ";

        $result = $this->redis->eval(
            $luaScript,
            1,
            $windowKey,
            $windowStart,
            $now,
            $limit,
            $windowSeconds
        );

        return $result[0] == 1;
    }

    /**
     * 📊 Fixed window counter with Redis
     */
    public function fixedWindow(string $key, int $limit, int $windowSeconds): bool
    {
        $windowKey = $this->prefix . "fixed:{$key}:" . floor(now()->timestamp / $windowSeconds);

        // Lua script for atomic fixed window operation
        $luaScript = "
            local window_key = KEYS[1]
            local limit = tonumber(ARGV[1])
            local window_seconds = tonumber(ARGV[2])
            
            local current = redis.call('GET', window_key)
            current = tonumber(current) or 0
            
            if current >= limit then
                return {0, current, limit}
            end
            
            local new_count = redis.call('INCR', window_key)
            if new_count == 1 then
                redis.call('EXPIRE', window_key, window_seconds)
            end
            
            return {1, new_count, limit}
        ";

        $result = $this->redis->eval(
            $luaScript,
            1,
            $windowKey,
            $limit,
            $windowSeconds
        );

        return $result[0] == 1;
    }

    /**
     * 📈 Get rate limit statistics
     */
    public function getStats(string $key, string $type = 'window'): array
    {
        $statsKey = $this->prefix . "{$type}:{$key}";

        switch ($type) {
            case 'bucket':
                $bucket = $this->redis->hmget($statsKey, 'tokens', 'last_refill');
                return [
                    'tokens' => (int) ($bucket[0] ?? 0),
                    'last_refill' => (int) ($bucket[1] ?? 0),
                ];

            case 'window':
                $count = $this->redis->zcard($statsKey);
                return [
                    'current_requests' => (int) $count,
                    'window_start' => now()->subMinutes(1)->timestamp,
                    'window_end' => now()->timestamp,
                ];

            case 'fixed':
                $windowKey = $statsKey . ':' . floor(now()->timestamp / 60);
                $count = $this->redis->get($windowKey) ?? 0;
                return [
                    'current_requests' => (int) $count,
                    'window_key' => $windowKey,
                ];

            default:
                return [];
        }
    }

    /**
     * 🧹 Clean up expired keys
     */
    public function cleanup(): int
    {
        $pattern = $this->prefix . '*';
        $keys = $this->redis->keys($pattern);
        $cleaned = 0;

        foreach ($keys as $key) {
            $ttl = $this->redis->ttl($key);
            if ($ttl === -1) { // Keys without expiration
                $this->redis->expire($key, 3600); // Set 1 hour expiration
                $cleaned++;
            }
        }

        return $cleaned;
    }
}
```

## 📱 API Rate Limiting

### 🔑 API Key Based Limiting

```php
// app/Http/Middleware/ApiKeyRateLimit.php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use App\Models\ApiKey;
use App\Services\RedisRateLimiter;

class ApiKeyRateLimit
{
    protected $rateLimiter;

    public function __construct(RedisRateLimiter $rateLimiter)
    {
        $this->rateLimiter = $rateLimiter;
    }

    public function handle(Request $request, Closure $next)
    {
        $apiKey = $this->extractApiKey($request);

        if (!$apiKey) {
            return response()->json([
                'success' => false,
                'message' => '🔑 API key required'
            ], 401);
        }

        $apiKeyModel = ApiKey::where('key', $apiKey)->where('is_active', true)->first();

        if (!$apiKeyModel) {
            return response()->json([
                'success' => false,
                'message' => '🚫 Invalid API key'
            ], 401);
        }

        // Apply rate limiting based on API key tier
        $allowed = $this->checkRateLimit($apiKeyModel);

        if (!$allowed) {
            return response()->json([
                'success' => false,
                'message' => '🚦 API rate limit exceeded',
                'limit' => $apiKeyModel->rate_limit,
                'window' => $apiKeyModel->rate_window,
                'retry_after' => $this->getRetryAfter($apiKeyModel)
            ], 429);
        }

        // Add API key to request for controllers to use
        $request->merge(['api_key_model' => $apiKeyModel]);

        $response = $next($request);

        return $this->addApiRateLimitHeaders($response, $apiKeyModel);
    }

    /**
     * 🔍 Extract API key from request
     */
    protected function extractApiKey(Request $request): ?string
    {
        // Check header first
        if ($request->hasHeader('X-API-Key')) {
            return $request->header('X-API-Key');
        }

        // Check authorization header
        $authorization = $request->header('Authorization');
        if ($authorization && str_starts_with($authorization, 'Bearer ')) {
            return substr($authorization, 7);
        }

        // Check query parameter
        return $request->query('api_key');
    }

    /**
     * 🚦 Check rate limit for API key
     */
    protected function checkRateLimit(ApiKey $apiKey): bool
    {
        $key = "api_key:{$apiKey->id}";
        
        return $this->rateLimiter->slidingWindow(
            $key,
            $apiKey->rate_limit,
            $apiKey->rate_window * 60 // Convert minutes to seconds
        );
    }

    /**
     * ⏰ Get retry after seconds
     */
    protected function getRetryAfter(ApiKey $apiKey): int
    {
        return $apiKey->rate_window * 60; // Convert minutes to seconds
    }

    /**
     * 📊 Add API rate limit headers
     */
    protected function addApiRateLimitHeaders($response, ApiKey $apiKey)
    {
        $stats = $this->rateLimiter->getStats("api_key:{$apiKey->id}");
        $remaining = max(0, $apiKey->rate_limit - ($stats['current_requests'] ?? 0));

        return $response->withHeaders([
            'X-RateLimit-Limit' => $apiKey->rate_limit,
            'X-RateLimit-Remaining' => $remaining,
            'X-RateLimit-Reset' => now()->addMinutes($apiKey->rate_window)->timestamp,
            'X-RateLimit-Window' => $apiKey->rate_window . ' minutes',
        ]);
    }
}
```

### 🏗️ API Key Model

```php
// app/Models/ApiKey.php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;

class ApiKey extends Model
{
    use HasFactory;

    protected $fillable = [
        'user_id',
        'name',
        'key',
        'tier',
        'rate_limit',
        'rate_window',
        'is_active',
        'last_used_at',
        'expires_at',
    ];

    protected $casts = [
        'is_active' => 'boolean',
        'last_used_at' => 'datetime',
        'expires_at' => 'datetime',
    ];

    /**
     * 👤 Get the user that owns the API key
     */
    public function user()
    {
        return $this->belongsTo(User::class);
    }

    /**
     * 🔑 Generate new API key
     */
    public static function generate(int $userId, string $name, string $tier = 'basic'): self
    {
        $limits = self::getTierLimits($tier);

        return self::create([
            'user_id' => $userId,
            'name' => $name,
            'key' => 'ak_' . bin2hex(random_bytes(32)),
            'tier' => $tier,
            'rate_limit' => $limits['rate_limit'],
            'rate_window' => $limits['rate_window'],
            'is_active' => true,
        ]);
    }

    /**
     * 🏆 Get rate limits for tier
     */
    public static function getTierLimits(string $tier): array
    {
        return match ($tier) {
            'enterprise' => [
                'rate_limit' => 10000,
                'rate_window' => 60, // per hour
            ],
            'premium' => [
                'rate_limit' => 1000,
                'rate_window' => 60, // per hour
            ],
            'pro' => [
                'rate_limit' => 500,
                'rate_window' => 60, // per hour
            ],
            default => [
                'rate_limit' => 100,
                'rate_window' => 60, // per hour
            ],
        };
    }

    /**
     * ⏰ Update last used timestamp
     */
    public function markAsUsed(): void
    {
        $this->update(['last_used_at' => now()]);
    }

    /**
     * ✅ Check if API key is valid
     */
    public function isValid(): bool
    {
        return $this->is_active && 
               ($this->expires_at === null || $this->expires_at->isFuture());
    }
}
```

## 🎭 Middleware Implementation

### 🛡️ Comprehensive Rate Limiting Middleware

```php
// app/Http/Middleware/ComprehensiveRateLimit.php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use App\Services\MultiTierRateLimiter;
use App\Services\GeographicRateLimiter;
use App\Services\RedisRateLimiter;

class ComprehensiveRateLimit
{
    protected $multiTierLimiter;
    protected $geoLimiter;
    protected $redisLimiter;

    public function __construct(
        MultiTierRateLimiter $multiTierLimiter,
        GeographicRateLimiter $geoLimiter,
        RedisRateLimiter $redisLimiter
    ) {
        $this->multiTierLimiter = $multiTierLimiter;
        $this->geoLimiter = $geoLimiter;
        $this->redisLimiter = $redisLimiter;
    }

    public function handle(Request $request, Closure $next, ...$parameters)
    {
        $config = $this->parseParameters($parameters);
        
        // 🌍 Geographic rate limiting
        if ($config['geo_limiting'] && !$this->geoLimiter->checkGeographicLimits($request)) {
            return $this->buildGeoLimitResponse();
        }

        // 🏆 Multi-tier rate limiting
        if ($config['multi_tier']) {
            $user = $request->user();
            if ($user) {
                $limits = $this->multiTierLimiter->getUserLimits($user);
                $results = $this->multiTierLimiter->checkLimits($request, $limits);
                
                $blockedTier = $this->findBlockedTier($results);
                if ($blockedTier) {
                    return $this->buildTierLimitResponse($blockedTier, $results[$blockedTier]);
                }
            }
        }

        // 🔴 Redis-based rate limiting
        if ($config['redis_limiting']) {
            $key = $this->generateRedisKey($request);
            $allowed = $this->redisLimiter->slidingWindow(
                $key,
                $config['limit'],
                $config['window']
            );

            if (!$allowed) {
                return $this->buildRedisLimitResponse($key, $config);
            }
        }

        $response = $next($request);

        return $this->addComprehensiveHeaders($response, $request, $config);
    }

    /**
     * ⚙️ Parse middleware parameters
     */
    protected function parseParameters(array $parameters): array
    {
        $config = [
            'geo_limiting' => false,
            'multi_tier' => false,
            'redis_limiting' => true,
            'limit' => 60,
            'window' => 60,
        ];

        foreach ($parameters as $parameter) {
            if (str_contains($parameter, '=')) {
                [$key, $value] = explode('=', $parameter, 2);
                $config[$key] = is_numeric($value) ? (int) $value : $value === 'true';
            } else {
                $config[$parameter] = true;
            }
        }

        return $config;
    }

    /**
     * 🔍 Find blocked tier
     */
    protected function findBlockedTier(array $results): ?string
    {
        foreach ($results as $tier => $result) {
            if (!$result['allowed']) {
                return $tier;
            }
        }
        return null;
    }

    /**
     * 🔑 Generate Redis key
     */
    protected function generateRedisKey(Request $request): string
    {
        $user = $request->user();
        $identifier = $user ? "user:{$user->id}" : "ip:{$request->ip()}";
        $route = $request->route() ? $request->route()->getName() : 'unknown';
        
        return "comprehensive:{$identifier}:{$route}";
    }

    /**
     * 🌍 Build geographic limit response
     */
    protected function buildGeoLimitResponse()
    {
        return response()->json([
            'success' => false,
            'message' => '🌍 Geographic rate limit exceeded',
            'error' => 'Your region has exceeded the allowed request rate',
            'type' => 'geographic_limit'
        ], 429);
    }

    /**
     * 🏆 Build tier limit response
     */
    protected function buildTierLimitResponse(string $tier, array $tierData)
    {
        return response()->json([
            'success' => false,
            'message' => "🏆 {$tier} rate limit exceeded",
            'error' => 'You have exceeded your subscription tier limits',
            'type' => 'tier_limit',
            'tier' => $tier,
            'limit' => $tierData['limit'],
            'attempts' => $tierData['attempts'],
            'reset_in' => $tierData['reset_time']
        ], 429);
    }

    /**
     * 🔴 Build Redis limit response
     */
    protected function buildRedisLimitResponse(string $key, array $config)
    {
        $stats = $this->redisLimiter->getStats($key);
        
        return response()->json([
            'success' => false,
            'message' => '🔴 Rate limit exceeded',
            'error' => 'Too many requests in the current window',
            'type' => 'redis_limit',
            'limit' => $config['limit'],
            'current' => $stats['current_requests'] ?? 0,
            'window' => $config['window'],
            'retry_after' => $config['window']
        ], 429);
    }

    /**
     * 📊 Add comprehensive headers
     */
    protected function addComprehensiveHeaders($response, Request $request, array $config)
    {
        $headers = [
            'X-RateLimit-Type' => 'comprehensive',
            'X-RateLimit-Geo-Enabled' => $config['geo_limiting'] ? 'true' : 'false',
            'X-RateLimit-Tier-Enabled' => $config['multi_tier'] ? 'true' : 'false',
        ];

        if ($config['redis_limiting']) {
            $key = $this->generateRedisKey($request);
            $stats = $this->redisLimiter->getStats($key);
            
            $headers['X-RateLimit-Limit'] = $config['limit'];
            $headers['X-RateLimit-Remaining'] = max(0, $config['limit'] - ($stats['current_requests'] ?? 0));
            $headers['X-RateLimit-Reset'] = now()->addSeconds($config['window'])->timestamp;
        }

        return $response->withHeaders($headers);
    }
}
```

## 📊 Response Headers

### 📋 Standard Rate Limit Headers

```php
// app/Services/RateLimitHeaderService.php
<?php

namespace App\Services;

use Illuminate\Http\Response;

class RateLimitHeaderService
{
    /**
     * 📊 Add standard rate limit headers
     */
    public static function addHeaders(Response $response, array $data): Response
    {
        $headers = [
            // Standard headers
            'X-RateLimit-Limit' => $data['limit'] ?? 0,
            'X-RateLimit-Remaining' => $data['remaining'] ?? 0,
            'X-RateLimit-Reset' => $data['reset'] ?? now()->addMinute()->timestamp,
            
            // Additional helpful headers
            'X-RateLimit-Window' => $data['window'] ?? '60 seconds',
            'X-RateLimit-Policy' => $data['policy'] ?? 'sliding-window',
            'X-RateLimit-Identifier' => $data['identifier'] ?? 'ip',
            
            // Retry information
            'Retry-After' => $data['retry_after'] ?? 60,
            
            // Custom headers
            'X-RateLimit-Burst-Allowed' => $data['burst_allowed'] ?? 'false',
            'X-RateLimit-Tier' => $data['tier'] ?? 'default',
        ];

        // Add conditional headers
        if (isset($data['quota_remaining'])) {
            $headers['X-RateLimit-Quota-Remaining'] = $data['quota_remaining'];
        }

        if (isset($data['quota_reset'])) {
            $headers['X-RateLimit-Quota-Reset'] = $data['quota_reset'];
        }

        return $response->withHeaders($headers);
    }

    /**
     * 🚫 Add rate limit exceeded headers
     */
    public static function addExceededHeaders(Response $response, array $data): Response
    {
        $headers = [
            'X-RateLimit-Limit' => $data['limit'] ?? 0,
            'X-RateLimit-Remaining' => 0,
            'X-RateLimit-Reset' => $data['reset'] ?? now()->addMinute()->timestamp,
            'Retry-After' => $data['retry_after'] ?? 60,
            'X-RateLimit-Exceeded-At' => now()->toISOString(),
            'X-RateLimit-Exceeded-By' => $data['exceeded_by'] ?? 1,
        ];

        return $response->withHeaders($headers);
    }

    /**
     * 📈 Add usage analytics headers
     */
    public static function addAnalyticsHeaders(Response $response, array $analytics): Response
    {
        $headers = [
            'X-RateLimit-Usage-Percent' => $analytics['usage_percent'] ?? 0,
            'X-RateLimit-Peak-Usage' => $analytics['peak_usage'] ?? 0,
            'X-RateLimit-Average-Usage' => $analytics['average_usage'] ?? 0,
            'X-RateLimit-Requests-Today' => $analytics['requests_today'] ?? 0,
        ];

        return $response->withHeaders($headers);
    }
}
```

## 🧪 Testing Rate Limits

### 🔬 Rate Limit Test Suite

```php
// tests/Feature/RateLimitTest.php
<?php

namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;
use App\Models\User;
use App\Models\ApiKey;
use Illuminate\Support\Facades\Redis;

class RateLimitTest extends TestCase
{
    use RefreshDatabase;

    protected function setUp(): void
    {
        parent::setUp();
        Redis::flushall(); // Clear Redis before each test
    }

    public function test_basic_rate_limiting_blocks_after_limit()
    {
        $user = User::factory()->create();

        // Make requests up to the limit
        for ($i = 0; $i < 5; $i++) {
            $response = $this->actingAs($user)->postJson('/api/test-endpoint');
            $response->assertStatus(200);
        }

        // Next request should be blocked
        $response = $this->actingAs($user)->postJson('/api/test-endpoint');
        $response->assertStatus(429)
                ->assertJsonStructure([
                    'success',
                    'message',
                    'retry_after'
                ]);
    }

    public function test_rate_limit_headers_are_present()
    {
        $user = User::factory()->create();

        $response = $this->actingAs($user)->getJson('/api/test-endpoint');

        $response->assertStatus(200)
                ->assertHeader('X-RateLimit-Limit')
                ->assertHeader('X-RateLimit-Remaining')
                ->assertHeader('X-RateLimit-Reset');
    }

    public function test_api_key_rate_limiting()
    {
        $user = User::factory()->create();
        $apiKey = ApiKey::generate($user->id, 'Test Key', 'basic');

        // Make requests up to API key limit
        for ($i = 0; $i < $apiKey->rate_limit; $i++) {
            $response = $this->getJson('/api/test-endpoint', [
                'X-API-Key' => $apiKey->key
            ]);
            
            if ($i < $apiKey->rate_limit - 1) {
                $response->assertStatus(200);
            }
        }

        // Next request should be blocked
        $response = $this->getJson('/api/test-endpoint', [
            'X-API-Key' => $apiKey->key
        ]);
        
        $response->assertStatus(429);
    }

    public function test_different_users_have_separate_limits()
    {
        $user1 = User::factory()->create();
        $user2 = User::factory()->create();

        // Exhaust user1's limit
        for ($i = 0; $i < 5; $i++) {
            $this->actingAs($user1)->postJson('/api/test-endpoint');
        }

        // User1 should be blocked
        $response = $this->actingAs($user1)->postJson('/api/test-endpoint');
        $response->assertStatus(429);

        // User2 should still have access
        $response = $this->actingAs($user2)->postJson('/api/test-endpoint');
        $response->assertStatus(200);
    }

    public function test_rate_limit_resets_after_window()
    {
        $user = User::factory()->create();

        // Exhaust the limit
        for ($i = 0; $i < 5; $i++) {
            $this->actingAs($user)->postJson('/api/test-endpoint');
        }

        // Should be blocked
        $response = $this->actingAs($user)->postJson('/api/test-endpoint');
        $response->assertStatus(429);

        // Travel forward in time past the window
        $this->travel(61)->seconds();

        // Should be allowed again
        $response = $this->actingAs($user)->postJson('/api/test-endpoint');
        $response->assertStatus(200);
    }

    public function test_sliding_window_rate_limiting()
    {
        $user = User::factory()->create();

        // Make 3 requests at start
        for ($i = 0; $i < 3; $i++) {
            $this->actingAs($user)->postJson('/api/sliding-endpoint');
        }

        // Travel 30 seconds forward
        $this->travel(30)->seconds();

        // Make 2 more requests (total 5 in 60s window)
        for ($i = 0; $i < 2; $i++) {
            $response = $this->actingAs($user)->postJson('/api/sliding-endpoint');
            $response->assertStatus(200);
        }

        // Next request should be blocked (would be 6 in 60s)
        $response = $this->actingAs($user)->postJson('/api/sliding-endpoint');
        $response->assertStatus(429);
    }
}
```

### 🧪 Load Testing Rate Limits

```php
// tests/Feature/RateLimitLoadTest.php
<?php

namespace Tests\Feature;

use Tests\TestCase;
use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Support\Facades\Redis;

class RateLimitLoadTest extends TestCase
{
    use RefreshDatabase;

    public function test_concurrent_requests_respect_rate_limits()
    {
        $user = User::factory()->create();
        $responses = [];

        // Simulate concurrent requests
        $promises = [];
        for ($i = 0; $i < 10; $i++) {
            $promises[] = $this->actingAs($user)
                               ->postJson('/api/test-endpoint');
        }

        // Count successful vs blocked requests
        $successful = 0;
        $blocked = 0;

        foreach ($promises as $response) {
            if ($response->status() === 200) {
                $successful++;
            } elseif ($response->status() === 429) {
                $blocked++;
            }
        }

        // Should have exactly 5 successful (the limit) and 5 blocked
        $this->assertEquals(5, $successful);
        $this->assertEquals(5, $blocked);
    }

    public function test_burst_traffic_handling()
    {
        $users = User::factory(10)->create();
        $totalRequests = 0;
        $blockedRequests = 0;

        // Simulate burst traffic from multiple users
        foreach ($users as $user) {
            for ($i = 0; $i < 20; $i++) {
                $response = $this->actingAs($user)->postJson('/api/test-endpoint');
                $totalRequests++;
                
                if ($response->status() === 429) {
                    $blockedRequests++;
                }
            }
        }

        // Verify that blocking occurred appropriately
        $this->assertGreaterThan(0, $blockedRequests);
        $this->assertLessThan($totalRequests, $blockedRequests);
    }
}
```

## 📊 Monitoring & Analytics

### 📈 Rate Limit Analytics Service

```php
// app/Services/RateLimitAnalyticsService.php
<?php

namespace App\Services;

use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Redis;
use Carbon\Carbon;

class RateLimitAnalyticsService
{
    protected $redis;

    public function __construct()
    {
        $this->redis = Redis::connection();
    }

    /**
     * 📊 Record rate limit event
     */
    public function recordEvent(string $type, string $identifier, array $data = []): void
    {
        $event = [
            'timestamp' => now()->timestamp,
            'type' => $type, // 'allowed', 'blocked', 'reset'
            'identifier' => $identifier,
            'data' => json_encode($data),
        ];

        // Store in Redis for real-time analytics
        $key = "rate_limit_events:" . now()->format('Y-m-d-H');
        $this->redis->lpush($key, json_encode($event));
        $this->redis->expire($key, 86400 * 7); // Keep for 7 days

        // Also store in database for long-term analytics
        DB::table('rate_limit_events')->insert([
            'type' => $type,
            'identifier' => $identifier,
            'data' => json_encode($data),
            'created_at' => now(),
        ]);
    }

    /**
     * 📈 Get hourly statistics
     */
    public function getHourlyStats(Carbon $date): array
    {
        $stats = [];
        
        for ($hour = 0; $hour < 24; $hour++) {
            $key = "rate_limit_events:" . $date->format('Y-m-d') . "-{$hour}";
            $events = $this->redis->lrange($key, 0, -1);
            
            $hourStats = [
                'hour' => $hour,
                'total_requests' => 0,
                'allowed_requests' => 0,
                'blocked_requests' => 0,
                'unique_identifiers' => 0,
            ];

            $identifiers = [];

            foreach ($events as $eventJson) {
                $event = json_decode($eventJson, true);
                $hourStats['total_requests']++;
                
                if ($event['type'] === 'allowed') {
                    $hourStats['allowed_requests']++;
                } elseif ($event['type'] === 'blocked') {
                    $hourStats['blocked_requests']++;
                }

                $identifiers[] = $event['identifier'];
            }

            $hourStats['unique_identifiers'] = count(array_unique($identifiers));
            $stats[] = $hourStats;
        }

        return $stats;
    }

    /**
     * 🔍 Get top blocked identifiers
     */
    public function getTopBlockedIdentifiers(int $hours = 24): array
    {
        $endTime = now();
        $startTime = $endTime->copy()->subHours($hours);

        return DB::table('rate_limit_events')
            ->select('identifier', DB::raw('count(*) as blocked_count'))
            ->where('type', 'blocked')
            ->where('created_at', '>=', $startTime)
            ->where('created_at', '<=', $endTime)
            ->groupBy('identifier')
            ->orderByDesc('blocked_count')
            ->limit(20)
            ->get()
            ->toArray();
    }

    /**
     * 📊 Get rate limit effectiveness metrics
     */
    public function getEffectivenessMetrics(int $days = 7): array
    {
        $endTime = now();
        $startTime = $endTime->copy()->subDays($days);

        $metrics = DB::table('rate_limit_events')
            ->select(
                DB::raw('count(*) as total_events'),
                DB::raw('sum(case when type = "allowed" then 1 else 0 end) as allowed_events'),
                DB::raw('sum(case when type = "blocked" then 1 else 0 end) as blocked_events'),
                DB::raw('count(distinct identifier) as unique_identifiers')
            )
            ->where('created_at', '>=', $startTime)
            ->where('created_at', '<=', $endTime)
            ->first();

        $blockRate = $metrics->total_events > 0 
            ? ($metrics->blocked_events / $metrics->total_events) * 100 
            : 0;

        return [
            'total_events' => $metrics->total_events,
            'allowed_events' => $metrics->allowed_events,
            'blocked_events' => $metrics->blocked_events,
            'unique_identifiers' => $metrics->unique_identifiers,
            'block_rate_percentage' => round($blockRate, 2),
            'period_days' => $days,
        ];
    }

    /**
     * ⚠️ Detect suspicious activity
     */
    public function detectSuspiciousActivity(): array
    {
        $suspicious = [];

        // Find identifiers with unusually high block rates
        $highBlockRate = DB::table('rate_limit_events')
            ->select(
                'identifier',
                DB::raw('count(*) as total_events'),
                DB::raw('sum(case when type = "blocked" then 1 else 0 end) as blocked_events')
            )
            ->where('created_at', '>=', now()->subHours(24))
            ->groupBy('identifier')
            ->havingRaw('count(*) > 100') // More than 100 events
            ->havingRaw('(sum(case when type = "blocked" then 1 else 0 end) / count(*)) > 0.8') // >80% blocked
            ->get();

        foreach ($highBlockRate as $suspect) {
            $suspicious[] = [
                'identifier' => $suspect->identifier,
                'type' => 'high_block_rate',
                'total_events' => $suspect->total_events,
                'blocked_events' => $suspect->blocked_events,
                'block_rate' => ($suspect->blocked_events / $suspect->total_events) * 100,
            ];
        }

        return $suspicious;
    }

    /**
     * 🧹 Cleanup old analytics data
     */
    public function cleanup(int $daysToKeep = 30): int
    {
        $cutoffDate = now()->subDays($daysToKeep);
        
        return DB::table('rate_limit_events')
            ->where('created_at', '<', $cutoffDate)
            ->delete();
    }
}
```

### 📊 Rate Limit Dashboard Controller

```php
// app/Http/Controllers/Admin/RateLimitDashboardController.php
<?php

namespace App\Http\Controllers\Admin;

use App\Http\Controllers\Controller;
use App\Services\RateLimitAnalyticsService;
use Illuminate\Http\Request;

class RateLimitDashboardController extends Controller
{
    protected $analyticsService;

    public function __construct(RateLimitAnalyticsService $analyticsService)
    {
        $this->analyticsService = $analyticsService;
    }

    /**
     * 📊 Dashboard overview
     */
    public function index()
    {
        $metrics = $this->analyticsService->getEffectivenessMetrics(7);
        $hourlyStats = $this->analyticsService->getHourlyStats(now());
        $topBlocked = $this->analyticsService->getTopBlockedIdentifiers(24);
        $suspicious = $this->analyticsService->detectSuspiciousActivity();

        return response()->json([
            'success' => true,
            'data' => [
                'metrics' => $metrics,
                'hourly_stats' => $hourlyStats,
                'top_blocked' => $topBlocked,
                'suspicious_activity' => $suspicious,
            ]
        ]);
    }

    /**
     * 📈 Get detailed analytics
     */
    public function analytics(Request $request)
    {
        $request->validate([
            'days' => 'integer|min:1|max:90',
            'type' => 'in:hourly,daily,weekly',
        ]);

        $days = $request->input('days', 7);
        $type = $request->input('type', 'daily');

        $data = match ($type) {
            'hourly' => $this->getHourlyAnalytics($days),
            'daily' => $this->getDailyAnalytics($days),
            'weekly' => $this->getWeeklyAnalytics($days),
        };

        return response()->json([
            'success' => true,
            'data' => $data,
            'period' => $days,
            'type' => $type,
        ]);
    }

    /**
     * 🕐 Get hourly analytics
     */
    protected function getHourlyAnalytics(int $days): array
    {
        $analytics = [];
        
        for ($i = 0; $i < $days; $i++) {
            $date = now()->subDays($i);
            $analytics[] = [
                'date' => $date->format('Y-m-d'),
                'stats' => $this->analyticsService->getHourlyStats($date),
            ];
        }

        return array_reverse($analytics);
    }

    /**
     * 📅 Get daily analytics
     */
    protected function getDailyAnalytics(int $days): array
    {
        // Implementation for daily analytics aggregation
        return [];
    }

    /**
     * 📊 Get weekly analytics
     */
    protected function getWeeklyAnalytics(int $days): array
    {
        // Implementation for weekly analytics aggregation
        return [];
    }
}
```

## 🛠️ Best Practices

### 🔧 Rate Limiting Best Practices

#### 🎯 **Choose the Right Algorithm**
- **Fixed Window**: Simple, memory efficient, but allows bursts at window boundaries
- **Sliding Window**: More accurate, prevents bursts, higher memory usage
- **Token Bucket**: Allows controlled bursts, good for APIs with varying load
- **Leaky Bucket**: Smooth traffic flow, good for downstream protection

#### 📊 **Set Appropriate Limits**
```php
// ✅ Good: Tiered limits based on user type
$limits = match ($user->tier) {
    'premium' => ['requests' => 1000, 'window' => 60],
    'basic' => ['requests' => 100, 'window' => 60],
    default => ['requests' => 20, 'window' => 60],
};

// ❌ Bad: One-size-fits-all approach
$limit = 100; // Too restrictive for premium users, too lenient for free users
```

#### 🔑 **Use Appropriate Keys**
```php
// ✅ Good: Specific keys for different scenarios
$loginKey = $request->ip() . '|' . $request->input('email'); // Login attempts
$apiKey = $request->user()->id ?? $request->ip(); // API requests
$searchKey = $request->ip() . '|search'; // Search queries

// ❌ Bad: Generic keys that don't differentiate use cases
$genericKey = $request->ip(); // Too broad, affects unrelated actions
```

#### 📱 **Handle Different Client Types**
```php
// ✅ Good: Different limits for different clients
public function getClientLimits(Request $request): array
{
    $userAgent = $request->userAgent();
    
    if (str_contains($userAgent, 'Mobile')) {
        return ['limit' => 50, 'window' => 60]; // Lower for mobile
    }
    
    if (str_contains($userAgent, 'Bot')) {
        return ['limit' => 10, 'window' => 60]; // Very low for bots
    }
    
    return ['limit' => 100, 'window' => 60]; // Standard for web
}
```

#### ⚡ **Performance Considerations**
```php
// ✅ Good: Use Redis for high-performance scenarios
class HighPerformanceRateLimiter
{
    public function check(string $key, int $limit): bool
    {
        // Use Redis with Lua scripts for atomic operations
        return $this->redisLimiter->slidingWindow($key, $limit, 60);
    }
}

// ❌ Bad: Database queries on every request
class SlowRateLimiter
{
    public function check(string $key, int $limit): bool
    {
        // Database query on every request - too slow
        return DB::table('rate_limits')->where('key', $key)->count() < $limit;
    }
}
```

### 🛡️ Security Best Practices

#### 🔒 **Prevent Bypass Attempts**
```php
// ✅ Good: Multiple validation layers
public function validateRequest(Request $request): bool
{
    // Check IP-based limits
    if (!$this->checkIpLimit($request->ip())) {
        return false;
    }
    
    // Check user-based limits if authenticated
    if ($user = $request->user()) {
        if (!$this->checkUserLimit($user->id)) {
            return false;
        }
    }
    
    // Check endpoint-specific limits
    return $this->checkEndpointLimit($request->route()->getName());
}

// ❌ Bad: Single point of failure
public function validateRequest(Request $request): bool
{
    return $this->checkIpLimit($request->ip()); // Easy to bypass with proxy
}
```

#### 🕵️ **Monitor for Abuse Patterns**
```php
// ✅ Good: Detect and respond to abuse
public function detectAbuse(string $identifier): array
{
    $patterns = [
        'rapid_fire' => $this->detectRapidFireRequests($identifier),
        'distributed' => $this->detectDistributedAttack($identifier),
        'credential_stuffing' => $this->detectCredentialStuffing($identifier),
    ];
    
    return array_filter($patterns);
}

public function handleAbuse(string $identifier, array $patterns): void
{
    if (!empty($patterns)) {
        // Implement progressive penalties
        $this->implementPenalties($identifier, $patterns);
        $this->notifySecurityTeam($identifier, $patterns);
    }
}
```

## 🔧 Troubleshooting

### 🐛 Common Issues and Solutions

#### ⚡ **Performance Issues**

**Problem**: Rate limiting causing slow response times
```php
// ❌ Problematic: Synchronous cache calls
public function check(string $key): bool
{
    $current = Cache::get($key, 0); // Slow cache call
    if ($current >= $this->limit) {
        return false;
    }
    Cache::put($key, $current + 1, 60); // Another slow call
    return true;
}
```

**Solution**: Use atomic operations and connection pooling
```php
// ✅ Solution: Atomic Redis operations
public function check(string $key): bool
{
    $luaScript = "
        local current = redis.call('INCR', KEYS[1])
        if current == 1 then
            redis.call('EXPIRE', KEYS[1], ARGV[1])
        end
        return current <= tonumber(ARGV[2])
    ";
    
    return $this->redis->eval($luaScript, 1, $key, 60, $this->limit);
}
```

#### 🔄 **Race Conditions**

**Problem**: Concurrent requests bypassing limits
```php
// ❌ Problematic: Non-atomic check-then-act
public function attempt(string $key): bool
{
    $current = $this->cache->get($key, 0);
    
    if ($current >= $this->limit) {
        return false; // Race condition here!
    }
    
    $this->cache->put($key, $current + 1, 60);
    return true;
}
```

**Solution**: Use atomic operations
```php
// ✅ Solution: Atomic increment with limit check
public function attempt(string $key): bool
{
    $current = $this->cache->increment($key);
    
    if ($current === 1) {
        $this->cache->expire($key, 60);
    }
    
    return $current <= $this->limit;
}
```

#### 🗄️ **Memory Issues**

**Problem**: Rate limiting data consuming too much memory
```php
// ❌ Problematic: Storing full request data
public function trackRequest(Request $request): void
{
    $this->cache->put(
        "request:{$request->ip()}:" . time(),
        $request->all(), // Storing entire request data
        3600
    );
}
```

**Solution**: Store only necessary data and implement cleanup
```php
// ✅ Solution: Minimal data storage with cleanup
public function trackRequest(Request $request): void
{
    $key = "requests:{$request->ip()}";
    $timestamp = time();
    
    // Store only timestamp
    $this->cache->zadd($key, $timestamp, $timestamp);
    
    // Remove old entries
    $cutoff = $timestamp - 3600;
    $this->cache->zremrangebyscore($key, 0, $cutoff);
}
```

### 🔍 Debugging Tools

#### 📊 Rate Limit Debug Middleware

```php
// app/Http/Middleware/RateLimitDebug.php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Log;

class RateLimitDebug
{
    public function handle(Request $request, Closure $next)
    {
        $startTime = microtime(true);
        $memoryBefore = memory_get_usage(true);
        
        $response = $next($request);
        
        $endTime = microtime(true);
        $memoryAfter = memory_get_usage(true);
        
        // Log performance metrics
        Log::debug('Rate Limit Performance', [
            'route' => $request->route()?->getName(),
            'ip' => $request->ip(),
            'user_id' => $request->user()?->id,
            'execution_time_ms' => round(($endTime - $startTime) * 1000, 2),
            'memory_used_kb' => round(($memoryAfter - $memoryBefore) / 1024, 2),
            'rate_limit_headers' => [
                'limit' => $response->headers->get('X-RateLimit-Limit'),
                'remaining' => $response->headers->get('X-RateLimit-Remaining'),
                'reset' => $response->headers->get('X-RateLimit-Reset'),
            ]
        ]);
        
        return $response;
    }
}
```

#### 🔧 Rate Limit Testing Command

```php
// app/Console/Commands/TestRateLimit.php
<?php

namespace App\Console\Commands;

use Illuminate\Console\Command;
use App\Services\RedisRateLimiter;

class TestRateLimit extends Command
{
    protected $signature = 'rate-limit:test {key} {--limit=10} {--window=60} {--requests=20}';
    protected $description = 'Test rate limiting configuration';

    public function handle(RedisRateLimiter $rateLimiter)
    {
        $key = $this->argument('key');
        $limit = (int) $this->option('limit');
        $window = (int) $this->option('window');
        $requests = (int) $this->option('requests');

        $this->info("Testing rate limit: {$limit} requests per {$window} seconds");
        $this->info("Making {$requests} test requests...");

        $allowed = 0;
        $blocked = 0;

        for ($i = 1; $i <= $requests; $i++) {
            $result = $rateLimiter->slidingWindow($key, $limit, $window);
            
            if ($result) {
                $allowed++;
                $this->line("Request {$i}: ✅ ALLOWED");
            } else {
                $blocked++;
                $this->line("Request {$i}: ❌ BLOCKED");
            }
            
            // Small delay to simulate real requests
            usleep(100000); // 0.1 seconds
        }

        $this->info("\nResults:");
        $this->table(
            ['Metric', 'Value'],
            [
                ['Total Requests', $requests],
                ['Allowed', $allowed],
                ['Blocked', $blocked],
                ['Success Rate', round(($allowed / $requests) * 100, 2) . '%'],
            ]
        );
    }
}
```

## 📋 Configuration Examples

### ⚙️ Environment Configuration

```env
# .env - Rate Limiting Configuration

# Default rate limiting
RATE_LIMIT_DRIVER=redis
RATE_LIMIT_CONNECTION=default

# API rate limits
API_RATE_LIMIT_PER_MINUTE=60
API_RATE_LIMIT_BURST=10

# Login rate limits
LOGIN_RATE_LIMIT_PER_MINUTE=5
LOGIN_RATE_LIMIT_PER_HOUR=20

# Email rate limits
EMAIL_RATE_LIMIT_PER_MINUTE=10
EMAIL_RATE_LIMIT_PER_HOUR=100

# Geographic rate limiting
GEO_RATE_LIMITING_ENABLED=true
HIGH_RISK_COUNTRIES=CN,RU,IR,KP
TRUSTED_COUNTRIES=US,CA,GB,DE,FR,JP,AU

# Monitoring
RATE_LIMIT_ANALYTICS_ENABLED=true
RATE_LIMIT_CLEANUP_DAYS=30
```

### 🎯 Service Provider Configuration

```php
// app/Providers/RateLimitServiceProvider.php
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use App\Services\RateLimiterService;
use App\Services\MultiTierRateLimiter;
use App\Services\GeographicRateLimiter;
use App\Services\RedisRateLimiter;

class RateLimitServiceProvider extends ServiceProvider
{
    public function register()
    {
        $this->app->singleton(RateLimiterService::class, function ($app) {
            return new RateLimiterService();
        });

        $this->app->singleton(RedisRateLimiter::class, function ($app) {
            return new RedisRateLimiter();
        });

        $this->app->singleton(MultiTierRateLimiter::class, function ($app) {
            return new MultiTierRateLimiter(
                $app->make(RateLimiterService::class)
            );
        });

        $this->app->singleton(GeographicRateLimiter::class, function ($app) {
            return new GeographicRateLimiter(
                $app->make(RateLimiterService::class)
            );
        });
    }

    public function boot()
    {
        $this->publishes([
            __DIR__.'/../../config/rate_limiting.php' => config_path('rate_limiting.php'),
        ], 'rate-limiting-config');

        $this->loadMigrationsFrom(__DIR__.'/../../database/migrations');
    }
}
```

### 📁 Complete Configuration File

```php
// config/rate_limiting.php
<?php

return [
    /*
    |--------------------------------------------------------------------------
    | Default Rate Limiting Driver
    |--------------------------------------------------------------------------
    */
    'default' => env('RATE_LIMIT_DRIVER', 'redis'),

    /*
    |--------------------------------------------------------------------------
    | Rate Limiting Stores
    |--------------------------------------------------------------------------
    */
    'stores' => [
        'redis' => [
            'driver' => 'redis',
            'connection' => env('RATE_LIMIT_CONNECTION', 'default'),
        ],
        
        'database' => [
            'driver' => 'database',
            'table' => 'rate_limits',
        ],
        
        'memory' => [
            'driver' => 'array',
        ],
    ],

    /*
    |--------------------------------------------------------------------------
    | Rate Limiting Configurations
    |--------------------------------------------------------------------------
    */
    'limits' => [
        'api' => [
            'requests' => env('API_RATE_LIMIT_PER_MINUTE', 60),
            'window' => 60, // seconds
            'burst' => env('API_RATE_LIMIT_BURST', 10),
        ],
        
        'login' => [
            'requests' => env('LOGIN_RATE_LIMIT_PER_MINUTE', 5),
            'window' => 60,
            'lockout_duration' => 900, // 15 minutes
        ],
        
        'email' => [
            'requests' => env('EMAIL_RATE_LIMIT_PER_MINUTE', 10),
            'window' => 60,
            'daily_limit' => 1000,
        ],
        
        'search' => [
            'requests' => 30,
            'window' => 60,
        ],
    ],

    /*
    |--------------------------------------------------------------------------
    | User Tier Configurations
    |--------------------------------------------------------------------------
    */
    'tiers' => [
        'free' => [
            'requests_per_minute' => 20,
            'requests_per_hour' => 500,
            'requests_per_day' => 2000,
        ],
        
        'basic' => [
            'requests_per_minute' => 100,
            'requests_per_hour' => 2000,
            'requests_per_day' => 10000,
        ],
        
        'premium' => [
            'requests_per_minute' => 500,
            'requests_per_hour' => 5000,
            'requests_per_day' => 50000,
        ],
        
        'enterprise' => [
            'requests_per_minute' => 1000,
            'requests_per_hour' => 10000,
            'requests_per_day' => 100000,
        ],
    ],

    /*
    |--------------------------------------------------------------------------
    | Geographic Rate Limiting
    |--------------------------------------------------------------------------
    */
    'geographic' => [
        'enabled' => env('GEO_RATE_LIMITING_ENABLED', false),
        
        'high_risk_countries' => explode(',', env('HIGH_RISK_COUNTRIES', 'CN,RU,IR,KP')),
        'trusted_countries' => explode(',', env('TRUSTED_COUNTRIES', 'US,CA,GB,DE,FR,JP,AU')),
        
        'limits' => [
            'high_risk' => ['requests' => 10, 'window' => 300], // 5 minutes
            'default' => ['requests' => 50, 'window' => 60],
            'trusted' => ['requests' => 200, 'window' => 60],
        ],
    ],

    /*
    |--------------------------------------------------------------------------
    | Monitoring and Analytics
    |--------------------------------------------------------------------------
    */
    'analytics' => [
        'enabled' => env('RATE_LIMIT_ANALYTICS_ENABLED', true),
        'cleanup_days' => env('RATE_LIMIT_CLEANUP_DAYS', 30),
        'real_time_window' => 3600, // 1 hour in seconds
    ],

    /*
    |--------------------------------------------------------------------------
    | Response Configuration
    |--------------------------------------------------------------------------
    */
    'responses' => [
        'headers' => [
            'add_standard_headers' => true,
            'add_custom_headers' => true,
            'include_retry_after' => true,
        ],
        
        'messages' => [
            'default' => 'Rate limit exceeded. Please try again later.',
            'login' => 'Too many login attempts. Please try again in :minutes minutes.',
            'api' => 'API rate limit exceeded. Upgrade your plan for higher limits.',
        ],
    ],
];
```

## 🚀 Advanced Patterns

### 🎯 Adaptive Rate Limiting

```php
// app/Services/AdaptiveRateLimiter.php
<?php

namespace App\Services;

class AdaptiveRateLimiter
{
    /**
     * 🧠 Adjust limits based on system load
     */
    public function getAdaptiveLimit(string $identifier): int
    {
        $systemLoad = $this->getSystemLoad();
        $userHistory = $this->getUserHistory($identifier);
        
        $baseLimit = 100;
        
        // Reduce limits during high system load
        if ($systemLoad > 0.8) {
            $baseLimit *= 0.5;
        } elseif ($systemLoad > 0.6) {
            $baseLimit *= 0.7;
        }
        
        // Increase limits for well-behaved users
        if ($userHistory['abuse_score'] < 0.1) {
            $baseLimit *= 1.2;
        }
        
        return (int) $baseLimit;
    }

    private function getSystemLoad(): float
    {
        // Implementation to get system metrics
        return 0.5; // Placeholder
    }

    private function getUserHistory(string $identifier): array
    {
        // Implementation to get user behavior history
        return ['abuse_score' => 0.05]; // Placeholder
    }
}
```

This completes the comprehensive Laravel Rate Limiting and Throttling guide. The document now includes all the remaining sections covering monitoring, analytics, best practices, troubleshooting, configuration examples, and advanced patterns.