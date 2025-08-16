# 📦 Laravel Service Container & Service Providers Guide

## 📋 Table of Contents
- [What is Service Container?](#-what-is-service-container)
- [What are Service Providers?](#-what-are-service-providers)
- [Service Container Deep Dive](#-service-container-deep-dive)
- [Service Providers Deep Dive](#-service-providers-deep-dive)
- [Binding Types](#-binding-types)
- [Dependency Injection](#-dependency-injection)
- [Real-World Examples](#-real-world-examples)
- [Best Practices](#-best-practices)

---

## 🔍 What is Service Container?

The **Service Container** (also called IoC Container - Inversion of Control) is Laravel's powerful dependency injection container. It's the heart of Laravel's architecture that manages class dependencies and performs dependency injection.

### 🎯 Key Concepts:

- **🏭 Factory Pattern**: Creates and manages object instances
- **💉 Dependency Injection**: Automatically resolves class dependencies
- **🔄 Inversion of Control**: Framework controls object creation, not your code
- **📝 Service Registry**: Central registry for all application services

### 🌟 Why Use Service Container?

```php
// ❌ Without Service Container (Hard Dependencies)
class OrderController 
{
    public function store()
    {
        $emailService = new EmailService(); // Hard dependency
        $paymentGateway = new StripePaymentGateway(); // Hard dependency
        $logger = new FileLogger(); // Hard dependency
        
        // Business logic...
    }
}

// ✅ With Service Container (Dependency Injection)
class OrderController 
{
    public function __construct(
        EmailServiceInterface $emailService,
        PaymentGatewayInterface $paymentGateway,
        LoggerInterface $logger
    ) {
        // Dependencies injected automatically by container
    }
}
```

---

## 🏗️ What are Service Providers?

**Service Providers** are the central place where you register services into the Service Container. They tell Laravel how to build and configure various components of your application.

### 🎯 Key Responsibilities:

- **📋 Registration**: Register services in the container
- **⚙️ Configuration**: Configure how services should be built
- **🚀 Bootstrapping**: Initialize application components
- **🔗 Binding**: Bind interfaces to concrete implementations

### 📁 Default Service Providers:
```
config/app.php -> 'providers' array

- App\Providers\AppServiceProvider
- App\Providers\AuthServiceProvider  
- App\Providers\EventServiceProvider
- App\Providers\RouteServiceProvider
```

---

## 🏭 Service Container Deep Dive

### 🔧 Basic Container Operations

```php
// Getting the container instance
$container = app();
// or
$container = resolve();
// or
$container = \Illuminate\Container\Container::getInstance();
```

### 📝 Binding Services

#### 1. **🎯 Simple Binding**
```php
// Binding a concrete class
app()->bind('payment', PaymentService::class);

// Resolving
$payment = app('payment');
// or
$payment = resolve('payment');
```

#### 2. **🔒 Singleton Binding**
```php
// Single instance throughout application lifecycle
app()->singleton('logger', function ($app) {
    return new Logger(config('app.log_level'));
});

// Every resolve() call returns the same instance
$logger1 = resolve('logger');
$logger2 = resolve('logger');
// $logger1 === $logger2 (same instance)
```

#### 3. **🎭 Interface Binding**
```php
// Bind interface to implementation
app()->bind(
    PaymentGatewayInterface::class,
    StripePaymentGateway::class
);

// Laravel will inject StripePaymentGateway when PaymentGatewayInterface is requested
```

#### 4. **🏃 Instance Binding**
```php
// Bind existing instance
$apiClient = new ApiClient('https://api.example.com');
app()->instance('api.client', $apiClient);
```

#### 5. **🏷️ Contextual Binding**
```php
// Different implementations for different contexts
app()->when(OrderController::class)
    ->needs(PaymentGatewayInterface::class)
    ->give(StripePaymentGateway::class);

app()->when(SubscriptionController::class)
    ->needs(PaymentGatewayInterface::class)
    ->give(PayPalPaymentGateway::class);
```

### 🔍 Resolving Dependencies

#### 1. **🎯 Direct Resolution**
```php
$service = app()->make(PaymentService::class);
$service = resolve(PaymentService::class);
$service = app(PaymentService::class);
```

#### 2. **💉 Automatic Injection**
```php
class OrderController extends Controller
{
    // Laravel automatically resolves and injects dependencies
    public function __construct(
        PaymentService $paymentService,
        EmailService $emailService
    ) {
        $this->paymentService = $paymentService;
        $this->emailService = $emailService;
    }
}
```

#### 3. **🔧 Method Injection**
```php
class OrderController extends Controller
{
    // Dependencies can be injected into methods too
    public function store(Request $request, PaymentService $paymentService)
    {
        // $paymentService is automatically resolved
    }
}
```

---

## 🏗️ Service Providers Deep Dive

### 🎯 Creating a Service Provider

```bash
# Generate a new service provider
php artisan make:provider PaymentServiceProvider
```

### 📝 Service Provider Structure

```php
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;

class PaymentServiceProvider extends ServiceProvider
{
    /**
     * 📋 Register services into the container
     */
    public function register()
    {
        // This method is called first
        // Register bindings here
    }

    /**
     * 🚀 Bootstrap services after all providers are registered
     */
    public function boot()
    {
        // This method is called after all register() methods
        // Initialize, configure, set up event listeners, etc.
    }
}
```

### 🔄 Provider Lifecycle

1. **📋 Register Phase**: All `register()` methods are called
2. **🚀 Boot Phase**: All `boot()` methods are called (after registration)

### 💡 Complete Service Provider Example

```php
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use App\Services\PaymentService;
use App\Services\EmailService;
use App\Contracts\PaymentGatewayInterface;
use App\Services\StripePaymentGateway;
use App\Services\PayPalPaymentGateway;

class PaymentServiceProvider extends ServiceProvider
{
    /**
     * 📋 Register services
     */
    public function register()
    {
        // 🎯 Simple binding
        $this->app->bind(PaymentService::class, function ($app) {
            return new PaymentService(
                $app->make(PaymentGatewayInterface::class),
                $app->make(EmailService::class)
            );
        });

        // 🔒 Singleton binding
        $this->app->singleton(EmailService::class, function ($app) {
            return new EmailService(
                config('mail.driver'),
                config('mail.from.address')
            );
        });

        // 🎭 Interface binding based on config
        $this->app->bind(PaymentGatewayInterface::class, function ($app) {
            $gateway = config('payment.default_gateway');
            
            switch ($gateway) {
                case 'stripe':
                    return new StripePaymentGateway(config('services.stripe.secret'));
                case 'paypal':
                    return new PayPalPaymentGateway(config('services.paypal.secret'));
                default:
                    throw new \Exception("Unsupported payment gateway: {$gateway}");
            }
        });

        // 🏷️ Contextual binding
        $this->app->when(SubscriptionController::class)
            ->needs(PaymentGatewayInterface::class)
            ->give(function ($app) {
                return new StripePaymentGateway(config('services.stripe.secret'));
            });
    }

    /**
     * 🚀 Bootstrap services
     */
    public function boot()
    {
        // 📁 Publish configuration files
        $this->publishes([
            __DIR__.'/../config/payment.php' => config_path('payment.php'),
        ], 'payment-config');

        // 📊 Set up event listeners
        Event::listen(PaymentProcessed::class, SendPaymentNotification::class);

        // 🔧 Extend existing services
        $this->app->extend('view', function ($view, $app) {
            $view->share('paymentGateways', config('payment.available_gateways'));
            return $view;
        });

        // 🎯 Conditional service registration
        if ($this->app->environment('production')) {
            $this->app->singleton(PaymentLogger::class, function ($app) {
                return new PaymentLogger(storage_path('logs/payments.log'));
            });
        }
    }

    /**
     * 📦 Get the services provided by the provider
     */
    public function provides()
    {
        return [
            PaymentService::class,
            PaymentGatewayInterface::class,
            EmailService::class,
        ];
    }
}
```

### 📋 Registering Service Provider

```php
// config/app.php
'providers' => [
    // Other providers...
    App\Providers\PaymentServiceProvider::class,
],
```

---

## 🔗 Binding Types

### 1. **🎯 Basic Binding**
```php
// Closure binding
app()->bind('email', function ($app) {
    return new EmailService(config('mail.driver'));
});

// Class binding
app()->bind(EmailServiceInterface::class, EmailService::class);
```

### 2. **🔒 Singleton Binding**
```php
// Single instance
app()->singleton('database', function ($app) {
    return new DatabaseConnection(config('database.default'));
});

// Singleton with interface
app()->singleton(CacheInterface::class, RedisCache::class);
```

### 3. **🏃 Instance Binding**
```php
$logger = new Logger('application');
app()->instance('logger', $logger);
```

### 4. **🏷️ Tagged Binding**
```php
// Tag multiple services
app()->bind('notification.email', EmailNotification::class);
app()->bind('notification.sms', SmsNotification::class);

app()->tag(['notification.email', 'notification.sms'], 'notifications');

// Resolve all tagged services
$notifications = app()->tagged('notifications');
```

### 5. **🔄 Extending Bindings**
```php
// Extend existing binding
app()->extend('payment', function ($payment, $app) {
    return new PaymentDecorator($payment);
});
```

---

## 💉 Dependency Injection

### 🎯 Constructor Injection

```php
class OrderService
{
    public function __construct(
        PaymentGatewayInterface $paymentGateway,
        EmailServiceInterface $emailService,
        LoggerInterface $logger
    ) {
        $this->paymentGateway = $paymentGateway;
        $this->emailService = $emailService;
        $this->logger = $logger;
    }

    public function processOrder(Order $order)
    {
        try {
            $result = $this->paymentGateway->charge($order->total);
            $this->emailService->sendConfirmation($order);
            $this->logger->info("Order {$order->id} processed successfully");
        } catch (Exception $e) {
            $this->logger->error("Order processing failed: " . $e->getMessage());
            throw $e;
        }
    }
}
```

### 🔧 Method Injection

```php
class OrderController extends Controller
{
    public function store(
        Request $request,
        OrderService $orderService,
        OrderValidator $validator
    ) {
        $validator->validate($request->all());
        $order = $orderService->createOrder($request->all());
        return response()->json($order);
    }
}
```

### 🏷️ Parameter Resolution

```php
// When Laravel can't resolve a parameter, you can provide default values
app()->bind(ApiClient::class, function ($app) {
    return new ApiClient(
        baseUrl: config('api.base_url'),
        timeout: config('api.timeout', 30),
        retries: config('api.retries', 3)
    );
});
```

---

## 🌍 Real-World Examples

### 📧 Email Service Example

```php
// 1. Define Interface
interface EmailServiceInterface
{
    public function send(string $to, string $subject, string $body): bool;
}

// 2. Create Implementations
class SmtpEmailService implements EmailServiceInterface
{
    public function __construct(private string $host, private int $port) {}
    
    public function send(string $to, string $subject, string $body): bool
    {
        // SMTP implementation
        return true;
    }
}

class MailgunEmailService implements EmailServiceInterface
{
    public function __construct(private string $apiKey, private string $domain) {}
    
    public function send(string $to, string $subject, string $body): bool
    {
        // Mailgun API implementation
        return true;
    }
}

// 3. Service Provider
class EmailServiceProvider extends ServiceProvider
{
    public function register()
    {
        $this->app->bind(EmailServiceInterface::class, function ($app) {
            $driver = config('mail.driver');
            
            return match($driver) {
                'smtp' => new SmtpEmailService(
                    config('mail.host'),
                    config('mail.port')
                ),
                'mailgun' => new MailgunEmailService(
                    config('services.mailgun.secret'),
                    config('services.mailgun.domain')
                ),
                default => throw new InvalidArgumentException("Unsupported mail driver: {$driver}")
            };
        });
    }
}

// 4. Usage in Controller
class UserController extends Controller
{
    public function sendWelcomeEmail(User $user, EmailServiceInterface $emailService)
    {
        $emailService->send(
            $user->email,
            'Welcome!',
            "Welcome to our platform, {$user->name}!"
        );
    }
}
```

### 💳 Payment Gateway Example

```php
// 1. Payment Gateway Service Provider
class PaymentServiceProvider extends ServiceProvider
{
    public function register()
    {
        // Register different gateways
        $this->app->bind('payment.stripe', function ($app) {
            return new StripePaymentGateway(config('services.stripe.secret'));
        });

        $this->app->bind('payment.paypal', function ($app) {
            return new PayPalPaymentGateway(
                config('services.paypal.client_id'),
                config('services.paypal.client_secret')
            );
        });

        // Payment factory
        $this->app->bind(PaymentFactory::class, function ($app) {
            return new PaymentFactory([
                'stripe' => $app->make('payment.stripe'),
                'paypal' => $app->make('payment.paypal'),
            ]);
        });

        // Main payment service
        $this->app->singleton(PaymentService::class, function ($app) {
            return new PaymentService(
                $app->make(PaymentFactory::class),
                $app->make(PaymentLogger::class)
            );
        });
    }

    public function boot()
    {
        // Boot configuration
        $this->mergeConfigFrom(__DIR__.'/../config/payment.php', 'payment');
    }
}

// 2. Usage
class CheckoutController extends Controller
{
    public function process(
        CheckoutRequest $request,
        PaymentService $paymentService
    ) {
        $result = $paymentService->charge(
            gateway: $request->payment_method,
            amount: $request->amount,
            currency: $request->currency
        );

        return response()->json($result);
    }
}
```

### 🗄️ Repository Pattern Example

```php
// 1. Repository Interface
interface UserRepositoryInterface
{
    public function find(int $id): ?User;
    public function create(array $data): User;
    public function update(int $id, array $data): bool;
}

// 2. Implementation
class EloquentUserRepository implements UserRepositoryInterface
{
    public function find(int $id): ?User
    {
        return User::find($id);
    }

    public function create(array $data): User
    {
        return User::create($data);
    }

    public function update(int $id, array $data): bool
    {
        return User::where('id', $id)->update($data);
    }
}

// 3. Service Provider
class RepositoryServiceProvider extends ServiceProvider
{
    public function register()
    {
        $this->app->bind(
            UserRepositoryInterface::class,
            EloquentUserRepository::class
        );
    }
}

// 4. Usage in Service
class UserService
{
    public function __construct(
        private UserRepositoryInterface $userRepository,
        private EmailServiceInterface $emailService
    ) {}

    public function createUser(array $userData): User
    {
        $user = $this->userRepository->create($userData);
        $this->emailService->send(
            $user->email,
            'Welcome!',
            'Welcome to our platform!'
        );
        return $user;
    }
}
```

---

## ✨ Best Practices

### 🎯 Service Container Best Practices

1. **🎭 Use Interfaces**: Always bind interfaces, not concrete classes
```php
// ✅ Good
app()->bind(PaymentGatewayInterface::class, StripePaymentGateway::class);

// ❌ Avoid
app()->bind(StripePaymentGateway::class, StripePaymentGateway::class);
```

2. **🔒 Use Singletons Wisely**: Only for stateless services
```php
// ✅ Good - Stateless service
app()->singleton(ConfigService::class);

// ❌ Avoid - Stateful service
app()->singleton(ShoppingCart::class); // Should be per-user
```

3. **🏷️ Use Contextual Binding**: For different implementations per context
```php
app()->when(OrderController::class)
    ->needs(PaymentGatewayInterface::class)
    ->give(StripePaymentGateway::class);
```

### 🏗️ Service Provider Best Practices

1. **📋 Keep Register() Pure**: Only bindings in register()
```php
public function register()
{
    // ✅ Only bindings
    $this->app->bind(Service::class, Implementation::class);
    
    // ❌ No side effects
    // Event::listen(...); // This belongs in boot()
}
```

2. **🚀 Use Boot() for Side Effects**: Configuration, events, etc.
```php
public function boot()
{
    // ✅ Side effects here
    Event::listen(UserRegistered::class, SendWelcomeEmail::class);
    View::composer('layouts.app', AppComposer::class);
}
```

3. **🎯 Deferred Providers**: For performance
```php
class ExpensiveServiceProvider extends ServiceProvider
{
    protected $defer = true; // Only load when needed

    public function provides()
    {
        return [ExpensiveService::class];
    }
}
```

### 🔧 Dependency Injection Best Practices

1. **🎯 Constructor Injection**: For required dependencies
```php
class OrderService
{
    public function __construct(
        private PaymentGatewayInterface $paymentGateway // Required
    ) {}
}
```

2. **🔧 Method Injection**: For optional or context-specific dependencies
```php
public function processOrder(Order $order, ?Logger $logger = null)
{
    // Optional dependency
}
```

3. **🏷️ Type Hint Everything**: For automatic resolution
```php
// ✅ Good - Laravel can resolve automatically
public function handle(PaymentService $paymentService) {}

// ❌ Avoid - Manual resolution needed
public function handle() {
    $paymentService = app(PaymentService::class);
}
```

### 📊 Performance Tips

1. **🔒 Use Singletons for Heavy Objects**
```php
app()->singleton(DatabaseConnection::class, function ($app) {
    return new DatabaseConnection(config('database.default'));
});
```

2. **⏰ Defer Heavy Providers**
```php
class HeavyServiceProvider extends ServiceProvider
{
    protected $defer = true;
    
    public function provides()
    {
        return [HeavyService::class];
    }
}
```

3. **🎯 Use Contextual Binding to Avoid Conditionals**
```php
// ✅ Better performance
app()->when(ApiController::class)
    ->needs(CacheInterface::class)
    ->give(RedisCache::class);

app()->when(WebController::class)
    ->needs(CacheInterface::class)
    ->give(FileCache::class);

// ❌ Slower - conditional in binding
app()->bind(CacheInterface::class, function ($app) {
    if (app()->runningInConsole()) {
        return new FileCache();
    }
    return new RedisCache();
});
```

---

## 🎓 Summary

### 📦 Service Container
- **Central dependency manager** for your Laravel application
- **Automatic dependency injection** eliminates manual object creation
- **Flexible binding system** supports various registration patterns
- **Improves testability** through dependency injection

### 🏗️ Service Providers
- **Bootstrap application services** and configure the container
- **Central registration point** for all application bindings
- **Two-phase lifecycle**: register() then boot()
- **Organize related services** in logical groups

### 🚀 Benefits
- **🔧 Flexible Architecture**: Easy to swap implementations
- **🧪 Better Testing**: Mock dependencies easily
- **📈 Scalable Code**: Loose coupling between components
- **⚡ Performance**: Lazy loading and singleton patterns
- **🎯 Clean Code**: No hardcoded dependencies

The Service Container and Service Providers are fundamental to Laravel's architecture, enabling clean, testable, and maintainable applications through proper dependency management!