# Complete Performance Optimization Guide
## Laravel 12 + React + Vite + Socket.io Project

---

## 1. Laravel Backend Optimization

### 1.1 Database Optimization

#### Query Optimization
```php
// Use eager loading to prevent N+1 queries
$users = User::with(['posts', 'comments'])->get();

// Use select to limit columns
$users = User::select(['id', 'name', 'email'])->get();

// Use database indexing
Schema::table('users', function (Blueprint $table) {
    $table->index(['email', 'status']);
    $table->index('created_at');
});

// Use query scopes for complex queries
class User extends Model {
    public function scopeActive($query) {
        return $query->where('status', 'active');
    }
}
```

#### Database Configuration
```php
// config/database.php
'mysql' => [
    'driver' => 'mysql',
    'options' => [
        PDO::MYSQL_ATTR_USE_BUFFERED_QUERY => false,
    ],
    'charset' => 'utf8mb4',
    'collation' => 'utf8mb4_unicode_ci',
    'strict' => true,
    'engine' => 'InnoDB',
],
```

### 1.2 Caching Strategies

#### Redis Configuration
```php
// config/cache.php
'default' => env('CACHE_DRIVER', 'redis'),

'stores' => [
    'redis' => [
        'driver' => 'redis',
        'connection' => 'cache',
        'lock_connection' => 'default',
    ],
],

// Usage examples
Cache::remember('users.active', 3600, function () {
    return User::active()->get();
});

// Model caching
class User extends Model {
    use Cacheable;
    
    protected $cacheTags = ['users'];
}
```

#### API Response Caching
```php
// routes/api.php
Route::get('/users', [UserController::class, 'index'])
    ->middleware('cache.headers:public;max_age=3600');

// Controller caching
class UserController extends Controller {
    public function index() {
        return Cache::remember('api.users', 3600, function() {
            return UserResource::collection(User::active()->get());
        });
    }
}
```

### 1.3 Queue System Optimization

#### Queue Configuration
```php
// config/queue.php
'default' => env('QUEUE_CONNECTION', 'redis'),

'connections' => [
    'redis' => [
        'driver' => 'redis',
        'connection' => 'default',
        'queue' => env('REDIS_QUEUE', 'default'),
        'retry_after' => 90,
        'block_for' => null,
        'after_commit' => false,
    ],
],

// Job example
class ProcessDataJob implements ShouldQueue {
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;
    
    public $timeout = 120;
    public $maxExceptions = 3;
    
    public function handle() {
        // Heavy processing logic here
    }
}
```

### 1.4 API Resource Optimization

```php
// Optimized API Resources
class UserResource extends JsonResource {
    public function toArray($request) {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'email' => $this->when($request->user()->isAdmin(), $this->email),
            'posts' => PostResource::collection($this->whenLoaded('posts')),
        ];
    }
}

// Paginated responses
class UserController extends Controller {
    public function index() {
        $users = User::select(['id', 'name', 'email'])
                    ->paginate(20);
                    
        return UserResource::collection($users);
    }
}
```

### 1.5 Laravel Performance Configuration

```php
// .env optimizations
APP_DEBUG=false
LOG_LEVEL=error
SESSION_DRIVER=redis
CACHE_DRIVER=redis
QUEUE_CONNECTION=redis
DB_CONNECTION=mysql

// config/app.php
'debug' => env('APP_DEBUG', false),

// Optimize Artisan commands
php artisan config:cache
php artisan route:cache
php artisan view:cache
php artisan event:cache
php artisan optimize
```

---

## 2. React Frontend Optimization

### 2.1 Component Optimization

#### React.memo and useMemo
```jsx
import React, { memo, useMemo, useCallback } from 'react';

// Memoized component
const UserCard = memo(({ user, onEdit }) => {
    const fullName = useMemo(() => 
        `${user.firstName} ${user.lastName}`, 
        [user.firstName, user.lastName]
    );
    
    const handleEdit = useCallback(() => {
        onEdit(user.id);
    }, [user.id, onEdit]);
    
    return (
        <div className="user-card">
            <h3>{fullName}</h3>
            <button onClick={handleEdit}>Edit</button>
        </div>
    );
});

// Custom hooks for data fetching
const useUsers = () => {
    const [users, setUsers] = useState([]);
    const [loading, setLoading] = useState(false);
    
    const fetchUsers = useCallback(async () => {
        setLoading(true);
        try {
            const response = await api.get('/users');
            setUsers(response.data);
        } finally {
            setLoading(false);
        }
    }, []);
    
    return { users, loading, fetchUsers };
};
```

### 2.2 State Management Optimization

#### Context Optimization
```jsx
// Split contexts to avoid unnecessary re-renders
const UserContext = createContext();
const ThemeContext = createContext();

const UserProvider = ({ children }) => {
    const [user, setUser] = useState(null);
    
    const value = useMemo(() => ({ user, setUser }), [user]);
    
    return (
        <UserContext.Provider value={value}>
            {children}
        </UserContext.Provider>
    );
};

// Zustand for global state (lightweight alternative)
import { create } from 'zustand';

const useStore = create((set) => ({
    users: [],
    loading: false,
    setUsers: (users) => set({ users }),
    setLoading: (loading) => set({ loading }),
}));
```

### 2.3 Code Splitting and Lazy Loading

```jsx
import { lazy, Suspense } from 'react';

// Lazy load components
const UserDashboard = lazy(() => import('./pages/UserDashboard'));
const AdminPanel = lazy(() => import('./pages/AdminPanel'));

// Route-based code splitting
const App = () => (
    <Router>
        <Suspense fallback={<div>Loading...</div>}>
            <Routes>
                <Route path="/dashboard" element={<UserDashboard />} />
                <Route path="/admin" element={<AdminPanel />} />
            </Routes>
        </Suspense>
    </Router>
);

// Component-based code splitting
const LazyModal = lazy(() => import('./components/Modal'));

const MyComponent = () => {
    const [showModal, setShowModal] = useState(false);
    
    return (
        <div>
            {showModal && (
                <Suspense fallback={<div>Loading modal...</div>}>
                    <LazyModal />
                </Suspense>
            )}
        </div>
    );
};
```

### 2.4 API Integration Optimization

#### React Query Implementation
```jsx
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';

// Optimized data fetching
const useUsers = () => {
    return useQuery({
        queryKey: ['users'],
        queryFn: () => api.get('/users').then(res => res.data),
        staleTime: 5 * 60 * 1000, // 5 minutes
        cacheTime: 10 * 60 * 1000, // 10 minutes
    });
};

// Optimistic updates
const useUpdateUser = () => {
    const queryClient = useQueryClient();
    
    return useMutation({
        mutationFn: (userData) => api.put(`/users/${userData.id}`, userData),
        onMutate: async (newUser) => {
            await queryClient.cancelQueries(['users']);
            const previousUsers = queryClient.getQueryData(['users']);
            
            queryClient.setQueryData(['users'], old => 
                old.map(user => user.id === newUser.id ? newUser : user)
            );
            
            return { previousUsers };
        },
        onError: (err, newUser, context) => {
            queryClient.setQueryData(['users'], context.previousUsers);
        },
        onSettled: () => {
            queryClient.invalidateQueries(['users']);
        },
    });
};
```

---

## 3. Vite Build Optimization

### 3.1 Vite Configuration

```javascript
// vite.config.js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import { resolve } from 'path';

export default defineConfig({
    plugins: [react()],
    
    // Build optimizations
    build: {
        target: 'esnext',
        minify: 'esbuild',
        cssMinify: true,
        rollupOptions: {
            output: {
                manualChunks: {
                    vendor: ['react', 'react-dom'],
                    ui: ['@headlessui/react', '@heroicons/react'],
                },
            },
        },
        chunkSizeWarningLimit: 1000,
    },
    
    // Development optimizations
    server: {
        hmr: {
            overlay: false,
        },
    },
    
    // Dependency optimization
    optimizeDeps: {
        include: ['react', 'react-dom', 'axios'],
        exclude: ['@vite/client', '@vite/env'],
    },
    
    // Path resolution
    resolve: {
        alias: {
            '@': resolve(__dirname, 'src'),
            '@components': resolve(__dirname, 'src/components'),
            '@pages': resolve(__dirname, 'src/pages'),
        },
    },
});
```

### 3.2 Asset Optimization

```javascript
// Image optimization
import imageOptimize from 'vite-plugin-imagemin';

export default defineConfig({
    plugins: [
        react(),
        imageOptimize({
            gifsicle: { optimizationLevel: 7 },
            mozjpeg: { quality: 80 },
            pngquant: { quality: [0.65, 0.8] },
            svgo: {
                plugins: [
                    { name: 'removeViewBox', active: false },
                    { name: 'removeEmptyAttrs', active: false },
                ],
            },
        }),
    ],
});

// CSS optimization
import { defineConfig } from 'vite';
import { resolve } from 'path';

export default defineConfig({
    css: {
        modules: {
            localsConvention: 'camelCaseOnly',
        },
        preprocessorOptions: {
            scss: {
                additionalData: `@import "@/styles/variables.scss";`,
            },
        },
    },
});
```

---

## 4. Socket.io Optimization

### 4.1 Server-Side Optimization

```javascript
// server.js
import express from 'express';
import { createServer } from 'http';
import { Server } from 'socket.io';
import Redis from 'ioredis';
import { createAdapter } from '@socket.io/redis-adapter';

const app = express();
const server = createServer(app);

// Redis adapter for scaling
const pubClient = new Redis(process.env.REDIS_URL);
const subClient = pubClient.duplicate();

const io = new Server(server, {
    cors: {
        origin: process.env.CLIENT_URL,
        methods: ["GET", "POST"]
    },
    // Connection optimization
    pingTimeout: 60000,
    pingInterval: 25000,
    upgradeTimeout: 30000,
    maxHttpBufferSize: 1e6,
});

io.adapter(createAdapter(pubClient, subClient));

// Namespace organization
const userNamespace = io.of('/users');
const adminNamespace = io.of('/admin');

// Room management
const roomManager = {
    joinRoom: (socket, roomId) => {
        socket.join(roomId);
        socket.to(roomId).emit('user-joined', socket.userId);
    },
    
    leaveRoom: (socket, roomId) => {
        socket.leave(roomId);
        socket.to(roomId).emit('user-left', socket.userId);
    },
};

// Optimized event handlers
io.on('connection', (socket) => {
    console.log('User connected:', socket.id);
    
    // Throttle message sending
    const messageHandler = throttle((data) => {
        socket.broadcast.emit('message', data);
    }, 100);
    
    socket.on('send-message', messageHandler);
    
    // Batch updates
    const updates = [];
    const batchUpdate = debounce(() => {
        if (updates.length > 0) {
            socket.broadcast.emit('batch-update', updates);
            updates.length = 0;
        }
    }, 50);
    
    socket.on('update', (data) => {
        updates.push(data);
        batchUpdate();
    });
});

// Utility functions
function throttle(func, delay) {
    let timeoutId;
    let lastExecTime = 0;
    
    return function (...args) {
        const currentTime = Date.now();
        
        if (currentTime - lastExecTime > delay) {
            func.apply(this, args);
            lastExecTime = currentTime;
        } else {
            clearTimeout(timeoutId);
            timeoutId = setTimeout(() => {
                func.apply(this, args);
                lastExecTime = Date.now();
            }, delay - (currentTime - lastExecTime));
        }
    };
}

function debounce(func, delay) {
    let timeoutId;
    return function (...args) {
        clearTimeout(timeoutId);
        timeoutId = setTimeout(() => func.apply(this, args), delay);
    };
}
```

### 4.2 Client-Side Socket Optimization

```javascript
// hooks/useSocket.js
import { useEffect, useRef, useCallback } from 'react';
import { io } from 'socket.io-client';

export const useSocket = (namespace = '') => {
    const socket = useRef(null);
    const reconnectAttempts = useRef(0);
    
    useEffect(() => {
        socket.current = io(`${process.env.REACT_APP_SOCKET_URL}${namespace}`, {
            autoConnect: false,
            reconnection: true,
            reconnectionDelay: 1000,
            reconnectionDelayMax: 5000,
            maxReconnectionAttempts: 5,
            timeout: 20000,
        });
        
        // Connection management
        socket.current.on('connect', () => {
            console.log('Connected to server');
            reconnectAttempts.current = 0;
        });
        
        socket.current.on('disconnect', (reason) => {
            console.log('Disconnected:', reason);
            if (reason === 'io server disconnect') {
                socket.current.connect();
            }
        });
        
        socket.current.on('reconnect_attempt', () => {
            reconnectAttempts.current++;
            console.log(`Reconnection attempt: ${reconnectAttempts.current}`);
        });
        
        socket.current.connect();
        
        return () => {
            socket.current?.disconnect();
        };
    }, [namespace]);
    
    const emit = useCallback((event, data) => {
        if (socket.current?.connected) {
            socket.current.emit(event, data);
        }
    }, []);
    
    const on = useCallback((event, callback) => {
        socket.current?.on(event, callback);
        
        return () => {
            socket.current?.off(event, callback);
        };
    }, []);
    
    return { emit, on, socket: socket.current };
};

// Message batching hook
export const useBatchedMessages = (onMessages) => {
    const messagesRef = useRef([]);
    const timeoutRef = useRef(null);
    
    const addMessage = useCallback((message) => {
        messagesRef.current.push(message);
        
        if (timeoutRef.current) {
            clearTimeout(timeoutRef.current);
        }
        
        timeoutRef.current = setTimeout(() => {
            onMessages(messagesRef.current);
            messagesRef.current = [];
        }, 16); // ~60fps
    }, [onMessages]);
    
    return addMessage;
};
```

---

## 5. Server Infrastructure Optimization

### 5.1 Nginx Configuration

```nginx
# /etc/nginx/sites-available/laravel-app
server {
    listen 80;
    server_name your-domain.com;
    root /var/www/laravel/public;
    
    index index.php index.html;
    
    # Gzip compression
    gzip on;
    gzip_vary on;
    gzip_min_length 1024;
    gzip_types
        text/plain
        text/css
        text/xml
        text/javascript
        application/javascript
        application/xml+rss
        application/json;
    
    # Static file caching
    location ~* \.(jpg|jpeg|png|gif|ico|css|js|woff|woff2)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
    
    # PHP-FPM configuration
    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.2-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
        
        # Increase buffer sizes
        fastcgi_buffer_size 64k;
        fastcgi_buffers 4 64k;
        fastcgi_busy_buffers_size 128k;
    }
    
    # Socket.io proxy
    location /socket.io/ {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### 5.2 PHP-FPM Optimization

```ini
; /etc/php/8.2/fpm/pool.d/www.conf
[www]
user = www-data
group = www-data
listen = /var/run/php/php8.2-fpm.sock
listen.owner = www-data
listen.group = www-data
listen.mode = 0660

; Process management
pm = dynamic
pm.max_children = 50
pm.start_servers = 10
pm.min_spare_servers = 5
pm.max_spare_servers = 20
pm.max_requests = 500

; Performance tuning
request_terminate_timeout = 30s
rlimit_files = 131072
rlimit_core = unlimited

; Memory settings
php_admin_value[memory_limit] = 256M
php_admin_value[max_execution_time] = 30
php_admin_value[max_input_time] = 30
php_admin_value[post_max_size] = 20M
php_admin_value[upload_max_filesize] = 20M
```

### 5.3 Redis Configuration

```conf
# /etc/redis/redis.conf
maxmemory 2gb
maxmemory-policy allkeys-lru
save 900 1
save 300 10
save 60 10000

# Network optimization
tcp-keepalive 300
timeout 300

# Performance
hz 10
tcp-backlog 511
databases 16
```

---

## 6. Monitoring and Optimization Tools

### 6.1 Laravel Performance Monitoring

```php
// Install Laravel Telescope for debugging
composer require laravel/telescope --dev

// Install Laravel Debugbar
composer require barryvdh/laravel-debugbar --dev

// Custom performance monitoring
class PerformanceMonitor {
    public static function time($callback, $label = '') {
        $start = microtime(true);
        $result = $callback();
        $duration = (microtime(true) - $start) * 1000;
        
        Log::info("Performance: {$label}", [
            'duration_ms' => round($duration, 2),
            'memory_mb' => round(memory_get_usage(true) / 1024 / 1024, 2)
        ]);
        
        return $result;
    }
}
```

### 6.2 Frontend Performance Monitoring

```javascript
// Performance monitoring utilities
export const performanceMonitor = {
    measureRender: (componentName) => {
        return (target, propertyKey, descriptor) => {
            const originalMethod = descriptor.value;
            
            descriptor.value = function (...args) {
                const start = performance.now();
                const result = originalMethod.apply(this, args);
                const end = performance.now();
                
                console.log(`${componentName} render time: ${end - start}ms`);
                return result;
            };
            
            return descriptor;
        };
    },
    
    measureAsyncOperation: async (operation, label) => {
        const start = performance.now();
        try {
            const result = await operation();
            const end = performance.now();
            console.log(`${label}: ${end - start}ms`);
            return result;
        } catch (error) {
            const end = performance.now();
            console.error(`${label} failed after ${end - start}ms:`, error);
            throw error;
        }
    }
};

// Web Vitals monitoring
import { getCLS, getFID, getFCP, getLCP, getTTFB } from 'web-vitals';

function sendToAnalytics(metric) {
    console.log(metric);
    // Send to your analytics service
}

getCLS(sendToAnalytics);
getFID(sendToAnalytics);
getFCP(sendToAnalytics);
getLCP(sendToAnalytics);
getTTFB(sendToAnalytics);
```

---

## 7. Deployment Optimization

### 7.1 Docker Configuration

```dockerfile
# Dockerfile for Laravel
FROM php:8.2-fpm-alpine

# Install dependencies
RUN apk add --no-cache nginx curl

# Install PHP extensions
RUN docker-php-ext-install pdo pdo_mysql opcache redis

# OPcache configuration
RUN echo "opcache.enable=1" >> /usr/local/etc/php/conf.d/opcache.ini
RUN echo "opcache.memory_consumption=256" >> /usr/local/etc/php/conf.d/opcache.ini
RUN echo "opcache.max_accelerated_files=20000" >> /usr/local/etc/php/conf.d/opcache.ini

# Copy application
COPY . /var/www/html
WORKDIR /var/www/html

# Optimize Laravel
RUN composer install --optimize-autoloader --no-dev
RUN php artisan config:cache
RUN php artisan route:cache
RUN php artisan view:cache

# Node.js build stage
FROM node:18-alpine as node-builder
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build

EXPOSE 9000
```

### 7.2 CI/CD Pipeline Optimization

```yaml
# .github/workflows/deploy.yml
name: Deploy
on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup PHP
      uses: shivammathur/setup-php@v2
      with:
        php-version: '8.2'
        extensions: mbstring, bcmath, pdo_mysql
        coverage: none
    
    - name: Cache Composer dependencies
      uses: actions/cache@v3
      with:
        path: vendor
        key: ${{ runner.os }}-composer-${{ hashFiles('**/composer.lock') }}
    
    - name: Install dependencies
      run: composer install --optimize-autoloader --no-dev
    
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
        cache: 'npm'
    
    - name: Build assets
      run: |
        npm ci
        npm run build
    
    - name: Deploy
      run: |
        php artisan config:cache
        php artisan route:cache
        php artisan view:cache
        php artisan migrate --force
```

---

## 8. Performance Checklist

### Backend Checklist
- [ ] Database queries optimized with indexes
- [ ] Eager loading implemented for relationships
- [ ] Redis caching configured
- [ ] Queue system implemented for heavy tasks
- [ ] API resources used for data transformation
- [ ] OPcache enabled
- [ ] Composer autoloader optimized

### Frontend Checklist
- [ ] Components memoized where appropriate
- [ ] Code splitting implemented
- [ ] Images optimized and lazy loaded
- [ ] Bundle size analyzed and optimized
- [ ] React Query or SWR implemented for data fetching
- [ ] Unnecessary re-renders eliminated
- [ ] Production build optimized

### Socket.io Checklist
- [ ] Redis adapter configured for scaling
- [ ] Event handlers optimized and throttled
- [ ] Connection management implemented
- [ ] Namespaces used for organization
- [ ] Message batching implemented
- [ ] Proper error handling and reconnection logic

### Infrastructure Checklist
- [ ] Nginx configured with proper caching headers
- [ ] PHP-FPM optimized
- [ ] Redis configured with appropriate memory policies
- [ ] SSL/TLS properly configured
- [ ] Monitoring and logging implemented
- [ ] CDN configured for static assets

This comprehensive guide should help you optimize every aspect of your Laravel 12 + React + Vite + Socket.io project for maximum performance.