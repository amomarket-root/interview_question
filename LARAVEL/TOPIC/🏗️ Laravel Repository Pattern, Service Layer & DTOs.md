# 🏗️ Laravel Repository Pattern, Service Layer & DTOs Guide

## 📋 Table of Contents
- [Repository Pattern](#-repository-pattern)
- [Service Layer](#-service-layer)
- [DTOs (Data Transfer Objects)](#-dtos-data-transfer-objects)
- [Complete Implementation Example](#-complete-implementation-example)
- [Best Practices](#-best-practices)
- [Testing Strategies](#-testing-strategies)

---

## 🗄️ Repository Pattern

### 📖 What is Repository Pattern?

The **Repository Pattern** is a design pattern that encapsulates the logic needed to access data sources. It provides a uniform interface for accessing domain objects, abstracting the underlying data storage mechanism.

### 🎯 Why Use Repository Pattern?

- **🔄 Data Access Abstraction**: Hide database implementation details
- **🧪 Better Testing**: Easy to mock for unit tests
- **🔄 Flexibility**: Switch between different data sources
- **📦 Single Responsibility**: Separate data access from business logic
- **🎯 Cleaner Controllers**: Keep controllers thin and focused

### ❌ Without Repository Pattern

```php
// ❌ Fat Controller with direct Eloquent usage
class UserController extends Controller
{
    public function index()
    {
        // Database logic mixed with controller logic
        $users = User::with('posts')
            ->where('status', 'active')
            ->where('created_at', '>=', now()->subDays(30))
            ->orderBy('name')
            ->paginate(15);
            
        return view('users.index', compact('users'));
    }
    
    public function store(Request $request)
    {
        // Direct model manipulation
        $user = User::create([
            'name' => $request->name,
            'email' => $request->email,
            'password' => Hash::make($request->password),
            'status' => 'active'
        ]);
        
        // Difficult to test, hard to change data source
        return response()->json($user);
    }
}
```

### ✅ With Repository Pattern

#### 1. **📝 Repository Interface**

```php
<?php

namespace App\Repositories\Contracts;

use App\Models\User;
use Illuminate\Contracts\Pagination\LengthAwarePaginator;
use Illuminate\Database\Eloquent\Collection;

interface UserRepositoryInterface
{
    public function all(): Collection;
    public function find(int $id): ?User;
    public function findByEmail(string $email): ?User;
    public function create(array $data): User;
    public function update(int $id, array $data): bool;
    public function delete(int $id): bool;
    public function getActiveUsers(): Collection;
    public function getRecentUsers(int $days = 30): Collection;
    public function paginate(int $perPage = 15): LengthAwarePaginator;
    public function getUsersWithPosts(): Collection;
}
```

#### 2. **🏗️ Repository Implementation**

```php
<?php

namespace App\Repositories;

use App\Models\User;
use App\Repositories\Contracts\UserRepositoryInterface;
use Illuminate\Contracts\Pagination\LengthAwarePaginator;
use Illuminate\Database\Eloquent\Collection;

class UserRepository implements UserRepositoryInterface
{
    public function __construct(private User $model) {}

    public function all(): Collection
    {
        return $this->model->all();
    }

    public function find(int $id): ?User
    {
        return $this->model->find($id);
    }

    public function findByEmail(string $email): ?User
    {
        return $this->model->where('email', $email)->first();
    }

    public function create(array $data): User
    {
        return $this->model->create($data);
    }

    public function update(int $id, array $data): bool
    {
        return $this->model->where('id', $id)->update($data);
    }

    public function delete(int $id): bool
    {
        return $this->model->where('id', $id)->delete();
    }

    public function getActiveUsers(): Collection
    {
        return $this->model
            ->where('status', 'active')
            ->orderBy('name')
            ->get();
    }

    public function getRecentUsers(int $days = 30): Collection
    {
        return $this->model
            ->where('created_at', '>=', now()->subDays($days))
            ->orderBy('created_at', 'desc')
            ->get();
    }

    public function paginate(int $perPage = 15): LengthAwarePaginator
    {
        return $this->model
            ->where('status', 'active')
            ->orderBy('name')
            ->paginate($perPage);
    }

    public function getUsersWithPosts(): Collection
    {
        return $this->model
            ->with('posts')
            ->where('status', 'active')
            ->get();
    }
}
```

#### 3. **📦 Service Provider Registration**

```php
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use App\Repositories\Contracts\UserRepositoryInterface;
use App\Repositories\UserRepository;

class RepositoryServiceProvider extends ServiceProvider
{
    public function register()
    {
        $this->app->bind(
            UserRepositoryInterface::class,
            UserRepository::class
        );
    }
}
```

#### 4. **✅ Clean Controller**

```php
<?php

namespace App\Http\Controllers;

use App\Repositories\Contracts\UserRepositoryInterface;
use Illuminate\Http\Request;

class UserController extends Controller
{
    public function __construct(
        private UserRepositoryInterface $userRepository
    ) {}

    public function index()
    {
        $users = $this->userRepository->paginate();
        return view('users.index', compact('users'));
    }

    public function store(Request $request)
    {
        $user = $this->userRepository->create($request->validated());
        return response()->json($user);
    }

    public function show(int $id)
    {
        $user = $this->userRepository->find($id);
        return view('users.show', compact('user'));
    }
}
```

---

## ⚙️ Service Layer

### 📖 What is Service Layer?

The **Service Layer** contains business logic and orchestrates operations between different parts of your application. It sits between controllers and repositories, handling complex business rules and workflows.

### 🎯 Why Use Service Layer?

- **🧠 Business Logic Centralization**: Keep business rules in one place
- **🔄 Reusability**: Share logic across controllers, commands, jobs
- **🧪 Better Testing**: Test business logic independently
- **📦 Single Responsibility**: Controllers handle HTTP, Services handle business
- **🔗 Orchestration**: Coordinate multiple repositories and external services

### ❌ Without Service Layer

```php
// ❌ Fat Controller with business logic
class OrderController extends Controller
{
    public function store(Request $request, UserRepositoryInterface $userRepo)
    {
        DB::beginTransaction();
        
        try {
            // Business logic mixed in controller
            $user = $userRepo->find($request->user_id);
            
            if (!$user || $user->status !== 'active') {
                throw new Exception('Invalid user');
            }
            
            $order = Order::create([
                'user_id' => $user->id,
                'total' => $request->total,
                'status' => 'pending'
            ]);
            
            foreach ($request->items as $item) {
                $product = Product::find($item['product_id']);
                
                if ($product->stock < $item['quantity']) {
                    throw new Exception('Insufficient stock');
                }
                
                OrderItem::create([
                    'order_id' => $order->id,
                    'product_id' => $product->id,
                    'quantity' => $item['quantity'],
                    'price' => $product->price
                ]);
                
                $product->decrement('stock', $item['quantity']);
            }
            
            // Send email
            Mail::to($user->email)->send(new OrderConfirmation($order));
            
            DB::commit();
            return response()->json($order);
            
        } catch (Exception $e) {
            DB::rollback();
            return response()->json(['error' => $e->getMessage()], 400);
        }
    }
}
```

### ✅ With Service Layer

#### 1. **⚙️ Service Class**

```php
<?php

namespace App\Services;

use App\Models\Order;
use App\Models\OrderItem;
use App\Repositories\Contracts\UserRepositoryInterface;
use App\Repositories\Contracts\ProductRepositoryInterface;
use App\Repositories\Contracts\OrderRepositoryInterface;
use App\DTOs\CreateOrderDTO;
use App\DTOs\OrderItemDTO;
use App\Mail\OrderConfirmation;
use App\Exceptions\InsufficientStockException;
use App\Exceptions\InvalidUserException;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Mail;

class OrderService
{
    public function __construct(
        private UserRepositoryInterface $userRepository,
        private ProductRepositoryInterface $productRepository,
        private OrderRepositoryInterface $orderRepository,
        private InventoryService $inventoryService,
        private EmailService $emailService
    ) {}

    public function createOrder(CreateOrderDTO $orderData): Order
    {
        return DB::transaction(function () use ($orderData) {
            // 1. Validate user
            $user = $this->validateUser($orderData->userId);
            
            // 2. Validate and reserve inventory
            $this->validateAndReserveInventory($orderData->items);
            
            // 3. Create order
            $order = $this->orderRepository->create([
                'user_id' => $user->id,
                'total' => $this->calculateTotal($orderData->items),
                'status' => 'pending'
            ]);
            
            // 4. Create order items
            $this->createOrderItems($order, $orderData->items);
            
            // 5. Update inventory
            $this->updateInventory($orderData->items);
            
            // 6. Send confirmation email
            $this->emailService->sendOrderConfirmation($user, $order);
            
            return $order;
        });
    }

    public function cancelOrder(int $orderId): bool
    {
        return DB::transaction(function () use ($orderId) {
            $order = $this->orderRepository->find($orderId);
            
            if (!$order || $order->status !== 'pending') {
                throw new InvalidOrderException('Cannot cancel this order');
            }
            
            // Restore inventory
            $this->restoreInventory($order->items);
            
            // Update order status
            $this->orderRepository->update($orderId, ['status' => 'cancelled']);
            
            // Send cancellation email
            $this->emailService->sendOrderCancellation($order->user, $order);
            
            return true;
        });
    }

    public function processPayment(int $orderId, string $paymentMethod): bool
    {
        $order = $this->orderRepository->find($orderId);
        
        // Delegate to payment service
        $paymentResult = $this->paymentService->processPayment(
            $order->total,
            $paymentMethod
        );
        
        if ($paymentResult->isSuccessful()) {
            $this->orderRepository->update($orderId, [
                'status' => 'paid',
                'payment_id' => $paymentResult->getTransactionId()
            ]);
            
            $this->emailService->sendPaymentConfirmation($order->user, $order);
            return true;
        }
        
        return false;
    }

    private function validateUser(int $userId): User
    {
        $user = $this->userRepository->find($userId);
        
        if (!$user || $user->status !== 'active') {
            throw new InvalidUserException('Invalid or inactive user');
        }
        
        return $user;
    }

    private function validateAndReserveInventory(array $items): void
    {
        foreach ($items as $itemData) {
            $product = $this->productRepository->find($itemData->productId);
            
            if (!$this->inventoryService->hasStock($product, $itemData->quantity)) {
                throw new InsufficientStockException(
                    "Insufficient stock for product: {$product->name}"
                );
            }
        }
    }

    private function calculateTotal(array $items): float
    {
        $total = 0;
        
        foreach ($items as $itemData) {
            $product = $this->productRepository->find($itemData->productId);
            $total += $product->price * $itemData->quantity;
        }
        
        return $total;
    }

    private function createOrderItems(Order $order, array $items): void
    {
        foreach ($items as $itemData) {
            $product = $this->productRepository->find($itemData->productId);
            
            OrderItem::create([
                'order_id' => $order->id,
                'product_id' => $product->id,
                'quantity' => $itemData->quantity,
                'price' => $product->price
            ]);
        }
    }

    private function updateInventory(array $items): void
    {
        foreach ($items as $itemData) {
            $this->inventoryService->reduceStock(
                $itemData->productId,
                $itemData->quantity
            );
        }
    }
}
```

#### 2. **✅ Clean Controller with Service**

```php
<?php

namespace App\Http\Controllers;

use App\Services\OrderService;
use App\DTOs\CreateOrderDTO;
use App\Http\Requests\CreateOrderRequest;
use Illuminate\Http\JsonResponse;

class OrderController extends Controller
{
    public function __construct(
        private OrderService $orderService
    ) {}

    public function store(CreateOrderRequest $request): JsonResponse
    {
        try {
            $orderDTO = CreateOrderDTO::fromRequest($request);
            $order = $this->orderService->createOrder($orderDTO);
            
            return response()->json($order, 201);
        } catch (Exception $e) {
            return response()->json(['error' => $e->getMessage()], 400);
        }
    }

    public function cancel(int $orderId): JsonResponse
    {
        try {
            $this->orderService->cancelOrder($orderId);
            return response()->json(['message' => 'Order cancelled successfully']);
        } catch (Exception $e) {
            return response()->json(['error' => $e->getMessage()], 400);
        }
    }
}
```

---

## 📦 DTOs (Data Transfer Objects)

### 📖 What are DTOs?

**Data Transfer Objects (DTOs)** are simple objects that carry data between processes. They contain no business logic, only data and basic validation. DTOs help structure data flow and provide type safety.

### 🎯 Why Use DTOs?

- **🛡️ Type Safety**: Ensure data structure consistency
- **📝 Documentation**: Self-documenting data contracts
- **✅ Validation**: Centralized data validation
- **🔄 Transformation**: Convert between different data formats
- **🧪 Testing**: Easier to mock and test
- **🎯 API Contracts**: Clear API input/output definitions

### 🏗️ DTO Implementation

#### 1. **📝 Basic DTO Structure**

```php
<?php

namespace App\DTOs;

class CreateUserDTO
{
    public function __construct(
        public readonly string $name,
        public readonly string $email,
        public readonly string $password,
        public readonly ?string $phone = null,
        public readonly array $roles = []
    ) {}

    public static function fromRequest(Request $request): self
    {
        return new self(
            name: $request->input('name'),
            email: $request->input('email'),
            password: $request->input('password'),
            phone: $request->input('phone'),
            roles: $request->input('roles', [])
        );
    }

    public static function fromArray(array $data): self
    {
        return new self(
            name: $data['name'],
            email: $data['email'],
            password: $data['password'],
            phone: $data['phone'] ?? null,
            roles: $data['roles'] ?? []
        );
    }

    public function toArray(): array
    {
        return [
            'name' => $this->name,
            'email' => $this->email,
            'password' => $this->password,
            'phone' => $this->phone,
            'roles' => $this->roles
        ];
    }

    public function toCreateArray(): array
    {
        return array_filter([
            'name' => $this->name,
            'email' => $this->email,
            'password' => Hash::make($this->password),
            'phone' => $this->phone,
            'email_verified_at' => now()
        ], fn($value) => $value !== null);
    }
}
```

#### 2. **🔗 Complex DTO with Nested Objects**

```php
<?php

namespace App\DTOs;

use App\DTOs\OrderItemDTO;
use Illuminate\Http\Request;

class CreateOrderDTO
{
    public function __construct(
        public readonly int $userId,
        public readonly array $items, // Array of OrderItemDTO
        public readonly ?string $notes = null,
        public readonly ?string $couponCode = null,
        public readonly string $shippingAddress = '',
        public readonly string $billingAddress = ''
    ) {}

    public static function fromRequest(Request $request): self
    {
        $items = collect($request->input('items', []))
            ->map(fn($item) => OrderItemDTO::fromArray($item))
            ->toArray();

        return new self(
            userId: $request->input('user_id'),
            items: $items,
            notes: $request->input('notes'),
            couponCode: $request->input('coupon_code'),
            shippingAddress: $request->input('shipping_address'),
            billingAddress: $request->input('billing_address')
        );
    }

    public function getTotalQuantity(): int
    {
        return array_sum(array_map(
            fn(OrderItemDTO $item) => $item->quantity,
            $this->items
        ));
    }

    public function hasItems(): bool
    {
        return !empty($this->items);
    }

    public function toArray(): array
    {
        return [
            'user_id' => $this->userId,
            'items' => array_map(fn(OrderItemDTO $item) => $item->toArray(), $this->items),
            'notes' => $this->notes,
            'coupon_code' => $this->couponCode,
            'shipping_address' => $this->shippingAddress,
            'billing_address' => $this->billingAddress
        ];
    }
}
```

#### 3. **🛒 Order Item DTO**

```php
<?php

namespace App\DTOs;

class OrderItemDTO
{
    public function __construct(
        public readonly int $productId,
        public readonly int $quantity,
        public readonly ?float $customPrice = null
    ) {
        if ($quantity <= 0) {
            throw new InvalidArgumentException('Quantity must be greater than 0');
        }
    }

    public static function fromArray(array $data): self
    {
        return new self(
            productId: $data['product_id'],
            quantity: $data['quantity'],
            customPrice: $data['custom_price'] ?? null
        );
    }

    public function toArray(): array
    {
        return array_filter([
            'product_id' => $this->productId,
            'quantity' => $this->quantity,
            'custom_price' => $this->customPrice
        ], fn($value) => $value !== null);
    }
}
```

#### 4. **🔄 Response DTOs**

```php
<?php

namespace App\DTOs;

use App\Models\User;
use Carbon\Carbon;

class UserResponseDTO
{
    public function __construct(
        public readonly int $id,
        public readonly string $name,
        public readonly string $email,
        public readonly ?string $phone,
        public readonly array $roles,
        public readonly Carbon $createdAt,
        public readonly bool $isActive
    ) {}

    public static function fromModel(User $user): self
    {
        return new self(
            id: $user->id,
            name: $user->name,
            email: $user->email,
            phone: $user->phone,
            roles: $user->roles->pluck('name')->toArray(),
            createdAt: $user->created_at,
            isActive: $user->status === 'active'
        );
    }

    public static function collection(iterable $users): array
    {
        return array_map(
            fn(User $user) => self::fromModel($user)->toArray(),
            is_array($users) ? $users : $users->all()
        );
    }

    public function toArray(): array
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'email' => $this->email,
            'phone' => $this->phone,
            'roles' => $this->roles,
            'created_at' => $this->createdAt->toISOString(),
            'is_active' => $this->isActive
        ];
    }
}
```

---

## 🏢 Complete Implementation Example

Let's see how Repository Pattern, Service Layer, and DTOs work together in a complete e-commerce example:

### 📁 Directory Structure
```
app/
├── DTOs/
│   ├── CreateOrderDTO.php
│   ├── OrderItemDTO.php
│   └── OrderResponseDTO.php
├── Repositories/
│   ├── Contracts/
│   │   ├── OrderRepositoryInterface.php
│   │   ├── ProductRepositoryInterface.php
│   │   └── UserRepositoryInterface.php
│   ├── OrderRepository.php
│   ├── ProductRepository.php
│   └── UserRepository.php
├── Services/
│   ├── OrderService.php
│   ├── PaymentService.php
│   └── InventoryService.php
└── Http/Controllers/
    └── OrderController.php
```

### 🛒 Complete Order Management Example

#### 1. **📦 Product Repository**

```php
<?php

namespace App\Repositories;

use App\Models\Product;
use App\Repositories\Contracts\ProductRepositoryInterface;

class ProductRepository implements ProductRepositoryInterface
{
    public function __construct(private Product $model) {}

    public function find(int $id): ?Product
    {
        return $this->model->find($id);
    }

    public function findWithStock(int $id): ?Product
    {
        return $this->model->where('id', $id)
            ->where('stock', '>', 0)
            ->first();
    }

    public function updateStock(int $productId, int $quantity): bool
    {
        return $this->model->where('id', $productId)
            ->update(['stock' => $quantity]);
    }

    public function decrementStock(int $productId, int $quantity): bool
    {
        return $this->model->where('id', $productId)
            ->where('stock', '>=', $quantity)
            ->decrement('stock', $quantity) > 0;
    }

    public function getAvailableProducts(): Collection
    {
        return $this->model->where('stock', '>', 0)
            ->where('status', 'active')
            ->get();
    }
}
```

#### 2. **⚙️ Inventory Service**

```php
<?php

namespace App\Services;

use App\Repositories\Contracts\ProductRepositoryInterface;
use App\Exceptions\InsufficientStockException;

class InventoryService
{
    public function __construct(
        private ProductRepositoryInterface $productRepository
    ) {}

    public function hasStock(int $productId, int $quantity): bool
    {
        $product = $this->productRepository->find($productId);
        return $product && $product->stock >= $quantity;
    }

    public function reserveStock(int $productId, int $quantity): bool
    {
        if (!$this->hasStock($productId, $quantity)) {
            throw new InsufficientStockException(
                "Insufficient stock for product ID: {$productId}"
            );
        }

        return $this->productRepository->decrementStock($productId, $quantity);
    }

    public function restoreStock(int $productId, int $quantity): bool
    {
        $product = $this->productRepository->find($productId);
        
        if (!$product) {
            return false;
        }

        return $this->productRepository->updateStock(
            $productId,
            $product->stock + $quantity
        );
    }

    public function validateInventory(array $items): void
    {
        foreach ($items as $item) {
            if (!$this->hasStock($item->productId, $item->quantity)) {
                $product = $this->productRepository->find($item->productId);
                throw new InsufficientStockException(
                    "Insufficient stock for {$product->name}. Available: {$product->stock}, Required: {$item->quantity}"
                );
            }
        }
    }
}
```

#### 3. **💳 Payment Service**

```php
<?php

namespace App\Services;

use App\DTOs\PaymentResultDTO;

class PaymentService
{
    public function processPayment(
        float $amount,
        string $method,
        array $paymentData = []
    ): PaymentResultDTO {
        
        // Simulate payment processing
        switch ($method) {
            case 'stripe':
                return $this->processStripePayment($amount, $paymentData);
            case 'paypal':
                return $this->processPayPalPayment($amount, $paymentData);
            default:
                throw new InvalidPaymentMethodException("Unsupported payment method: {$method}");
        }
    }

    private function processStripePayment(float $amount, array $data): PaymentResultDTO
    {
        // Stripe payment logic
        $transactionId = 'stripe_' . uniqid();
        
        return new PaymentResultDTO(
            success: true,
            transactionId: $transactionId,
            amount: $amount,
            method: 'stripe',
            message: 'Payment processed successfully'
        );
    }

    private function processPayPalPayment(float $amount, array $data): PaymentResultDTO
    {
        // PayPal payment logic
        $transactionId = 'paypal_' . uniqid();
        
        return new PaymentResultDTO(
            success: true,
            transactionId: $transactionId,
            amount: $amount,
            method: 'paypal',
            message: 'Payment processed successfully'
        );
    }
}
```

#### 4. **📧 Email Service**

```php
<?php

namespace App\Services;

use App\Models\User;
use App\Models\Order;
use App\Mail\OrderConfirmation;
use App\Mail\OrderCancellation;
use App\Mail\PaymentConfirmation;
use Illuminate\Support\Facades\Mail;

class EmailService
{
    public function sendOrderConfirmation(User $user, Order $order): void
    {
        Mail::to($user->email)->send(new OrderConfirmation($order));
    }

    public function sendOrderCancellation(User $user, Order $order): void
    {
        Mail::to($user->email)->send(new OrderCancellation($order));
    }

    public function sendPaymentConfirmation(User $user, Order $order): void
    {
        Mail::to($user->email)->send(new PaymentConfirmation($order));
    }
}
```

#### 5. **🎯 Final Controller**

```php
<?php

namespace App\Http\Controllers;

use App\Services\OrderService;
use App\Services\PaymentService;
use App\DTOs\CreateOrderDTO;
use App\DTOs\OrderResponseDTO;
use App\Http\Requests\CreateOrderRequest;
use App\Http\Requests\ProcessPaymentRequest;
use Illuminate\Http\JsonResponse;

class OrderController extends Controller
{
    public function __construct(
        private OrderService $orderService,
        private PaymentService $paymentService
    ) {}

    public function store(CreateOrderRequest $request): JsonResponse
    {
        try {
            $orderDTO = CreateOrderDTO::fromRequest($request);
            $order = $this->orderService->createOrder($orderDTO);
            
            return response()->json(
                OrderResponseDTO::fromModel($order)->toArray(),
                201
            );
        } catch (Exception $e) {
            return response()->json([
                'error' => $e->getMessage()
            ], 400);
        }
    }

    public function processPayment(
        int $orderId,
        ProcessPaymentRequest $request
    ): JsonResponse {
        try {
            $result = $this->orderService->processPayment(
                $orderId,
                $request->input('payment_method'),
                $request->input('payment_data', [])
            );

            return response()->json([
                'success' => $result,
                'message' => $result ? 'Payment processed successfully' : 'Payment failed'
            ]);
        } catch (Exception $e) {
            return response()->json([
                'error' => $e->getMessage()
            ], 400);
        }
    }

    public function cancel(int $orderId): JsonResponse
    {
        try {
            $this->orderService->cancelOrder($orderId);
            
            return response()->json([
                'message' => 'Order cancelled successfully'
            ]);
        } catch (Exception $e) {
            return response()->json([
                'error' => $e->getMessage()
            ], 400);
        }
    }

    public function show(int $orderId): JsonResponse
    {
        $order = $this->orderService->getOrderById($orderId);
        
        return response()->json(
            OrderResponseDTO::fromModel($order)->toArray()
        );
    }
}
```

---

## ✨ Best Practices

### 🗄️ Repository Pattern Best Practices

1. **🎭 Use Interfaces**: Always create interfaces for repositories
```php
// ✅ Good
interface UserRepositoryInterface { ... }
class EloquentUserRepository implements UserRepositoryInterface { ... }

// ❌ Avoid
class UserRepository { ... } // No interface
```

2. **📦 Keep Repositories Simple**: Only data access logic
```php
// ✅ Good - Simple data access
public function getActiveUsers(): Collection
{
    return $this->model->where('status', 'active')->get();
}

// ❌ Avoid - Business logic in repository
public function createUserWithWelcomeEmail(array $data): User
{
    $user = $this->model->create($data);
    Mail::to($user->email)->send(new WelcomeEmail($user)); // Business logic!
    return $user;
}
```

3. **🔍 Specific Methods**: Create specific methods instead of generic ones
```php
// ✅ Good - Specific methods
public function getActiveUsers(): Collection;
public function getRecentUsers(int $days): Collection;
public function getUsersWithPosts(): Collection;

// ❌ Avoid - Too generic
public function getUsers(array $criteria = []): Collection;
```

### ⚙️ Service Layer Best Practices

1. **🧠 Single Responsibility**: One service per domain
```php
// ✅ Good - Focused services
class OrderService { ... }
class PaymentService { ... }
class InventoryService { ... }

// ❌ Avoid - God service
class ECommerceService { 
    // Handles orders, payments, inventory, users, etc.
}
```

2. **🔄 Use Transactions**: Wrap complex operations
```php
// ✅ Good - Transactional service method
public function createOrder(CreateOrderDTO $orderData): Order
{
    return DB::transaction(function () use ($orderData) {
        // Multiple database operations
        $order = $this->orderRepository->create($orderData->toArray());
        $this->inventoryService->reserveStock($orderData->items);
        $this->emailService->sendConfirmation($order);
        return $order;
    });
}
```

3. **🎯 Inject Dependencies**: Use dependency injection
```php
// ✅ Good - Dependency injection
class OrderService
{
    public function __construct(
        private OrderRepositoryInterface $orderRepository,
        private InventoryService $inventoryService,
        private EmailService $emailService
    ) {}
}

// ❌ Avoid - Direct instantiation
class OrderService
{
    public function createOrder()
    {
        $orderRepo = new OrderRepository(); // Hard dependency
    }
}
```

### 📦 DTO Best Practices

1. **🔒 Immutable DTOs**: Use readonly properties
```php
// ✅ Good - Immutable DTO
class CreateUserDTO
{
    public function __construct(
        public readonly string $name,
        public readonly string $email
    ) {}
}

// ❌ Avoid - Mutable DTO
class CreateUserDTO
{
    public string $name;
    public string $email;
}
```

2. **✅ Validation in DTOs**: Include basic validation
```php
// ✅ Good - Validation in constructor
class CreateOrderDTO
{
    public function __construct(
        public readonly int $userId,
        public readonly array $items
    ) {
        if (empty($this->items)) {
            throw new InvalidArgumentException('Order must have at least one item');
        }
        
        if ($this->userId <= 0) {
            throw new InvalidArgumentException('Invalid user ID');
        }
    }
}
```

3. **🔄 Factory Methods**: Multiple creation methods
```php
// ✅ Good - Multiple factory methods
class UserDTO
{
    public static function fromRequest(Request $request): self { ... }
    public static function fromArray(array $data): self { ... }
    public static function fromModel(User $user): self { ... }
}
```

---

## 🧪 Testing Strategies

### 🗄️ Repository Testing

```php
<?php

namespace Tests\Unit\Repositories;

use Tests\TestCase;
use App\Models\User;
use App\Repositories\UserRepository;
use Illuminate\Foundation\Testing\RefreshDatabase;

class UserRepositoryTest extends TestCase
{
    use RefreshDatabase;

    private UserRepository $repository;

    protected function setUp(): void
    {
        parent::setUp();
        $this->repository = new UserRepository(new User());
    }

    /** @test */
    public function it_can_create_user()
    {
        $userData = [
            'name' => 'John Doe',
            'email' => 'john@example.com',
            'password' => 'password123'
        ];

        $user = $this->repository->create($userData);

        $this->assertInstanceOf(User::class, $user);
        $this->assertEquals('John Doe', $user->name);
        $this->assertDatabaseHas('users', ['email' => 'john@example.com']);
    }

    /** @test */
    public function it_can_find_active_users()
    {
        // Arrange
        User::factory()->create(['status' => 'active']);
        User::factory()->create(['status' => 'inactive']);
        User::factory()->create(['status' => 'active']);

        // Act
        $activeUsers = $this->repository->getActiveUsers();

        // Assert
        $this->assertCount(2, $activeUsers);
        $activeUsers->each(function ($user) {
            $this->assertEquals('active', $user->status);
        });
    }
}
```

### ⚙️ Service Testing

```php
<?php

namespace Tests\Unit\Services;

use Tests\TestCase;
use App\Services\OrderService;
use App\DTOs\CreateOrderDTO;
use App\DTOs\OrderItemDTO;
use App\Repositories\Contracts\OrderRepositoryInterface;
use App\Services\InventoryService;
use App\Services\EmailService;
use Mockery;

class OrderServiceTest extends TestCase
{
    private OrderService $orderService;
    private $orderRepository;
    private $inventoryService;
    private $emailService;

    protected function setUp(): void
    {
        parent::setUp();

        $this->orderRepository = Mockery::mock(OrderRepositoryInterface::class);
        $this->inventoryService = Mockery::mock(InventoryService::class);
        $this->emailService = Mockery::mock(EmailService::class);

        $this->orderService = new OrderService(
            $this->orderRepository,
            $this->inventoryService,
            $this->emailService
        );
    }

    /** @test */
    public function it_can_create_order_successfully()
    {
        // Arrange
        $orderDTO = new CreateOrderDTO(
            userId: 1,
            items: [
                new OrderItemDTO(productId: 1, quantity: 2),
                new OrderItemDTO(productId: 2, quantity: 1)
            ]
        );

        $this->inventoryService
            ->shouldReceive('validateInventory')
            ->once()
            ->with($orderDTO->items);

        $this->orderRepository
            ->shouldReceive('create')
            ->once()
            ->andReturn(new Order(['id' => 1]));

        $this->inventoryService
            ->shouldReceive('reserveStock')
            ->twice();

        $this->emailService
            ->shouldReceive('sendOrderConfirmation')
            ->once();

        // Act
        $order = $this->orderService->createOrder($orderDTO);

        // Assert
        $this->assertInstanceOf(Order::class, $order);
    }

    /** @test */
    public function it_throws_exception_when_insufficient_stock()
    {
        // Arrange
        $orderDTO = new CreateOrderDTO(
            userId: 1,
            items: [new OrderItemDTO(productId: 1, quantity: 10)]
        );

        $this->inventoryService
            ->shouldReceive('validateInventory')
            ->once()
            ->andThrow(new InsufficientStockException('Not enough stock'));

        // Act & Assert
        $this->expectException(InsufficientStockException::class);
        $this->orderService->createOrder($orderDTO);
    }
}
```

### 📦 DTO Testing

```php
<?php

namespace Tests\Unit\DTOs;

use Tests\TestCase;
use App\DTOs\CreateOrderDTO;
use App\DTOs\OrderItemDTO;
use Illuminate\Http\Request;

class CreateOrderDTOTest extends TestCase
{
    /** @test */
    public function it_can_be_created_from_request()
    {
        // Arrange
        $requestData = [
            'user_id' => 1,
            'items' => [
                ['product_id' => 1, 'quantity' => 2],
                ['product_id' => 2, 'quantity' => 1]
            ],
            'notes' => 'Test order'
        ];

        $request = Request::create('/', 'POST', $requestData);

        // Act
        $dto = CreateOrderDTO::fromRequest($request);

        // Assert
        $this->assertEquals(1, $dto->userId);
        $this->assertCount(2, $dto->items);
        $this->assertEquals('Test order', $dto->notes);
        $this->assertInstanceOf(OrderItemDTO::class, $dto->items[0]);
    }

    /** @test */
    public function it_calculates_total_quantity_correctly()
    {
        // Arrange
        $dto = new CreateOrderDTO(
            userId: 1,
            items: [
                new OrderItemDTO(productId: 1, quantity: 2),
                new OrderItemDTO(productId: 2, quantity: 3),
                new OrderItemDTO(productId: 3, quantity: 1)
            ]
        );

        // Act
        $totalQuantity = $dto->getTotalQuantity();

        // Assert
        $this->assertEquals(6, $totalQuantity);
    }

    /** @test */
    public function it_validates_items_exist()
    {
        // Arrange & Act
        $dto = new CreateOrderDTO(
            userId: 1,
            items: [new OrderItemDTO(productId: 1, quantity: 1)]
        );

        // Assert
        $this->assertTrue($dto->hasItems());

        // Test empty items
        $emptyDto = new CreateOrderDTO(userId: 1, items: []);
        $this->assertFalse($emptyDto->hasItems());
    }
}
```

### 🔄 Integration Testing

```php
<?php

namespace Tests\Feature;

use Tests\TestCase;
use App\Models\User;
use App\Models\Product;
use Illuminate\Foundation\Testing\RefreshDatabase;

class OrderManagementTest extends TestCase
{
    use RefreshDatabase;

    /** @test */
    public function user_can_create_order_successfully()
    {
        // Arrange
        $user = User::factory()->create();
        $product1 = Product::factory()->create(['stock' => 10, 'price' => 29.99]);
        $product2 = Product::factory()->create(['stock' => 5, 'price' => 49.99]);

        $orderData = [
            'user_id' => $user->id,
            'items' => [
                ['product_id' => $product1->id, 'quantity' => 2],
                ['product_id' => $product2->id, 'quantity' => 1]
            ],
            'shipping_address' => '123 Main St, City, State',
            'billing_address' => '123 Main St, City, State'
        ];

        // Act
        $response = $this->postJson('/api/orders', $orderData);

        // Assert
        $response->assertStatus(201);
        $response->assertJsonStructure([
            'id',
            'user_id',
            'total',
            'status',
            'items' => [
                '*' => ['product_id', 'quantity', 'price']
            ]
        ]);

        // Verify database changes
        $this->assertDatabaseHas('orders', [
            'user_id' => $user->id,
            'status' => 'pending'
        ]);

        // Verify stock reduction
        $this->assertEquals(8, $product1->fresh()->stock);
        $this->assertEquals(4, $product2->fresh()->stock);
    }

    /** @test */
    public function order_creation_fails_with_insufficient_stock()
    {
        // Arrange
        $user = User::factory()->create();
        $product = Product::factory()->create(['stock' => 1, 'price' => 29.99]);

        $orderData = [
            'user_id' => $user->id,
            'items' => [
                ['product_id' => $product->id, 'quantity' => 5] // More than available
            ]
        ];

        // Act
        $response = $this->postJson('/api/orders', $orderData);

        // Assert
        $response->assertStatus(400);
        $response->assertJson(['error' => 'Insufficient stock for ' . $product->name]);

        // Verify no database changes
        $this->assertDatabaseMissing('orders', ['user_id' => $user->id]);
        $this->assertEquals(1, $product->fresh()->stock); // Stock unchanged
    }
}
```

---

## 🚀 Advanced Patterns

### 🔧 Repository with Specifications

```php
<?php

namespace App\Specifications;

interface SpecificationInterface
{
    public function apply($query);
}

class ActiveUsersSpecification implements SpecificationInterface
{
    public function apply($query)
    {
        return $query->where('status', 'active');
    }
}

class RecentUsersSpecification implements SpecificationInterface
{
    public function __construct(private int $days) {}

    public function apply($query)
    {
        return $query->where('created_at', '>=', now()->subDays($this->days));
    }
}

// Enhanced Repository
class UserRepository implements UserRepositoryInterface
{
    public function findBySpecification(SpecificationInterface $specification): Collection
    {
        $query = $this->model->newQuery();
        return $specification->apply($query)->get();
    }

    public function findBySpecifications(array $specifications): Collection
    {
        $query = $this->model->newQuery();
        
        foreach ($specifications as $specification) {
            $query = $specification->apply($query);
        }
        
        return $query->get();
    }
}

// Usage
$activeRecentUsers = $userRepository->findBySpecifications([
    new ActiveUsersSpecification(),
    new RecentUsersSpecification(30)
]);
```

### 🎯 Command Pattern with Services

```php
<?php

namespace App\Commands;

interface CommandInterface
{
    public function execute();
}

class CreateOrderCommand implements CommandInterface
{
    public function __construct(
        private CreateOrderDTO $orderData,
        private OrderService $orderService
    ) {}

    public function execute(): Order
    {
        return $this->orderService->createOrder($this->orderData);
    }
}

class CommandBus
{
    public function execute(CommandInterface $command)
    {
        return $command->execute();
    }
}

// Usage in Controller
class OrderController extends Controller
{
    public function store(CreateOrderRequest $request, CommandBus $commandBus)
    {
        $orderDTO = CreateOrderDTO::fromRequest($request);
        $command = new CreateOrderCommand($orderDTO, app(OrderService::class));
        
        $order = $commandBus->execute($command);
        
        return response()->json(OrderResponseDTO::fromModel($order)->toArray());
    }
}
```

---

## 🎓 Summary

### 🗄️ Repository Pattern
- **🎯 Purpose**: Encapsulate data access logic
- **✅ Benefits**: Testability, flexibility, clean separation
- **🔧 Usage**: Create interfaces, implement with Eloquent, inject into services

### ⚙️ Service Layer
- **🎯 Purpose**: Handle business logic and orchestration
- **✅ Benefits**: Reusability, single responsibility, better organization
- **🔧 Usage**: Coordinate repositories, handle transactions, implement business rules

### 📦 DTOs (Data Transfer Objects)
- **🎯 Purpose**: Structure data transfer between layers
- **✅ Benefits**: Type safety, validation, clear contracts
- **🔧 Usage**: Request/response mapping, service communication, API contracts

### 🏗️ Architecture Flow
```
Request → Controller → Service Layer → Repository → Database
   ↑         ↓           ↓              ↓
  DTO ← Response ← Business Logic ← Data Access
```

### 🎯 Key Benefits
- **🧪 Better Testing**: Each layer can be tested independently
- **🔄 Maintainability**: Clear separation of concerns
- **⚡ Flexibility**: Easy to swap implementations
- **📈 Scalability**: Well-organized, extensible codebase
- **🛡️ Type Safety**: DTOs provide compile-time checks
- **🎯 Clean Code**: Each class has a single responsibility

This architecture pattern creates a robust, maintainable, and testable Laravel application that follows SOLID principles and best practices!