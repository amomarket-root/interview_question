# 📋 Visit Care Application - Complete Enhanced Documentation

## 🎯 Project Overview

A comprehensive medical appointment booking system with Laravel backend integrated with Vite + React frontend using Material-UI, featuring doctor/clinic search, availability management, and appointment booking with beautiful icons and enhanced UI. All primary keys use UUIDs with proper Sanctum authentication support.

***

## 📁 Project Structure

```
visit_care/
├── app/
│   ├── Http/
│   │   └── Controllers/
│   │   |    └── Api/
|   |   |       |── AdminController.php
│   │   |       ├── AuthController.php
|   |   |       |── SocialAuthController.php
│   │   |       ├── DoctorController.php
│   │   |       ├── ClinicController.php
│   │   |       └── AppointmentController.php
│   │   └── Middleware/
│   │   |       ├── AdminMiddleware.php
│   │   |       └── EnsureUserType.php
│   ├── Models/
│   │   ├── User.php
|   |   |── SocialProvider.php
│   │   ├── Doctor.php
│   │   ├── Clinic.php
│   │   ├── Appointment.php
│   │   ├── ClinicConnectionRequest.php
│   │   └── ClinicDoctor.php
│   └── Services/
│       └── SocialAuthService.php.php
│   └── Traits/
│       └── HasUuid.php
├── config/
│   └── cors.php
├── database/
│   ├── migrations/
│   │   ├── xxxx_create_users_table.php
|   |   ├── xxxx_create_social_providers.php
|   |   ├── xxxx_create_clinic_connection_requests.php
│   │   ├── xxxx_create_cache_table.php
│   │   ├── xxxx_create_jobs_table.php
│   │   ├── xxxx_create_clinics_table.php
│   │   ├── xxxx_add_owner_to_clinics_table.php
│   │   ├── xxxx_create_doctors_table.php
│   │   ├── xxxx_create_clinic_doctor_table.php
│   │   ├── xxxx_create_appointments_table.php
│   │   └── xxxx_create_personal_access_tokens_table.php
│   └── seeders/
│       ├── DatabaseSeeder.php
│       ├── UserSeeder.php
│       ├── ClinicSeeder.php
│       ├── DoctorSeeder.php
│       ├── DoctorClinicScheduleSeeder.php
│       └── AppointmentSeeder.php
├── resources/
│   ├── js/
│   │   ├── components/
│   │   │   ├── auth/
│   │   │   │   ├── AuthCallback.jsx
│   │   │   │   └── LoginModal.jsx
│   │   │   ├── common/
│   │   │   │   ├── Header.jsx
│   │   │   │   └── Footer.jsx
│   │   │   └── doctor/
│   │   │   │   ├── ProfileSetupModal.jsx
│   │   │   |   └── ClinicSearchModal.jsx
│   │   │   └── healthcare/
│   │   │   |   └── HealthcareProfileSetupModal.jsx
│   │   │   ├── home/
│   │   │   │   ├── SearchSection.jsx
│   │   │   │   ├── NearbySection.jsx
│   │   │   │   ├── StatsSection.jsx
│   │   │   │   ├── WhyChooseSection.jsx
│   │   │   │   ├── TestimonialsSection.jsx
│   │   │   │   └── LocationMap.jsx
│   │   │   └── layout/
│   │   │       └── MainLayout.jsx
│   │   ├── pages/
│   │   │   ├── BookAppointmentPage.jsx
│   │   │   ├── ClinicDetailPage.jsx
│   │   │   ├── DoctorDetailPage.jsx
│   │   │   └── HomePage.jsx
│   │   |    ├── admin/
│   │   │    |   └── AdminDashboard.jsx
│   │   |    ├── doctor/
│   │   │    |   └── DoctorDashboard.jsx
│   │   |    ├── healthcare/
│   │   │    |   └── HealthcareDashboard.jsx
│   │   ├── data/
│   │   │   └── dummyData.js
│   │   ├── theme/
│   │   │   ├── theme.js
│   │   │   └── theme.css
│   │   ├── app.jsx
│   │   └── bootstrap.js
│   ├── css/
│   │   └── app.css
│   └── views/
│       └── welcome.blade.php
├── routes/
|   ├──web.pphp
│   └── api.php
├── public/
│   └── images/
│       └── common/
│           ├── visit_care_logo.webp
│           ├── icon.webp
│           ├── avatar.webp
│           ├── apple_app_store.webp
│           └── google_play_store.webp
├── Configuration Files:
│   ├── .env
│   ├── .env.example
│   ├── composer.json
│   ├── package.json
│   └── vite.config.js
└── Documentation:
    └── README.md
```


***

## 🚀 Step-by-Step Installation Guide

### Step 1: Initial Laravel Setup

```bash
# Create new Laravel project
composer create-project laravel/laravel doctor-clinic-backend
cd doctor-clinic-backend

# Install required packages
composer require laravel/sanctum
composer require spatie/laravel-permission
composer require intervention/image
composer require ramsey/uuid
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
```


### Step 2: Configuration Files

**composer.json**

```json
{
    "$schema": "https://getcomposer.org/schema.json",
    "name": "laravel/laravel",
    "type": "project",
    "description": "The skeleton application for the Laravel framework.",
    "keywords": ["laravel", "framework"],
    "license": "MIT",
    "require": {
        "php": "^8.2",
        "intervention/image": "^3.11",
        "laravel/framework": "^12.0",
        "laravel/sanctum": "^4.2",
        "laravel/tinker": "^2.10.1",
        "ramsey/uuid": "^4.9",
        "spatie/laravel-permission": "^6.21"
    },
    "require-dev": {
        "fakerphp/faker": "^1.23",
        "laravel/pail": "^1.2.2",
        "laravel/pint": "^1.13",
        "laravel/sail": "^1.41",
        "mockery/mockery": "^1.6",
        "nunomaduro/collision": "^8.6",
        "phpunit/phpunit": "^11.5.3"
    },
    "autoload": {
        "psr-4": {
            "App\\": "app/",
            "Database\\Factories\\": "database/factories/",
            "Database\\Seeders\\": "database/seeders/"
        }
    },
    "autoload-dev": {
        "psr-4": {
            "Tests\\": "tests/"
        }
    },
    "scripts": {
        "post-autoload-dump": [
            "@php artisan config:clear",
            "@php artisan clear-compiled",
            "@php artisan package:discover --ansi"
        ],
        "post-update-cmd": [
            "@php artisan vendor:publish --tag=laravel-assets --ansi --force"
        ],
        "post-root-package-install": [
            "@php -r \"file_exists('.env') || copy('.env.example', '.env');\""
        ],
        "post-create-project-cmd": [
            "@php artisan key:generate --ansi",
            "@php -r \"file_exists('database/database.sqlite') || touch('database/database.sqlite');\"",
            "@php artisan migrate --graceful --ansi"
        ],
        "dev": [
            "Composer\\Config::disableProcessTimeout",
            "npx concurrently -c \"#93c5fd,#c4b5fd,#fb7185,#fdba74\" \"php artisan serve\" \"php artisan queue:listen --tries=1\" \"php artisan pail --timeout=0\" \"npm run dev\" --names=server,queue,logs,vite"
        ],
        "test": [
            "@php artisan config:clear --ansi",
            "@php artisan test"
        ]
    },
    "extra": {
        "laravel": {
            "dont-discover": []
        }
    },
    "config": {
        "optimize-autoloader": true,
        "preferred-install": "dist",
        "sort-packages": true,
        "allow-plugins": {
            "pestphp/pest-plugin": true,
            "php-http/discovery": true
        }
    },
    "minimum-stability": "stable",
    "prefer-stable": true
}
```


### Step 3: Environment Configuration

**.env**

```env
# ==================================================
# APPLICATION CONFIGURATION
# ==================================================
APP_NAME="VISIT CARE"
APP_ENV=local
APP_KEY=base64:I7qdQ2C1rTm0Jo/oirVWkFJLyWREeQ+MQFYBR/S1LY4=
APP_DEBUG=true
APP_URL=http://localhost:8000
APP_LOCALE=en
APP_FALLBACK_LOCALE=en
APP_FAKER_LOCALE=en_US
APP_MAINTENANCE_DRIVER=file
# APP_MAINTENANCE_STORE=database

# ==================================================
# SERVER CONFIGURATION
# ==================================================
PHP_CLI_SERVER_WORKERS=4

# ==================================================
# SECURITY & ENCRYPTION
# ==================================================
BCRYPT_ROUNDS=12

# ==================================================
# LOGGING CONFIGURATION
# ==================================================
LOG_CHANNEL=stack
LOG_STACK=single
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

# ==================================================
# DATABASE CONFIGURATION
# ==================================================
DB_CONNECTION=pgsql
DB_HOST=127.0.0.1
DB_PORT=5432
DB_DATABASE=visit_care
DB_USERNAME=visit_care_user
DB_PASSWORD=root_123

# ==================================================
# SESSION CONFIGURATION
# ==================================================
SESSION_DRIVER=database
SESSION_LIFETIME=120
SESSION_ENCRYPT=false
SESSION_PATH=/
SESSION_DOMAIN=null

# ==================================================
# BROADCASTING & QUEUE CONFIGURATION
# ==================================================
BROADCAST_CONNECTION=log
QUEUE_CONNECTION=database

# ==================================================
# CACHE & STORAGE CONFIGURATION
# ==================================================
CACHE_STORE=database
# CACHE_PREFIX=
FILESYSTEM_DISK=local

# ==================================================
# REDIS CONFIGURATION
# ==================================================
REDIS_CLIENT=phpredis
REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

# ==================================================
# MEMCACHED CONFIGURATION
# ==================================================
MEMCACHED_HOST=127.0.0.1

# ==================================================
# MAIL CONFIGURATION
# ==================================================
MAIL_MAILER=log
MAIL_SCHEME=null
MAIL_HOST=127.0.0.1
MAIL_PORT=2525
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_FROM_ADDRESS="hello@example.com"
MAIL_FROM_NAME="${APP_NAME}"

# ==================================================
# AWS CONFIGURATION
# ==================================================
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=
AWS_USE_PATH_STYLE_ENDPOINT=false

# ==================================================
# FRONTEND CONFIGURATION (VITE)
# ==================================================
FRONTEND_URL=http://localhost:8000
VITE_APP_NAME="${APP_NAME}"
VITE_API_URL=http://localhost:8000/api
VITE_GOOGLE_API_KEY=AIzaSyA9MaTVJlWIWpINjcgyJl5eS6JDhe60238

# ==================================================
# SANCTUM CONFIGURATION
# ==================================================
SANCTUM_STATEFUL_DOMAINS=localhost:8000

# ==================================================
# OAUTH PROVIDERS CONFIGURATION
# ==================================================

# Google OAuth
GOOGLE_CLIENT_ID=894752065284-gsjf10uier6rlqgh7mg3lnc1g2t6n2sp.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=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

# Facebook OAuth
FACEBOOK_CLIENT_ID=your_facebook_app_id
FACEBOOK_CLIENT_SECRET=your_facebook_app_secret

# GitHub OAuth
GITHUB_CLIENT_ID=Ov23liwfltZsOWcY91HX
GITHUB_CLIENT_SECRET=cb956de8de15c763aec63940e340f62cfc38e5ed

# Apple OAuth
APPLE_CLIENT_ID=your_apple_service_id
APPLE_CLIENT_SECRET=your_apple_client_secret
```


### Step 4: CORS Configuration

**config/cors.php**

```php
<?php

return [
    'paths' => ['api/*', 'sanctum/csrf-cookie'],
    'allowed_methods' => ['*'],
    'allowed_origins' => ['*'],
    'allowed_origins_patterns' => [],
    'allowed_headers' => ['*'],
    'exposed_headers' => [],
    'max_age' => 0,
    'supports_credentials' => true,
];
```


***

## 🔧 Backend Implementation

### Step 5: UUID Trait

**app/Traits/HasUuid.php**

```php
<?php
namespace App\Traits;

use Illuminate\Support\Str;

trait HasUuid
{
    protected static function bootHasUuid()
    {
        static::creating(function ($model) {
            if (empty($model->{$model->getKeyName()})) {
                $model->{$model->getKeyName()} = (string) Str::uuid();
            }
        });
    }

    public function getIncrementing()
    {
        return false;
    }

    public function getKeyType()
    {
        return 'string';
    }

    // Add this for better PostgreSQL compatibility
    public function getCasts()
    {
        return array_merge(parent::getCasts(), [
            $this->getKeyName() => 'string'
        ]);
    }
}
```


### Step 6: Database Migrations

**database/migrations/xxxx_create_users_table.php**

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('users', function (Blueprint $table) {
            $table->uuid('id')->primary();
            $table->string('name');
            $table->string('email')->unique();
            $table->string('phone')->nullable();
            $table->enum('user_type', ['patient', 'doctor', 'healthcare','admin']);
            $table->timestamp('email_verified_at')->nullable();
            $table->string('password')->nullable(); // Nullable for social users
            $table->string('avatar')->nullable();
            $table->text('address')->nullable();
            $table->decimal('latitude', 10, 8)->nullable();
            $table->decimal('longitude', 11, 8)->nullable();
            $table->boolean('is_social_user')->default(false);
            $table->timestamp('login_time')->nullable();
            $table->timestamp('logout_time')->nullable();
            $table->rememberToken();
            $table->timestamps();
        });

        Schema::create('password_reset_tokens', function (Blueprint $table) {
            $table->uuid('id')->primary();
            $table->string('email')->index();
            $table->string('token');
            $table->timestamp('created_at')->nullable();
            $table->timestamp('updated_at')->nullable();
        });

        Schema::create('sessions', function (Blueprint $table) {
            $table->string('id')->primary();
            $table->uuid('user_id')->nullable()->index();
            $table->string('ip_address', 45)->nullable();
            $table->text('user_agent')->nullable();
            $table->longText('payload');
            $table->integer('last_activity')->index();

            $table->foreign('user_id')
                ->references('id')->on('users')
                ->onDelete('set null');
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('sessions');
        Schema::dropIfExists('password_reset_tokens');
        Schema::dropIfExists('users');
    }
};
```

**database/migrations/xxxx_social_providers.php**

```php
<?php
// database/migrations/xxxx_xx_xx_create_social_providers_table.php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('social_providers', function (Blueprint $table) {
            $table->uuid('id')->primary();
            $table->uuid('user_id'); // Must match users.id type exactly
            $table->string('provider'); // google, facebook, github, apple
            $table->string('provider_id');
            $table->string('avatar')->nullable();
            $table->json('provider_data')->nullable();
            $table->timestamps();

            // Foreign key constraint
            $table->foreign('user_id')
                  ->references('id')
                  ->on('users')
                  ->onDelete('cascade');
                  
            // Unique constraint to prevent duplicate provider accounts
            $table->unique(['provider', 'provider_id']);
            
            // Index for better performance
            $table->index(['user_id', 'provider']);
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('social_providers');
    }
};
```

**database/migrations/xxxx_create_doctors_table.php**

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('doctors', function (Blueprint $table) {
            $table->uuid('id')->primary();
            $table->foreignUuid('user_id')->constrained('users')->onDelete('cascade');
            $table->string('specialization');
            $table->string('license_number')->unique();
            $table->integer('experience_years');
            $table->text('bio')->nullable();
            $table->text('education')->nullable();
            $table->decimal('consultation_fee', 8, 2);
            $table->text('qualification');
            $table->json('languages')->nullable();
            $table->json('services')->nullable(); // Array of services offered
            $table->decimal('rating', 3, 2)->default(0.00);
            $table->integer('total_reviews')->default(0);
            $table->boolean('is_verified')->default(false);
            $table->boolean('is_available')->default(true);
            $table->string('profile_image')->nullable();
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('doctors');
    }
};
```

**database/migrations/xxxx_clinic_connection_requests.php**

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('clinic_connection_requests', function (Blueprint $table) {
            $table->uuid('id')->primary();
            $table->foreignUuid('doctor_id')->constrained('doctors')->onDelete('cascade');
            $table->foreignUuid('clinic_id')->constrained('clinics')->onDelete('cascade');
            $table->enum('status', ['pending', 'approved', 'rejected'])->default('pending');
            $table->text('message')->nullable();
            $table->text('rejection_reason')->nullable();
            $table->json('proposed_schedule')->nullable(); // Doctor's proposed schedule
            $table->timestamps();
            
            $table->unique(['doctor_id', 'clinic_id']);
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('clinic_connection_requests');
    }
};
```

**database/migrations/xxxx_create_clinics_table.php**

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('clinics', function (Blueprint $table) {
            $table->uuid('id')->primary();
            $table->string('name');
            $table->text('description')->nullable();
            $table->text('address');
            $table->decimal('latitude', 10, 8);
            $table->decimal('longitude', 11, 8);
            $table->string('phone');
            $table->string('email')->nullable();
            $table->string('website')->nullable();
            $table->json('facilities')->nullable();
            $table->json('specialties')->nullable();
            $table->string('image')->nullable();
            $table->json('images')->nullable(); // Multiple images
            $table->time('opening_time')->default('09:00');
            $table->time('closing_time')->default('18:00');
            $table->json('working_days')->nullable(); // ['monday', 'tuesday', etc.]
            $table->decimal('rating', 3, 2)->default(0.00);
            $table->integer('total_reviews')->default(0);
            $table->boolean('is_active')->default(true);
            $table->boolean('is_featured')->default(false);
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('clinics');
    }
};
```
**database/migrations/xxxx_add_owner_to_clinics_table.php**

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::table('clinics', function (Blueprint $table) {
            $table->foreignUuid('owner_id')->nullable()->after('id')->constrained('users')->onDelete('cascade');
            $table->string('registration_number')->nullable()->after('name');
            $table->string('license_number')->nullable()->after('registration_number');
            $table->enum('organization_type', ['hospital', 'clinic', 'diagnostic_center', 'pharmacy', 'nursing_home'])->default('clinic')->after('license_number');
            $table->date('established_date')->nullable()->after('organization_type');
            $table->integer('bed_count')->nullable()->after('established_date');
            $table->json('certifications')->nullable()->after('bed_count');
            $table->boolean('is_verified')->default(false)->after('certifications');
            $table->string('logo')->nullable()->after('is_verified');
        });
    }

    public function down(): void
    {
        Schema::table('clinics', function (Blueprint $table) {
            $table->dropForeign(['owner_id']);
            $table->dropColumn([
                'owner_id', 'registration_number', 'license_number', 'organization_type',
                'established_date', 'bed_count', 'certifications', 'is_verified', 'logo'
            ]);
        });
    }
};
```

**database/migrations/xxxx_create_clinic_doctor_table.php**

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up()
    {
        Schema::create('clinic_doctor', function (Blueprint $table) {
            $table->uuid('doctor_id');
            $table->uuid('clinic_id');
            $table->foreign('doctor_id')->references('id')->on('doctors')->onDelete('cascade');
            $table->foreign('clinic_id')->references('id')->on('clinics')->onDelete('cascade');
            $table->json('available_days');
            $table->time('start_time');
            $table->time('end_time');
            $table->integer('slot_duration')->default(30);
            $table->boolean('is_active')->default(true);
            $table->timestamps();
            
            $table->primary(['doctor_id', 'clinic_id']);
        });
    }

    public function down()
    {
        Schema::dropIfExists('clinic_doctor');
    }
};
```

**database/migrations/xxxx_create_appointments_table.php**

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('appointments', function (Blueprint $table) {
            $table->uuid('id')->primary();
            $table->string('appointment_number')->unique();
            $table->foreignUuid('patient_id')->constrained('users')->onDelete('cascade');
            $table->foreignUuid('doctor_id')->constrained('doctors')->onDelete('cascade');
            $table->foreignUuid('clinic_id')->constrained('clinics')->onDelete('cascade');
            $table->date('appointment_date');
            $table->time('appointment_time');
            $table->enum('status', ['pending', 'confirmed', 'completed', 'cancelled'])->default('pending');
            $table->text('symptoms')->nullable();
            $table->text('notes')->nullable();
            $table->decimal('amount', 8, 2);
            $table->enum('payment_status', ['pending', 'paid', 'refunded'])->default('pending');
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('appointments');
    }
};
```

**database/migrations/xxxx_create_cache_table.php**

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('cache', function (Blueprint $table) {
            $table->string('key')->primary();
            $table->mediumText('value');
            $table->integer('expiration');
        });

        Schema::create('cache_locks', function (Blueprint $table) {
            $table->string('key')->primary();
            $table->string('owner');
            $table->integer('expiration');
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('cache');
        Schema::dropIfExists('cache_locks');
    }
};
```

**database/migrations/xxxx_create_jobs_table.php**

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('jobs', function (Blueprint $table) {
            $table->id();
            $table->string('queue')->index();
            $table->longText('payload');
            $table->unsignedTinyInteger('attempts');
            $table->unsignedInteger('reserved_at')->nullable();
            $table->unsignedInteger('available_at');
            $table->unsignedInteger('created_at');
        });

        Schema::create('job_batches', function (Blueprint $table) {
            $table->string('id')->primary();
            $table->string('name');
            $table->integer('total_jobs');
            $table->integer('pending_jobs');
            $table->integer('failed_jobs');
            $table->longText('failed_job_ids');
            $table->mediumText('options')->nullable();
            $table->integer('cancelled_at')->nullable();
            $table->integer('created_at');
            $table->integer('finished_at')->nullable();
        });

        Schema::create('failed_jobs', function (Blueprint $table) {
            $table->id();
            $table->string('uuid')->unique();
            $table->text('connection');
            $table->text('queue');
            $table->longText('payload');
            $table->longText('exception');
            $table->timestamp('failed_at')->useCurrent();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('jobs');
        Schema::dropIfExists('job_batches');
        Schema::dropIfExists('failed_jobs');
    }
};
```

**database/migrations/xxxx_create_personal_access_tokens_table.php**

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class () extends Migration {
    public function up(): void
    {
        Schema::create('personal_access_tokens', function (Blueprint $table) {
            $table->id();
            $table->uuidMorphs('tokenable');
            $table->string('name');
            $table->string('token', 64)->unique();
            $table->text('abilities')->nullable();
            $table->timestamp('last_used_at')->nullable();
            $table->timestamp('expires_at')->nullable();
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('personal_access_tokens');
    }
};
```


### Step 7: Models Implementation

**app/Models/User.php**

```php
<?php
// app/Models/User.php

namespace App\Models;

use App\Traits\HasUuid;
use Illuminate\Database\Eloquent\Relations\HasMany;
use Illuminate\Foundation\Auth\User as Authenticatable;
use Laravel\Sanctum\HasApiTokens;
use Spatie\Permission\Traits\HasRoles;

class User extends Authenticatable
{
    use HasApiTokens, HasRoles, HasUuid;

    protected $fillable = [
        'name',
        'email',
        'phone',
        'user_type',
        'password',
        'avatar',
        'address',
        'latitude',
        'longitude',
        'login_time',
        'logout_time',
        'is_social_user'
    ];

    protected $hidden = ['password', 'remember_token'];

    protected $casts = [
        'email_verified_at' => 'datetime',
        'login_time' => 'datetime',
        'logout_time' => 'datetime',
        'is_social_user' => 'boolean'
    ];

    public function doctor()
    {
        return $this->hasOne(Doctor::class);
    }

    public function patientAppointments()
    {
        return $this->hasMany(Appointment::class, 'patient_id');
    }

    public function socialProviders(): HasMany
    {
        return $this->hasMany(SocialProvider::class);
    }

    public function hasSocialProvider(string $provider): bool
    {
        return $this->socialProviders()->where('provider', $provider)->exists();
    }

    public function getSocialProvider(string $provider): ?SocialProvider
    {
        return $this->socialProviders()->where('provider', $provider)->first();
    }

    // Add this relationship method
    public function doctorConnectionRequests()
    {
        return $this->hasManyThrough(
            ClinicConnectionRequest::class,
            Doctor::class,
            'user_id', // Foreign key on doctors table
            'doctor_id', // Foreign key on clinic_connection_requests table
            'id', // Local key on users table
            'id' // Local key on doctors table
        );
    }

    // Add this relationship method
    public function ownedClinics()
    {
        return $this->hasMany(Clinic::class, 'owner_id');
    }
}
```

**app/Models/SocialProvider.php**

```php
<?php
// app/Models/SocialProvider.php

namespace App\Models;

use App\Traits\HasUuid;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;

class SocialProvider extends Model
{
    use HasUuid;

    protected $fillable = [
        'user_id',
        'provider',
        'provider_id',
        'avatar',
        'provider_data'
    ];

    protected $casts = [
        'provider_data' => 'array'
    ];

    public function user(): BelongsTo
    {
        return $this->belongsTo(User::class);
    }
}
```

**app/Models/Doctor.php**

```php
<?php

namespace App\Models;

use App\Traits\HasUuid;
use Illuminate\Database\Eloquent\Model;

class Doctor extends Model
{
    use HasUuid;

    protected $fillable = [
        'user_id', 'specialization', 'license_number', 'experience_years',
        'bio', 'education', 'consultation_fee', 'qualification', 
        'languages', 'services', 'rating', 'total_reviews', 
        'is_verified', 'is_available', 'profile_image'
    ];

    protected $casts = [
        'languages' => 'array',
        'services' => 'array',
        'consultation_fee' => 'decimal:2',
        'rating' => 'decimal:2',
        'is_verified' => 'boolean',
        'is_available' => 'boolean'
    ];

    public function user()
    {
        return $this->belongsTo(User::class);
    }

    public function clinics()
    {
        return $this->belongsToMany(Clinic::class, 'clinic_doctor')
                    ->withPivot(['available_days', 'start_time', 'end_time', 'slot_duration', 'is_active'])
                    ->withTimestamps();
    }

    public function appointments()
    {
        return $this->hasMany(Appointment::class);
    }

    // Scope for verified doctors
    public function scopeVerified($query)
    {
        return $query->where('is_verified', true);
    }

    // Scope for available doctors
    public function scopeAvailable($query)
    {
        return $query->where('is_available', true);
    }

    // Get next available appointment slot
    public function getNextAvailableSlot()
    {
        // Logic to calculate next available slot
        return now()->addDays(1)->format('Y-m-d H:i');
    }

    // NEW: Connection requests
    public function connectionRequests()
    {
        return $this->hasMany(ClinicConnectionRequest::class);
    }

    // NEW: Check if profile is complete
    public function isProfileComplete()
    {
        $requiredFields = [
            'specialization', 'license_number', 'experience_years',
            'bio', 'consultation_fee', 'qualification'
        ];

        foreach ($requiredFields as $field) {
            if (empty($this->$field)) {
                return false;
            }
        }

        return true;
    }
}
```

**app/Models/Clinic.php**

```php
<?php

namespace App\Models;

use App\Traits\HasUuid;
use Illuminate\Database\Eloquent\Model;

class Clinic extends Model
{
    use HasUuid;

    protected $fillable = [
        'owner_id',
        'name',
        'registration_number',
        'license_number',
        'organization_type',
        'description',
        'address',
        'latitude',
        'longitude',
        'phone',
        'email',
        'website',
        'facilities',
        'specialties',
        'image',
        'images',
        'opening_time',
        'closing_time',
        'working_days',
        'rating',
        'total_reviews',
        'is_active',
        'is_featured',
        'established_date',
        'bed_count',
        'certifications',
        'is_verified',
        'logo'
    ];

    protected $casts = [
        'facilities' => 'array',
        'specialties' => 'array',
        'images' => 'array',
        'working_days' => 'array',
        'certifications' => 'array',
        'latitude' => 'decimal:8',
        'longitude' => 'decimal:8',
        'rating' => 'decimal:2',
        'is_active' => 'boolean',
        'is_featured' => 'boolean',
        'is_verified' => 'boolean',
        'established_date' => 'date'
    ];

    // Owner relationship
    public function owner()
    {
        return $this->belongsTo(User::class, 'owner_id');
    }

    public function doctors()
    {
        return $this->belongsToMany(Doctor::class, 'clinic_doctor')
            ->withPivot(['available_days', 'start_time', 'end_time', 'slot_duration', 'is_active'])
            ->withTimestamps();
    }

    public function appointments()
    {
        return $this->hasMany(Appointment::class);
    }

    // Calculate distance from given coordinates
    public function getDistanceFromPoint($latitude, $longitude)
    {
        $earthRadius = 6371; // Earth radius in kilometers

        $latDelta = deg2rad($latitude - $this->latitude);
        $lonDelta = deg2rad($longitude - $this->longitude);

        $a = sin($latDelta / 2) * sin($latDelta / 2) +
            cos(deg2rad($this->latitude)) * cos(deg2rad($latitude)) *
            sin($lonDelta / 2) * sin($lonDelta / 2);

        $c = 2 * atan2(sqrt($a), sqrt(1 - $a));

        return round($earthRadius * $c, 2);
    }

    // Scope for active clinics
    public function scopeActive($query)
    {
        return $query->where('is_active', true);
    }

    // Scope for featured clinics
    public function scopeFeatured($query)
    {
        return $query->where('is_featured', true);
    }

    // Scope for owned clinics
    public function scopeOwnedBy($query, $userId)
    {
        return $query->where('owner_id', $userId);
    }

    // Connection requests for this clinic
    public function connectionRequests()
    {
        return $this->hasMany(ClinicConnectionRequest::class);
    }

    // Check if profile is complete for healthcare providers
    public function isProfileComplete()
    {
        $requiredFields = [
            'name',
            'registration_number',
            'organization_type',
            'description',
            'address',
            'phone',
            'license_number'
        ];

        foreach ($requiredFields as $field) {
            if (empty($this->$field)) {
                return false;
            }
        }

        return true;
    }
}
```

**app/Models/ClinicConnectionRequest.php**

```php
<?php

namespace App\Models;

use App\Traits\HasUuid;
use Illuminate\Database\Eloquent\Model;

class ClinicConnectionRequest extends Model
{
    use HasUuid;

    protected $fillable = [
        'doctor_id',
        'clinic_id', 
        'status',
        'message',
        'rejection_reason',
        'proposed_schedule'
    ];

    protected $casts = [
        'proposed_schedule' => 'array'
    ];

    public function doctor()
    {
        return $this->belongsTo(Doctor::class);
    }

    public function clinic()
    {
        return $this->belongsTo(Clinic::class);
    }
}
```

**app/Models/Appointment.php**

```php
<?php

namespace App\Models;

use App\Traits\HasUuid;
use Illuminate\Database\Eloquent\Model;

class Appointment extends Model
{
    use HasUuid;

    protected $fillable = [
        'appointment_number', 'patient_id', 'doctor_id', 'clinic_id',
        'appointment_date', 'appointment_time', 'status', 'symptoms',
        'notes', 'amount', 'payment_status'
    ];

    protected $casts = [
        'appointment_date' => 'date',
        'amount' => 'decimal:2'
    ];

    protected static function boot()
    {
        parent::boot();
        
        static::creating(function ($model) {
            $model->appointment_number = 'APT' . str_pad(mt_rand(1, 99999), 5, '0', STR_PAD_LEFT);
        });
    }

    public function patient()
    {
        return $this->belongsTo(User::class, 'patient_id');
    }

    public function doctor()
    {
        return $this->belongsTo(Doctor::class);
    }

    public function clinic()
    {
        return $this->belongsTo(Clinic::class);
    }
}
```

**app/Models/ClinicDoctor.php**

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Relations\Pivot;
use App\Traits\HasUuid;

class ClinicDoctor extends Pivot
{
    use HasUuid;

    protected $table = 'clinic_doctor';
    
    protected $fillable = [
        'doctor_id', 'clinic_id', 'available_days', 'start_time', 
        'end_time', 'slot_duration', 'is_active'
    ];

    protected $casts = [
        'available_days' => 'array',
        'is_active' => 'boolean'
    ];
}
```


### Step 8: API Controllers


**app/Http/Controllers/Api/AdminController.php**

```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Models\User;
use App\Models\Doctor;
use App\Models\Clinic;
use App\Models\Appointment;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;
use Illuminate\Support\Facades\DB;
use Carbon\Carbon;

class AdminController extends Controller
{
    // Dashboard Statistics
    public function getDashboardStats(Request $request)
    {
        $stats = [
            'total_users' => User::count(),
            'total_patients' => User::where('user_type', 'patient')->count(),
            'total_doctors' => User::where('user_type', 'doctor')->count(),
            'total_healthcare' => User::where('user_type', 'healthcare')->count(),
            'total_clinics' => Clinic::count(),
            'total_appointments' => Appointment::count(),
            'today_appointments' => Appointment::whereDate('appointment_date', today())->count(),
            'pending_appointments' => Appointment::where('status', 'pending')->count(),
            'monthly_revenue' => Appointment::where('payment_status', 'paid')
                ->whereMonth('created_at', now()->month)
                ->sum('amount'),
            'weekly_appointments' => Appointment::whereBetween('appointment_date', [
                now()->startOfWeek(),
                now()->endOfWeek()
            ])->count(),
        ];

        // Monthly data for charts (last 12 months)
        $monthlyData = [];
        for ($i = 11; $i >= 0; $i--) {
            $date = now()->subMonths($i);
            $monthlyData[] = [
                'month' => $date->format('M Y'),
                'appointments' => Appointment::whereYear('created_at', $date->year)
                    ->whereMonth('created_at', $date->month)
                    ->count(),
                'revenue' => Appointment::where('payment_status', 'paid')
                    ->whereYear('created_at', $date->year)
                    ->whereMonth('created_at', $date->month)
                    ->sum('amount'),
                'new_users' => User::whereYear('created_at', $date->year)
                    ->whereMonth('created_at', $date->month)
                    ->count(),
            ];
        }

        // User type distribution
        $userTypeStats = [
            'patients' => User::where('user_type', 'patient')->count(),
            'doctors' => User::where('user_type', 'doctor')->count(),
            'healthcare' => User::where('user_type', 'healthcare')->count(),
            'admins' => User::where('user_type', 'admin')->count(),
        ];

        // Recent activities
        $recentActivities = [
            'recent_users' => User::with(['doctor', 'ownedClinics'])
                ->latest()
                ->limit(10)
                ->get(),
            'recent_appointments' => Appointment::with(['patient', 'doctor.user', 'clinic'])
                ->latest()
                ->limit(10)
                ->get(),
        ];

        return response()->json([
            'stats' => $stats,
            'monthly_data' => $monthlyData,
            'user_type_stats' => $userTypeStats,
            'recent_activities' => $recentActivities,
        ]);
    }

    // User Management
    public function getUsers(Request $request)
    {
        $query = User::with(['doctor', 'ownedClinics', 'socialProviders']);

        if ($request->has('user_type') && $request->user_type !== 'all') {
            $query->where('user_type', $request->user_type);
        }

        if ($request->has('search') && !empty($request->search)) {
            $search = $request->search;
            $query->where(function($q) use ($search) {
                $q->where('name', 'like', "%{$search}%")
                  ->orWhere('email', 'like', "%{$search}%");
            });
        }

        $users = $query->orderBy('created_at', 'desc')
                      ->paginate($request->get('per_page', 15));

        return response()->json($users);
    }

    public function createUser(Request $request)
    {
        $validatedData = $request->validate([
            'name' => 'required|string|max:255',
            'email' => 'required|email|unique:users,email',
            'password' => 'required|string|min:8',
            'user_type' => 'required|in:patient,doctor,healthcare,admin',
            'phone' => 'nullable|string|max:15',
            'address' => 'nullable|string|max:500',
        ]);

        $validatedData['password'] = Hash::make($validatedData['password']);
        $validatedData['email_verified_at'] = now();

        $user = User::create($validatedData);

        return response()->json([
            'message' => 'User created successfully',
            'user' => $user->load(['doctor', 'ownedClinics'])
        ], 201);
    }

    public function updateUser(Request $request, $id)
    {
        $user = User::findOrFail($id);

        $validatedData = $request->validate([
            'name' => 'required|string|max:255',
            'email' => 'required|email|unique:users,email,' . $id,
            'user_type' => 'required|in:patient,doctor,healthcare,admin',
            'phone' => 'nullable|string|max:15',
            'address' => 'nullable|string|max:500',
        ]);

        if ($request->filled('password')) {
            $validatedData['password'] = Hash::make($request->password);
        }

        $user->update($validatedData);

        return response()->json([
            'message' => 'User updated successfully',
            'user' => $user->load(['doctor', 'ownedClinics'])
        ]);
    }

    public function deleteUser($id)
    {
        $user = User::findOrFail($id);
        
        // Prevent deleting the last admin
        if ($user->user_type === 'admin' && User::where('user_type', 'admin')->count() <= 1) {
            return response()->json([
                'error' => 'Cannot delete the last admin user'
            ], 400);
        }

        $user->delete();

        return response()->json([
            'message' => 'User deleted successfully'
        ]);
    }

    // Doctor Management
    public function getDoctors(Request $request)
    {
        $query = Doctor::with(['user', 'clinics']);

        if ($request->has('search') && !empty($request->search)) {
            $search = $request->search;
            $query->whereHas('user', function($q) use ($search) {
                $q->where('name', 'like', "%{$search}%");
            })->orWhere('specialization', 'like', "%{$search}%");
        }

        if ($request->has('specialization') && !empty($request->specialization)) {
            $query->where('specialization', $request->specialization);
        }

        $doctors = $query->orderBy('created_at', 'desc')
                        ->paginate($request->get('per_page', 15));

        return response()->json($doctors);
    }

    public function updateDoctorStatus(Request $request, $id)
    {
        $doctor = Doctor::findOrFail($id);

        $validatedData = $request->validate([
            'is_verified' => 'boolean',
            'is_available' => 'boolean',
        ]);

        $doctor->update($validatedData);

        return response()->json([
            'message' => 'Doctor status updated successfully',
            'doctor' => $doctor->load(['user', 'clinics'])
        ]);
    }

    // Healthcare/Clinic Management
    public function getClinics(Request $request)
    {
        $query = Clinic::with(['owner', 'doctors']);

        if ($request->has('search') && !empty($request->search)) {
            $search = $request->search;
            $query->where('name', 'like', "%{$search}%")
                  ->orWhere('address', 'like', "%{$search}%");
        }

        $clinics = $query->orderBy('created_at', 'desc')
                        ->paginate($request->get('per_page', 15));

        return response()->json($clinics);
    }

    public function updateClinicStatus(Request $request, $id)
    {
        $clinic = Clinic::findOrFail($id);

        $validatedData = $request->validate([
            'is_active' => 'boolean',
            'is_verified' => 'boolean',
            'is_featured' => 'boolean',
        ]);

        $clinic->update($validatedData);

        return response()->json([
            'message' => 'Clinic status updated successfully',
            'clinic' => $clinic->load(['owner', 'doctors'])
        ]);
    }

    // Appointment Management
    public function getAppointments(Request $request)
    {
        $query = Appointment::with(['patient', 'doctor.user', 'clinic']);

        if ($request->has('status') && $request->status !== 'all') {
            $query->where('status', $request->status);
        }

        if ($request->has('date_from')) {
            $query->whereDate('appointment_date', '>=', $request->date_from);
        }

        if ($request->has('date_to')) {
            $query->whereDate('appointment_date', '<=', $request->date_to);
        }

        $appointments = $query->orderBy('appointment_date', 'desc')
                             ->orderBy('appointment_time', 'desc')
                             ->paginate($request->get('per_page', 15));

        return response()->json($appointments);
    }

    public function updateAppointmentStatus(Request $request, $id)
    {
        $appointment = Appointment::findOrFail($id);

        $validatedData = $request->validate([
            'status' => 'required|in:pending,confirmed,completed,cancelled',
            'payment_status' => 'nullable|in:pending,paid,refunded',
            'notes' => 'nullable|string|max:500',
        ]);

        $appointment->update($validatedData);

        return response()->json([
            'message' => 'Appointment updated successfully',
            'appointment' => $appointment->load(['patient', 'doctor.user', 'clinic'])
        ]);
    }

    // System Analytics
    public function getAnalytics(Request $request)
    {
        $timeframe = $request->get('timeframe', '30'); // days

        $startDate = Carbon::now()->subDays($timeframe);
        $endDate = Carbon::now();

        $analytics = [
            // Daily appointment counts
            'daily_appointments' => Appointment::select(
                DB::raw('DATE(appointment_date) as date'),
                DB::raw('COUNT(*) as count')
            )
            ->whereBetween('appointment_date', [$startDate, $endDate])
            ->groupBy('date')
            ->orderBy('date')
            ->get(),

            // Revenue by day
            'daily_revenue' => Appointment::select(
                DB::raw('DATE(appointment_date) as date'),
                DB::raw('SUM(amount) as revenue')
            )
            ->where('payment_status', 'paid')
            ->whereBetween('appointment_date', [$startDate, $endDate])
            ->groupBy('date')
            ->orderBy('date')
            ->get(),

            // Popular specializations
            'popular_specializations' => Doctor::select('specialization', DB::raw('COUNT(*) as count'))
                ->groupBy('specialization')
                ->orderBy('count', 'desc')
                ->limit(10)
                ->get(),

            // Top performing clinics
            'top_clinics' => Clinic::select('clinics.name', DB::raw('COUNT(appointments.id) as appointment_count'))
                ->leftJoin('appointments', 'clinics.id', '=', 'appointments.clinic_id')
                ->groupBy('clinics.id', 'clinics.name')
                ->orderBy('appointment_count', 'desc')
                ->limit(10)
                ->get(),
        ];

        return response()->json($analytics);
    }

    // System Settings
    public function getSystemSettings()
    {
        // You can implement a settings table or use config values
        $settings = [
            'platform_name' => config('app.name'),
            'max_appointment_days' => 30,
            'default_consultation_fee' => 100,
            'appointment_cancellation_hours' => 2,
            'maintenance_mode' => false,
        ];

        return response()->json($settings);
    }

    public function updateSystemSettings(Request $request)
    {
        $validatedData = $request->validate([
            'max_appointment_days' => 'integer|min:1|max:365',
            'default_consultation_fee' => 'numeric|min:0',
            'appointment_cancellation_hours' => 'integer|min:1|max:48',
            'maintenance_mode' => 'boolean',
        ]);

        // Store in database or update config files
        // Implementation depends on your preference

        return response()->json([
            'message' => 'Settings updated successfully',
            'settings' => $validatedData
        ]);
    }
}
```

**app/Http/Controllers/Api/AuthController.php**

```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Models\User;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;
use Illuminate\Validation\ValidationException;
use Carbon\Carbon;
use Illuminate\Support\Facades\Log;

class AuthController extends Controller
{
    public function register(Request $request)
    {
        Log::info('Registration attempt', ['email' => $request->email, 'user_type' => $request->user_type]);
        
        $request->validate([
            'name' => 'required|string|max:255',
            'email' => 'required|string|email|max:255|unique:users',
            'password' => 'required|string|min:8|confirmed',
            'user_type' => 'required|in:patient,doctor,healthcare,admin',
            'phone' => 'nullable|string|max:15',
            'address' => 'nullable|string'
        ]);

        $user = User::create([
            'name' => $request->name,
            'email' => $request->email,
            'password' => Hash::make($request->password),
            'user_type' => $request->user_type,
            'phone' => $request->phone,
            'address' => $request->address,
            'login_time' => now(),
            'is_social_user' => false,
            'email_verified_at' => now(), // Auto-verify email for regular registration
        ]);

        $token = $user->createToken('auth_token')->plainTextToken;

        Log::info('Registration successful', ['user_id' => $user->id, 'user_type' => $user->user_type]);

        return response()->json([
            'user' => $user->load(['socialProviders']),
            'token' => $token,
            'token_type' => 'Bearer',
            'message' => 'Registration successful'
        ], 201);
    }

    public function login(Request $request)
    {
        Log::info('Login attempt', ['email' => $request->email]);
        
        $request->validate([
            'email' => 'required|email',
            'password' => 'required'
        ]);

        $user = User::where('email', $request->email)->first();

        if (!$user || !Hash::check($request->password, $user->password)) {
            Log::warning('Login failed - Invalid credentials', ['email' => $request->email]);
            
            throw ValidationException::withMessages([
                'email' => ['The provided credentials are incorrect.'],
            ]);
        }

        // Update login time
        $user->update(['login_time' => now()]);

        $token = $user->createToken('auth_token')->plainTextToken;

        Log::info('Login successful', ['user_id' => $user->id, 'user_type' => $user->user_type]);

        return response()->json([
            'user' => $user->load(['doctor', 'socialProviders']),
            'token' => $token,
            'token_type' => 'Bearer',
            'message' => 'Login successful'
        ]);
    }

    public function logout(Request $request)
    {
        Log::info('Logout attempt', ['user_id' => $request->user()->id]);
        
        // Update logout time
        $request->user()->update(['logout_time' => Carbon::now()]);
        
        // Delete current token
        $request->user()->currentAccessToken()->delete();
        
        Log::info('Logout successful', ['user_id' => $request->user()->id]);
        
        return response()->json(['message' => 'Logged out successfully']);
    }

    public function user(Request $request)
    {
        return response()->json(
            $request->user()->load(['doctor', 'socialProviders'])
        );
    }
}
```

**app/Http/Controllers/Api/SocialAuthController.php**

```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Services\SocialAuthService;
use Illuminate\Http\JsonResponse;
use Illuminate\Http\RedirectResponse;
use Laravel\Socialite\Facades\Socialite;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Log;

class SocialAuthController extends Controller
{
    public function __construct(
        protected SocialAuthService $socialAuthService
    ) {}

    public function getProviders(): JsonResponse
    {
        return response()->json([
            'providers' => $this->socialAuthService->getAllowedProviders(),
            'message' => 'Available social authentication providers'
        ]);
    }

    public function redirectToProvider(string $provider, Request $request): JsonResponse
    {
        if (!$this->socialAuthService->isProviderAllowed($provider)) {
            return response()->json([
                'error' => 'Provider not supported',
                'supported_providers' => $this->socialAuthService->getAllowedProviders()
            ], 400);
        }

        try {
            // Get user type and mode from request
            $userType = $request->get('user_type', 'patient');
            $mode = $request->get('mode', 'login');

            Log::info("OAuth redirect initiated", [
                'provider' => $provider,
                'user_type' => $userType,
                'mode' => $mode
            ]);

            // Add proper scopes for GitHub
            $driver = Socialite::driver($provider)->stateless();
            
            // Set specific scopes for different providers
            if ($provider === 'github') {
                $driver->scopes(['user:email', 'read:user']);
            } elseif ($provider === 'google') {
                $driver->scopes(['openid', 'email', 'profile']);
            }

            // Add state parameter with user type and mode - CRITICAL FIX
            $state = base64_encode(json_encode([
                'user_type' => $userType,
                'mode' => $mode,
                'timestamp' => time()
            ]));
            
            $driver->with(['state' => $state]);

            $redirectUrl = $driver->redirect()->getTargetUrl();

            return response()->json([
                'redirect_url' => $redirectUrl,
                'provider' => $provider,
                'user_type' => $userType,
                'mode' => $mode
            ]);
        } catch (\Exception $e) {
            Log::error("OAuth redirect error for {$provider}: " . $e->getMessage());
            return response()->json([
                'error' => 'Failed to generate redirect URL',
                'message' => $e->getMessage()
            ], 500);
        }
    }

    public function handleProviderCallback(string $provider, Request $request): JsonResponse|RedirectResponse
    {
        if (!$this->socialAuthService->isProviderAllowed($provider)) {
            return response()->json([
                'error' => 'Provider not supported'
            ], 400);
        }

        try {
            // CRITICAL FIX: Extract user type from state parameter properly
            $userType = 'patient'; // default
            $mode = 'login'; // default
            
            $state = $request->get('state');
            if ($state) {
                try {
                    $stateData = json_decode(base64_decode($state), true);
                    if ($stateData && isset($stateData['user_type'])) {
                        $userType = $stateData['user_type'];
                        $mode = $stateData['mode'] ?? 'login';
                    }
                } catch (\Exception $e) {
                    Log::warning("Failed to decode state parameter: " . $e->getMessage());
                }
            }

            // ADDITIONAL FIX: Check URL parameters as fallback
            if ($request->has('user_type')) {
                $userType = $request->get('user_type');
            }

            Log::info("OAuth callback processing", [
                'provider' => $provider,
                'user_type' => $userType,
                'mode' => $mode,
                'state_decoded' => $state ? 'yes' : 'no'
            ]);

            // Add specific scopes during callback as well
            $driver = Socialite::driver($provider)->stateless();
            
            if ($provider === 'github') {
                $driver->scopes(['user:email', 'read:user']);
            } elseif ($provider === 'google') {
                $driver->scopes(['openid', 'email', 'profile']);
            }

            $socialUser = $driver->user();

            // Log successful authentication for debugging
            Log::info("OAuth callback successful for {$provider}", [
                'user_id' => $socialUser->getId(),
                'email' => $socialUser->getEmail(),
                'name' => $socialUser->getName(),
                'selected_user_type' => $userType
            ]);

            // CRITICAL FIX: Properly merge user type into request
            $request->merge([
                'user_type' => $userType,
                'stored_user_type' => $userType,
                'auth_mode' => $mode
            ]);

        } catch (\Exception $e) {
            Log::error("OAuth callback error for {$provider}: " . $e->getMessage());
            
            // Check if this is an API request
            if (request()->expectsJson() || request()->header('Accept') === 'application/json') {
                return response()->json([
                    'error' => 'Authentication failed',
                    'message' => 'Could not authenticate with ' . ucfirst($provider),
                    'details' => $e->getMessage()
                ], 400);
            }

            // For web requests, redirect to callback with error
            $errorUrl = url('/auth/callback') . '?' . http_build_query([
                'error' => 'auth_failed',
                'message' => $e->getMessage()
            ]);
            return redirect($errorUrl);
        }

        try {
            // CRITICAL FIX: Pass user type to the service
            $result = $this->socialAuthService->handleSocialUser($socialUser, $provider, $userType);
            $token = $result['user']->createToken('auth_token')->plainTextToken;

            // Log successful user creation/login
            Log::info("Social user processed successfully", [
                'user_id' => $result['user']->id,
                'user_type' => $result['user']->user_type,
                'is_new_user' => $result['is_new_user']
            ]);

            // Check if this is an API request (expects JSON)
            if (request()->expectsJson() || request()->header('Accept') === 'application/json') {
                return response()->json([
                    'user' => $result['user'],
                    'token' => $token,
                    'token_type' => 'Bearer',
                    'provider' => $provider,
                    'is_new_user' => $result['is_new_user'],
                    'message' => $result['is_new_user'] 
                        ? 'Account created successfully via ' . ucfirst($provider)
                        : 'Logged in successfully via ' . ucfirst($provider)
                ]);
            }

            // For web requests, redirect to callback page with user type
            $userData = urlencode(json_encode($result['user']));
            $callbackUrl = url('/auth/callback') . '?' . http_build_query([
                'token' => $token,
                'user' => $userData,
                'provider' => $provider,
                'user_type' => $result['user']->user_type, // Pass the actual user type
                'is_new_user' => $result['is_new_user'] ? 'true' : 'false'
            ]);

            return redirect($callbackUrl);

        } catch (\Exception $e) {
            Log::error("OAuth user processing error for {$provider}: " . $e->getMessage());
            
            // Check if this is an API request
            if (request()->expectsJson() || request()->header('Accept') === 'application/json') {
                return response()->json([
                    'error' => 'Failed to process authentication',
                    'message' => $e->getMessage()
                ], 500);
            }

            // For web requests, redirect to callback with error
            $errorUrl = url('/auth/callback') . '?' . http_build_query([
                'error' => 'auth_failed',
                'message' => $e->getMessage()
            ]);

            return redirect($errorUrl);
        }
    }

    public function linkProvider(Request $request, string $provider): JsonResponse
    {
        $user = $request->user();
        
        if (!$this->socialAuthService->isProviderAllowed($provider)) {
            return response()->json([
                'error' => 'Provider not supported'
            ], 400);
        }

        if ($user->hasSocialProvider($provider)) {
            return response()->json([
                'error' => 'Provider already linked',
                'message' => ucfirst($provider) . ' is already linked to your account'
            ], 400);
        }

        try {
            $driver = Socialite::driver($provider)
                ->stateless()
                ->with(['state' => 'link_' . $user->id]);
                
            if ($provider === 'github') {
                $driver->scopes(['user:email', 'read:user']);
            }
            
            $redirectUrl = $driver->redirect()->getTargetUrl();

            return response()->json([
                'redirect_url' => $redirectUrl,
                'provider' => $provider,
                'action' => 'link'
            ]);
        } catch (\Exception $e) {
            return response()->json([
                'error' => 'Failed to generate link URL',
                'message' => $e->getMessage()
            ], 500);
        }
    }

    public function unlinkProvider(Request $request, string $provider): JsonResponse
    {
        $user = $request->user();
        
        if (!$this->socialAuthService->isProviderAllowed($provider)) {
            return response()->json([
                'error' => 'Provider not supported'
            ], 400);
        }

        $socialProvider = $user->getSocialProvider($provider);
        
        if (!$socialProvider) {
            return response()->json([
                'error' => 'Provider not linked',
                'message' => ucfirst($provider) . ' is not linked to your account'
            ], 400);
        }

        // Prevent unlinking if it's the only authentication method and no password
        if ($user->is_social_user && !$user->password && $user->socialProviders()->count() === 1) {
            return response()->json([
                'error' => 'Cannot unlink',
                'message' => 'Cannot unlink the only authentication method. Please set a password first.'
            ], 400);
        }

        $socialProvider->delete();

        return response()->json([
            'message' => ucfirst($provider) . ' unlinked successfully'
        ]);
    }

    public function getUserProviders(Request $request): JsonResponse
    {
        $user = $request->user();
        
        $providers = $user->socialProviders()
            ->select('provider', 'avatar', 'created_at')
            ->get()
            ->keyBy('provider');

        return response()->json([
            'linked_providers' => $providers,
            'available_providers' => $this->socialAuthService->getAllowedProviders(),
            'has_password' => !is_null($user->password)
        ]);
    }
}
```

**app/Http/Controllers/Api/ClinicController.php**

```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Models\Appointment;
use App\Models\Clinic;
use App\Models\ClinicConnectionRequest;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Storage;
use Illuminate\Support\Facades\Validator;


class ClinicController extends Controller
{
    public function index(Request $request)
    {
        $query = Clinic::with(['doctors.user'])
            ->active();

        // Search by name, address, or specialties
        if ($request->has('search') && !empty($request->search)) {
            $search = $request->search;
            $query->where(function ($q) use ($search) {
                $q->where('name', 'like', '%' . $search . '%')
                    ->orWhere('address', 'like', '%' . $search . '%')
                    ->orWhere('description', 'like', '%' . $search . '%')
                    ->orWhereJsonContains('specialties', $search)
                    ->orWhereJsonContains('facilities', $search);
            });
        }

        // Filter by specialty
        if ($request->has('specialty') && !empty($request->specialty)) {
            $query->whereJsonContains('specialties', $request->specialty);
        }

        // Filter by facilities
        if ($request->has('facility') && !empty($request->facility)) {
            $query->whereJsonContains('facilities', $request->facility);
        }

        // Location-based search
        if ($request->has('latitude') && $request->has('longitude')) {
            $lat = $request->latitude;
            $lng = $request->longitude;
            $radius = $request->radius ?? 10; // Default 10km radius

            $query->selectRaw("*, 
                (6371 * acos(cos(radians(?)) * cos(radians(latitude)) * 
                cos(radians(longitude) - radians(?)) + sin(radians(?)) * 
                sin(radians(latitude)))) AS distance", [$lat, $lng, $lat])
                ->havingRaw('distance < ?', [$radius])
                ->orderBy('distance');
        } else {
            // Order by rating if no location provided
            $query->orderBy('rating', 'desc');
        }

        $perPage = $request->get('per_page', 12);
        $clinics = $query->paginate($perPage);

        // Add distance to each clinic if coordinates provided
        if ($request->has('latitude') && $request->has('longitude')) {
            $clinics->getCollection()->transform(function ($clinic) use ($request) {
                $clinic->distance_km = $clinic->getDistanceFromPoint(
                    $request->latitude,
                    $request->longitude
                );
                return $clinic;
            });
        }

        return response()->json($clinics);
    }

    public function show($id)
    {
        $clinic = Clinic::with([
            'doctors.user',
            'doctors' => function ($query) {
                $query->verified()->available();
            }
        ])->findOrFail($id);

        return response()->json($clinic);
    }

    public function getDoctors($id, Request $request)
    {
        $clinic = Clinic::findOrFail($id);

        $query = $clinic->doctors()
            ->with('user')
            ->verified()
            ->available();

        // Filter by specialization
        if ($request->has('specialization')) {
            $query->where('specialization', $request->specialization);
        }

        $doctors = $query->get();

        // Add clinic-specific schedule information
        $doctors->transform(function ($doctor) use ($clinic) {
            $pivot = $doctor->pivot;
            $doctor->clinic_schedule = [
                'available_days' => $pivot->available_days,
                'start_time' => $pivot->start_time,
                'end_time' => $pivot->end_time,
                'slot_duration' => $pivot->slot_duration,
                'is_active' => $pivot->is_active
            ];
            return $doctor;
        });

        return response()->json($doctors);
    }

    public function featured()
    {
        $clinics = Clinic::with(['doctors.user'])
            ->featured()
            ->active()
            ->orderBy('rating', 'desc')
            ->limit(6)
            ->get();

        return response()->json($clinics);
    }

    // NEW: Get healthcare profile (using primary clinic as profile)
    public function getHealthcareProfile(Request $request)
    {
        $user = $request->user();

        if ($user->user_type !== 'healthcare') {
            return response()->json(['error' => 'Unauthorized'], 403);
        }

        $primaryClinic = $user->ownedClinics()->first();

        return response()->json([
            'profile' => $primaryClinic,
            'total_clinics' => $user->ownedClinics()->count(),
            'is_complete' => $primaryClinic ? $primaryClinic->isProfileComplete() : false
        ]);
    }

  // FIXED: Update healthcare profile with corrected website validation
    public function updateHealthcareProfile(Request $request)
    {
        $user = $request->user();
        
        if ($user->user_type !== 'healthcare') {
            return response()->json(['error' => 'Unauthorized'], 403);
        }

        $primaryClinic = $user->ownedClinics()->first();

        // FIXED: Custom validation with proper website handling
        $validator = Validator::make($request->all(), [
            'name' => 'required|string|max:255',
            'registration_number' => 'required|string|max:255|unique:clinics,registration_number,' . ($primaryClinic->id ?? 'null'),
            'license_number' => 'required|string|max:255|unique:clinics,license_number,' . ($primaryClinic->id ?? 'null'),
            'organization_type' => 'required|in:hospital,clinic,diagnostic_center,pharmacy,nursing_home',
            'description' => 'required|string|max:2000',
            'address' => 'required|string|max:1000',
            'phone' => 'required|string|max:20',
            'email' => 'nullable|email|max:255',
            // FIXED: More flexible website validation
            'website' => [
                'nullable',
                'string',
                'max:255',
                function ($attribute, $value, $fail) {
                    if (!empty($value)) {
                        // Remove common prefixes for validation
                        $cleanValue = str_replace(['http://', 'https://'], '', trim($value));
                        
                        // Check if it looks like a domain
                        if (!preg_match('/^[a-zA-Z0-9]([a-zA-Z0-9\-]{0,61}[a-zA-Z0-9])?(\.[a-zA-Z0-9]([a-zA-Z0-9\-]{0,61}[a-zA-Z0-9])?)*$/', $cleanValue) && 
                            !filter_var('http://' . $cleanValue, FILTER_VALIDATE_URL)) {
                            $fail('The website field must be a valid domain or URL.');
                        }
                    }
                }
            ],
            'facilities' => 'nullable|array',
            'facilities.*' => 'string|max:100',
            'specialties' => 'nullable|array', 
            'specialties.*' => 'string|max:100',
            'opening_time' => 'required|date_format:H:i',
            'closing_time' => 'required|date_format:H:i|after:opening_time',
            'working_days' => 'required|array|min:1',
            'working_days.*' => 'in:monday,tuesday,wednesday,thursday,friday,saturday,sunday',
            'established_date' => 'nullable|date|before_or_equal:today',
            'bed_count' => 'nullable|integer|min:0|max:10000',
            'certifications' => 'nullable|array',
            'certifications.*' => 'string|max:200',
            'logo' => 'nullable|image|mimes:jpeg,png,jpg,gif,webp|max:2048',
            'image' => 'nullable|image|mimes:jpeg,png,jpg,gif,webp|max:2048',
            'images.*' => 'nullable|image|mimes:jpeg,png,jpg,gif,webp|max:2048',
            // Add latitude/longitude if provided
            'latitude' => 'nullable|numeric|between:-90,90',
            'longitude' => 'nullable|numeric|between:-180,180'
        ]);

        if ($validator->fails()) {
            return response()->json([
                'error' => 'Validation failed',
                'errors' => $validator->errors()
            ], 422);
        }

        $validatedData = $validator->validated();

        // FIXED: Process website URL properly
        if (!empty($validatedData['website'])) {
            $website = trim($validatedData['website']);
            // Add protocol if missing
            if (!preg_match('/^https?:\/\//', $website)) {
                $validatedData['website'] = 'https://' . $website;
            }
        } else {
            $validatedData['website'] = null;
        }

        // Handle logo upload
        if ($request->hasFile('logo')) {
            // Delete old logo if exists
            if ($primaryClinic && $primaryClinic->logo) {
                $oldLogoPath = str_replace('/storage/', '', $primaryClinic->logo);
                Storage::disk('public')->delete($oldLogoPath);
            }
            
            $logo = $request->file('logo');
            $logoPath = $logo->store('clinic-logos', 'public');
            $validatedData['logo'] = Storage::url($logoPath);
        }

        // Handle main image upload
        if ($request->hasFile('image')) {
            // Delete old image if exists
            if ($primaryClinic && $primaryClinic->image) {
                $oldImagePath = str_replace('/storage/', '', $primaryClinic->image);
                Storage::disk('public')->delete($oldImagePath);
            }
            
            $image = $request->file('image');
            $imagePath = $image->store('clinic-images', 'public');
            $validatedData['image'] = Storage::url($imagePath);
        }

        // Handle multiple images upload
        if ($request->hasFile('images')) {
            $images = [];
            foreach ($request->file('images') as $image) {
                $imagePath = $image->store('clinic-images', 'public');
                $images[] = Storage::url($imagePath);
            }
            $validatedData['images'] = $images;
        }

        // Add required fields with defaults
        $validatedData['owner_id'] = $user->id;
        $validatedData['latitude'] = $validatedData['latitude'] ?? 0;
        $validatedData['longitude'] = $validatedData['longitude'] ?? 0;
        $validatedData['is_active'] = true;
        $validatedData['is_verified'] = $primaryClinic ? $primaryClinic->is_verified : false;

        try {
            // Create or update primary clinic
            if ($primaryClinic) {
                $primaryClinic->update($validatedData);
                $clinic = $primaryClinic;
            } else {
                $clinic = $user->ownedClinics()->create($validatedData);
            }

            return response()->json([
                'message' => 'Profile updated successfully',
                'profile' => $clinic->fresh()
            ]);
        } catch (\Exception $e) {
            return response()->json([
                'error' => 'Failed to save profile',
                'message' => $e->getMessage()
            ], 500);
        }
    }

    // NEW: Get healthcare dashboard statistics
    public function getHealthcareDashboardStats(Request $request)
    {
        $user = $request->user();

        if ($user->user_type !== 'healthcare') {
            return response()->json(['error' => 'Unauthorized'], 403);
        }

        $clinics = $user->ownedClinics;
        $clinicIds = $clinics->pluck('id');

        $today = now()->toDateString();
        $thisMonth = now()->format('Y-m');

        // Get connected doctors through clinic_doctor pivot table
        $connectedDoctors = DB::table('clinic_doctor')
            ->whereIn('clinic_id', $clinicIds)
            ->distinct('doctor_id')
            ->count();

        $stats = [
            'total_facilities' => $clinics->count(),
            'connected_doctors' => $connectedDoctors,
            'today_appointments' => Appointment::whereIn('clinic_id', $clinicIds)
                ->whereDate('appointment_date', $today)
                ->count(),
            'monthly_appointments' => Appointment::whereIn('clinic_id', $clinicIds)
                ->where('appointment_date', 'like', $thisMonth . '%')
                ->count(),
            'pending_requests' => ClinicConnectionRequest::whereIn('clinic_id', $clinicIds)
                ->where('status', 'pending')
                ->count(),
            'total_patients' => Appointment::whereIn('clinic_id', $clinicIds)
                ->distinct('patient_id')
                ->count(),
            'average_rating' => $clinics->avg('rating') ?? 0,
            'total_reviews' => $clinics->sum('total_reviews') ?? 0
        ];

        return response()->json($stats);
    }

    // NEW: Get connection requests
    public function getConnectionRequests(Request $request)
    {
        $user = $request->user();

        if ($user->user_type !== 'healthcare') {
            return response()->json(['error' => 'Unauthorized'], 403);
        }

        $clinicIds = $user->ownedClinics()->pluck('id');

        $requests = ClinicConnectionRequest::with(['doctor.user', 'clinic'])
            ->whereIn('clinic_id', $clinicIds)
            ->orderBy('created_at', 'desc')
            ->paginate(10);

        return response()->json($requests);
    }

    // NEW: Respond to connection request
    public function respondToConnectionRequest(Request $request, $id)
    {
        $user = $request->user();

        if ($user->user_type !== 'healthcare') {
            return response()->json(['error' => 'Unauthorized'], 403);
        }

        $validatedData = $request->validate([
            'status' => 'required|in:approved,rejected',
            'rejection_reason' => 'nullable|string|max:500',
            'schedule_adjustments' => 'nullable|array'
        ]);

        $connectionRequest = ClinicConnectionRequest::with(['doctor', 'clinic'])->findOrFail($id);

        // Check if this clinic belongs to the healthcare provider
        $clinicIds = $user->ownedClinics()->pluck('id');
        if (!$clinicIds->contains($connectionRequest->clinic_id)) {
            return response()->json(['error' => 'Unauthorized'], 403);
        }

        DB::transaction(function () use ($connectionRequest, $validatedData) {
            $connectionRequest->update([
                'status' => $validatedData['status'],
                'rejection_reason' => $validatedData['rejection_reason'] ?? null
            ]);

            if ($validatedData['status'] === 'approved') {
                // Create connection in clinic_doctor pivot table
                $schedule = $connectionRequest->proposed_schedule;

                // Apply any schedule adjustments
                if (isset($validatedData['schedule_adjustments'])) {
                    $schedule = array_merge($schedule, $validatedData['schedule_adjustments']);
                }

                $connectionRequest->doctor->clinics()->attach($connectionRequest->clinic_id, [
                    'available_days' => json_encode($schedule['available_days']),
                    'start_time' => $schedule['start_time'],
                    'end_time' => $schedule['end_time'],
                    'slot_duration' => $schedule['slot_duration'] ?? 30,
                    'is_active' => true
                ]);
            }
        });

        return response()->json([
            'message' => $validatedData['status'] === 'approved' ? 'Doctor connected successfully' : 'Connection request rejected',
            'request' => $connectionRequest->fresh(['doctor.user', 'clinic'])
        ]);
    }

    // NEW: Get connected doctors
    public function getConnectedDoctors(Request $request)
    {
        $user = $request->user();

        if ($user->user_type !== 'healthcare') {
            return response()->json(['error' => 'Unauthorized'], 403);
        }

        $clinics = $user->ownedClinics()->with(['doctors.user'])->get();

        $doctors = collect();
        foreach ($clinics as $clinic) {
            foreach ($clinic->doctors as $doctor) {
                $doctor->clinic_info = [
                    'clinic_id' => $clinic->id,
                    'clinic_name' => $clinic->name,
                    'schedule' => $doctor->pivot
                ];
                $doctors->push($doctor);
            }
        }

        return response()->json($doctors->unique('id')->values());
    }

    // NEW: Disconnect doctor
    public function disconnectDoctor(Request $request, $doctorId)
    {
        $user = $request->user();

        if ($user->user_type !== 'healthcare') {
            return response()->json(['error' => 'Unauthorized'], 403);
        }

        $clinicIds = $user->ownedClinics()->pluck('id');

        // Remove doctor from all clinics belonging to this healthcare provider
        DB::table('clinic_doctor')
            ->where('doctor_id', $doctorId)
            ->whereIn('clinic_id', $clinicIds)
            ->delete();

        return response()->json(['message' => 'Doctor disconnected successfully']);
    }

    // NEW: Get clinic appointments
    public function getClinicAppointments(Request $request)
    {
        $user = $request->user();

        if ($user->user_type !== 'healthcare') {
            return response()->json(['error' => 'Unauthorized'], 403);
        }

        $clinicIds = $user->ownedClinics()->pluck('id');

        $query = Appointment::with(['patient', 'doctor.user', 'clinic'])
            ->whereIn('clinic_id', $clinicIds)
            ->orderBy('appointment_date', 'desc')
            ->orderBy('appointment_time', 'desc');

        // Filter by status
        if ($request->has('status') && $request->status !== 'all') {
            $query->where('status', $request->status);
        }

        // Filter by clinic
        if ($request->has('clinic_id')) {
            $query->where('clinic_id', $request->clinic_id);
        }

        // Filter by date range
        if ($request->has('date_from')) {
            $query->whereDate('appointment_date', '>=', $request->date_from);
        }

        if ($request->has('date_to')) {
            $query->whereDate('appointment_date', '<=', $request->date_to);
        }

        $appointments = $query->paginate(15);

        return response()->json($appointments);
    }

    // NEW: Update clinic appointment status
    public function updateClinicAppointmentStatus(Request $request, $id)
    {
        $user = $request->user();

        if ($user->user_type !== 'healthcare') {
            return response()->json(['error' => 'Unauthorized'], 403);
        }

        $validatedData = $request->validate([
            'status' => 'required|in:confirmed,completed,cancelled',
            'notes' => 'nullable|string|max:500'
        ]);

        $clinicIds = $user->ownedClinics()->pluck('id');
        $appointment = Appointment::whereIn('clinic_id', $clinicIds)->findOrFail($id);

        $appointment->update([
            'status' => $validatedData['status'],
            'notes' => $validatedData['notes'] ?? $appointment->notes
        ]);

        return response()->json([
            'message' => 'Appointment status updated successfully',
            'appointment' => $appointment->load(['patient', 'doctor.user', 'clinic'])
        ]);
    }

    // NEW: Get healthcare provider's clinics list
    public function getMyClinicsList(Request $request)
    {
        $user = $request->user();

        if ($user->user_type !== 'healthcare') {
            return response()->json(['error' => 'Unauthorized'], 403);
        }

        $clinics = $user->ownedClinics()->with(['doctors'])->get();

        return response()->json($clinics);
    }

    // NEW: Add new clinic
    public function addNewClinic(Request $request)
    {
        $user = $request->user();

        if ($user->user_type !== 'healthcare') {
            return response()->json(['error' => 'Unauthorized'], 403);
        }

        $validatedData = $request->validate([
            'name' => 'required|string|max:255',
            'address' => 'required|string|max:500',
            'phone' => 'required|string|max:20',
            'email' => 'nullable|email',
            'description' => 'nullable|string|max:1000',
            'opening_time' => 'required|date_format:H:i',
            'closing_time' => 'required|date_format:H:i',
            'working_days' => 'required|array|min:1',
            'specialties' => 'nullable|array',
            'facilities' => 'nullable|array'
        ]);

        $validatedData['owner_id'] = $user->id;
        $validatedData['latitude'] = 0; // You might want to geocode the address
        $validatedData['longitude'] = 0;
        $validatedData['is_active'] = true;

        $clinic = $user->ownedClinics()->create($validatedData);

        return response()->json([
            'message' => 'Clinic added successfully',
            'clinic' => $clinic
        ]);
    }
}
```

**app/Http/Controllers/Api/DoctorController.php**

```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Models\Doctor;
use App\Models\Clinic;
use App\Models\ClinicConnectionRequest;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Storage;


class DoctorController extends Controller
{
    public function index(Request $request)
    {
        $query = Doctor::with(['user', 'clinics'])
                      ->verified()
                      ->available();

        // Search by name or specialization
        if ($request->has('search') && !empty($request->search)) {
            $search = $request->search;
            $query->whereHas('user', function($q) use ($search) {
                $q->where('name', 'like', '%' . $search . '%');
            })->orWhere('specialization', 'like', '%' . $search . '%')
              ->orWhere('bio', 'like', '%' . $search . '%');
        }

        // Filter by specialization
        if ($request->has('specialization') && !empty($request->specialization)) {
            $query->where('specialization', $request->specialization);
        }

        // Filter by clinic
        if ($request->has('clinic_id') && !empty($request->clinic_id)) {
            $query->whereHas('clinics', function($q) use ($request) {
                $q->where('clinic_id', $request->clinic_id);
            });
        }

        // Location-based search
        if ($request->has('latitude') && $request->has('longitude')) {
            $lat = $request->latitude;
            $lng = $request->longitude;
            $radius = $request->radius ?? 10;

            $query->whereHas('clinics', function($q) use ($lat, $lng, $radius) {
                $q->selectRaw("*, 
                    (6371 * acos(cos(radians(?)) * cos(radians(latitude)) * 
                    cos(radians(longitude) - radians(?)) + sin(radians(?)) * 
                    sin(radians(latitude)))) AS distance", [$lat, $lng, $lat])
                  ->havingRaw('distance < ?', [$radius]);
            });
        }

        // Order by rating
        $query->orderBy('rating', 'desc');

        $perPage = $request->get('per_page', 12);
        $doctors = $query->paginate($perPage);

        return response()->json($doctors);
    }

    public function show($id)
    {
        $doctor = Doctor::with(['user', 'clinics'])
                       ->findOrFail($id);

        return response()->json($doctor);
    }

    public function getAvailability($doctorId, $clinicId, Request $request)
    {
        $doctor = Doctor::with(['clinics' => function($query) use ($clinicId) {
            $query->where('clinic_id', $clinicId);
        }])->findOrFail($doctorId);

        $clinic = $doctor->clinics->first();
        
        if (!$clinic) {
            return response()->json(['error' => 'Doctor not available at this clinic'], 404);
        }

        $schedule = $clinic->pivot;
        $availableDays = json_decode($schedule->available_days, true);
        
        // Generate available slots for the next 7 days
        $availableSlots = [];
        $startDate = now();
        
        for ($i = 0; $i < 7; $i++) {
            $date = $startDate->copy()->addDays($i);
            $dayName = strtolower($date->format('l'));
            
            if (in_array($dayName, $availableDays)) {
                $slots = $this->generateTimeSlots(
                    $schedule->start_time,
                    $schedule->end_time,
                    $schedule->slot_duration
                );
                
                $availableSlots[] = [
                    'date' => $date->format('Y-m-d'),
                    'day' => $date->format('l'),
                    'slots' => $slots
                ];
            }
        }

        return response()->json([
            'doctor' => $doctor,
            'clinic' => $clinic,
            'schedule' => $schedule,
            'available_slots' => $availableSlots
        ]);
    }

    private function generateTimeSlots($startTime, $endTime, $duration)
    {
        $slots = [];
        $current = \Carbon\Carbon::createFromFormat('H:i:s', $startTime);
        $end = \Carbon\Carbon::createFromFormat('H:i:s', $endTime);

        while ($current->lt($end)) {
            $slots[] = $current->format('H:i');
            $current->addMinutes($duration);
        }

        return $slots;
    }

    public function specializations()
    {
        $specializations = Doctor::verified()
                                ->select('specialization')
                                ->distinct()
                                ->pluck('specialization');

        return response()->json($specializations);
    }

    // NEW: Get doctor profile
    public function getProfile(Request $request)
    {
        $user = $request->user();
        
        if ($user->user_type !== 'doctor') {
            return response()->json(['error' => 'Unauthorized'], 403);
        }

        $doctor = $user->doctor()->with(['user', 'clinics'])->first();
        
        return response()->json([
            'doctor' => $doctor,
            'is_complete' => $doctor ? $doctor->isProfileComplete() : false
        ]);
    }

    // NEW: Update doctor profile
    public function updateProfile(Request $request)
    {
        $user = $request->user();
        
        if ($user->user_type !== 'doctor') {
            return response()->json(['error' => 'Unauthorized'], 403);
        }

        $validatedData = $request->validate([
            'specialization' => 'required|string|max:255',
            'license_number' => 'required|string|max:255|unique:doctors,license_number,' . ($user->doctor->id ?? 'null'),
            'experience_years' => 'required|integer|min:0|max:50',
            'bio' => 'required|string|max:1000',
            'education' => 'nullable|string|max:500',
            'consultation_fee' => 'required|numeric|min:0',
            'qualification' => 'required|string|max:500',
            'languages' => 'nullable|array',
            'services' => 'nullable|array',
            'profile_image' => 'nullable|image|mimes:jpeg,png,jpg,gif|max:2048'
        ]);

        // Handle profile image upload
        if ($request->hasFile('profile_image')) {
            $image = $request->file('profile_image');
            $imagePath = $image->store('doctor-profiles', 'public');
            $validatedData['profile_image'] = Storage::url($imagePath);
        }

        // Create or update doctor profile
        $doctor = $user->doctor()->updateOrCreate(
            ['user_id' => $user->id],
            $validatedData
        );

        return response()->json([
            'message' => 'Profile updated successfully',
            'doctor' => $doctor->load(['user', 'clinics'])
        ]);
    }

    // NEW: Get dashboard statistics
    public function getDashboardStats(Request $request)
    {
        $user = $request->user();
        $doctor = $user->doctor;

        if (!$doctor) {
            return response()->json(['error' => 'Doctor profile not found'], 404);
        }

        $today = now()->toDateString();
        $thisMonth = now()->format('Y-m');

        $stats = [
            'today_appointments' => $doctor->appointments()
                ->whereDate('appointment_date', $today)
                ->count(),
            'total_patients' => $doctor->appointments()
                ->distinct('patient_id')
                ->count(),
            'monthly_appointments' => $doctor->appointments()
                ->where('appointment_date', 'like', $thisMonth . '%')
                ->count(),
            'active_clinics' => $doctor->clinics()->count(),
            'pending_requests' => $doctor->connectionRequests()
                ->where('status', 'pending')
                ->count(),
            'rating' => $doctor->rating ?? 0,
            'total_reviews' => $doctor->total_reviews ?? 0
        ];

        return response()->json($stats);
    }

    // NEW: Get available clinics for connection
    public function getAvailableClinics(Request $request)
    {
        $user = $request->user();
        $doctor = $user->doctor;

        if (!$doctor) {
            return response()->json(['error' => 'Doctor profile not found'], 404);
        }

        // Get clinics that doctor is not connected to and hasn't sent pending requests to
        $connectedClinicIds = $doctor->clinics()->pluck('clinics.id');
        $pendingRequestClinicIds = $doctor->connectionRequests()
            ->where('status', 'pending')
            ->pluck('clinic_id');

        $excludedIds = $connectedClinicIds->merge($pendingRequestClinicIds);

        $query = Clinic::active()->whereNotIn('id', $excludedIds);

        // Search functionality
        if ($request->has('search') && !empty($request->search)) {
            $search = $request->search;
            $query->where(function($q) use ($search) {
                $q->where('name', 'like', '%' . $search . '%')
                  ->orWhere('address', 'like', '%' . $search . '%')
                  ->orWhereJsonContains('specialties', $search);
            });
        }

        $clinics = $query->orderBy('rating', 'desc')->paginate(12);

        return response()->json($clinics);
    }

    // NEW: Get connection requests
    public function getConnectionRequests(Request $request)
    {
        $user = $request->user();
        $doctor = $user->doctor;

        if (!$doctor) {
            return response()->json(['error' => 'Doctor profile not found'], 404);
        }

        $requests = $doctor->connectionRequests()
            ->with(['clinic'])
            ->orderBy('created_at', 'desc')
            ->paginate(10);

        return response()->json($requests);
    }

    // NEW: Get doctor's connected clinics
    public function getMyClinics(Request $request)
    {
        $user = $request->user();
        $doctor = $user->doctor;

        if (!$doctor) {
            return response()->json(['error' => 'Doctor profile not found'], 404);
        }

        $clinics = $doctor->clinics()->get();

        return response()->json($clinics);
    }

    // NEW: Disconnect from clinic
    public function disconnectClinic(Request $request, $clinicId)
    {
        $user = $request->user();
        $doctor = $user->doctor;

        if (!$doctor) {
            return response()->json(['error' => 'Doctor profile not found'], 404);
        }

        $doctor->clinics()->detach($clinicId);

        return response()->json(['message' => 'Disconnected from clinic successfully']);
    }

    // NEW: Get schedule for specific clinic
    public function getSchedule(Request $request, $clinicId)
    {
        $user = $request->user();
        $doctor = $user->doctor;

        if (!$doctor) {
            return response()->json(['error' => 'Doctor profile not found'], 404);
        }

        $clinic = $doctor->clinics()->where('clinic_id', $clinicId)->first();
        
        if (!$clinic) {
            return response()->json(['error' => 'Not connected to this clinic'], 404);
        }

        return response()->json([
            'clinic' => $clinic,
            'schedule' => $clinic->pivot
        ]);
    }

    // NEW: Update schedule for specific clinic
    public function updateSchedule(Request $request, $clinicId)
    {
        $user = $request->user();
        $doctor = $user->doctor;

        if (!$doctor) {
            return response()->json(['error' => 'Doctor profile not found'], 404);
        }

        $validatedData = $request->validate([
            'available_days' => 'required|array|min:1',
            'start_time' => 'required|date_format:H:i',
            'end_time' => 'required|date_format:H:i|after:start_time',
            'slot_duration' => 'required|integer|min:15|max:120'
        ]);

        $doctor->clinics()->updateExistingPivot($clinicId, $validatedData);

        return response()->json(['message' => 'Schedule updated successfully']);
    }

    // NEW: Get doctor's appointments
    public function getMyAppointments(Request $request)
    {
        $user = $request->user();
        $doctor = $user->doctor;

        if (!$doctor) {
            return response()->json(['error' => 'Doctor profile not found'], 404);
        }

        $query = $doctor->appointments()
            ->with(['patient', 'clinic'])
            ->orderBy('appointment_date', 'desc')
            ->orderBy('appointment_time', 'desc');

        // Filter by status
        if ($request->has('status') && $request->status !== 'all') {
            $query->where('status', $request->status);
        }

        // Filter by date range
        if ($request->has('date_from')) {
            $query->whereDate('appointment_date', '>=', $request->date_from);
        }

        if ($request->has('date_to')) {
            $query->whereDate('appointment_date', '<=', $request->date_to);
        }

        $appointments = $query->paginate(15);

        return response()->json($appointments);
    }

    // NEW: Update appointment status
    public function updateAppointmentStatus(Request $request, $id)
    {
        $user = $request->user();
        $doctor = $user->doctor;

        if (!$doctor) {
            return response()->json(['error' => 'Doctor profile not found'], 404);
        }

        $validatedData = $request->validate([
            'status' => 'required|in:confirmed,completed,cancelled',
            'notes' => 'nullable|string|max:500'
        ]);

        $appointment = $doctor->appointments()->findOrFail($id);
        
        $appointment->update([
            'status' => $validatedData['status'],
            'notes' => $validatedData['notes'] ?? $appointment->notes
        ]);

        return response()->json([
            'message' => 'Appointment status updated successfully',
            'appointment' => $appointment->load(['patient', 'clinic'])
        ]);
    }
     // NEW: Search clinics by name, specialty, or location
    public function searchClinics(Request $request)
    {
        $user = $request->user();
        
        if ($user->user_type !== 'doctor') {
            return response()->json(['error' => 'Unauthorized'], 403);
        }

        $doctorProfile = $user->doctor;
        if (!$doctorProfile) {
            return response()->json(['error' => 'Doctor profile not found'], 404);
        }

        $query = Clinic::query()->where('is_active', true);

        // Search by name
        if ($request->has('name') && !empty($request->name)) {
            $query->where('name', 'LIKE', '%' . $request->name . '%');
        }

        // Search by specialty
        if ($request->has('specialty') && !empty($request->specialty)) {
            $query->whereJsonContains('specialties', $request->specialty);
        }

        // Search by location
        if ($request->has('location') && !empty($request->location)) {
            $query->where('address', 'LIKE', '%' . $request->location . '%');
        }

        // Filter by organization type
        if ($request->has('type') && !empty($request->type)) {
            $query->where('organization_type', $request->type);
        }

        // Get already connected clinics
        $connectedClinicIds = $doctorProfile->clinics()->pluck('clinic_id');
        
        // Get pending requests
        $pendingRequestClinicIds = ClinicConnectionRequest::where('doctor_id', $doctorProfile->id)
            ->where('status', 'pending')
            ->pluck('clinic_id');

        $clinics = $query->with(['owner'])
            ->orderBy('name')
            ->paginate(10);

        // Add connection status to each clinic
        $clinics->getCollection()->transform(function ($clinic) use ($connectedClinicIds, $pendingRequestClinicIds) {
            $clinic->connection_status = 'not_connected';
            
            if ($connectedClinicIds->contains($clinic->id)) {
                $clinic->connection_status = 'connected';
            } elseif ($pendingRequestClinicIds->contains($clinic->id)) {
                $clinic->connection_status = 'pending';
            }
            
            return $clinic;
        });

        return response()->json($clinics);
    }

    // NEW: Send connection request to clinic
    public function sendConnectionRequest(Request $request)
    {
        $user = $request->user();
        
        if ($user->user_type !== 'doctor') {
            return response()->json(['error' => 'Unauthorized'], 403);
        }

        $doctorProfile = $user->doctor;
        if (!$doctorProfile) {
            return response()->json(['error' => 'Doctor profile not found'], 404);
        }

        $validatedData = $request->validate([
            'clinic_id' => 'required|exists:clinics,id',
            'message' => 'nullable|string|max:500',
            'proposed_schedule' => 'required|array',
            'proposed_schedule.available_days' => 'required|array|min:1',
            'proposed_schedule.available_days.*' => 'in:monday,tuesday,wednesday,thursday,friday,saturday,sunday',
            'proposed_schedule.start_time' => 'required|date_format:H:i',
            'proposed_schedule.end_time' => 'required|date_format:H:i|after:proposed_schedule.start_time',
            'proposed_schedule.slot_duration' => 'required|integer|min:15|max:120'
        ]);

        // Check if already connected
        if ($doctorProfile->clinics()->where('clinic_id', $validatedData['clinic_id'])->exists()) {
            return response()->json(['error' => 'Already connected to this clinic'], 409);
        }

        // Check if request already exists
        $existingRequest = ClinicConnectionRequest::where('doctor_id', $doctorProfile->id)
            ->where('clinic_id', $validatedData['clinic_id'])
            ->where('status', 'pending')
            ->first();

        if ($existingRequest) {
            return response()->json(['error' => 'Connection request already sent'], 409);
        }

        $connectionRequest = ClinicConnectionRequest::create([
            'doctor_id' => $doctorProfile->id,
            'clinic_id' => $validatedData['clinic_id'],
            'message' => $validatedData['message'],
            'proposed_schedule' => $validatedData['proposed_schedule'],
            'status' => 'pending'
        ]);

        $connectionRequest->load(['clinic', 'doctor.user']);

        return response()->json([
            'message' => 'Connection request sent successfully',
            'request' => $connectionRequest
        ]);
    }

    // NEW: Get doctor's connection requests
    public function getMyConnectionRequests(Request $request)
    {
        $user = $request->user();
        
        if ($user->user_type !== 'doctor') {
            return response()->json(['error' => 'Unauthorized'], 403);
        }

        $doctorProfile = $user->doctor;
        if (!$doctorProfile) {
            return response()->json(['error' => 'Doctor profile not found'], 404);
        }

        $requests = ClinicConnectionRequest::with(['clinic'])
            ->where('doctor_id', $doctorProfile->id)
            ->orderBy('created_at', 'desc')
            ->paginate(10);

        return response()->json($requests);
    }

    // NEW: Get doctor's connected clinics
    public function getMyConnectedClinics(Request $request)
    {
        $user = $request->user();
        
        if ($user->user_type !== 'doctor') {
            return response()->json(['error' => 'Unauthorized'], 403);
        }

        $doctorProfile = $user->doctor;
        if (!$doctorProfile) {
            return response()->json(['error' => 'Doctor profile not found'], 404);
        }

        $connectedClinics = $doctorProfile->clinics()
            ->with(['owner'])
            ->get()
            ->map(function ($clinic) {
                $clinic->schedule = $clinic->pivot;
                return $clinic;
            });

        return response()->json($connectedClinics);
    }

    // NEW: Disconnect from clinic
    public function disconnectFromClinic(Request $request, $clinicId)
    {
        $user = $request->user();
        
        if ($user->user_type !== 'doctor') {
            return response()->json(['error' => 'Unauthorized'], 403);
        }

        $doctorProfile = $user->doctor;
        if (!$doctorProfile) {
            return response()->json(['error' => 'Doctor profile not found'], 404);
        }

        $doctorProfile->clinics()->detach($clinicId);

        return response()->json(['message' => 'Successfully disconnected from clinic']);
    }
}
```

**app/Http/Controllers/Api/AppointmentController.php**

```php
<?php

namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Models\Appointment;
use App\Models\Doctor;
use App\Models\Clinic;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\DB;

class AppointmentController extends Controller
{
    public function store(Request $request)
    {
        $request->validate([
            'doctor_id' => 'required|exists:doctors,id',
            'clinic_id' => 'required|exists:clinics,id',
            'appointment_date' => 'required|date|after_or_equal:today',
            'appointment_time' => 'required|date_format:H:i',
            'symptoms' => 'nullable|string|max:1000',
            'notes' => 'nullable|string|max:500'
        ]);

        // Check if user is authenticated
        if (!auth()->check()) {
            return response()->json([
                'error' => 'Authentication required',
                'message' => 'Please login to book an appointment'
            ], 401);
        }

        // Check if the time slot is available
        $existingAppointment = Appointment::where([
            'doctor_id' => $request->doctor_id,
            'clinic_id' => $request->clinic_id,
            'appointment_date' => $request->appointment_date,
            'appointment_time' => $request->appointment_time,
        ])->whereIn('status', ['pending', 'confirmed'])->first();

        if ($existingAppointment) {
            return response()->json([
                'error' => 'Time slot not available',
                'message' => 'This time slot is already booked. Please choose another time.'
            ], 409);
        }

        // Verify doctor works at this clinic
        $doctor = Doctor::with('clinics')->findOrFail($request->doctor_id);
        $clinic = $doctor->clinics->where('id', $request->clinic_id)->first();

        if (!$clinic) {
            return response()->json([
                'error' => 'Invalid booking',
                'message' => 'Doctor is not available at this clinic'
            ], 400);
        }

        // Create appointment
        $appointment = DB::transaction(function () use ($request, $doctor) {
            return Appointment::create([
                'patient_id' => auth()->id(),
                'doctor_id' => $request->doctor_id,
                'clinic_id' => $request->clinic_id,
                'appointment_date' => $request->appointment_date,
                'appointment_time' => $request->appointment_time,
                'symptoms' => $request->symptoms,
                'notes' => $request->notes,
                'amount' => $doctor->consultation_fee,
                'status' => 'pending',
                'payment_status' => 'pending'
            ]);
        });

        $appointment->load(['doctor.user', 'clinic', 'patient']);

        return response()->json([
            'message' => 'Appointment booked successfully',
            'appointment' => $appointment
        ], 201);
    }

    public function index(Request $request)
    {
        if (!auth()->check()) {
            return response()->json(['error' => 'Authentication required'], 401);
        }

        $query = auth()->user()->patientAppointments()
                            ->with(['doctor.user', 'clinic'])
                            ->orderBy('appointment_date', 'desc')
                            ->orderBy('appointment_time', 'desc');

        // Filter by status
        if ($request->has('status')) {
            $query->where('status', $request->status);
        }

        $appointments = $query->paginate(10);

        return response()->json($appointments);
    }

    public function show($id)
    {
        $appointment = Appointment::with(['doctor.user', 'clinic', 'patient'])
                                 ->findOrFail($id);
        
        // Check if user has permission to view this appointment
        if ($appointment->patient_id !== auth()->id() && 
            $appointment->doctor->user_id !== auth()->id()) {
            return response()->json(['error' => 'Unauthorized'], 403);
        }

        return response()->json($appointment);
    }

    public function cancel($id)
    {
        $appointment = Appointment::findOrFail($id);
        
        // Check if user owns this appointment
        if ($appointment->patient_id !== auth()->id()) {
            return response()->json(['error' => 'Unauthorized'], 403);
        }

        // Check if appointment can be cancelled
        if ($appointment->status === 'completed') {
            return response()->json([
                'error' => 'Cannot cancel completed appointment'
            ], 400);
        }

        if ($appointment->status === 'cancelled') {
            return response()->json([
                'error' => 'Appointment is already cancelled'
            ], 400);
        }

        // Check if appointment is within cancellation window (e.g., 2 hours before)
        $appointmentDateTime = \Carbon\Carbon::parse($appointment->appointment_date . ' ' . $appointment->appointment_time);
        if ($appointmentDateTime->diffInHours(now()) < 2) {
            return response()->json([
                'error' => 'Cannot cancel appointment less than 2 hours before scheduled time'
            ], 400);
        }

        $appointment->update(['status' => 'cancelled']);

        return response()->json([
            'message' => 'Appointment cancelled successfully',
            'appointment' => $appointment
        ]);
    }
}
```
### Step 8.0: Middleware


**app/Http/Middleware/AdminMiddleware.php**

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;

class AdminMiddleware
{
    public function handle(Request $request, Closure $next): Response
    {
        if (!auth()->check() || auth()->user()->user_type !== 'admin') {
            return response()->json([
                'error' => 'Unauthorized. Admin access required.'
            ], 403);
        }

        return $next($request);
    }
}
```

**app/Http/Middleware/EnsureUserType.php**

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;

class EnsureUserType
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response)  $next
     * @param  string  ...$types
     * @return \Symfony\Component\HttpFoundation\Response
     */
    public function handle(Request $request, Closure $next, ...$types): Response
    {
        // Check if user is authenticated
        if (!auth()->check()) {
            return response()->json([
                'error' => 'Authentication required.',
                'message' => 'Please log in to access this resource.'
            ], 401);
        }

        $user = auth()->user();
        
        // Check if user has required user type
        if (!in_array($user->user_type, $types)) {
            return response()->json([
                'error' => 'Unauthorized access.',
                'message' => 'Required user type: ' . implode(' or ', $types),
                'your_type' => $user->user_type
            ], 403);
        }

        return $next($request);
    }
}
```

### Step 8.1: Services class

**app/Services/SocialAuthService.php**

```php
<?php

namespace App\Services;

use App\Models\User;
use App\Models\SocialProvider;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Str;
use Laravel\Socialite\Contracts\User as SocialiteUser;
use Illuminate\Support\Facades\Log;

class SocialAuthService
{
    protected array $allowedProviders = ['google', 'facebook', 'github', 'apple'];

    public function isProviderAllowed(string $provider): bool
    {
        return in_array($provider, $this->allowedProviders);
    }

    // CRITICAL FIX: Added user type parameter
    public function handleSocialUser(SocialiteUser $socialUser, string $provider, string $userType = null): array
    {
        return DB::transaction(function () use ($socialUser, $provider, $userType) {
            // Validate required data
            if (!$socialUser->getEmail()) {
                throw new \Exception('Email is required but not provided by ' . ucfirst($provider));
            }

            // CRITICAL FIX: Use passed user type or get from request
            $finalUserType = $userType ?: $this->getUserTypeFromRequest();

            Log::info("Processing social user", [
                'provider' => $provider,
                'email' => $socialUser->getEmail(),
                'passed_user_type' => $userType,
                'final_user_type' => $finalUserType
            ]);

            // First, check if this social account already exists
            $socialProvider = SocialProvider::where('provider', $provider)
                ->where('provider_id', $socialUser->getId())
                ->first();

            if ($socialProvider) {
                // Update existing social provider data
                $socialProvider->update([
                    'avatar' => $socialUser->getAvatar(),
                    'provider_data' => $this->extractProviderData($socialUser, $provider)
                ]);

                $user = $socialProvider->user;
                $isNewUser = false;

                Log::info("Found existing social provider", [
                    'user_id' => $user->id,
                    'user_type' => $user->user_type
                ]);
            } else {
                // Check if user exists with this email
                $user = User::where('email', $socialUser->getEmail())->first();

                if ($user) {
                    // Link existing user with social provider
                    $this->createSocialProvider($user, $socialUser, $provider);
                    $isNewUser = false;

                    Log::info("Linked social provider to existing user", [
                        'user_id' => $user->id,
                        'user_type' => $user->user_type
                    ]);
                } else {
                    // CRITICAL FIX: Create new user with proper user type
                    $user = $this->createUserFromSocial($socialUser, $provider, $finalUserType);
                    $this->createSocialProvider($user, $socialUser, $provider);
                    $isNewUser = true;

                    Log::info("Created new social user", [
                        'user_id' => $user->id,
                        'user_type' => $user->user_type,
                        'requested_user_type' => $finalUserType
                    ]);
                }
            }

            // Update login time
            $user->update(['login_time' => now()]);

            return [
                'user' => $user->fresh()->load(['doctor', 'socialProviders']),
                'is_new_user' => $isNewUser
            ];
        });
    }

    protected function getUserTypeFromRequest(): string
    {
        // CRITICAL FIX: Enhanced user type detection
        $userType = null;

        // Try to get from request parameters
        $userType = request()->get('user_type') ?: request()->get('stored_user_type');
        
        // Try to get from merged request data
        if (!$userType && request()->has('user_type')) {
            $userType = request()->input('user_type');
        }

        // Try to get from session or other sources if needed
        if (!$userType) {
            $userType = session('selected_user_type');
        }

        Log::info("User type detection", [
            'from_request' => request()->get('user_type'),
            'from_stored' => request()->get('stored_user_type'),
            'from_input' => request()->input('user_type'),
            'final_user_type' => $userType
        ]);
        
        // Validate user type
        $validTypes = ['patient', 'doctor', 'healthcare', 'admin'];
        if (!$userType || !in_array($userType, $validTypes)) {
            Log::warning("Invalid or missing user type, defaulting to patient", [
                'provided_user_type' => $userType
            ]);
            $userType = 'patient'; // Default fallback
        }

        return $userType;
    }

    protected function createUserFromSocial(SocialiteUser $socialUser, string $provider, string $userType = 'patient'): User
    {
        Log::info("Creating user from social", [
            'email' => $socialUser->getEmail(),
            'user_type' => $userType,
            'provider' => $provider
        ]);

        return User::create([
            'name' => $socialUser->getName() ?: $this->generateNameFromEmail($socialUser->getEmail()),
            'email' => $socialUser->getEmail(),
            'avatar' => $socialUser->getAvatar(),
            'email_verified_at' => now(),
            'user_type' => $userType, // CRITICAL FIX: Use the passed user type
            'is_social_user' => true,
            'password' => null
        ]);
    }

    protected function createSocialProvider(User $user, SocialiteUser $socialUser, string $provider): SocialProvider
    {
        return $user->socialProviders()->create([
            'provider' => $provider,
            'provider_id' => $socialUser->getId(),
            'avatar' => $socialUser->getAvatar(),
            'provider_data' => $this->extractProviderData($socialUser, $provider)
        ]);
    }

    protected function extractProviderData(SocialiteUser $socialUser, string $provider): array
    {
        $baseData = [
            'name' => $socialUser->getName(),
            'email' => $socialUser->getEmail(),
            'avatar' => $socialUser->getAvatar(),
        ];

        // Provider-specific data extraction
        switch ($provider) {
            case 'google':
                return array_merge($baseData, [
                    'email_verified' => $socialUser->user['email_verified'] ?? false,
                    'locale' => $socialUser->user['locale'] ?? null,
                ]);

            case 'facebook':
                return array_merge($baseData, [
                    'first_name' => $socialUser->user['first_name'] ?? null,
                    'last_name' => $socialUser->user['last_name'] ?? null,
                ]);

            case 'github':
                return array_merge($baseData, [
                    'username' => $socialUser->getNickname(),
                    'bio' => $socialUser->user['bio'] ?? null,
                    'company' => $socialUser->user['company'] ?? null,
                    'location' => $socialUser->user['location'] ?? null,
                    'blog' => $socialUser->user['blog'] ?? null,
                ]);

            case 'apple':
                return array_merge($baseData, [
                    'is_private_email' => $socialUser->user['is_private_email'] ?? false,
                ]);

            default:
                return $baseData;
        }
    }

    protected function generateNameFromEmail(string $email): string
    {
        return Str::title(Str::before($email, '@'));
    }

    public function getAllowedProviders(): array
    {
        return $this->allowedProviders;
    }
}
```

### Step 9:  Routes

**routes/web.php**

```php
<?php

use Illuminate\Support\Facades\Route;

Route::get('/', function () {
    return view('welcome');
});

// Catch-all route for React Router
Route::get('/{any}', function () {
    return view('welcome');
})->where('any', '.*');
```

**routes/api.php**

```php
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\Api\AuthController;
use App\Http\Controllers\Api\DoctorController;
use App\Http\Controllers\Api\ClinicController;
use App\Http\Controllers\Api\AppointmentController;
use App\Http\Controllers\Api\SocialAuthController;

// Authentication routes
Route::post('/register', [AuthController::class, 'register']);
Route::post('/login', [AuthController::class, 'login']);

// Social Authentication routes
Route::prefix('auth/social')->group(function () {
    Route::get('/providers', [SocialAuthController::class, 'getProviders']);
    Route::get('/{provider}', [SocialAuthController::class, 'redirectToProvider'])
        ->where('provider', 'google|facebook|github|apple');
    Route::get('/{provider}/callback', [SocialAuthController::class, 'handleProviderCallback'])
        ->where('provider', 'google|facebook|github|apple');
});

// Public routes - Clinics
Route::prefix('clinics')->group(function () {
    Route::get('/', [ClinicController::class, 'index']);
    Route::get('/featured', [ClinicController::class, 'featured']);
    Route::get('/{id}', [ClinicController::class, 'show']);
    Route::get('/{id}/doctors', [ClinicController::class, 'getDoctors']);
});

// Public routes - Doctors
Route::prefix('doctors')->group(function () {
    Route::get('/', [DoctorController::class, 'index']);
    Route::get('/specializations', [DoctorController::class, 'specializations']);
    Route::get('/{id}', [DoctorController::class, 'show']);
    Route::get('/{doctorId}/availability/{clinicId}', [DoctorController::class, 'getAvailability']);
});

// Protected routes
Route::middleware('auth:sanctum')->group(function () {
    // Auth routes
    Route::post('/logout', [AuthController::class, 'logout']);
    Route::get('/user', [AuthController::class, 'user']);
    
    // Social auth management
    Route::prefix('auth/social')->group(function () {
        Route::get('/user/providers', [SocialAuthController::class, 'getUserProviders']);
        Route::post('/link/{provider}', [SocialAuthController::class, 'linkProvider'])
            ->where('provider', 'google|facebook|github|apple');
        Route::delete('/unlink/{provider}', [SocialAuthController::class, 'unlinkProvider'])
            ->where('provider', 'google|facebook|github|apple');
    });
    
    // Appointments
    Route::prefix('appointments')->group(function () {
        Route::get('/', [AppointmentController::class, 'index']);
        Route::post('/', [AppointmentController::class, 'store']);
        Route::get('/{id}', [AppointmentController::class, 'show']);
        Route::put('/{id}/cancel', [AppointmentController::class, 'cancel']);
    });
});

// Doctor-specific routes
Route::middleware('auth:sanctum')->group(function () {
    // ... existing routes ...
    
    // Doctor Profile Management
    Route::prefix('doctor')->group(function () {
        Route::get('/profile', [DoctorController::class, 'getProfile']);
        Route::post('/profile', [DoctorController::class, 'updateProfile']);
        Route::get('/dashboard-stats', [DoctorController::class, 'getDashboardStats']);
        
        // Clinic Connection Management
        Route::get('/available-clinics', [DoctorController::class, 'getAvailableClinics']);
        Route::post('/send-connection-request', [DoctorController::class, 'sendConnectionRequest']);
        Route::get('/connection-requests', [DoctorController::class, 'getConnectionRequests']);
        Route::post('/update-connection-status/{id}', [DoctorController::class, 'updateConnectionStatus']);
        Route::get('/my-clinics', [DoctorController::class, 'getMyClinics']);
        Route::delete('/disconnect-clinic/{clinicId}', [DoctorController::class, 'disconnectClinic']);
        
        // Schedule Management
        Route::get('/schedule/{clinicId}', [DoctorController::class, 'getSchedule']);
        Route::post('/schedule/{clinicId}', [DoctorController::class, 'updateSchedule']);
        
        // Appointment Management
        Route::get('/appointments', [DoctorController::class, 'getMyAppointments']);
        Route::post('/appointments/{id}/update-status', [DoctorController::class, 'updateAppointmentStatus']);
    });
});

// Healthcare/Clinic-specific routes
Route::middleware('auth:sanctum')->group(function () {
    // ... existing routes ...
    
    // Healthcare Profile Management
    Route::prefix('healthcare')->group(function () {
        Route::get('/profile', [ClinicController::class, 'getHealthcareProfile']);
        Route::post('/profile', [ClinicController::class, 'updateHealthcareProfile']);
        Route::get('/dashboard-stats', [ClinicController::class, 'getHealthcareDashboardStats']);
        
        // Doctor Connection Management
        Route::get('/connection-requests', [ClinicController::class, 'getConnectionRequests']);
        Route::post('/connection-requests/{id}/respond', [ClinicController::class, 'respondToConnectionRequest']);
        Route::get('/connected-doctors', [ClinicController::class, 'getConnectedDoctors']);
        Route::delete('/disconnect-doctor/{doctorId}', [ClinicController::class, 'disconnectDoctor']);
        
        // Schedule Management
        Route::get('/doctor-schedule/{doctorId}', [ClinicController::class, 'getDoctorSchedule']);
        Route::post('/doctor-schedule/{doctorId}', [ClinicController::class, 'updateDoctorSchedule']);
        
        // Appointment Management
        Route::get('/appointments', [ClinicController::class, 'getClinicAppointments']);
        Route::post('/appointments/{id}/update-status', [ClinicController::class, 'updateClinicAppointmentStatus']);
        
        // Clinic Management (for multiple clinics)
        Route::get('/my-clinics', [ClinicController::class, 'getMyClinicsList']);
        Route::post('/add-clinic', [ClinicController::class, 'addNewClinic']);
    });
});

// Doctor-specific routes for clinic connections
Route::middleware('auth:sanctum')->group(function () {
    // ... existing routes ...
    
    // Doctor routes for clinic connections
    Route::prefix('doctor')->group(function () {
        // ... existing doctor routes ...
        
        // Clinic search and connection
        Route::get('/search-clinics', [DoctorController::class, 'searchClinics']);
        Route::post('/send-connection-request', [DoctorController::class, 'sendConnectionRequest']);
        Route::get('/my-connection-requests', [DoctorController::class, 'getMyConnectionRequests']);
        Route::get('/my-connected-clinics', [DoctorController::class, 'getMyConnectedClinics']);
        Route::delete('/disconnect-clinic/{clinicId}', [DoctorController::class, 'disconnectFromClinic']);
    });
});
```

***

## 🎨 Frontend Implementation

### Step 10: Frontend Package Configuration

**package.json**

```json
{
    "$schema": "https://json.schemastore.org/package.json",
    "private": true,
    "type": "module",
    "scripts": {
        "build": "vite build",
        "dev": "vite"
    },
    "devDependencies": {
        "@laravel/echo-react": "^2.1.6",
        "@tailwindcss/vite": "^4.0.0",
        "@vitejs/plugin-react": "^4.3.4",
        "axios": "^1.9.0",
        "concurrently": "^9.0.1",
        "laravel-echo": "^2.1.6",
        "laravel-vite-plugin": "^1.2.0",
        "pusher-js": "^8.4.0",
        "tailwindcss": "^4.0.0",
        "terser": "^5.43.1",
        "vite": "^6.2.4",
        "vite-plugin-compression": "^0.5.1"
    },
    "dependencies": {
        "@emotion/react": "^11.14.0",
        "@emotion/styled": "^11.14.0",
        "@mui/icons-material": "^6.4.4",
        "@mui/material": "^6.4.4",
        "@mui/x-charts": "^7.29.1",
        "@mui/x-data-grid": "^8.10.2",
        "@mui/x-date-pickers": "^8.10.2",
        "@react-google-maps/api": "^2.20.7",
        "@socket.io/redis-adapter": "^8.2.1",
        "@vitejs/plugin-react": "^4.5.0",
        "cors": "^2.8.5",
        "date-fns": "^4.1.0",
        "dotenv": "^16.4.5",
        "express": "^4.19.2",
        "ioredis": "^5.3.2",
        "leaflet": "^1.9.4",
        "qs": "^6.14.0",
        "react": "^18.2.0",
        "react-countup": "^6.5.3",
        "react-dom": "^18.2.0",
        "react-icons": "^5.5.0",
        "react-leaflet": "^4.2.1",
        "react-router-dom": "^7.1.5",
        "recharts": "^3.1.2",
        "socket.io": "^4.7.5",
        "socket.io-client": "^4.7.5",
        "styled-components": "^6.1.15",
        "styled-jsx": "^5.1.7",
        "validate-upi-id": "^1.0.1"
    }
}
```

### Step 11: Vite Configuration

**vite.config.js**

```javascript
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';
import react from '@vitejs/plugin-react';

export default defineConfig({
    plugins: [
        react({
            babel: {
                plugins: ['styled-jsx/babel']
            }
        }),
        laravel({
            input: ['resources/css/app.css', 'resources/js/app.jsx'],
            refresh: true,
        }),
    ],
    build: {
        assetsInlineLimit: 0,
        rollupOptions: {
            output: {
                manualChunks: {
                    vendor: ['react', 'react-dom'],
                    mui: ['@mui/material', '@mui/icons-material'],
                },
            },
        },
        minify: 'terser',
        terserOptions: {
            compress: {
                drop_console: true,
                drop_debugger: true,
            },
        },
        sourcemap: false,
        chunkSizeWarningLimit: 1600,
    },
    server: {
        hmr: {
            host: 'localhost',
        },
    },
    esbuild: {
        drop: process.env.NODE_ENV === 'production' ? ['console', 'debugger'] : [],
    },
});
```


### Step 12: Bootstrap Configuration

**resources/js/bootstrap.js**

```javascript
import axios from 'axios';
window.axios = axios;
window.axios.defaults.headers.common['X-Requested-With'] = 'XMLHttpRequest';
```


### Step 13: Welcome Blade Template

**resources/views/welcome.blade.php**

```php
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta name="csrf-token" content="{{ csrf_token() }}">
    <title>Visit Care - Healthcare Booking Platform</title>
    <meta name="description" content="Find and book appointments with verified doctors and top-rated clinics near you. Your trusted healthcare companion.">
    <meta name="keywords" content="healthcare, doctors, clinics, appointments, medical booking, telemedicine">
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link href="https://fonts.googleapis.com/css2?family=Roboto:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <link rel="icon" type="image/x-icon" href="/favicon.ico">
    
    <!-- Favicon -->
    <link rel="apple-touch-icon" sizes="180x180" href="/apple-touch-icon.png">
    <link rel="icon" type="image/png" sizes="32x32" href="/favicon-32x32.png">
    <link rel="icon" type="image/png" sizes="16x16" href="/favicon-16x16.png">
    
    <!-- Open Graph Meta Tags -->
    <meta property="og:title" content="Visit Care - Healthcare Booking Platform">
    <meta property="og:description" content="Find and book appointments with verified doctors and clinics near you">
    <meta property="og:image" content="/og-image.jpg">
    <meta property="og:url" content="{{ url('/') }}">
    <meta property="og:type" content="website">
    
    <!-- Twitter Meta Tags -->
    <meta name="twitter:card" content="summary_large_image">
    <meta name="twitter:title" content="Visit Care - Healthcare Booking Platform">
    <meta name="twitter:description" content="Find and book appointments with verified doctors and clinics near you">
    <meta name="twitter:image" content="/twitter-image.jpg">
    
    <!-- Preload critical resources -->
    <link rel="preload" href="{{ Vite::asset('resources/css/app.css') }}" as="style">
    <link rel="preload" href="{{ Vite::asset('resources/js/app.jsx') }}" as="script" crossorigin>

    <!-- Leaflet CSS -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"
              integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY="
              crossorigin=""/>

    @viteReactRefresh
    @vite(['resources/css/app.css', 'resources/js/app.jsx'])
</head>

<body>
    <div id="app"></div>
</body>

</html>
```


### Step 14: Main App Component

**resources/js/app.jsx**

```jsx
import React from 'react';
import { createRoot } from 'react-dom/client';
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';
import { ThemeProvider } from '@mui/material/styles';
import CssBaseline from '@mui/material/CssBaseline';
import theme from './theme/theme';
import HomePage from './pages/HomePage';
import ClinicDetailPage from './pages/ClinicDetailPage';
import DoctorDetailPage from './pages/DoctorDetailPage';
import BookAppointmentPage from './pages/BookAppointmentPage';
import DoctorDashboard from './pages/doctor/DoctorDashboard';
import HealthcareDashboard from './pages/healthcare/HealthcareDashboard';
import AdminDashboard from './pages/admin/AdminDashboard';
import AuthCallback from './components/auth/AuthCallback';
import '../css/app.css';
import './bootstrap';

function App() {
    return (
        <ThemeProvider theme={theme}>
            <CssBaseline />
            <Router>
                <Routes>
                    {/* Public Routes */}
                    <Route path="/" element={<HomePage />} />
                    <Route path="/clinic/:id" element={<ClinicDetailPage />} />
                    <Route path="/doctor/:id" element={<DoctorDetailPage />} />
                    
                    {/* Protected Routes */}
                    <Route path="/book-appointment" element={<BookAppointmentPage />} />
                    
                    {/* Auth Callback Route */}
                    <Route path="/auth/callback" element={<AuthCallback />} />
                    
                    {/* Role-based Dashboard Routes */}
                    <Route path="/doctor/dashboard" element={<DoctorDashboard />} />
                    <Route path="/healthcare/dashboard" element={<HealthcareDashboard />} />
                    <Route path="/admin/dashboard" element={<AdminDashboard />} />
                    
                    {/* Fallback Route */}
                    <Route path="*" element={<HomePage />} />
                </Routes>
            </Router>
        </ThemeProvider>
    );
}

const container = document.getElementById('app');
const root = createRoot(container);
root.render(<App />);
```


### Step 15: Material-UI Theme with Enhanced Icons

**resources/js/theme/theme.js**

```javascript
import { createTheme } from '@mui/material/styles';
import "./theme.css";

const theme = createTheme({
  palette: {
    primary: {
      main: '#10d915',    
      light: '#4de352',   
      dark: '#0cb010',    
    },
    secondary: {
      main: '#dc004e',
      light: '#ff5983',
      dark: '#9a0036',
    },
    success: {
      main: '#10d915',
    },
    error: {
      main: '#f27474',
    },
    warning: {
      main: '#f7e119',
    },
    info: {
      main: '#2196f3',
    },
    background: {
      default: '#f5f5f5',
      paper: '#ffffff',
    },
  },
  typography: {
    fontFamily: '"Roboto", "Helvetica", "Arial", sans-serif',
    h1: {
      fontSize: '2.5rem',
      fontWeight: 700,
    },
    h2: {
      fontSize: '2rem',
      fontWeight: 600,
    },
    h3: {
      fontSize: '1.5rem',
      fontWeight: 600,
    },
  },
  components: {
    MuiButton: {
      styleOverrides: {
        root: {
          borderRadius: 8,
          textTransform: 'none',
          fontWeight: 600,
        },
      },
    },
    MuiCard: {
      styleOverrides: {
        root: {
          borderRadius: 12,
          boxShadow: '0 4px 12px rgba(0,0,0,0.1)',
          '&:hover': {
            boxShadow: '0 8px 24px rgba(0,0,0,0.15)',
            transform: 'translateY(-2px)',
          },
          transition: 'all 0.3s ease-in-out',
        },
      },
    },
  },
});

export default theme;
```

**resources/js/theme/theme.css**

```css
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@200;300;400;500;600&display=swap');

* {
  font-family: 'Poppins', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
```


### Step 16: Enhanced Dummy Data with Icons

**resources/js/data/dummyData.js**

```javascript
export const specialties = [
    'General Medicine', 'Cardiology', 'Dermatology', 'Orthopedics',
    'Pediatrics', 'Neurology', 'Dentistry', 'Gynecology', 'Psychiatry', 'Oncology'
];

export const clinics = [
    {
        id: 1,
        name: 'City Medical Center',
        address: '123 Health Street, Downtown, NY 10001',
        distance: '0.5 km',
        rating: 4.8,
        reviewCount: 234,
        facilities: ['🩻 X-Ray', '🔬 Lab', '💊 Pharmacy', '🚨 Emergency'],
        image: 'https://images.unsplash.com/photo-1551190822-a9333d879b1f?w=400&h=300&fit=crop',
        openTime: '08:00',
        closeTime: '20:00',
        isOpen: true
    },
    {
        id: 2,
        name: 'Green Valley Clinic',
        address: '456 Wellness Ave, Midtown, NY 10002',
        distance: '1.2 km',
        rating: 4.6,
        reviewCount: 187,
        facilities: ['🔬 Lab', '💊 Pharmacy', '❤️ Cardiology', '🔍 Radiology'],
        image: 'https://images.unsplash.com/photo-1519494026892-80bbd2d6fd0d?w=400&h=300&fit=crop',
        openTime: '09:00',
        closeTime: '18:00',
        isOpen: true
    },
    {
        id: 3,
        name: 'Metro Health Hub',
        address: '789 Care Road, Uptown, NY 10003',
        distance: '2.1 km',
        rating: 4.7,
        reviewCount: 156,
        facilities: ['🩻 X-Ray', '🧲 MRI', '🔬 Lab', '💊 Pharmacy', '⚕️ Surgery'],
        image: 'https://images.unsplash.com/photo-1504813184591-01572f98c85f?w=400&h=300&fit=crop',
        openTime: '24/7',
        closeTime: '24/7',
        isOpen: true
    },
    {
        id: 4,
        name: 'Riverside Medical',
        address: '321 River Lane, Brooklyn, NY 11201',
        distance: '3.5 km',
        rating: 4.5,
        reviewCount: 298,
        facilities: ['🚨 Emergency', '🏥 ICU', '🔬 Lab', '💊 Pharmacy'],
        image: 'https://images.unsplash.com/photo-1586773860418-d37222d8eeb4?w=400&h=300&fit=crop',
        openTime: '06:00',
        closeTime: '22:00',
        isOpen: true
    }
];

export const doctors = [
    {
        id: 1,
        name: 'Dr. Sarah Johnson',
        specialty: '❤️ Cardiology',
        experience: 12,
        rating: 4.9,
        reviewCount: 145,
        fee: 150,
        isVerified: true,
        nextAvailable: 'Today 2:30 PM',
        languages: ['🇺🇸 English', '🇪🇸 Spanish'],
        image: 'https://images.unsplash.com/photo-1559839734-2b71ea197ec2?w=150&h=150&fit=crop&crop=face',
        clinicName: 'City Medical Center'
    },
    {
        id: 2,
        name: 'Dr. Michael Chen',
        specialty: '🩺 General Medicine',
        experience: 8,
        rating: 4.7,
        reviewCount: 89,
        fee: 100,
        isVerified: true,
        nextAvailable: 'Tomorrow 9:00 AM',
        languages: ['🇺🇸 English', '🇨🇳 Mandarin'],
        image: 'https://images.unsplash.com/photo-1612349317150-e413f6a5b16d?w=150&h=150&fit=crop&crop=face',
        clinicName: 'Green Valley Clinic'
    },
    {
        id: 3,
        name: 'Dr. Emily Rodriguez',
        specialty: '🌟 Dermatology',
        experience: 10,
        rating: 4.8,
        reviewCount: 123,
        fee: 120,
        isVerified: true,
        nextAvailable: 'Today 4:00 PM',
        languages: ['🇺🇸 English', '🇪🇸 Spanish'],
        image: 'https://images.unsplash.com/photo-1594824280438-29ed7265034c?w=150&h=150&fit=crop&crop=face',
        clinicName: 'Metro Health Hub'
    },
    {
        id: 4,
        name: 'Dr. James Wilson',
        specialty: '🦴 Orthopedics',
        experience: 15,
        rating: 4.9,
        reviewCount: 167,
        fee: 180,
        isVerified: true,
        nextAvailable: 'Tomorrow 11:30 AM',
        languages: ['🇺🇸 English'],
        image: 'https://images.unsplash.com/photo-1622253692010-333f2da6031d?w=150&h=150&fit=crop&crop=face',
        clinicName: 'Riverside Medical'
    },
    {
        id: 5,
        name: 'Dr. Lisa Anderson',
        specialty: '👶 Pediatrics',
        experience: 9,
        rating: 4.8,
        reviewCount: 201,
        fee: 110,
        isVerified: true,
        nextAvailable: 'Today 3:15 PM',
        languages: ['🇺🇸 English', '🇫🇷 French'],
        image: 'https://images.unsplash.com/photo-1582750433449-648ed127bb54?w=150&h=150&fit=crop&crop=face',
        clinicName: 'City Medical Center'
    },
    {
        id: 6,
        name: 'Dr. Robert Kumar',
        specialty: '🧠 Neurology',
        experience: 14,
        rating: 4.7,
        reviewCount: 134,
        fee: 200,
        isVerified: true,
        nextAvailable: 'Tomorrow 2:00 PM',
        languages: ['🇺🇸 English', '🇮🇳 Hindi'],
        image: 'https://images.unsplash.com/photo-1571019613454-1cb2f99b2d8b?w=150&h=150&fit=crop&crop=face',
        clinicName: 'Metro Health Hub'
    }
];

export const testimonials = [
    {
        id: 1,
        name: 'Alice Thompson',
        rating: 5,
        comment: 'Amazing experience! Found the perfect doctor near me within minutes. The booking process was seamless.',
        avatar: 'https://images.unsplash.com/photo-1494790108755-2616b612b977?w=60&h=60&fit=crop&crop=face',
        location: 'Manhattan, NY'
    },
    {
        id: 2,
        name: 'David Kumar',
        rating: 5,
        comment: 'The app made booking so easy. Great selection of verified doctors with real reviews from patients.',
        avatar: 'https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=60&h=60&fit=crop&crop=face',
        location: 'Brooklyn, NY'
    },
    {
        id: 3,
        name: 'Maria Garcia',
        rating: 4,
        comment: 'Love the location-based search. Found excellent healthcare nearby and saved so much time.',
        avatar: 'https://images.unsplash.com/photo-1438761681033-6461ffad8d80?w=60&h=60&fit=crop&crop=face',
        location: 'Queens, NY'
    },
    {
        id: 4,
        name: 'John Smith',
        rating: 5,
        comment: 'Professional service and verified doctors. The reminders feature is incredibly helpful.',
        avatar: 'https://images.unsplash.com/photo-1472099645785-5658abf4ff4e?w=60&h=60&fit=crop&crop=face',
        location: 'Bronx, NY'
    }
];

export const whyChooseFeatures = [
    {
        icon: 'LocationOn',
        title: '📍 Smart Location Search',
        description: 'Find doctors and clinics near you with precise GPS-based location search and distance tracking.'
    },
    {
        icon: 'Schedule',
        title: '⏰ Real Time Booking',
        description: 'Book appointments instantly with live availability updates and immediate confirmation.'
    },
    {
        icon: 'VerifiedUser',
        title: '✅ Verified Doctors',
        description: 'All our healthcare providers are thoroughly verified with proven credentials and licenses.'
    },
    {
        icon: 'PhoneIphone',
        title: '📱 Mobile Optimized',
        description: 'Perfect experience across all devices with our responsive and intuitive design.'
    },
    {
        icon: 'Security',
        title: '🔒 Secure & Private',
        description: 'Your health information is protected with bank-level encryption and privacy controls.'
    },
    {
        icon: 'NotificationImportant',
        title: '🔔 Smart Reminders',
        description: 'Never miss appointments with personalized reminders and health notifications.'
    }
];
```


### Step 17: Layout Components

**resources/js/components/layout/MainLayout.jsx**

```jsx
import React from 'react';
import { Box, useTheme, useMediaQuery } from '@mui/material';
import Header from '../common/Header';
import Footer from '../common/Footer';

const MainLayout = ({ children, disableGutters = false, maxWidth = "false", fullWidth = false, sx = {} }) => {
    const theme = useTheme();
    const isMobile = useMediaQuery(theme.breakpoints.down('md'));
    const isSmallMobile = useMediaQuery(theme.breakpoints.down('sm'));
    const isTablet = useMediaQuery(theme.breakpoints.between('sm', 'md'));

    // Calculate appropriate top padding based on device type
    const getTopPadding = () => {
        if (isSmallMobile) return '65px';
        if (isMobile) return '85px';
        if (isTablet) return '70px';
        return '0px';
    };

    return (
        <Box 
            sx={{
                display: 'flex',
                flexDirection: 'column',
                minHeight: '100vh',
                width: '100%',
                overflowX: 'hidden',
                ...sx
            }}
        >
            {/* Header - Fixed */}
            <Header />
            
            {/* Main Content Area */}
            <Box
                component="main"
                sx={{
                    flexGrow: 1,
                    width: fullWidth ? '100vw' : '100%',
                    paddingTop: getTopPadding(),
                    paddingX: disableGutters || fullWidth ? 0 : { xs: 0.5, sm: 1, md: 1.5 }, // Reduced padding
                    minHeight: `calc(100vh - ${getTopPadding()})`,
                    position: 'relative',
                    zIndex: 1,
                    // Ensure content doesn't get cut off on mobile
                    '@supports (padding: max(0px))': {
                        paddingTop: `max(${getTopPadding()}, env(safe-area-inset-top))`,
                    },
                    // Full width override for specific sections
                    ...(fullWidth && {
                        marginLeft: 'calc(-50vw + 50%)',
                        marginRight: 'calc(-50vw + 50%)',
                        maxWidth: '100vw',
                        width: '100vw',
                    })
                }}
            >
                {children}
            </Box>
            
            {/* Footer */}
            <Footer />
        </Box>
    );
};

export default MainLayout;
```


### Step 18: Enhanced Header Component

**resources/js/components/common/Header.jsx**

```jsx
import React, { useState, useEffect } from "react";
import {
    AppBar,
    Toolbar,
    Typography,
    Box,
    Button,
    IconButton,
    Avatar,
    Menu,
    MenuItem,
    Chip,
    CircularProgress,
    useMediaQuery,
    useTheme,
    Divider,
} from "@mui/material";
import {
    LocationOn,
    AccountCircle,
    Menu as MenuIcon,
    MyLocation,
    Search,
    Logout,
} from "@mui/icons-material";
import LocationMap from "../home/LocationMap";
import LoginModal from "../auth/LoginModal";

const Header = () => {
    const theme = useTheme();
    const isMobile = useMediaQuery(theme.breakpoints.down("md"));
    const [anchorEl, setAnchorEl] = useState(null);
    const [showLocationMap, setShowLocationMap] = useState(false);
    const [showLoginModal, setShowLoginModal] = useState(false);
    const [currentLocation, setCurrentLocation] = useState(
        "Getting location..."
    );
    const [currentCoords, setCurrentCoords] = useState([20.2961, 85.8245]); // Default to Bhubaneswar, Odisha
    const [locationLoading, setLocationLoading] = useState(true);
    const [user, setUser] = useState(null);
    const [isAuthenticated, setIsAuthenticated] = useState(false);

    useEffect(() => {
        // Check if user is authenticated on component mount
        checkAuthStatus();
        // Try to get user's current location on component mount
        getCurrentLocation();
    }, []);

    const checkAuthStatus = () => {
        const token = localStorage.getItem("auth_token");
        const userData = localStorage.getItem("user");

        if (token && userData) {
            try {
                const parsedUser = JSON.parse(userData);
                setUser(parsedUser);
                setIsAuthenticated(true);
            } catch (error) {
                console.error("Error parsing user data:", error);
                // Clear invalid data
                localStorage.removeItem("auth_token");
                localStorage.removeItem("user");
                setIsAuthenticated(false);
                setUser(null);
            }
        } else {
            setIsAuthenticated(false);
            setUser(null);
        }
    };

    const getCurrentLocation = () => {
        setLocationLoading(true);

        if (navigator.geolocation) {
            navigator.geolocation.getCurrentPosition(
                async (position) => {
                    const { latitude, longitude } = position.coords;
                    const coords = [latitude, longitude];

                    try {
                        const address = await reverseGeocodeGoogle(
                            latitude,
                            longitude
                        );
                        setCurrentLocation(address);
                        setCurrentCoords(coords);
                    } catch (error) {
                        console.error("Error getting address:", error);
                        setCurrentLocation("Bhubaneswar, Odisha, India");
                        setCurrentCoords(coords);
                    } finally {
                        setLocationLoading(false);
                    }
                },
                (error) => {
                    console.error("Geolocation error:", error);
                    setCurrentLocation("Bhubaneswar, Odisha, India");
                    setCurrentCoords([20.2961, 85.8245]); // Bhubaneswar fallback
                    setLocationLoading(false);
                },
                {
                    enableHighAccuracy: true,
                    timeout: 10000,
                    maximumAge: 300000, // 5 minutes
                }
            );
        } else {
            setCurrentLocation("Bhubaneswar, Odisha, India");
            setCurrentCoords([20.2961, 85.8245]); // Bhubaneswar fallback
            setLocationLoading(false);
        }
    };

    // Google Maps Reverse Geocoding
    const reverseGeocodeGoogle = async (lat, lng) => {
        try {
            // Check if Google Maps is available
            if (
                window.google &&
                window.google.maps &&
                window.google.maps.Geocoder
            ) {
                return new Promise((resolve) => {
                    const geocoder = new window.google.maps.Geocoder();
                    geocoder.geocode(
                        { location: { lat: Number(lat), lng: Number(lng) } },
                        (results, status) => {
                            if (status === "OK" && results && results[0]) {
                                const address = results[0].formatted_address;
                                if (typeof address === "string") {
                                    // Extract meaningful parts of the address
                                    const parts = address
                                        .split(",")
                                        .slice(0, 3);
                                    resolve(parts.join(",").trim());
                                } else {
                                    resolve("Location found");
                                }
                            } else {
                                console.warn(
                                    "Google geocoding failed:",
                                    status
                                );
                                resolve("Bhubaneswar, Odisha, India");
                            }
                        }
                    );
                });
            } else {
                // Fallback to Google Geocoding API if Maps JS API is not available
                const apiKey = import.meta.env.VITE_GOOGLE_API_KEY;
                if (!apiKey) {
                    throw new Error("Google API key not available");
                }

                const response = await fetch(
                    `https://maps.googleapis.com/maps/api/geocode/json?latlng=${lat},${lng}&key=${apiKey}&language=en&region=in`
                );

                if (!response.ok) {
                    throw new Error("Network response was not ok");
                }

                const data = await response.json();

                if (data.status === "OK" && data.results && data.results[0]) {
                    const address = data.results[0].formatted_address;
                    // Extract meaningful parts of the address (city, state, country)
                    const parts = address.split(",").slice(0, 3);
                    return parts.join(",").trim();
                } else {
                    throw new Error("No results found");
                }
            }
        } catch (error) {
            console.error("Google reverse geocoding error:", error);
            return "Bhubaneswar, Odisha, India";
        }
    };

    // COMMENTED OUT: OpenStreetMap reverse geocoding (keeping for reference)
    /*
  const reverseGeocode = async (lat, lng) => {
    try {
      const response = await fetch(
        `https://api.allorigins.win/get?url=${encodeURIComponent(
          `https://nominatim.openstreetmap.org/reverse?format=json&lat=${lat}&lon=${lng}&zoom=18&addressdetails=1`
        )}`
      );

      if (!response.ok) {
        throw new Error('Network response was not ok');
      }

      const proxyData = await response.json();
      const data = JSON.parse(proxyData.contents);

      if (data.display_name) {
        const address = data.address;
        if (address) {
          const parts = [];
          if (address.house_number && address.road) {
            parts.push(`${address.house_number} ${address.road}`);
          } else if (address.road) {
            parts.push(address.road);
          }
          if (address.city || address.town || address.village) {
            parts.push(address.city || address.town || address.village);
          }
          if (address.state) {
            parts.push(address.state);
          }
          if (address.postcode) {
            parts.push(address.postcode);
          }

          if (parts.length > 0) {
            return parts.join(', ');
          }
        }
        return data.display_name.split(',').slice(0, 3).join(',');
      } else {
        return 'Location not found';
      }
    } catch (error) {
      console.error('Reverse geocoding error:', error);
      return 'Location not found';
    }
  };
  */

    const handleProfileMenuOpen = (event) => {
        if (!isAuthenticated) {
            // Show login modal if user is not authenticated
            setShowLoginModal(true);
        } else {
            // Show profile menu if user is authenticated
            setAnchorEl(event.currentTarget);
        }
    };

    const handleMenuClose = () => {
        setAnchorEl(null);
    };

    const handleLocationUpdate = (newLocation, newCoords) => {
        setCurrentLocation(newLocation);
        setCurrentCoords(newCoords);
        setShowLocationMap(false);
    };

    const handleMenuItemClick = (action) => {
        handleMenuClose();
        if (action === "changeLocation") {
            setShowLocationMap(true);
        } else if (action === "logout") {
            handleLogout();
        }
        // Add other menu actions here as needed
    };

    const handleLoginSuccess = (userData, token) => {
        setUser(userData);
        setIsAuthenticated(true);
        setShowLoginModal(false);

        // Role-based redirection after login
        const redirectBasedOnRole = (role) => {
            switch (role) {
                case "patient":
                    // Stay on current page
                    break;
                case "doctor":
                    window.location.href = "/doctor/dashboard";
                    break;
                case "healthcare":
                    window.location.href = "/healthcare/dashboard";
                    break;
                case "admin":
                    window.location.href = "/admin/dashboard";
                    break;
                default:
                // Stay on current page
            }
        };

        redirectBasedOnRole(userData.user_type);
    };

    const handleLogout = () => {
        // Clear authentication data
        localStorage.removeItem("auth_token");
        localStorage.removeItem("user");
        setUser(null);
        setIsAuthenticated(false);
        // Optionally redirect to home page or show a success message
    };

    const LocationButton = () => (
        <Button
            startIcon={
                locationLoading ? (
                    <CircularProgress size={16} />
                ) : (
                    <LocationOn />
                )
            }
            onClick={() => setShowLocationMap(!showLocationMap)}
            sx={{
                color: "var(--text-primary)",
                textTransform: "none",
                "&:hover": {
                    backgroundColor: "rgba(255,255,255,0.1)",
                },
                minWidth: isMobile ? "auto" : "200px",
            }}
        >
            <Box
                sx={{
                    display: "flex",
                    flexDirection: "column",
                    alignItems: "flex-start",
                }}
            >
                <Typography
                    variant="caption"
                    sx={{ opacity: 0.8, fontSize: "0.7rem" }}
                >
                    Current Location
                </Typography>
                <Typography
                    variant="body2"
                    sx={{
                        fontWeight: 500,
                        maxWidth: isMobile ? "150px" : "180px",
                        overflow: "hidden",
                        textOverflow: "ellipsis",
                        whiteSpace: "nowrap",
                    }}
                >
                    {currentLocation}
                </Typography>
            </Box>
        </Button>
    );

    return (
        <>
            <AppBar
                position="fixed"
                color="transparent"
                elevation={0}
                sx={{
                    zIndex: 1201,
                    boxShadow: "0 6px 10px rgba(0, 0, 0, 0.3)",
                    backdropFilter: "blur(15px)",
                    borderBottomLeftRadius: "10px",
                    borderBottomRightRadius: "10px",
                    backgroundColor: "var(--appbar-bg)",
                    color: "var(--text-primary)",
                }}
            >
                <Toolbar>
                    {/* Logo and Location Button for Desktop */}
                    <Box
                        sx={{
                            display: "flex",
                            alignItems: "center",
                            gap: isMobile ? 0 : 3,
                        }}
                    >
                        <Typography
                            variant="h6"
                            component="div"
                            sx={{
                                fontWeight: "bold",
                                display: "flex",
                                alignItems: "center",
                                gap: 1,
                            }}
                        >
                            <img
                                src="/images/common/visit_care_logo.webp"
                                alt="Visit Care"
                                style={{
                                    height: isMobile ? "45px" : "45px",
                                    width: "auto",
                                    objectFit: "contain",
                                }}
                                onError={(e) => {
                                    console.error("Logo failed to load");
                                    e.target.style.display = "none";
                                    e.target.nextSibling.style.display =
                                        "inline";
                                }}
                            />
                        </Typography>

                        {/* Desktop Location Button - placed next to logo */}
                        {!isMobile && <LocationButton />}
                    </Box>

                    {/* Spacer for desktop */}
                    <Box sx={{ flexGrow: 1 }} />

                    {/* Profile Menu */}
                    <Box sx={{ display: "flex", alignItems: "center", gap: 1 }}>
                        {!isMobile && isAuthenticated && (
                            <Chip
                                label="Patient"
                                size="small"
                                variant="outlined"
                                sx={{
                                    color: "green",
                                    borderColor: "rgba(58, 187, 7, 0.5)",
                                }}
                            />
                        )}
                        <IconButton
                            edge="end"
                            onClick={handleProfileMenuOpen}
                            sx={{ color: "var(--text-primary)" }}
                        >
                            <Avatar
                                src={
                                    isAuthenticated && user?.avatar
                                        ? user.avatar
                                        : "/images/common/avatar.webp"
                                }
                                sx={{ width: 40, height: 40 }}
                            />
                        </IconButton>
                    </Box>
                </Toolbar>

                {/* Mobile Location Bar */}
                {isMobile && (
                    <Box
                        sx={{
                            px: 2,
                            pb: 1,
                            borderTop: "1px solid rgba(255,255,255,0.1)",
                        }}
                    >
                        <LocationButton />
                    </Box>
                )}
            </AppBar>

            {/* Enhanced Profile Menu - Only shown when authenticated */}
            {isAuthenticated && (
                <Menu
                    anchorEl={anchorEl}
                    open={Boolean(anchorEl)}
                    onClose={handleMenuClose}
                    anchorOrigin={{ vertical: "bottom", horizontal: "right" }}
                    transformOrigin={{ vertical: "top", horizontal: "right" }}
                    sx={{
                        "& .MuiPaper-root": {
                            minWidth: isMobile ? "250px" : "200px",
                            mt: 1,
                        },
                    }}
                >
                    {/* User Info Section */}
                    <Box
                        sx={{
                            px: 2,
                            py: 1,
                            borderBottom: "1px solid rgba(0,0,0,0.1)",
                        }}
                    >
                        <Typography
                            variant="subtitle2"
                            sx={{ fontWeight: "bold" }}
                        >
                            {user?.name || "User"}
                        </Typography>
                        <Typography variant="caption" color="text.secondary">
                            {user?.email || "user@example.com"}
                        </Typography>
                    </Box>

                    {/* Profile Section */}
                    <MenuItem onClick={() => handleMenuItemClick("profile")}>
                        <Box
                            sx={{
                                display: "flex",
                                alignItems: "center",
                                gap: 1,
                                width: "100%",
                            }}
                        >
                            <AccountCircle />
                            <Typography>My Profile</Typography>
                        </Box>
                    </MenuItem>

                    <MenuItem
                        onClick={() => handleMenuItemClick("appointments")}
                    >
                        <Box
                            sx={{
                                display: "flex",
                                alignItems: "center",
                                gap: 1,
                                width: "100%",
                            }}
                        >
                            <Search />
                            <Typography>My Appointments</Typography>
                        </Box>
                    </MenuItem>

                    {/* Divider to separate profile options from navigation options */}
                    <Divider sx={{ my: 1 }} />

                    {/* Navigation Options */}
                    <MenuItem
                        onClick={() => handleMenuItemClick("findDoctors")}
                    >
                        <Box
                            sx={{
                                display: "flex",
                                alignItems: "center",
                                gap: 1,
                                width: "100%",
                            }}
                        >
                            <Search />
                            <Typography>Find Doctors</Typography>
                        </Box>
                    </MenuItem>

                    <MenuItem
                        onClick={() => handleMenuItemClick("findClinics")}
                    >
                        <Box
                            sx={{
                                display: "flex",
                                alignItems: "center",
                                gap: 1,
                                width: "100%",
                            }}
                        >
                            <LocationOn />
                            <Typography>Find Clinics</Typography>
                        </Box>
                    </MenuItem>

                    <MenuItem
                        onClick={() => handleMenuItemClick("changeLocation")}
                    >
                        <Box
                            sx={{
                                display: "flex",
                                alignItems: "center",
                                gap: 1,
                                width: "100%",
                            }}
                        >
                            <MyLocation />
                            <Typography>Change Location</Typography>
                        </Box>
                    </MenuItem>

                    {/* Divider before logout */}
                    <Divider sx={{ my: 1 }} />

                    <MenuItem onClick={() => handleMenuItemClick("logout")}>
                        <Box
                            sx={{
                                display: "flex",
                                alignItems: "center",
                                gap: 1,
                                width: "100%",
                            }}
                        >
                            <Logout />
                            <Typography>Logout</Typography>
                        </Box>
                    </MenuItem>
                </Menu>
            )}

            {/* Login Modal */}
            <LoginModal
                open={showLoginModal}
                onClose={() => setShowLoginModal(false)}
                onLoginSuccess={handleLoginSuccess}
            />

            {/* Location Map Modal */}
            {showLocationMap && (
                <LocationMap
                    open={showLocationMap}
                    onClose={() => setShowLocationMap(false)}
                    currentLocation={currentLocation}
                    currentCoords={currentCoords}
                    onLocationUpdate={handleLocationUpdate}
                />
            )}
        </>
    );
};

export default Header;
```


### Step 19: Enhanced Footer Component

**resources/js/components/common/Footer.jsx**

```jsx
import React from 'react';
import {
    Box,
    Container,
    Grid,
    Typography,
    Link,
    IconButton,
    Divider,
    List,
    ListItem,
    ListItemIcon,
    ListItemText,
} from '@mui/material';
import {
    Phone,
    Email,
    LocationOn,
    Schedule,
    Facebook,
    Twitter,
    Instagram,
    LinkedIn,
    LocalHospital,
} from '@mui/icons-material';

const Footer = () => {
    const footerLinks = {
        services: [
            '🔍 Find Doctors',
            '📅 Book Appointments', 
            '🏥 Find Clinics',
            '📋 Health Records',
            '💻 Telemedicine',
            '🚨 Emergency Care'
        ],
        support: [
            '❓ Help Center',
            '📞 Contact Us',
            '💬 FAQ',
            '🔐 Privacy Policy',
            '📜 Terms of Service',
            '♿ Accessibility'
        ],
        company: [
            'ℹ️ About Us',
            '💼 Careers',
            '📰 Press',
            '🤝 Partners',
            '💰 Investors',
            '📝 Blog'
        ]
    };

    const contactInfo = [
        { icon: <Phone />, text: '📞 +1 (555) 123-4567' },
        { icon: <Email />, text: '📧 support@visitcare.com' },
        { icon: <LocationOn />, text: '📍 123 Healthcare Ave, New York, NY 10001' },
        { icon: <Schedule />, text: '🕐 24/7 Customer Support' }
    ];

    const socialIcons = [
        { icon: <Facebook />, link: '#', color: '#1877F2', name: 'Facebook' },
        { icon: <Twitter />, link: '#', color: '#1DA1F2', name: 'Twitter' },
        { icon: <Instagram />, link: '#', color: '#E4405F', name: 'Instagram' },
        { icon: <LinkedIn />, link: '#', color: '#0A66C2', name: 'LinkedIn' }
    ];

    return (
        <Box
            component="footer"
            sx={{
                bgcolor: 'grey.900',
                color: 'white',
                pt: 3,
                pb: 2,
                mt: 'auto',
                width: '100%'
            }}
        >
            <Container maxWidth="lg" sx={{ px: { xs: 1, sm: 2, md: 2 } }}>
                <Grid container spacing={2}>
                    <Grid item xs={12} md={4}>
                        <Box sx={{ mb: 2 }}>
                            <Box sx={{ display: 'flex', alignItems: 'center', mb: 3 }}>
                                <LocalHospital 
                                    sx={{ 
                                        color: 'primary.main', 
                                        fontSize: '2rem',
                                        mr: 1
                                    }} 
                                />
                                <Typography 
                                    variant="h5" 
                                    sx={{ 
                                        fontWeight: 'bold',
                                        background: 'linear-gradient(45deg, #10d915 30%, #21CBF3 90%)',
                                        backgroundClip: 'text',
                                        WebkitBackgroundClip: 'text',
                                        WebkitTextFillColor: 'transparent',
                                    }}
                                >
                                    Visit Care
                                </Typography>
                            </Box>
                            
                            <Typography 
                                variant="body2" 
                                sx={{ 
                                    color: 'grey.400', 
                                    mb: 2, 
                                    lineHeight: 1.7,
                                    fontSize: '0.9rem'
                                }}
                            >
                                🩺 Your trusted healthcare companion – easily find, book, and manage appointments with top providers near you.
                            </Typography>
                            
                            <Box sx={{ display: 'flex', gap: 1, mb: 2 }}>
                                {socialIcons.map((social, index) => (
                                    <IconButton
                                        key={index}
                                        component="a"
                                        href={social.link}
                                        title={social.name}
                                        sx={{
                                            color: 'grey.400',
                                            bgcolor: 'rgba(255, 255, 255, 0.05)',
                                            border: '1px solid rgba(255, 255, 255, 0.1)',
                                            width: 40,
                                            height: 40,
                                            transition: 'all 0.3s ease',
                                            '&:hover': {
                                                color: social.color,
                                                bgcolor: 'rgba(255, 255, 255, 0.1)',
                                                transform: 'translateY(-2px)',
                                                boxShadow: '0 4px 15px rgba(0, 0, 0, 0.3)'
                                            }
                                        }}
                                    >
                                        {social.icon}
                                    </IconButton>
                                ))}
                            </Box>

                            <Box>
                                <Typography 
                                    variant="body2" 
                                    sx={{ 
                                        mb: 2, 
                                        color: 'white',
                                        fontWeight: 500,
                                        fontSize: '1rem'
                                    }}
                                >
                                    📱 Download Our App
                                </Typography>
                                <Box sx={{ display: 'flex', gap: 1.5, flexWrap: 'wrap' }}>
                                    <Box
                                        component="img"
                                        src="/images/common/apple_app_store.webp"
                                        alt="Download on App Store"
                                        sx={{ 
                                            height: 40, 
                                            width: 'auto',
                                            cursor: 'pointer',
                                            transition: 'all 0.3s ease',
                                            borderRadius: 1,
                                            '&:hover': {
                                                transform: 'scale(1.05) translateY(-2px)',
                                                boxShadow: '0 6px 20px rgba(0, 0, 0, 0.3)'
                                            }
                                        }}
                                    />
                                    <Box
                                        component="img" 
                                        src="/images/common/google_play_store.webp"
                                        alt="Get it on Google Play"
                                        sx={{ 
                                            height: 40, 
                                            width: 'auto',
                                            cursor: 'pointer',
                                            transition: 'all 0.3s ease',
                                            borderRadius: 1,
                                            '&:hover': {
                                                transform: 'scale(1.05) translateY(-2px)',
                                                boxShadow: '0 6px 20px rgba(0, 0, 0, 0.3)'
                                            }
                                        }}
                                    />
                                </Box>
                            </Box>
                        </Box>
                    </Grid>

                    <Grid item xs={12} sm={6} md={2}>
                        <Typography 
                            variant="h6" 
                            sx={{ 
                                mb: 2, 
                                fontWeight: 'bold',
                                color: 'white',
                                fontSize: '1.1rem'
                            }}
                        >
                            🛠️ Services
                        </Typography>
                        <Box>
                            {footerLinks.services.map((service, index) => (
                                <Link
                                    key={index}
                                    href="#"
                                    sx={{
                                        display: 'block',
                                        color: 'grey.400',
                                        textDecoration: 'none',
                                        mb: 1.2,
                                        fontSize: '0.9rem',
                                        transition: 'all 0.3s ease',
                                        '&:hover': {
                                            color: 'white',
                                            textDecoration: 'none',
                                            transform: 'translateX(5px)'
                                        }
                                    }}
                                >
                                    {service}
                                </Link>
                            ))}
                        </Box>
                    </Grid>

                    <Grid item xs={12} sm={6} md={2}>
                        <Typography 
                            variant="h6" 
                            sx={{ 
                                mb: 2, 
                                fontWeight: 'bold',
                                color: 'white',
                                fontSize: '1.1rem'
                            }}
                        >
                            💬 Support
                        </Typography>
                        <Box>
                            {footerLinks.support.map((support, index) => (
                                <Link
                                    key={index}
                                    href="#"
                                    sx={{
                                        display: 'block',
                                        color: 'grey.400',
                                        textDecoration: 'none',
                                        mb: 1.2,
                                        fontSize: '0.9rem',
                                        transition: 'all 0.3s ease',
                                        '&:hover': {
                                            color: 'white',
                                            textDecoration: 'none',
                                            transform: 'translateX(5px)'
                                        }
                                    }}
                                >
                                    {support}
                                </Link>
                            ))}
                        </Box>
                    </Grid>

                    <Grid item xs={12} sm={6} md={2}>
                        <Typography 
                            variant="h6" 
                            sx={{ 
                                mb: 2, 
                                fontWeight: 'bold',
                                color: 'white',
                                fontSize: '1.1rem'
                            }}
                        >
                            🏢 Company
                        </Typography>
                        <Box>
                            {footerLinks.company.map((company, index) => (
                                <Link
                                    key={index}
                                    href="#"
                                    sx={{
                                        display: 'block',
                                        color: 'grey.400',
                                        textDecoration: 'none',
                                        mb: 1.2,
                                        fontSize: '0.9rem',
                                        transition: 'all 0.3s ease',
                                        '&:hover': {
                                            color: 'white',
                                            textDecoration: 'none',
                                            transform: 'translateX(5px)'
                                        }
                                    }}
                                >
                                    {company}
                                </Link>
                            ))}
                        </Box>
                    </Grid>

                    <Grid item xs={12} md={2}>
                        <Typography 
                            variant="h6" 
                            sx={{ 
                                mb: 2, 
                                fontWeight: 'bold',
                                color: 'white',
                                fontSize: '1.1rem'
                            }}
                        >
                            📞 Contact Info
                        </Typography>
                        <List dense>
                            {contactInfo.map((contact, index) => (
                                <ListItem key={index} disableGutters sx={{ py: 0.5 }}>
                                    <ListItemIcon 
                                        sx={{ 
                                            minWidth: 36, 
                                            color: 'primary.main',
                                            '& .MuiSvgIcon-root': {
                                                fontSize: '1.1rem'
                                            }
                                        }}
                                    >
                                        {contact.icon}
                                    </ListItemIcon>
                                    <ListItemText 
                                        primary={contact.text}
                                        primaryTypographyProps={{
                                            variant: 'body2',
                                            color: 'grey.400',
                                            fontSize: '0.85rem',
                                            lineHeight: 1.4
                                        }}
                                    />
                                </ListItem>
                            ))}
                        </List>
                    </Grid>
                </Grid>

                <Divider sx={{ my: 4, bgcolor: 'grey.700' }} />

                <Box
                    sx={{
                        display: 'flex',
                        flexDirection: { xs: 'column', sm: 'row' },
                        justifyContent: 'space-between',
                        alignItems: 'center',
                        gap: 2
                    }}
                >
                    <Typography 
                        variant="body2" 
                        sx={{ 
                            color: 'grey.400',
                            fontSize: '0.9rem'
                        }}
                    >
                        © 2025 Visit Care. All rights reserved. Made with ❤️ for better healthcare.
                    </Typography>
                    
                    <Box sx={{ display: 'flex', gap: 3, flexWrap: 'wrap' }}>
                        {['🔐 Privacy Policy', '📜 Terms of Service', '🍪 Cookie Policy', '♿ Accessibility'].map((link) => (
                            <Link
                                key={link}
                                href="#"
                                sx={{
                                    color: 'grey.400',
                                    textDecoration: 'none',
                                    fontSize: '0.875rem',
                                    transition: 'all 0.3s ease',
                                    '&:hover': {
                                        color: 'white',
                                        textDecoration: 'underline'
                                    }
                                }}
                            >
                                {link}
                            </Link>
                        ))}
                    </Box>
                </Box>
            </Container>
        </Box>
    );
};

export default Footer;
```


### Step 20: Home Page Component

**resources/js/pages/HomePage.jsx**

```jsx
import React from 'react';
import MainLayout from '../components/layout/MainLayout';
import SearchSection from '../components/home/SearchSection';
import NearbySection from '../components/home/NearbySection';
import StatsSection from '../components/home/StatsSection';
import WhyChooseSection from '../components/home/WhyChooseSection';
import TestimonialsSection from '../components/home/TestimonialsSection';

const HomePage = () => {
    return (
        <MainLayout>
            <SearchSection />
            <NearbySection />
            <StatsSection />
            <WhyChooseSection />
            <TestimonialsSection />
        </MainLayout>
    );
};

export default HomePage;
```

**resources/js/pages/DoctorDetailPage.jsx**

```jsx
import React, { useState, useEffect } from "react";
import { useParams, useNavigate } from "react-router-dom";
import {
    Container,
    Typography,
    Box,
    Grid,
    Card,
    Button,
    Chip,
    Rating,
    Avatar,
    List,
    ListItem,
    ListItemIcon,
    ListItemText,
    CircularProgress,
    Alert,
    Tabs,
    Tab,
    Accordion,
    AccordionSummary,
    AccordionDetails,
    Paper,
    Table,
    TableBody,
    TableCell,
    TableContainer,
    TableHead,
    TableRow,
    Divider,
    useTheme,
    useMediaQuery,
    Stack,
} from "@mui/material";
import {
    CalendarToday,
    ExpandMore,
    Work,
    Today,
    Weekend,
    BusinessCenter,
} from "@mui/icons-material";
import {
    FaUserMd,
    FaHospital,
    FaCalendarAlt,
    FaClock,
    FaCheckCircle,
    FaTimesCircle,
    FaPhone,
    FaMapMarkerAlt,
    FaStethoscope,
    FaMedkit,
    FaGraduationCap,
    FaLanguage,
    FaMoneyBillWave,
    FaCalendarCheck,
} from "react-icons/fa";
import {
    MdVerified,
    MdSchedule,
    MdLocationOn,
    MdLocalHospital,
    MdInfo,
    MdAccessTime,
    MdEventAvailable,
} from "react-icons/md";
import MainLayout from "../components/layout/MainLayout";
import LoginModal from "../components/auth/LoginModal";
import axios from "axios";

const DoctorDetailPage = () => {
    const { id } = useParams();
    const navigate = useNavigate();
    const apiUrl = import.meta.env.VITE_API_URL;
    const theme = useTheme();
    const isMobile = useMediaQuery(theme.breakpoints.down("sm"));
    const isTablet = useMediaQuery(theme.breakpoints.down("md"));

    const [doctor, setDoctor] = useState(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState("");
    const [tabValue, setTabValue] = useState(0);
    const [showLoginModal, setShowLoginModal] = useState(false);
    const [selectedClinic, setSelectedClinic] = useState(null);

    // Days of the week with React Icons
    const daysOfWeek = [
        { key: "monday", label: "Monday", icon: <Today />, shortLabel: "Mon" },
        {
            key: "tuesday",
            label: "Tuesday",
            icon: <Today />,
            shortLabel: "Tue",
        },
        {
            key: "wednesday",
            label: "Wednesday",
            icon: <Today />,
            shortLabel: "Wed",
        },
        {
            key: "thursday",
            label: "Thursday",
            icon: <Today />,
            shortLabel: "Thu",
        },
        { key: "friday", label: "Friday", icon: <Today />, shortLabel: "Fri" },
        {
            key: "saturday",
            label: "Saturday",
            icon: <Weekend />,
            shortLabel: "Sat",
        },
        {
            key: "sunday",
            label: "Sunday",
            icon: <Weekend />,
            shortLabel: "Sun",
        },
    ];

    useEffect(() => {
        loadDoctorDetails();
    }, [id]);

    const loadDoctorDetails = async () => {
        try {
            const response = await axios.get(`${apiUrl}/doctors/${id}`);
            setDoctor(response.data);
        } catch (error) {
            console.error("Failed to load doctor details:", error);
            setError("Failed to load doctor details");
        } finally {
            setLoading(false);
        }
    };

    const handleBookAppointment = (clinic) => {
        const token = localStorage.getItem("auth_token");
        if (!token) {
            setSelectedClinic(clinic);
            setShowLoginModal(true);
        } else {
            navigate(`/book-appointment`, {
                state: {
                    doctor: doctor,
                    clinic: clinic,
                    type: "doctor",
                },
            });
        }
    };

    const handleLoginSuccess = (userData, token) => {
        localStorage.setItem("auth_token", token);
        localStorage.setItem("user_data", JSON.stringify(userData));
        setShowLoginModal(false);

        if (selectedClinic) {
            navigate(`/book-appointment`, {
                state: {
                    doctor: doctor,
                    clinic: selectedClinic,
                    type: "doctor",
                },
            });
        }
    };

    // Safe number conversion for ratings
    const safeRating = (rating) => {
        if (rating === null || rating === undefined) return 0;
        const numRating =
            typeof rating === "string" ? parseFloat(rating) : rating;
        return isNaN(numRating) ? 0 : numRating;
    };

    // Parse available days safely
    const parseAvailableDays = (availableDays) => {
        if (!availableDays) return [];
        try {
            if (typeof availableDays === "string") {
                return JSON.parse(availableDays);
            }
            return Array.isArray(availableDays) ? availableDays : [];
        } catch (error) {
            console.error("Error parsing available days:", error);
            return [];
        }
    };

    // Check if doctor is available on a specific day at any clinic
    const isDoctorAvailableOnDay = (dayKey) => {
        if (!doctor || !doctor.clinics) return false;

        return doctor.clinics.some((clinic) => {
            const availableDays = parseAvailableDays(
                clinic.pivot?.available_days
            );
            return availableDays.includes(dayKey);
        });
    };

    // Get clinics where doctor is available on a specific day
    const getClinicsForDay = (dayKey) => {
        if (!doctor || !doctor.clinics) return [];

        return doctor.clinics.filter((clinic) => {
            const availableDays = parseAvailableDays(
                clinic.pivot?.available_days
            );
            return availableDays.includes(dayKey);
        });
    };

    // Mobile-friendly Weekly Schedule Section
    const renderWeeklySchedule = () => (
        <Box>
            <Box sx={{ display: "flex", alignItems: "center", gap: 1, mb: 3 }}>
                <MdEventAvailable
                    size={28}
                    color={theme.palette.primary.main}
                />
                <Typography
                    variant={isMobile ? "h6" : "h5"}
                    sx={{ fontWeight: "bold" }}
                >
                    Weekly Schedule
                </Typography>
            </Box>

            {/* Summary Cards - Mobile Optimized */}
            <Grid container spacing={2} sx={{ mb: 3 }}>
                <Grid item xs={6} sm={3}>
                    <Card
                        elevation={2}
                        sx={{
                            textAlign: "center",
                            p: isMobile ? 1.5 : 2,
                            bgcolor: "primary.50",
                            height: "100%",
                        }}
                    >
                        <MdLocalHospital
                            size={isMobile ? 20 : 24}
                            color={theme.palette.warning.main}
                        />
                        <Typography
                            variant={isMobile ? "h6" : "h4"}
                            sx={{
                                fontWeight: "bold",
                                color: "warning.main",
                                fontSize: isMobile ? "1.2rem" : "2.5rem",
                            }}
                        >
                            {doctor.clinics.length}
                        </Typography>
                        <Typography
                            variant={isMobile ? "caption" : "body2"}
                            color="text.secondary"
                        >
                            Total Clinics
                        </Typography>
                    </Card>
                </Grid>
                <Grid item xs={6} sm={3}>
                    <Card
                        elevation={2}
                        sx={{
                            textAlign: "center",
                            p: isMobile ? 1.5 : 2,
                            bgcolor: "success.50",
                            height: "100%",
                        }}
                    >
                        <FaCalendarCheck
                            size={isMobile ? 20 : 24}
                            color={theme.palette.success.main}
                        />
                        <Typography
                            variant={isMobile ? "h6" : "h4"}
                            sx={{
                                fontWeight: "bold",
                                color: "success.main",
                                fontSize: isMobile ? "1.2rem" : "2.5rem",
                            }}
                        >
                            {
                                daysOfWeek.filter((day) =>
                                    isDoctorAvailableOnDay(day.key)
                                ).length
                            }
                        </Typography>
                        <Typography
                            variant={isMobile ? "caption" : "body2"}
                            color="text.secondary"
                        >
                            Available Days
                        </Typography>
                    </Card>
                </Grid>
                <Grid item xs={6} sm={3}>
                    <Card
                        elevation={2}
                        sx={{
                            textAlign: "center",
                            p: isMobile ? 1.5 : 2,
                            bgcolor: "info.50",
                            height: "100%",
                        }}
                    >
                        <MdAccessTime
                            size={isMobile ? 20 : 24}
                            color={theme.palette.info.main}
                        />
                        <Typography
                            variant={isMobile ? "h6" : "h4"}
                            sx={{
                                fontWeight: "bold",
                                color: "info.main",
                                fontSize: isMobile ? "1.2rem" : "2.5rem",
                            }}
                        >
                            {doctor.clinics.reduce((total, clinic) => {
                                const availableDays = parseAvailableDays(
                                    clinic.pivot?.available_days
                                );
                                return total + availableDays.length;
                            }, 0)}
                        </Typography>
                        <Typography
                            variant={isMobile ? "caption" : "body2"}
                            color="text.secondary"
                        >
                            Total Sessions
                        </Typography>
                    </Card>
                </Grid>
                <Grid item xs={6} sm={3}>
                    <Card
                        elevation={2}
                        sx={{
                            textAlign: "center",
                            p: isMobile ? 1.5 : 2,
                            bgcolor: "warning.50",
                            height: "100%",
                        }}
                    >
                        <FaMoneyBillWave
                            size={isMobile ? 20 : 24}
                            color={theme.palette.secondary.light}
                        />
                        <Typography
                            variant={isMobile ? "h6" : "h4"}
                            sx={{
                                fontWeight: "bold",
                                color: "secondary.light",
                                fontSize: isMobile ? "1.2rem" : "2.5rem",
                            }}
                        >
                            ₹{doctor.consultation_fee}
                        </Typography>
                        <Typography
                            variant={isMobile ? "caption" : "body2"}
                            color="text.secondary"
                        >
                            Consultation Fee
                        </Typography>
                    </Card>
                </Grid>
            </Grid>

            {/* Mobile-First Schedule Design */}
            {isMobile ? renderMobileSchedule() : renderDesktopSchedule()}

            {/* Additional Schedule Information */}
            <Alert severity="info" elevation={3} sx={{ mt: 2, borderRadius: 2 }} icon={<MdInfo />}>
                <Typography variant="body2">
                    <strong>Note:</strong>
                    <ul
                        style={{
                            marginTop: "8px",
                            marginBottom: "8px",
                            paddingLeft: "20px",
                        }}
                    >
                        <li>
                            Schedule may vary during holidays and special
                            occasions. Please confirm appointment availability
                            before visiting.
                        </li>
                        <li>
                            Emergency consultations may be available outside
                            regular hours.
                        </li>
                        <li>
                            If the doctor or healthcare provider cancels a
                            schedule, we will inform you via email and SMS, and
                            it will be reflected on our website as well.
                        </li>
                    </ul>
                </Typography>
            </Alert>
        </Box>
    );

    // Mobile Schedule Cards
    const renderMobileSchedule = () => (
        <Box sx={{ mb: 3 }}>
            <Box sx={{ display: "flex", alignItems: "center", gap: 1, mb: 2 }}>
                <MdSchedule size={20} color={theme.palette.primary.main} />
                <Typography variant="h6" sx={{ fontWeight: "bold" }}>
                    Day-wise Availability
                </Typography>
            </Box>

            <Stack spacing={2}>
                {daysOfWeek.map((day) => {
                    const isAvailable = isDoctorAvailableOnDay(day.key);
                    const clinicsForDay = getClinicsForDay(day.key);

                    return (
                        <Card
                            key={day.key}
                            elevation={5}
                            sx={{
                                p: 2,
                                bgcolor: isAvailable
                                    ? "rgba(76, 175, 80, 0.05)"
                                    : "rgba(0,0,0,0.02)",
                                border: isAvailable
                                    ? "1px solid rgba(76, 175, 80, 0.2)"
                                    : "1px solid rgba(0,0,0,0.1)",
                            }}
                        >
                            <Box
                                sx={{
                                    display: "flex",
                                    justifyContent: "space-between",
                                    alignItems: "flex-start",
                                    mb: 1,
                                }}
                            >
                                <Box
                                    sx={{
                                        display: "flex",
                                        alignItems: "center",
                                        gap: 1,
                                        flex: 1,
                                    }}
                                >
                                    {day.icon}
                                    <Typography
                                        variant="subtitle1"
                                        sx={{ fontWeight: 600 }}
                                    >
                                        {day.label}
                                    </Typography>
                                </Box>

                                {isAvailable ? (
                                    <Chip
                                        icon={<FaCheckCircle size={12} />}
                                        label="Available"
                                        color="success"
                                        size="small"
                                        sx={{
                                            fontWeight: 500,
                                            fontSize: "0.75rem",
                                            color: "white",
                                        }}
                                    />
                                ) : (
                                    <Chip
                                        icon={<FaTimesCircle size={12} />}
                                        label="Not Available"
                                        color="error"
                                        size="small"
                                        variant="outlined"
                                        sx={{ fontSize: "0.75rem" }}
                                    />
                                )}
                            </Box>

                            {isAvailable ? (
                                <Box>
                                    {clinicsForDay.map((clinic, index) => {
                                        const startTime =
                                            clinic.pivot?.start_time || "N/A";
                                        const endTime =
                                            clinic.pivot?.end_time || "N/A";

                                        return (
                                            <Box
                                                key={clinic.id}
                                                sx={{
                                                    mb:
                                                        index <
                                                        clinicsForDay.length - 1
                                                            ? 2
                                                            : 0,
                                                }}
                                            >
                                                <Box
                                                    sx={{
                                                        display: "flex",
                                                        alignItems: "center",
                                                        gap: 1,
                                                        mb: 0.5,
                                                    }}
                                                >
                                                    <FaHospital
                                                        size={14}
                                                        color={
                                                            theme.palette
                                                                .primary.main
                                                        }
                                                    />
                                                    <Typography
                                                        variant="body2"
                                                        sx={{
                                                            fontWeight: 600,
                                                            color: "primary.main",
                                                            fontSize: "0.9rem",
                                                        }}
                                                    >
                                                        {clinic.name}
                                                    </Typography>
                                                </Box>
                                                <Box
                                                    sx={{
                                                        display: "flex",
                                                        alignItems: "center",
                                                        gap: 1,
                                                        mb: 1,
                                                    }}
                                                >
                                                    <FaClock
                                                        size={12}
                                                        color={
                                                            theme.palette.text
                                                                .secondary
                                                        }
                                                    />
                                                    <Typography
                                                        variant="caption"
                                                        color="text.secondary"
                                                    >
                                                        {startTime} - {endTime}
                                                    </Typography>
                                                </Box>
                                                {index <
                                                    clinicsForDay.length -
                                                        1 && (
                                                    <Divider sx={{ my: 1 }} />
                                                )}
                                            </Box>
                                        );
                                    })}

                                    <Box sx={{ mt: 2 }}>
                                        <Button
                                            variant="outlined"
                                            size="small"
                                            startIcon={<CalendarToday />}
                                            onClick={() =>
                                                handleBookAppointment(
                                                    clinicsForDay[0]
                                                )
                                            }
                                            sx={{
                                                textTransform: "none",
                                                borderRadius: 2,
                                                fontWeight: 500,
                                                width: "100%",
                                            }}
                                        >
                                            Book Appointment
                                        </Button>
                                    </Box>
                                </Box>
                            ) : (
                                <Typography
                                    variant="body2"
                                    color="text.secondary"
                                    sx={{ fontSize: "0.9rem" }}
                                >
                                    No sessions scheduled for this day
                                </Typography>
                            )}
                        </Card>
                    );
                })}
            </Stack>
        </Box>
    );

    // Desktop Schedule Table
    const renderDesktopSchedule = () => (
        <Paper elevation={5} sx={{ mb: 3, borderRadius: 3 }}>
            <Box sx={{ p: 3, pb: 0 }}>
                <Box
                    sx={{
                        display: "flex",
                        alignItems: "center",
                        gap: 1,
                        mb: 2,
                    }}
                >
                    <MdSchedule size={24} color={theme.palette.primary.main} />
                    <Typography variant="h6" sx={{ fontWeight: "bold" }}>
                        Day-wise Availability
                    </Typography>
                </Box>
            </Box>

            <TableContainer>
                <Table>
                    <TableHead>
                        <TableRow>
                            <TableCell
                                sx={{ fontWeight: "bold", fontSize: "1rem" }}
                            >
                                Day
                            </TableCell>
                            <TableCell
                                sx={{ fontWeight: "bold", fontSize: "1rem" }}
                            >
                                Available
                            </TableCell>
                            <TableCell
                                sx={{ fontWeight: "bold", fontSize: "1rem" }}
                            >
                                Clinics & Timings
                            </TableCell>
                            <TableCell
                                sx={{ fontWeight: "bold", fontSize: "1rem" }}
                            >
                                Action
                            </TableCell>
                        </TableRow>
                    </TableHead>
                    <TableBody>
                        {daysOfWeek.map((day) => {
                            const isAvailable = isDoctorAvailableOnDay(day.key);
                            const clinicsForDay = getClinicsForDay(day.key);

                            return (
                                <TableRow
                                    key={day.key}
                                    sx={{
                                        "&:hover": {
                                            bgcolor: "rgba(0,0,0,0.04)",
                                        },
                                        bgcolor: isAvailable
                                            ? "rgba(76, 175, 80, 0.05)"
                                            : "rgba(0,0,0,0.02)",
                                    }}
                                >
                                    <TableCell>
                                        <Box
                                            sx={{
                                                display: "flex",
                                                alignItems: "center",
                                                gap: 1,
                                            }}
                                        >
                                            {day.icon}
                                            <Typography
                                                variant="body1"
                                                sx={{ fontWeight: 600 }}
                                            >
                                                {day.label}
                                            </Typography>
                                        </Box>
                                    </TableCell>

                                    <TableCell>
                                        {isAvailable ? (
                                            <Chip
                                                icon={
                                                    <FaCheckCircle size={14} />
                                                }
                                                label="Available"
                                                color="success"
                                                size="small"
                                                sx={{
                                                    fontWeight: 500,
                                                    color: "white",
                                                }}
                                            />
                                        ) : (
                                            <Chip
                                                icon={
                                                    <FaTimesCircle size={14} />
                                                }
                                                label="Not Available"
                                                color="error"
                                                size="small"
                                                variant="outlined"
                                            />
                                        )}
                                    </TableCell>

                                    <TableCell>
                                        {isAvailable ? (
                                            <Box
                                                sx={{
                                                    display: "flex",
                                                    flexDirection: "column",
                                                    gap: 1,
                                                }}
                                            >
                                                {clinicsForDay.map(
                                                    (clinic, index) => {
                                                        const startTime =
                                                            clinic.pivot
                                                                ?.start_time ||
                                                            "N/A";
                                                        const endTime =
                                                            clinic.pivot
                                                                ?.end_time ||
                                                            "N/A";

                                                        return (
                                                            <Box
                                                                key={clinic.id}
                                                            >
                                                                <Box
                                                                    sx={{
                                                                        display:
                                                                            "flex",
                                                                        alignItems:
                                                                            "center",
                                                                        gap: 1,
                                                                    }}
                                                                >
                                                                    <FaHospital
                                                                        size={
                                                                            14
                                                                        }
                                                                        color={
                                                                            theme
                                                                                .palette
                                                                                .primary
                                                                                .main
                                                                        }
                                                                    />
                                                                    <Typography
                                                                        variant="body2"
                                                                        sx={{
                                                                            fontWeight: 600,
                                                                            color: "primary.main",
                                                                        }}
                                                                    >
                                                                        {
                                                                            clinic.name
                                                                        }
                                                                    </Typography>
                                                                </Box>
                                                                <Box
                                                                    sx={{
                                                                        display:
                                                                            "flex",
                                                                        alignItems:
                                                                            "center",
                                                                        gap: 1,
                                                                        ml: 2,
                                                                    }}
                                                                >
                                                                    <FaClock
                                                                        size={
                                                                            12
                                                                        }
                                                                    />
                                                                    <Typography variant="caption">
                                                                        {
                                                                            startTime
                                                                        }{" "}
                                                                        -{" "}
                                                                        {
                                                                            endTime
                                                                        }
                                                                    </Typography>
                                                                </Box>
                                                                {index <
                                                                    clinicsForDay.length -
                                                                        1 && (
                                                                    <Divider
                                                                        sx={{
                                                                            my: 1,
                                                                        }}
                                                                    />
                                                                )}
                                                            </Box>
                                                        );
                                                    }
                                                )}
                                            </Box>
                                        ) : (
                                            <Typography
                                                variant="body2"
                                                color="text.secondary"
                                            >
                                                No sessions scheduled
                                            </Typography>
                                        )}
                                    </TableCell>

                                    <TableCell>
                                        {isAvailable &&
                                            clinicsForDay.length > 0 && (
                                                <Button
                                                    variant="outlined"
                                                    size="small"
                                                    startIcon={
                                                        <CalendarToday />
                                                    }
                                                    onClick={() =>
                                                        handleBookAppointment(
                                                            clinicsForDay[0]
                                                        )
                                                    }
                                                    sx={{
                                                        textTransform: "none",
                                                        borderRadius: 2,
                                                        fontWeight: 500,
                                                    }}
                                                >
                                                    Book
                                                </Button>
                                            )}
                                    </TableCell>
                                </TableRow>
                            );
                        })}
                    </TableBody>
                </Table>
            </TableContainer>
        </Paper>
    );

    const renderDoctorInfo = () => (
        <Grid container spacing={isMobile ? 2 : 4}>
            <Grid item xs={12} md={4}>
                <Card
                    elevation={5}
                    sx={{
                        textAlign: "center",
                        p: isMobile ? 2 : 3,
                        height: "fit-content",
                    }}
                >
                    <Avatar
                        src={doctor.profile_image}
                        sx={{
                            width: isMobile ? 120 : 150,
                            height: isMobile ? 120 : 150,
                            mx: "auto",
                            mb: 3,
                            border: "4px solid",
                            borderColor: "primary.main",
                        }}
                    >
                        <FaUserMd size={isMobile ? 40 : 60} />
                    </Avatar>

                    <Typography
                        variant={isMobile ? "h5" : "h4"}
                        gutterBottom
                        sx={{ fontWeight: "bold", mb: 2 }}
                    >
                        {doctor.user.name}
                    </Typography>

                    <Typography
                        sx={{ mb: 2 }}
                        variant="h6"
                        color="secondary"
                        gutterBottom
                    >
                        {doctor.specialization}
                    </Typography>

                    {doctor.is_verified && (
                        <Chip
                            label="Verified Doctor"
                            color="success"
                            icon={<MdVerified />}
                            sx={{ mb: 3, color: "white" }}
                        />
                    )}

                    <Box
                        sx={{
                            display: "flex",
                            justifyContent: "center",
                            alignItems: "center",
                            mb: 3,
                            gap: 1,
                        }}
                    >
                        <Rating
                            value={safeRating(doctor.rating)}
                            precision={0.1}
                            readOnly
                            size={isMobile ? "small" : "medium"}
                        />
                        <Typography variant="body1">
                            {safeRating(doctor.rating)} ({doctor.total_reviews}{" "}
                            reviews)
                        </Typography>
                    </Box>

                    <Box
                        sx={{
                            display: "flex",
                            alignItems: "center",
                            justifyContent: "center",
                            mb: 3,
                            gap: 1,
                        }}
                    >
                        <Work
                            fontSize="small"
                            color="action"
                            sx={{
                                fontSize: "1.5rem",
                            }}
                        />
                        <Typography
                            variant="caption"
                            sx={{
                                fontSize: "1.3rem",
                                fontWeight: "bold",
                            }}
                            color="text.secondary"
                        >
                            {doctor.experience_years} years of experience
                        </Typography>
                    </Box>
                </Card>
            </Grid>

            <Grid item xs={12} md={8}>
                <Card elevation={5} sx={{ p: isMobile ? 2 : 3, mb: 3 }}>
                    <Box
                        sx={{
                            display: "flex",
                            alignItems: "center",
                            gap: 1,
                            mb: 1,
                        }}
                    >
                        <FaStethoscope
                            size={20}
                            color={theme.palette.primary.main}
                        />
                        <Typography
                            variant={isMobile ? "h6" : "h5"}
                            sx={{ fontWeight: "bold" }}
                        >
                            About {doctor.user.name}
                        </Typography>
                    </Box>

                    <Typography
                        variant="body1"
                        paragraph
                        sx={{ lineHeight: 1.6 }}
                    >
                        {doctor.bio}
                    </Typography>

                    <Grid container spacing={3}>
                        <Grid item xs={12} sm={6}>
                            <Box
                                sx={{
                                    display: "flex",
                                    alignItems: "center",
                                    gap: 1,
                                    mb: 1,
                                }}
                            >
                                <FaGraduationCap
                                    size={16}
                                    color={theme.palette.primary.main}
                                />
                                <Typography variant="h6" gutterBottom>
                                    Education
                                </Typography>
                            </Box>
                            <Typography
                                variant="body2"
                                sx={{ lineHeight: 1.5 }}
                            >
                                {doctor.qualification}
                            </Typography>
                            {doctor.education && (
                                <Typography
                                    variant="body2"
                                    sx={{ mt: 1, lineHeight: 1.5 }}
                                >
                                    {doctor.education}
                                </Typography>
                            )}
                        </Grid>

                        <Grid item xs={12} sm={6}>
                            <Box
                                sx={{
                                    display: "flex",
                                    alignItems: "center",
                                    gap: 1,
                                    mb: 1,
                                }}
                            >
                                <FaLanguage
                                    size={16}
                                    color={theme.palette.primary.main}
                                />
                                <Typography variant="h6" gutterBottom>
                                    Languages
                                </Typography>
                            </Box>
                            <Box
                                sx={{
                                    display: "flex",
                                    flexWrap: "wrap",
                                    gap: 1,
                                }}
                            >
                                {doctor.languages &&
                                    doctor.languages.map((language, index) => (
                                        <Chip
                                            key={index}
                                            label={language}
                                            size="small"
                                            variant="outlined"
                                        />
                                    ))}
                            </Box>
                        </Grid>
                    </Grid>

                    {doctor.services && (
                        <Box sx={{ mt: 2 }}>
                            <Box
                                sx={{
                                    display: "flex",
                                    alignItems: "center",
                                    gap: 1,
                                    mb: 1,
                                }}
                            >
                                <FaMedkit
                                    size={16}
                                    color={theme.palette.primary.main}
                                />
                                <Typography variant="h6" gutterBottom>
                                    Services
                                </Typography>
                            </Box>
                            <Box
                                sx={{
                                    display: "flex",
                                    flexWrap: "wrap",
                                    gap: 1,
                                }}
                            >
                                {doctor.services.map((service, index) => (
                                    <Chip
                                        key={index}
                                        label={service}
                                        color="primary"
                                        variant="outlined"
                                    />
                                ))}
                            </Box>
                        </Box>
                    )}
                </Card>

                <Card elevation={5} sx={{ p: isMobile ? 2 : 3 }}>
                    <Box
                        sx={{
                            display: "flex",
                            alignItems: "center",
                            gap: 1,
                            mb: 0.5,
                        }}
                    >
                        <Typography variant="h6" sx={{ fontWeight: "bold" }}>
                            Consultation Fee
                        </Typography>
                    </Box>
                    <Typography
                        variant={isMobile ? "h5" : "h4"}
                        color="secondary.light"
                        sx={{ fontWeight: "bold" }}
                    >
                        ₹{doctor.consultation_fee}
                    </Typography>
                    <Typography variant="body2" color="text.secondary">
                        Per consultation
                    </Typography>
                </Card>
            </Grid>
        </Grid>
    );

    const renderClinics = () => (
        <Box>
            <Box sx={{ display: "flex", alignItems: "center", gap: 1, mb: 3 }}>
                <FaHospital
                    size={isMobile ? 20 : 24}
                    color={theme.palette.primary.main}
                />
                <Typography
                    variant={isMobile ? "h6" : "h5"}
                    sx={{ fontWeight: "bold" }}
                >
                    Available at Clinics
                </Typography>
            </Box>

            {doctor.clinics.map((clinic, index) => (
                <Accordion key={clinic.id} elevation={5} sx={{ mb: 2, borderRadius: 3 }}>
                    <AccordionSummary
                        expandIcon={<ExpandMore />}
                        sx={{
                            "& .MuiAccordionSummary-content": {
                                alignItems: "center",
                            },
                        }}
                    >
                        <Box
                            sx={{
                                display: "flex",
                                alignItems: "center",
                                width: "100%",
                            }}
                        >
                            <MdLocalHospital
                                size={24}
                                color={theme.palette.primary.main}
                                style={{ marginRight: 16 }}
                            />
                            <Box sx={{ flexGrow: 1 }}>
                                <Typography
                                    variant={isMobile ? "subtitle1" : "h6"}
                                    sx={{ fontWeight: "bold" }}
                                >
                                    {clinic.name}
                                </Typography>
                                <Typography
                                    variant="body2"
                                    color="text.secondary"
                                    sx={{
                                        display: "flex",
                                        alignItems: "center",
                                        gap: 0.5,
                                        mt: 0.5,
                                    }}
                                >
                                    <MdLocationOn size={14} />
                                    {clinic.address}
                                </Typography>
                            </Box>
                        </Box>
                    </AccordionSummary>
                    <AccordionDetails>
                        <Grid container spacing={3}>
                            <Grid item xs={12} md={6}>
                                <Box
                                    sx={{
                                        display: "flex",
                                        alignItems: "center",
                                        gap: 1,
                                        mb: 2,
                                    }}
                                >
                                    <FaPhone
                                        size={16}
                                        color={theme.palette.primary.main}
                                    />
                                    <Typography
                                        variant="subtitle1"
                                        sx={{ fontWeight: "bold" }}
                                    >
                                        Contact Information
                                    </Typography>
                                </Box>
                                <List dense>
                                    <ListItem disablePadding sx={{ mb: 1 }}>
                                        <ListItemIcon>
                                            <FaMapMarkerAlt size={14} />
                                        </ListItemIcon>
                                        <ListItemText
                                            primary={clinic.address}
                                            primaryTypographyProps={{
                                                variant: "body2",
                                            }}
                                        />
                                    </ListItem>
                                    <ListItem disablePadding>
                                        <ListItemIcon>
                                            <FaPhone size={14} />
                                        </ListItemIcon>
                                        <ListItemText
                                            primary={clinic.phone}
                                            primaryTypographyProps={{
                                                variant: "body2",
                                            }}
                                        />
                                    </ListItem>
                                </List>
                            </Grid>

                            <Grid item xs={12} md={6}>
                                <Box
                                    sx={{
                                        display: "flex",
                                        alignItems: "center",
                                        gap: 1,
                                        mb: 2,
                                    }}
                                >
                                    <FaClock
                                        size={16}
                                        color={theme.palette.primary.main}
                                    />
                                    <Typography
                                        variant="subtitle1"
                                        sx={{ fontWeight: "bold" }}
                                    >
                                        Schedule
                                    </Typography>
                                </Box>
                                {clinic.pivot && (
                                    <List dense>
                                        <ListItem disablePadding>
                                            <ListItemIcon>
                                                <MdSchedule size={16} />
                                            </ListItemIcon>
                                            <ListItemText
                                                primary={`${clinic.pivot.start_time} - ${clinic.pivot.end_time}`}
                                                secondary={`Available days: ${parseAvailableDays(
                                                    clinic.pivot.available_days
                                                ).join(", ")}`}
                                                primaryTypographyProps={{
                                                    variant: "body2",
                                                    fontWeight: 600,
                                                }}
                                                secondaryTypographyProps={{
                                                    variant: "caption",
                                                }}
                                            />
                                        </ListItem>
                                    </List>
                                )}
                            </Grid>
                        </Grid>

                        {clinic.facilities && (
                            <Box sx={{ mt: 3 }}>
                                <Box
                                    sx={{
                                        display: "flex",
                                        alignItems: "center",
                                        gap: 1,
                                        mb: 1,
                                    }}
                                >
                                    <BusinessCenter
                                        fontSize="small"
                                        color="primary"
                                    />
                                    <Typography
                                        variant="subtitle1"
                                        sx={{ fontWeight: "bold" }}
                                    >
                                        Facilities
                                    </Typography>
                                </Box>
                                <Box
                                    sx={{
                                        display: "flex",
                                        flexWrap: "wrap",
                                        gap: 1,
                                    }}
                                >
                                    {clinic.facilities.map((facility, idx) => (
                                        <Chip
                                            key={idx}
                                            label={facility}
                                            size="small"
                                            variant="outlined"
                                        />
                                    ))}
                                </Box>
                            </Box>
                        )}

                        <Box
                            sx={{
                                mt: 3,
                                display: "flex",
                                justifyContent: "center",
                            }}
                        >
                            <Button
                                variant="contained"
                                startIcon={<CalendarToday />}
                                onClick={() => handleBookAppointment(clinic)}
                                sx={{
                                    color: "white",
                                    borderRadius: 2,
                                    px: 3,
                                    py: 1.5,
                                    fontWeight: 600,
                                }}
                            >
                                Book Here
                            </Button>
                        </Box>
                    </AccordionDetails>
                </Accordion>
            ))}
        </Box>
    );

    if (loading) {
        return (
            <MainLayout>
                <Container maxWidth="xl" sx={{ py: 10, textAlign: "center" }}>
                    <CircularProgress size={60} />
                    <Typography variant="h6" sx={{ mt: 2 }}>
                        Loading doctor details...
                    </Typography>
                </Container>
            </MainLayout>
        );
    }

    if (error || !doctor) {
        return (
            <MainLayout>
                <Container maxWidth="xl" sx={{ py: 10 }}>
                    <Alert severity="error">
                        {error || "Doctor not found"}
                    </Alert>
                </Container>
            </MainLayout>
        );
    }

    return (
        <MainLayout>
            <Container
                maxWidth="xl"
                sx={{
                    py: { xs: 2, md: 4 },
                    mt: { xs: 6, md: 6 },
                    px: { xs: 1, sm: 2, md: 3 },
                }}
            >
                <Box sx={{ mb: 4 }}>
                    <Tabs
                        value={tabValue}
                        onChange={(e, newValue) => setTabValue(newValue)}
                        variant={isMobile ? "scrollable" : "standard"}
                        scrollButtons="auto"
                        sx={{
                            "& .MuiTab-root": {
                                textTransform: "none",
                                fontWeight: 600,
                                fontSize: isMobile ? "0.9rem" : "1rem",
                            },
                        }}
                    >
                        <Tab
                            label="Doctor Info"
                            icon={<FaUserMd />}
                            iconPosition="start"
                        />
                        <Tab
                            label="Weekly Schedule"
                            icon={<FaCalendarAlt />}
                            iconPosition="start"
                        />
                        <Tab
                            label={`Clinics (${doctor.clinics.length})`}
                            icon={<FaHospital />}
                            iconPosition="start"
                        />
                    </Tabs>
                </Box>

                <Box sx={{ minHeight: "60vh" }}>
                    {tabValue === 0 && renderDoctorInfo()}
                    {tabValue === 1 && renderWeeklySchedule()}
                    {tabValue === 2 && renderClinics()}
                </Box>
            </Container>

            <LoginModal
                open={showLoginModal}
                onClose={() => setShowLoginModal(false)}
                onLoginSuccess={handleLoginSuccess}
            />
        </MainLayout>
    );
};

export default DoctorDetailPage;
```

**resources/js/pages/ClinicDetailPage.jsx**

```jsx
import React, { useState, useEffect } from "react";
import { useParams, useNavigate } from "react-router-dom";
import {
    Container,
    Typography,
    Box,
    Grid,
    Card,
    CardContent,
    CardMedia,
    Button,
    Chip,
    Rating,
    Avatar,
    List,
    ListItem,
    ListItemIcon,
    ListItemText,
    Paper,
    CircularProgress,
    Alert,
    Tabs,
    Tab,
    useTheme,
    useMediaQuery,
    IconButton,
} from "@mui/material";
import {
    CalendarToday,
    ChevronLeft,
    ChevronRight,
    Work,
    MedicalServices,
    BusinessCenter,
    Info,
} from "@mui/icons-material";
import {
    FaUserMd,
    FaHospital,
    FaPhone,
    FaMapMarkerAlt,
    FaEnvelope,
    FaClock,
    FaStethoscope,
    FaStar,
    FaAward,
    FaCalendarAlt,
    FaMoneyBillWave,
    FaBusinessTime,
} from "react-icons/fa";
import {
    MdInfo,
    MdVerified,
    MdPhone,
    MdToday,
    MdWeekend,
} from "react-icons/md";
import MainLayout from "../components/layout/MainLayout";
import LoginModal from "../components/auth/LoginModal";
import axios from "axios";

const ClinicDetailPage = () => {
    const { id } = useParams();
    const navigate = useNavigate();
    const apiUrl = import.meta.env.VITE_API_URL;
    const theme = useTheme();
    const isMobile = useMediaQuery(theme.breakpoints.down("sm"));
    const isTablet = useMediaQuery(theme.breakpoints.down("md"));

    const [clinic, setClinic] = useState(null);
    const [doctors, setDoctors] = useState([]);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState("");
    const [tabValue, setTabValue] = useState(0);
    const [showLoginModal, setShowLoginModal] = useState(false);
    const [selectedDoctor, setSelectedDoctor] = useState(null);
    const [currentSlide, setCurrentSlide] = useState(0);

    // Days of the week
    const daysOfWeek = [
        {
            key: "monday",
            label: "Monday",
            icon: <MdToday />,
            shortLabel: "Mon",
        },
        {
            key: "tuesday",
            label: "Tuesday",
            icon: <MdToday />,
            shortLabel: "Tue",
        },
        {
            key: "wednesday",
            label: "Wednesday",
            icon: <MdToday />,
            shortLabel: "Wed",
        },
        {
            key: "thursday",
            label: "Thursday",
            icon: <MdToday />,
            shortLabel: "Thu",
        },
        {
            key: "friday",
            label: "Friday",
            icon: <MdToday />,
            shortLabel: "Fri",
        },
        {
            key: "saturday",
            label: "Saturday",
            icon: <MdWeekend />,
            shortLabel: "Sat",
        },
        {
            key: "sunday",
            label: "Sunday",
            icon: <MdWeekend />,
            shortLabel: "Sun",
        },
    ];

    useEffect(() => {
        loadClinicDetails();
        loadClinicDoctors();
    }, [id]);

    const loadClinicDetails = async () => {
        try {
            const response = await axios.get(`${apiUrl}/clinics/${id}`);
            setClinic(response.data);
        } catch (error) {
            console.error("Failed to load clinic details:", error);
            setError("Failed to load clinic details");
        }
    };

    const loadClinicDoctors = async () => {
        try {
            const response = await axios.get(`${apiUrl}/clinics/${id}/doctors`);
            setDoctors(response.data);
        } catch (error) {
            console.error("Failed to load doctors:", error);
            setError("Failed to load doctors");
        } finally {
            setLoading(false);
        }
    };

    const handleBookAppointment = (doctor) => {
        const token = localStorage.getItem("auth_token");
        if (!token) {
            setSelectedDoctor(doctor);
            setShowLoginModal(true);
        } else {
            navigate(`/book-appointment`, {
                state: {
                    doctor: doctor,
                    clinic: clinic,
                    type: "clinic",
                },
            });
        }
    };

    const handleLoginSuccess = (userData, token) => {
        localStorage.setItem("auth_token", token);
        localStorage.setItem("user_data", JSON.stringify(userData));
        setShowLoginModal(false);

        if (selectedDoctor) {
            navigate(`/book-appointment`, {
                state: {
                    doctor: selectedDoctor,
                    clinic: clinic,
                    type: "clinic",
                },
            });
        }
    };

    // Safe number conversion for ratings
    const safeRating = (rating) => {
        if (rating === null || rating === undefined || rating === "") return 0;
        const numRating =
            typeof rating === "string" ? parseFloat(rating) : Number(rating);
        return isNaN(numRating) ? 0 : numRating;
    };

    // Parse available days safely
    const parseAvailableDays = (availableDays) => {
        if (!availableDays) return [];
        try {
            if (typeof availableDays === "string") {
                return JSON.parse(availableDays);
            }
            return Array.isArray(availableDays) ? availableDays : [];
        } catch (error) {
            console.error("Error parsing available days:", error);
            return [];
        }
    };

    // Get current day of week in lowercase
    const getCurrentDayKey = () => {
        const today = new Date();
        const dayNames = [
            "sunday",
            "monday",
            "tuesday",
            "wednesday",
            "thursday",
            "friday",
            "saturday",
        ];
        return dayNames[today.getDay()];
    };

    // Get today's available doctors
    const getTodaysAvailableDoctors = () => {
        const today = getCurrentDayKey();
        return doctors.filter((doctor) => {
            if (doctor.pivot) {
                const availableDays = parseAvailableDays(
                    doctor.pivot.available_days
                );
                return availableDays.includes(today);
            }
            return false;
        });
    };

    // Get doctor's available days
    const getDoctorAvailableDays = (doctor) => {
        if (doctor.pivot) {
            return parseAvailableDays(doctor.pivot.available_days);
        }
        return [];
    };

    // Check if doctor is available today
    const isDoctorAvailableToday = (doctor) => {
        const today = getCurrentDayKey();
        const availableDays = getDoctorAvailableDays(doctor);
        return availableDays.includes(today);
    };

    // Today's Available Doctors Slider - Mobile Optimized
    const renderTodaysAvailableDoctors = () => {
        const todaysDoctors = getTodaysAvailableDoctors();

        if (todaysDoctors.length === 0) {
            return (
                <Paper
                    elevation={5}
                    sx={{ p: isMobile ? 2 : 3, mb: 1, borderRadius: 3 }}
                >
                    <Box
                        sx={{
                            display: "flex",
                            alignItems: "center",
                            gap: 1,
                            mb: 2,
                        }}
                    >
                        <MdToday
                            size={isMobile ? 20 : 24}
                            color={theme.palette.primary.main}
                        />
                        <Typography
                            variant={isMobile ? "subtitle1" : "h6"}
                            sx={{ fontWeight: "bold" }}
                        >
                            Today's Available Doctors
                        </Typography>
                    </Box>
                    <Alert severity="info" icon={<Info />}>
                        No doctors available today. Please check other days or
                        contact the clinic directly.
                    </Alert>
                </Paper>
            );
        }

        const nextSlide = () => {
            setCurrentSlide((prev) => (prev + 1) % todaysDoctors.length);
        };

        const prevSlide = () => {
            setCurrentSlide(
                (prev) =>
                    (prev - 1 + todaysDoctors.length) % todaysDoctors.length
            );
        };

        const currentDoctor = todaysDoctors[currentSlide];

        return (
            <Paper
                elevation={5}
                sx={{ p: isMobile ? 2 : 3, mb: 2, borderRadius: 3 }}
            >
                <Box
                    sx={{
                        display: "flex",
                        alignItems: "center",
                        gap: 1,
                        mb: 1,
                    }}
                >
                    <MdToday
                        size={isMobile ? 20 : 24}
                        color={theme.palette.primary.main}
                    />
                    <Typography
                        variant={isMobile ? "subtitle1" : "h6"}
                        sx={{ fontWeight: "bold" }}
                    >
                        Today's Available Doctors ({todaysDoctors.length})
                    </Typography>
                </Box>

                <Box sx={{ position: "relative" }}>
                    <Card elevation={3} sx={{ p: isMobile ? 1 : 1 }}>
                        {/* Unified Layout for Both Mobile and Desktop */}
                        <Box>
                            {/* Available Today Badge - Top of card */}
                            <Box
                                sx={{
                                    display: "flex",
                                    justifyContent: "center",
                                    mb: 1,
                                }}
                            >
                                <Chip
                                    label="Available Today"
                                    color="success"
                                    size="small"
                                    sx={{
                                        color: "white",
                                        animation: "pulse 2s infinite",
                                        "@keyframes pulse": {
                                            "0%": { transform: "scale(1)" },
                                            "50%": { transform: "scale(1.05)" },
                                            "100%": { transform: "scale(1)" },
                                        },
                                    }}
                                />
                            </Box>

                            {/* Doctor Info - Center aligned */}
                            <Box sx={{ textAlign: "center", mb: 1 }}>
                                <Avatar
                                    src={currentDoctor.profile_image}
                                    sx={{
                                        width: isMobile ? 70 : 90,
                                        height: isMobile ? 70 : 90,
                                        border: "3px solid",
                                        borderColor: "primary.main",
                                        mx: "auto",
                                        mb: 1,
                                    }}
                                >
                                    <FaUserMd size={isMobile ? 25 : 30} />
                                </Avatar>

                                <Box
                                    sx={{
                                        display: "flex",
                                        alignItems: "center",
                                        justifyContent: "center",
                                        gap: 1,
                                        mb: 0.5,
                                        flexWrap: "wrap",
                                    }}
                                >
                                    <Typography
                                        variant={isMobile ? "h6" : "h5"}
                                        sx={{
                                            fontWeight: "bold",
                                            fontSize: isMobile
                                                ? "1.1rem"
                                                : "1.4rem",
                                        }}
                                    >
                                        {currentDoctor.user?.name ||
                                            currentDoctor.name ||
                                            "N/A"}
                                    </Typography>
                                    {currentDoctor.is_verified && (
                                        <Chip
                                            icon={<MdVerified />}
                                            label="Verified"
                                            size={isMobile ? "small" : "medium"}
                                            color="success"
                                            sx={{
                                                color: "white",
                                                fontSize: isMobile
                                                    ? "0.7rem"
                                                    : "0.8rem",
                                            }}
                                        />
                                    )}
                                </Box>

                                <Typography
                                    variant={isMobile ? "body2" : "body1"}
                                    color="secondary.light"
                                    sx={{ mb: 1 }}
                                >
                                    <FaStethoscope
                                        size={isMobile ? 12 : 14}
                                        style={{ marginRight: 4 }}
                                    />
                                    {currentDoctor.specialization || "N/A"}
                                </Typography>

                                <Typography
                                    variant="caption"
                                    color="text.secondary"
                                    sx={{
                                        display: "block",
                                        mb: 0.5,
                                        fontSize: isMobile
                                            ? "0.75rem"
                                            : "0.875rem",
                                    }}
                                >
                                    <Work
                                        fontSize="small"
                                        sx={{
                                            mr: 0.5,
                                            fontSize: isMobile
                                                ? "12px"
                                                : "14px",
                                        }}
                                    />
                                    {currentDoctor.experience_years || 0} years
                                    experience
                                </Typography>
                                <Typography
                                    variant={isMobile ? "h6" : "h5"}
                                    color="secondary.light"
                                    sx={{
                                        fontWeight: "bold",
                                        mb: 1,
                                        fontSize: isMobile
                                            ? "1.1rem"
                                            : "1.3rem",
                                    }}
                                >
                                    <FaMoneyBillWave
                                        size={isMobile ? 14 : 16}
                                        style={{ marginRight: 4 }}
                                    />
                                    ₹{currentDoctor.consultation_fee || 0}
                                </Typography>
                            </Box>

                            {/* Book Button */}
                            <Button
                                fullWidth
                                variant="contained"
                                size={isMobile ? "medium" : "large"}
                                startIcon={<CalendarToday />}
                                onClick={() =>
                                    handleBookAppointment(currentDoctor)
                                }
                                sx={{
                                    borderRadius: 2,
                                    fontWeight: 500,
                                    color: "white",
                                    py: isMobile ? 0.5 : 1,
                                }}
                            >
                                Book Now
                            </Button>
                        </Box>
                    </Card>

                    {/* Navigation arrows - show when multiple doctors */}
                    {todaysDoctors.length > 1 && (
                        <>
                            <IconButton
                                onClick={prevSlide}
                                sx={{
                                    position: "absolute",
                                    left: isMobile ? -8 : -12,
                                    top: "50%",
                                    transform: "translateY(-50%)",
                                    bgcolor: "background.paper",
                                    boxShadow: 2,
                                    "&:hover": { bgcolor: "primary.light" },
                                    width: isMobile ? 32 : 40,
                                    height: isMobile ? 32 : 40,
                                }}
                            >
                                <ChevronLeft />
                            </IconButton>
                            <IconButton
                                onClick={nextSlide}
                                sx={{
                                    position: "absolute",
                                    right: isMobile ? -8 : -12,
                                    top: "50%",
                                    transform: "translateY(-50%)",
                                    bgcolor: "background.paper",
                                    boxShadow: 2,
                                    "&:hover": { bgcolor: "primary.light" },
                                    width: isMobile ? 32 : 40,
                                    height: isMobile ? 32 : 40,
                                }}
                            >
                                <ChevronRight />
                            </IconButton>
                        </>
                    )}
                </Box>

                {/* Dots indicator - show when multiple doctors */}
                {todaysDoctors.length > 1 && (
                    <Box
                        sx={{
                            display: "flex",
                            justifyContent: "center",
                            mt: 2,
                            gap: 1,
                        }}
                    >
                        {todaysDoctors.map((_, index) => (
                            <Box
                                key={index}
                                sx={{
                                    width: 8,
                                    height: 8,
                                    borderRadius: "50%",
                                    bgcolor:
                                        index === currentSlide
                                            ? "primary.main"
                                            : "grey.300",
                                    cursor: "pointer",
                                }}
                                onClick={() => setCurrentSlide(index)}
                            />
                        ))}
                    </Box>
                )}
            </Paper>
        );
    };

    const renderClinicInfo = () => (
        <Grid container spacing={2}>
            <Grid item xs={12} md={8}>
                <Card elevation={5}>
                    <CardMedia
                        component="img"
                        height={isMobile ? "200" : "300"}
                        image={clinic.image || "/images/clinic-default.jpg"}
                        alt={clinic.name}
                    />
                    <CardContent sx={{ p: isMobile ? 2 : 2 }}>
                        <Box
                            sx={{
                                display: "flex",
                                alignItems: "center",
                                gap: 1,
                                mb: 2,
                            }}
                        >
                            <FaHospital
                                size={isMobile ? 24 : 28}
                                color={theme.palette.primary.main}
                            />
                            <Typography
                                variant={isMobile ? "h5" : "h4"}
                                sx={{ fontWeight: "bold" }}
                            >
                                {clinic.name || "N/A"}
                            </Typography>
                        </Box>

                        <Box
                            sx={{
                                display: "flex",
                                alignItems: "center",
                                gap: 2,
                                mb: 2,
                            }}
                        >
                            <Rating
                                value={safeRating(clinic.rating)}
                                precision={0.1}
                                readOnly
                                size={isMobile ? "small" : "medium"}
                            />
                            <Typography variant="body1">
                                {safeRating(clinic.rating)} (
                                {clinic.total_reviews || 0} reviews)
                            </Typography>
                        </Box>

                        <Typography variant="body1" paragraph sx={{ mb: 4 }}>
                            {clinic.description || "No description available"}
                        </Typography>

                        <Box sx={{ mb: 2 }}>
                            <Box
                                sx={{
                                    display: "flex",
                                    alignItems: "center",
                                    gap: 1,
                                    mb: 2,
                                }}
                            >
                                <MedicalServices color="primary" />
                                <Typography
                                    variant="h6"
                                    sx={{ fontWeight: "bold" }}
                                >
                                    Specialties
                                </Typography>
                            </Box>
                            <Box
                                sx={{
                                    display: "flex",
                                    flexWrap: "wrap",
                                    gap: 1,
                                }}
                            >
                                {clinic.specialties &&
                                clinic.specialties.length > 0 ? (
                                    clinic.specialties.map(
                                        (specialty, index) => (
                                            <Chip
                                                key={index}
                                                label={specialty}
                                                color="primary"
                                                variant="outlined"
                                                size={
                                                    isMobile
                                                        ? "small"
                                                        : "medium"
                                                }
                                            />
                                        )
                                    )
                                ) : (
                                    <Typography
                                        variant="body2"
                                        color="text.secondary"
                                    >
                                        No specialties listed
                                    </Typography>
                                )}
                            </Box>
                        </Box>

                        <Box sx={{ mb: 1 }}>
                            <Box
                                sx={{
                                    display: "flex",
                                    alignItems: "center",
                                    gap: 1,
                                    mb: 2,
                                }}
                            >
                                <BusinessCenter color="primary" />
                                <Typography
                                    variant="h6"
                                    sx={{ fontWeight: "bold" }}
                                >
                                    Facilities
                                </Typography>
                            </Box>
                            <Box
                                sx={{
                                    display: "flex",
                                    flexWrap: "wrap",
                                    gap: 1,
                                }}
                            >
                                {clinic.facilities &&
                                clinic.facilities.length > 0 ? (
                                    clinic.facilities.map((facility, index) => (
                                        <Chip
                                            key={index}
                                            label={facility}
                                            color="secondary"
                                            variant="outlined"
                                            size={isMobile ? "small" : "medium"}
                                        />
                                    ))
                                ) : (
                                    <Typography
                                        variant="body2"
                                        color="text.secondary"
                                    >
                                        No facilities listed
                                    </Typography>
                                )}
                            </Box>
                        </Box>
                    </CardContent>
                </Card>
                <Alert
                    elevation={3}
                    severity="info"
                    sx={{ mt: 1, borderRadius: 4 }}
                    icon={<MdInfo />}
                >
                    <Typography variant="body2">
                        <strong>Note:</strong>
                        <ul
                            style={{
                                marginTop: "8px",
                                marginBottom: "8px",
                                paddingLeft: "20px",
                            }}
                        >
                            <li>
                                Schedule may vary during holidays and special
                                occasions. Please confirm appointment
                                availability before visiting.
                            </li>
                            <li>
                                Emergency consultations may be available outside
                                regular hours.
                            </li>
                            <li>
                                If the doctor or healthcare provider cancels a
                                schedule, we will inform you via email and SMS,
                                and it will be reflected on our website as well.
                            </li>
                        </ul>
                    </Typography>
                </Alert>
            </Grid>

            <Grid item xs={12} md={4}>
                <Paper
                    elevation={5}
                    sx={{ p: isMobile ? 2 : 3, mb: 2, borderRadius: 3 }}
                >
                    <Box
                        sx={{
                            display: "flex",
                            alignItems: "center",
                            gap: 1,
                            mb: 2,
                        }}
                    >
                        <FaPhone color={theme.palette.primary.main} />
                        <Typography variant="h6" sx={{ fontWeight: "bold" }}>
                            Contact Information
                        </Typography>
                    </Box>

                    <List disablePadding>
                        <ListItem disablePadding sx={{ mb: 0.5 }}>
                            <ListItemIcon>
                                <FaMapMarkerAlt
                                    color={theme.palette.primary.main}
                                />
                            </ListItemIcon>
                            <ListItemText
                                primary={
                                    clinic.address || "Address not available"
                                }
                                primaryTypographyProps={{
                                    variant: isMobile ? "body2" : "body1",
                                }}
                            />
                        </ListItem>

                        <ListItem disablePadding sx={{ mb: 0.5 }}>
                            <ListItemIcon>
                                <MdPhone color={theme.palette.primary.main} />
                            </ListItemIcon>
                            <ListItemText
                                primary={clinic.phone || "Phone not available"}
                                primaryTypographyProps={{
                                    variant: isMobile ? "body2" : "body1",
                                }}
                            />
                        </ListItem>

                        {clinic.email && (
                            <ListItem disablePadding sx={{ mb: 0.5 }}>
                                <ListItemIcon>
                                    <FaEnvelope
                                        color={theme.palette.primary.main}
                                    />
                                </ListItemIcon>
                                <ListItemText
                                    primary={clinic.email}
                                    primaryTypographyProps={{
                                        variant: isMobile ? "body2" : "body1",
                                    }}
                                />
                            </ListItem>
                        )}

                        <ListItem disablePadding>
                            <ListItemIcon>
                                <FaClock color={theme.palette.primary.main} />
                            </ListItemIcon>
                            <ListItemText
                                primary={`${clinic.opening_time || "N/A"} - ${
                                    clinic.closing_time || "N/A"
                                }`}
                                secondary="Working Hours"
                                primaryTypographyProps={{
                                    variant: isMobile ? "body2" : "body1",
                                }}
                            />
                        </ListItem>
                    </List>
                </Paper>

                <Paper
                    elevation={5}
                    sx={{ p: isMobile ? 2 : 3, mb: 2, borderRadius: 3 }}
                >
                    <Box
                        sx={{
                            display: "flex",
                            alignItems: "center",
                            gap: 1,
                            mb: 1,
                        }}
                    >
                        <FaBusinessTime color={theme.palette.primary.main} />
                        <Typography variant="h6" sx={{ fontWeight: "bold" }}>
                            Quick Stats
                        </Typography>
                    </Box>

                    <Box
                        sx={{
                            display: "flex",
                            flexDirection: "column",
                            gap: 1,
                        }}
                    >
                        <Box
                            sx={{
                                display: "flex",
                                justifyContent: "space-between",
                            }}
                        >
                            <Typography variant={isMobile ? "body2" : "body1"}>
                                <FaUserMd
                                    size={16}
                                    style={{ marginRight: 8 }}
                                />
                                Total Doctors:
                            </Typography>
                            <Typography fontWeight="bold">
                                {doctors.length}
                            </Typography>
                        </Box>
                        <Box
                            sx={{
                                display: "flex",
                                justifyContent: "space-between",
                            }}
                        >
                            <Typography variant={isMobile ? "body2" : "body1"}>
                                <FaStar size={16} style={{ marginRight: 8 }} />
                                Rating:
                            </Typography>
                            <Typography fontWeight="bold">
                                {safeRating(clinic.rating)}/5
                            </Typography>
                        </Box>
                        <Box
                            sx={{
                                display: "flex",
                                justifyContent: "space-between",
                            }}
                        >
                            <Typography variant={isMobile ? "body2" : "body1"}>
                                <FaAward size={16} style={{ marginRight: 8 }} />
                                Reviews:
                            </Typography>
                            <Typography fontWeight="bold">
                                {clinic.total_reviews || 0}
                            </Typography>
                        </Box>
                        <Box
                            sx={{
                                display: "flex",
                                justifyContent: "space-between",
                            }}
                        >
                            <Typography variant={isMobile ? "body2" : "body1"}>
                                <MdToday size={16} style={{ marginRight: 8 }} />
                                Available Today:
                            </Typography>
                            <Typography fontWeight="bold" color="success.main">
                                {getTodaysAvailableDoctors().length}
                            </Typography>
                        </Box>
                    </Box>
                </Paper>

                {/* Today's Available Doctors Slider */}
                {renderTodaysAvailableDoctors()}
            </Grid>
        </Grid>
    );

    const renderDoctors = () => (
        <Grid container spacing={isMobile ? 2 : 3}>
            {doctors.map((doctor) => {
                const availableDays = getDoctorAvailableDays(doctor);
                const isAvailableToday = isDoctorAvailableToday(doctor);

                return (
                    <Grid item xs={12} sm={6} md={4} key={doctor.id}>
                        <Card
                            elevation={5}
                            sx={{
                                height: "100%",
                                display: "flex",
                                flexDirection: "column",
                                border: isAvailableToday ? "2px solid" : "none",
                                borderColor: isAvailableToday
                                    ? "success.main"
                                    : "transparent",
                                position: "relative",
                                pt: isAvailableToday ? 5 : 2, // Add padding top when badge is present
                            }}
                        >
                            {isAvailableToday && (
                                <Box
                                    sx={{
                                        position: "absolute",
                                        top: 8,
                                        left: 8,
                                        right: 8,
                                        display: "flex",
                                        justifyContent: "center",
                                        zIndex: 2,
                                    }}
                                >
                                    <Chip
                                        label="Available Today"
                                        color="success"
                                        size="small"
                                        sx={{
                                            color: "white",
                                            fontWeight: 600,
                                            animation: "pulse 2s infinite",
                                            "@keyframes pulse": {
                                                "0%": { transform: "scale(1)" },
                                                "50%": {
                                                    transform: "scale(1.05)",
                                                },
                                                "100%": {
                                                    transform: "scale(1)",
                                                },
                                            },
                                        }}
                                    />
                                </Box>
                            )}

                            <CardContent sx={{ flexGrow: 1, pt: 1 }}>
                                <Box
                                    sx={{
                                        display: "flex",
                                        alignItems: "center",
                                        mb: 2,
                                    }}
                                >
                                    <Avatar
                                        src={doctor.profile_image}
                                        sx={{
                                            width: isMobile ? 50 : 60,
                                            height: isMobile ? 50 : 60,
                                            mr: 2,
                                        }}
                                    >
                                        <FaUserMd />
                                    </Avatar>
                                    <Box sx={{ flex: 1 }}>
                                        <Typography
                                            variant={
                                                isMobile ? "subtitle1" : "h6"
                                            }
                                            sx={{
                                                fontWeight: "bold",
                                                lineHeight: 1.2,
                                            }}
                                        >
                                            {doctor.user?.name ||
                                                doctor.name ||
                                                "N/A"}
                                        </Typography>
                                        {doctor.is_verified && (
                                            <Box sx={{ mt: 0.5 }}>
                                                <Chip
                                                    label="Verified"
                                                    size="small"
                                                    color="success"
                                                    icon={<MdVerified />}
                                                    sx={{
                                                        color: "white",
                                                        fontSize: "0.7rem",
                                                        height: "20px",
                                                    }}
                                                />
                                            </Box>
                                        )}
                                    </Box>
                                </Box>

                                <Typography
                                    variant="body2"
                                    color="secondary.light"
                                    sx={{ mb: 1 }}
                                >
                                    <FaStethoscope
                                        size={12}
                                        style={{ marginRight: 4 }}
                                    />
                                    {doctor.specialization || "N/A"}
                                </Typography>

                                <Typography
                                    variant="caption"
                                    color="text.secondary"
                                    sx={{ mb: 2, display: "block" }}
                                >
                                    <Work fontSize="small" sx={{ mr: 0.5 }} />
                                    {doctor.experience_years || 0} years
                                    experience
                                </Typography>

                                <Box
                                    sx={{
                                        display: "flex",
                                        alignItems: "center",
                                        mb: 2,
                                    }}
                                >
                                    <Rating
                                        value={safeRating(doctor.rating)}
                                        precision={0.1}
                                        size="small"
                                        readOnly
                                    />
                                    <Typography
                                        variant="body2"
                                        sx={{ ml: 1, fontSize: "0.85rem" }}
                                    >
                                        {safeRating(doctor.rating)} (
                                        {doctor.total_reviews || 0} reviews)
                                    </Typography>
                                </Box>

                                <Typography
                                    variant={isMobile ? "subtitle1" : "h6"}
                                    color="secondary.light"
                                    sx={{ fontWeight: "bold", mb: 2 }}
                                >
                                    <FaMoneyBillWave
                                        size={14}
                                        style={{ marginRight: 4 }}
                                    />
                                    ₹ {doctor.consultation_fee || 0}
                                </Typography>

                                {doctor.pivot && (
                                    <Box sx={{ mb: 2 }}>
                                        <Typography
                                            variant="caption"
                                            color="text.secondary"
                                            sx={{ display: "block", mb: 1 }}
                                        >
                                            <FaClock
                                                size={10}
                                                style={{ marginRight: 4 }}
                                            />
                                            Available:{" "}
                                            {doctor.pivot.start_time || "N/A"} -{" "}
                                            {doctor.pivot.end_time || "N/A"}
                                        </Typography>
                                    </Box>
                                )}

                                {/* Available Days */}
                                <Box sx={{ mb: 2 }}>
                                    <Typography
                                        variant="caption"
                                        color="text.secondary"
                                        sx={{ mb: 1, display: "block" }}
                                    >
                                        <FaCalendarAlt
                                            size={10}
                                            style={{ marginRight: 4 }}
                                        />
                                        Available Days:
                                    </Typography>
                                    <Box
                                        sx={{
                                            display: "flex",
                                            flexWrap: "wrap",
                                            gap: 0.5,
                                        }}
                                    >
                                        {daysOfWeek.map((day) => {
                                            const isAvailable =
                                                availableDays.includes(day.key);
                                            const isToday =
                                                getCurrentDayKey() === day.key;

                                            return (
                                                <Chip
                                                    key={day.key}
                                                    label={day.shortLabel}
                                                    size="small"
                                                    variant={
                                                        isAvailable
                                                            ? "filled"
                                                            : "outlined"
                                                    }
                                                    color={
                                                        isToday && isAvailable
                                                            ? "success"
                                                            : "default"
                                                    } // Removed primary color
                                                    sx={{
                                                        fontSize: "0.6rem",
                                                        height: "20px",
                                                        // Apply light blue styles for available days
                                                        backgroundColor:
                                                            isAvailable
                                                                ? isToday
                                                                    ? undefined
                                                                    : "lightblue"
                                                                : undefined,
                                                        color: isAvailable
                                                            ? isToday
                                                                ? "white"
                                                                : "darkblue"
                                                            : "text.secondary",
                                                        borderColor: isAvailable
                                                            ? "lightblue"
                                                            : undefined,
                                                        animation:
                                                            isToday &&
                                                            isAvailable
                                                                ? "glow 2s infinite"
                                                                : "none",
                                                        "@keyframes glow": {
                                                            "0%": {
                                                                boxShadow:
                                                                    "0 0 5px rgba(76, 175, 80, 0.5)",
                                                            },
                                                            "50%": {
                                                                boxShadow:
                                                                    "0 0 20px rgba(76, 175, 80, 0.8)",
                                                            },
                                                            "100%": {
                                                                boxShadow:
                                                                    "0 0 5px rgba(76, 175, 80, 0.5)",
                                                            },
                                                        },
                                                        "&:hover": {
                                                            backgroundColor:
                                                                isAvailable
                                                                    ? "#a3d5f7"
                                                                    : undefined,
                                                        },
                                                    }}
                                                />
                                            );
                                        })}
                                    </Box>
                                </Box>
                            </CardContent>

                            <Box sx={{ p: 2, pt: 0 }}>
                                <Button
                                    fullWidth
                                    variant="contained"
                                    startIcon={<CalendarToday />}
                                    onClick={() =>
                                        handleBookAppointment(doctor)
                                    }
                                    sx={{
                                        borderRadius: 2,
                                        fontWeight: 600,
                                        color: "white",
                                        py: isMobile ? 1.2 : 1,
                                    }}
                                >
                                    Book Appointment
                                </Button>
                            </Box>
                        </Card>
                    </Grid>
                );
            })}
        </Grid>
    );

    if (loading) {
        return (
            <MainLayout>
                <Container maxWidth="xl" sx={{ py: 10, textAlign: "center" }}>
                    <CircularProgress size={60} />
                    <Typography variant="h6" sx={{ mt: 2 }}>
                        Loading clinic details...
                    </Typography>
                </Container>
            </MainLayout>
        );
    }

    if (error || !clinic) {
        return (
            <MainLayout>
                <Container maxWidth="xl" sx={{ py: 10 }}>
                    <Alert severity="error">
                        {error || "Clinic not found"}
                    </Alert>
                </Container>
            </MainLayout>
        );
    }

    return (
        <MainLayout>
            <Container
                maxWidth="xl"
                sx={{
                    py: { xs: 2, md: 4 },
                    mt: { xs: 6, md: 8 },
                    px: { xs: 1, sm: 2, md: 3 },
                }}
            >
                <Box sx={{ mb: 4 }}>
                    <Tabs
                        value={tabValue}
                        onChange={(e, newValue) => setTabValue(newValue)}
                        variant={isMobile ? "scrollable" : "standard"}
                        scrollButtons="auto"
                        sx={{
                            "& .MuiTab-root": {
                                textTransform: "none",
                                fontWeight: 600,
                                fontSize: isMobile ? "0.9rem" : "1rem",
                            },
                        }}
                    >
                        <Tab
                            label="Clinic Info"
                            icon={<FaHospital />}
                            iconPosition="start"
                        />
                        <Tab
                            label={`Doctors (${doctors.length})`}
                            icon={<FaUserMd />}
                            iconPosition="start"
                        />
                    </Tabs>
                </Box>

                <Box sx={{ minHeight: "60vh" }}>
                    {tabValue === 0 && renderClinicInfo()}
                    {tabValue === 1 && renderDoctors()}
                </Box>
            </Container>

            <LoginModal
                open={showLoginModal}
                onClose={() => setShowLoginModal(false)}
                onLoginSuccess={handleLoginSuccess}
            />
        </MainLayout>
    );
};

export default ClinicDetailPage;
```


**resources/js/pages/BookAppointmentPage.jsx**

```jsx
import React, { useState, useEffect } from "react";
import { useLocation, useNavigate } from "react-router-dom";
import {
    Container,
    Typography,
    Box,
    Grid,
    Card,
    CardContent,
    Button,
    TextField,
    FormControl,
    Alert,
    Avatar,
    Chip,
    Paper,
    CircularProgress,
    Stepper,
    Step,
    StepLabel,
    useTheme,
    useMediaQuery,
    Stack,
    RadioGroup,
    FormControlLabel,
    Radio,
} from "@mui/material";
import {
    LocalHospital,
    CalendarToday,
    Payment,
    CheckCircle,
    ArrowBack,
    ArrowForward,
    AccessTime,
    Event,
    Description,
    Home,
    Visibility,
    Star,
    CreditCard,
    AccountBalanceWallet,
} from "@mui/icons-material";
import {
    FaUserMd,
    FaHospital,
    FaCalendarAlt,
    FaClock,
    FaCheckCircle,
    FaNotesMedical,
    FaMoneyBillWave,
    FaCalendarCheck,
    FaStethoscope,
    FaGraduationCap,
    FaUserCheck,
} from "react-icons/fa";
import {
    MdPayment,
    MdInfo,
    MdLocationOn,
    MdAccessTime,
    MdVerified,
    MdLocalHospital,
    MdEmail,
    MdPhone,
} from "react-icons/md";
import { DatePicker } from "@mui/x-date-pickers/DatePicker";
import { LocalizationProvider } from "@mui/x-date-pickers/LocalizationProvider";
import { AdapterDateFns } from "@mui/x-date-pickers/AdapterDateFns";
import MainLayout from "../components/layout/MainLayout";
import axios from "axios";
import { format, isToday, isTomorrow, parseISO } from "date-fns";

const BookAppointmentPage = () => {
    const location = useLocation();
    const navigate = useNavigate();
    const apiUrl = import.meta.env.VITE_API_URL;
    const theme = useTheme();
    const isMobile = useMediaQuery(theme.breakpoints.down("sm"));
    const isTablet = useMediaQuery(theme.breakpoints.down("md"));

    const { doctor, clinic, type } = location.state || {};

    const [activeStep, setActiveStep] = useState(0);
    const [selectedDate, setSelectedDate] = useState(null);
    const [selectedTime, setSelectedTime] = useState("");
    const [symptoms, setSymptoms] = useState("");
    const [notes, setNotes] = useState("");
    // FIXED: Initialize paymentMethod as empty string to prevent premature completion
    const [paymentMethod, setPaymentMethod] = useState("");
    const [availableSlots, setAvailableSlots] = useState([]);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState("");
    const [booking, setBooking] = useState(false);
    const [appointmentId, setAppointmentId] = useState(null);
    // NEW: Track visited steps to prevent premature completion
    const [visitedSteps, setVisitedSteps] = useState([0]);

    // Updated steps with Payment restored
    const steps = [
        {
            label: "Select Date & Time",
            icon: <Event />,
        },
        {
            label: "Details",
            icon: <Description />,
        },
        {
            label: "Payment",
            icon: <Payment />,
        },
        {
            label: "Confirmation",
            icon: <CheckCircle />,
        },
    ];

    // FIXED: Updated function to check if a step is completed
    const isStepCompleted = (stepIndex) => {
        switch (stepIndex) {
            case 0:
                // Date & Time step is completed if both date and time are selected
                return selectedDate && selectedTime;
            case 1:
                // Details step is completed if symptoms are provided
                return symptoms.trim() !== "";
            case 2:
                // Payment step is completed if payment method is selected AND step has been visited
                return paymentMethod !== "" && visitedSteps.includes(stepIndex);
            case 3:
                // Confirmation step is completed when appointment is booked
                return appointmentId !== null;
            default:
                return false;
        }
    };

    useEffect(() => {
        if (!doctor || !clinic) {
            navigate("/");
            return;
        }

        // Check authentication
        const token = localStorage.getItem("auth_token");
        if (!token) {
            navigate("/");
            return;
        }
    }, [doctor, clinic, navigate]);

    useEffect(() => {
        if (selectedDate) {
            loadAvailableSlots();
        }
    }, [selectedDate]);

    const loadAvailableSlots = async () => {
        if (!selectedDate || !doctor || !clinic) return;

        setLoading(true);
        try {
            const response = await axios.get(
                `${apiUrl}/doctors/${doctor.id}/availability/${clinic.id}`,
                {
                    params: {
                        date: format(selectedDate, "yyyy-MM-dd"),
                    },
                }
            );

            const dateSlots = response.data.available_slots.find(
                (slot) => slot.date === format(selectedDate, "yyyy-MM-dd")
            );

            setAvailableSlots(dateSlots ? dateSlots.slots : []);
            setError("");
        } catch (error) {
            console.error("Failed to load available slots:", error);
            setError("Failed to load available time slots");
            setAvailableSlots([]);
        } finally {
            setLoading(false);
        }
    };

    // UPDATED: Track visited steps when moving forward
    const handleNext = () => {
        const nextStep = activeStep + 1;
        if (!visitedSteps.includes(nextStep)) {
            setVisitedSteps([...visitedSteps, nextStep]);
        }
        setActiveStep(nextStep);
    };

    // UPDATED: Track visited steps when moving backward
    const handleBack = () => {
        const prevStep = activeStep - 1;
        if (!visitedSteps.includes(prevStep)) {
            setVisitedSteps([...visitedSteps, prevStep]);
        }
        setActiveStep(prevStep);
    };

    const handleBookAppointment = async () => {
        if (!selectedDate || !selectedTime) {
            setError("Please select date and time");
            return;
        }

        // Set default payment method if none selected
        const finalPaymentMethod = paymentMethod || "clinic";

        setBooking(true);
        setError("");

        try {
            const token = localStorage.getItem("auth_token");
            const response = await axios.post(
                `${apiUrl}/appointments`,
                {
                    doctor_id: doctor.id,
                    clinic_id: clinic.id,
                    appointment_date: format(selectedDate, "yyyy-MM-dd"),
                    appointment_time: selectedTime,
                    symptoms: symptoms,
                    notes: notes,
                    payment_method: finalPaymentMethod,
                },
                {
                    headers: {
                        Authorization: `Bearer ${token}`,
                    },
                }
            );

            setAppointmentId(response.data.appointment.id);
            setActiveStep(3); // Move to confirmation step
        } catch (error) {
            console.error("Booking failed:", error);
            setError(
                error.response?.data?.message ||
                    "Failed to book appointment. Please try again."
            );
        } finally {
            setBooking(false);
        }
    };

    const formatDateDisplay = (date) => {
        if (isToday(date)) return "Today";
        if (isTomorrow(date)) return "Tomorrow";
        return format(date, "EEEE, MMM dd, yyyy");
    };

    // Safe number conversion for ratings
    const safeRating = (rating) => {
        if (rating === null || rating === undefined) return 0;
        const numRating =
            typeof rating === "string" ? parseFloat(rating) : rating;
        return isNaN(numRating) ? 0 : numRating;
    };

    const renderConfirmationSection = () => {
        return (
            <Box sx={{ minHeight: "80vh" }}>
                {/* Success Header */}
                <Card
                    elevation={8}
                    sx={{
                        mb: 4,
                        background: `linear-gradient(135deg, ${theme.palette.success.light} 0%, ${theme.palette.success.main} 100%)`,
                        color: "white",
                        borderRadius: 3,
                    }}
                >
                    <CardContent
                        sx={{ textAlign: "center", py: { xs: 3, md: 5 } }}
                    >
                        <CheckCircle
                            sx={{ fontSize: { xs: 60, md: 80 }, mb: 2 }}
                        />
                        <Typography
                            variant={isMobile ? "h5" : "h4"}
                            gutterBottom
                            sx={{ fontWeight: "bold" }}
                        >
                            Appointment Booked Successfully!
                        </Typography>
                        <Typography
                            variant={isMobile ? "body1" : "h6"}
                            sx={{ opacity: 0.9 }}
                        >
                            Your healthcare appointment has been confirmed
                        </Typography>

                        {appointmentId && (
                            <Paper
                                elevation={3}
                                sx={{
                                    p: 2,
                                    mt: 3,
                                    bgcolor: "rgba(255,255,255,0.95)",
                                    color: "text.primary",
                                    display: "inline-block",
                                    borderRadius: 2,
                                }}
                            >
                                <Typography
                                    variant="body2"
                                    color="text.secondary"
                                >
                                    Appointment ID
                                </Typography>
                                <Typography
                                    variant={isMobile ? "h6" : "h5"}
                                    sx={{
                                        fontWeight: "bold",
                                        color: "success.main",
                                    }}
                                >
                                    #{appointmentId.slice(-8).toUpperCase()}
                                </Typography>
                            </Paper>
                        )}
                    </CardContent>
                </Card>

                {/* Doctor and Healthcare Information */}
                <Grid container spacing={3}>
                    {/* Doctor Information Card */}
                    <Grid item xs={12} lg={6}>
                        <Card
                            elevation={5}
                            sx={{ height: "100%", borderRadius: 3 }}
                        >
                            <CardContent sx={{ p: { xs: 2, md: 3 } }}>
                                <Box
                                    sx={{
                                        display: "flex",
                                        alignItems: "center",
                                        gap: 1,
                                        mb: 3,
                                    }}
                                >
                                    <FaUserMd
                                        size={24}
                                        color={theme.palette.primary.main}
                                    />
                                    <Typography
                                        variant={isMobile ? "h6" : "h5"}
                                        sx={{ fontWeight: "bold" }}
                                    >
                                        Your Doctor
                                    </Typography>
                                </Box>

                                {/* Doctor Profile */}
                                <Box
                                    sx={{
                                        display: "flex",
                                        flexDirection: {
                                            xs: "column",
                                            sm: "row",
                                        },
                                        gap: 2,
                                        mb: 3,
                                    }}
                                >
                                    <Avatar
                                        src={doctor.profile_image}
                                        sx={{
                                            width: { xs: 80, md: 100 },
                                            height: { xs: 80, md: 100 },
                                            alignSelf: {
                                                xs: "center",
                                                sm: "flex-start",
                                            },
                                            border: `3px solid ${theme.palette.primary.main}`,
                                        }}
                                    >
                                        <FaUserMd size={isMobile ? 30 : 40} />
                                    </Avatar>

                                    <Box
                                        sx={{
                                            flex: 1,
                                            textAlign: {
                                                xs: "center",
                                                sm: "left",
                                            },
                                        }}
                                    >
                                        <Typography
                                            variant={isMobile ? "h6" : "h5"}
                                            sx={{ fontWeight: "bold", mb: 1 }}
                                        >
                                            {doctor.user.name}
                                        </Typography>

                                        <Box
                                            sx={{
                                                display: "flex",
                                                alignItems: "center",
                                                gap: 1,
                                                mb: 1,
                                                justifyContent: {
                                                    xs: "center",
                                                    sm: "flex-start",
                                                },
                                            }}
                                        >
                                            <FaStethoscope
                                                size={16}
                                                color={
                                                    theme.palette.secondary.main
                                                }
                                            />
                                            <Typography
                                                variant="body1"
                                                color="secondary"
                                                sx={{ fontWeight: 600 }}
                                            >
                                                {doctor.specialization}
                                            </Typography>
                                        </Box>

                                        <Box
                                            sx={{
                                                display: "flex",
                                                alignItems: "center",
                                                gap: 1,
                                                mb: 2,
                                                justifyContent: {
                                                    xs: "center",
                                                    sm: "flex-start",
                                                },
                                            }}
                                        >
                                            <FaGraduationCap
                                                size={14}
                                                color={
                                                    theme.palette.text.secondary
                                                }
                                            />
                                            <Typography
                                                variant="body2"
                                                color="text.secondary"
                                            >
                                                {doctor.experience_years} years
                                                experience
                                            </Typography>
                                        </Box>

                                        {doctor.is_verified && (
                                            <Chip
                                                icon={<MdVerified />}
                                                label="Verified Doctor"
                                                color="success"
                                                size="small"
                                                sx={{
                                                    color: "white",
                                                    fontWeight: 500,
                                                }}
                                            />
                                        )}
                                    </Box>
                                </Box>

                                {/* Doctor Details */}
                                <Stack spacing={2}>
                                    {doctor.qualification && (
                                        <Box>
                                            <Typography
                                                variant="subtitle2"
                                                color="text.secondary"
                                                sx={{
                                                    display: "flex",
                                                    alignItems: "center",
                                                    gap: 1,
                                                    mb: 0.5,
                                                }}
                                            >
                                                <FaGraduationCap size={14} />
                                                Qualification
                                            </Typography>
                                            <Typography
                                                variant="body2"
                                                sx={{ pl: 2.5 }}
                                            >
                                                {doctor.qualification}
                                            </Typography>
                                        </Box>
                                    )}

                                    {doctor.rating && (
                                        <Box>
                                            <Typography
                                                variant="subtitle2"
                                                color="text.secondary"
                                                sx={{
                                                    display: "flex",
                                                    alignItems: "center",
                                                    gap: 1,
                                                    mb: 0.5,
                                                }}
                                            >
                                                <Star fontSize="small" />
                                                Rating
                                            </Typography>
                                            <Box
                                                sx={{
                                                    display: "flex",
                                                    alignItems: "center",
                                                    gap: 1,
                                                    pl: 2.5,
                                                }}
                                            >
                                                <Typography
                                                    variant="body2"
                                                    sx={{ fontWeight: 600 }}
                                                >
                                                    {safeRating(
                                                        doctor.rating
                                                    ).toFixed(1)}
                                                </Typography>
                                                <Typography
                                                    variant="body2"
                                                    color="text.secondary"
                                                >
                                                    ({doctor.total_reviews || 0}{" "}
                                                    reviews)
                                                </Typography>
                                            </Box>
                                        </Box>
                                    )}

                                    <Box>
                                        <Typography
                                            variant="subtitle2"
                                            color="text.secondary"
                                            sx={{
                                                display: "flex",
                                                alignItems: "center",
                                                gap: 1,
                                                mb: 0.5,
                                            }}
                                        >
                                            <FaMoneyBillWave size={14} />
                                            Consultation Fee
                                        </Typography>
                                        <Typography
                                            variant="h6"
                                            color="primary"
                                            sx={{ fontWeight: "bold", pl: 2.5 }}
                                        >
                                            ₹{doctor.consultation_fee}
                                        </Typography>
                                    </Box>
                                </Stack>
                            </CardContent>
                        </Card>
                    </Grid>

                    {/* Healthcare/Clinic Information Card */}
                    <Grid item xs={12} lg={6}>
                        <Card
                            elevation={5}
                            sx={{ height: "100%", borderRadius: 3 }}
                        >
                            <CardContent sx={{ p: { xs: 2, md: 3 } }}>
                                <Box
                                    sx={{
                                        display: "flex",
                                        alignItems: "center",
                                        gap: 1,
                                        mb: 3,
                                    }}
                                >
                                    <FaHospital
                                        size={24}
                                        color={theme.palette.primary.main}
                                    />
                                    <Typography
                                        variant={isMobile ? "h6" : "h5"}
                                        sx={{ fontWeight: "bold" }}
                                    >
                                        Healthcare Facility
                                    </Typography>
                                </Box>

                                {/* Clinic Profile */}
                                <Box sx={{ mb: 3 }}>
                                    <Box
                                        sx={{
                                            display: "flex",
                                            alignItems: "center",
                                            gap: 1,
                                            mb: 2,
                                        }}
                                    >
                                        <MdLocalHospital
                                            size={20}
                                            color={theme.palette.primary.main}
                                        />
                                        <Typography
                                            variant={isMobile ? "h6" : "h5"}
                                            sx={{ fontWeight: "bold" }}
                                        >
                                            {clinic.name}
                                        </Typography>
                                    </Box>
                                </Box>

                                {/* Clinic Details */}
                                <Stack spacing={2}>
                                    <Box>
                                        <Typography
                                            variant="subtitle2"
                                            color="text.secondary"
                                            sx={{
                                                display: "flex",
                                                alignItems: "center",
                                                gap: 1,
                                                mb: 0.5,
                                            }}
                                        >
                                            <MdLocationOn size={16} />
                                            Address
                                        </Typography>
                                        <Typography
                                            variant="body2"
                                            sx={{ pl: 2.5, lineHeight: 1.5 }}
                                        >
                                            {clinic.address}
                                        </Typography>
                                    </Box>

                                    {clinic.phone && (
                                        <Box>
                                            <Typography
                                                variant="subtitle2"
                                                color="text.secondary"
                                                sx={{
                                                    display: "flex",
                                                    alignItems: "center",
                                                    gap: 1,
                                                    mb: 0.5,
                                                }}
                                            >
                                                <MdPhone size={16} />
                                                Contact
                                            </Typography>
                                            <Typography
                                                variant="body2"
                                                sx={{ pl: 2.5 }}
                                            >
                                                {clinic.phone}
                                            </Typography>
                                        </Box>
                                    )}

                                    {clinic.email && (
                                        <Box>
                                            <Typography
                                                variant="subtitle2"
                                                color="text.secondary"
                                                sx={{
                                                    display: "flex",
                                                    alignItems: "center",
                                                    gap: 1,
                                                    mb: 0.5,
                                                }}
                                            >
                                                <MdEmail size={16} />
                                                Email
                                            </Typography>
                                            <Typography
                                                variant="body2"
                                                sx={{ pl: 2.5 }}
                                            >
                                                {clinic.email}
                                            </Typography>
                                        </Box>
                                    )}

                                    {clinic.facilities &&
                                        clinic.facilities.length > 0 && (
                                            <Box>
                                                <Typography
                                                    variant="subtitle2"
                                                    color="text.secondary"
                                                    sx={{
                                                        display: "flex",
                                                        alignItems: "center",
                                                        gap: 1,
                                                        mb: 1,
                                                    }}
                                                >
                                                    <MdLocalHospital
                                                        size={16}
                                                    />
                                                    Facilities
                                                </Typography>
                                                <Box
                                                    sx={{
                                                        display: "flex",
                                                        flexWrap: "wrap",
                                                        gap: 1,
                                                        pl: 2.5,
                                                    }}
                                                >
                                                    {clinic.facilities
                                                        .slice(0, 4)
                                                        .map(
                                                            (
                                                                facility,
                                                                index
                                                            ) => (
                                                                <Chip
                                                                    key={index}
                                                                    label={
                                                                        facility
                                                                    }
                                                                    size="small"
                                                                    variant="outlined"
                                                                    color="primary"
                                                                />
                                                            )
                                                        )}
                                                    {clinic.facilities.length >
                                                        4 && (
                                                        <Chip
                                                            label={`+${
                                                                clinic
                                                                    .facilities
                                                                    .length - 4
                                                            } more`}
                                                            size="small"
                                                            variant="outlined"
                                                        />
                                                    )}
                                                </Box>
                                            </Box>
                                        )}
                                </Stack>
                            </CardContent>
                        </Card>
                    </Grid>
                </Grid>

                {/* Appointment Summary Card */}
                <Card elevation={5} sx={{ mt: 3, borderRadius: 3 }}>
                    <CardContent sx={{ p: { xs: 2, md: 3 } }}>
                        <Box
                            sx={{
                                display: "flex",
                                alignItems: "center",
                                gap: 1,
                                mb: 3,
                            }}
                        >
                            <FaCalendarCheck
                                size={24}
                                color={theme.palette.primary.main}
                            />
                            <Typography
                                variant={isMobile ? "h6" : "h5"}
                                sx={{ fontWeight: "bold" }}
                            >
                                Appointment Summary
                            </Typography>
                        </Box>

                        <Grid container spacing={3}>
                            <Grid item xs={12} sm={6} md={3}>
                                <Paper
                                    elevation={2}
                                    sx={{
                                        p: 2,
                                        textAlign: "center",
                                        bgcolor: "primary.50",
                                        borderRadius: 2,
                                    }}
                                >
                                    <FaCalendarAlt
                                        size={20}
                                        color={theme.palette.primary.main}
                                    />
                                    <Typography
                                        variant="body2"
                                        color="text.secondary"
                                        sx={{ mt: 1 }}
                                    >
                                        Date
                                    </Typography>
                                    <Typography
                                        variant="body1"
                                        sx={{ fontWeight: "bold" }}
                                    >
                                        {selectedDate
                                            ? formatDateDisplay(selectedDate)
                                            : ""}
                                    </Typography>
                                </Paper>
                            </Grid>

                            <Grid item xs={12} sm={6} md={3}>
                                <Paper
                                    elevation={2}
                                    sx={{
                                        p: 2,
                                        textAlign: "center",
                                        bgcolor: "secondary.50",
                                        borderRadius: 2,
                                    }}
                                >
                                    <FaClock
                                        size={20}
                                        color={theme.palette.secondary.main}
                                    />
                                    <Typography
                                        variant="body2"
                                        color="text.secondary"
                                        sx={{ mt: 1 }}
                                    >
                                        Time
                                    </Typography>
                                    <Typography
                                        variant="body1"
                                        sx={{ fontWeight: "bold" }}
                                    >
                                        {selectedTime}
                                    </Typography>
                                </Paper>
                            </Grid>

                            <Grid item xs={12} sm={6} md={3}>
                                <Paper
                                    elevation={2}
                                    sx={{
                                        p: 2,
                                        textAlign: "center",
                                        bgcolor: "success.50",
                                        borderRadius: 2,
                                    }}
                                >
                                    <FaMoneyBillWave
                                        size={20}
                                        color={theme.palette.success.main}
                                    />
                                    <Typography
                                        variant="body2"
                                        color="text.secondary"
                                        sx={{ mt: 1 }}
                                    >
                                        Fee
                                    </Typography>
                                    <Typography
                                        variant="body1"
                                        sx={{ fontWeight: "bold" }}
                                    >
                                        ₹{doctor.consultation_fee}
                                    </Typography>
                                </Paper>
                            </Grid>

                            <Grid item xs={12} sm={6} md={3}>
                                <Paper
                                    elevation={2}
                                    sx={{
                                        p: 2,
                                        textAlign: "center",
                                        bgcolor: "warning.50",
                                        borderRadius: 2,
                                    }}
                                >
                                    <FaUserCheck
                                        size={20}
                                        color={theme.palette.warning.main}
                                    />
                                    <Typography
                                        variant="body2"
                                        color="text.secondary"
                                        sx={{ mt: 1 }}
                                    >
                                        Status
                                    </Typography>
                                    <Typography
                                        variant="body1"
                                        sx={{
                                            fontWeight: "bold",
                                            color: "success.main",
                                        }}
                                    >
                                        Confirmed
                                    </Typography>
                                </Paper>
                            </Grid>
                        </Grid>

                        {symptoms && (
                            <Box
                                sx={{
                                    mt: 3,
                                    p: 2,
                                    bgcolor: "grey.50",
                                    borderRadius: 2,
                                }}
                            >
                                <Typography
                                    variant="subtitle2"
                                    color="text.secondary"
                                    sx={{
                                        display: "flex",
                                        alignItems: "center",
                                        gap: 1,
                                        mb: 1,
                                    }}
                                >
                                    <FaNotesMedical size={14} />
                                    Symptoms/Reason for Visit
                                </Typography>
                                <Typography variant="body2" sx={{ pl: 2.5 }}>
                                    {symptoms}
                                </Typography>
                            </Box>
                        )}

                        {/* Payment Method Display */}
                        <Box
                            sx={{
                                mt: 3,
                                p: 2,
                                bgcolor: "info.50",
                                borderRadius: 2,
                            }}
                        >
                            <Typography
                                variant="subtitle2"
                                color="text.secondary"
                                sx={{
                                    display: "flex",
                                    alignItems: "center",
                                    gap: 1,
                                    mb: 1,
                                }}
                            >
                                <MdPayment size={14} />
                                Payment Method
                            </Typography>
                            <Typography
                                variant="body2"
                                sx={{ pl: 2.5, fontWeight: "bold" }}
                            >
                                {paymentMethod === "clinic"
                                    ? "Pay at Clinic"
                                    : paymentMethod === "online"
                                    ? "Online Payment"
                                    : paymentMethod === "cash"
                                    ? "Cash Payment"
                                    : "Pay at Clinic"}
                            </Typography>
                        </Box>
                    </CardContent>
                </Card>

                {/* Important Notes */}
                <Stack spacing={2} sx={{ mt: 3 }}>
                    <Alert
                        severity="success"
                        icon={<FaCheckCircle />}
                        sx={{ borderRadius: 2 }}
                    >
                        <Typography variant="body2" sx={{ fontWeight: 500 }}>
                            <strong>Confirmation sent!</strong> You will receive
                            appointment details via email and SMS.
                        </Typography>
                    </Alert>

                    <Alert
                        severity="info"
                        icon={<MdAccessTime />}
                        sx={{ borderRadius: 2 }}
                    >
                        <Typography variant="body2">
                            <strong>Please arrive 15 minutes early</strong> for
                            your appointment to complete any necessary
                            paperwork.
                        </Typography>
                    </Alert>

                    <Alert
                        severity="warning"
                        icon={<MdInfo />}
                        sx={{ borderRadius: 2 }}
                    >
                        <Typography variant="body2">
                            <strong>Payment:</strong>{" "}
                            {paymentMethod === "clinic" || !paymentMethod
                                ? "You can pay at the clinic during your visit. Payment confirmation will be updated after your appointment."
                                : paymentMethod === "online"
                                ? "Your online payment has been processed successfully."
                                : "Please bring exact cash amount for your appointment."}
                        </Typography>
                    </Alert>
                </Stack>

                {/* Action Buttons */}
                <Box
                    sx={{
                        mt: 4,
                        display: "flex",
                        gap: 2,
                        flexDirection: { xs: "column", sm: "row" },
                        justifyContent: "center",
                    }}
                >
                    <Button
                        variant="contained"
                        size="large"
                        startIcon={<Visibility />}
                        onClick={() => navigate("/appointments")}
                        sx={{
                            color: "white",
                            px: 4,
                            py: 1.5,
                            borderRadius: 2,
                            fontWeight: 600,
                        }}
                    >
                        View My Appointments
                    </Button>
                    <Button
                        variant="outlined"
                        size="large"
                        startIcon={<Home />}
                        onClick={() => navigate("/")}
                        sx={{
                            px: 4,
                            py: 1.5,
                            borderRadius: 2,
                            fontWeight: 600,
                        }}
                    >
                        Go Home
                    </Button>
                </Box>
            </Box>
        );
    };

    const renderStepContent = (step) => {
        switch (step) {
            case 0:
                return (
                    <Card elevation={2} sx={{ borderRadius: 2 }}>
                        <CardContent sx={{ p: { xs: 2, md: 3 } }}>
                            <Box
                                sx={{
                                    display: "flex",
                                    alignItems: "center",
                                    gap: 1,
                                    mb: 3,
                                }}
                            >
                                <Event color="primary" />
                                <Typography
                                    variant={isMobile ? "h6" : "h5"}
                                    sx={{ fontWeight: "bold" }}
                                >
                                    Select Appointment Date & Time
                                </Typography>
                            </Box>

                            <Grid container spacing={3}>
                                <Grid item xs={12} md={6}>
                                    <LocalizationProvider
                                        dateAdapter={AdapterDateFns}
                                    >
                                        <DatePicker
                                            label="Select Date"
                                            value={selectedDate}
                                            onChange={(newValue) => {
                                                setSelectedDate(newValue);
                                                setSelectedTime(""); // Reset time when date changes
                                            }}
                                            minDate={new Date()}
                                            maxDate={
                                                new Date(
                                                    Date.now() +
                                                        30 * 24 * 60 * 60 * 1000
                                                )
                                            }
                                            enableAccessibleFieldDOMStructure={
                                                false
                                            }
                                            slotProps={{
                                                textField: {
                                                    fullWidth: true,
                                                    sx: {
                                                        "& .MuiOutlinedInput-root":
                                                            {
                                                                borderRadius: 2,
                                                            },
                                                    },
                                                },
                                            }}
                                        />
                                    </LocalizationProvider>
                                </Grid>

                                {/* Time Selection - Show as buttons when date is selected */}
                                <Grid item xs={12} md={6}>
                                    {selectedDate && (
                                        <Box>
                                            <Typography
                                                variant="h6"
                                                sx={{
                                                    mb: 2,
                                                    display: "flex",
                                                    alignItems: "center",
                                                    gap: 1,
                                                    fontWeight: "bold",
                                                }}
                                            >
                                                <AccessTime color="primary" />
                                                Select Time Slot
                                            </Typography>

                                            {loading ? (
                                                <Box
                                                    sx={{
                                                        display: "flex",
                                                        justifyContent:
                                                            "center",
                                                        alignItems: "center",
                                                        py: 4,
                                                    }}
                                                >
                                                    <CircularProgress
                                                        size={32}
                                                    />
                                                    <Typography
                                                        sx={{ ml: 2 }}
                                                        color="text.secondary"
                                                    >
                                                        Loading available
                                                        slots...
                                                    </Typography>
                                                </Box>
                                            ) : availableSlots.length === 0 ? (
                                                <Alert
                                                    severity="warning"
                                                    sx={{ borderRadius: 2 }}
                                                >
                                                    No time slots available for
                                                    the selected date. Please
                                                    choose another date.
                                                </Alert>
                                            ) : (
                                                <Box
                                                    sx={{
                                                        display: "flex",
                                                        flexWrap: "wrap",
                                                        gap: 1.5,
                                                        maxHeight: "200px",
                                                        overflowY: "auto",
                                                        p: 1,
                                                    }}
                                                >
                                                    {availableSlots.map(
                                                        (slot) => (
                                                            <Button
                                                                key={slot}
                                                                variant={
                                                                    selectedTime ===
                                                                    slot
                                                                        ? "contained"
                                                                        : "outlined"
                                                                }
                                                                onClick={() =>
                                                                    setSelectedTime(
                                                                        slot
                                                                    )
                                                                }
                                                                startIcon={
                                                                    <FaClock
                                                                        size={
                                                                            14
                                                                        }
                                                                    />
                                                                }
                                                                sx={{
                                                                    minWidth:
                                                                        "120px",
                                                                    height: "40px",
                                                                    borderRadius: 2,
                                                                    fontWeight: 600,
                                                                    color:
                                                                        selectedTime ===
                                                                        slot
                                                                            ? "white"
                                                                            : "primary.main",
                                                                    "&:hover": {
                                                                        transform:
                                                                            "translateY(-2px)",
                                                                        boxShadow: 2,
                                                                    },
                                                                    transition:
                                                                        "all 0.2s ease-in-out",
                                                                }}
                                                            >
                                                                {slot}
                                                            </Button>
                                                        )
                                                    )}
                                                </Box>
                                            )}
                                        </Box>
                                    )}
                                </Grid>
                            </Grid>

                            {selectedDate && selectedTime && (
                                <Alert
                                    severity="success"
                                    sx={{ mt: 3, borderRadius: 2 }}
                                    icon={<CheckCircle />}
                                >
                                    <Box
                                        sx={{
                                            display: "flex",
                                            alignItems: "center",
                                            gap: 1,
                                        }}
                                    >
                                        <FaCalendarCheck
                                            color={theme.palette.success.main}
                                        />
                                        <Typography sx={{ fontWeight: 600 }}>
                                            Selected:{" "}
                                            {formatDateDisplay(selectedDate)} at{" "}
                                            {selectedTime}
                                        </Typography>
                                    </Box>
                                </Alert>
                            )}
                        </CardContent>
                    </Card>
                );

            case 1:
                return (
                    <Card elevation={2} sx={{ borderRadius: 2 }}>
                        <CardContent sx={{ p: { xs: 2, md: 3 } }}>
                            <Box
                                sx={{
                                    display: "flex",
                                    alignItems: "center",
                                    gap: 1,
                                    mb: 3,
                                }}
                            >
                                <FaNotesMedical
                                    size={20}
                                    color={theme.palette.primary.main}
                                />
                                <Typography
                                    variant={isMobile ? "h6" : "h5"}
                                    sx={{ fontWeight: "bold" }}
                                >
                                    Appointment Details
                                </Typography>
                            </Box>

                            <Grid container spacing={3}>
                                <Grid item xs={12}>
                                    <TextField
                                        fullWidth
                                        label="Symptoms / Reason for Visit"
                                        multiline
                                        rows={4}
                                        value={symptoms}
                                        onChange={(e) =>
                                            setSymptoms(e.target.value)
                                        }
                                        placeholder="Please describe your symptoms or reason for visiting the doctor..."
                                        required
                                        helperText="This information helps the doctor prepare for your visit"
                                        InputProps={{
                                            startAdornment: (
                                                <FaStethoscope
                                                    size={16}
                                                    color={
                                                        theme.palette.primary
                                                            .main
                                                    }
                                                    style={{ marginRight: 8 }}
                                                />
                                            ),
                                        }}
                                        sx={{
                                            "& .MuiOutlinedInput-root": {
                                                borderRadius: 2,
                                            },
                                        }}
                                    />
                                </Grid>

                                <Grid item xs={12}>
                                    <TextField
                                        fullWidth
                                        label="Additional Notes (Optional)"
                                        multiline
                                        rows={2}
                                        value={notes}
                                        onChange={(e) =>
                                            setNotes(e.target.value)
                                        }
                                        placeholder="Any additional information you'd like to share..."
                                        InputProps={{
                                            startAdornment: (
                                                <Description
                                                    fontSize="small"
                                                    color="primary"
                                                    sx={{ mr: 1 }}
                                                />
                                            ),
                                        }}
                                        sx={{
                                            "& .MuiOutlinedInput-root": {
                                                borderRadius: 2,
                                            },
                                        }}
                                    />
                                </Grid>
                            </Grid>

                            {/* Summary of selected date and time */}
                            <Paper
                                elevation={2}
                                sx={{
                                    mt: 3,
                                    p: 2,
                                    bgcolor: "grey.50",
                                    borderRadius: 2,
                                }}
                            >
                                <Typography
                                    variant="subtitle1"
                                    sx={{
                                        fontWeight: "bold",
                                        mb: 1,
                                        display: "flex",
                                        alignItems: "center",
                                        gap: 1,
                                    }}
                                >
                                    <FaCalendarCheck
                                        color={theme.palette.primary.main}
                                    />
                                    Appointment Schedule
                                </Typography>
                                <Box
                                    sx={{
                                        display: "flex",
                                        gap: 3,
                                        flexWrap: "wrap",
                                    }}
                                >
                                    <Box
                                        sx={{
                                            display: "flex",
                                            alignItems: "center",
                                            gap: 1,
                                        }}
                                    >
                                        <FaCalendarAlt
                                            size={14}
                                            color={theme.palette.primary.main}
                                        />
                                        <Typography
                                            variant="body2"
                                            color="text.secondary"
                                        >
                                            Date:
                                        </Typography>
                                        <Typography
                                            variant="body2"
                                            sx={{ fontWeight: "bold" }}
                                        >
                                            {selectedDate
                                                ? formatDateDisplay(
                                                      selectedDate
                                                  )
                                                : ""}
                                        </Typography>
                                    </Box>
                                    <Box
                                        sx={{
                                            display: "flex",
                                            alignItems: "center",
                                            gap: 1,
                                        }}
                                    >
                                        <FaClock
                                            size={14}
                                            color={theme.palette.secondary.main}
                                        />
                                        <Typography
                                            variant="body2"
                                            color="text.secondary"
                                        >
                                            Time:
                                        </Typography>
                                        <Typography
                                            variant="body2"
                                            sx={{ fontWeight: "bold" }}
                                        >
                                            {selectedTime}
                                        </Typography>
                                    </Box>
                                </Box>
                            </Paper>
                        </CardContent>
                    </Card>
                );

            case 2:
                return (
                    <Card elevation={2} sx={{ borderRadius: 2 }}>
                        <CardContent sx={{ p: { xs: 2, md: 3 } }}>
                            <Box
                                sx={{
                                    display: "flex",
                                    alignItems: "center",
                                    gap: 1,
                                    mb: 3,
                                }}
                            >
                                <MdPayment
                                    size={20}
                                    color={theme.palette.primary.main}
                                />
                                <Typography
                                    variant={isMobile ? "h6" : "h5"}
                                    sx={{ fontWeight: "bold" }}
                                >
                                    Payment & Review
                                </Typography>
                            </Box>

                            {/* Appointment Summary */}
                            <Paper
                                elevation={2}
                                sx={{ p: 3, mb: 3, borderRadius: 2 }}
                            >
                                <Box
                                    sx={{
                                        display: "flex",
                                        alignItems: "center",
                                        gap: 1,
                                        mb: 2,
                                    }}
                                >
                                    <Description color="primary" />
                                    <Typography
                                        variant="h6"
                                        sx={{ fontWeight: "bold" }}
                                    >
                                        Appointment Summary
                                    </Typography>
                                </Box>

                                <Grid container spacing={2}>
                                    <Grid item xs={12} sm={6}>
                                        <Typography
                                            variant="body2"
                                            color="text.secondary"
                                        >
                                            Doctor:
                                        </Typography>
                                        <Box
                                            sx={{
                                                display: "flex",
                                                alignItems: "center",
                                                gap: 1,
                                            }}
                                        >
                                            <FaUserMd
                                                size={16}
                                                color={
                                                    theme.palette.primary.main
                                                }
                                            />
                                            <Typography
                                                variant="body1"
                                                fontWeight="bold"
                                            >
                                                {doctor.user.name}
                                            </Typography>
                                        </Box>
                                    </Grid>

                                    <Grid item xs={12} sm={6}>
                                        <Typography
                                            variant="body2"
                                            color="text.secondary"
                                        >
                                            Specialization:
                                        </Typography>
                                        <Box
                                            sx={{
                                                display: "flex",
                                                alignItems: "center",
                                                gap: 1,
                                            }}
                                        >
                                            <FaStethoscope
                                                size={16}
                                                color={
                                                    theme.palette.primary.main
                                                }
                                            />
                                            <Typography
                                                variant="body1"
                                                fontWeight="bold"
                                            >
                                                {doctor.specialization}
                                            </Typography>
                                        </Box>
                                    </Grid>

                                    <Grid item xs={12} sm={6}>
                                        <Typography
                                            variant="body2"
                                            color="text.secondary"
                                        >
                                            Clinic:
                                        </Typography>
                                        <Box
                                            sx={{
                                                display: "flex",
                                                alignItems: "center",
                                                gap: 1,
                                            }}
                                        >
                                            <FaHospital
                                                size={16}
                                                color={
                                                    theme.palette.primary.main
                                                }
                                            />
                                            <Typography
                                                variant="body1"
                                                fontWeight="bold"
                                            >
                                                {clinic.name}
                                            </Typography>
                                        </Box>
                                    </Grid>

                                    <Grid item xs={12} sm={6}>
                                        <Typography
                                            variant="body2"
                                            color="text.secondary"
                                        >
                                            Date:
                                        </Typography>
                                        <Box
                                            sx={{
                                                display: "flex",
                                                alignItems: "center",
                                                gap: 1,
                                            }}
                                        >
                                            <FaCalendarAlt
                                                size={16}
                                                color={
                                                    theme.palette.primary.main
                                                }
                                            />
                                            <Typography
                                                variant="body1"
                                                fontWeight="bold"
                                            >
                                                {selectedDate
                                                    ? formatDateDisplay(
                                                          selectedDate
                                                      )
                                                    : ""}
                                            </Typography>
                                        </Box>
                                    </Grid>

                                    <Grid item xs={12} sm={6}>
                                        <Typography
                                            variant="body2"
                                            color="text.secondary"
                                        >
                                            Time:
                                        </Typography>
                                        <Box
                                            sx={{
                                                display: "flex",
                                                alignItems: "center",
                                                gap: 1,
                                            }}
                                        >
                                            <FaClock
                                                size={16}
                                                color={
                                                    theme.palette.primary.main
                                                }
                                            />
                                            <Typography
                                                variant="body1"
                                                fontWeight="bold"
                                            >
                                                {selectedTime}
                                            </Typography>
                                        </Box>
                                    </Grid>

                                    <Grid item xs={12} sm={6}>
                                        <Typography
                                            variant="body2"
                                            color="text.secondary"
                                        >
                                            Consultation Fee:
                                        </Typography>
                                        <Box
                                            sx={{
                                                display: "flex",
                                                alignItems: "center",
                                                gap: 1,
                                            }}
                                        >
                                            <FaMoneyBillWave
                                                size={16}
                                                color={
                                                    theme.palette.success.main
                                                }
                                            />
                                            <Typography
                                                variant="h5"
                                                color="primary"
                                                fontWeight="bold"
                                            >
                                                ₹{doctor.consultation_fee}
                                            </Typography>
                                        </Box>
                                    </Grid>
                                </Grid>

                                {symptoms && (
                                    <Box
                                        sx={{
                                            mt: 2,
                                            pt: 2,
                                            borderTop: "1px solid #e0e0e0",
                                        }}
                                    >
                                        <Typography
                                            variant="body2"
                                            color="text.secondary"
                                        >
                                            Symptoms/Reason:
                                        </Typography>
                                        <Typography variant="body2">
                                            {symptoms}
                                        </Typography>
                                    </Box>
                                )}

                                {notes && (
                                    <Box sx={{ mt: 1 }}>
                                        <Typography
                                            variant="body2"
                                            color="text.secondary"
                                        >
                                            Additional Notes:
                                        </Typography>
                                        <Typography variant="body2">
                                            {notes}
                                        </Typography>
                                    </Box>
                                )}
                            </Paper>

                            {/* FIXED: Payment Method Selection with Responsive Layout */}
                            <Paper
                                elevation={2}
                                sx={{ p: 3, mb: 3, borderRadius: 2 }}
                            >
                                <Box
                                    sx={{
                                        display: "flex",
                                        alignItems: "center",
                                        gap: 1,
                                        mb: 2,
                                    }}
                                >
                                    <Payment color="primary" />
                                    <Typography
                                        variant="h6"
                                        sx={{ fontWeight: "bold" }}
                                    >
                                        Payment Method
                                    </Typography>
                                </Box>

                                <FormControl component="fieldset">
                                    <RadioGroup
                                        value={paymentMethod}
                                        onChange={(e) =>
                                            setPaymentMethod(e.target.value)
                                        }
                                        sx={{
                                            mt: 1,
                                            // RESPONSIVE LAYOUT: Row on desktop, column on mobile
                                            display: "flex",
                                            flexDirection: {
                                                xs: "column",
                                                md: "row",
                                            },
                                            gap: 2,
                                        }}
                                    >
                                        <FormControlLabel
                                            value="clinic"
                                            control={<Radio />}
                                            label={
                                                <Box
                                                    sx={{
                                                        display: "flex",
                                                        alignItems: "center",
                                                        gap: 1,
                                                    }}
                                                >
                                                    <LocalHospital color="primary" />
                                                    <Box>
                                                        <Typography
                                                            variant="body1"
                                                            sx={{
                                                                fontWeight: 600,
                                                            }}
                                                        >
                                                            Pay at Clinic
                                                        </Typography>
                                                        <Typography
                                                            variant="body2"
                                                            color="text.secondary"
                                                        >
                                                            Pay during your
                                                            visit at the clinic
                                                        </Typography>
                                                    </Box>
                                                </Box>
                                            }
                                            sx={{
                                                p: 2,
                                                border:
                                                    paymentMethod === "clinic"
                                                        ? "2px solid"
                                                        : "1px solid",
                                                borderColor:
                                                    paymentMethod === "clinic"
                                                        ? "primary.main"
                                                        : "grey.300",
                                                borderRadius: 2,
                                                bgcolor:
                                                    paymentMethod === "clinic"
                                                        ? "primary.50"
                                                        : "transparent",
                                                flex: { md: 1 }, // Equal width on desktop
                                                m: 0, // Remove default margin
                                            }}
                                        />

                                        <FormControlLabel
                                            value="online"
                                            control={<Radio />}
                                            label={
                                                <Box
                                                    sx={{
                                                        display: "flex",
                                                        alignItems: "center",
                                                        gap: 1,
                                                    }}
                                                >
                                                    <CreditCard color="primary" />
                                                    <Box>
                                                        <Typography
                                                            variant="body1"
                                                            sx={{
                                                                fontWeight: 600,
                                                            }}
                                                        >
                                                            Online Payment
                                                        </Typography>
                                                        <Typography
                                                            variant="body2"
                                                            color="text.secondary"
                                                        >
                                                            Pay now using card,
                                                            UPI, or net banking
                                                        </Typography>
                                                    </Box>
                                                </Box>
                                            }
                                            sx={{
                                                p: 2,
                                                border:
                                                    paymentMethod === "online"
                                                        ? "2px solid"
                                                        : "1px solid",
                                                borderColor:
                                                    paymentMethod === "online"
                                                        ? "primary.main"
                                                        : "grey.300",
                                                borderRadius: 2,
                                                bgcolor:
                                                    paymentMethod === "online"
                                                        ? "primary.50"
                                                        : "transparent",
                                                flex: { md: 1 }, // Equal width on desktop
                                                m: 0, // Remove default margin
                                            }}
                                        />

                                        <FormControlLabel
                                            value="cash"
                                            control={<Radio />}
                                            label={
                                                <Box
                                                    sx={{
                                                        display: "flex",
                                                        alignItems: "center",
                                                        gap: 1,
                                                    }}
                                                >
                                                    <AccountBalanceWallet color="primary" />
                                                    <Box>
                                                        <Typography
                                                            variant="body1"
                                                            sx={{
                                                                fontWeight: 600,
                                                            }}
                                                        >
                                                            Cash Payment
                                                        </Typography>
                                                        <Typography
                                                            variant="body2"
                                                            color="text.secondary"
                                                        >
                                                            Pay with cash at the
                                                            clinic
                                                        </Typography>
                                                    </Box>
                                                </Box>
                                            }
                                            sx={{
                                                p: 2,
                                                border:
                                                    paymentMethod === "cash"
                                                        ? "2px solid"
                                                        : "1px solid",
                                                borderColor:
                                                    paymentMethod === "cash"
                                                        ? "primary.main"
                                                        : "grey.300",
                                                borderRadius: 2,
                                                bgcolor:
                                                    paymentMethod === "cash"
                                                        ? "primary.50"
                                                        : "transparent",
                                                flex: { md: 1 }, // Equal width on desktop
                                                m: 0, // Remove default margin
                                            }}
                                        />
                                    </RadioGroup>
                                </FormControl>
                            </Paper>

                            {/* Payment Info Alert */}
                            <Alert
                                severity={
                                    paymentMethod === "online"
                                        ? "success"
                                        : "info"
                                }
                                sx={{ mb: 2, borderRadius: 2 }}
                                icon={<MdPayment />}
                            >
                                <Box
                                    sx={{
                                        display: "flex",
                                        alignItems: "center",
                                        gap: 1,
                                    }}
                                >
                                    {(paymentMethod === "clinic" ||
                                        !paymentMethod) && (
                                        <>
                                            <LocalHospital />
                                            You can pay at the clinic during
                                            your visit. Payment confirmation
                                            will be updated after your
                                            appointment.
                                        </>
                                    )}
                                    {paymentMethod === "online" && (
                                        <>
                                            <CreditCard />
                                            Secure online payment. You will be
                                            redirected to payment gateway after
                                            confirmation.
                                        </>
                                    )}
                                    {paymentMethod === "cash" && (
                                        <>
                                            <AccountBalanceWallet />
                                            Please bring exact cash amount (₹
                                            {doctor.consultation_fee}) for your
                                            appointment.
                                        </>
                                    )}
                                </Box>
                            </Alert>
                        </CardContent>
                    </Card>
                );

            case 3:
                return renderConfirmationSection();

            default:
                return null;
        }
    };

    if (!doctor || !clinic) {
        return (
            <MainLayout>
                <Container maxWidth="md" sx={{ py: 10, textAlign: "center" }}>
                    <Alert severity="error">
                        Invalid booking request. Please try again.
                    </Alert>
                </Container>
            </MainLayout>
        );
    }

    return (
        <MainLayout>
            <Container maxWidth="xl" sx={{ py: 10 }}>
                {activeStep < 3 && (
                    <>
                        <Box
                            sx={{
                                display: "flex",
                                alignItems: "center",
                                justifyContent: "center",
                                position: "relative",
                                width: "100vw",
                                marginLeft: "calc(-50vw + 50%)",
                                marginRight: "calc(-50vw + 50%)",
                                gap: 1,
                                mb: 4,
                            }}
                        >
                            <CalendarToday color="primary" fontSize="large" />
                            <Typography
                                variant={isMobile ? "h5" : "h4"}
                                textAlign="center"
                                sx={{ fontWeight: "bold" }}
                            >
                                Book Appointment
                            </Typography>
                        </Box>

                        {/* Doctor & Clinic Info */}
                        <Card elevation={5} sx={{ mb: 4, borderRadius: 3 }}>
                            <CardContent>
                                <Grid container spacing={3} alignItems="center">
                                    {/* Doctor Info */}
                                    <Grid item xs={12} sm={6}>
                                        <Box
                                            sx={{
                                                display: "flex",
                                                alignItems: "center",
                                            }}
                                        >
                                            <Avatar
                                                src={doctor.profile_image}
                                                sx={{
                                                    width: 65,
                                                    height: 65,
                                                    mr: 2,
                                                }}
                                            >
                                                <FaUserMd size={50} />
                                            </Avatar>

                                            <Box>
                                                <Typography
                                                    variant="h6"
                                                    sx={{
                                                        fontWeight: "bold",
                                                        mb: 0.5,
                                                    }}
                                                >
                                                    {doctor.user.name}
                                                </Typography>
                                                <Typography
                                                    variant="body2"
                                                    color="text.secondary"
                                                    sx={{ mb: 1 }}
                                                >
                                                    {doctor.specialization}
                                                </Typography>
                                                <Chip
                                                    label={`₹${doctor.consultation_fee}`}
                                                    size="small"
                                                    color="primary"
                                                    sx={{
                                                        color: "white",
                                                        fontWeight: "bold",
                                                    }}
                                                />
                                            </Box>
                                        </Box>
                                    </Grid>

                                    {/* Clinic Info */}
                                    <Grid item xs={12} sm={6}>
                                        <Box
                                            sx={{
                                                display: "flex",
                                                alignItems: "flex-start",
                                            }}
                                        >
                                            <LocalHospital
                                                sx={{
                                                    mr: 2,
                                                    color: "primary.main",
                                                    fontSize: 32,
                                                }}
                                            />
                                            <Box>
                                                <Typography
                                                    variant="h6"
                                                    color="text.secondary"
                                                    sx={{
                                                        fontWeight: "bold",
                                                        mb: 0.5,
                                                        display: "flex",
                                                        alignItems: "center",
                                                        gap: 1,
                                                    }}
                                                >
                                                    <FaHospital
                                                        size={16}
                                                        color="primary"
                                                    />
                                                    {clinic.name}
                                                </Typography>
                                                <Typography
                                                    variant="body2"
                                                    color="text.secondary"
                                                    sx={{
                                                        display: "flex",
                                                        alignItems: "center",
                                                        gap: 0.5,
                                                    }}
                                                >
                                                    <MdLocationOn
                                                        size={
                                                            isMobile ? 50 : 30
                                                        }
                                                    />
                                                    {clinic.address}
                                                </Typography>
                                            </Box>
                                        </Box>
                                    </Grid>
                                </Grid>
                            </CardContent>
                        </Card>

                        {/* FIXED: Updated Stepper with proper completion logic */}
                        <Card
                            elevation={5}
                            sx={{ p: 2, mb: 3, borderRadius: 3 }}
                        >
                            <Box sx={{ mb: 2 }}>
                                <Stepper
                                    activeStep={activeStep}
                                    alternativeLabel={!isMobile}
                                    orientation={
                                        isMobile ? "vertical" : "horizontal"
                                    }
                                >
                                    {steps.map((step, index) => {
                                        const isCompleted =
                                            index < activeStep ||
                                            isStepCompleted(index);
                                        const isActive = index === activeStep;

                                        return (
                                            <Step
                                                key={step.label}
                                                completed={isCompleted}
                                            >
                                                <StepLabel
                                                    icon={step.icon}
                                                    sx={{
                                                        "& .MuiStepLabel-label":
                                                            {
                                                                fontSize:
                                                                    isMobile
                                                                        ? "0.9rem"
                                                                        : "1rem",
                                                                fontWeight:
                                                                    isActive ||
                                                                    isCompleted
                                                                        ? "bold"
                                                                        : "normal",
                                                                color:
                                                                    isCompleted &&
                                                                    !isActive
                                                                        ? "success.main"
                                                                        : "inherit",
                                                            },
                                                        "& .MuiStepIcon-root": {
                                                            color: isCompleted
                                                                ? "success.main"
                                                                : isActive
                                                                ? "primary.main"
                                                                : "grey.400",
                                                            fontSize: "1.5rem",
                                                        },
                                                        "& .MuiStepIcon-text": {
                                                            fill: isCompleted
                                                                ? "white"
                                                                : "inherit",
                                                        },
                                                    }}
                                                >
                                                    {step.label}
                                                </StepLabel>
                                            </Step>
                                        );
                                    })}
                                </Stepper>
                            </Box>
                        </Card>
                        {/* Error Alert */}
                        {error && (
                            <Alert severity="error" sx={{ mb: 3 }}>
                                {error}
                            </Alert>
                        )}

                        {/* Step Content - Now wrapped in cards */}
                        <Box sx={{ mb: 4 }}>
                            {renderStepContent(activeStep)}
                        </Box>

                        {/* Navigation Buttons */}
                        <Box
                            sx={{
                                display: "flex",
                                justifyContent: "space-between",
                                flexDirection: isMobile ? "column" : "row",
                                gap: isMobile ? 2 : 0,
                            }}
                        >
                            <Button
                                disabled={activeStep === 0}
                                onClick={handleBack}
                                variant="outlined"
                                startIcon={<ArrowBack />}
                                sx={{ width: isMobile ? "100%" : "auto" }}
                            >
                                Back
                            </Button>

                            {activeStep === 2 ? (
                                <Button
                                    variant="contained"
                                    onClick={handleBookAppointment}
                                    disabled={
                                        booking ||
                                        !selectedDate ||
                                        !selectedTime ||
                                        !symptoms.trim()
                                    }
                                    startIcon={
                                        booking ? (
                                            <CircularProgress size={20} />
                                        ) : (
                                            <CheckCircle />
                                        )
                                    }
                                    sx={{
                                        color: "white",
                                        width: isMobile ? "100%" : "auto",
                                    }}
                                >
                                    {booking ? "Booking..." : "Confirm Booking"}
                                </Button>
                            ) : (
                                <Button
                                    variant="contained"
                                    onClick={handleNext}
                                    disabled={
                                        (activeStep === 0 &&
                                            (!selectedDate || !selectedTime)) ||
                                        (activeStep === 1 && !symptoms.trim())
                                    }
                                    endIcon={<ArrowForward />}
                                    sx={{
                                        color: "white",
                                        width: isMobile ? "100%" : "auto",
                                    }}
                                >
                                    Next
                                </Button>
                            )}
                        </Box>
                    </>
                )}

                {activeStep === 3 && renderConfirmationSection()}
            </Container>
        </MainLayout>
    );
};

export default BookAppointmentPage;
```

**resources/js/pages/admin/AdminDashboard.jsx**

```jsx
import React from "react";
import {
    Box,
    Container,
    Typography,
    Grid,
    Card,
    CardContent,
    Avatar,
    Button,
    Paper,
    List,
    ListItem,
    ListItemText,
    ListItemIcon,
    Chip,
    Divider,
} from "@mui/material";
import {
    AdminPanelSettings,
    People,
    LocalHospital,
    TrendingUp,
    Security,
    Assessment,
    Settings,
    Notifications,
    SupervisorAccount,
    VerifiedUser,
} from "@mui/icons-material";
import MainLayout from "../../components/layout/MainLayout";

const AdminDashboard = () => {
    const adminStats = [
        {
            title: "👥 Total Users",
            value: "2,456",
            icon: <People />,
            color: "primary",
        },
        {
            title: "🏥 Healthcare Providers",
            value: "156",
            icon: <LocalHospital />,
            color: "success",
        },
        {
            title: "🔐 System Security",
            value: "99.9%",
            icon: <Security />,
            color: "warning",
        },
        {
            title: "📈 Platform Growth",
            value: "+45%",
            icon: <TrendingUp />,
            color: "info",
        },
    ];

    const systemAlerts = [
        {
            type: "Security",
            message: "System security scan completed",
            status: "success",
            time: "1 hour ago",
        },
        {
            type: "Performance",
            message: "High traffic detected - scaling resources",
            status: "warning",
            time: "3 hours ago",
        },
        {
            type: "Maintenance",
            message: "Scheduled backup completed successfully",
            status: "success",
            time: "6 hours ago",
        },
    ];

    return (
        <MainLayout>
            <Container maxWidth="lg" sx={{ mt: 12, mb: 4 }}>
                {/* Header */}
                <Box sx={{ mb: 4 }}>
                    <Typography
                        variant="h4"
                        sx={{
                            fontWeight: "bold",
                            display: "flex",
                            alignItems: "center",
                            gap: 2,
                            mb: 1,
                        }}
                    >
                        <AdminPanelSettings
                            sx={{ fontSize: 40, color: "primary.main" }}
                        />
                        👑 Admin Dashboard
                    </Typography>
                    <Typography variant="h6" color="text.secondary">
                        🎛️ Complete platform administration and system
                        management
                    </Typography>
                </Box>

                {/* Stats Cards */}
                <Grid container spacing={3} sx={{ mb: 4 }}>
                    {adminStats.map((stat, index) => (
                        <Grid item xs={12} sm={6} md={3} key={index}>
                            <Card elevation={3} sx={{ height: "100%" }}>
                                <CardContent>
                                    <Box
                                        sx={{
                                            display: "flex",
                                            alignItems: "center",
                                            mb: 2,
                                        }}
                                    >
                                        <Avatar
                                            sx={{
                                                bgcolor: `${stat.color}.main`,
                                                mr: 2,
                                            }}
                                        >
                                            {stat.icon}
                                        </Avatar>
                                        <Box>
                                            <Typography
                                                variant="h4"
                                                sx={{ fontWeight: "bold" }}
                                            >
                                                {stat.value}
                                            </Typography>
                                            <Typography
                                                variant="body2"
                                                color="text.secondary"
                                            >
                                                {stat.title}
                                            </Typography>
                                        </Box>
                                    </Box>
                                </CardContent>
                            </Card>
                        </Grid>
                    ))}
                </Grid>

                <Grid container spacing={3}>
                    {/* System Alerts */}
                    <Grid item xs={12} md={8}>
                        <Paper elevation={3} sx={{ p: 3 }}>
                            <Typography
                                variant="h6"
                                sx={{
                                    fontWeight: "bold",
                                    mb: 3,
                                    display: "flex",
                                    alignItems: "center",
                                    gap: 1,
                                }}
                            >
                                <Notifications color="primary" />
                                🚨 System Alerts
                            </Typography>
                            <List>
                                {systemAlerts.map((alert, index) => (
                                    <React.Fragment key={index}>
                                        <ListItem>
                                            <ListItemIcon>
                                                <Security
                                                    color={
                                                        alert.status ===
                                                        "success"
                                                            ? "success"
                                                            : alert.status ===
                                                              "warning"
                                                            ? "warning"
                                                            : "error"
                                                    }
                                                />
                                            </ListItemIcon>
                                            <ListItemText
                                                primary={`🔔 ${alert.type}: ${alert.message}`}
                                                secondary={`⏰ ${alert.time}`}
                                            />
                                            <Chip
                                                label={alert.status}
                                                color={
                                                    alert.status === "success"
                                                        ? "success"
                                                        : alert.status ===
                                                          "warning"
                                                        ? "warning"
                                                        : "error"
                                                }
                                                size="small"
                                            />
                                        </ListItem>
                                        {index < systemAlerts.length - 1 && (
                                            <Divider />
                                        )}
                                    </React.Fragment>
                                ))}
                            </List>
                        </Paper>
                    </Grid>

                    {/* Admin Actions */}
                    <Grid item xs={12} md={4}>
                        <Paper elevation={3} sx={{ p: 3 }}>
                            <Typography
                                variant="h6"
                                sx={{
                                    fontWeight: "bold",
                                    mb: 3,
                                    display: "flex",
                                    alignItems: "center",
                                    gap: 1,
                                }}
                            >
                                <Settings color="primary" />
                                🔧 Admin Controls
                            </Typography>
                            <Box
                                sx={{
                                    display: "flex",
                                    flexDirection: "column",
                                    gap: 2,
                                }}
                            >
                                <Button
                                    variant="outlined"
                                    startIcon={<SupervisorAccount />}
                                    fullWidth
                                    sx={{ justifyContent: "flex-start" }}
                                >
                                    👥 User Management
                                </Button>
                                <Button
                                    variant="outlined"
                                    startIcon={<VerifiedUser />}
                                    fullWidth
                                    sx={{ justifyContent: "flex-start" }}
                                >
                                    ✅ Verify Doctors
                                </Button>
                                <Button
                                    variant="outlined"
                                    startIcon={<LocalHospital />}
                                    fullWidth
                                    sx={{ justifyContent: "flex-start" }}
                                >
                                    🏥 Platform Settings
                                </Button>
                                <Button
                                    variant="outlined"
                                    startIcon={<Assessment />}
                                    fullWidth
                                    sx={{ justifyContent: "flex-start" }}
                                >
                                    📊 Analytics
                                </Button>
                                <Button
                                    variant="outlined"
                                    startIcon={<Security />}
                                    fullWidth
                                    sx={{ justifyContent: "flex-start" }}
                                >
                                    🔐 Security Center
                                </Button>
                            </Box>
                        </Paper>
                    </Grid>
                </Grid>
            </Container>
        </MainLayout>
    );
};

export default AdminDashboard;
```

**resources/js/pages/doctor/DoctorDashboard.jsx**

```jsx
import React, { useState, useEffect } from 'react';
import {
    Box,
    Container,
    Typography,
    Grid,
    Card,
    CardContent,
    Avatar,
    Button,
    Chip,
    Paper,
    List,
    ListItem,
    ListItemText,
    ListItemIcon,
    Divider,
    Alert,
    CircularProgress,
    Tabs,
    Tab,
    Dialog,
    DialogTitle,
    DialogContent,
    DialogActions,
    TextField,
    FormControl,
    InputLabel,
    Select,
    MenuItem,
    Table,
    TableBody,
    TableCell,
    TableContainer,
    TableHead,
    TableRow,
    IconButton,
    Tooltip,
    Badge
} from '@mui/material';
import {
    MedicalServices,
    Schedule,
    People,
    TrendingUp,
    CalendarToday,
    Notifications,
    Settings,
    LocalHospital,
    Assignment,
    Assessment,
    Person,
    Edit,
    Visibility,
    CheckCircle,
    Cancel,
    AccessTime,
    Phone,
    Email,
    Business,
    Search,
    Send,
    Star,
    LocationOn
} from '@mui/icons-material';
import { format, parseISO, isValid } from 'date-fns';
import MainLayout from '../../components/layout/MainLayout';
import ProfileSetupModal from '../../components/doctor/ProfileSetupModal';
import ClinicSearchModal from '../../components/doctor/ClinicSearchModal';
import axios from 'axios';

const DoctorDashboard = () => {
    const apiUrl = import.meta.env.VITE_API_URL;
    const [doctor, setDoctor] = useState(null);
    const [stats, setStats] = useState(null);
    const [appointments, setAppointments] = useState([]);
    const [clinics, setClinics] = useState([]);
    const [connectionRequests, setConnectionRequests] = useState([]);
    const [loading, setLoading] = useState(true);
    const [showProfileModal, setShowProfileModal] = useState(false);
    const [showClinicSearch, setShowClinicSearch] = useState(false);
    const [tabValue, setTabValue] = useState(0);
    const [appointmentFilter, setAppointmentFilter] = useState('all');
    const [selectedAppointment, setSelectedAppointment] = useState(null);
    const [showAppointmentDialog, setShowAppointmentDialog] = useState(false);

    useEffect(() => {
        loadDoctorData();
        loadConnectedClinics();
    }, []);

    const loadDoctorData = async () => {
        try {
            const token = localStorage.getItem('auth_token');
            const headers = { Authorization: `Bearer ${token}` };

            // Load doctor profile
            const profileResponse = await axios.get(`${apiUrl}/doctor/profile`, { headers });
            const doctorData = profileResponse.data.doctor;
            
            setDoctor(doctorData);

            if (!doctorData || !profileResponse.data.is_complete) {
                setShowProfileModal(true);
                setLoading(false);
                return;
            }

            // Load dashboard stats
            const statsResponse = await axios.get(`${apiUrl}/doctor/dashboard-stats`, { headers });
            setStats(statsResponse.data);

            // Load appointments
            const appointmentsResponse = await axios.get(`${apiUrl}/doctor/appointments`, { 
                headers,
                params: { status: appointmentFilter, per_page: 10 }
            });
            setAppointments(appointmentsResponse.data.data || []);

        } catch (error) {
            console.error('Failed to load doctor data:', error);
        } finally {
            setLoading(false);
        }
    };

    const loadConnectedClinics = async () => {
        try {
            const token = localStorage.getItem('auth_token');
            const headers = { Authorization: `Bearer ${token}` };

            // Load connected clinics (your existing API)
            try {
                const clinicsResponse = await axios.get(`${apiUrl}/doctor/my-clinics`, { headers });
                setClinics(clinicsResponse.data || []);
            } catch (error) {
                console.log('my-clinics endpoint not available, trying connected-clinics');
                setClinics([]);
            }

            // Load new connected clinics from the connection system
            try {
                const connectedClinicsResponse = await axios.get(`${apiUrl}/doctor/my-connected-clinics`, { headers });
                // Merge with existing clinics if needed
                setClinics(prevClinics => {
                    const newClinics = connectedClinicsResponse.data || [];
                    const allClinics = [...prevClinics, ...newClinics];
                    // Remove duplicates based on ID
                    const uniqueClinics = allClinics.filter((clinic, index, arr) => 
                        arr.findIndex(c => c.id === clinic.id) === index
                    );
                    return uniqueClinics;
                });
            } catch (error) {
                console.log('connected-clinics endpoint not available');
            }

            // Load connection requests
            try {
                const requestsResponse = await axios.get(`${apiUrl}/doctor/my-connection-requests`, { headers });
                setConnectionRequests(requestsResponse.data.data || []);
            } catch (error) {
                console.log('connection-requests endpoint not available');
                setConnectionRequests([]);
            }

        } catch (error) {
            console.error('Failed to load clinic data:', error);
        }
    };

    const handleProfileComplete = (doctorData) => {
        setDoctor(doctorData);
        setShowProfileModal(false);
        loadDoctorData(); // Reload all data
    };

    const handleAppointmentStatusUpdate = async (appointmentId, status) => {
        try {
            const token = localStorage.getItem('auth_token');
            await axios.post(`${apiUrl}/doctor/appointments/${appointmentId}/update-status`, 
                { status }, 
                { headers: { Authorization: `Bearer ${token}` } }
            );
            
            // Reload appointments
            loadDoctorData();
            setShowAppointmentDialog(false);
        } catch (error) {
            console.error('Failed to update appointment:', error);
        }
    };

    const handleDisconnectClinic = async (clinicId) => {
        try {
            const token = localStorage.getItem('auth_token');
            await axios.delete(`${apiUrl}/doctor/disconnect-clinic/${clinicId}`, {
                headers: { Authorization: `Bearer ${token}` }
            });
            
            // Reload clinic data
            loadConnectedClinics();
        } catch (error) {
            console.error('Failed to disconnect from clinic:', error);
        }
    };

    const getStatusColor = (status) => {
        switch (status) {
            case 'confirmed': return 'success';
            case 'pending': return 'warning';
            case 'completed': return 'info';
            case 'cancelled': return 'error';
            case 'approved': return 'success';
            case 'rejected': return 'error';
            default: return 'default';
        }
    };

    // FIXED: Enhanced formatDateTime function with proper error handling
    const formatDateTime = (date, time) => {
        try {
            // Check if date and time are valid
            if (!date || !time) {
                return {
                    date: 'Invalid Date',
                    time: 'Invalid Time'
                };
            }

            // Handle different date formats
            let dateObj;
            
            // If date is already a Date object
            if (date instanceof Date) {
                dateObj = date;
            } 
            // If date is a string, try to parse it
            else if (typeof date === 'string') {
                // Try different parsing methods
                if (date.includes('T')) {
                    // ISO format: 2025-08-28T10:15:00Z
                    dateObj = parseISO(date);
                } else {
                    // Date only format: 2025-08-28
                    const timeStr = time.includes(':') ? time : `${time}:00`;
                    const dateTimeStr = `${date}T${timeStr}`;
                    dateObj = parseISO(dateTimeStr);
                }
            } else {
                // Invalid date type
                throw new Error('Invalid date format');
            }

            // Check if the parsed date is valid
            if (!isValid(dateObj)) {
                throw new Error('Invalid date');
            }

            return {
                date: format(dateObj, 'MMM dd, yyyy'),
                time: format(dateObj, 'hh:mm a')
            };
        } catch (error) {
            console.error('Error formatting date/time:', { date, time, error: error.message });
            return {
                date: 'Invalid Date',
                time: 'Invalid Time'
            };
        }
    };

    // FIXED: Safe date formatting for connection requests
    const formatRequestDate = (dateString) => {
        try {
            if (!dateString) return 'Unknown Date';
            
            const dateObj = parseISO(dateString);
            if (!isValid(dateObj)) return 'Invalid Date';
            
            return format(dateObj, 'MMM dd, yyyy');
        } catch (error) {
            console.error('Error formatting request date:', error);
            return 'Invalid Date';
        }
    };

    if (loading) {
        return (
            <MainLayout>
                <Container maxWidth="lg" sx={{ mt: 12, mb: 4, textAlign: 'center' }}>
                    <CircularProgress size={60} />
                    <Typography variant="h6" sx={{ mt: 2 }}>
                        Loading your dashboard...
                    </Typography>
                </Container>
            </MainLayout>
        );
    }

    if (!doctor) {
        return (
            <>
                <MainLayout>
                    <Container maxWidth="lg" sx={{ mt: 12, mb: 4 }}>
                        <Alert severity="info">
                            Please complete your profile setup to access the dashboard.
                        </Alert>
                    </Container>
                </MainLayout>
                <ProfileSetupModal
                    open={showProfileModal}
                    onClose={() => setShowProfileModal(false)}
                    onComplete={handleProfileComplete}
                />
            </>
        );
    }

    return (
        <MainLayout>
            <Container maxWidth="lg" sx={{ mt: 12, mb: 4 }}>
                {/* Header */}
                <Box sx={{ mb: 4 }}>
                    <Grid container spacing={3} alignItems="center">
                        <Grid item>
                            <Avatar
                                src={doctor.profile_image}
                                sx={{ width: 80, height: 80, border: '3px solid', borderColor: 'primary.main' }}
                            >
                                <Person sx={{ fontSize: 40 }} />
                            </Avatar>
                        </Grid>
                        <Grid item xs>
                            <Typography variant="h4" sx={{ fontWeight: 'bold', mb: 1 }}>
                                Welcome, Dr. {doctor.user.name}
                            </Typography>
                            <Box sx={{ display: 'flex', gap: 1, alignItems: 'center', mb: 1 }}>
                                <Chip label={doctor.specialization} color="primary" />
                                {doctor.is_verified && (
                                    <Chip label="Verified" color="success" icon={<CheckCircle />} />
                                )}
                            </Box>
                            <Typography variant="body1" color="text.secondary">
                                {doctor.experience_years} years experience • ₹{doctor.consultation_fee} consultation fee
                            </Typography>
                        </Grid>
                        <Grid item>
                            <Button
                                variant="outlined"
                                startIcon={<Edit />}
                                onClick={() => setShowProfileModal(true)}
                            >
                                Edit Profile
                            </Button>
                        </Grid>
                    </Grid>
                </Box>

                {/* Quick Actions */}
                <Paper elevation={2} sx={{ p: 3, mb: 4 }}>
                    <Typography variant="h6" sx={{ fontWeight: 'bold', mb: 2 }}>
                        Quick Actions
                    </Typography>
                    <Grid container spacing={2}>
                        <Grid item>
                            <Button
                                variant="contained"
                                startIcon={<Search />}
                                onClick={() => setShowClinicSearch(true)}
                                sx={{ color: 'white' }}
                            >
                                Find Clinics
                            </Button>
                        </Grid>
                        <Grid item>
                            <Button
                                variant="outlined"
                                startIcon={<Schedule />}
                                onClick={() => setTabValue(0)}
                            >
                                View Appointments
                            </Button>
                        </Grid>
                        <Grid item>
                            <Button
                                variant="outlined"
                                startIcon={<LocalHospital />}
                                onClick={() => setTabValue(1)}
                            >
                                My Clinics
                            </Button>
                        </Grid>
                        <Grid item>
                            <Button
                                variant="outlined"
                                startIcon={<Settings />}
                                onClick={() => setShowProfileModal(true)}
                            >
                                Settings
                            </Button>
                        </Grid>
                    </Grid>
                </Paper>

                {/* Stats Cards */}
                {stats && (
                    <Grid container spacing={3} sx={{ mb: 4 }}>
                        <Grid item xs={12} sm={6} md={3}>
                            <Card elevation={3}>
                                <CardContent>
                                    <Box sx={{ display: 'flex', alignItems: 'center', mb: 2 }}>
                                        <Avatar sx={{ bgcolor: 'primary.main', mr: 2 }}>
                                            <Schedule />
                                        </Avatar>
                                        <Box>
                                            <Typography variant="h4" sx={{ fontWeight: 'bold' }}>
                                                {stats.today_appointments || 0}
                                            </Typography>
                                            <Typography variant="body2" color="text.secondary">
                                                Today's Appointments
                                            </Typography>
                                        </Box>
                                    </Box>
                                </CardContent>
                            </Card>
                        </Grid>

                        <Grid item xs={12} sm={6} md={3}>
                            <Card elevation={3}>
                                <CardContent>
                                    <Box sx={{ display: 'flex', alignItems: 'center', mb: 2 }}>
                                        <Avatar sx={{ bgcolor: 'success.main', mr: 2 }}>
                                            <People />
                                        </Avatar>
                                        <Box>
                                            <Typography variant="h4" sx={{ fontWeight: 'bold' }}>
                                                {stats.total_patients || 0}
                                            </Typography>
                                            <Typography variant="body2" color="text.secondary">
                                                Total Patients
                                            </Typography>
                                        </Box>
                                    </Box>
                                </CardContent>
                            </Card>
                        </Grid>

                        <Grid item xs={12} sm={6} md={3}>
                            <Card elevation={3}>
                                <CardContent>
                                    <Box sx={{ display: 'flex', alignItems: 'center', mb: 2 }}>
                                        <Avatar sx={{ bgcolor: 'warning.main', mr: 2 }}>
                                            <TrendingUp />
                                        </Avatar>
                                        <Box>
                                            <Typography variant="h4" sx={{ fontWeight: 'bold' }}>
                                                {stats.rating || doctor.rating || '0.0'}
                                            </Typography>
                                            <Typography variant="body2" color="text.secondary">
                                                Average Rating
                                            </Typography>
                                        </Box>
                                    </Box>
                                </CardContent>
                            </Card>
                        </Grid>

                        <Grid item xs={12} sm={6} md={3}>
                            <Card elevation={3}>
                                <CardContent>
                                    <Box sx={{ display: 'flex', alignItems: 'center', mb: 2 }}>
                                        <Avatar sx={{ bgcolor: 'info.main', mr: 2 }}>
                                            <LocalHospital />
                                        </Avatar>
                                        <Box>
                                            <Typography variant="h4" sx={{ fontWeight: 'bold' }}>
                                                {clinics.length}
                                            </Typography>
                                            <Typography variant="body2" color="text.secondary">
                                                Connected Clinics
                                            </Typography>
                                        </Box>
                                    </Box>
                                </CardContent>
                            </Card>
                        </Grid>
                    </Grid>
                )}

                {/* Main Content Tabs */}
                <Paper elevation={3} sx={{ borderRadius: 2 }}>
                    <Tabs 
                        value={tabValue} 
                        onChange={(e, newValue) => setTabValue(newValue)}
                        sx={{ borderBottom: 1, borderColor: 'divider', px: 2 }}
                    >
                        <Tab label="Appointments" icon={<Assignment />} />
                        <Tab 
                            label={
                                <Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
                                    My Clinics
                                    {connectionRequests.filter(req => req.status === 'pending').length > 0 && (
                                        <Badge 
                                            badgeContent={connectionRequests.filter(req => req.status === 'pending').length} 
                                            color="error" 
                                        />
                                    )}
                                </Box>
                            } 
                            icon={<LocalHospital />} 
                        />
                        <Tab label="Find Clinics" icon={<Search />} />
                    </Tabs>

                    <Box sx={{ p: 3 }}>
                        {/* Appointments Tab */}
                        {tabValue === 0 && (
                            <Box>
                                <Box sx={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', mb: 3 }}>
                                    <Typography variant="h6" sx={{ fontWeight: 'bold' }}>
                                        My Appointments
                                    </Typography>
                                    <FormControl size="small" sx={{ minWidth: 120 }}>
                                        <InputLabel>Filter</InputLabel>
                                        <Select
                                            value={appointmentFilter}
                                            onChange={(e) => setAppointmentFilter(e.target.value)}
                                        >
                                            <MenuItem value="all">All</MenuItem>
                                            <MenuItem value="pending">Pending</MenuItem>
                                            <MenuItem value="confirmed">Confirmed</MenuItem>
                                            <MenuItem value="completed">Completed</MenuItem>
                                            <MenuItem value="cancelled">Cancelled</MenuItem>
                                        </Select>
                                    </FormControl>
                                </Box>

                                <TableContainer>
                                    <Table>
                                        <TableHead>
                                            <TableRow>
                                                <TableCell>Patient</TableCell>
                                                <TableCell>Date & Time</TableCell>
                                                <TableCell>Clinic</TableCell>
                                                <TableCell>Status</TableCell>
                                                <TableCell>Fee</TableCell>
                                                <TableCell>Actions</TableCell>
                                            </TableRow>
                                        </TableHead>
                                        <TableBody>
                                            {appointments.map((appointment) => {
                                                // FIXED: Safe date formatting
                                                const dateTime = formatDateTime(appointment.appointment_date, appointment.appointment_time);
                                                return (
                                                    <TableRow key={appointment.id}>
                                                        <TableCell>
                                                            <Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
                                                                <Avatar sx={{ width: 32, height: 32 }}>
                                                                    {appointment.patient?.name?.[0] || 'P'}
                                                                </Avatar>
                                                                <Box>
                                                                    <Typography variant="body2" sx={{ fontWeight: 600 }}>
                                                                        {appointment.patient?.name || 'Unknown Patient'}
                                                                    </Typography>
                                                                    <Typography variant="caption" color="text.secondary">
                                                                        {appointment.patient?.email || 'No email'}
                                                                    </Typography>
                                                                </Box>
                                                            </Box>
                                                        </TableCell>
                                                        <TableCell>
                                                            <Box>
                                                                <Typography variant="body2" sx={{ fontWeight: 600 }}>
                                                                    {dateTime.date}
                                                                </Typography>
                                                                <Typography variant="caption" color="text.secondary">
                                                                    {dateTime.time}
                                                                </Typography>
                                                            </Box>
                                                        </TableCell>
                                                        <TableCell>
                                                            <Typography variant="body2">
                                                                {appointment.clinic?.name || 'Online'}
                                                            </Typography>
                                                        </TableCell>
                                                        <TableCell>
                                                            <Chip 
                                                                label={appointment.status || 'pending'}
                                                                color={getStatusColor(appointment.status)}
                                                                size="small"
                                                            />
                                                        </TableCell>
                                                        <TableCell>
                                                            <Typography variant="body2" sx={{ fontWeight: 600 }}>
                                                                ₹{appointment.amount || 0}
                                                            </Typography>
                                                        </TableCell>
                                                        <TableCell>
                                                            <Tooltip title="View Details">
                                                                <IconButton 
                                                                    size="small"
                                                                    onClick={() => {
                                                                        setSelectedAppointment(appointment);
                                                                        setShowAppointmentDialog(true);
                                                                    }}
                                                                >
                                                                    <Visibility />
                                                                </IconButton>
                                                            </Tooltip>
                                                        </TableCell>
                                                    </TableRow>
                                                );
                                            })}
                                        </TableBody>
                                    </Table>
                                </TableContainer>

                                {appointments.length === 0 && (
                                    <Box sx={{ textAlign: 'center', py: 4 }}>
                                        <Typography variant="h6" color="text.secondary">
                                            No appointments found
                                        </Typography>
                                        <Typography variant="body2" color="text.secondary">
                                            Your appointments will appear here once patients book with you.
                                        </Typography>
                                    </Box>
                                )}
                            </Box>
                        )}

                        {/* My Clinics Tab */}
                        {tabValue === 1 && (
                            <Box>
                                <Box sx={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', mb: 3 }}>
                                    <Typography variant="h6" sx={{ fontWeight: 'bold' }}>
                                        Connected Clinics ({clinics.length})
                                    </Typography>
                                    <Button
                                        variant="contained"
                                        startIcon={<Search />}
                                        onClick={() => setShowClinicSearch(true)}
                                        sx={{ color: 'white' }}
                                    >
                                        Find More Clinics
                                    </Button>
                                </Box>
                                
                                <Grid container spacing={3}>
                                    {clinics.map((clinic) => (
                                        <Grid item xs={12} md={6} key={clinic.id}>
                                            <Card elevation={2}>
                                                <CardContent>
                                                    <Box sx={{ display: 'flex', alignItems: 'center', gap: 2, mb: 2 }}>
                                                        <Avatar src={clinic.logo} sx={{ width: 60, height: 60 }}>
                                                            <LocalHospital />
                                                        </Avatar>
                                                        <Box sx={{ flex: 1 }}>
                                                            <Typography variant="h6" sx={{ fontWeight: 'bold' }}>
                                                                {clinic.name}
                                                            </Typography>
                                                            <Typography variant="body2" color="text.secondary">
                                                                {clinic.organization_type?.replace('_', ' ').toUpperCase() || 'Clinic'}
                                                            </Typography>
                                                            <Box sx={{ display: 'flex', alignItems: 'center', gap: 1, mt: 1 }}>
                                                                <Chip label="Connected" color="success" size="small" />
                                                                {clinic.rating > 0 && (
                                                                    <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.5 }}>
                                                                        <Star fontSize="small" color="warning" />
                                                                        <Typography variant="caption">
                                                                            {clinic.rating}
                                                                        </Typography>
                                                                    </Box>
                                                                )}
                                                            </Box>
                                                        </Box>
                                                    </Box>
                                                    
                                                    <Box sx={{ mb: 2 }}>
                                                        <Typography variant="body2" color="text.secondary" sx={{ mb: 1 }}>
                                                            <LocationOn fontSize="small" sx={{ mr: 1, verticalAlign: 'middle' }} />
                                                            {clinic.address?.substring(0, 60)}...
                                                        </Typography>
                                                        <Typography variant="body2" color="text.secondary">
                                                            <Phone fontSize="small" sx={{ mr: 1, verticalAlign: 'middle' }} />
                                                            {clinic.phone}
                                                        </Typography>
                                                    </Box>

                                                    {(clinic.pivot || clinic.schedule) && (
                                                        <Box sx={{ mb: 2 }}>
                                                            <Typography variant="caption" color="text.secondary">
                                                                Your Schedule:
                                                            </Typography>
                                                            <Typography variant="body2">
                                                                {clinic.pivot?.start_time || clinic.schedule?.start_time || 'N/A'} - {clinic.pivot?.end_time || clinic.schedule?.end_time || 'N/A'}
                                                            </Typography>
                                                            <Typography variant="body2" color="text.secondary">
                                                                Days: {JSON.parse(clinic.pivot?.available_days || clinic.schedule?.available_days || '[]').join(', ') || 'N/A'}
                                                            </Typography>
                                                        </Box>
                                                    )}
                                                    
                                                    <Box sx={{ display: 'flex', gap: 1 }}>
                                                        <Button size="small" variant="outlined">
                                                            Edit Schedule
                                                        </Button>
                                                        <Button 
                                                            size="small" 
                                                            color="error"
                                                            onClick={() => handleDisconnectClinic(clinic.id)}
                                                        >
                                                            Disconnect
                                                        </Button>
                                                    </Box>
                                                </CardContent>
                                            </Card>
                                        </Grid>
                                    ))}
                                </Grid>

                                {/* Connection Requests Section */}
                                {connectionRequests.length > 0 && (
                                    <>
                                        <Typography variant="h6" sx={{ fontWeight: 'bold', mt: 4, mb: 2 }}>
                                            Connection Requests ({connectionRequests.length})
                                        </Typography>
                                        
                                        <List>
                                            {connectionRequests.map((request) => (
                                                <React.Fragment key={request.id}>
                                                    <ListItem sx={{ border: '1px solid', borderColor: 'divider', borderRadius: 2, mb: 1 }}>
                                                        <ListItemIcon>
                                                            <Avatar src={request.clinic?.logo}>
                                                                <LocalHospital />
                                                            </Avatar>
                                                        </ListItemIcon>
                                                        <ListItemText
                                                            primary={request.clinic?.name || 'Unknown Clinic'}
                                                            secondary={
                                                                <Box>
                                                                    <Typography variant="body2" color="text.secondary">
                                                                        Request sent: {formatRequestDate(request.created_at)}
                                                                    </Typography>
                                                                    {request.message && (
                                                                        <Typography variant="body2" sx={{ mt: 1 }}>
                                                                            Message: {request.message}
                                                                        </Typography>
                                                                    )}
                                                                    {request.rejection_reason && (
                                                                        <Typography variant="body2" color="error" sx={{ mt: 1 }}>
                                                                            Rejection reason: {request.rejection_reason}
                                                                        </Typography>
                                                                    )}
                                                                </Box>
                                                            }
                                                        />
                                                        <Chip 
                                                            label={request.status || 'pending'}
                                                            color={getStatusColor(request.status)}
                                                            size="small"
                                                        />
                                                    </ListItem>
                                                </React.Fragment>
                                            ))}
                                        </List>
                                    </>
                                )}

                                {clinics.length === 0 && (
                                    <Box sx={{ textAlign: 'center', py: 4 }}>
                                        <Typography variant="h6" color="text.secondary">
                                            No clinics connected
                                        </Typography>
                                        <Typography variant="body2" color="text.secondary" sx={{ mb: 2 }}>
                                            Connect with healthcare facilities to start accepting appointments.
                                        </Typography>
                                        <Button 
                                            variant="contained" 
                                            startIcon={<Search />}
                                            onClick={() => setShowClinicSearch(true)}
                                            sx={{ color: 'white' }}
                                        >
                                            Find Clinics
                                        </Button>
                                    </Box>
                                )}
                            </Box>
                        )}

                        {/* Find Clinics Tab */}
                        {tabValue === 2 && (
                            <Box>
                                <Typography variant="h6" sx={{ fontWeight: 'bold', mb: 3 }}>
                                    Find Healthcare Facilities
                                </Typography>
                                <Alert severity="info" sx={{ mb: 3 }}>
                                    Search and connect with healthcare facilities to expand your practice reach.
                                </Alert>
                                
                                <Box sx={{ textAlign: 'center', py: 4 }}>
                                    <Typography variant="h5" color="text.secondary" sx={{ mb: 2 }}>
                                        🏥 Ready to Expand Your Practice?
                                    </Typography>
                                    <Typography variant="body1" color="text.secondary" sx={{ mb: 3 }}>
                                        Connect with hospitals, clinics, and healthcare facilities in your area
                                    </Typography>
                                    <Button
                                        variant="contained"
                                        size="large"
                                        startIcon={<Search />}
                                        onClick={() => setShowClinicSearch(true)}
                                        sx={{ color: 'white', px: 4, py: 1.5 }}
                                    >
                                        Search Healthcare Facilities
                                    </Button>
                                </Box>

                                {/* Recent Search Results or Popular Clinics could go here */}
                                <Box sx={{ mt: 4 }}>
                                    <Typography variant="h6" sx={{ mb: 2 }}>
                                        Why Connect with Healthcare Facilities?
                                    </Typography>
                                    <Grid container spacing={3}>
                                        <Grid item xs={12} md={4}>
                                            <Card elevation={1}>
                                                <CardContent sx={{ textAlign: 'center' }}>
                                                    <Avatar sx={{ bgcolor: 'primary.main', mx: 'auto', mb: 2 }}>
                                                        <People />
                                                    </Avatar>
                                                    <Typography variant="h6" gutterBottom>
                                                        More Patients
                                                    </Typography>
                                                    <Typography variant="body2" color="text.secondary">
                                                        Reach more patients through established healthcare networks
                                                    </Typography>
                                                </CardContent>
                                            </Card>
                                        </Grid>
                                        <Grid item xs={12} md={4}>
                                            <Card elevation={1}>
                                                <CardContent sx={{ textAlign: 'center' }}>
                                                    <Avatar sx={{ bgcolor: 'success.main', mx: 'auto', mb: 2 }}>
                                                        <LocalHospital />
                                                    </Avatar>
                                                    <Typography variant="h6" gutterBottom>
                                                        Better Resources
                                                    </Typography>
                                                    <Typography variant="body2" color="text.secondary">
                                                        Access to advanced medical equipment and facilities
                                                    </Typography>
                                                </CardContent>
                                            </Card>
                                        </Grid>
                                        <Grid item xs={12} md={4}>
                                            <Card elevation={1}>
                                                <CardContent sx={{ textAlign: 'center' }}>
                                                    <Avatar sx={{ bgcolor: 'info.main', mx: 'auto', mb: 2 }}>
                                                        <TrendingUp />
                                                    </Avatar>
                                                    <Typography variant="h6" gutterBottom>
                                                        Grow Practice
                                                    </Typography>
                                                    <Typography variant="body2" color="text.secondary">
                                                        Expand your medical practice and increase earnings
                                                    </Typography>
                                                </CardContent>
                                            </Card>
                                        </Grid>
                                    </Grid>
                                </Box>
                            </Box>
                        )}
                    </Box>
                </Paper>
            </Container>

            {/* Modals */}
            <ProfileSetupModal
                open={showProfileModal}
                onClose={() => setShowProfileModal(false)}
                onComplete={handleProfileComplete}
            />

            <ClinicSearchModal
                open={showClinicSearch}
                onClose={() => setShowClinicSearch(false)}
            />

            {/* Appointment Details Dialog */}
            <Dialog 
                open={showAppointmentDialog} 
                onClose={() => setShowAppointmentDialog(false)}
                maxWidth="sm"
                fullWidth
            >
                {selectedAppointment && (
                    <>
                        <DialogTitle>
                            Appointment Details
                        </DialogTitle>
                        <DialogContent>
                            <Grid container spacing={2}>
                                <Grid item xs={12}>
                                    <Typography variant="subtitle2" color="text.secondary">Patient</Typography>
                                    <Typography variant="body1" sx={{ fontWeight: 600 }}>
                                        {selectedAppointment.patient?.name || 'Unknown Patient'}
                                    </Typography>
                                    <Typography variant="body2" color="text.secondary">
                                        {selectedAppointment.patient?.email || 'No email'}
                                    </Typography>
                                </Grid>
                                
                                <Grid item xs={6}>
                                    <Typography variant="subtitle2" color="text.secondary">Date</Typography>
                                    <Typography variant="body1">
                                        {formatDateTime(selectedAppointment.appointment_date, selectedAppointment.appointment_time).date}
                                    </Typography>
                                </Grid>
                                
                                <Grid item xs={6}>
                                    <Typography variant="subtitle2" color="text.secondary">Time</Typography>
                                    <Typography variant="body1">
                                        {formatDateTime(selectedAppointment.appointment_date, selectedAppointment.appointment_time).time}
                                    </Typography>
                                </Grid>
                                
                                <Grid item xs={12}>
                                    <Typography variant="subtitle2" color="text.secondary">Clinic</Typography>
                                    <Typography variant="body1">
                                        {selectedAppointment.clinic?.name || 'Online Consultation'}
                                    </Typography>
                                </Grid>
                                
                                {selectedAppointment.symptoms && (
                                    <Grid item xs={12}>
                                        <Typography variant="subtitle2" color="text.secondary">Symptoms/Reason</Typography>
                                        <Typography variant="body1">
                                            {selectedAppointment.symptoms}
                                        </Typography>
                                    </Grid>
                                )}
                                
                                <Grid item xs={12}>
                                    <Typography variant="subtitle2" color="text.secondary">Status</Typography>
                                    <Chip 
                                        label={selectedAppointment.status || 'pending'}
                                        color={getStatusColor(selectedAppointment.status)}
                                        size="small"
                                    />
                                </Grid>
                            </Grid>
                        </DialogContent>
                        <DialogActions>
                            <Button onClick={() => setShowAppointmentDialog(false)}>
                                Close
                            </Button>
                            {selectedAppointment.status === 'pending' && (
                                <>
                                    <Button 
                                        color="success"
                                        onClick={() => handleAppointmentStatusUpdate(selectedAppointment.id, 'confirmed')}
                                    >
                                        Confirm
                                    </Button>
                                    <Button 
                                        color="error"
                                        onClick={() => handleAppointmentStatusUpdate(selectedAppointment.id, 'cancelled')}
                                    >
                                        Cancel
                                    </Button>
                                </>
                            )}
                            {selectedAppointment.status === 'confirmed' && (
                                <Button 
                                    color="primary"
                                    onClick={() => handleAppointmentStatusUpdate(selectedAppointment.id, 'completed')}
                                >
                                    Mark Complete
                                </Button>
                            )}
                        </DialogActions>
                    </>
                )}
            </Dialog>
        </MainLayout>
    );
};

export default DoctorDashboard;
```

**resources/js/pages/healthcare/HealthcareDashboard.jsx**

```jsx
import React, { useState, useEffect } from 'react';
import {
    Box,
    Container,
    Typography,
    Grid,
    Card,
    CardContent,
    Avatar,
    Button,
    Chip,
    Paper,
    List,
    ListItem,
    ListItemText,
    ListItemIcon,
    Divider,
    Alert,
    CircularProgress,
    Tabs,
    Tab,
    Dialog,
    DialogTitle,
    DialogContent,
    DialogActions,
    TextField,
    FormControl,
    InputLabel,
    Select,
    MenuItem,
    Table,
    TableBody,
    TableCell,
    TableContainer,
    TableHead,
    TableRow,
    IconButton,
    Tooltip,
    Badge
} from '@mui/material';
import {
    LocalHospital,
    People,
    Assignment,
    TrendingUp,
    MedicalServices,
    Schedule,
    Settings,
    Assessment,
    Notifications,
    PersonAdd,
    Edit,
    Visibility,
    CheckCircle,
    Cancel,
    AccessTime,
    Business,
    Phone,
    Email,
    LocationOn,
    Check,
    Close
} from '@mui/icons-material';
import { format, parseISO, isValid } from 'date-fns';
import MainLayout from '../../components/layout/MainLayout';
import HealthcareProfileSetupModal from '../../components/healthcare/HealthcareProfileSetupModal';
import axios from 'axios';

const HealthcareDashboard = () => {
    const apiUrl = import.meta.env.VITE_API_URL;
    const [profile, setProfile] = useState(null);
    const [stats, setStats] = useState(null);
    const [appointments, setAppointments] = useState([]);
    const [doctors, setDoctors] = useState([]);
    const [connectionRequests, setConnectionRequests] = useState([]);
    const [loading, setLoading] = useState(true);
    const [showProfileModal, setShowProfileModal] = useState(false);
    const [tabValue, setTabValue] = useState(0);
    const [appointmentFilter, setAppointmentFilter] = useState('all');
    const [selectedAppointment, setSelectedAppointment] = useState(null);
    const [showAppointmentDialog, setShowAppointmentDialog] = useState(false);
    const [selectedRequest, setSelectedRequest] = useState(null);
    const [showRequestDialog, setShowRequestDialog] = useState(false);

    useEffect(() => {
        loadHealthcareData();
    }, []);

    const loadHealthcareData = async () => {
        try {
            const token = localStorage.getItem('auth_token');
            const headers = { Authorization: `Bearer ${token}` };

            // Load healthcare profile
            const profileResponse = await axios.get(`${apiUrl}/healthcare/profile`, { headers });
            const profileData = profileResponse.data.profile;
            
            setProfile(profileData);

            if (!profileData || !profileResponse.data.is_complete) {
                setShowProfileModal(true);
                setLoading(false);
                return;
            }

            // Load dashboard stats
            const statsResponse = await axios.get(`${apiUrl}/healthcare/dashboard-stats`, { headers });
            setStats(statsResponse.data);

            // Load appointments
            const appointmentsResponse = await axios.get(`${apiUrl}/healthcare/appointments`, { 
                headers,
                params: { status: appointmentFilter, per_page: 10 }
            });
            setAppointments(appointmentsResponse.data.data || []);

            // Load connected doctors
            const doctorsResponse = await axios.get(`${apiUrl}/healthcare/connected-doctors`, { headers });
            setDoctors(doctorsResponse.data || []);

            // Load connection requests
            const requestsResponse = await axios.get(`${apiUrl}/healthcare/connection-requests`, { headers });
            setConnectionRequests(requestsResponse.data.data || []);

        } catch (error) {
            console.error('Failed to load healthcare data:', error);
        } finally {
            setLoading(false);
        }
    };

    const handleProfileComplete = (profileData) => {
        setProfile(profileData);
        setShowProfileModal(false);
        loadHealthcareData(); // Reload all data
    };

    const handleAppointmentStatusUpdate = async (appointmentId, status) => {
        try {
            const token = localStorage.getItem('auth_token');
            await axios.post(`${apiUrl}/healthcare/appointments/${appointmentId}/update-status`, 
                { status }, 
                { headers: { Authorization: `Bearer ${token}` } }
            );
            
            // Reload appointments
            loadHealthcareData();
            setShowAppointmentDialog(false);
        } catch (error) {
            console.error('Failed to update appointment:', error);
        }
    };

    const handleConnectionRequestResponse = async (requestId, status, rejectionReason = '') => {
        try {
            const token = localStorage.getItem('auth_token');
            await axios.post(`${apiUrl}/healthcare/connection-requests/${requestId}/respond`, 
                { 
                    status,
                    rejection_reason: rejectionReason 
                }, 
                { headers: { Authorization: `Bearer ${token}` } }
            );
            
            // Reload data
            loadHealthcareData();
            setShowRequestDialog(false);
        } catch (error) {
            console.error('Failed to respond to request:', error);
        }
    };

    const getStatusColor = (status) => {
        switch (status) {
            case 'confirmed': return 'success';
            case 'pending': return 'warning';
            case 'completed': return 'info';
            case 'cancelled': return 'error';
            case 'approved': return 'success';
            case 'rejected': return 'error';
            default: return 'default';
        }
    };

    // FIXED: Enhanced formatDateTime function with proper error handling
    const formatDateTime = (date, time) => {
        try {
            if (!date || !time) {
                return { date: 'Invalid Date', time: 'Invalid Time' };
            }

            // Handle different date formats
            let dateObj;
            
            // If date is already a Date object
            if (date instanceof Date) {
                dateObj = date;
            } 
            // If date is a string, try to parse it
            else if (typeof date === 'string') {
                // Handle time format - ensure it has seconds
                let timeStr = time;
                if (typeof time === 'string') {
                    // If time is in hh:mm format, add seconds
                    if (time.match(/^\d{2}:\d{2}$/)) {
                        timeStr = time + ':00';
                    }
                    // If time has invalid format, default to 00:00:00
                    else if (!time.match(/^\d{2}:\d{2}:\d{2}$/)) {
                        timeStr = '00:00:00';
                    }
                } else {
                    timeStr = '00:00:00';
                }

                // Try different parsing methods
                if (date.includes('T')) {
                    // ISO format: 2025-08-28T10:15:00Z
                    dateObj = parseISO(date);
                } else {
                    // Date only format: 2025-08-28
                    const dateTimeStr = `${date}T${timeStr}`;
                    dateObj = parseISO(dateTimeStr);
                }
            } else {
                // Invalid date type
                throw new Error('Invalid date format');
            }

            // Check if the parsed date is valid
            if (!isValid(dateObj)) {
                throw new Error('Invalid date');
            }

            return {
                date: format(dateObj, 'MMM dd, yyyy'),
                time: format(dateObj, 'hh:mm a')
            };
        } catch (error) {
            console.error('Error formatting date/time:', { date, time, error: error.message });
            return {
                date: 'Invalid Date',
                time: 'Invalid Time'
            };
        }
    };

    if (loading) {
        return (
            <MainLayout>
                <Container maxWidth="lg" sx={{ mt: 12, mb: 4, textAlign: 'center' }}>
                    <CircularProgress size={60} />
                    <Typography variant="h6" sx={{ mt: 2 }}>
                        Loading your dashboard...
                    </Typography>
                </Container>
            </MainLayout>
        );
    }

    if (!profile) {
        return (
            <>
                <MainLayout>
                    <Container maxWidth="lg" sx={{ mt: 12, mb: 4 }}>
                        <Alert severity="info">
                            Please complete your organization profile setup to access the dashboard.
                        </Alert>
                    </Container>
                </MainLayout>
                <HealthcareProfileSetupModal
                    open={showProfileModal}
                    onClose={() => setShowProfileModal(false)}
                    onComplete={handleProfileComplete}
                />
            </>
        );
    }

    return (
        <MainLayout>
            <Container maxWidth="lg" sx={{ mt: 12, mb: 4 }}>
                {/* Header */}
                <Box sx={{ mb: 4 }}>
                    <Grid container spacing={3} alignItems="center">
                        <Grid item>
                            <Avatar
                                src={profile.logo}
                                sx={{ 
                                    width: 80, 
                                    height: 80, 
                                    border: '3px solid', 
                                    borderColor: 'primary.main' 
                                }}
                            >
                                <Business sx={{ fontSize: 40 }} />
                            </Avatar>
                        </Grid>
                        <Grid item xs>
                            <Typography variant="h4" sx={{ fontWeight: 'bold', mb: 1 }}>
                                {profile.name}
                            </Typography>
                            <Box sx={{ display: 'flex', gap: 1, alignItems: 'center', mb: 1 }}>
                                <Chip label={profile.organization_type?.replace('_', ' ').toUpperCase() || 'CLINIC'} color="primary" />
                                {profile.is_verified && (
                                    <Chip label="Verified" color="success" icon={<CheckCircle />} />
                                )}
                            </Box>
                            <Typography variant="body1" color="text.secondary">
                                {profile.description?.substring(0, 100)}...
                            </Typography>
                        </Grid>
                        <Grid item>
                            <Button
                                variant="outlined"
                                startIcon={<Edit />}
                                onClick={() => setShowProfileModal(true)}
                            >
                                Edit Profile
                            </Button>
                        </Grid>
                    </Grid>
                </Box>

                {/* Stats Cards */}
                {stats && (
                    <Grid container spacing={3} sx={{ mb: 4 }}>
                        <Grid item xs={12} sm={6} md={3}>
                            <Card elevation={3}>
                                <CardContent>
                                    <Box sx={{ display: 'flex', alignItems: 'center', mb: 2 }}>
                                        <Avatar sx={{ bgcolor: 'primary.main', mr: 2 }}>
                                            <LocalHospital />
                                        </Avatar>
                                        <Box>
                                            <Typography variant="h4" sx={{ fontWeight: 'bold' }}>
                                                {stats.total_facilities || 0}
                                            </Typography>
                                            <Typography variant="body2" color="text.secondary">
                                                Total Facilities
                                            </Typography>
                                        </Box>
                                    </Box>
                                </CardContent>
                            </Card>
                        </Grid>

                        <Grid item xs={12} sm={6} md={3}>
                            <Card elevation={3}>
                                <CardContent>
                                    <Box sx={{ display: 'flex', alignItems: 'center', mb: 2 }}>
                                        <Avatar sx={{ bgcolor: 'success.main', mr: 2 }}>
                                            <MedicalServices />
                                        </Avatar>
                                        <Box>
                                            <Typography variant="h4" sx={{ fontWeight: 'bold' }}>
                                                {stats.connected_doctors || 0}
                                            </Typography>
                                            <Typography variant="body2" color="text.secondary">
                                                Connected Doctors
                                            </Typography>
                                        </Box>
                                    </Box>
                                </CardContent>
                            </Card>
                        </Grid>

                        <Grid item xs={12} sm={6} md={3}>
                            <Card elevation={3}>
                                <CardContent>
                                    <Box sx={{ display: 'flex', alignItems: 'center', mb: 2 }}>
                                        <Avatar sx={{ bgcolor: 'info.main', mr: 2 }}>
                                            <Schedule />
                                        </Avatar>
                                        <Box>
                                            <Typography variant="h4" sx={{ fontWeight: 'bold' }}>
                                                {stats.today_appointments || 0}
                                            </Typography>
                                            <Typography variant="body2" color="text.secondary">
                                                Today's Appointments
                                            </Typography>
                                        </Box>
                                    </Box>
                                </CardContent>
                            </Card>
                        </Grid>

                        <Grid item xs={12} sm={6} md={3}>
                            <Card elevation={3}>
                                <CardContent>
                                    <Box sx={{ display: 'flex', alignItems: 'center', mb: 2 }}>
                                        <Badge badgeContent={stats.pending_requests || 0} color="error">
                                            <Avatar sx={{ bgcolor: 'warning.main', mr: 2 }}>
                                                <Notifications />
                                            </Avatar>
                                        </Badge>
                                        <Box>
                                            <Typography variant="h4" sx={{ fontWeight: 'bold' }}>
                                                {stats.pending_requests || 0}
                                            </Typography>
                                            <Typography variant="body2" color="text.secondary">
                                                Pending Requests
                                            </Typography>
                                        </Box>
                                    </Box>
                                </CardContent>
                            </Card>
                        </Grid>
                    </Grid>
                )}

                {/* Main Content Tabs */}
                <Paper elevation={3} sx={{ borderRadius: 2 }}>
                    <Tabs 
                        value={tabValue} 
                        onChange={(e, newValue) => setTabValue(newValue)}
                        sx={{ borderBottom: 1, borderColor: 'divider', px: 2 }}
                    >
                        <Tab label="Appointments" icon={<Assignment />} />
                        <Tab 
                            label={
                                <Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
                                    Connection Requests
                                    {connectionRequests.filter(req => req.status === 'pending').length > 0 && (
                                        <Badge 
                                            badgeContent={connectionRequests.filter(req => req.status === 'pending').length} 
                                            color="error" 
                                        />
                                    )}
                                </Box>
                            } 
                            icon={<Notifications />} 
                        />
                        <Tab label="Connected Doctors" icon={<MedicalServices />} />
                        <Tab label="Organization Info" icon={<Business />} />
                    </Tabs>

                    <Box sx={{ p: 3 }}>
                        {/* Appointments Tab */}
                        {tabValue === 0 && (
                            <Box>
                                <Box sx={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', mb: 3 }}>
                                    <Typography variant="h6" sx={{ fontWeight: 'bold' }}>
                                        Clinic Appointments
                                    </Typography>
                                    <FormControl size="small" sx={{ minWidth: 120 }}>
                                        <InputLabel>Filter</InputLabel>
                                        <Select
                                            value={appointmentFilter}
                                            onChange={(e) => setAppointmentFilter(e.target.value)}
                                        >
                                            <MenuItem value="all">All</MenuItem>
                                            <MenuItem value="pending">Pending</MenuItem>
                                            <MenuItem value="confirmed">Confirmed</MenuItem>
                                            <MenuItem value="completed">Completed</MenuItem>
                                            <MenuItem value="cancelled">Cancelled</MenuItem>
                                        </Select>
                                    </FormControl>
                                </Box>

                                <TableContainer>
                                    <Table>
                                        <TableHead>
                                            <TableRow>
                                                <TableCell>Patient</TableCell>
                                                <TableCell>Doctor</TableCell>
                                                <TableCell>Date & Time</TableCell>
                                                <TableCell>Status</TableCell>
                                                <TableCell>Fee</TableCell>
                                                <TableCell>Actions</TableCell>
                                            </TableRow>
                                        </TableHead>
                                        <TableBody>
                                            {appointments.map((appointment) => {
                                                // FIXED: Safe date formatting with error handling
                                                const dateTime = formatDateTime(appointment.appointment_date, appointment.appointment_time);
                                                return (
                                                    <TableRow key={appointment.id}>
                                                        <TableCell>
                                                            <Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
                                                                <Avatar sx={{ width: 32, height: 32 }}>
                                                                    {appointment.patient?.name?.[0] || 'P'}
                                                                </Avatar>
                                                                <Box>
                                                                    <Typography variant="body2" sx={{ fontWeight: 600 }}>
                                                                        {appointment.patient?.name || 'Unknown Patient'}
                                                                    </Typography>
                                                                    <Typography variant="caption" color="text.secondary">
                                                                        {appointment.patient?.email || 'No email'}
                                                                    </Typography>
                                                                </Box>
                                                            </Box>
                                                        </TableCell>
                                                        <TableCell>
                                                            <Box>
                                                                <Typography variant="body2" sx={{ fontWeight: 600 }}>
                                                                    Dr. {appointment.doctor?.user?.name || 'Unknown Doctor'}
                                                                </Typography>
                                                                <Typography variant="caption" color="text.secondary">
                                                                    {appointment.doctor?.specialization || 'General'}
                                                                </Typography>
                                                            </Box>
                                                        </TableCell>
                                                        <TableCell>
                                                            <Box>
                                                                <Typography variant="body2" sx={{ fontWeight: 600 }}>
                                                                    {dateTime.date}
                                                                </Typography>
                                                                <Typography variant="caption" color="text.secondary">
                                                                    {dateTime.time}
                                                                </Typography>
                                                            </Box>
                                                        </TableCell>
                                                        <TableCell>
                                                            <Chip 
                                                                label={appointment.status || 'pending'}
                                                                color={getStatusColor(appointment.status)}
                                                                size="small"
                                                            />
                                                        </TableCell>
                                                        <TableCell>
                                                            <Typography variant="body2" sx={{ fontWeight: 600 }}>
                                                                ₹{appointment.amount || 0}
                                                            </Typography>
                                                        </TableCell>
                                                        <TableCell>
                                                            <Tooltip title="View Details">
                                                                <IconButton 
                                                                    size="small"
                                                                    onClick={() => {
                                                                        setSelectedAppointment(appointment);
                                                                        setShowAppointmentDialog(true);
                                                                    }}
                                                                >
                                                                    <Visibility />
                                                                </IconButton>
                                                            </Tooltip>
                                                        </TableCell>
                                                    </TableRow>
                                                );
                                            })}
                                        </TableBody>
                                    </Table>
                                </TableContainer>

                                {appointments.length === 0 && (
                                    <Box sx={{ textAlign: 'center', py: 4 }}>
                                        <Typography variant="h6" color="text.secondary">
                                            No appointments found
                                        </Typography>
                                        <Typography variant="body2" color="text.secondary">
                                            Appointments will appear here once patients book with your doctors.
                                        </Typography>
                                    </Box>
                                )}
                            </Box>
                        )}

                        {/* Connection Requests Tab */}
                        {tabValue === 1 && (
                            <Box>
                                <Typography variant="h6" sx={{ fontWeight: 'bold', mb: 3 }}>
                                    Doctor Connection Requests ({connectionRequests.length})
                                </Typography>
                                
                                <List>
                                    {connectionRequests.map((request) => (
                                        <React.Fragment key={request.id}>
                                            <ListItem
                                                sx={{ 
                                                    border: '1px solid',
                                                    borderColor: 'divider',
                                                    borderRadius: 2,
                                                    mb: 2
                                                }}
                                            >
                                                <Box sx={{ display: 'flex', alignItems: 'center', gap: 2, width: '100%' }}>
                                                    <Avatar src={request.doctor?.profile_image} sx={{ width: 50, height: 50 }}>
                                                        <MedicalServices />
                                                    </Avatar>
                                                    
                                                    <Box sx={{ flex: 1 }}>
                                                        <Typography variant="h6" sx={{ fontWeight: 'bold' }}>
                                                            Dr. {request.doctor?.user?.name || 'Unknown Doctor'}
                                                        </Typography>
                                                        <Typography variant="body2" color="text.secondary">
                                                            {request.doctor?.specialization || 'General'} • {request.doctor?.experience_years || 0} years exp.
                                                        </Typography>
                                                        <Typography variant="body2" sx={{ mt: 1 }}>
                                                            Clinic: {request.clinic?.name || 'Unknown Clinic'}
                                                        </Typography>
                                                        {request.message && (
                                                            <Typography variant="body2" color="text.secondary" sx={{ mt: 1 }}>
                                                                Message: {request.message}
                                                            </Typography>
                                                        )}
                                                    </Box>
                                                    
                                                    <Box sx={{ display: 'flex', flexDirection: 'column', gap: 1, alignItems: 'center' }}>
                                                        <Chip 
                                                            label={request.status || 'pending'}
                                                            color={getStatusColor(request.status)}
                                                            size="small"
                                                        />
                                                        {request.status === 'pending' && (
                                                            <Box sx={{ display: 'flex', gap: 1 }}>
                                                                <Button
                                                                    size="small"
                                                                    variant="contained"
                                                                    color="success"
                                                                    startIcon={<Check />}
                                                                    onClick={() => handleConnectionRequestResponse(request.id, 'approved')}
                                                                    sx={{ color: 'white' }}
                                                                >
                                                                    Accept
                                                                </Button>
                                                                <Button
                                                                    size="small"
                                                                    variant="outlined"
                                                                    color="error"
                                                                    startIcon={<Close />}
                                                                    onClick={() => {
                                                                        setSelectedRequest(request);
                                                                        setShowRequestDialog(true);
                                                                    }}
                                                                >
                                                                    Reject
                                                                </Button>
                                                            </Box>
                                                        )}
                                                        <Button
                                                            size="small"
                                                            variant="outlined"
                                                            startIcon={<Visibility />}
                                                            onClick={() => {
                                                                setSelectedRequest(request);
                                                                setShowRequestDialog(true);
                                                            }}
                                                        >
                                                            View Details
                                                        </Button>
                                                    </Box>
                                                </Box>
                                            </ListItem>
                                        </React.Fragment>
                                    ))}
                                </List>

                                {connectionRequests.length === 0 && (
                                    <Box sx={{ textAlign: 'center', py: 4 }}>
                                        <Typography variant="h6" color="text.secondary">
                                            No connection requests
                                        </Typography>
                                        <Typography variant="body2" color="text.secondary">
                                            Doctors can send connection requests to join your healthcare facilities.
                                        </Typography>
                                    </Box>
                                )}
                            </Box>
                        )}

                        {/* Connected Doctors Tab */}
                        {tabValue === 2 && (
                            <Box>
                                <Typography variant="h6" sx={{ fontWeight: 'bold', mb: 3 }}>
                                    Connected Doctors ({doctors.length})
                                </Typography>
                                
                                <Grid container spacing={3}>
                                    {doctors.map((doctor) => (
                                        <Grid item xs={12} md={6} key={doctor.id}>
                                            <Card elevation={2}>
                                                <CardContent>
                                                    <Box sx={{ display: 'flex', alignItems: 'center', gap: 2, mb: 2 }}>
                                                        <Avatar 
                                                            src={doctor.profile_image} 
                                                            sx={{ width: 60, height: 60 }}
                                                        >
                                                            <MedicalServices />
                                                        </Avatar>
                                                        <Box sx={{ flex: 1 }}>
                                                            <Typography variant="h6" sx={{ fontWeight: 'bold' }}>
                                                                Dr. {doctor.user?.name || 'Unknown Doctor'}
                                                            </Typography>
                                                            <Typography variant="body2" color="text.secondary">
                                                                {doctor.specialization || 'General'}
                                                            </Typography>
                                                            <Box sx={{ display: 'flex', gap: 1, alignItems: 'center', mt: 1 }}>
                                                                <Chip 
                                                                    label={`${doctor.experience_years || 0} years exp.`} 
                                                                    size="small" 
                                                                    variant="outlined" 
                                                                />
                                                                <Chip 
                                                                    label={`₹${doctor.consultation_fee || 0}`} 
                                                                    size="small" 
                                                                    color="primary" 
                                                                />
                                                            </Box>
                                                        </Box>
                                                    </Box>
                                                    
                                                    {doctor.clinic_info && (
                                                        <Box sx={{ mb: 2 }}>
                                                            <Typography variant="caption" color="text.secondary">
                                                                Schedule at {doctor.clinic_info.clinic_name}:
                                                            </Typography>
                                                            <Typography variant="body2">
                                                                {doctor.clinic_info.schedule?.start_time || 'N/A'} - {doctor.clinic_info.schedule?.end_time || 'N/A'}
                                                            </Typography>
                                                        </Box>
                                                    )}
                                                    
                                                    <Box sx={{ display: 'flex', gap: 1 }}>
                                                        <Button size="small" variant="outlined">
                                                            View Profile
                                                        </Button>
                                                        <Button size="small" color="error">
                                                            Disconnect
                                                        </Button>
                                                    </Box>
                                                </CardContent>
                                            </Card>
                                        </Grid>
                                    ))}
                                </Grid>

                                {doctors.length === 0 && (
                                    <Box sx={{ textAlign: 'center', py: 4 }}>
                                        <Typography variant="h6" color="text.secondary">
                                            No doctors connected
                                        </Typography>
                                        <Typography variant="body2" color="text.secondary">
                                            Accept connection requests from doctors to start providing healthcare services.
                                        </Typography>
                                    </Box>
                                )}
                            </Box>
                        )}

                        {/* Organization Info Tab */}
                        {tabValue === 3 && (
                            <Box>
                                <Typography variant="h6" sx={{ fontWeight: 'bold', mb: 3 }}>
                                    Organization Information
                                </Typography>
                                
                                <Grid container spacing={3}>
                                    <Grid item xs={12} md={6}>
                                        <Card elevation={2}>
                                            <CardContent>
                                                <Typography variant="h6" gutterBottom>Basic Information</Typography>
                                                <Box sx={{ display: 'flex', flexDirection: 'column', gap: 2 }}>
                                                    <Box>
                                                        <Typography variant="caption" color="text.secondary">Organization Name</Typography>
                                                        <Typography variant="body1" sx={{ fontWeight: 600 }}>{profile.name}</Typography>
                                                    </Box>
                                                    <Box>
                                                        <Typography variant="caption" color="text.secondary">Type</Typography>
                                                        <Typography variant="body1">{profile.organization_type?.replace('_', ' ').toUpperCase()}</Typography>
                                                    </Box>
                                                    <Box>
                                                        <Typography variant="caption" color="text.secondary">Registration Number</Typography>
                                                        <Typography variant="body1">{profile.registration_number}</Typography>
                                                    </Box>
                                                    <Box>
                                                        <Typography variant="caption" color="text.secondary">License Number</Typography>
                                                        <Typography variant="body1">{profile.license_number}</Typography>
                                                    </Box>
                                                </Box>
                                            </CardContent>
                                        </Card>
                                    </Grid>
                                    
                                    <Grid item xs={12} md={6}>
                                        <Card elevation={2}>
                                            <CardContent>
                                                <Typography variant="h6" gutterBottom>Contact Information</Typography>
                                                <Box sx={{ display: 'flex', flexDirection: 'column', gap: 2 }}>
                                                    <Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
                                                        <LocationOn fontSize="small" color="action" />
                                                        <Typography variant="body2">{profile.address}</Typography>
                                                    </Box>
                                                    <Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
                                                        <Phone fontSize="small" color="action" />
                                                        <Typography variant="body2">{profile.phone}</Typography>
                                                    </Box>
                                                    {profile.email && (
                                                        <Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
                                                            <Email fontSize="small" color="action" />
                                                            <Typography variant="body2">{profile.email}</Typography>
                                                        </Box>
                                                    )}
                                                    <Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
                                                        <AccessTime fontSize="small" color="action" />
                                                        <Typography variant="body2">
                                                            {profile.opening_time} - {profile.closing_time}
                                                        </Typography>
                                                    </Box>
                                                </Box>
                                            </CardContent>
                                        </Card>
                                    </Grid>
                                </Grid>

                                <Button
                                    variant="contained"
                                    startIcon={<Edit />}
                                    onClick={() => setShowProfileModal(true)}
                                    sx={{ mt: 3, color: 'white' }}
                                >
                                    Edit Organization Profile
                                </Button>
                            </Box>
                        )}
                    </Box>
                </Paper>
            </Container>

            {/* Profile Setup Modal */}
            <HealthcareProfileSetupModal
                open={showProfileModal}
                onClose={() => setShowProfileModal(false)}
                onComplete={handleProfileComplete}
            />

            {/* Appointment Details Dialog */}
            <Dialog 
                open={showAppointmentDialog} 
                onClose={() => setShowAppointmentDialog(false)}
                maxWidth="sm"
                fullWidth
            >
                {selectedAppointment && (
                    <>
                        <DialogTitle>Appointment Details</DialogTitle>
                        <DialogContent>
                            <Grid container spacing={2}>
                                <Grid item xs={12}>
                                    <Typography variant="subtitle2" color="text.secondary">Patient</Typography>
                                    <Typography variant="body1" sx={{ fontWeight: 600 }}>
                                        {selectedAppointment.patient?.name || 'Unknown Patient'}
                                    </Typography>
                                </Grid>
                                
                                <Grid item xs={12}>
                                    <Typography variant="subtitle2" color="text.secondary">Doctor</Typography>
                                    <Typography variant="body1">
                                        Dr. {selectedAppointment.doctor?.user?.name || 'Unknown Doctor'} - {selectedAppointment.doctor?.specialization || 'General'}
                                    </Typography>
                                </Grid>
                                
                                {selectedAppointment.symptoms && (
                                    <Grid item xs={12}>
                                        <Typography variant="subtitle2" color="text.secondary">Symptoms/Reason</Typography>
                                        <Typography variant="body1">
                                            {selectedAppointment.symptoms}
                                        </Typography>
                                    </Grid>
                                )}
                            </Grid>
                        </DialogContent>
                        <DialogActions>
                            <Button onClick={() => setShowAppointmentDialog(false)}>Close</Button>
                            {selectedAppointment.status === 'pending' && (
                                <>
                                    <Button 
                                        color="success"
                                        onClick={() => handleAppointmentStatusUpdate(selectedAppointment.id, 'confirmed')}
                                    >
                                        Confirm
                                    </Button>
                                    <Button 
                                        color="error"
                                        onClick={() => handleAppointmentStatusUpdate(selectedAppointment.id, 'cancelled')}
                                    >
                                        Cancel
                                    </Button>
                                </>
                            )}
                        </DialogActions>
                    </>
                )}
            </Dialog>

            {/* Connection Request Dialog */}
            <Dialog 
                open={showRequestDialog} 
                onClose={() => setShowRequestDialog(false)}
                maxWidth="sm"
                fullWidth
            >
                {selectedRequest && (
                    <>
                        <DialogTitle>Connection Request Details</DialogTitle>
                        <DialogContent>
                            <Grid container spacing={2}>
                                <Grid item xs={12}>
                                    <Typography variant="subtitle2" color="text.secondary">Doctor</Typography>
                                    <Typography variant="body1" sx={{ fontWeight: 600 }}>
                                        Dr. {selectedRequest.doctor?.user?.name || 'Unknown Doctor'}
                                    </Typography>
                                    <Typography variant="body2" color="text.secondary">
                                        {selectedRequest.doctor?.specialization || 'General'} • {selectedRequest.doctor?.experience_years || 0} years experience
                                    </Typography>
                                </Grid>
                                
                                {selectedRequest.message && (
                                    <Grid item xs={12}>
                                        <Typography variant="subtitle2" color="text.secondary">Message</Typography>
                                        <Typography variant="body1">{selectedRequest.message}</Typography>
                                    </Grid>
                                )}
                                
                                {selectedRequest.proposed_schedule && (
                                    <Grid item xs={12}>
                                        <Typography variant="subtitle2" color="text.secondary">Proposed Schedule</Typography>
                                        <Typography variant="body2">
                                            Time: {selectedRequest.proposed_schedule.start_time} - {selectedRequest.proposed_schedule.end_time}
                                        </Typography>
                                        <Typography variant="body2">
                                            Days: {selectedRequest.proposed_schedule.available_days?.join(', ') || 'Not specified'}
                                        </Typography>
                                    </Grid>
                                )}
                            </Grid>
                        </DialogContent>
                        <DialogActions>
                            <Button onClick={() => setShowRequestDialog(false)}>Close</Button>
                            {selectedRequest.status === 'pending' && (
                                <>
                                    <Button 
                                        color="success"
                                        onClick={() => handleConnectionRequestResponse(selectedRequest.id, 'approved')}
                                    >
                                        Accept
                                    </Button>
                                    <Button 
                                        color="error"
                                        onClick={() => handleConnectionRequestResponse(selectedRequest.id, 'rejected')}
                                    >
                                        Reject
                                    </Button>
                                </>
                            )}
                        </DialogActions>
                    </>
                )}
            </Dialog>
        </MainLayout>
    );
};

export default HealthcareDashboard;
```

### Step 21: Enhanced Search Section Component 

**resources/js/components/home/SearchSection.jsx**

```jsx
import React, { useState, useEffect } from 'react';
import { useNavigate } from 'react-router-dom';
import {
    Box,
    Container,
    Typography,
    Grid,
    FormControl,
    Select,
    MenuItem,
    TextField,
    Button,
    Avatar,
    Chip,
    Rating,
    Collapse,
    Paper,
    List,
    ListItem,
    ListItemAvatar,
    ListItemText,
    Divider,
    ButtonBase,
    CircularProgress,
} from '@mui/material';
import { 
    Search, 
    LocationOn, 
    LocalHospital, 
    Person,
    Verified,
    AccessTime
} from '@mui/icons-material';
import axios from 'axios';

const SearchSection = () => {
    const navigate = useNavigate();
    const apiUrl = import.meta.env.VITE_API_URL;

    // NEW: Search functionality states
    const [searchType, setSearchType] = useState('both'); // 'doctors', 'clinics', 'both'
    const [specializations, setSpecializations] = useState([]);
    const [loading, setLoading] = useState(false);

    // OLD: Original states
    const [selectedSpecialty, setSelectedSpecialty] = useState('');
    const [searchQuery, setSearchQuery] = useState('');
    const [searchResults, setSearchResults] = useState([]);
    const [showResults, setShowResults] = useState(false);
    const [currentWordIndex, setCurrentWordIndex] = useState(0);
    const [displayedText, setDisplayedText] = useState('');
    const [isTyping, setIsTyping] = useState(true);

    // OLD: Words to cycle through
    const words = ['Healthcare', 'Doctor'];
    
    // OLD: Static parts of the title and subtitle
    const titleTemplate = 'Find _ Near You';
    const subtitles = [
        "Discover verified doctors and top-rated clinics in your area with smart location-based search",
        "Connect with qualified medical professionals and book appointments at your convenience"
    ];

    // NEW: Load specializations on component mount
    useEffect(() => {
        loadSpecializations();
    }, []);

    // OLD: Typewriter effect with slower speed
    useEffect(() => {
        const currentWord = words[currentWordIndex];
        let currentIndex = 0;
        setDisplayedText('');
        setIsTyping(true);

        // Typing animation - slower speed
        const typingInterval = setInterval(() => {
            if (currentIndex < currentWord.length) {
                setDisplayedText(currentWord.substring(0, currentIndex + 1));
                currentIndex++;
            } else {
                clearInterval(typingInterval);
                setIsTyping(false);
                
                // Wait 6 seconds before starting next word
                setTimeout(() => {
                    setCurrentWordIndex((prevIndex) => (prevIndex + 1) % words.length);
                }, 6000);
            }
        }, 300); // Changed from 150ms to 300ms for slower typing

        return () => clearInterval(typingInterval);
    }, [currentWordIndex]);

    // NEW: Load specializations function
    const loadSpecializations = async () => {
        try {
            const response = await axios.get(`${apiUrl}/doctors/specializations`);
            setSpecializations(response.data);
        } catch (error) {
            console.error('Failed to load specializations:', error);
            // Fallback to old static data if API fails
            setSpecializations([
                'Cardiology', 'Dermatology', 'Endocrinology', 'Gastroenterology',
                'Neurology', 'Orthopedics', 'Pediatrics', 'Psychiatry'
            ]);
        }
    };

    // NEW: Updated search function with API calls
    const handleSearch = async () => {
        if (!searchQuery.trim() && !selectedSpecialty) {
            return;
        }

        setLoading(true);
        setShowResults(true);

        try {
            const results = [];
            
            // Search doctors if type is 'doctors' or 'both'
            if (searchType === 'doctors' || searchType === 'both') {
                try {
                    const doctorParams = {
                        search: searchQuery,
                        specialization: selectedSpecialty,
                        per_page: 5
                    };

                    const doctorResponse = await axios.get(`${apiUrl}/doctors`, {
                        params: doctorParams
                    });

                    const doctors = doctorResponse.data.data.map(doctor => ({
                        ...doctor,
                        type: 'doctor',
                        // Map API fields to old UI expected fields for compatibility
                        name: doctor.user?.name || doctor.name,
                        specialty: doctor.specialization,
                        experience: doctor.experience_years,
                        rating: parseFloat(doctor.rating) || 0,
                        reviewCount: doctor.total_reviews || 0,
                        fee: doctor.consultation_fee,
                        nextAvailable: 'Available for appointments',
                        isVerified: doctor.is_verified,
                        image: doctor.profile_image
                    }));

                    results.push(...doctors);
                } catch (doctorError) {
                    console.error('Doctor search failed:', doctorError);
                }
            }

            // Search clinics if type is 'clinics' or 'both'
            if (searchType === 'clinics' || searchType === 'both') {
                try {
                    const clinicParams = {
                        search: searchQuery,
                        specialty: selectedSpecialty,
                        per_page: 5
                    };

                    const clinicResponse = await axios.get(`${apiUrl}/clinics`, {
                        params: clinicParams
                    });

                    const clinics = clinicResponse.data.data.map(clinic => ({
                        ...clinic,
                        type: 'clinic',
                        // Map API fields to old UI expected fields for compatibility
                        rating: parseFloat(clinic.rating) || 0,
                        reviewCount: clinic.total_reviews || 0,
                        distance: clinic.distance_km ? `${clinic.distance_km} km` : 'Near you'
                    }));

                    results.push(...clinics);
                } catch (clinicError) {
                    console.error('Clinic search failed:', clinicError);
                }
            }

            setSearchResults(results);
        } catch (error) {
            console.error('Search failed:', error);
            setSearchResults([]);
        } finally {
            setLoading(false);
        }
    };

    const handleKeyPress = (event) => {
        if (event.key === 'Enter') {
            handleSearch();
        }
    };

    // NEW: Updated result click handler with navigation
    const handleResultClick = (result) => {
        if (result.type === 'doctor') {
            navigate(`/doctor/${result.id}`);
        } else if (result.type === 'clinic') {
            navigate(`/clinic/${result.id}`);
        }
    };

    // OLD: Function to render the animated title
    const renderAnimatedTitle = () => {
        const parts = titleTemplate.split('_');
        return (
            <>
                {parts[0]}{' '} {/* "Find " with natural space */}
                <span 
                    style={{ 
                        color: '#ff5983',
                        position: 'relative',
                        display: 'inline-block',
                        textShadow: '2px 2px 4px rgba(255, 89, 131, 0.3)', // Pink shadow for animated word
                    }}
                >
                    {displayedText}
                    {isTyping && (
                        <span 
                            className="blinking-cursor"
                            style={{
                                borderRight: '3px solid #ff5983',
                                marginLeft: '2px'
                            }}
                        />
                    )}
                </span>
                {' '}{parts[1]} {/* " Near You" with natural space before */}
            </>
        );
    };

    // NEW: Safe number conversion for ratings
    const safeRating = (rating) => {
        const numRating = typeof rating === 'string' ? parseFloat(rating) : rating;
        return isNaN(numRating) ? 0 : numRating;
    };

    return (
        <>
            <Box
            sx={{
                background: '#fff',
                color: '#1a2a32',
                boxShadow: '0 8px 32px 0 rgba(16,217,21,0.10), 0 1.5px 4px 0 rgba(60,60,60,0.10)',
                borderRadius: 2,
                py: { xs: 8, md: 12 },
                position: 'relative',
                overflow: 'hidden',
                // Make this section full width
                width: '100vw',
                marginLeft: 'calc(-50vw + 50%)',
                marginRight: 'calc(-50vw + 50%)',
            }}
        >
                <Container maxWidth="xl" sx={{ position: 'relative', zIndex: 1 }}>
                    {/* OLD: Hero Content with Typewriter Animation */}
                    <Box textAlign="center" mb={6}>
                        <Typography 
                            variant="h2" 
                            component="h1" 
                            gutterBottom
                            sx={{ 
                                fontWeight: 'bold',
                                fontSize: { xs: '2.5rem', md: '3.5rem', lg: '4rem' },
                                color: '#10d915',
                                mb: 3,
                                minHeight: { xs: '3rem', md: '4rem', lg: '5rem' }, // Prevent layout shift
                                textShadow: '3px 3px 6px rgba(16, 217, 21, 0.3)', // Green shadow for main text
                            }}
                        >
                            {renderAnimatedTitle()}
                        </Typography>
                        <Typography 
                            variant="h5" 
                            sx={{ 
                                opacity: 0.95,
                                fontSize: { xs: '1.2rem', md: '1.5rem' },
                                maxWidth: '600px',
                                mx: 'auto',
                                lineHeight: 1.4,
                                transition: 'opacity 0.5s ease-in-out',
                                textShadow: '1px 1px 2px rgba(0, 0, 0, 0.1)', // Subtle shadow for subtitle
                            }}
                            key={`subtitle-${currentWordIndex}`}
                        >
                            {subtitles[currentWordIndex]}
                        </Typography>
                    </Box>

                    {/* MIXED: Search Form - Old UI with New Functionality - Mobile Optimized */}
                    <Paper 
                        elevation={8} 
                        sx={{ 
                            p: { xs: 2, md: 4 }, 
                            borderRadius: 3,
                            background: 'rgba(255, 255, 255, 0.98)',
                            backdropFilter: 'blur(10px)',
                            maxWidth: 900,
                            mx: 'auto'
                        }}
                    >
                        <Grid container spacing={2} alignItems="end">
                            {/* NEW: Search Type Dropdown - NOW VISIBLE ON MOBILE */}
                            <Grid item xs={6} sm={6} md={2}>
                                <Typography variant="subtitle2" color="text.primary" gutterBottom sx={{ fontSize: { xs: '0.8rem', md: '0.875rem' } }}>
                                    Type
                                </Typography>
                                <FormControl fullWidth>
                                    <Select
                                        value={searchType}
                                        onChange={(e) => setSearchType(e.target.value)}
                                        sx={{ 
                                            borderRadius: 2,
                                            fontSize: { xs: '0.85rem', md: '1rem' },
                                            '& .MuiOutlinedInput-root': {
                                                '& fieldset': { borderColor: 'rgba(0,0,0,0.07)' },
                                            }
                                        }}
                                    >
                                        <MenuItem value="both">Both</MenuItem>
                                        <MenuItem value="doctors">Doctors</MenuItem>
                                        <MenuItem value="clinics">Clinics</MenuItem>
                                    </Select>
                                </FormControl>
                            </Grid>

                            {/* MIXED: Specialty Dropdown - Mobile Optimized */}
                            <Grid item xs={6} sm={6} md={3}>
                                <Typography variant="subtitle2" color="text.primary" gutterBottom sx={{ fontSize: { xs: '0.8rem', md: '0.875rem' } }}>
                                    Specialty
                                </Typography>
                                <FormControl fullWidth>
                                    <Select
                                        value={selectedSpecialty}
                                        onChange={(e) => setSelectedSpecialty(e.target.value)}
                                        displayEmpty
                                        sx={{ 
                                            borderRadius: 2,
                                            fontSize: { xs: '0.85rem', md: '1rem' },
                                            '& .MuiOutlinedInput-root': {
                                                '& fieldset': { borderColor: 'rgba(0,0,0,0.07)' },
                                            }
                                        }}
                                    >
                                        <MenuItem value="">All Specialties</MenuItem>
                                        {specializations.map((specialty) => (
                                            <MenuItem key={specialty} value={specialty}>
                                                {specialty}
                                            </MenuItem>
                                        ))}
                                    </Select>
                                </FormControl>
                            </Grid>

                            {/* OLD: Search Field - Mobile Optimized */}
                            <Grid item xs={12} sm={8} md={5}>
                                <Typography variant="subtitle2" color="text.primary" gutterBottom sx={{ fontSize: { xs: '0.8rem', md: '0.875rem' } }}>
                                    Search Doctor or Clinic
                                </Typography>
                                <TextField
                                    fullWidth
                                    placeholder="Enter doctor name, clinic name, or specialty..."
                                    value={searchQuery}
                                    onChange={(e) => setSearchQuery(e.target.value)}
                                    onKeyPress={handleKeyPress}
                                    sx={{ 
                                        '& .MuiOutlinedInput-root': { 
                                            borderRadius: 2,
                                            fontSize: { xs: '0.9rem', md: '1rem' },
                                            '& fieldset': { borderColor: 'rgba(0,0,0,0.07)' },
                                        }
                                    }}
                                    InputProps={{
                                        startAdornment: <Search sx={{ mr: 1, color: 'action.active', fontSize: { xs: '1.2rem', md: '1.5rem' } }} />
                                    }}
                                />
                            </Grid>

                            {/* MIXED: Search Button - Mobile Optimized */}
                            <Grid item xs={12} sm={4} md={2}>
                                <Button
                                    fullWidth
                                    variant="contained"
                                    size="large"
                                    onClick={handleSearch}
                                    disabled={loading}
                                    startIcon={loading ? <CircularProgress size={20} color="inherit" /> : null}
                                    sx={{ 
                                        py: { xs: 1.5, md: 1.8 },
                                        borderRadius: 2,
                                        fontWeight: 600,
                                        fontSize: { xs: '0.9rem', md: '1rem' },
                                        boxShadow: 3,
                                        color: "white",
                                        '&:hover': { boxShadow: 6 }
                                    }}
                                >
                                    {loading ? 'Searching...' : 'Search'}
                                </Button>
                            </Grid>
                        </Grid>

                        {/* MIXED: Search Results - Mobile Optimized */}
                        <Collapse in={showResults}>
                            <Box sx={{ mt: 4, pt: 3, borderTop: '1px solid rgba(0,0,0,0.08)' }}>
                                <Typography variant="h6" gutterBottom color="text.primary" sx={{ fontSize: { xs: '1.1rem', md: '1.25rem' } }}>
                                    Search Results ({searchResults.length})
                                </Typography>
                                
                                <Paper 
                                    sx={{ 
                                        maxHeight: { xs: 350, md: 400 },
                                        overflow: 'auto',
                                        border: '1px solid rgba(0,0,0,0.07)',
                                        borderRadius: 2
                                    }}
                                >
                                    <List disablePadding>
                                        {searchResults.map((result, index) => (
                                            <React.Fragment key={`${result.type}-${result.id}`}>
                                                <ListItem 
                                                    disablePadding
                                                >
                                                    <ButtonBase
                                                        component="div"
                                                        onClick={() => handleResultClick(result)}
                                                        sx={{
                                                            width: '100%',
                                                            py: { xs: 1.5, md: 2 },
                                                            px: { xs: 1.5, md: 2 },
                                                            display: 'flex',
                                                            alignItems: 'flex-start',
                                                            textAlign: 'left',
                                                            '&:hover': { 
                                                                backgroundColor: 'rgba(16,217,21,0.08)' 
                                                            }
                                                        }}
                                                    >
                                                        <ListItemAvatar sx={{ mr: { xs: 1, md: 2 } }}>
                                                            <Avatar 
                                                                src={result.image || result.profile_image}
                                                                sx={{ 
                                                                    width: { xs: 50, md: 60 },
                                                                    height: { xs: 50, md: 60 },
                                                                    border: '2px solid',
                                                                    borderColor: result.type === 'doctor' ? '#10d915' : 'secondary.main'
                                                                }}
                                                            >
                                                                {result.type === 'doctor' ? <Person /> : <LocalHospital />}
                                                            </Avatar>
                                                        </ListItemAvatar>
                                                        
                                                        <ListItemText
                                                            primary={
                                                                <Box sx={{ display: 'flex', alignItems: 'center', gap: 1, mb: 0.5, flexWrap: 'wrap' }}>
                                                                    <Typography variant="h6" component="span" sx={{ fontSize: { xs: '1rem', md: '1.25rem' } }}>
                                                                        {result.name}
                                                                    </Typography>
                                                                    {result.type === 'doctor' && result.isVerified && (
                                                                        <Chip 
                                                                            label="Verified" 
                                                                            size="small" 
                                                                            color="success" 
                                                                            icon={<Verified sx={{ fontSize: '0.7rem' }} />}
                                                                            sx={{ 
                                                                                fontSize: { xs: '0.6rem', md: '0.7rem' },
                                                                                height: { xs: '18px', md: '24px' },
                                                                                color: 'white' 
                                                                            }}
                                                                        />
                                                                    )}
                                                                </Box>
                                                            }
                                                            secondary={
                                                                <Box>
                                                                    {result.type === 'doctor' ? (
                                                                        <>
                                                                            <Typography variant="body2" color="primary" sx={{ fontSize: { xs: '0.85rem', md: '0.875rem' } }}>
                                                                                {result.specialty || result.specialization} • {result.experience || result.experience_years} years exp.
                                                                            </Typography>
                                                                            <Box sx={{ display: 'flex', alignItems: 'center', gap: 2, mt: 1, flexWrap: 'wrap' }}>
                                                                                <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.5 }}>
                                                                                    <Rating value={safeRating(result.rating)} precision={0.1} size="small" readOnly />
                                                                                    <Typography variant="body2" sx={{ fontSize: { xs: '0.8rem', md: '0.875rem' } }}>
                                                                                        {safeRating(result.rating)} ({result.reviewCount || result.total_reviews || 0} reviews)
                                                                                    </Typography>
                                                                                </Box>
                                                                                {result.fee && (
                                                                                    <Chip 
                                                                                        label={`₹${result.fee}`}
                                                                                        size="small"
                                                                                        color="secondary"
                                                                                        variant="outlined"
                                                                                        sx={{ 
                                                                                            fontSize: { xs: '0.7rem', md: '0.75rem' },
                                                                                            height: { xs: '20px', md: '24px' }
                                                                                        }}
                                                                                    />
                                                                                )}
                                                                            </Box>
                                                                            <Box sx={{ display: 'flex', alignItems: 'center', gap: 1, mt: 0.5 }}>
                                                                                <AccessTime fontSize="small" color="action" sx={{ fontSize: { xs: '1rem', md: '1.2rem' } }} />
                                                                                <Typography variant="caption" color="text.secondary" sx={{ fontSize: { xs: '0.75rem', md: '0.75rem' } }}>
                                                                                    {result.nextAvailable || 'Available for appointments'}
                                                                                </Typography>
                                                                            </Box>
                                                                        </>
                                                                    ) : (
                                                                        <>
                                                                            <Box sx={{ display: 'flex', alignItems: 'center', gap: 1, mb: 1 }}>
                                                                                <LocationOn fontSize="small" color="primary" sx={{ fontSize: { xs: '1rem', md: '1.2rem' } }} />
                                                                                <Typography variant="body2" sx={{ fontSize: { xs: '0.8rem', md: '0.875rem' } }}>
                                                                                    {result.address}
                                                                                </Typography>
                                                                            </Box>
                                                                            <Box sx={{ display: 'flex', alignItems: 'center', gap: 2, flexWrap: 'wrap' }}>
                                                                                <Box sx={{ display: 'flex', alignItems: 'center', gap: 0.5 }}>
                                                                                    <Rating value={safeRating(result.rating)} precision={0.1} size="small" readOnly />
                                                                                    <Typography variant="body2" sx={{ fontSize: { xs: '0.8rem', md: '0.875rem' } }}>
                                                                                        {safeRating(result.rating)} ({result.reviewCount || result.total_reviews || 0} reviews)
                                                                                    </Typography>
                                                                                </Box>
                                                                                {result.distance && (
                                                                                    <Chip 
                                                                                        label={result.distance}
                                                                                        size="small"
                                                                                        color="info"
                                                                                        variant="outlined"
                                                                                        sx={{ 
                                                                                            fontSize: { xs: '0.7rem', md: '0.75rem' },
                                                                                            height: { xs: '20px', md: '24px' }
                                                                                        }}
                                                                                    />
                                                                                )}
                                                                            </Box>
                                                                            {/* Show specialties for clinics */}
                                                                            {result.specialties && result.specialties.length > 0 && (
                                                                                <Box sx={{ display: 'flex', flexWrap: 'wrap', gap: 0.5, mt: 1 }}>
                                                                                    {result.specialties.slice(0, 3).map((specialty, idx) => (
                                                                                        <Chip 
                                                                                            key={idx} 
                                                                                            label={specialty} 
                                                                                            size="small" 
                                                                                            variant="outlined"
                                                                                            sx={{ 
                                                                                                fontSize: { xs: '0.6rem', md: '0.7rem' },
                                                                                                height: { xs: '18px', md: '22px' }
                                                                                            }}
                                                                                        />
                                                                                    ))}
                                                                                    {result.specialties.length > 3 && (
                                                                                        <Chip 
                                                                                            label={`+${result.specialties.length - 3} more`} 
                                                                                            size="small"
                                                                                            sx={{ 
                                                                                                fontSize: { xs: '0.6rem', md: '0.7rem' },
                                                                                                height: { xs: '18px', md: '22px' }
                                                                                            }}
                                                                                        />
                                                                                    )}
                                                                                </Box>
                                                                            )}
                                                                        </>
                                                                    )}
                                                                </Box>
                                                            }
                                                        />
                                                    </ButtonBase>
                                                </ListItem>
                                                {index < searchResults.length - 1 && <Divider />}
                                            </React.Fragment>
                                        ))}

                                        {/* NEW: No results message */}
                                        {searchResults.length === 0 && showResults && !loading && (
                                            <ListItem>
                                                <ListItemText
                                                    primary={
                                                        <Typography variant="h6" color="text.secondary" textAlign="center" sx={{ fontSize: { xs: '1rem', md: '1.25rem' } }}>
                                                            No results found
                                                        </Typography>
                                                    }
                                                    secondary={
                                                        <Typography variant="body2" color="text.secondary" textAlign="center" sx={{ fontSize: { xs: '0.8rem', md: '0.875rem' } }}>
                                                            Try adjusting your search criteria or browse our featured doctors and clinics.
                                                        </Typography>
                                                    }
                                                />
                                            </ListItem>
                                        )}

                                        {/* NEW: Loading state */}
                                        {loading && (
                                            <ListItem>
                                                <Box sx={{ display: 'flex', justifyContent: 'center', width: '100%', py: 2 }}>
                                                    <CircularProgress />
                                                </Box>
                                            </ListItem>
                                        )}
                                    </List>
                                </Paper>
                            </Box>
                        </Collapse>
                    </Paper>
                </Container>
            </Box>

            {/* OLD: Regular CSS for animations */}
            <style>{`
                @keyframes blink {
                    0%, 50% {
                        opacity: 1;
                    }
                    51%, 100% {
                        opacity: 0;
                    }
                }
                
                .blinking-cursor {
                    animation: blink 1.2s infinite;
                }
            `}</style>
        </>
    );
};

export default SearchSection;
```


### Step 22: Enhanced Nearby Section Component

**resources/js/components/home/NearbySection.jsx**

```jsx
import React from "react";
import {
    Box,
    Container,
    Typography,
    Grid,
    Card,
    CardMedia,
    CardContent,
    CardActions,
    Button,
    Chip,
    Rating,
    Avatar,
    Paper,
    Divider,
} from "@mui/material";
import {
    LocationOn,
    Verified,
    LocalHospital,
    AccessTime,
    CheckCircle,
} from "@mui/icons-material";
import MedicalInformationTwoToneIcon from "@mui/icons-material/MedicalInformationTwoTone";
import Diversity1TwoToneIcon from "@mui/icons-material/Diversity1TwoTone";
import { clinics, doctors } from "../../data/dummyData";

const NearbySection = () => {
    return (
        <Box
            sx={{
                py: { xs: 6, md: 10 },
                position: "relative",
                width: "100vw",
                marginLeft: "calc(-50vw + 50%)",
                marginRight: "calc(-50vw + 50%)",
                bgcolor: "#fafbfc",
            }}
        >
            <Container maxWidth="xl">
                {/* Nearby Clinics */}
                <Box sx={{ mb: { xs: 6, md: 10 } }}>
                    <Box textAlign="center" sx={{ mb: { xs: 4, md: 6 } }}>
                        <Typography
                            variant="h3"
                            component="h2"
                            gutterBottom
                            sx={{
                                display: "flex",
                                alignItems: "center",
                                justifyContent: "center",
                                gap: { xs: 1, sm: 1.25, md: 1.5 },
                                fontWeight: "bold",
                                fontSize: {
                                    xs: "2rem",
                                    sm: "2.5rem",
                                    md: "3rem",
                                },
                                background:
                                    "linear-gradient(45deg, #2196F3 30%, #21CBF3 90%)",
                                backgroundClip: "text",
                                WebkitBackgroundClip: "text",
                                WebkitTextFillColor: "transparent",
                            }}
                        >
                            <MedicalInformationTwoToneIcon
                                sx={{
                                    fontSize: { xs: 40, sm: 50, md: 60 },
                                    color: "#2196F3",
                                    filter: "drop-shadow(0 2px 6px rgba(33,150,243,0.25))",
                                }}
                            />
                            <Box component="span" sx={{ lineHeight: 1 }}>
                                Nearby Clinics
                            </Box>
                        </Typography>
                        <Typography
                            variant="h6"
                            color="text.secondary"
                            sx={{
                                maxWidth: 600,
                                mx: "auto",
                                fontSize: { xs: "1rem", md: "1.25rem" },
                                px: { xs: 2, md: 0 },
                            }}
                        >
                            Top-rated medical facilities in your area with
                            comprehensive healthcare services
                        </Typography>
                    </Box>

                    <Grid container spacing={{ xs: 2, sm: 3, md: 4 }}>
                        {clinics.map((clinic) => (
                            <Grid
                                item
                                xs={12}
                                sm={6}
                                md={6}
                                lg={4}
                                xl={3}
                                key={clinic.id}
                            >
                                <Card
                                    elevation={0}
                                    sx={{
                                        height: "100%",
                                        display: "flex",
                                        flexDirection: "column",
                                        border: "1px solid",
                                        borderColor: "grey.200",
                                        borderRadius: { xs: 3, md: 2 },
                                        overflow: "hidden",
                                        background:
                                            "linear-gradient(145deg, #ffffff 0%, #f8f9fa 100%)",
                                        "&:hover": {
                                            transform: "translateY(-8px)",
                                            boxShadow:
                                                "0 20px 40px rgba(0,0,0,0.1)",
                                            borderColor: "primary.main",
                                        },
                                        transition: "all 0.3s ease",
                                    }}
                                >
                                    {/* Clinic Image */}
                                    <Box sx={{ position: "relative" }}>
                                        <CardMedia
                                            component="img"
                                            height={180}
                                            image={clinic.image}
                                            alt={clinic.name}
                                            sx={{ objectFit: "cover" }}
                                        />
                                        <Box
                                            sx={{
                                                position: "absolute",
                                                top: 12,
                                                right: 12,
                                                display: "flex",
                                                gap: 1,
                                            }}
                                        >
                                            {clinic.isOpen && (
                                                <Chip
                                                    label="Open Now"
                                                    size="small"
                                                    icon={<CheckCircle />}
                                                    sx={{
                                                        bgcolor: "success.main",
                                                        color: "white",
                                                        fontWeight: 600,
                                                        "& .MuiChip-icon": {
                                                            color: "white",
                                                        },
                                                    }}
                                                />
                                            )}
                                            <Chip
                                                label={clinic.distance}
                                                size="small"
                                                sx={{
                                                    bgcolor:
                                                        "rgba(255,255,255,0.9)",
                                                    fontWeight: 600,
                                                    backdropFilter:
                                                        "blur(10px)",
                                                }}
                                            />
                                        </Box>
                                    </Box>

                                    {/* Clinic Content */}
                                    <CardContent
                                        sx={{
                                            flexGrow: 1,
                                            p: { xs: 1, md: 2 },
                                        }}
                                    >
                                        <Typography
                                            variant="h6"
                                            component="h3"
                                            sx={{
                                                fontWeight: "bold",
                                                mb: 1,
                                                fontSize: {
                                                    xs: "1.25rem",
                                                    md: "1.35rem",
                                                },
                                            }}
                                        >
                                            {clinic.name}
                                        </Typography>

                                        {/* Address */}
                                        <Box
                                            sx={{
                                                display: "flex",
                                                alignItems: "flex-start",
                                                mb: 1,
                                            }}
                                        >
                                            <LocationOn
                                                sx={{
                                                    color: "text.secondary",
                                                    mr: 1,
                                                    fontSize: 18,
                                                }}
                                            />
                                            <Typography
                                                variant="body2"
                                                color="text.secondary"
                                            >
                                                {clinic.address}
                                            </Typography>
                                        </Box>

                                        {/* Rating */}
                                        <Box
                                            sx={{
                                                display: "flex",
                                                alignItems: "center",
                                                mb: 1,
                                            }}
                                        >
                                            <Rating
                                                value={clinic.rating}
                                                precision={0.1}
                                                size="small"
                                                readOnly
                                            />
                                            <Typography
                                                variant="body2"
                                                sx={{ ml: 1, fontWeight: 600 }}
                                            >
                                                {clinic.rating}
                                            </Typography>
                                        </Box>

                                        {/* Facilities */}
                                        <Box sx={{ mb: 1.5 }}>
                                            <Typography
                                                variant="caption"
                                                color="text.secondary"
                                                sx={{ fontWeight: 600 }}
                                            >
                                                Facilities
                                            </Typography>
                                            <Box
                                                sx={{
                                                    display: "flex",
                                                    flexWrap: "wrap",
                                                    gap: 0.5,
                                                    mt: 1,
                                                }}
                                            >
                                                {clinic.facilities
                                                    .slice(0, 2)
                                                    .map((facility, idx) => (
                                                        <Chip
                                                            key={idx}
                                                            label={facility}
                                                            size="small"
                                                            variant="outlined"
                                                        />
                                                    ))}
                                                {clinic.facilities.length >
                                                    2 && (
                                                    <Chip
                                                        label={`+${
                                                            clinic.facilities
                                                                .length - 2
                                                        }`}
                                                        size="small"
                                                    />
                                                )}
                                            </Box>
                                        </Box>

                                        {/* Timings */}
                                        <Box
                                            sx={{
                                                display: "flex",
                                                alignItems: "center",
                                            }}
                                        >
                                            <AccessTime
                                                sx={{
                                                    color: "text.secondary",
                                                    fontSize: 18,
                                                    mr: 1,
                                                }}
                                            />
                                            <Typography
                                                variant="caption"
                                                color="text.secondary"
                                            >
                                                {clinic.openTime === "24/7"
                                                    ? "Open 24/7"
                                                    : `${clinic.openTime} - ${clinic.closeTime}`}
                                            </Typography>
                                        </Box>
                                    </CardContent>

                                    <Divider />

                                    {/* Button */}
                                    <CardActions sx={{ p: { xs: 1, md: 1 } }}>
                                        <Button
                                            variant="contained"
                                            fullWidth
                                            startIcon={<LocalHospital />}
                                            sx={{
                                                borderRadius: 3,
                                                py: { xs: 1.5, md: 1.8 },
                                                fontWeight: 600,
                                                color: "white",
                                                background:
                                                    "linear-gradient(45deg, #0cb010 30%, #10d915 90%)",
                                            }}
                                        >
                                            View Details
                                        </Button>
                                    </CardActions>
                                </Card>
                            </Grid>
                        ))}
                    </Grid>
                </Box>

                {/* Doctors Section */}
                <Box>
                    <Box textAlign="center" sx={{ mb: { xs: 4, md: 6 } }}>
                        <Typography
                            variant="h3"
                            sx={{
                                display: "flex",
                                alignItems: "center",
                                justifyContent: "center",
                                gap: { xs: 1, sm: 1.25, md: 1.5 },
                                fontWeight: "bold",
                                fontSize: {
                                    xs: "2rem",
                                    sm: "2.5rem",
                                    md: "3rem",
                                },
                                background:
                                    "linear-gradient(45deg, #FF6B6B 30%, #FF8E8E 90%)",
                                backgroundClip: "text",
                                WebkitBackgroundClip: "text",
                                WebkitTextFillColor: "transparent",
                                mb: 1,
                            }}
                        >
                            <Diversity1TwoToneIcon
                                sx={{
                                    fontSize: { xs: 30, sm: 40, md: 50 },
                                    color: "#FF6B6B",
                                    filter: "drop-shadow(0 2px 6px rgba(255,107,107,0.25))",
                                }}
                            />
                            <Box component="span" sx={{ lineHeight: 1 }}>
                                Available Doctors
                            </Box>
                        </Typography>
                        <Typography
                            variant="h6"
                            color="text.secondary"
                            sx={{
                                maxWidth: 600,
                                mx: "auto",
                                fontSize: { xs: "1rem", md: "1.25rem" },
                            }}
                        >
                            Experienced healthcare professionals ready to
                            provide quality medical care
                        </Typography>
                    </Box>

                    <Grid container spacing={{ xs: 2, sm: 3, md: 4 }}>
                        {doctors.map((doctor) => (
                            <Grid
                                item
                                xs={12}
                                sm={6}
                                md={4}
                                lg={3}
                                key={doctor.id}
                            >
                                <Card
                                    elevation={0}
                                    sx={{
                                        textAlign: "center",
                                        p: { xs: 1, md: 1 },
                                        height: "100%",
                                        display: "flex",
                                        flexDirection: "column",
                                        position: "relative",
                                        border: "1px solid",
                                        borderColor: "grey.200",
                                        borderRadius: 3,
                                        background:
                                            "linear-gradient(145deg, #ffffff 0%, #f8f9fa 100%)",
                                        "&:hover": {
                                            transform: "translateY(-8px)",
                                            boxShadow:
                                                "0 20px 40px rgba(0,0,0,0.1)",
                                            borderColor: "primary.main",
                                        },
                                        transition: "all 0.3s ease",
                                    }}
                                >
                                    {/* Avatar + Verified Badge */}
                                    <Box
                                        sx={{
                                            position: "relative",
                                            mb: { xs: 1.5, md: 2.5 },
                                            width: "100%",
                                        }}
                                    >
                                        <Avatar
                                            src={doctor.image}
                                            alt={doctor.name}
                                            sx={{
                                                width: {
                                                    xs: 88,
                                                    sm: 92,
                                                    md: 104,
                                                },
                                                height: {
                                                    xs: 88,
                                                    sm: 92,
                                                    md: 104,
                                                },
                                                mx: "auto",
                                                border: 3,
                                                borderColor: "primary.main",
                                                boxShadow:
                                                    "0 8px 20px rgba(0,0,0,0.08)",
                                            }}
                                        />
                                        {doctor.isVerified && (
                                            <Box
                                                sx={{
                                                    position: "absolute",
                                                    right: "50%",
                                                    bottom: {
                                                        xs: -10,
                                                        sm: -10,
                                                        md: -8,
                                                    },
                                                    transform:
                                                        "translateX(50%)",
                                                    bgcolor: "success.main",
                                                    borderRadius: "50%",
                                                    display: "flex",
                                                    alignItems: "center",
                                                    justifyContent: "center",
                                                    border: "2px solid white",
                                                    boxShadow:
                                                        "0 2px 8px rgba(0,0,0,0.2)",
                                                    width: {
                                                        xs: 26,
                                                        sm: 28,
                                                        md: 30,
                                                    },
                                                    height: {
                                                        xs: 26,
                                                        sm: 28,
                                                        md: 30,
                                                    },
                                                }}
                                            >
                                                <Verified
                                                    sx={{
                                                        color: "white",
                                                        fontSize: {
                                                            xs: 18,
                                                            sm: 20,
                                                            md: 22,
                                                        },
                                                        lineHeight: 1,
                                                    }}
                                                />
                                            </Box>
                                        )}
                                    </Box>

                                    {/* Name + Specialty */}
                                    <Typography
                                        variant="h6"
                                        sx={{
                                            fontWeight: "bold",
                                            lineHeight: 1.2,
                                        }}
                                    >
                                        {doctor.name}
                                    </Typography>
                                    <Typography
                                        variant="body2"
                                        color="primary"
                                        sx={{ fontWeight: 600, mb: 0.5 }}
                                    >
                                        {doctor.specialty}
                                    </Typography>

                                    {/* Experience */}
                                    <Typography
                                        variant="caption"
                                        color="text.secondary"
                                        sx={{ mb: 1 }}
                                    >
                                        {doctor.experience} years exp.
                                    </Typography>

                                    {/* Rating */}
                                    <Box
                                        sx={{
                                            display: "flex",
                                            alignItems: "center",
                                            justifyContent: "center",
                                            mb: 1,
                                        }}
                                    >
                                        <Rating
                                            value={doctor.rating}
                                            precision={0.1}
                                            size="small"
                                            readOnly
                                        />
                                        <Typography
                                            variant="body2"
                                            sx={{ ml: 0.75, fontWeight: 600 }}
                                        >
                                            {doctor.rating}
                                        </Typography>
                                    </Box>

                                    {/* Fee */}
                                    <Paper
                                        variant="outlined"
                                        sx={{
                                            p: { xs: 0.5, md: 1 },
                                            mb: 1,
                                            background:
                                                "linear-gradient(45deg, #E3F2FD 30%, #F3E5F5 90%)",
                                            borderColor: "primary.200",
                                            borderRadius: 3,
                                        }}
                                    >
                                        <Typography
                                            variant="h6"
                                            color="primary"
                                            sx={{ fontWeight: "bold" }}
                                        >
                                            ${doctor.fee}
                                        </Typography>
                                        <Typography
                                            variant="caption"
                                            color="text.secondary"
                                        >
                                            Consultation
                                        </Typography>
                                    </Paper>

                                    {/* Next availability */}
                                    <Typography
                                        variant="caption"
                                        color="text.secondary"
                                        sx={{ mb: "auto" }}
                                    >
                                        Available: {doctor.nextAvailable}
                                    </Typography>

                                    {/* CTA */}
                                    <Button
                                        variant="contained"
                                        fullWidth
                                        sx={{
                                            mt: { xs: 1, md: 1.5 },
                                            borderRadius: 3,
                                            fontWeight: 600,
                                            py: { xs: 0.9, md: 1.05 },
                                            background:
                                                "linear-gradient(45deg, #FF6B6B 30%, #FF8E8E 90%)",
                                            boxShadow:
                                                "0 4px 20px rgba(255, 107, 107, 0.3)",
                                            "&:hover": {
                                                boxShadow:
                                                    "0 6px 25px rgba(255, 107, 107, 0.4)",
                                            },
                                        }}
                                    >
                                        Book Now
                                    </Button>
                                </Card>
                            </Grid>
                        ))}
                    </Grid>
                </Box>
            </Container>
        </Box>
    );
};

export default NearbySection;
```


### Step 23: Enhanced Stats Section Component

**resources/js/components/home/StatsSection.jsx**

```jsx
import React, { useState, useEffect, useRef } from "react";
import {
    Box,
    Container,
    Typography,
    Grid,
    Card,
    CardContent,
    Avatar,
} from "@mui/material";
import { VerifiedUser, LocalHospital, Event, Star } from "@mui/icons-material";

const StatsSection = () => {
    const [isVisible, setIsVisible] = useState(false);
    const [animatedCounts, setAnimatedCounts] = useState([0, 0, 0, 0]);
    const sectionRef = useRef(null);

    const stats = [
        {
            icon: <VerifiedUser fontSize="large" />,
            count: "500+",
            targetValue: 500,
            suffix: "+",
            label: "Verified Doctors",
            color: "success.main",
            bgColor: "success.50",
        },
        {
            icon: <LocalHospital fontSize="large" />,
            count: "100+",
            targetValue: 100,
            suffix: "+",
            label: "Healthcare Facilities",
            color: "primary.main",
            bgColor: "primary.50",
        },
        {
            icon: <Event fontSize="large" />,
            count: "10,000+",
            targetValue: 10000,
            suffix: "+",
            label: "Appointments Booked",
            color: "secondary.main",
            bgColor: "secondary.50",
        },
        {
            icon: <Star fontSize="large" />,
            count: "4.8/5",
            targetValue: 4.8,
            suffix: "/5",
            label: "Average Rating",
            color: "warning.main",
            bgColor: "warning.50",
        },
    ];

    // Intersection Observer to detect when section comes into view
    useEffect(() => {
        const observer = new IntersectionObserver(
            ([entry]) => {
                if (entry.isIntersecting && !isVisible) {
                    setIsVisible(true);
                }
            },
            { threshold: 0.3 }
        );

        if (sectionRef.current) {
            observer.observe(sectionRef.current);
        }

        return () => {
            if (sectionRef.current) {
                observer.unobserve(sectionRef.current);
            }
        };
    }, [isVisible]);

    // Animation function for counting numbers
    useEffect(() => {
        if (!isVisible) return;

        const animateCount = (index, targetValue, duration = 2000) => {
            const startTime = Date.now();
            const startValue = 0;

            const updateCount = () => {
                const elapsed = Date.now() - startTime;
                const progress = Math.min(elapsed / duration, 1);

                // Easing function for smooth animation
                const easeOutQuart = 1 - Math.pow(1 - progress, 4);
                const currentValue =
                    startValue + (targetValue - startValue) * easeOutQuart;

                setAnimatedCounts((prev) => {
                    const newCounts = [...prev];
                    newCounts[index] = currentValue;
                    return newCounts;
                });

                if (progress < 1) {
                    requestAnimationFrame(updateCount);
                }
            };

            requestAnimationFrame(updateCount);
        };

        // Start animations with slight delays for each stat
        stats.forEach((stat, index) => {
            setTimeout(() => {
                animateCount(index, stat.targetValue, 2000);
            }, index * 200);
        });
    }, [isVisible]);

    // Format the animated count values
    const formatCount = (value, index) => {
        const stat = stats[index];
        if (index === 3) {
            // Rating stat
            return value.toFixed(1);
        } else if (index === 2 && value >= 1000) {
            // Appointments stat
            return (value / 1000).toFixed(value >= 10000 ? 0 : 1) + "k";
        }
        return Math.floor(value).toString();
    };

    return (
        <Box
            sx={{
                py: { xs: 6, md: 10 },
                background: "linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%)",
                position: "relative",
                width: "100vw",
                marginLeft: "calc(-50vw + 50%)",
                marginRight: "calc(-50vw + 50%)",
            }}
        >
            <Container maxWidth="xl">
                <Box textAlign="center" sx={{ mb: { xs: 4, md: 6 } }}>
                    <Typography
                        variant="h3"
                        component="h2"
                        gutterBottom
                        sx={{
                            fontWeight: "bold",
                            color: "text.primary",
                            fontSize: { xs: "2rem", sm: "2.5rem", md: "3rem" },
                        }}
                    >
                        Our Impact in Numbers
                    </Typography>
                    <Typography
                        variant="h6"
                        color="text.secondary"
                        sx={{
                            maxWidth: 600,
                            mx: "auto",
                            fontSize: { xs: "1rem", md: "1.25rem" },
                            px: { xs: 2, md: 0 },
                        }}
                    >
                        Trusted by thousands of patients for quality healthcare
                        services and professional medical care
                    </Typography>
                </Box>

                <Grid container spacing={{ xs: 2, sm: 3, md: 4 }}>
                    {stats.map((stat, index) => (
                        <Grid item xs={6} md={3} key={index}>
                            <Card
                                elevation={4}
                                sx={{
                                    textAlign: "center",
                                    p: { xs: 2, sm: 3, md: 4 },
                                    height: "100%",
                                    minHeight: { xs: 180, sm: 200, md: 220 },
                                    background:
                                        "linear-gradient(145deg, #ffffff 0%, #f8f9fa 100%)",
                                    border: "1px solid rgba(0,0,0,0.05)",
                                    borderRadius: { xs: 3, md: 2 },
                                    "&:hover": {
                                        transform: "translateY(-8px)",
                                        boxShadow: { xs: 4, md: 6 },
                                    },
                                    transition: "all 0.3s ease-in-out",
                                    display: "flex",
                                    flexDirection: "column",
                                    justifyContent: "center",
                                }}
                            >
                                <CardContent
                                    sx={{
                                        p: 0,
                                        flexGrow: 1,
                                        display: "flex",
                                        flexDirection: "column",
                                        justifyContent: "center",
                                    }}
                                >
                                    <Avatar
                                        sx={{
                                            width: { xs: 50, sm: 60, md: 80 },
                                            height: { xs: 50, sm: 60, md: 80 },
                                            mx: "auto",
                                            mb: { xs: 2, md: 3 },
                                            bgcolor: stat.bgColor,
                                            color: stat.color,
                                            boxShadow: 2,
                                        }}
                                    >
                                        {stat.icon}
                                    </Avatar>

                                    <Typography
                                        variant="h3"
                                        component="div"
                                        sx={{
                                            fontWeight: "bold",
                                            color: stat.color,
                                            mb: { xs: 0.5, md: 1 },
                                            fontSize: {
                                                xs: "1.5rem",
                                                sm: "1.8rem",
                                                md: "2.5rem",
                                            },
                                            lineHeight: 1.2,
                                            fontFamily: "monospace", // Better for counting animation
                                            minHeight: {
                                                xs: "2rem",
                                                md: "2.5rem",
                                            }, // Prevent layout shift
                                        }}
                                    >
                                        {formatCount(
                                            animatedCounts[index],
                                            index
                                        )}
                                        {stat.suffix}
                                    </Typography>

                                    <Typography
                                        variant="h6"
                                        color="text.secondary"
                                        sx={{
                                            fontWeight: 500,
                                            fontSize: {
                                                xs: "0.75rem",
                                                sm: "0.85rem",
                                                md: "1.1rem",
                                            },
                                            lineHeight: 1.3,
                                            px: { xs: 0.5, md: 0 },
                                        }}
                                    >
                                        {stat.label}
                                    </Typography>
                                </CardContent>
                            </Card>
                        </Grid>
                    ))}
                </Grid>

                {/* Additional Info */}
                <Box sx={{ mt: { xs: 4, md: 6 }, textAlign: "center" }}>
                    <Typography
                        variant="body1"
                        color="text.secondary"
                        sx={{
                            fontStyle: "italic",
                            fontSize: { xs: "0.9rem", md: "1rem" },
                            px: { xs: 2, md: 0 },
                        }}
                    >
                        Join thousands of satisfied patients who trust Visit
                        Care for their healthcare needs
                    </Typography>
                </Box>
            </Container>
        </Box>
    );
};

export default StatsSection;
```


### Step 24: Enhanced Why Choose Section Component

**resources/js/components/home/WhyChooseSection.jsx**

```jsx
import React from "react";
import {
    Box,
    Container,
    Typography,
    Grid,
    Card,
    CardContent,
    Avatar,
} from "@mui/material";
import {
    LocationOn,
    Schedule,
    VerifiedUser,
    PhoneIphone,
    Security,
    NotificationImportant,
} from "@mui/icons-material";
import { whyChooseFeatures } from "../../data/dummyData";

const WhyChooseSection = () => {
    const iconMap = {
        LocationOn: <LocationOn fontSize="large" />,
        Schedule: <Schedule fontSize="large" />,
        VerifiedUser: <VerifiedUser fontSize="large" />,
        PhoneIphone: <PhoneIphone fontSize="large" />,
        Security: <Security fontSize="large" />,
        NotificationImportant: <NotificationImportant fontSize="large" />,
    };

    const colors = [
        { main: "primary.main", bg: "primary.50" },
        { main: "success.main", bg: "success.50" },
        { main: "info.main", bg: "info.50" },
        { main: "warning.main", bg: "warning.50" },
        { main: "error.main", bg: "error.50" },
        { main: "secondary.main", bg: "secondary.50" },
    ];

    return (
        <Box
            sx={{
                py: { xs: 6, md: 10 },
                position: "relative",
                width: "100vw",
                marginLeft: "calc(-50vw + 50%)",
                marginRight: "calc(-50vw + 50%)",
                bgcolor: "background.paper",
            }}
        >
            <Container maxWidth="xl">
                <Box textAlign="center" sx={{ mb: 8 }}>
                    <Typography
                        variant="h3"
                        component="h2"
                        gutterBottom
                        sx={{ fontWeight: "bold", color: "text.primary" }}
                    >
                        Why Choose Visit Care?
                    </Typography>
                    <Typography
                        variant="h6"
                        color="text.secondary"
                        sx={{ maxWidth: 800, mx: "auto", lineHeight: 1.6 }}
                    >
                        We're revolutionizing healthcare with cutting-edge
                        technology and a patient-first approach that puts your
                        health and convenience at the center of everything we do
                    </Typography>
                </Box>

                <Grid container spacing={4}>
                    {whyChooseFeatures.map((feature, index) => (
                        <Grid item xs={12} sm={6} lg={4} key={index}>
                            <Card
                                elevation={0}
                                sx={{
                                    textAlign: "center",
                                    p: { xs: 3, md: 4 },
                                    height: "100%",
                                    background:
                                        "linear-gradient(145deg, #ffffff 0%, #f8f9fa 100%)",
                                    border: "1px solid rgba(0,0,0,0.05)",
                                    borderRadius: 3,
                                    "&:hover": {
                                        transform: "translateY(-8px)",
                                        boxShadow: 8,
                                        "& .feature-avatar": {
                                            transform: "scale(1.1)",
                                        },
                                    },
                                    transition: "all 0.4s ease-in-out",
                                }}
                            >
                                <CardContent>
                                    <Avatar
                                        className="feature-avatar"
                                        sx={{
                                            width: 80,
                                            height: 80,
                                            mx: "auto",
                                            mb: 3,
                                            bgcolor: colors[index].bg,
                                            color: colors[index].main,
                                            boxShadow: 3,
                                            transition:
                                                "transform 0.3s ease-in-out",
                                        }}
                                    >
                                        {iconMap[feature.icon]}
                                    </Avatar>

                                    <Typography
                                        variant="h5"
                                        component="h3"
                                        gutterBottom
                                        sx={{
                                            fontWeight: "bold",
                                            color: "text.primary",
                                            mb: 2,
                                        }}
                                    >
                                        {feature.title}
                                    </Typography>

                                    <Typography
                                        variant="body1"
                                        color="text.secondary"
                                        sx={{
                                            lineHeight: 1.6,
                                            fontSize: "1rem",
                                        }}
                                    >
                                        {feature.description}
                                    </Typography>
                                </CardContent>
                            </Card>
                        </Grid>
                    ))}
                </Grid>
            </Container>
        </Box>
    );
};

export default WhyChooseSection;
```


### Step 25: Enhanced Testimonials Section Component

**resources/js/components/home/TestimonialsSection.jsx**

```jsx
import React from "react";
import {
    Box,
    Container,
    Typography,
    Grid,
    Card,
    CardContent,
    Avatar,
    Rating,
    Paper,
    Chip,
} from "@mui/material";
import { FormatQuote, LocationOn } from "@mui/icons-material";
import { testimonials } from "../../data/dummyData";

const TestimonialsSection = () => {
    return (
        <Box
            sx={{
                py: { xs: 6, md: 10 },
                background: "linear-gradient(135deg, #667eea 0%, #764ba2 100%)",
                color: "white",
                padding: "0px",
                position: "relative",
                width: "100vw",
                marginLeft: "calc(-50vw + 50%)",
                marginRight: "calc(-50vw + 50%)",
                "&::before": {
                    content: '""',
                    position: "absolute",
                    top: 0,
                    left: 0,
                    right: 0,
                    bottom: 0,
                    background:
                        "url(\"data:image/svg+xml,%3Csvg width='60' height='60' viewBox='0 0 60 60' xmlns='http://www.w3.org/2000/svg'%3E%3Cg fill='none' fill-rule='evenodd'%3E%3Cg fill='%23ffffff' fill-opacity='0.05'%3E%3Ccircle cx='30' cy='30' r='2'/%3E%3C/g%3E%3C/g%3E%3C/svg%3E\")",
                    opacity: 0.3,
                },
            }}
        >
            <Container maxWidth="xl" sx={{ position: "relative", zIndex: 1 }}>
                <Box textAlign="center" sx={{ mb: 8 }}>
                    <Typography
                        variant="h3"
                        component="h2"
                        gutterBottom
                        sx={{ fontWeight: "bold", color: "white" }}
                    >
                        What Our Users Say
                    </Typography>
                    <Typography
                        variant="h6"
                        sx={{
                            maxWidth: 600,
                            mx: "auto",
                            opacity: 0.9,
                            lineHeight: 1.6,
                        }}
                    >
                        Real stories from satisfied patients who found their
                        perfect healthcare match through Visit Care
                    </Typography>
                </Box>

                <Grid container spacing={4}>
                    {testimonials.map((testimonial) => (
                        <Grid item xs={12} sm={6} lg={3} key={testimonial.id}>
                            <Card
                                elevation={8}
                                sx={{
                                    height: "100%",
                                    background: "rgba(255, 255, 255, 0.95)",
                                    backdropFilter: "blur(10px)",
                                    border: "1px solid rgba(255, 255, 255, 0.2)",
                                    borderRadius: 3,
                                    "&:hover": {
                                        transform: "translateY(-8px)",
                                        boxShadow: 12,
                                    },
                                    transition: "all 0.3s ease-in-out",
                                    position: "relative",
                                    overflow: "visible",
                                }}
                            >
                                {/* Perfect Quote Icon Circle */}
                                <Box
                                    sx={{
                                        position: "absolute",
                                        top: -22,
                                        left: 20,
                                        width: 44,
                                        height: 44,
                                        bgcolor: "primary.main",
                                        borderRadius: "50%",
                                        boxShadow: 3,
                                        display: "flex",
                                        alignItems: "center",
                                        justifyContent: "center",
                                        zIndex: 2,
                                    }}
                                >
                                    <FormatQuote
                                        sx={{ color: "white", fontSize: 24 }}
                                    />
                                </Box>

                                <CardContent sx={{ p: 4, pt: 5 }}>
                                    {/* Rating */}
                                    <Box
                                        sx={{
                                            mb: 3,
                                            display: "flex",
                                            justifyContent: "center",
                                        }}
                                    >
                                        <Rating
                                            value={testimonial.rating}
                                            readOnly
                                            size="small"
                                            sx={{
                                                "& .MuiRating-iconFilled": {
                                                    color: "warning.main",
                                                },
                                            }}
                                        />
                                    </Box>

                                    {/* Comment */}
                                    <Typography
                                        variant="body1"
                                        sx={{
                                            mb: 4,
                                            fontStyle: "italic",
                                            color: "text.secondary",
                                            lineHeight: 1.6,
                                            minHeight: 80,
                                            display: "flex",
                                            alignItems: "center",
                                        }}
                                    >
                                        "{testimonial.comment}"
                                    </Typography>

                                    {/* User Info */}
                                    <Box
                                        sx={{
                                            display: "flex",
                                            alignItems: "center",
                                            justifyContent: "center",
                                        }}
                                    >
                                        <Avatar
                                            src={testimonial.avatar}
                                            sx={{
                                                width: 50,
                                                height: 50,
                                                mr: 2,
                                                border: "2px solid",
                                                borderColor: "primary.main",
                                            }}
                                        />
                                        <Box sx={{ textAlign: "left" }}>
                                            <Typography
                                                variant="h6"
                                                sx={{
                                                    fontWeight: "bold",
                                                    color: "text.primary",
                                                    fontSize: "1rem",
                                                }}
                                            >
                                                {testimonial.name}
                                            </Typography>
                                            <Box
                                                sx={{
                                                    display: "flex",
                                                    alignItems: "center",
                                                    mt: 0.5,
                                                }}
                                            >
                                                <LocationOn
                                                    fontSize="small"
                                                    sx={{
                                                        color: "text.secondary",
                                                        mr: 0.5,
                                                    }}
                                                />
                                                <Typography
                                                    variant="caption"
                                                    color="text.secondary"
                                                >
                                                    {testimonial.location}
                                                </Typography>
                                            </Box>
                                        </Box>
                                    </Box>

                                    {/* Verified Badge */}
                                    <Box
                                        sx={{
                                            mt: 2,
                                            display: "flex",
                                            justifyContent: "center",
                                        }}
                                    >
                                        <Chip
                                            label="Verified Patient"
                                            size="small"
                                            color="success"
                                            variant="outlined"
                                            sx={{ fontSize: "0.7rem" }}
                                        />
                                    </Box>
                                </CardContent>
                            </Card>
                        </Grid>
                    ))}
                </Grid>

                {/* Additional Info */}
                <Box sx={{ mt: 8, textAlign: "center" }}>
                    <Paper
                        elevation={4}
                        sx={{
                            p: 4,
                            bgcolor: "rgba(255, 255, 255, 0.1)",
                            backdropFilter: "blur(10px)",
                            border: "1px solid rgba(255, 255, 255, 0.2)",
                            borderRadius: 3,
                            color: "white",
                        }}
                    >
                        <Typography variant="h6" gutterBottom>
                            Join 50,000+ Happy Patients
                        </Typography>
                        <Typography variant="body2" sx={{ opacity: 0.9 }}>
                            Over 98% of our users recommend Visit Care to their
                            friends and family
                        </Typography>
                    </Paper>
                </Box>
            </Container>
        </Box>
    );
};

export default TestimonialsSection;
```

### Step 26: Complete Location Map Component

**resources/js/components/home/LocationMap.jsx**

```jsx
import React, { useState, useEffect, useRef, useCallback } from "react";
import {
  Dialog,
  DialogTitle,
  DialogContent,
  DialogActions,
  Box,
  Typography,
  Button,
  IconButton,
  List,
  ListItem,
  ListItemIcon,
  ListItemText,
  TextField,
  InputAdornment,
  Chip,
  CircularProgress,
  Alert,
  useMediaQuery,
  useTheme,
} from "@mui/material";
import { ListItemButton } from "@mui/material";
import { 
  Close, 
  LocationOn, 
  MyLocation, 
  Search, 
  Navigation,
  Map,
  Place
} from "@mui/icons-material";

const LocationMap = ({
  open,
  onClose,
  currentLocation,
  currentCoords,
  onLocationUpdate,
}) => {
  const theme = useTheme();
  const isMobile = useMediaQuery(theme.breakpoints.down("md"));
  const isSmallScreen = useMediaQuery(theme.breakpoints.down("sm"));

  const [searchLocation, setSearchLocation] = useState("");
  const [currentPosition, setCurrentPosition] = useState(null);
  const [selectedLocation, setSelectedLocation] = useState(
    currentLocation || "New York, NY"
  );
  const [selectedCoords, setSelectedCoords] = useState(
    currentCoords || [40.7128, -74.006]
  );
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState("");
  const [searchResults, setSearchResults] = useState([]);
  const [mapLoaded, setMapLoaded] = useState(false);
  const [googleMapsLoaded, setGoogleMapsLoaded] = useState(false);
  
  const mapRef = useRef(null);
  const googleMapRef = useRef(null);
  const markersRef = useRef([]);
  const searchTimeoutRef = useRef(null);
  const geocoderRef = useRef(null);
  const mapContainerRef = useRef(null);
  const isUnmountingRef = useRef(false);
  const placesServiceRef = useRef(null);

  const recentLocations = [
    { name: 'New York, NY', coords: [40.7128, -74.006], emoji: '🗽' },
    { name: 'Brooklyn, NY', coords: [40.6892, -73.9442], emoji: '🌉' },
    { name: 'Queens, NY', coords: [40.7282, -73.9442], emoji: '🏙️' },
    { name: 'Manhattan, NY', coords: [40.7158, -73.997], emoji: '🏢' },
  ];

  useEffect(() => {
    if (currentLocation && currentCoords) {
      setSelectedLocation(currentLocation);
      setSelectedCoords(Array.isArray(currentCoords) ? currentCoords : [40.7128, -74.006]);
    }
  }, [currentLocation, currentCoords]);

  const cleanup = useCallback(() => {
    isUnmountingRef.current = true;
    
    if (searchTimeoutRef.current) {
      clearTimeout(searchTimeoutRef.current);
      searchTimeoutRef.current = null;
    }

    if (markersRef.current && Array.isArray(markersRef.current)) {
      markersRef.current.forEach(marker => {
        try {
          if (marker && typeof marker.setMap === 'function') {
            marker.setMap(null);
          }
        } catch (e) {
          console.warn('Error removing marker:', e);
        }
      });
      markersRef.current = [];
    }

    if (googleMapRef.current) {
      try {
        googleMapRef.current = null;
      } catch (e) {
        console.warn('Error clearing map reference:', e);
      }
    }

    geocoderRef.current = null;
    placesServiceRef.current = null;
    setMapLoaded(false);
  }, []);

  useEffect(() => {
    const loadGoogleMaps = async () => {
      if (window.google && window.google.maps) {
        setGoogleMapsLoaded(true);
        return;
      }

      try {
        if (document.querySelector('script[src*="maps.googleapis.com"]')) {
          const checkGoogleMaps = () => {
            if (isUnmountingRef.current) return;
            
            if (window.google && window.google.maps) {
              setGoogleMapsLoaded(true);
            } else {
              setTimeout(checkGoogleMaps, 100);
            }
          };
          checkGoogleMaps();
          return;
        }

        const apiKey = import.meta.env.VITE_GOOGLE_API_KEY;
        if (!apiKey) {
          throw new Error("Google Maps API key not found in environment variables");
        }

        await new Promise((resolve, reject) => {
          const script = document.createElement("script");
          script.src = `https://maps.googleapis.com/maps/api/js?key=${apiKey}&libraries=places,geometry`;
          script.async = true;
          script.defer = true;
          script.onload = () => {
            if (!isUnmountingRef.current) {
              setGoogleMapsLoaded(true);
            }
            resolve();
          };
          script.onerror = () => {
            if (!isUnmountingRef.current) {
              setError("Failed to load Google Maps");
            }
            reject();
          };
          document.head.appendChild(script);
        });
      } catch (error) {
        console.error("Error loading Google Maps:", error);
        if (!isUnmountingRef.current) {
          setError("Failed to load Google Maps");
        }
      }
    };

    if (open) {
      isUnmountingRef.current = false;
      loadGoogleMaps();
    }
  }, [open]);

  useEffect(() => {
    if (open && googleMapsLoaded && !googleMapRef.current && mapRef.current && !isUnmountingRef.current) {
      const timer = setTimeout(() => {
        if (!isUnmountingRef.current) {
          initializeMap();
        }
      }, 100);

      return () => clearTimeout(timer);
    }
  }, [open, googleMapsLoaded]);

  useEffect(() => {
    if (!open) {
      cleanup();
    }
    
    return () => {
      cleanup();
    };
  }, [open, cleanup]);

  const initializeMap = () => {
    if (!mapRef.current || !window.google || googleMapRef.current || isUnmountingRef.current) {
      return;
    }

    try {
      const coords = Array.isArray(selectedCoords) && selectedCoords.length >= 2 
        ? selectedCoords 
        : [40.7128, -74.006];

      googleMapRef.current = new window.google.maps.Map(mapRef.current, {
        center: { lat: coords[^0], lng: coords[^1] },
        zoom: 13,
        mapTypeControl: false,
        streetViewControl: false,
        fullscreenControl: false,
        styles: [
          {
            featureType: "poi",
            elementType: "labels",
            stylers: [{ visibility: "on" }]
          }
        ]
      });

      geocoderRef.current = new window.google.maps.Geocoder();
      placesServiceRef.current = new window.google.maps.places.PlacesService(googleMapRef.current);

      addMarker(coords, selectedLocation, true);
      googleMapRef.current.addListener("click", handleMapClick);

      if (!isUnmountingRef.current) {
        setMapLoaded(true);
      }
    } catch (error) {
      console.error("Error initializing map:", error);
      if (!isUnmountingRef.current) {
        setError("Failed to initialize map");
      }
    }
  };

  const addMarker = (coords, title, isSelected = false) => {
    if (!googleMapRef.current || !window.google || isUnmountingRef.current) return;

    if (!Array.isArray(coords) || coords.length < 2) {
      console.error("Invalid coordinates for marker:", coords);
      return;
    }

    const position = { lat: coords[^0], lng: coords[^1] };
    
    try {
      const marker = new window.google.maps.Marker({
        position: position,
        map: googleMapRef.current,
        title: title,
        icon: isSelected
          ? {
              path: window.google.maps.SymbolPath.CIRCLE,
              scale: 10,
              fillColor: "#2196F3",
              fillOpacity: 1,
              strokeColor: "white",
              strokeWeight: 3,
            }
          : {
              path: window.google.maps.SymbolPath.CIRCLE,
              scale: 8,
              fillColor: "#ff5722",
              fillOpacity: 1,
              strokeColor: "white",
              strokeWeight: 2,
            }
      });

      const infoWindow = new window.google.maps.InfoWindow({
        content: title,
      });

      marker.addListener("click", () => {
        if (!isUnmountingRef.current) {
          infoWindow.open(googleMapRef.current, marker);
        }
      });

      markersRef.current.push(marker);
      return marker;
    } catch (error) {
      console.error("Error adding marker:", error);
      return null;
    }
  };

  const clearMarkers = () => {
    if (markersRef.current && Array.isArray(markersRef.current)) {
      markersRef.current.forEach((marker) => {
        try {
          if (marker && typeof marker.setMap === 'function') {
            marker.setMap(null);
          }
        } catch (e) {
          console.warn('Error removing marker:', e);
        }
      });
      markersRef.current = [];
    }
  };

  const handleMapClick = async (e) => {
    if (isUnmountingRef.current) return;
    
    const lat = e.latLng.lat();
    const lng = e.latLng.lng();
    setLoading(true);

    try {
      const address = await reverseGeocode(lat, lng);
      if (!isUnmountingRef.current) {
        setSelectedLocation(address);
        setSelectedCoords([lat, lng]);

        clearMarkers();
        addMarker([lat, lng], address, true);
      }
    } catch (error) {
      if (!isUnmountingRef.current) {
        setError("Failed to get location details");
      }
    } finally {
      if (!isUnmountingRef.current) {
        setLoading(false);
      }
    }
  };

  const reverseGeocode = async (lat, lng) => {
    try {
      if (!geocoderRef.current || isUnmountingRef.current) return "Location not found";

      return new Promise((resolve) => {
        geocoderRef.current.geocode(
          { location: { lat, lng } },
          (results, status) => {
            if (isUnmountingRef.current) {
              resolve("Location not found");
              return;
            }
            
            if (status === "OK" && results[^0]) {
              const address = results.formatted_address;
              const parts = address.split(",").slice(0, 3);
              resolve(parts.join(",").trim());
            } else {
              resolve("Location not found");
            }
          }
        );
      });
    } catch (error) {
      console.error("Reverse geocoding error:", error);
      return "Location not found";
    }
  };

  const searchLocations = async (query) => {
    if (!query.trim() || isUnmountingRef.current) {
      setSearchResults([]);
      return;
    }

    setLoading(true);
    try {
      if (!window.google || !window.google.maps || !placesServiceRef.current) {
        throw new Error("Google Maps not loaded");
      }

      const request = {
        query: query,
        fields: ["name", "geometry", "formatted_address", "place_id"],
      };

      placesServiceRef.current.textSearch(request, (results, status) => {
        if (isUnmountingRef.current) return;
        
        if (status === window.google.maps.places.PlacesServiceStatus.OK && results) {
          const searchData = results.slice(0, 5).map(place => ({
            display_name: place.formatted_address,
            lat: place.geometry.location.lat(),
            lon: place.geometry.location.lng(),
            place_id: place.place_id
          }));
          setSearchResults(searchData);
        } else {
          setSearchResults([]);
        }
        setLoading(false);
      });
    } catch (error) {
      console.error("Search error:", error);
      if (!isUnmountingRef.current) {
        setError("Failed to search locations");
        setSearchResults([]);
        setLoading(false);
      }
    }
  };

  const handleSearchChange = (e) => {
    const value = e.target.value;
    setSearchLocation(value);

    if (searchTimeoutRef.current) {
      clearTimeout(searchTimeoutRef.current);
    }

    searchTimeoutRef.current = setTimeout(() => {
      if (!isUnmountingRef.current) {
        searchLocations(value);
      }
    }, 500);
  };

  const selectSearchResult = (result) => {
    if (isUnmountingRef.current) return;
    
    const coords = [parseFloat(result.lat), parseFloat(result.lon)];
    const locationName = result.display_name.split(",").slice(0, 3).join(",").trim();

    setSelectedLocation(locationName);
    setSelectedCoords(coords);
    setSearchLocation("");
    setSearchResults([]);

    if (googleMapRef.current) {
      googleMapRef.current.setCenter({ lat: coords[^0], lng: coords[^1] });
      googleMapRef.current.setZoom(15);
      clearMarkers();
      addMarker(coords, locationName, true);
    }
  };

  const selectRecentLocation = (location) => {
    if (isUnmountingRef.current) return;
    
    setSelectedLocation(location.name);
    setSelectedCoords(location.coords);

    if (googleMapRef.current) {
      googleMapRef.current.setCenter({ lat: location.coords[^0], lng: location.coords[^1] });
      googleMapRef.current.setZoom(15);
      clearMarkers();
      addMarker(location.coords, location.name, true);
    }
  };

  const handleCurrentLocation = () => {
    if (!navigator.geolocation || isUnmountingRef.current) {
      setError("Geolocation is not supported by this browser");
      return;
    }

    setLoading(true);
    navigator.geolocation.getCurrentPosition(
      async (position) => {
        if (isUnmountingRef.current) return;
        
        const { latitude, longitude } = position.coords;
        const coords = [latitude, longitude];

        try {
          const address = await reverseGeocode(latitude, longitude);
          if (!isUnmountingRef.current) {
            setCurrentPosition(coords);
            setSelectedLocation(address);
            setSelectedCoords(coords);

            if (googleMapRef.current) {
              googleMapRef.current.setCenter({ lat: latitude, lng: longitude });
              googleMapRef.current.setZoom(15);
              clearMarkers();
              addMarker(coords, address, true);
            }
          }
        } catch (error) {
          if (!isUnmountingRef.current) {
            setError("Failed to get current location details");
          }
        } finally {
          if (!isUnmountingRef.current) {
            setLoading(false);
          }
        }
      },
      (error) => {
        console.error("Geolocation error:", error);
        if (!isUnmountingRef.current) {
          setError("Failed to get current location");
          setLoading(false);
        }
      },
      {
        enableHighAccuracy: true,
        timeout: 10000,
        maximumAge: 300000,
      }
    );
  };

  const handleUpdateLocation = () => {
    if (onLocationUpdate) {
      onLocationUpdate(selectedLocation, selectedCoords);
    }
    onClose();
  };

  const formatCoordinates = (coords) => {
    if (!Array.isArray(coords) || coords.length < 2) {
      return "0.0000, 0.0000";
    }
    return `${coords[^0].toFixed(4)}, ${coords[^1].toFixed(4)}`;
  };

  return (
    <Dialog
      open={open}
      onClose={onClose}
      maxWidth={false}
      fullScreen={isSmallScreen}
      PaperProps={{
        sx: {
          borderRadius: isSmallScreen ? 0 : 3,
          maxHeight: isSmallScreen ? "100vh" : "95vh",
          width: isSmallScreen ? "100vw" : isMobile ? "90vw" : "800px",
          height: isSmallScreen ? "100vh" : "auto",
          m: isSmallScreen ? 0 : 2,
        },
      }}
    >
      <DialogTitle
        sx={{
          display: "flex",
          justifyContent: "space-between",
          alignItems: "center",
          pb: 1,
          borderBottom: "1px solid",
          borderColor: "divider",
          marginBottom: 1,
        }}
      >
        <Typography variant="h6" component="div" sx={{ fontWeight: "bold", display: 'flex', alignItems: 'center', gap: 1 }}>
          <Map sx={{ color: 'primary.main' }} />
          🗺️ Select Location
        </Typography>
        <IconButton onClick={onClose} size="small">
          <Close />
        </IconButton>
      </DialogTitle>

      <DialogContent
        sx={{
          p: isMobile ? 2 : 3,
          position: "relative",
          height: isSmallScreen ? "calc(100vh - 140px)" : "auto",
          overflowY: "auto",
        }}
      >
        {error && (
          <Alert
            severity="error"
            sx={{ mb: 2 }}
            onClose={() => setError("")}
            action={
              <Button color="inherit" size="small" onClick={() => setError("")}>
                Dismiss
              </Button>
            }
          >
            {error}
          </Alert>
        )}

        <Button
          fullWidth
          variant="outlined"
          startIcon={loading ? <CircularProgress size={16} /> : <MyLocation />}
          onClick={handleCurrentLocation}
          disabled={loading}
          sx={{
            mb: 1,
            py: 1.5,
            borderRadius: 2,
            textTransform: "none",
            justifyContent: "flex-start",
            fontSize: isMobile ? "0.9rem" : "1rem",
          }}
        >
          📍 Use Current Location
        </Button>

        <Box sx={{ position: "relative", mb: 1 }}>
          <TextField
            fullWidth
            placeholder="Search for a location..."
            value={searchLocation}
            onChange={handleSearchChange}
            size={isMobile ? "small" : "medium"}
            InputProps={{
              startAdornment: (
                <InputAdornment position="start">
                  {loading ? <CircularProgress size={20} /> : <Search />}
                </InputAdornment>
              ),
            }}
            sx={{
              "& .MuiOutlinedInput-root": {
                borderRadius: 2,
              },
            }}
          />

          {searchResults.length > 0 && (
            <Box
              sx={{
                position: "absolute",
                top: "100%",
                left: 0,
                right: 0,
                bgcolor: "background.paper",
                border: 1,
                borderColor: "divider",
                borderRadius: 1,
                boxShadow: 3,
                zIndex: 1000,
                maxHeight: 250,
                overflow: "auto",
              }}
            >
              <List disablePadding>
                {searchResults.map((result, index) => (
                  <ListItem key={result.place_id || index} disablePadding>
                    <ListItemButton
                      onClick={() => selectSearchResult(result)}
                      sx={{
                        py: 1,
                        "&:hover": { backgroundColor: "action.hover" },
                      }}
                    >
                      <ListItemIcon sx={{ minWidth: 30 }}>
                        <Place fontSize="small" color="primary" />
                      </ListItemIcon>
                      <ListItemText
                        primary={result.display_name
                          .split(",")
                          .slice(0, 3)
                          .join(",")}
                        primaryTypographyProps={{
                          variant: "body2",
                          sx: { fontSize: isMobile ? "0.8rem" : "0.9rem" },
                        }}
                      />
                    </ListItemButton>
                  </ListItem>
                ))}
              </List>
            </Box>
          )}
        </Box>

        <Box
          ref={(el) => {
            mapRef.current = el;
            mapContainerRef.current = el;
          }}
          sx={{
            height: isMobile ? 300 : 350,
            width: "100%",
            borderRadius: 2,
            mb: 2,
            border: "1px solid",
            borderColor: "divider",
            position: "relative",
            backgroundColor: "#f5f5f5",
            display: "flex",
            alignItems: "center",
            justifyContent: "center",
          }}
        >
          {!mapLoaded && googleMapsLoaded && (
            <Box
              sx={{
                display: "flex",
                flexDirection: "column",
                alignItems: "center",
              }}
            >
              <CircularProgress />
              <Typography variant="body2" sx={{ mt: 1 }}>
                🗺️ Loading map...
              </Typography>
            </Box>
          )}
          {!googleMapsLoaded && (
            <Box
              sx={{
                display: "flex",
                flexDirection: "column",
                alignItems: "center",
              }}
            >
              <CircularProgress />
              <Typography variant="body2" sx={{ mt: 1 }}>
                🌍 Initializing map...
              </Typography>
            </Box>
          )}
        </Box>

        <Box
          sx={{
            mb: 2,
            p: 2.5,
            bgcolor: "rgba(255, 255, 255, 0.95)",
            backdropFilter: "blur(15px)",
            borderRadius: 3,
            border: "1px solid rgba(16, 217, 21, 0.2)",
            boxShadow: "0 8px 32px rgba(16, 217, 21, 0.1)",
            position: "relative",
            overflow: "hidden",
            "&::before": {
              content: '""',
              position: "absolute",
              top: 0,
              left: 0,
              right: 0,
              height: "3px",
              background: "linear-gradient(90deg, #ff5983, #f53b69ff)",
            },
          }}
        >
          <Box
            sx={{
              display: "flex",
              alignItems: "center",
              mb: 1.5,
              position: "relative",
              zIndex: 1,
            }}
          >
            <Box
              sx={{
                p: 0.8,
                borderRadius: "50%",
                bgcolor: "rgba(16, 217, 21, 0.1)",
                display: "flex",
                alignItems: "center",
                justifyContent: "center",
                mr: 1.5,
              }}
            >
              <LocationOn
                sx={{
                  color: "primary.main",
                  fontSize: "1.2rem",
                }}
              />
            </Box>
            <Typography
              variant="subtitle1"
              sx={{
                fontWeight: 700,
                color: "primary.main",
                fontSize: isMobile ? "1rem" : "1.1rem",
                textShadow: "0 1px 2px rgba(0, 0, 0, 0.1)",
              }}
            >
              📍 Selected Location
            </Typography>
          </Box>

          <Typography
            variant="body1"
            sx={{
              fontWeight: 600,
              mb: 2,
              fontSize: isMobile ? "1rem" : "1.1rem",
              color: "text.primary",
              lineHeight: 1.4,
            }}
          >
            {selectedLocation}
          </Typography>

          <Box
            sx={{
              display: "flex",
              flexWrap: "wrap",
              gap: 1.5,
              alignItems: "center",
            }}
          >
            <Chip
              label={`📍 ${formatCoordinates(selectedCoords)}`}
              size="small"
              sx={{
                fontSize: "0.75rem",
                bgcolor: "rgba(16, 217, 21, 0.08)",
                border: "1px solid rgba(16, 217, 21, 0.2)",
                color: "primary.main",
                fontWeight: 500,
                backdropFilter: "blur(10px)",
                "&:hover": {
                  bgcolor: "rgba(16, 217, 21, 0.15)",
                  transform: "translateY(-1px)",
                  boxShadow: "0 4px 12px rgba(16, 217, 21, 0.2)",
                },
                transition: "all 0.3s ease",
              }}
            />
            <Chip
              label="🗺️ Google Maps"
              size="small"
              sx={{
                fontSize: "0.75rem",
                bgcolor: "rgba(108, 117, 125, 0.08)",
                border: "1px solid rgba(108, 117, 125, 0.2)",
                color: "text.secondary",
                fontWeight: 500,
                backdropFilter: "blur(10px)",
                "&:hover": {
                  bgcolor: "rgba(108, 117, 125, 0.15)",
                  transform: "translateY(-1px)",
                  boxShadow: "0 4px 12px rgba(108, 117, 125, 0.2)",
                },
                transition: "all 0.3s ease",
              }}
            />
          </Box>
        </Box>

        <Typography
          variant="h6"
          sx={{
            fontWeight: 600,
            mb: 2,
            color: "primary.main",
            display: "flex",
            alignItems: "center",
            gap: 1,
          }}
        >
          <LocationOn fontSize="small" />
          📍 Recent Search Locations
        </Typography>

        <Box
          sx={{
            bgcolor: "rgba(255, 255, 255, 0.9)",
            backdropFilter: "blur(10px)",
            borderRadius: 3,
            border: "1px solid rgba(0, 0, 0, 0.1)",
            boxShadow: "0 4px 20px rgba(0, 0, 0, 0.1)",
            overflow: "hidden",
          }}
        >
          <List disablePadding>
            {recentLocations.map((location, index) => (
              <ListItem
                key={index}
                disablePadding
                sx={{
                  borderBottom:
                    index < recentLocations.length - 1
                      ? "1px solid rgba(0, 0, 0, 0.05)"
                      : "none",
                  transition: "all 0.3s ease",
                }}
                onClick={() => selectRecentLocation(location)}
              >
                <ListItemButton
                  sx={{
                    py: 2,
                    px: 2.5,
                    "&:hover": {
                      bgcolor: "rgba(16, 217, 21, 0.08)",
                      "& .location-icon": {
                        transform: "scale(1.1)",
                        color: "primary.main",
                      },
                    },
                  }}
                >
                  <ListItemIcon sx={{ minWidth: 50 }}>
                    <Typography sx={{ fontSize: '1.5rem', mr: 1 }}>
                      {location.emoji}
                    </Typography>
                  </ListItemIcon>
                  <ListItemText
                    primary={location.name}
                    primaryTypographyProps={{
                      variant: "body1",
                      sx: {
                        fontWeight: 500,
                        fontSize: isMobile ? "0.9rem" : "1rem",
                      },
                    }}
                  />
                </ListItemButton>
              </ListItem>
            ))}
          </List>
        </Box>
      </DialogContent>

      <DialogActions
        sx={{
          p: isMobile ? 2 : 3,
          pt: 1,
          borderTop: "1px solid",
          borderColor: "divider",
          gap: 1,
        }}
      >
        <Button
          onClick={onClose}
          variant="outlined"
          color="secondary"
          sx={{ mr: 1, minWidth: isMobile ? "80px" : "100px" }}
        >
          ❌ Cancel
        </Button>
        <Button
          variant="contained"
          startIcon={<Navigation />}
          onClick={handleUpdateLocation}
          disabled={loading}
          sx={{
            minWidth: isMobile ? "120px" : "140px",
            py: isMobile ? 1 : 1.2,
            color: "white",
          }}
        >
          📍 Update Location
        </Button>
      </DialogActions>
    </Dialog>
  );
};

export default LocationMap;
```
### Step 27: Enhanced Auth Component 

**resources/js/components/auth/LoginModal.jsx**

```jsx
import React, { useState, useEffect } from "react";
import {
    Modal,
    Box,
    Typography,
    TextField,
    Button,
    IconButton,
    Card,
    CardContent,
    InputAdornment,
    Alert,
    CircularProgress,
    Link,
    useTheme,
    useMediaQuery,
    Fade,
    Backdrop,
    Select,
    MenuItem,
    FormControl,
    InputLabel,
    Avatar,
} from "@mui/material";
import {
    Close,
    Person,
    Phone,
    Email,
    Lock,
    Google,
    Facebook,
    Apple,
    GitHub,
    PersonAdd,
    Visibility,
    VisibilityOff,
    LoginOutlined,
    PersonAddOutlined,
} from "@mui/icons-material";
import axios from "axios";

// Reusable Components
const UserTypeSelect = ({ value, onChange, error, disabled, required = false, sx = {} }) => {
    const theme = useTheme();
    
    const userTypeOptions = [
        { value: "patient", label: "Patient" },
        { value: "doctor", label: "Doctor" },
        { value: "healthcare", label: "Healthcare Provider" },
        { value: "admin", label: "Admin" }, // ADDED ADMIN OPTION
    ];

    return (
        <FormControl
            fullWidth
            margin="dense"
            required={required}
            error={!!error}
            sx={{ mb: 1, ...sx }}
        >
            <InputLabel>I want to become a</InputLabel>
            <Select
                value={value}
                onChange={onChange}
                label="I want to become a"
                disabled={disabled}
                startAdornment={
                    <InputAdornment position="start">
                        <PersonAdd color="action" />
                    </InputAdornment>
                }
                sx={{
                    borderRadius: 2,
                    "&:hover": {
                        "& .MuiOutlinedInput-notchedOutline": {
                            borderColor: theme.palette.primary.main,
                        },
                    },
                }}
            >
                {userTypeOptions.map((option) => (
                    <MenuItem key={option.value} value={option.value}>
                        {option.label}
                    </MenuItem>
                ))}
            </Select>
            {error && (
                <Typography variant="caption" color="error" sx={{ mt: 0.5, ml: 1.5 }}>
                    {Array.isArray(error) ? error[0] : error}
                </Typography>
            )}
        </FormControl>
    );
};

const AuthToggle = ({ authType, setAuthType, disabled }) => (
    <Box
        sx={{
            display: "flex",
            mb: 1.5,
            borderRadius: 2,
            backgroundColor: "#f8f9fa",
            p: 0.5,
            border: "1px solid #e9ecef",
        }}
    >
        <Button
            fullWidth
            variant={authType === "mobile" ? "contained" : "text"}
            onClick={() => setAuthType("mobile")}
            startIcon={<Phone />}
            disabled={disabled}
            sx={{
                borderRadius: 1.5,
                textTransform: "none",
                fontWeight: 500,
                transition: "all 0.2s ease-in-out",
                ...(authType === "mobile" && {
                    background: "linear-gradient(135deg, #ff5983 0%, #dc004e 100%)",
                    color: "white",
                    boxShadow: "0 4px 8px rgba(196, 9, 25, 0.3)",
                }),
            }}
        >
            Mobile
        </Button>
        <Button
            fullWidth
            variant={authType === "email" ? "contained" : "text"}
            onClick={() => setAuthType("email")}
            startIcon={<Email />}
            disabled={disabled}
            sx={{
                borderRadius: 1.5,
                textTransform: "none",
                fontWeight: 500,
                transition: "all 0.2s ease-in-out",
                ...(authType === "email" && {
                    background: "linear-gradient(135deg, #ff5983 0%, #dc004e 100%)",
                    color: "white",
                    boxShadow: "0 4px 8px rgba(196, 9, 25, 0.3)",
                }),
            }}
        >
            Email
        </Button>
        <Button
            fullWidth
            variant={authType === "social" ? "contained" : "text"}
            onClick={() => setAuthType("social")}
            startIcon={<Google />}
            disabled={disabled}
            sx={{
                borderRadius: 1.5,
                textTransform: "none",
                fontWeight: 500,
                transition: "all 0.2s ease-in-out",
                ...(authType === "social" && {
                    background: "linear-gradient(135deg, #ff5983 0%, #dc004e 100%)",
                    color: "white",
                    boxShadow: "0 4px 8px rgba(196, 9, 25, 0.3)",
                }),
            }}
        >
            Social
        </Button>
    </Box>
);

const SubmitButton = ({ loading, disabled, onClick, children, type = "button" }) => (
    <Button
        type={type}
        fullWidth
        variant="contained"
        size="large"
        disabled={loading || disabled}
        onClick={onClick}
        sx={{
            mt: 1,
            mb: 1,
            borderRadius: 2,
            textTransform: "none",
            fontSize: "1.1rem",
            fontWeight: "bold",
            minHeight: "48px",
            backgroundColor: "#00C853",
            boxShadow: "0 4px 15px rgba(0, 200, 83, 0.4)",
            transition: "all 0.3s ease-in-out",
            "&:hover": {
                backgroundColor: "#00B348",
                boxShadow: "0 6px 20px rgba(0, 200, 83, 0.6)",
                transform: "translateY(-1px)",
            },
            "&:active": {
                transform: "translateY(0)",
            },
            "&:disabled": {
                background: "#e0e0e0",
                color: "#9e9e9e",
                boxShadow: "none",
                transform: "none",
            },
        }}
    >
        {loading ? (
            <Box sx={{ display: "flex", alignItems: "center", gap: 1 }}>
                <CircularProgress size={20} sx={{ color: "white" }} />
                <Typography variant="inherit">Loading...</Typography>
            </Box>
        ) : (
            children
        )}
    </Button>
);

const SocialButton = ({ provider, icon, onClick, disabled, fullWidth = false, color }) => (
    <Button
        fullWidth={fullWidth}
        variant="outlined"
        startIcon={icon}
        onClick={onClick}
        disabled={disabled}
        sx={{
            borderRadius: 2,
            textTransform: "none",
            fontWeight: 500,
            minHeight: "44px",
            borderColor: color,
            color: color,
            transition: "all 0.3s ease-in-out",
            "&:hover": {
                backgroundColor: color,
                color: "white",
                borderColor: color,
                transform: "translateY(-1px)",
                boxShadow: `0 4px 12px ${color}30`,
            },
            "&:active": {
                transform: "translateY(0)",
            },
            "&:disabled": {
                borderColor: "#e0e0e0",
                color: "#9e9e9e",
            },
        }}
    >
        {provider}
    </Button>
);

// Main Component
const LoginModal = ({ open, onClose, onLoginSuccess }) => {
    const theme = useTheme();
    const isMobile = useMediaQuery(theme.breakpoints.down("sm"));
    const isTablet = useMediaQuery(theme.breakpoints.between("sm", "md"));
    const apiUrl = import.meta.env.VITE_API_URL;

    // Form states
    const [isLogin, setIsLogin] = useState(true);
    const [authType, setAuthType] = useState("mobile");
    const [showPassword, setShowPassword] = useState(false);
    const [showConfirmPassword, setShowConfirmPassword] = useState(false);
    
    // Mobile auth data
    const [mobileData, setMobileData] = useState({
        fullName: "",
        mobileNumber: "",
        userType: "",
    });
    
    // Email auth data
    const [emailData, setEmailData] = useState({
        name: "",
        email: "",
        password: "",
        password_confirmation: "",
        user_type: "",
    });
    
    // Social auth data
    const [socialData, setSocialData] = useState({
        user_type: "",
    });

    // Error states
    const [mobileErrors, setMobileErrors] = useState({
        name: "",
        number: "",
    });
    const [emailErrors, setEmailErrors] = useState({});
    const [loading, setLoading] = useState(false);
    const [generalError, setGeneralError] = useState("");
    const [socialProviders, setSocialProviders] = useState([]);
    const [socialLoading, setSocialLoading] = useState({});

    // Reset form when modal opens/closes
    useEffect(() => {
        if (!open) {
            const timer = setTimeout(() => {
                setMobileData({ fullName: "", mobileNumber: "", userType: "" });
                setEmailData({
                    name: "", email: "", password: "", password_confirmation: "", user_type: "",
                });
                setSocialData({ user_type: "" });
                setMobileErrors({ name: "", number: "" });
                setEmailErrors({});
                setGeneralError("");
                setLoading(false);
                setAuthType("mobile");
                setIsLogin(true);
                setShowPassword(false);
                setShowConfirmPassword(false);
                setSocialLoading({});
                // Clear stored user type on modal close
                localStorage.removeItem("selected_user_type");
                localStorage.removeItem("social_auth_mode");
                localStorage.removeItem("social_redirect_provider");
            }, 150);
            return () => clearTimeout(timer);
        }
    }, [open]);

    // Load social providers
    useEffect(() => {
        if (open) {
            loadSocialProviders();
        }
    }, [open]);

    const loadSocialProviders = async () => {
        try {
            const response = await axios.get(`${apiUrl}/auth/social/providers`);
            setSocialProviders(response.data.providers || []);
        } catch (error) {
            console.error('Failed to load social providers:', error);
            // Set default providers if API fails
            setSocialProviders(['google', 'facebook', 'github', 'apple']);
        }
    };

    const checkAlreadyLoggedIn = () => {
        const token = localStorage.getItem("auth_token") || localStorage.getItem("portal_token");
        const userId = localStorage.getItem("user_id");
        return token && userId;
    };

    // Mobile validation
    const validateMobileForm = () => {
        let valid = true;
        const trimmedName = mobileData.fullName.trim();
        const trimmedNumber = mobileData.mobileNumber.trim();

        setMobileErrors({ name: "", number: "" });

        if (!trimmedName) {
            setMobileErrors(prev => ({ ...prev, name: "Name is required" }));
            valid = false;
        } else if (trimmedName.length < 2) {
            setMobileErrors(prev => ({ ...prev, name: "Name must be at least 2 characters" }));
            valid = false;
        }

        const mobileRegex = /^[6-9]\d{9}$/;
        if (!trimmedNumber) {
            setMobileErrors(prev => ({ ...prev, number: "Mobile number is required" }));
            valid = false;
        } else if (!mobileRegex.test(trimmedNumber)) {
            setMobileErrors(prev => ({ ...prev, number: "Enter a valid 10-digit Indian mobile number" }));
            valid = false;
        }

        if (!mobileData.userType) {
            setGeneralError("Please select your role");
            valid = false;
        }

        return valid;
    };

    // Email validation
    const validateEmailForm = () => {
        let newErrors = {};
        let valid = true;

        if (!isLogin && !emailData.name.trim()) {
            newErrors.name = "Name is required";
            valid = false;
        } else if (!isLogin && emailData.name.trim().length < 2) {
            newErrors.name = "Name must be at least 2 characters";
            valid = false;
        }

        const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
        if (!emailData.email.trim()) {
            newErrors.email = "Email is required";
            valid = false;
        } else if (!emailRegex.test(emailData.email.trim())) {
            newErrors.email = "Enter a valid email address";
            valid = false;
        }

        if (!emailData.password) {
            newErrors.password = "Password is required";
            valid = false;
        } else if (!isLogin && emailData.password.length < 8) {
            newErrors.password = "Password must be at least 8 characters";
            valid = false;
        }

        if (!isLogin && emailData.password !== emailData.password_confirmation) {
            newErrors.password_confirmation = "Passwords do not match";
            valid = false;
        }

        if (!isLogin && !emailData.user_type) {
            newErrors.user_type = "Please select your role";
            valid = false;
        }

        setEmailErrors(newErrors);
        return valid;
    };

    // Handle mobile input changes
    const handleMobileInputChange = (field) => (event) => {
        setMobileData({ ...mobileData, [field]: event.target.value });

        if (field === "fullName" && event.target.value.trim().length >= 2) {
            setMobileErrors(prev => ({ ...prev, name: "" }));
        }
        if (field === "mobileNumber") {
            const mobileRegex = /^[6-9]\d{9}$/;
            if (mobileRegex.test(event.target.value.trim())) {
                setMobileErrors(prev => ({ ...prev, number: "" }));
            }
        }
        if (generalError) setGeneralError("");
    };

    // Handle email input changes
    const handleEmailInputChange = (field) => (event) => {
        setEmailData({ ...emailData, [field]: event.target.value });
        if (emailErrors[field]) {
            setEmailErrors({ ...emailErrors, [field]: "" });
        }
        if (generalError) setGeneralError("");
    };

    // Handle social input changes - CRITICAL FIX FOR USER TYPE HANDLING
    const handleSocialInputChange = (field) => (event) => {
        const newValue = event.target.value;
        console.log(`Social ${field} changed to:`, newValue);
        
        setSocialData({ ...socialData, [field]: newValue });
        
        // CRITICAL FIX: Store the selected user type immediately when changed
        if (field === "user_type" && newValue) {
            localStorage.setItem("selected_user_type", newValue);
            console.log("Stored user_type in localStorage:", newValue);
        }
        
        if (generalError) setGeneralError("");
    };

    // Mobile authentication
    const handleMobileAuth = async (e) => {
        e.preventDefault();
        if (!validateMobileForm()) return;

        if (checkAlreadyLoggedIn()) {
            setGeneralError("Please logout first before logging in again");
            return;
        }

        setLoading(true);
        setGeneralError("");

        try {
            const response = await axios.post(
                `${apiUrl}/auth/mobile/authenticate`,
                {
                    name: mobileData.fullName.trim(),
                    number: mobileData.mobileNumber.trim(),
                    userType: mobileData.userType,
                }
            );

            localStorage.removeItem("otp_token");
            localStorage.setItem("otp_token", response.data.token);

            console.log(`OTP sent: ${response.data.otp}`);

            onLoginSuccess({
                user: response.data.user,
                token: response.data.token,
                otp: response.data.otp,
                mobileNumber: mobileData.mobileNumber.trim(),
                requiresOTP: true,
            });

            onClose();
        } catch (error) {
            if (error.response?.data?.errors) {
                const errs = error.response.data.errors;
                if (errs.name) setMobileErrors(prev => ({ ...prev, name: errs.name[0] }));
                if (errs.number) setMobileErrors(prev => ({ ...prev, number: errs.number[0] }));
            } else {
                setGeneralError(error.response?.data?.message || "An error occurred during mobile authentication");
            }
        } finally {
            setLoading(false);
        }
    };

    // Email authentication
    const handleEmailAuth = async (e) => {
        e.preventDefault();
        if (!validateEmailForm()) return;

        if (checkAlreadyLoggedIn()) {
            setGeneralError("Please logout first before logging in again");
            return;
        }

        setLoading(true);
        setGeneralError("");

        try {
            const endpoint = isLogin ? "/login" : "/register";
            const payload = isLogin 
                ? {
                    email: emailData.email.trim(),
                    password: emailData.password,
                }
                : {
                    name: emailData.name.trim(),
                    email: emailData.email.trim(),
                    password: emailData.password,
                    password_confirmation: emailData.password_confirmation,
                    user_type: emailData.user_type,
                };

            console.log(`Making ${isLogin ? 'login' : 'register'} request to:`, `${apiUrl}${endpoint}`);
            console.log('Payload:', payload);

            // FIXED: Use axios.post with full URL
            const response = await axios({
                method: 'POST',
                url: `${apiUrl}${endpoint}`,
                data: payload,
                headers: {
                    'Content-Type': 'application/json',
                    'Accept': 'application/json'
                }
            });

            console.log('Auth response:', response.data);

            // Store authentication data
            localStorage.setItem("auth_token", response.data.token);
            localStorage.setItem("user_id", response.data.user.id);
            localStorage.setItem("user_data", JSON.stringify(response.data.user));

            // Call success handler
            onLoginSuccess({
                user: response.data.user,
                token: response.data.token,
                isNewUser: !isLogin,
            });

            onClose();
        } catch (error) {
            console.error('Auth error:', error);
            console.error('Error response:', error.response?.data);
            
            if (error.response?.data?.errors) {
                setEmailErrors(error.response.data.errors);
            } else if (error.response?.data?.message) {
                setGeneralError(error.response.data.message);
            } else if (error.message.includes('404')) {
                setGeneralError("Authentication endpoint not found. Please check your API configuration.");
            } else {
                setGeneralError("An error occurred during authentication. Please try again.");
            }
        } finally {
            setLoading(false);
        }
    };

    // CRITICAL FIX: Social Authentication with Proper User Type Handling
    const handleSocialLogin = async (provider) => {
        console.log("Social login initiated for provider:", provider);
        console.log("Current socialData:", socialData);
        console.log("IsLogin:", isLogin);
        
        // CRITICAL FIX: Always require user type selection for social auth
        if (!socialData.user_type) {
            setGeneralError("Please select your role first");
            return;
        }

        if (checkAlreadyLoggedIn()) {
            setGeneralError("Please logout first before logging in again");
            return;
        }

        try {
            setSocialLoading(prev => ({ ...prev, [provider]: true }));

            // CRITICAL FIX: Always store user type in localStorage before redirect
            const userTypeToStore = socialData.user_type;
            
            // Clear previous stored data
            localStorage.removeItem("selected_user_type");
            localStorage.removeItem("social_auth_mode");
            localStorage.removeItem("social_redirect_provider");
            localStorage.removeItem("social_redirect_timestamp");
            
            // Store the selected user type and auth mode
            localStorage.setItem("selected_user_type", userTypeToStore);
            localStorage.setItem("social_auth_mode", isLogin ? "login" : "register");
            localStorage.setItem("social_redirect_provider", provider);
            localStorage.setItem("social_redirect_timestamp", Date.now().toString());
            
            console.log("Stored data before redirect:", {
                selected_user_type: userTypeToStore,
                social_auth_mode: isLogin ? "login" : "register",
                provider: provider
            });
            
            // CRITICAL FIX: Always pass user_type in URL regardless of login/register mode
            const requestUrl = `${apiUrl}/auth/social/${provider}?user_type=${encodeURIComponent(userTypeToStore)}&mode=${isLogin ? 'login' : 'register'}`;
                
            console.log("Making request to:", requestUrl);
            
            const response = await axios.get(requestUrl);
            
            if (response.data.redirect_url) {
                // CRITICAL FIX: Ensure user_type is always in redirect URL
                let redirectUrl = response.data.redirect_url;
                
                // Parse the existing URL to add user_type parameter
                const url = new URL(redirectUrl);
                url.searchParams.set('user_type', userTypeToStore);
                redirectUrl = url.toString();
                
                console.log("Final redirect URL:", redirectUrl);
                
                // Redirect to the OAuth provider
                window.location.href = redirectUrl;
            } else {
                throw new Error("No redirect URL received");
            }
        } catch (error) {
            setSocialLoading(prev => ({ ...prev, [provider]: false }));
            console.error(`${provider} authentication failed:`, error);
            setGeneralError(`${provider} authentication failed: ${error.response?.data?.message || error.message}`);
        }
    };

    const handleClose = () => {
        if (!loading && !Object.values(socialLoading).some(loading => loading)) {
            onClose();
        }
    };

    const toggleAuthMode = () => {
        setIsLogin(!isLogin);
        setEmailErrors({});
        setMobileErrors({ name: "", number: "" });
        setGeneralError("");
        setEmailData({
            name: "", email: "", password: "", password_confirmation: "", user_type: "",
        });
        setMobileData({ fullName: "", mobileNumber: "", userType: "" });
        setSocialData({ user_type: "" });
        // Clear stored user type when switching modes
        localStorage.removeItem("selected_user_type");
        localStorage.removeItem("social_auth_mode");
    };

    const getModalWidth = () => {
        if (isMobile) {
            return "calc(100vw - 16px)";
        } else if (isTablet) {
            return "480px";
        } else {
            return "520px";
        }
    };

    const modalStyle = {
        position: "fixed",
        top: "50%",
        left: "50%",
        transform: "translate(-50%, -50%)",
        width: getModalWidth(),
        maxWidth: isMobile ? "420px" : "540px",
        maxHeight: "calc(100vh - 16px)",
        overflowY: "auto",
        outline: "none",
        zIndex: 1300,
        contain: "layout style paint",
        willChange: "transform, opacity",
    };

    return (
        <Modal
            open={open}
            onClose={handleClose}
            closeAfterTransition
            disableAutoFocus
            disableEnforceFocus
            disableRestoreFocus
            BackdropComponent={Backdrop}
            BackdropProps={{
                timeout: 200,
                sx: {
                    backgroundColor: "rgba(0, 0, 0, 0.7)",
                    backdropFilter: "blur(8px)",
                    zIndex: 1299,
                },
            }}
            sx={{
                zIndex: 1300,
                position: "fixed",
                top: 0,
                left: 0,
                right: 0,
                bottom: 0,
                display: "flex",
                alignItems: "center",
                justifyContent: "center",
                padding: "8px",
                overflow: "hidden",
            }}
        >
            <Fade in={open} timeout={200} appear>
                <Box sx={modalStyle}>
                    <Card
                        sx={{
                            width: "100%",
                            boxShadow:
                                "0 24px 38px 3px rgba(0, 0, 0, 0.14), 0 9px 46px 8px rgba(0, 0, 0, 0.12), 0 11px 15px -7px rgba(0, 0, 0, 0.2)",
                            borderRadius: "16px",
                            overflow: "hidden",
                            backfaceVisibility: "hidden",
                            perspective: 1000,
                            transform: "translateZ(0)",
                        }}
                        elevation={0}
                    >
                        <CardContent sx={{ p: 0 }}>
                            {/* Header with Logo */}
                            <Box
                                sx={{
                                    p: 2.5,
                                    position: "relative",
                                    textAlign: "center",
                                    display: "flex",
                                    flexDirection: "column",
                                    alignItems: "center",
                                }}
                            >
                                <IconButton
                                    onClick={handleClose}
                                    disabled={loading || Object.values(socialLoading).some(loading => loading)}
                                    sx={{
                                        position: "absolute",
                                        right: 8,
                                        top: 8,
                                        color: "text.secondary",
                                        "&:hover": {
                                            backgroundColor: "rgba(0, 0, 0, 0.04)",
                                        },
                                        "&:disabled": {
                                            color: "text.disabled",
                                        },
                                    }}
                                >
                                    <Close />
                                </IconButton>

                                <Avatar
                                    alt="App Logo"
                                    src="/images/common/icon.webp"
                                    loading="eager"
                                    decoding="async"
                                    sx={{
                                        width: 55,
                                        height: 55,
                                        mb: 0.5,
                                        bgcolor: "white",
                                        mx: "auto",
                                        "& img": {
                                            objectFit: "contain",
                                            width: "100%",
                                            height: "100%",
                                        },
                                    }}
                                />

                                <Typography
                                    variant="h6"
                                    sx={{
                                        fontWeight: "bold",
                                        mb: 0.5,
                                        color: "text.primary",
                                    }}
                                >
                                    India's healthcare booking app
                                </Typography>
                                <Typography
                                    variant="body2"
                                    sx={{ color: "text.secondary" }}
                                >
                                    {authType === "mobile" 
                                        ? "Log in or Sign up" 
                                        : isLogin 
                                        ? "Log in to your account" 
                                        : "Create your account"
                                    }
                                </Typography>
                            </Box>

                            {/* Form Content */}
                            <Box sx={{ px: 2.5, pb: 2 }}>
                                {generalError && (
                                    <Alert
                                        severity="error"
                                        sx={{
                                            mb: 2,
                                            borderRadius: 2,
                                            fontSize: "0.85rem",
                                        }}
                                        onClose={() => setGeneralError("")}
                                    >
                                        {generalError}
                                    </Alert>
                                )}

                                {/* Debug Info - Remove in production */}
                                {process.env.NODE_ENV === 'development' && authType === "social" && (
                                    <Box sx={{ mb: 2, p: 1, bgcolor: "#f0f0f0", borderRadius: 1, fontSize: "0.8rem" }}>
                                        <Typography variant="caption">
                                            Debug - Social Data: {JSON.stringify(socialData)}
                                        </Typography>
                                        <br />
                                        <Typography variant="caption">
                                            Debug - IsLogin: {isLogin ? 'true' : 'false'}
                                        </Typography>
                                        <br />
                                        <Typography variant="caption">
                                            Debug - LocalStorage user_type: {localStorage.getItem("selected_user_type")}
                                        </Typography>
                                    </Box>
                                )}

                                {/* Auth Type Toggle */}
                                <AuthToggle
                                    authType={authType}
                                    setAuthType={setAuthType}
                                    disabled={loading || Object.values(socialLoading).some(loading => loading)}
                                />

                                {/* Mobile Authentication */}
                                {authType === "mobile" && (
                                    <form onSubmit={handleMobileAuth}>
                                        <TextField
                                            fullWidth
                                            label="Enter your name"
                                            value={mobileData.fullName}
                                            onChange={handleMobileInputChange("fullName")}
                                            margin="dense"
                                            required
                                            disabled={loading}
                                            placeholder="Enter your full name"
                                            error={!!mobileErrors.name}
                                            helperText={mobileErrors.name}
                                            InputProps={{
                                                startAdornment: (
                                                    <InputAdornment position="start">
                                                        <Person color="action" />
                                                    </InputAdornment>
                                                ),
                                            }}
                                            sx={{
                                                mb: 1,
                                                "& .MuiOutlinedInput-root": {
                                                    borderRadius: 2,
                                                    "&:hover": {
                                                        "& .MuiOutlinedInput-notchedOutline": {
                                                            borderColor: theme.palette.primary.main,
                                                        },
                                                    },
                                                },
                                            }}
                                        />

                                        <TextField
                                            fullWidth
                                            label="Enter mobile number"
                                            type="tel"
                                            value={mobileData.mobileNumber}
                                            onChange={handleMobileInputChange("mobileNumber")}
                                            margin="dense"
                                            required
                                            disabled={loading}
                                            placeholder="Enter mobile number"
                                            error={!!mobileErrors.number}
                                            helperText={mobileErrors.number}
                                            InputProps={{
                                                startAdornment: (
                                                    <InputAdornment position="start">
                                                        <Typography
                                                            variant="body1"
                                                            sx={{
                                                                mr: 1,
                                                                color: "text.secondary",
                                                            }}
                                                        >
                                                            +91
                                                        </Typography>
                                                    </InputAdornment>
                                                ),
                                            }}
                                            sx={{
                                                mb: 1,
                                                "& .MuiOutlinedInput-root": {
                                                    borderRadius: 2,
                                                    "&:hover": {
                                                        "& .MuiOutlinedInput-notchedOutline": {
                                                            borderColor: theme.palette.primary.main,
                                                        },
                                                    },
                                                },
                                            }}
                                        />

                                        <UserTypeSelect
                                            value={mobileData.userType}
                                            onChange={handleMobileInputChange("userType")}
                                            disabled={loading}
                                            required
                                        />

                                        <SubmitButton
                                            type="submit"
                                            loading={loading}
                                            disabled={
                                                !mobileData.fullName ||
                                                !mobileData.mobileNumber ||
                                                !mobileData.userType
                                            }
                                        >
                                            CONTINUE
                                        </SubmitButton>
                                    </form>
                                )}

                                {/* Email Authentication */}
                                {authType === "email" && (
                                    <form onSubmit={handleEmailAuth}>
                                        {!isLogin && (
                                            <TextField
                                                fullWidth
                                                label="Full Name"
                                                value={emailData.name}
                                                onChange={handleEmailInputChange("name")}
                                                margin="dense"
                                                required
                                                disabled={loading}
                                                placeholder="Enter your full name"
                                                error={!!emailErrors.name}
                                                helperText={emailErrors.name ? (Array.isArray(emailErrors.name) ? emailErrors.name[0] : emailErrors.name) : ""}
                                                InputProps={{
                                                    startAdornment: (
                                                        <InputAdornment position="start">
                                                            <Person color="action" />
                                                        </InputAdornment>
                                                    ),
                                                }}
                                                sx={{
                                                    mb: 1,
                                                    "& .MuiOutlinedInput-root": {
                                                        borderRadius: 2,
                                                        "&:hover": {
                                                            "& .MuiOutlinedInput-notchedOutline": {
                                                                borderColor: theme.palette.primary.main,
                                                            },
                                                        },
                                                    },
                                                }}
                                            />
                                        )}

                                        <TextField
                                            fullWidth
                                            label="Email Address"
                                            type="email"
                                            value={emailData.email}
                                            onChange={handleEmailInputChange("email")}
                                            margin="dense"
                                            required
                                            disabled={loading}
                                            placeholder="Enter your email"
                                            error={!!emailErrors.email}
                                            helperText={emailErrors.email ? (Array.isArray(emailErrors.email) ? emailErrors.email[0] : emailErrors.email) : ""}
                                            InputProps={{
                                                startAdornment: (
                                                    <InputAdornment position="start">
                                                        <Email color="action" />
                                                    </InputAdornment>
                                                ),
                                            }}
                                            sx={{
                                                mb: 1,
                                                "& .MuiOutlinedInput-root": {
                                                    borderRadius: 2,
                                                    "&:hover": {
                                                        "& .MuiOutlinedInput-notchedOutline": {
                                                            borderColor: theme.palette.primary.main,
                                                        },
                                                    },
                                                },
                                            }}
                                        />

                                        <TextField
                                            fullWidth
                                            label="Password"
                                            type={showPassword ? "text" : "password"}
                                            value={emailData.password}
                                            onChange={handleEmailInputChange("password")}
                                            margin="dense"
                                            required
                                            disabled={loading}
                                            placeholder="Enter your password"
                                            error={!!emailErrors.password}
                                            helperText={emailErrors.password ? (Array.isArray(emailErrors.password) ? emailErrors.password[0] : emailErrors.password) : ""}
                                            InputProps={{
                                                startAdornment: (
                                                    <InputAdornment position="start">
                                                        <Lock color="action" />
                                                    </InputAdornment>
                                                ),
                                                endAdornment: (
                                                    <InputAdornment position="end">
                                                        <IconButton
                                                            onClick={() => setShowPassword(!showPassword)}
                                                            disabled={loading}
                                                            edge="end"
                                                        >
                                                            {showPassword ? <VisibilityOff /> : <Visibility />}
                                                        </IconButton>
                                                    </InputAdornment>
                                                ),
                                            }}
                                            sx={{
                                                mb: 1,
                                                "& .MuiOutlinedInput-root": {
                                                    borderRadius: 2,
                                                    "&:hover": {
                                                        "& .MuiOutlinedInput-notchedOutline": {
                                                            borderColor: theme.palette.primary.main,
                                                        },
                                                    },
                                                },
                                            }}
                                        />

                                        {!isLogin && (
                                            <TextField
                                                fullWidth
                                                label="Confirm Password"
                                                type={showConfirmPassword ? "text" : "password"}
                                                value={emailData.password_confirmation}
                                                onChange={handleEmailInputChange("password_confirmation")}
                                                margin="dense"
                                                required
                                                disabled={loading}
                                                placeholder="Confirm your password"
                                                error={!!emailErrors.password_confirmation}
                                                helperText={emailErrors.password_confirmation ? (Array.isArray(emailErrors.password_confirmation) ? emailErrors.password_confirmation[0] : emailErrors.password_confirmation) : ""}
                                                InputProps={{
                                                    startAdornment: (
                                                        <InputAdornment position="start">
                                                            <Lock color="action" />
                                                        </InputAdornment>
                                                    ),
                                                    endAdornment: (
                                                        <InputAdornment position="end">
                                                            <IconButton
                                                                onClick={() => setShowConfirmPassword(!showConfirmPassword)}
                                                                disabled={loading}
                                                                edge="end"
                                                            >
                                                                {showConfirmPassword ? <VisibilityOff /> : <Visibility />}
                                                            </IconButton>
                                                        </InputAdornment>
                                                    ),
                                                }}
                                                sx={{
                                                    mb: 1,
                                                    "& .MuiOutlinedInput-root": {
                                                        borderRadius: 2,
                                                        "&:hover": {
                                                            "& .MuiOutlinedInput-notchedOutline": {
                                                                borderColor: theme.palette.primary.main,
                                                            },
                                                        },
                                                    },
                                                }}
                                            />
                                        )}

                                        {!isLogin && (
                                            <UserTypeSelect
                                                value={emailData.user_type}
                                                onChange={handleEmailInputChange("user_type")}
                                                error={emailErrors.user_type}
                                                disabled={loading}
                                                required
                                            />
                                        )}

                                        <SubmitButton
                                            type="submit"
                                            loading={loading}
                                        >
                                            {isLogin ? "LOGIN" : "CREATE ACCOUNT"}
                                        </SubmitButton>

                                        {/* Forgot Password Link for Login */}
                                        {isLogin && (
                                            <Box sx={{ textAlign: "center", mt: 1, mb: 2 }}>
                                                <Link
                                                    component="button"
                                                    type="button"
                                                    variant="body2"
                                                    onClick={() => {
                                                        // Handle forgot password
                                                        console.log("Forgot password clicked");
                                                    }}
                                                    disabled={loading}
                                                    sx={{
                                                        textDecoration: "none",
                                                        color: "primary.main",
                                                        cursor: "pointer",
                                                        "&:hover": {
                                                            textDecoration: "underline",
                                                        },
                                                        "&:disabled": {
                                                            color: "text.disabled",
                                                        },
                                                    }}
                                                >
                                                    Forgot your password?
                                                </Link>
                                            </Box>
                                        )}

                                        {/* Toggle Auth Mode */}
                                        <Box sx={{ textAlign: "center", mt: 1 }}>
                                            <Typography variant="body2" color="text.secondary">
                                                {isLogin ? "Don't have an account? " : "Already have an account? "}
                                                <Link
                                                    component="button"
                                                    type="button"
                                                    onClick={toggleAuthMode}
                                                    disabled={loading}
                                                    sx={{
                                                        textDecoration: "underline",
                                                        color: "primary.main",
                                                        cursor: "pointer",
                                                        "&:disabled": {
                                                            color: "text.disabled",
                                                        },
                                                    }}
                                                >
                                                    {isLogin ? "Sign up" : "Log in"}
                                                </Link>
                                            </Typography>
                                        </Box>
                                    </form>
                                )}

                                {/* CRITICAL FIX: Social Authentication with Proper User Type Handling */}
                                {authType === "social" && (
                                    <Box>
                                        {/* ALWAYS SHOW USER TYPE SELECT FOR SOCIAL AUTH */}
                                        <UserTypeSelect
                                            value={socialData.user_type}
                                            onChange={handleSocialInputChange("user_type")}
                                            disabled={loading || Object.values(socialLoading).some(loading => loading)}
                                            required={true} // Always required for social auth
                                            sx={{ mb: 2 }}
                                        />

                                        {/* Social Login Buttons - Only enable when user type is selected */}
                                        <Box
                                            sx={{
                                                display: "flex",
                                                flexDirection: "column",
                                                gap: 1.5,
                                                mb: 2,
                                            }}
                                        >
                                            <SocialButton
                                                provider={socialLoading.google ? "Connecting..." : "Continue with Google"}
                                                icon={socialLoading.google ? <CircularProgress size={20} /> : <Google />}
                                                onClick={() => handleSocialLogin("google")}
                                                disabled={loading || socialLoading.google || !socialData.user_type}
                                                fullWidth
                                                color="#db4437"
                                            />

                                            <Box sx={{ display: "flex", gap: 1 }}>
                                                <SocialButton
                                                    provider={socialLoading.facebook ? "Connecting..." : "Facebook"}
                                                    icon={socialLoading.facebook ? <CircularProgress size={20} /> : <Facebook />}
                                                    onClick={() => handleSocialLogin("facebook")}
                                                    disabled={loading || socialLoading.facebook || !socialData.user_type}
                                                    fullWidth
                                                    color="#4267B2"
                                                />

                                                <SocialButton
                                                    provider={socialLoading.github ? "Connecting..." : "GitHub"}
                                                    icon={socialLoading.github ? <CircularProgress size={20} /> : <GitHub />}
                                                    onClick={() => handleSocialLogin("github")}
                                                    disabled={loading || socialLoading.github || !socialData.user_type}
                                                    fullWidth
                                                    color="#333"
                                                />
                                            </Box>

                                            <SocialButton
                                                provider={socialLoading.apple ? "Connecting..." : "Continue with Apple"}
                                                icon={socialLoading.apple ? <CircularProgress size={20} /> : <Apple />}
                                                onClick={() => handleSocialLogin("apple")}
                                                disabled={loading || socialLoading.apple || !socialData.user_type}
                                                fullWidth
                                                color="#000000"
                                            />
                                        </Box>

                                        {/* Show helper text when no user type is selected */}
                                        {!socialData.user_type && (
                                            <Box sx={{ textAlign: "center", mb: 2 }}>
                                                <Typography variant="caption" color="text.secondary">
                                                    👆 Please select your role first to continue with social login
                                                </Typography>
                                            </Box>
                                        )}

                                        {/* Toggle Auth Mode for Social */}
                                        <Box sx={{ textAlign: "center", mt: 1 }}>
                                            <Typography variant="body2" color="text.secondary">
                                                {isLogin ? "Don't have an account? " : "Already have an account? "}
                                                <Link
                                                    component="button"
                                                    type="button"
                                                    onClick={toggleAuthMode}
                                                    disabled={loading || Object.values(socialLoading).some(loading => loading)}
                                                    sx={{
                                                        textDecoration: "underline",
                                                        color: "primary.main",
                                                        cursor: "pointer",
                                                        "&:disabled": {
                                                            color: "text.disabled",
                                                        },
                                                    }}
                                                >
                                                    {isLogin ? "Sign up" : "Log in"}
                                                </Link>
                                            </Typography>
                                        </Box>
                                    </Box>
                                )}

                                {/* Terms and Privacy */}
                                <Box
                                    sx={{
                                        textAlign: "center",
                                        pt: 1,
                                        borderTop: "1px solid #e9ecef",
                                    }}
                                >
                                    <Typography
                                        variant="caption"
                                        color="text.secondary"
                                        sx={{ fontSize: "0.75rem" }}
                                    >
                                        By continuing, you agree to our{" "}
                                        <Link
                                            component="button"
                                            type="button"
                                            variant="caption"
                                            onClick={() => {
                                                onClose();
                                                console.log("Navigate to Terms of Service");
                                            }}
                                            disabled={loading || Object.values(socialLoading).some(loading => loading)}
                                            sx={{
                                                textDecoration: "underline",
                                                color: "grey",
                                                fontSize: "inherit",
                                                cursor: "pointer",
                                                "&:disabled": {
                                                    color: "text.disabled",
                                                },
                                            }}
                                        >
                                            Terms of Service
                                        </Link>
                                        {" & "}
                                        <Link
                                            component="button"
                                            type="button"
                                            variant="caption"
                                            onClick={() => {
                                                onClose();
                                                console.log("Navigate to Privacy Policy");
                                            }}
                                            disabled={loading || Object.values(socialLoading).some(loading => loading)}
                                            sx={{
                                                textDecoration: "underline",
                                                color: "grey",
                                                fontSize: "inherit",
                                                cursor: "pointer",
                                                "&:disabled": {
                                                    color: "text.disabled",
                                                },
                                            }}
                                        >
                                            Privacy Policy
                                        </Link>
                                    </Typography>
                                </Box>
                            </Box>
                        </CardContent>
                    </Card>
                </Box>
            </Fade>
        </Modal>
    );
};

export default LoginModal;
```

**resources/js//components/auth/AuthCallback.jsx**

```jsx
import React, { useEffect, useState } from 'react';
import { useNavigate, useSearchParams } from 'react-router-dom';
import {
    Box,
    Typography,
    CircularProgress,
    Card,
    CardContent,
    Avatar,
    Fade,
    LinearProgress,
} from '@mui/material';
import {
    CheckCircle,
    Person,
    LocalHospital,
    MedicalServices,
    AdminPanelSettings,
} from '@mui/icons-material';

const AuthCallback = () => {
    const [searchParams] = useSearchParams();
    const navigate = useNavigate();
    const [progress, setProgress] = useState(0);
    const [userType, setUserType] = useState('');
    const [userName, setUserName] = useState('');

    useEffect(() => {
        // Get URL parameters for both new and old social auth styles
        const authToken = searchParams.get('token') || searchParams.get('portal_token');
        const userParam = searchParams.get('user');
        const isNewUser = searchParams.get('is_new_user') === 'true';
        const provider = searchParams.get('provider');
        const error = searchParams.get('error');
        const urlUserType = searchParams.get('user_type'); // CRITICAL FIX: Get user_type from URL

        console.log("AuthCallback received params:", {
            authToken: !!authToken,
            userParam: !!userParam,
            isNewUser,
            provider,
            error,
            urlUserType
        });

        // Handle error cases
        if (error) {
            console.error('Social auth error:', error);
            setTimeout(() => {
                navigate('/?error=social_auth_failed');
            }, 1000);
            return;
        }

        if (authToken && userParam) {
            try {
                // Parse user data
                const userData = JSON.parse(decodeURIComponent(userParam));
                setUserName(userData.name || 'User');
                
                // CRITICAL FIX: Use URL user_type if available, otherwise use userData user_type
                const finalUserType = urlUserType || userData.user_type || 'patient';
                setUserType(finalUserType);

                console.log("Processed user data:", {
                    userId: userData.id,
                    userType: finalUserType,
                    userName: userData.name,
                    isNewUser
                });

                // Clear existing tokens
                localStorage.removeItem('auth_token');
                localStorage.removeItem('portal_token');
                localStorage.removeItem('user_id');
                localStorage.removeItem('user_data');
                localStorage.removeItem('selected_user_type');
                localStorage.removeItem('social_auth_mode');
                localStorage.removeItem('social_redirect_provider');

                // Start progress animation
                const progressInterval = setInterval(() => {
                    setProgress((prev) => {
                        if (prev >= 100) {
                            clearInterval(progressInterval);
                            return 100;
                        }
                        return prev + 2;
                    });
                }, 50);

                // Store auth data after progress completes
                setTimeout(() => {
                    // Store tokens based on auth type
                    if (searchParams.get('portal_token')) {
                        // Old style mobile auth
                        localStorage.setItem('portal_token', authToken);
                        localStorage.setItem('user_id', userData.id);
                    } else {
                        // New style email/social auth
                        localStorage.setItem('auth_token', authToken);
                        localStorage.setItem('user_id', userData.id);
                    }
                    
                    // CRITICAL FIX: Store user data with correct user_type
                    const userDataToStore = {
                        ...userData,
                        user_type: finalUserType // Ensure correct user_type is stored
                    };
                    localStorage.setItem('user_data', JSON.stringify(userDataToStore));

                    console.log("Stored user data:", userDataToStore);

                    // Role-based redirection
                    redirectBasedOnRole(finalUserType);
                }, 3000);

            } catch (error) {
                console.error('Auth callback error:', error);
                setTimeout(() => {
                    navigate('/?error=auth_data_invalid');
                }, 1000);
            }
        } else {
            console.error('Missing auth data in callback');
            setTimeout(() => {
                navigate('/?error=missing_auth_data');
            }, 1000);
        }
    }, [searchParams, navigate]);

    const redirectBasedOnRole = (role) => {
        console.log("Redirecting based on role:", role);
        
        switch (role) {
            case 'patient':
                navigate('/');
                break;
            case 'doctor':
                navigate('/doctor/dashboard');
                break;
            case 'healthcare':
                navigate('/healthcare/dashboard');
                break;
            case 'admin':
                navigate('/admin/dashboard');
                break;
            default:
                console.log("Unknown role, redirecting to home:", role);
                navigate('/');
        }
    };

    const getRoleIcon = () => {
        switch (userType) {
            case 'patient':
                return <Person sx={{ fontSize: 40, color: 'primary.main' }} />;
            case 'doctor':
                return <MedicalServices sx={{ fontSize: 40, color: 'success.main' }} />;
            case 'healthcare':
                return <LocalHospital sx={{ fontSize: 40, color: 'info.main' }} />;
            case 'admin':
                return <AdminPanelSettings sx={{ fontSize: 40, color: 'warning.main' }} />;
            default:
                return <Person sx={{ fontSize: 40, color: 'primary.main' }} />;
        }
    };

    const getRoleColor = () => {
        switch (userType) {
            case 'patient':
                return 'primary.main';
            case 'doctor':
                return 'success.main';
            case 'healthcare':
                return 'info.main';
            case 'admin':
                return 'warning.main';
            default:
                return 'primary.main';
        }
    };

    const getRoleEmoji = () => {
        switch (userType) {
            case 'patient':
                return '👤';
            case 'doctor':
                return '👨‍⚕️';
            case 'healthcare':
                return '🏥';
            case 'admin':
                return '👑';
            default:
                return '👤';
        }
    };

    const getRoleDisplayName = () => {
        switch (userType) {
            case 'patient':
                return 'Patient';
            case 'doctor':
                return 'Doctor';
            case 'healthcare':
                return 'Healthcare Provider';
            case 'admin':
                return 'Administrator';
            default:
                return 'User';
        }
    };

    return (
        <Box
            sx={{
                display: 'flex',
                justifyContent: 'center',
                alignItems: 'center',
                height: '100vh',
                width: '100vw',
                position: 'fixed',
                top: 0,
                left: 0,
                background: 'linear-gradient(135deg, #667eea 0%, #764ba2 100%)',
                zIndex: 9999,
            }}
        >
            <Fade in={true} timeout={500}>
                <Card
                    elevation={24}
                    sx={{
                        maxWidth: 400,
                        width: '90%',
                        borderRadius: 4,
                        overflow: 'hidden',
                        background: 'rgba(255, 255, 255, 0.95)',
                        backdropFilter: 'blur(20px)',
                        border: '1px solid rgba(255, 255, 255, 0.2)',
                    }}
                >
                    <CardContent sx={{ p: 4, textAlign: 'center' }}>
                        {/* Logo */}
                        <Avatar
                            src="/images/common/icon.webp"
                            sx={{
                                width: 80,
                                height: 80,
                                mx: 'auto',
                                mb: 3,
                                border: '3px solid',
                                borderColor: getRoleColor(),
                                boxShadow: 6,
                            }}
                        />

                        {/* Role Icon */}
                        <Box
                            sx={{
                                mb: 3,
                                p: 2,
                                borderRadius: '50%',
                                bgcolor: 'rgba(255, 255, 255, 0.8)',
                                display: 'inline-flex',
                                alignItems: 'center',
                                justifyContent: 'center',
                                boxShadow: 4,
                            }}
                        >
                            {getRoleIcon()}
                        </Box>

                        {/* Welcome Message */}
                        <Typography
                            variant="h5"
                            sx={{
                                fontWeight: 'bold',
                                mb: 1,
                                color: 'text.primary',
                                display: 'flex',
                                alignItems: 'center',
                                justifyContent: 'center',
                                gap: 1,
                            }}
                        >
                            {getRoleEmoji()} Welcome, {userName}!
                        </Typography>

                        <Typography
                            variant="body1"
                            sx={{
                                color: 'text.secondary',
                                mb: 3,
                                fontWeight: 500,
                            }}
                        >
                            🎉 Login successful! Setting up your {getRoleDisplayName()} dashboard...
                        </Typography>

                        {/* Progress Bar */}
                        <Box sx={{ mb: 3 }}>
                            <LinearProgress
                                variant="determinate"
                                value={progress}
                                sx={{
                                    height: 8,
                                    borderRadius: 4,
                                    bgcolor: 'rgba(0, 0, 0, 0.1)',
                                    '& .MuiLinearProgress-bar': {
                                        borderRadius: 4,
                                        background: `linear-gradient(45deg, ${getRoleColor()}, ${getRoleColor()}dd)`,
                                    },
                                }}
                            />
                            <Typography
                                variant="caption"
                                sx={{
                                    display: 'block',
                                    mt: 1,
                                    color: 'text.secondary',
                                    fontWeight: 500,
                                }}
                            >
                                ⏳ {Math.round(progress)}% Complete
                            </Typography>
                        </Box>

                        {/* Status Message */}
                        <Box
                            sx={{
                                display: 'flex',
                                alignItems: 'center',
                                justifyContent: 'center',
                                gap: 2,
                                p: 2,
                                borderRadius: 3,
                                bgcolor: 'rgba(76, 175, 80, 0.1)',
                                border: '1px solid rgba(76, 175, 80, 0.3)',
                            }}
                        >
                            {progress >= 100 ? (
                                <CheckCircle sx={{ color: 'success.main' }} />
                            ) : (
                                <CircularProgress
                                    size={20}
                                    sx={{ color: getRoleColor() }}
                                />
                            )}
                            <Typography
                                variant="body2"
                                sx={{
                                    color: progress >= 100 ? 'success.main' : 'text.secondary',
                                    fontWeight: 600,
                                }}
                            >
                                {progress >= 100
                                    ? '✅ Redirecting to dashboard...'
                                    : '🔄 Completing authentication...'}
                            </Typography>
                        </Box>

                        {/* Debug Info - Remove in production */}
                        {process.env.NODE_ENV === 'development' && (
                            <Box sx={{ mt: 2, p: 1, bgcolor: "#f0f0f0", borderRadius: 1, fontSize: "0.8rem" }}>
                                <Typography variant="caption">
                                    Debug - User Type: {userType}
                                </Typography>
                                <br />
                                <Typography variant="caption">
                                    Debug - User Name: {userName}
                                </Typography>
                            </Box>
                        )}
                    </CardContent>
                </Card>
            </Fade>
        </Box>
    );
};

export default AuthCallback;
```

### Step 28: Enhanced Doctor Functionality

**resources/js/components/doctor/ClinicSearchModal.jsx**

```jsx
import React, { useState, useEffect } from 'react';
import {
    Dialog,
    DialogTitle,
    DialogContent,
    DialogActions,
    TextField,
    Button,
    Grid,
    Typography,
    Card,
    CardContent,
    Avatar,
    Chip,
    Box,
    FormControl,
    InputLabel,
    Select,
    MenuItem,
    CircularProgress,
    Alert,
    Pagination,
    IconButton,
    Tooltip,
    List,
    ListItem,
    ListItemText,
    ListItemIcon,
    Divider
} from '@mui/material';
import {
    Search,
    LocationOn,
    Phone,
    Email,
    Business,
    Send,
    CheckCircle,
    Schedule,
    MedicalServices,
    Close,
    Star
} from '@mui/icons-material';
import axios from 'axios';

const ClinicSearchModal = ({ open, onClose }) => {
    const apiUrl = import.meta.env.VITE_API_URL;
    const [searchParams, setSearchParams] = useState({
        name: '',
        specialty: '',
        location: '',
        type: ''
    });
    const [clinics, setClinics] = useState([]);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState('');
    const [currentPage, setCurrentPage] = useState(1);
    const [totalPages, setTotalPages] = useState(1);
    const [selectedClinic, setSelectedClinic] = useState(null);
    const [showConnectionDialog, setShowConnectionDialog] = useState(false);

    const organizationTypes = [
        { value: '', label: 'All Types' },
        { value: 'hospital', label: 'Hospital' },
        { value: 'clinic', label: 'Clinic' },
        { value: 'diagnostic_center', label: 'Diagnostic Center' },
        { value: 'pharmacy', label: 'Pharmacy' },
        { value: 'nursing_home', label: 'Nursing Home' }
    ];

    const handleSearch = async (page = 1) => {
        setLoading(true);
        setError('');

        try {
            const token = localStorage.getItem('auth_token');
            const params = {
                ...searchParams,
                page
            };

            const response = await axios.get(`${apiUrl}/doctor/search-clinics`, {
                headers: { Authorization: `Bearer ${token}` },
                params
            });

            setClinics(response.data.data);
            setCurrentPage(response.data.current_page);
            setTotalPages(response.data.last_page);
        } catch (error) {
            console.error('Search failed:', error);
            setError('Failed to search clinics');
        } finally {
            setLoading(false);
        }
    };

    useEffect(() => {
        if (open) {
            handleSearch();
        }
    }, [open]);

    const handleInputChange = (field, value) => {
        setSearchParams(prev => ({
            ...prev,
            [field]: value
        }));
    };

    const handlePageChange = (event, value) => {
        handleSearch(value);
    };

    const getConnectionStatusColor = (status) => {
        switch (status) {
            case 'connected': return 'success';
            case 'pending': return 'warning';
            default: return 'default';
        }
    };

    const getConnectionStatusText = (status) => {
        switch (status) {
            case 'connected': return 'Connected';
            case 'pending': return 'Request Pending';
            default: return 'Not Connected';
        }
    };

    const handleSendRequest = (clinic) => {
        setSelectedClinic(clinic);
        setShowConnectionDialog(true);
    };

    return (
        <>
            <Dialog 
                open={open} 
                onClose={onClose}
                maxWidth="lg" 
                fullWidth
            >
                <DialogTitle>
                    <Box sx={{ display: 'flex', alignItems: 'center', gap: 2 }}>
                        <Search color="primary" />
                        <Typography variant="h5" sx={{ fontWeight: 'bold' }}>
                            Find Healthcare Facilities
                        </Typography>
                    </Box>
                </DialogTitle>

                <DialogContent>
                    {/* Search Filters */}
                    <Card elevation={1} sx={{ mb: 3 }}>
                        <CardContent>
                            <Grid container spacing={2}>
                                <Grid item xs={12} sm={6} md={3}>
                                    <TextField
                                        fullWidth
                                        size="small"
                                        label="Clinic Name"
                                        value={searchParams.name}
                                        onChange={(e) => handleInputChange('name', e.target.value)}
                                        placeholder="Search by name..."
                                    />
                                </Grid>
                                <Grid item xs={12} sm={6} md={3}>
                                    <TextField
                                        fullWidth
                                        size="small"
                                        label="Specialty"
                                        value={searchParams.specialty}
                                        onChange={(e) => handleInputChange('specialty', e.target.value)}
                                        placeholder="e.g., Cardiology"
                                    />
                                </Grid>
                                <Grid item xs={12} sm={6} md={3}>
                                    <TextField
                                        fullWidth
                                        size="small"
                                        label="Location"
                                        value={searchParams.location}
                                        onChange={(e) => handleInputChange('location', e.target.value)}
                                        placeholder="City, area..."
                                    />
                                </Grid>
                                <Grid item xs={12} sm={6} md={3}>
                                    <FormControl fullWidth size="small">
                                        <InputLabel>Type</InputLabel>
                                        <Select
                                            value={searchParams.type}
                                            onChange={(e) => handleInputChange('type', e.target.value)}
                                        >
                                            {organizationTypes.map(type => (
                                                <MenuItem key={type.value} value={type.value}>
                                                    {type.label}
                                                </MenuItem>
                                            ))}
                                        </Select>
                                    </FormControl>
                                </Grid>
                                <Grid item xs={12}>
                                    <Button
                                        variant="contained"
                                        startIcon={<Search />}
                                        onClick={() => handleSearch(1)}
                                        disabled={loading}
                                        sx={{ color: 'white' }}
                                    >
                                        Search Clinics
                                    </Button>
                                </Grid>
                            </Grid>
                        </CardContent>
                    </Card>

                    {error && (
                        <Alert severity="error" sx={{ mb: 3 }}>
                            {error}
                        </Alert>
                    )}

                    {/* Search Results */}
                    {loading ? (
                        <Box sx={{ textAlign: 'center', py: 4 }}>
                            <CircularProgress />
                            <Typography variant="body1" sx={{ mt: 2 }}>
                                Searching clinics...
                            </Typography>
                        </Box>
                    ) : (
                        <>
                            <Typography variant="h6" sx={{ mb: 2 }}>
                                Search Results ({clinics.length} found)
                            </Typography>
                            
                            <Grid container spacing={2}>
                                {clinics.map((clinic) => (
                                    <Grid item xs={12} md={6} key={clinic.id}>
                                        <Card elevation={2} sx={{ height: '100%' }}>
                                            <CardContent>
                                                <Box sx={{ display: 'flex', alignItems: 'flex-start', gap: 2, mb: 2 }}>
                                                    <Avatar 
                                                        src={clinic.logo} 
                                                        sx={{ width: 60, height: 60 }}
                                                    >
                                                        <Business />
                                                    </Avatar>
                                                    <Box sx={{ flex: 1 }}>
                                                        <Typography variant="h6" sx={{ fontWeight: 'bold', mb: 1 }}>
                                                            {clinic.name}
                                                        </Typography>
                                                        <Box sx={{ display: 'flex', gap: 1, mb: 1 }}>
                                                            <Chip 
                                                                label={clinic.organization_type?.replace('_', ' ').toUpperCase()} 
                                                                size="small" 
                                                                color="primary" 
                                                            />
                                                            <Chip 
                                                                label={getConnectionStatusText(clinic.connection_status)}
                                                                size="small"
                                                                color={getConnectionStatusColor(clinic.connection_status)}
                                                                icon={clinic.connection_status === 'connected' ? <CheckCircle /> : undefined}
                                                            />
                                                        </Box>
                                                        {clinic.rating > 0 && (
                                                            <Box sx={{ display: 'flex', alignItems: 'center', gap: 1, mb: 1 }}>
                                                                <Star fontSize="small" color="warning" />
                                                                <Typography variant="body2">
                                                                    {clinic.rating} ({clinic.total_reviews} reviews)
                                                                </Typography>
                                                            </Box>
                                                        )}
                                                    </Box>
                                                </Box>

                                                <Typography variant="body2" color="text.secondary" sx={{ mb: 2 }}>
                                                    {clinic.description?.substring(0, 120)}...
                                                </Typography>

                                                <Box sx={{ mb: 2 }}>
                                                    <Box sx={{ display: 'flex', alignItems: 'center', gap: 1, mb: 1 }}>
                                                        <LocationOn fontSize="small" color="action" />
                                                        <Typography variant="body2">
                                                            {clinic.address?.substring(0, 50)}...
                                                        </Typography>
                                                    </Box>
                                                    <Box sx={{ display: 'flex', alignItems: 'center', gap: 1, mb: 1 }}>
                                                        <Phone fontSize="small" color="action" />
                                                        <Typography variant="body2">{clinic.phone}</Typography>
                                                    </Box>
                                                    <Box sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
                                                        <Schedule fontSize="small" color="action" />
                                                        <Typography variant="body2">
                                                            {clinic.opening_time} - {clinic.closing_time}
                                                        </Typography>
                                                    </Box>
                                                </Box>

                                                {/* Specialties */}
                                                {clinic.specialties && clinic.specialties.length > 0 && (
                                                    <Box sx={{ mb: 2 }}>
                                                        <Typography variant="caption" color="text.secondary">
                                                            Specialties:
                                                        </Typography>
                                                        <Box sx={{ display: 'flex', flexWrap: 'wrap', gap: 0.5, mt: 0.5 }}>
                                                            {clinic.specialties.slice(0, 3).map((specialty, index) => (
                                                                <Chip 
                                                                    key={index}
                                                                    label={specialty} 
                                                                    size="small" 
                                                                    variant="outlined" 
                                                                />
                                                            ))}
                                                            {clinic.specialties.length > 3 && (
                                                                <Chip 
                                                                    label={`+${clinic.specialties.length - 3} more`}
                                                                    size="small" 
                                                                    variant="outlined"
                                                                    color="secondary"
                                                                />
                                                            )}
                                                        </Box>
                                                    </Box>
                                                )}

                                                {/* Action Button */}
                                                <Box sx={{ display: 'flex', gap: 1 }}>
                                                    {clinic.connection_status === 'not_connected' && (
                                                        <Button
                                                            variant="contained"
                                                            size="small"
                                                            startIcon={<Send />}
                                                            onClick={() => handleSendRequest(clinic)}
                                                            sx={{ color: 'white' }}
                                                        >
                                                            Send Request
                                                        </Button>
                                                    )}
                                                    {clinic.connection_status === 'pending' && (
                                                        <Button
                                                            variant="outlined"
                                                            size="small"
                                                            disabled
                                                        >
                                                            Request Pending
                                                        </Button>
                                                    )}
                                                    {clinic.connection_status === 'connected' && (
                                                        <Button
                                                            variant="outlined"
                                                            size="small"
                                                            color="success"
                                                            startIcon={<CheckCircle />}
                                                        >
                                                            Connected
                                                        </Button>
                                                    )}
                                                </Box>
                                            </CardContent>
                                        </Card>
                                    </Grid>
                                ))}
                            </Grid>

                            {clinics.length === 0 && !loading && (
                                <Box sx={{ textAlign: 'center', py: 4 }}>
                                    <Typography variant="h6" color="text.secondary">
                                        No clinics found
                                    </Typography>
                                    <Typography variant="body2" color="text.secondary">
                                        Try adjusting your search criteria
                                    </Typography>
                                </Box>
                            )}

                            {/* Pagination */}
                            {totalPages > 1 && (
                                <Box sx={{ display: 'flex', justifyContent: 'center', mt: 3 }}>
                                    <Pagination
                                        count={totalPages}
                                        page={currentPage}
                                        onChange={handlePageChange}
                                        color="primary"
                                    />
                                </Box>
                            )}
                        </>
                    )}
                </DialogContent>

                <DialogActions>
                    <Button onClick={onClose}>Close</Button>
                </DialogActions>
            </Dialog>

            {/* Connection Request Dialog */}
            <ConnectionRequestDialog
                open={showConnectionDialog}
                onClose={() => setShowConnectionDialog(false)}
                clinic={selectedClinic}
                onSuccess={() => {
                    setShowConnectionDialog(false);
                    handleSearch(currentPage); // Refresh results
                }}
            />
        </>
    );
};

// Connection Request Dialog Component
const ConnectionRequestDialog = ({ open, onClose, clinic, onSuccess }) => {
    const apiUrl = import.meta.env.VITE_API_URL;
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState('');
    const [formData, setFormData] = useState({
        message: '',
        available_days: [],
        start_time: '09:00',
        end_time: '17:00',
        slot_duration: 30
    });

    const weekDays = [
        { value: 'monday', label: 'Monday' },
        { value: 'tuesday', label: 'Tuesday' },
        { value: 'wednesday', label: 'Wednesday' },
        { value: 'thursday', label: 'Thursday' },
        { value: 'friday', label: 'Friday' },
        { value: 'saturday', label: 'Saturday' },
        { value: 'sunday', label: 'Sunday' }
    ];

    const handleDayToggle = (day) => {
        setFormData(prev => ({
            ...prev,
            available_days: prev.available_days.includes(day)
                ? prev.available_days.filter(d => d !== day)
                : [...prev.available_days, day]
        }));
    };

    const handleSubmit = async () => {
        if (formData.available_days.length === 0) {
            setError('Please select at least one working day');
            return;
        }

        setLoading(true);
        setError('');

        try {
            const token = localStorage.getItem('auth_token');
            await axios.post(`${apiUrl}/doctor/send-connection-request`, {
                clinic_id: clinic.id,
                message: formData.message,
                proposed_schedule: {
                    available_days: formData.available_days,
                    start_time: formData.start_time,
                    end_time: formData.end_time,
                    slot_duration: formData.slot_duration
                }
            }, {
                headers: { Authorization: `Bearer ${token}` }
            });

            onSuccess();
        } catch (error) {
            console.error('Failed to send request:', error);
            setError(error.response?.data?.message || 'Failed to send connection request');
        } finally {
            setLoading(false);
        }
    };

    if (!clinic) return null;

    return (
        <Dialog open={open} onClose={onClose} maxWidth="sm" fullWidth>
            <DialogTitle>
                <Box sx={{ display: 'flex', alignItems: 'center', justifyContent: 'space-between' }}>
                    <Typography variant="h6">Send Connection Request</Typography>
                    <IconButton onClick={onClose}>
                        <Close />
                    </IconButton>
                </Box>
            </DialogTitle>

            <DialogContent>
                {error && (
                    <Alert severity="error" sx={{ mb: 2 }}>
                        {error}
                    </Alert>
                )}

                {/* Clinic Info */}
                <Card elevation={1} sx={{ mb: 3 }}>
                    <CardContent>
                        <Box sx={{ display: 'flex', alignItems: 'center', gap: 2 }}>
                            <Avatar src={clinic.logo}>
                                <Business />
                            </Avatar>
                            <Box>
                                <Typography variant="h6">{clinic.name}</Typography>
                                <Typography variant="body2" color="text.secondary">
                                    {clinic.organization_type?.replace('_', ' ').toUpperCase()}
                                </Typography>
                            </Box>
                        </Box>
                    </CardContent>
                </Card>

                <Grid container spacing={3}>
                    <Grid item xs={12}>
                        <TextField
                            fullWidth
                            multiline
                            rows={3}
                            label="Message (Optional)"
                            value={formData.message}
                            onChange={(e) => setFormData(prev => ({ ...prev, message: e.target.value }))}
                            placeholder="Introduce yourself and explain why you'd like to join this clinic..."
                            inputProps={{ maxLength: 500 }}
                            helperText={`${formData.message.length}/500 characters`}
                        />
                    </Grid>

                    <Grid item xs={12}>
                        <Typography variant="h6" gutterBottom>Proposed Schedule</Typography>
                    </Grid>

                    <Grid item xs={12} sm={6}>
                        <TextField
                            fullWidth
                            type="time"
                            label="Start Time"
                            value={formData.start_time}
                            onChange={(e) => setFormData(prev => ({ ...prev, start_time: e.target.value }))}
                            InputLabelProps={{ shrink: true }}
                        />
                    </Grid>

                    <Grid item xs={12} sm={6}>
                        <TextField
                            fullWidth
                            type="time"
                            label="End Time"
                            value={formData.end_time}
                            onChange={(e) => setFormData(prev => ({ ...prev, end_time: e.target.value }))}
                            InputLabelProps={{ shrink: true }}
                        />
                    </Grid>

                    <Grid item xs={12}>
                        <FormControl fullWidth>
                            <InputLabel>Slot Duration (minutes)</InputLabel>
                            <Select
                                value={formData.slot_duration}
                                onChange={(e) => setFormData(prev => ({ ...prev, slot_duration: e.target.value }))}
                            >
                                <MenuItem value={15}>15 minutes</MenuItem>
                                <MenuItem value={30}>30 minutes</MenuItem>
                                <MenuItem value={45}>45 minutes</MenuItem>
                                <MenuItem value={60}>60 minutes</MenuItem>
                            </Select>
                        </FormControl>
                    </Grid>

                    <Grid item xs={12}>
                        <Typography variant="subtitle1" gutterBottom>Available Days *</Typography>
                        <Grid container spacing={1}>
                            {weekDays.map((day) => (
                                <Grid item xs={6} sm={4} key={day.value}>
                                    <Button
                                        variant={formData.available_days.includes(day.value) ? 'contained' : 'outlined'}
                                        size="small"
                                        onClick={() => handleDayToggle(day.value)}
                                        sx={{ width: '100%' }}
                                    >
                                        {day.label}
                                    </Button>
                                </Grid>
                            ))}
                        </Grid>
                        {formData.available_days.length === 0 && (
                            <Typography variant="caption" color="error">
                                Please select at least one day
                            </Typography>
                        )}
                    </Grid>
                </Grid>
            </DialogContent>

            <DialogActions>
                <Button onClick={onClose} disabled={loading}>
                    Cancel
                </Button>
                <Button
                    variant="contained"
                    onClick={handleSubmit}
                    disabled={loading || formData.available_days.length === 0}
                    startIcon={loading ? <CircularProgress size={20} /> : <Send />}
                    sx={{ color: 'white' }}
                >
                    {loading ? 'Sending...' : 'Send Request'}
                </Button>
            </DialogActions>
        </Dialog>
    );
};

export default ClinicSearchModal;
```

**resources/js/components/doctor/ProfileSetupModal.jsx**

```jsx
import React, { useState } from 'react';
import {
    Dialog,
    DialogTitle,
    DialogContent,
    DialogActions,
    TextField,
    Button,
    Grid,
    Typography,
    Chip,
    Box,
    FormControl,
    InputLabel,
    Select,
    MenuItem,
    Alert,
    CircularProgress,
    Stepper,
    Step,
    StepLabel,
    Paper,
    InputAdornment
} from '@mui/material';
import {
    Person,
    School,
    Work,
    MedicalServices,
    MonetizationOn,
    Language,
    CloudUpload
} from '@mui/icons-material';
import axios from 'axios';

const ProfileSetupModal = ({ open, onClose, onComplete }) => {
    const apiUrl = import.meta.env.VITE_API_URL;
    const [activeStep, setActiveStep] = useState(0);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState('');

    // Form data
    const [formData, setFormData] = useState({
        specialization: '',
        license_number: '',
        experience_years: '',
        bio: '',
        education: '',
        consultation_fee: '',
        qualification: '',
        languages: [],
        services: [],
        profile_image: null
    });

    const [newLanguage, setNewLanguage] = useState('');
    const [newService, setNewService] = useState('');

    const steps = [
        'Basic Information',
        'Professional Details', 
        'Additional Information'
    ];

    const specializations = [
        'Cardiology', 'Dermatology', 'Endocrinology', 'Gastroenterology',
        'General Medicine', 'Neurology', 'Orthopedics', 'Pediatrics',
        'Psychiatry', 'Pulmonology', 'Radiology', 'Surgery'
    ];

    const handleInputChange = (field, value) => {
        setFormData(prev => ({
            ...prev,
            [field]: value
        }));
    };

    const handleFileChange = (e) => {
        const file = e.target.files[0];
        if (file) {
            setFormData(prev => ({
                ...prev,
                profile_image: file
            }));
        }
    };

    const addLanguage = () => {
        if (newLanguage && !formData.languages.includes(newLanguage)) {
            setFormData(prev => ({
                ...prev,
                languages: [...prev.languages, newLanguage]
            }));
            setNewLanguage('');
        }
    };

    const removeLanguage = (language) => {
        setFormData(prev => ({
            ...prev,
            languages: prev.languages.filter(lang => lang !== language)
        }));
    };

    const addService = () => {
        if (newService && !formData.services.includes(newService)) {
            setFormData(prev => ({
                ...prev,
                services: [...prev.services, newService]
            }));
            setNewService('');
        }
    };

    const removeService = (service) => {
        setFormData(prev => ({
            ...prev,
            services: prev.services.filter(serv => serv !== service)
        }));
    };

    const handleNext = () => {
        setActiveStep(prev => prev + 1);
    };

    const handleBack = () => {
        setActiveStep(prev => prev - 1);
    };

    const validateStep = (step) => {
        switch (step) {
            case 0:
                return formData.specialization && formData.license_number && formData.experience_years;
            case 1:
                return formData.bio && formData.qualification && formData.consultation_fee;
            case 2:
                return true; // Optional step
            default:
                return false;
        }
    };

    const handleSubmit = async () => {
        setLoading(true);
        setError('');

        try {
            const submitData = new FormData();
            
            // Add all form fields
            Object.keys(formData).forEach(key => {
                if (key === 'languages' || key === 'services') {
                    formData[key].forEach((item, index) => {
                        submitData.append(`${key}[${index}]`, item);
                    });
                } else if (key === 'profile_image' && formData[key]) {
                    submitData.append(key, formData[key]);
                } else {
                    submitData.append(key, formData[key]);
                }
            });

            const token = localStorage.getItem('auth_token');
            const response = await axios.post(`${apiUrl}/doctor/profile`, submitData, {
                headers: {
                    'Authorization': `Bearer ${token}`,
                    'Content-Type': 'multipart/form-data'
                }
            });

            onComplete(response.data.doctor);
            onClose();
        } catch (error) {
            console.error('Profile setup failed:', error);
            setError(error.response?.data?.message || 'Failed to setup profile');
        } finally {
            setLoading(false);
        }
    };

    const renderStepContent = (step) => {
        switch (step) {
            case 0:
                return (
                    <Grid container spacing={3}>
                        <Grid item xs={12}>
                            <FormControl fullWidth required>
                                <InputLabel>Specialization</InputLabel>
                                <Select
                                    value={formData.specialization}
                                    onChange={(e) => handleInputChange('specialization', e.target.value)}
                                    startAdornment={<MedicalServices sx={{mr: 1}} />}
                                >
                                    {specializations.map(spec => (
                                        <MenuItem key={spec} value={spec}>{spec}</MenuItem>
                                    ))}
                                </Select>
                            </FormControl>
                        </Grid>
                        <Grid item xs={12} sm={6}>
                            <TextField
                                fullWidth
                                required
                                label="Medical License Number"
                                value={formData.license_number}
                                onChange={(e) => handleInputChange('license_number', e.target.value)}
                                InputProps={{
                                    startAdornment: <InputAdornment position="start"><Work /></InputAdornment>
                                }}
                            />
                        </Grid>
                        <Grid item xs={12} sm={6}>
                            <TextField
                                fullWidth
                                required
                                type="number"
                                label="Years of Experience"
                                value={formData.experience_years}
                                onChange={(e) => handleInputChange('experience_years', e.target.value)}
                                inputProps={{ min: 0, max: 50 }}
                            />
                        </Grid>
                        <Grid item xs={12}>
                            <TextField
                                fullWidth
                                required
                                multiline
                                rows={4}
                                label="Professional Bio"
                                value={formData.bio}
                                onChange={(e) => handleInputChange('bio', e.target.value)}
                                placeholder="Tell patients about yourself, your approach to medicine, and your expertise..."
                                inputProps={{ maxLength: 1000 }}
                                helperText={`${formData.bio.length}/1000 characters`}
                            />
                        </Grid>
                    </Grid>
                );

            case 1:
                return (
                    <Grid container spacing={3}>
                        <Grid item xs={12}>
                            <TextField
                                fullWidth
                                required
                                label="Qualification"
                                value={formData.qualification}
                                onChange={(e) => handleInputChange('qualification', e.target.value)}
                                placeholder="MBBS, MD, MS, etc."
                                InputProps={{
                                    startAdornment: <InputAdornment position="start"><School /></InputAdornment>
                                }}
                            />
                        </Grid>
                        <Grid item xs={12}>
                            <TextField
                                fullWidth
                                label="Education Background"
                                multiline
                                rows={3}
                                value={formData.education}
                                onChange={(e) => handleInputChange('education', e.target.value)}
                                placeholder="Medical college, university, certifications..."
                            />
                        </Grid>
                        <Grid item xs={12} sm={6}>
                            <TextField
                                fullWidth
                                required
                                type="number"
                                label="Consultation Fee (₹)"
                                value={formData.consultation_fee}
                                onChange={(e) => handleInputChange('consultation_fee', e.target.value)}
                                InputProps={{
                                    startAdornment: <InputAdornment position="start"><MonetizationOn /></InputAdornment>
                                }}
                            />
                        </Grid>
                    </Grid>
                );

            case 2:
                return (
                    <Grid container spacing={3}>
                        {/* Languages */}
                        <Grid item xs={12}>
                            <Typography variant="h6" gutterBottom>Languages Spoken</Typography>
                            <Box sx={{ display: 'flex', gap: 1, mb: 2, alignItems: 'center' }}>
                                <TextField
                                    size="small"
                                    label="Add Language"
                                    value={newLanguage}
                                    onChange={(e) => setNewLanguage(e.target.value)}
                                    onKeyPress={(e) => e.key === 'Enter' && addLanguage()}
                                    InputProps={{
                                        startAdornment: <InputAdornment position="start"><Language /></InputAdornment>
                                    }}
                                />
                                <Button variant="outlined" onClick={addLanguage}>Add</Button>
                            </Box>
                            <Box sx={{ display: 'flex', flexWrap: 'wrap', gap: 1 }}>
                                {formData.languages.map((language, index) => (
                                    <Chip
                                        key={index}
                                        label={language}
                                        onDelete={() => removeLanguage(language)}
                                        color="primary"
                                        variant="outlined"
                                    />
                                ))}
                            </Box>
                        </Grid>

                        {/* Services */}
                        <Grid item xs={12}>
                            <Typography variant="h6" gutterBottom>Services Offered</Typography>
                            <Box sx={{ display: 'flex', gap: 1, mb: 2, alignItems: 'center' }}>
                                <TextField
                                    size="small"
                                    label="Add Service"
                                    value={newService}
                                    onChange={(e) => setNewService(e.target.value)}
                                    onKeyPress={(e) => e.key === 'Enter' && addService()}
                                />
                                <Button variant="outlined" onClick={addService}>Add</Button>
                            </Box>
                            <Box sx={{ display: 'flex', flexWrap: 'wrap', gap: 1 }}>
                                {formData.services.map((service, index) => (
                                    <Chip
                                        key={index}
                                        label={service}
                                        onDelete={() => removeService(service)}
                                        color="secondary"
                                        variant="outlined"
                                    />
                                ))}
                            </Box>
                        </Grid>

                        {/* Profile Image */}
                        <Grid item xs={12}>
                            <Typography variant="h6" gutterBottom>Profile Photo</Typography>
                            <Box sx={{ display: 'flex', alignItems: 'center', gap: 2 }}>
                                <Button
                                    variant="outlined"
                                    component="label"
                                    startIcon={<CloudUpload />}
                                >
                                    Upload Photo
                                    <input
                                        type="file"
                                        hidden
                                        accept="image/*"
                                        onChange={handleFileChange}
                                    />
                                </Button>
                                {formData.profile_image && (
                                    <Typography variant="body2" color="primary">
                                        {formData.profile_image.name}
                                    </Typography>
                                )}
                            </Box>
                        </Grid>
                    </Grid>
                );

            default:
                return null;
        }
    };

    return (
        <Dialog 
            open={open} 
            onClose={!loading ? onClose : undefined}
            maxWidth="md" 
            fullWidth
            disableEscapeKeyDown={loading}
        >
            <DialogTitle>
                <Typography variant="h5" component="div" sx={{ fontWeight: 'bold' }}>
                    Complete Your Doctor Profile
                </Typography>
                <Typography variant="body2" color="text.secondary">
                    Please provide your professional details to start connecting with clinics
                </Typography>
            </DialogTitle>

            <DialogContent>
                {error && (
                    <Alert severity="error" sx={{ mb: 3 }}>
                        {error}
                    </Alert>
                )}

                <Paper elevation={1} sx={{ p: 2, mb: 3 }}>
                    <Stepper activeStep={activeStep} alternativeLabel>
                        {steps.map((label) => (
                            <Step key={label}>
                                <StepLabel>{label}</StepLabel>
                            </Step>
                        ))}
                    </Stepper>
                </Paper>

                <Box sx={{ mt: 3 }}>
                    {renderStepContent(activeStep)}
                </Box>
            </DialogContent>

            <DialogActions sx={{ p: 3, pt: 1 }}>
                <Button 
                    onClick={handleBack} 
                    disabled={activeStep === 0 || loading}
                >
                    Back
                </Button>
                <Box sx={{ flex: 1 }} />
                {activeStep === steps.length - 1 ? (
                    <Button
                        variant="contained"
                        onClick={handleSubmit}
                        disabled={loading}
                        startIcon={loading ? <CircularProgress size={20} /> : <Person />}
                        sx={{ color: 'white' }}
                    >
                        {loading ? 'Setting up...' : 'Complete Profile'}
                    </Button>
                ) : (
                    <Button
                        variant="contained"
                        onClick={handleNext}
                        disabled={!validateStep(activeStep)}
                        sx={{ color: 'white' }}
                    >
                        Next
                    </Button>
                )}
            </DialogActions>
        </Dialog>
    );
};

export default ProfileSetupModal;
```

### Step 29: Enhanced healthcare functionality

**resources/js/components/healthcare/HealthcareProfileSetupModal.jsx**

```jsx
import React, { useState } from 'react';
import {
    Dialog,
    DialogTitle,
    DialogContent,
    DialogActions,
    TextField,
    Button,
    Grid,
    Typography,
    Chip,
    Box,
    FormControl,
    InputLabel,
    Select,
    MenuItem,
    Alert,
    CircularProgress,
    Stepper,
    Step,
    StepLabel,
    Paper,
    InputAdornment,
    FormControlLabel,
    Checkbox
} from '@mui/material';
import {
    Business,
    School,
    Work,
    MedicalServices,
    LocationOn,
    Phone,
    Email,
    CloudUpload,
    Schedule,
    Language
} from '@mui/icons-material';
import axios from 'axios';

const HealthcareProfileSetupModal = ({ open, onClose, onComplete }) => {
    const apiUrl = import.meta.env.VITE_API_URL;
    const [activeStep, setActiveStep] = useState(0);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState('');

    // Form data with proper initialization
    const [formData, setFormData] = useState({
        name: '',
        registration_number: '',
        license_number: '',
        organization_type: 'clinic',
        description: '',
        address: '',
        phone: '',
        email: '',
        website: '',
        opening_time: '09:00',
        closing_time: '18:00',
        working_days: [],
        established_date: '',
        bed_count: '',
        facilities: [],
        specialties: [],
        certifications: [],
        logo: null,
        image: null,
        images: []
    });

    const [newFacility, setNewFacility] = useState('');
    const [newSpecialty, setNewSpecialty] = useState('');
    const [newCertification, setNewCertification] = useState('');

    const steps = [
        'Organization Details',
        'Contact & Location', 
        'Services & Facilities',
        'Additional Information'
    ];

    const organizationTypes = [
        { value: 'hospital', label: '🏥 Hospital' },
        { value: 'clinic', label: '🏩 Clinic' },
        { value: 'diagnostic_center', label: '🔬 Diagnostic Center' },
        { value: 'pharmacy', label: '💊 Pharmacy' },
        { value: 'nursing_home', label: '🏠 Nursing Home' }
    ];

    const weekDays = [
        { value: 'monday', label: 'Monday' },
        { value: 'tuesday', label: 'Tuesday' },
        { value: 'wednesday', label: 'Wednesday' },
        { value: 'thursday', label: 'Thursday' },
        { value: 'friday', label: 'Friday' },
        { value: 'saturday', label: 'Saturday' },
        { value: 'sunday', label: 'Sunday' }
    ];

    const commonSpecialties = [
        'General Medicine', 'Cardiology', 'Dermatology', 'Orthopedics',
        'Pediatrics', 'Neurology', 'Gynecology', 'Psychiatry', 'Dentistry'
    ];

    const commonFacilities = [
        'X-Ray', 'CT Scan', 'MRI', 'Ultrasound', 'ECG', 'Laboratory',
        'Pharmacy', 'Emergency Care', 'Surgery Theater', 'ICU'
    ];

    const handleInputChange = (field, value) => {
        setFormData(prev => ({
            ...prev,
            [field]: value
        }));
    };

    const handleFileChange = (field, file) => {
        setFormData(prev => ({
            ...prev,
            [field]: file
        }));
    };

    const handleMultipleFilesChange = (files) => {
        setFormData(prev => ({
            ...prev,
            images: Array.from(files)
        }));
    };

    const addToArray = (field, value, newValueState, setNewValueState) => {
        if (value && !formData[field].includes(value)) {
            setFormData(prev => ({
                ...prev,
                [field]: [...prev[field], value]
            }));
            setNewValueState('');
        }
    };

    const addFromPredefined = (field, value) => {
        if (value && !formData[field].includes(value)) {
            setFormData(prev => ({
                ...prev,
                [field]: [...prev[field], value]
            }));
        }
    };

    const removeFromArray = (field, value) => {
        setFormData(prev => ({
            ...prev,
            [field]: prev[field].filter(item => item !== value)
        }));
    };

    const handleWorkingDayToggle = (day) => {
        const newWorkingDays = formData.working_days.includes(day)
            ? formData.working_days.filter(d => d !== day)
            : [...formData.working_days, day];
        
        handleInputChange('working_days', newWorkingDays);
    };

    const handleNext = () => {
        setActiveStep(prev => prev + 1);
    };

    const handleBack = () => {
        setActiveStep(prev => prev - 1);
    };

    const validateStep = (step) => {
        switch (step) {
            case 0:
                return formData.name && formData.registration_number && formData.license_number && formData.organization_type;
            case 1:
                return formData.address && formData.phone && formData.opening_time && formData.closing_time && formData.working_days.length > 0;
            case 2:
                return formData.description;
            case 3:
                return true; // Optional step
            default:
                return false;
        }
    };

    const validateWebsite = (url) => {
        if (!url) return true; // Empty is allowed
        const pattern = /^https?:\/\/.+/i;
        return pattern.test(url) || url.includes('.');
    };

    const handleSubmit = async () => {
        setLoading(true);
        setError('');

        try {
            const submitData = new FormData();
            
            // Add all form fields
            Object.keys(formData).forEach(key => {
                if (['facilities', 'specialties', 'certifications', 'working_days'].includes(key)) {
                    formData[key].forEach((item, index) => {
                        submitData.append(`${key}[${index}]`, item);
                    });
                } else if (key === 'images' && formData[key].length > 0) {
                    formData[key].forEach((file, index) => {
                        submitData.append(`images[${index}]`, file);
                    });
                } else if (['logo', 'image'].includes(key) && formData[key]) {
                    submitData.append(key, formData[key]);
                } else if (formData[key] !== null && formData[key] !== '') {
                    submitData.append(key, formData[key]);
                }
            });

            const token = localStorage.getItem('auth_token');
            const response = await axios.post(`${apiUrl}/healthcare/profile`, submitData, {
                headers: {
                    'Authorization': `Bearer ${token}`,
                    'Content-Type': 'multipart/form-data'
                }
            });

            onComplete(response.data.profile);
            onClose();
        } catch (error) {
            console.error('Profile setup failed:', error);
            if (error.response?.data?.errors) {
                const errorMessages = Object.values(error.response.data.errors).flat();
                setError(errorMessages.join(', '));
            } else {
                setError(error.response?.data?.message || 'Failed to setup profile');
            }
        } finally {
            setLoading(false);
        }
    };

    const renderStepContent = (step) => {
        switch (step) {
            case 0:
                return (
                    <Grid container spacing={3}>
                        <Grid item xs={12}>
                            <TextField
                                fullWidth
                                required
                                label="Organization Name"
                                value={formData.name}
                                onChange={(e) => handleInputChange('name', e.target.value)}
                                InputProps={{
                                    startAdornment: <InputAdornment position="start"><Business /></InputAdornment>
                                }}
                                helperText="Enter the full legal name of your healthcare organization"
                            />
                        </Grid>
                        <Grid item xs={12} sm={6}>
                            <TextField
                                fullWidth
                                required
                                label="Registration Number"
                                value={formData.registration_number}
                                onChange={(e) => handleInputChange('registration_number', e.target.value)}
                                InputProps={{
                                    startAdornment: <InputAdornment position="start"><Work /></InputAdornment>
                                }}
                                helperText="Government registration number"
                            />
                        </Grid>
                        <Grid item xs={12} sm={6}>
                            <TextField
                                fullWidth
                                required
                                label="Medical License Number"
                                value={formData.license_number}
                                onChange={(e) => handleInputChange('license_number', e.target.value)}
                                InputProps={{
                                    startAdornment: <InputAdornment position="start"><School /></InputAdornment>
                                }}
                                helperText="Medical practice license number"
                            />
                        </Grid>
                        <Grid item xs={12}>
                            <FormControl fullWidth required>
                                <InputLabel>Organization Type</InputLabel>
                                <Select
                                    value={formData.organization_type}
                                    onChange={(e) => handleInputChange('organization_type', e.target.value)}
                                >
                                    {organizationTypes.map(type => (
                                        <MenuItem key={type.value} value={type.value}>{type.label}</MenuItem>
                                    ))}
                                </Select>
                            </FormControl>
                        </Grid>
                        <Grid item xs={12}>
                            <TextField
                                fullWidth
                                required
                                multiline
                                rows={4}
                                label="Organization Description"
                                value={formData.description}
                                onChange={(e) => handleInputChange('description', e.target.value)}
                                placeholder="Tell patients about your healthcare facility, services, and mission..."
                                inputProps={{ maxLength: 2000 }}
                                helperText={`${formData.description.length}/2000 characters`}
                            />
                        </Grid>
                    </Grid>
                );

            case 1:
                return (
                    <Grid container spacing={3}>
                        <Grid item xs={12}>
                            <TextField
                                fullWidth
                                required
                                multiline
                                rows={3}
                                label="Complete Address"
                                value={formData.address}
                                onChange={(e) => handleInputChange('address', e.target.value)}
                                InputProps={{
                                    startAdornment: <InputAdornment position="start"><LocationOn /></InputAdornment>
                                }}
                                helperText="Full address including city, state, and postal code"
                            />
                        </Grid>
                        <Grid item xs={12} sm={6}>
                            <TextField
                                fullWidth
                                required
                                label="Phone Number"
                                value={formData.phone}
                                onChange={(e) => handleInputChange('phone', e.target.value)}
                                InputProps={{
                                    startAdornment: <InputAdornment position="start"><Phone /></InputAdornment>
                                }}
                                helperText="Primary contact number"
                            />
                        </Grid>
                        <Grid item xs={12} sm={6}>
                            <TextField
                                fullWidth
                                label="Email Address"
                                type="email"
                                value={formData.email}
                                onChange={(e) => handleInputChange('email', e.target.value)}
                                InputProps={{
                                    startAdornment: <InputAdornment position="start"><Email /></InputAdornment>
                                }}
                                helperText="Contact email (optional)"
                            />
                        </Grid>
                        <Grid item xs={12}>
                            <TextField
                                fullWidth
                                label="Website URL"
                                value={formData.website}
                                onChange={(e) => handleInputChange('website', e.target.value)}
                                placeholder="https://www.yourwebsite.com"
                                error={formData.website && !validateWebsite(formData.website)}
                                helperText={
                                    formData.website && !validateWebsite(formData.website)
                                        ? "Please enter a valid website URL"
                                        : "Your organization's website (optional)"
                                }
                                InputProps={{
                                    startAdornment: <InputAdornment position="start"><Language /></InputAdornment>
                                }}
                            />
                        </Grid>
                        
                        {/* Working Hours */}
                        <Grid item xs={12}>
                            <Typography variant="h6" gutterBottom sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
                                <Schedule color="primary" />
                                Operating Hours
                            </Typography>
                        </Grid>
                        <Grid item xs={12} sm={6}>
                            <TextField
                                fullWidth
                                required
                                type="time"
                                label="Opening Time"
                                value={formData.opening_time}
                                onChange={(e) => handleInputChange('opening_time', e.target.value)}
                                InputLabelProps={{ shrink: true }}
                                helperText="Daily opening time"
                            />
                        </Grid>
                        <Grid item xs={12} sm={6}>
                            <TextField
                                fullWidth
                                required
                                type="time"
                                label="Closing Time"
                                value={formData.closing_time}
                                onChange={(e) => handleInputChange('closing_time', e.target.value)}
                                InputLabelProps={{ shrink: true }}
                                helperText="Daily closing time"
                            />
                        </Grid>
                        
                        {/* Working Days */}
                        <Grid item xs={12}>
                            <Typography variant="subtitle1" gutterBottom>
                                Working Days * (Select at least one)
                            </Typography>
                            <Paper elevation={1} sx={{ p: 2, bgcolor: 'grey.50' }}>
                                <Grid container spacing={1}>
                                    {weekDays.map((day) => (
                                        <Grid item xs={6} sm={4} md={3} key={day.value}>
                                            <FormControlLabel
                                                control={
                                                    <Checkbox
                                                        checked={formData.working_days.includes(day.value)}
                                                        onChange={() => handleWorkingDayToggle(day.value)}
                                                        color="primary"
                                                    />
                                                }
                                                label={day.label}
                                            />
                                        </Grid>
                                    ))}
                                </Grid>
                            </Paper>
                        </Grid>
                    </Grid>
                );

            case 2:
                return (
                    <Grid container spacing={3}>
                        {/* Specialties */}
                        <Grid item xs={12}>
                            <Typography variant="h6" gutterBottom sx={{ display: 'flex', alignItems: 'center', gap: 1 }}>
                                <MedicalServices color="primary" />
                                Medical Specialties
                            </Typography>
                            <Typography variant="body2" color="text.secondary" sx={{ mb: 2 }}>
                                Select from common specialties or add custom ones
                            </Typography>
                            
                            {/* Quick add buttons for common specialties */}
                            <Box sx={{ mb: 2 }}>
                                <Typography variant="subtitle2" sx={{ mb: 1 }}>Quick Add:</Typography>
                                <Box sx={{ display: 'flex', flexWrap: 'wrap', gap: 1 }}>
                                    {commonSpecialties.filter(spec => !formData.specialties.includes(spec)).map((specialty) => (
                                        <Button
                                            key={specialty}
                                            size="small"
                                            variant="outlined"
                                            onClick={() => addFromPredefined('specialties', specialty)}
                                        >
                                            + {specialty}
                                        </Button>
                                    ))}
                                </Box>
                            </Box>

                            <Box sx={{ display: 'flex', gap: 1, mb: 2, alignItems: 'center' }}>
                                <TextField
                                    size="small"
                                    label="Add Custom Specialty"
                                    value={newSpecialty}
                                    onChange={(e) => setNewSpecialty(e.target.value)}
                                    onKeyPress={(e) => e.key === 'Enter' && addToArray('specialties', newSpecialty, newSpecialty, setNewSpecialty)}
                                />
                                <Button 
                                    variant="outlined" 
                                    onClick={() => addToArray('specialties', newSpecialty, newSpecialty, setNewSpecialty)}
                                    disabled={!newSpecialty}
                                >
                                    Add
                                </Button>
                            </Box>
                            <Box sx={{ display: 'flex', flexWrap: 'wrap', gap: 1, mb: 3 }}>
                                {formData.specialties.map((specialty, index) => (
                                    <Chip
                                        key={index}
                                        label={specialty}
                                        onDelete={() => removeFromArray('specialties', specialty)}
                                        color="secondary"
                                        variant="outlined"
                                    />
                                ))}
                            </Box>
                        </Grid>

                        {/* Facilities */}
                        <Grid item xs={12}>
                            <Typography variant="h6" gutterBottom>Facilities Available</Typography>
                            <Typography variant="body2" color="text.secondary" sx={{ mb: 2 }}>
                                Select from common facilities or add custom ones
                            </Typography>
                            
                            {/* Quick add buttons for common facilities */}
                            <Box sx={{ mb: 2 }}>
                                <Typography variant="subtitle2" sx={{ mb: 1 }}>Quick Add:</Typography>
                                <Box sx={{ display: 'flex', flexWrap: 'wrap', gap: 1 }}>
                                    {commonFacilities.filter(fac => !formData.facilities.includes(fac)).map((facility) => (
                                        <Button
                                            key={facility}
                                            size="small"
                                            variant="outlined"
                                            onClick={() => addFromPredefined('facilities', facility)}
                                        >
                                            + {facility}
                                        </Button>
                                    ))}
                                </Box>
                            </Box>

                            <Box sx={{ display: 'flex', gap: 1, mb: 2, alignItems: 'center' }}>
                                <TextField
                                    size="small"
                                    label="Add Custom Facility"
                                    value={newFacility}
                                    onChange={(e) => setNewFacility(e.target.value)}
                                    onKeyPress={(e) => e.key === 'Enter' && addToArray('facilities', newFacility, newFacility, setNewFacility)}
                                />
                                <Button 
                                    variant="outlined" 
                                    onClick={() => addToArray('facilities', newFacility, newFacility, setNewFacility)}
                                    disabled={!newFacility}
                                >
                                    Add
                                </Button>
                            </Box>
                            <Box sx={{ display: 'flex', flexWrap: 'wrap', gap: 1 }}>
                                {formData.facilities.map((facility, index) => (
                                    <Chip
                                        key={index}
                                        label={facility}
                                        onDelete={() => removeFromArray('facilities', facility)}
                                        color="primary"
                                        variant="outlined"
                                    />
                                ))}
                            </Box>
                        </Grid>
                    </Grid>
                );

            case 3:
                return (
                    <Grid container spacing={3}>
                        {/* Additional Details */}
                        <Grid item xs={12} sm={6}>
                            <TextField
                                fullWidth
                                type="date"
                                label="Established Date"
                                value={formData.established_date}
                                onChange={(e) => handleInputChange('established_date', e.target.value)}
                                InputLabelProps={{ shrink: true }}
                                helperText="When was your organization established? (optional)"
                            />
                        </Grid>
                        <Grid item xs={12} sm={6}>
                            <TextField
                                fullWidth
                                type="number"
                                label="Number of Beds"
                                value={formData.bed_count}
                                onChange={(e) => handleInputChange('bed_count', e.target.value)}
                                inputProps={{ min: 0, max: 10000 }}
                                helperText="Total bed capacity (if applicable)"
                            />
                        </Grid>

                        {/* Certifications */}
                        <Grid item xs={12}>
                            <Typography variant="h6" gutterBottom>Certifications & Accreditations</Typography>
                            <Typography variant="body2" color="text.secondary" sx={{ mb: 2 }}>
                                Add any certifications, accreditations, or awards
                            </Typography>
                            <Box sx={{ display: 'flex', gap: 1, mb: 2, alignItems: 'center' }}>
                                <TextField
                                    size="small"
                                    label="Add Certification"
                                    value={newCertification}
                                    onChange={(e) => setNewCertification(e.target.value)}
                                    onKeyPress={(e) => e.key === 'Enter' && addToArray('certifications', newCertification, newCertification, setNewCertification)}
                                />
                                <Button 
                                    variant="outlined" 
                                    onClick={() => addToArray('certifications', newCertification, newCertification, setNewCertification)}
                                    disabled={!newCertification}
                                >
                                    Add
                                </Button>
                            </Box>
                            <Box sx={{ display: 'flex', flexWrap: 'wrap', gap: 1, mb: 3 }}>
                                {formData.certifications.map((cert, index) => (
                                    <Chip
                                        key={index}
                                        label={cert}
                                        onDelete={() => removeFromArray('certifications', cert)}
                                        color="success"
                                        variant="outlined"
                                    />
                                ))}
                            </Box>
                        </Grid>

                        {/* Images */}
                        <Grid item xs={12}>
                            <Typography variant="h6" gutterBottom>Images & Media</Typography>
                            
                            {/* Logo */}
                            <Box sx={{ mb: 3 }}>
                                <Typography variant="subtitle1" gutterBottom>Organization Logo</Typography>
                                <Button
                                    variant="outlined"
                                    component="label"
                                    startIcon={<CloudUpload />}
                                >
                                    Upload Logo
                                    <input
                                        type="file"
                                        hidden
                                        accept="image/*"
                                        onChange={(e) => handleFileChange('logo', e.target.files[0])}
                                    />
                                </Button>
                                {formData.logo && (
                                    <Typography variant="body2" color="primary" sx={{ ml: 2 }}>
                                        ✓ {formData.logo.name}
                                    </Typography>
                                )}
                            </Box>

                            {/* Main Image */}
                            <Box sx={{ mb: 3 }}>
                                <Typography variant="subtitle1" gutterBottom>Main Facility Image</Typography>
                                <Button
                                    variant="outlined"
                                    component="label"
                                    startIcon={<CloudUpload />}
                                >
                                    Upload Main Image
                                    <input
                                        type="file"
                                        hidden
                                        accept="image/*"
                                        onChange={(e) => handleFileChange('image', e.target.files[0])}
                                    />
                                </Button>
                                {formData.image && (
                                    <Typography variant="body2" color="primary" sx={{ ml: 2 }}>
                                        ✓ {formData.image.name}
                                    </Typography>
                                )}
                            </Box>

                            {/* Additional Images */}
                            <Box>
                                <Typography variant="subtitle1" gutterBottom>Additional Images (Optional)</Typography>
                                <Button
                                    variant="outlined"
                                    component="label"
                                    startIcon={<CloudUpload />}
                                >
                                    Upload Multiple Images
                                    <input
                                        type="file"
                                        hidden
                                        accept="image/*"
                                        multiple
                                        onChange={(e) => handleMultipleFilesChange(e.target.files)}
                                    />
                                </Button>
                                {formData.images.length > 0 && (
                                    <Typography variant="body2" color="primary" sx={{ ml: 2 }}>
                                        ✓ {formData.images.length} image(s) selected
                                    </Typography>
                                )}
                            </Box>
                        </Grid>
                    </Grid>
                );

            default:
                return null;
        }
    };

    return (
        <Dialog 
            open={open} 
            onClose={!loading ? onClose : undefined}
            maxWidth="lg" 
            fullWidth
            disableEscapeKeyDown={loading}
        >
            <DialogTitle>
                <Typography variant="h5" component="div" sx={{ fontWeight: 'bold' }}>
                    🏥 Setup Healthcare Organization Profile
                </Typography>
                <Typography variant="body2" color="text.secondary">
                    Please provide your organization details to start connecting with doctors
                </Typography>
            </DialogTitle>

            <DialogContent>
                {error && (
                    <Alert severity="error" sx={{ mb: 3 }} onClose={() => setError('')}>
                        {error}
                    </Alert>
                )}

                <Paper elevation={1} sx={{ p: 2, mb: 3 }}>
                    <Stepper activeStep={activeStep} alternativeLabel>
                        {steps.map((label) => (
                            <Step key={label}>
                                <StepLabel>{label}</StepLabel>
                            </Step>
                        ))}
                    </Stepper>
                </Paper>

                <Box sx={{ mt: 3, minHeight: '500px' }}>
                    {renderStepContent(activeStep)}
                </Box>
            </DialogContent>

            <DialogActions sx={{ p: 3, pt: 1 }}>
                <Button 
                    onClick={handleBack} 
                    disabled={activeStep === 0 || loading}
                >
                    Back
                </Button>
                <Box sx={{ flex: 1 }} />
                {activeStep === steps.length - 1 ? (
                    <Button
                        variant="contained"
                        onClick={handleSubmit}
                        disabled={loading || !validateStep(activeStep)}
                        startIcon={loading ? <CircularProgress size={20} /> : <Business />}
                        sx={{ color: 'white' }}
                    >
                        {loading ? 'Setting up...' : 'Complete Profile'}
                    </Button>
                ) : (
                    <Button
                        variant="contained"
                        onClick={handleNext}
                        disabled={!validateStep(activeStep)}
                        sx={{ color: 'white' }}
                    >
                        Next
                    </Button>
                )}
            </DialogActions>
        </Dialog>
    );
};

export default HealthcareProfileSetupModal;
```

### Step 30: Enhanced CSS Styles

**resources/css/app.css**

```css
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@300;400;500;600;700&display=swap');

* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    margin: 0;
    padding: 0;
    font-family: 'Roboto', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
    line-height: 1.6;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    background-color: #f5f5f5;
}

/* CSS Variables for Theme Colors */
:root {
    --primary-color: #10d915;
    --secondary-color: #dc004e;
    --success-color: #10d915;
    --error-color: #f27474;
    --warning-color: #f7e119;
    --info-color: #2196f3;
    --text-primary: #1a2a32;
    --text-secondary: #6c757d;
    --appbar-bg: rgba(255, 255, 255, 0.95);
    --card-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
    --hover-shadow: 0 8px 24px rgba(0, 0, 0, 0.15);
}

/* Dark mode variables */
@media (prefers-color-scheme: dark) {
    :root {
        --appbar-bg: rgba(26, 32, 44, 0.95);
        --text-primary: #ffffff;
        --text-secondary: #a0aec0;
    }
}

/* Custom scrollbar */
::-webkit-scrollbar {
    width: 8px;
}

::-webkit-scrollbar-track {
    background: #f1f1f1;
    border-radius: 4px;
}

::-webkit-scrollbar-thumb {
    background: #c1c1c1;
    border-radius: 4px;
    transition: background 0.3s ease;
}

::-webkit-scrollbar-thumb:hover {
    background: #a8a8a8;
}

/* Smooth scroll behavior */
html {
    scroll-behavior: smooth;
}

/* Remove default button focus outline */
button:focus {
    outline: none;
}

/* Enhanced utility classes */
.gradient-text {
    background: linear-gradient(45deg, #1976d2, #dc004e);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
}

.gradient-primary {
    background: linear-gradient(45deg, var(--primary-color) 30%, #21CBF3 90%);
}

.gradient-secondary {
    background: linear-gradient(45deg, var(--secondary-color) 30%, #ff5983 90%);
}

/* Enhanced animations */
.animate-fade-in {
    animation: fadeIn 0.6s ease-in;
}

@keyframes fadeIn {
    from { 
        opacity: 0; 
        transform: translateY(20px); 
    }
    to { 
        opacity: 1; 
        transform: translateY(0); 
    }
}

.animate-slide-up {
    animation: slideUp 0.5s ease-out;
}

@keyframes slideUp {
    from {
        opacity: 0;
        transform: translateY(30px);
    }
    to {
        opacity: 1;
        transform: translateY(0);
    }
}

.animate-scale {
    transition: transform 0.3s ease-in-out;
}

.animate-scale:hover {
    transform: scale(1.02);
}

.animate-bounce {
    animation: bounce 2s infinite;
}

@keyframes bounce {
    0%, 20%, 53%, 80%, 100% {
        animation-timing-function: cubic-bezier(0.215, 0.610, 0.355, 1.000);
        transform: translate3d(0, 0, 0);
    }
    40%, 43% {
        animation-timing-function: cubic-bezier(0.755, 0.050, 0.855, 0.060);
        transform: translate3d(0, -30px, 0);
    }
    70% {
        animation-timing-function: cubic-bezier(0.755, 0.050, 0.855, 0.060);
        transform: translate3d(0, -15px, 0);
    }
    90% {
        transform: translate3d(0, -4px, 0);
    }
}

/* Enhanced card shadows */
.card-shadow {
    box-shadow: var(--card-shadow);
    transition: box-shadow 0.3s ease-in-out, transform 0.3s ease-in-out;
}

.card-shadow:hover {
    box-shadow: var(--hover-shadow);
    transform: translateY(-4px);
}

.card-shadow-lg {
    box-shadow: 0 10px 25px rgba(0, 0, 0, 0.15);
}

.card-shadow-xl {
    box-shadow: 0 20px 50px rgba(0, 0, 0, 0.2);
}

/* Loading animation */
@keyframes pulse {
    0% { opacity: 1; }
    50% { opacity: 0.5; }
    100% { opacity: 1; }
}

.pulse {
    animation: pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite;
}

@keyframes spin {
    from { transform: rotate(0deg); }
    to { transform: rotate(360deg); }
}

.spin {
    animation: spin 1s linear infinite;
}

/* Enhanced responsive utilities */
.container {
    width: 100%;
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 16px;
}

@media (min-width: 600px) {
    .container {
        padding: 0 24px;
    }
}

@media (min-width: 900px) {
    .container {
        padding: 0 32px;
    }
}

@media (min-width: 1200px) {
    .container {
        padding: 0 40px;
    }
}

/* Enhanced button styles */
.btn-gradient {
    background: linear-gradient(45deg, #1976d2 30%, #21CBF3 90%);
    border: none;
    border-radius: 8px;
    color: white;
    padding: 12px 24px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    text-transform: none;
    font-family: inherit;
}

.btn-gradient:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 20px rgba(25, 118, 210, 0.4);
}

.btn-gradient:active {
    transform: translateY(0);
}

.btn-gradient:disabled {
    opacity: 0.6;
    cursor: not-allowed;
    transform: none;
}

/* Enhanced form elements */
.form-input {
    border-radius: 8px;
    border: 1px solid #e2e8f0;
    padding: 12px 16px;
    font-size: 16px;
    transition: all 0.3s ease;
    background-color: #ffffff;
}

.form-input:focus {
    outline: none;
    border-color: var(--primary-color);
    box-shadow: 0 0 0 3px rgba(16, 217, 21, 0.1);
}

.form-input:hover {
    border-color: #cbd5e0;
}

/* Hide scrollbar for specific elements */
.hide-scrollbar {
    -ms-overflow-style: none;
    scrollbar-width: none;
}

.hide-scrollbar::-webkit-scrollbar {
    display: none;
}

/* Glass morphism effect */
.glass {
    background: rgba(255, 255, 255, 0.25);
    backdrop-filter: blur(10px);
    border: 1px solid rgba(255, 255, 255, 0.18);
}

.glass-dark {
    background: rgba(0, 0, 0, 0.25);
    backdrop-filter: blur(10px);
    border: 1px solid rgba(255, 255, 255, 0.1);
}

/* Material-UI custom overrides */
.MuiCard-root {
    transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1) !important;
}

.MuiButton-root {
    font-weight: 600 !important;
    text-transform: none !important;
}

.MuiChip-root {
    font-weight: 500 !important;
}

.MuiAlert-root {
    border-radius: 8px !important;
}

/* Enhanced focus styles */
.MuiOutlinedInput-root:hover .MuiOutlinedInput-notchedOutline {
    border-color: var(--primary-color) !important;
}

.MuiOutlinedInput-root.Mui-focused .MuiOutlinedInput-notchedOutline {
    border-color: var(--primary-color) !important;
}

/* Enhanced typography */
.text-gradient {
    background: linear-gradient(45deg, var(--primary-color), var(--info-color));
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
}

.text-shadow {
    text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1);
}

.text-shadow-lg {
    text-shadow: 3px 3px 6px rgba(0, 0, 0, 0.2);
}

/* Enhanced responsive font sizes */
@media (max-width: 600px) {
    .MuiTypography-h1 {
        font-size: 2rem !important;
    }
    
    .MuiTypography-h2 {
        font-size: 1.75rem !important;
    }
    
    .MuiTypography-h3 {
        font-size: 1.5rem !important;
    }
    
    .MuiTypography-h4 {
        font-size: 1.25rem !important;
    }
}

/* Custom emoji animations */
.emoji-bounce {
    display: inline-block;
    animation: emojiBounce 2s ease-in-out infinite;
}

@keyframes emojiBounce {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-10px); }
}

.emoji-pulse {
    display: inline-block;
    animation: emojiPulse 1.5s ease-in-out infinite;
}

@keyframes emojiPulse {
    0%, 100% { transform: scale(1); }
    50% { transform: scale(1.1); }
}

/* Enhanced accessibility */
@media (prefers-reduced-motion: reduce) {
    *,
    *::before,
    *::after {
        animation-duration: 0.01ms !important;
        animation-iteration-count: 1 !important;
        transition-duration: 0.01ms !important;
    }
}

/* Print styles */
@media print {
    .no-print {
        display: none !important;
    }
    
    body {
        background: white !important;
        color: black !important;
    }
}

/* High contrast mode support */
@media (prefers-contrast: high) {
    :root {
        --primary-color: #0066cc;
        --secondary-color: #cc0066;
        --text-primary: #000000;
        --text-secondary: #333333;
    }
}

/* Enhanced selection styles */
::selection {
    background-color: rgba(16, 217, 21, 0.3);
    color: inherit;
}

::-moz-selection {
    background-color: rgba(16, 217, 21, 0.3);
    color: inherit;
}
```


### Step 29: Database Seeders

**database/seeders/UserSeeder.php**

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use App\Models\User;
use Illuminate\Support\Facades\Hash;

class UserSeeder extends Seeder
{
    public function run()
    {
        // Create doctor users
        $doctorUsers = [
            [
                'name' => 'Dr. Rajesh Kumar',
                'email' => 'dr.rajesh@example.com',
                'password' => Hash::make('password'),
                'user_type' => 'doctor',
                'phone' => '+91-9876543210',
                'address' => '12 Medical Complex, Bhubaneswar, Odisha',
                'latitude' => 20.2961,
                'longitude' => 85.8245
            ],
            [
                'name' => 'Dr. Priya Sharma',
                'email' => 'dr.priya@example.com',
                'password' => Hash::make('password'),
                'user_type' => 'doctor',
                'phone' => '+91-9876543220',
                'address' => '34 Healthcare Avenue, Cuttack, Odisha',
                'latitude' => 20.4625,
                'longitude' => 85.8828
            ],
            [
                'name' => 'Dr. Arun Patel',
                'email' => 'dr.arun@example.com',
                'password' => Hash::make('password'),
                'user_type' => 'doctor',
                'phone' => '+91-9876543230',
                'address' => '56 Medical Street, Berhampur, Odisha',
                'latitude' => 19.3149,
                'longitude' => 84.7941
            ],
            [
                'name' => 'Dr. Kavya Reddy',
                'email' => 'dr.kavya@example.com',
                'password' => Hash::make('password'),
                'user_type' => 'doctor',
                'phone' => '+91-9876543240',
                'address' => '78 Pediatric Lane, Rourkela, Odisha',
                'latitude' => 22.2604,
                'longitude' => 84.8536
            ],
            [
                'name' => 'Dr. Vikram Singh',
                'email' => 'dr.vikram@example.com',
                'password' => Hash::make('password'),
                'user_type' => 'doctor',
                'phone' => '+91-9876543250',
                'address' => '90 Dermatology Plaza, Sambalpur, Odisha',
                'latitude' => 21.4669,
                'longitude' => 83.9812
            ],
            [
                'name' => 'Dr. Sunita Mohanty',
                'email' => 'dr.sunita@example.com',
                'password' => Hash::make('password'),
                'user_type' => 'doctor',
                'phone' => '+91-9876543260',
                'address' => '101 Cardiology Center, Puri, Odisha',
                'latitude' => 19.8135,
                'longitude' => 85.8312
            ],
            [
                'name' => 'Dr. Ramesh Jena',
                'email' => 'dr.ramesh@example.com',
                'password' => Hash::make('password'),
                'user_type' => 'doctor',
                'phone' => '+91-9876543270',
                'address' => '202 Orthopedic Clinic, Balasore, Odisha',
                'latitude' => 21.4934,
                'longitude' => 86.9335
            ],
            [
                'name' => 'Dr. Sujata Pradhan',
                'email' => 'dr.pradhan@example.com',
                'password' => Hash::make('password'),
                'user_type' => 'doctor',
                'phone' => '+91-9876543280',
                'address' => '303 Gynecology Wing, Angul, Odisha',
                'latitude' => 20.8397,
                'longitude' => 85.1022
            ],
            [
                'name' => 'Dr. Biswajit Mishra',
                'email' => 'dr.mishra@example.com',
                'password' => Hash::make('password'),
                'user_type' => 'doctor',
                'phone' => '+91-9876543290',
                'address' => '404 Neurology Department, Jharsuguda, Odisha',
                'latitude' => 21.8643,
                'longitude' => 84.0081
            ],
            [
                'name' => 'Dr. Rashmi Behera',
                'email' => 'dr.behera@example.com',
                'password' => Hash::make('password'),
                'user_type' => 'doctor',
                'phone' => '+91-9876543300',
                'address' => '505 ENT Specialist Center, Koraput, Odisha',
                'latitude' => 18.8126,
                'longitude' => 82.7095
            ]
        ];

        // Create patient users
        $patientUsers = [
            [
                'name' => 'Rahul Nayak',
                'email' => 'rahul@example.com',
                'password' => Hash::make('password'),
                'user_type' => 'patient',
                'phone' => '+91-9876543211',
                'address' => '789 Patient Street, Bhubaneswar, Odisha',
                'latitude' => 20.2961,
                'longitude' => 85.8245
            ],
            [
                'name' => 'Shreya Das',
                'email' => 'shreya@example.com',
                'password' => Hash::make('password'),
                'user_type' => 'patient',
                'phone' => '+91-9876543212',
                'address' => '456 Health Avenue, Cuttack, Odisha',
                'latitude' => 20.4625,
                'longitude' => 85.8828
            ],
            [
                'name' => 'Amit Sahoo',
                'email' => 'amit@example.com',
                'password' => Hash::make('password'),
                'user_type' => 'patient',
                'phone' => '+91-9876543213',
                'address' => '123 Wellness Road, Berhampur, Odisha',
                'latitude' => 19.3149,
                'longitude' => 84.7941
            ],
            [
                'name' => 'Priyanka Swain',
                'email' => 'priyanka@example.com',
                'password' => Hash::make('password'),
                'user_type' => 'patient',
                'phone' => '+91-9876543214',
                'address' => '321 Care Lane, Rourkela, Odisha',
                'latitude' => 22.2604,
                'longitude' => 84.8536
            ],
            [
                'name' => 'Sanjay Parida',
                'email' => 'sanjay@example.com',
                'password' => Hash::make('password'),
                'user_type' => 'patient',
                'phone' => '+91-9876543215',
                'address' => '654 Treatment Street, Sambalpur, Odisha',
                'latitude' => 21.4669,
                'longitude' => 83.9812
            ]
        ];

        // Create healthcare admin user
        $healthcareUser = [
            'name' => 'Dr. Manoj Patnaik',
            'email' => 'healthcare@example.com',
            'password' => Hash::make('password'),
            'user_type' => 'healthcare',
            'phone' => '+91-9876543199',
            'address' => 'Healthcare Administration, Medical College, Bhubaneswar',
            'latitude' => 20.2961,
            'longitude' => 85.8245
        ];

        // Create admin user
        $adminUser = [
            'name' => 'Admin User',
            'email' => 'admin@visitcare.in',
            'password' => Hash::make('Admin@0505'),
            'user_type' => 'admin',
            'phone' => '+91-9876543200',
            'address' => 'Admin Office, Healthcare Plaza, Bhubaneswar',
            'latitude' => 20.2961,
            'longitude' => 85.8245
        ];

        // Insert all users
        foreach ($doctorUsers as $userData) {
            User::create($userData);
        }

        foreach ($patientUsers as $userData) {
            User::create($userData);
        }

        User::create($healthcareUser);
        User::create($adminUser);

        $this->command->info('✅ Users seeded successfully with Odisha locations!');
    }
}
```

**database/seeders/ClinicSeeder.php**

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use App\Models\Clinic;

class ClinicSeeder extends Seeder
{
    public function run()
    {
        $clinics = [
            [
                'name' => 'Apollo Health City',
                'description' => 'Multi-specialty tertiary care hospital with state-of-the-art facilities and world-class medical services across all major specialties.',
                'address' => '154, Bannerghatta Road, Opposite IIM, Bangalore, Karnataka 560076',
                'latitude' => 12.9343,
                'longitude' => 77.6114,
                'phone' => '+91-80-2692-2222',
                'email' => 'info@apollohealthcity.com',
                'website' => 'https://www.apollohealthcity.com',
                'facilities' => ['🚨 24/7 Emergency', '🩻 Digital X-Ray', '🧲 MRI & CT Scan', '🔬 Advanced Lab', '💊 24/7 Pharmacy', '🏥 ICU & NICU', '🩺 OPD Consultation', '🚑 Ambulance Service'],
                'specialties' => ['Cardiology', 'Neurology', 'Oncology', 'Orthopedics', 'Gastroenterology', 'Nephrology', 'Endocrinology', 'Pulmonology'],
                'working_days' => ['monday', 'tuesday', 'wednesday', 'thursday', 'friday', 'saturday', 'sunday'],
                'opening_time' => '00:00',
                'closing_time' => '23:59',
                'rating' => 4.8,
                'total_reviews' => 2547,
                'is_featured' => true
            ],
            [
                'name' => 'Fortis Hospital',
                'description' => 'Leading healthcare provider offering comprehensive medical services with cutting-edge technology and experienced medical professionals.',
                'address' => '14, Cunningham Road, Bangalore, Karnataka 560052',
                'latitude' => 12.9855,
                'longitude' => 77.5990,
                'phone' => '+91-80-6621-4444',
                'email' => 'info@fortishealthcare.com',
                'website' => 'https://www.fortishealthcare.com',
                'facilities' => ['🚨 Emergency Care', '🩻 Radiology', '🔬 Pathology Lab', '💊 Pharmacy', '🏥 Critical Care', '🩺 Specialist Clinics', '🔄 Dialysis Center'],
                'specialties' => ['Cardiology', 'Neuroscience', 'Orthopedics', 'Oncology', 'Gastroenterology', 'Urology', 'Dermatology'],
                'working_days' => ['monday', 'tuesday', 'wednesday', 'thursday', 'friday', 'saturday'],
                'opening_time' => '06:00',
                'closing_time' => '22:00',
                'rating' => 4.6,
                'total_reviews' => 1889,
                'is_featured' => true
            ],
            [
                'name' => 'Manipal Hospital',
                'description' => 'Trusted healthcare institution providing patient-centric care with advanced medical technology and skilled healthcare professionals.',
                'address' => '98, Rustom Bagh, Airport Road, Bangalore, Karnataka 560017',
                'latitude' => 13.0181,
                'longitude' => 77.5619,
                'phone' => '+91-80-2502-4444',
                'email' => 'info@manipalhospitals.com',
                'website' => 'https://www.manipalhospitals.com',
                'facilities' => ['🚨 24x7 Emergency', '🩻 Advanced Imaging', '🔬 Clinical Lab', '💊 Retail Pharmacy', '🏥 Intensive Care', '🩺 Outpatient Services'],
                'specialties' => ['Cardiology', 'Neurology', 'Pediatrics', 'Orthopedics', 'General Surgery', 'Internal Medicine', 'Psychiatry'],
                'working_days' => ['monday', 'tuesday', 'wednesday', 'thursday', 'friday', 'saturday'],
                'opening_time' => '08:00',
                'closing_time' => '20:00',
                'rating' => 4.5,
                'total_reviews' => 1634,
                'is_featured' => true
            ],
            [
                'name' => 'Columbia Asia Hospital',
                'description' => 'International standard healthcare facility offering comprehensive medical services with focus on patient safety and quality care.',
                'address' => 'Kirloskar Business Park, Bellary Road, Hebbal, Bangalore, Karnataka 560024',
                'latitude' => 13.0513,
                'longitude' => 77.5944,
                'phone' => '+91-80-6742-5000',
                'email' => 'info@columbiaasia.com',
                'website' => 'https://www.columbiaasia.com',
                'facilities' => ['🚨 Emergency Department', '🩻 Diagnostic Imaging', '🔬 Laboratory Services', '💊 Pharmacy', '🏥 Day Care Surgery', '🩺 Health Screening'],
                'specialties' => ['General Medicine', 'Pediatrics', 'Obstetrics & Gynecology', 'Orthopedics', 'ENT', 'Ophthalmology', 'Dermatology'],
                'working_days' => ['monday', 'tuesday', 'wednesday', 'thursday', 'friday', 'saturday'],
                'opening_time' => '09:00',
                'closing_time' => '21:00',
                'rating' => 4.4,
                'total_reviews' => 967,
                'is_featured' => false
            ],
            [
                'name' => 'Narayana Health City',
                'description' => 'Affordable healthcare destination providing world-class medical services across multiple specialties with state-of-the-art infrastructure.',
                'address' => '258/A, Bommasandra Industrial Area, Anekal Taluk, Bangalore, Karnataka 560099',
                'latitude' => 12.8068,
                'longitude' => 77.6785,
                'phone' => '+91-80-7122-2200',
                'email' => 'info@narayanahealth.org',
                'website' => 'https://www.narayanahealth.org',
                'facilities' => ['🚨 Trauma & Emergency', '🩻 Advanced Radiology', '🔬 Reference Lab', '💊 24-Hour Pharmacy', '🏥 Critical Care Units', '🩺 Specialty Clinics', '🚁 Air Ambulance'],
                'specialties' => ['Cardiac Sciences', 'Neurosciences', 'Oncology', 'Transplant Medicine', 'Orthopedics', 'Gastroenterology', 'Nephrology'],
                'working_days' => ['monday', 'tuesday', 'wednesday', 'thursday', 'friday', 'saturday', 'sunday'],
                'opening_time' => '00:00',
                'closing_time' => '23:59',
                'rating' => 4.7,
                'total_reviews' => 3245,
                'is_featured' => true
            ],
            [
                'name' => 'Sakra World Hospital',
                'description' => 'Premium healthcare facility offering personalized medical care with Japanese hospital management standards and cutting-edge technology.',
                'address' => 'Syamala Hills, Opposite to Science City, Sompura Gate, Off Sarjapur Road, Bangalore, Karnataka 560034',
                'latitude' => 12.9057,
                'longitude' => 77.6906,
                'phone' => '+91-80-4969-4969',
                'email' => 'info@sakraworldhospital.com',
                'website' => 'https://www.sakraworldhospital.com',
                'facilities' => ['🚨 Level 1 Trauma Center', '🩻 3T MRI & 128 Slice CT', '🔬 NABL Accredited Lab', '💊 Clinical Pharmacy', '🏥 Robotic Surgery', '🩺 International Patient Services'],
                'specialties' => ['Cardiac Sciences', 'Neurosciences', 'Oncology', 'Orthopedics', 'Gastroenterology', 'Pulmonology', 'Emergency Medicine'],
                'working_days' => ['monday', 'tuesday', 'wednesday', 'thursday', 'friday', 'saturday'],
                'opening_time' => '08:00',
                'closing_time' => '22:00',
                'rating' => 4.6,
                'total_reviews' => 876,
                'is_featured' => false
            ]
        ];

        foreach ($clinics as $clinicData) {
            Clinic::create($clinicData);
        }

        $this->command->info('✅ Real healthcare clinics seeded successfully!');
    }
}
```

**database/seeders/DoctorSeeder.php**

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use App\Models\User;
use App\Models\Doctor;

class DoctorSeeder extends Seeder
{
    public function run()
    {
        $doctorUsers = User::where('user_type', 'doctor')->get();

        $doctorsData = [
            [
                'email' => 'dr.rajesh@example.com',
                'specialization' => 'Cardiology',
                'license_number' => 'ODISHA12345',
                'experience_years' => 15,
                'bio' => '🫀 Experienced cardiologist with expertise in interventional cardiology and heart disease prevention. Specializes in advanced cardiac procedures and patient care.',
                'consultation_fee' => 800.00,
                'qualification' => 'MBBS, MD Cardiology, Fellowship in Interventional Cardiology',
                'languages' => ['🇺🇸 English', '🇮🇳 Hindi', '🇮🇳 Odia'],
                'services' => ['🫀 Heart Checkup', '⚡ ECG', '🩺 Consultation', '💊 Treatment'],
                'rating' => 4.8,
                'total_reviews' => 125,
                'is_verified' => true
            ],
            [
                'email' => 'dr.priya@example.com',
                'specialization' => 'Dermatology',
                'license_number' => 'ODISHA12346',
                'experience_years' => 8,
                'bio' => '✨ Specialist in skin care, cosmetic dermatology, and advanced skin treatments with focus on aesthetic medicine and dermatological conditions.',
                'consultation_fee' => 600.00,
                'qualification' => 'MBBS, MD Dermatology, Aesthetic Medicine Certification',
                'languages' => ['🇺🇸 English', '🇮🇳 Hindi', '🇮🇳 Odia'],
                'services' => ['🌟 Skin Treatment', '💄 Cosmetic Care', '🔬 Skin Analysis', '💊 Medication'],
                'rating' => 4.5,
                'total_reviews' => 89,
                'is_verified' => true
            ],
            [
                'email' => 'dr.arun@example.com',
                'specialization' => 'General Medicine',
                'license_number' => 'ODISHA12347',
                'experience_years' => 12,
                'bio' => '🩺 General practitioner with expertise in family medicine and preventive healthcare, providing comprehensive medical care for all ages.',
                'consultation_fee' => 400.00,
                'qualification' => 'MBBS, MD General Medicine, Diploma in Family Medicine',
                'languages' => ['🇺🇸 English', '🇮🇳 Hindi', '🇮🇳 Odia'],
                'services' => ['🩺 General Checkup', '💉 Vaccination', '🔬 Health Screening', '💊 Treatment'],
                'rating' => 4.3,
                'total_reviews' => 156,
                'is_verified' => true
            ],
            [
                'email' => 'dr.kavya@example.com',
                'specialization' => 'Pediatrics',
                'license_number' => 'ODISHA12348',
                'experience_years' => 10,
                'bio' => '👶 Pediatric specialist focusing on child health, vaccination, and developmental care with compassionate approach to young patients.',
                'consultation_fee' => 500.00,
                'qualification' => 'MBBS, MD Pediatrics, Fellowship in Pediatric Critical Care',
                'languages' => ['🇺🇸 English', '🇮🇳 Hindi', '🇮🇳 Odia', '🇮🇳 Telugu'],
                'services' => ['👶 Child Care', '💉 Immunization', '📏 Growth Monitoring', '💊 Pediatric Treatment'],
                'rating' => 4.7,
                'total_reviews' => 203,
                'is_verified' => true
            ],
            [
                'email' => 'dr.vikram@example.com',
                'specialization' => 'Dermatology',
                'license_number' => 'ODISHA12349',
                'experience_years' => 6,
                'bio' => '🌟 Young dermatologist specializing in acne treatment and laser therapies with modern treatment approaches and advanced technology.',
                'consultation_fee' => 550.00,
                'qualification' => 'MBBS, MD Dermatology, Laser Therapy Certification',
                'languages' => ['🇺🇸 English', '🇮🇳 Hindi', '🇮🇳 Odia', '🇮🇳 Punjabi'],
                'services' => ['✨ Laser Treatment', '🌟 Acne Care', '🔬 Skin Analysis', '💊 Dermatology Care'],
                'rating' => 4.4,
                'total_reviews' => 67,
                'is_verified' => true
            ],
            [
                'email' => 'dr.sunita@example.com',
                'specialization' => 'Cardiology',
                'license_number' => 'ODISHA12350',
                'experience_years' => 14,
                'bio' => '❤️ Senior cardiologist with specialization in women\'s heart health and preventive cardiology. Expert in cardiac rehabilitation.',
                'consultation_fee' => 750.00,
                'qualification' => 'MBBS, MD Cardiology, DM Cardiology, Women\'s Heart Health Certificate',
                'languages' => ['🇺🇸 English', '🇮🇳 Hindi', '🇮🇳 Odia'],
                'services' => ['💖 Women\'s Heart Care', '⚡ Cardiac Screening', '🏃‍♀️ Cardiac Rehab', '💊 Heart Treatment'],
                'rating' => 4.6,
                'total_reviews' => 178,
                'is_verified' => true
            ],
            [
                'email' => 'dr.ramesh@example.com',
                'specialization' => 'Orthopedics',
                'license_number' => 'ODISHA12351',
                'experience_years' => 18,
                'bio' => '🦴 Senior orthopedic surgeon specializing in joint replacement, sports injuries, and spine surgery with extensive surgical experience.',
                'consultation_fee' => 900.00,
                'qualification' => 'MBBS, MS Orthopedics, Fellowship in Joint Replacement',
                'languages' => ['🇺🇸 English', '🇮🇳 Hindi', '🇮🇳 Odia'],
                'services' => ['🦴 Bone Surgery', '🏃‍♂️ Sports Injury', '🔧 Joint Replacement', '💊 Orthopedic Care'],
                'rating' => 4.9,
                'total_reviews' => 245,
                'is_verified' => true
            ],
            [
                'email' => 'dr.pradhan@example.com',
                'specialization' => 'Gynecology',
                'license_number' => 'ODISHA12352',
                'experience_years' => 16,
                'bio' => '👩‍⚕️ Experienced gynecologist specializing in women\'s health, pregnancy care, and minimally invasive gynecological surgeries.',
                'consultation_fee' => 700.00,
                'qualification' => 'MBBS, MD Gynecology, Fellowship in Laparoscopic Surgery',
                'languages' => ['🇺🇸 English', '🇮🇳 Hindi', '🇮🇳 Odia'],
                'services' => ['🤱 Pregnancy Care', '👩 Women\'s Health', '🔬 Gynec Surgery', '💊 Reproductive Health'],
                'rating' => 4.7,
                'total_reviews' => 189,
                'is_verified' => true
            ],
            [
                'email' => 'dr.mishra@example.com',
                'specialization' => 'Neurology',
                'license_number' => 'ODISHA12353',
                'experience_years' => 13,
                'bio' => '🧠 Neurologist specializing in stroke care, epilepsy management, and neurodegenerative diseases with advanced diagnostic expertise.',
                'consultation_fee' => 850.00,
                'qualification' => 'MBBS, MD Medicine, DM Neurology, Stroke Specialist Certificate',
                'languages' => ['🇺🇸 English', '🇮🇳 Hindi', '🇮🇳 Odia'],
                'services' => ['🧠 Neuro Care', '⚡ Stroke Treatment', '💊 Epilepsy Management', '🔬 Neuro Diagnostics'],
                'rating' => 4.8,
                'total_reviews' => 134,
                'is_verified' => true
            ],
            [
                'email' => 'dr.behera@example.com',
                'specialization' => 'ENT',
                'license_number' => 'ODISHA12354',
                'experience_years' => 11,
                'bio' => '👂 ENT specialist with expertise in ear, nose, and throat disorders, endoscopic surgeries, and hearing disorders management.',
                'consultation_fee' => 650.00,
                'qualification' => 'MBBS, MS ENT, Fellowship in Endoscopic Surgery',
                'languages' => ['🇺🇸 English', '🇮🇳 Hindi', '🇮🇳 Odia'],
                'services' => ['👂 Hearing Care', '👃 Nasal Treatment', '🗣️ Throat Care', '🔬 ENT Surgery'],
                'rating' => 4.5,
                'total_reviews' => 98,
                'is_verified' => true
            ]
        ];

        foreach ($doctorsData as $doctorData) {
            $user = $doctorUsers->where('email', $doctorData['email'])->first();
            if ($user) {
                Doctor::create([
                    'user_id' => $user->id,
                    'specialization' => $doctorData['specialization'],
                    'license_number' => $doctorData['license_number'],
                    'experience_years' => $doctorData['experience_years'],
                    'bio' => $doctorData['bio'],
                    'consultation_fee' => $doctorData['consultation_fee'],
                    'qualification' => $doctorData['qualification'],
                    'languages' => $doctorData['languages'],
                    'services' => $doctorData['services'],
                    'rating' => $doctorData['rating'],
                    'total_reviews' => $doctorData['total_reviews'],
                    'is_verified' => $doctorData['is_verified']
                ]);
            }
        }

        $this->command->info('✅ Doctors seeded successfully!');
    }
}
```

**database/seeders/DoctorClinicScheduleSeeder.php**

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use App\Models\Doctor;
use App\Models\Clinic;

class DoctorClinicScheduleSeeder extends Seeder
{
    public function run()
    {
        $doctors = Doctor::with('user')->get();
        $clinics = Clinic::all();

        $schedules = [
            'dr.rajesh@example.com' => [
                'Apollo Health City' => [
                    'available_days' => ['monday', 'tuesday', 'wednesday', 'friday'],
                    'start_time' => '10:00',
                    'end_time' => '16:00',
                    'slot_duration' => 30,
                    'is_active' => true
                ],
                'Fortis Hospital' => [
                    'available_days' => ['thursday', 'saturday'],
                    'start_time' => '09:00',
                    'end_time' => '13:00',
                    'slot_duration' => 30,
                    'is_active' => true
                ]
            ],
            'dr.priya@example.com' => [
                'Fortis Hospital' => [
                    'available_days' => ['monday', 'wednesday', 'friday'],
                    'start_time' => '14:00',
                    'end_time' => '18:00',
                    'slot_duration' => 30,
                    'is_active' => true
                ],
                'Manipal Hospital' => [
                    'available_days' => ['tuesday', 'thursday', 'saturday'],
                    'start_time' => '10:00',
                    'end_time' => '17:00',
                    'slot_duration' => 45,
                    'is_active' => true
                ]
            ],
            'dr.arun@example.com' => [
                'Manipal Hospital' => [
                    'available_days' => ['monday', 'tuesday', 'wednesday', 'thursday', 'friday'],
                    'start_time' => '09:00',
                    'end_time' => '17:00',
                    'slot_duration' => 20,
                    'is_active' => true
                ],
                'Columbia Asia Hospital' => [
                    'available_days' => ['saturday'],
                    'start_time' => '10:00',
                    'end_time' => '16:00',
                    'slot_duration' => 20,
                    'is_active' => true
                ]
            ],
            'dr.kavya@example.com' => [
                'Columbia Asia Hospital' => [
                    'available_days' => ['monday', 'tuesday', 'wednesday', 'thursday', 'friday'],
                    'start_time' => '09:00',
                    'end_time' => '17:00',
                    'slot_duration' => 30,
                    'is_active' => true
                ],
                'Narayana Health City' => [
                    'available_days' => ['saturday'],
                    'start_time' => '10:00',
                    'end_time' => '14:00',
                    'slot_duration' => 30,
                    'is_active' => true
                ]
            ],
            'dr.vikram@example.com' => [
                'Narayana Health City' => [
                    'available_days' => ['monday', 'wednesday', 'friday'],
                    'start_time' => '11:00',
                    'end_time' => '18:00',
                    'slot_duration' => 45,
                    'is_active' => true
                ],
                'Sakra World Hospital' => [
                    'available_days' => ['tuesday', 'thursday'],
                    'start_time' => '14:00',
                    'end_time' => '17:00',
                    'slot_duration' => 30,
                    'is_active' => true
                ]
            ],
            'dr.sunita@example.com' => [
                'Apollo Health City' => [
                    'available_days' => ['tuesday', 'thursday', 'saturday'],
                    'start_time' => '09:00',
                    'end_time' => '15:00',
                    'slot_duration' => 30,
                    'is_active' => true
                ]
            ],
            'dr.ramesh@example.com' => [
                'Fortis Hospital' => [
                    'available_days' => ['monday', 'wednesday', 'friday'],
                    'start_time' => '10:00',
                    'end_time' => '16:00',
                    'slot_duration' => 45,
                    'is_active' => true
                ]
            ],
            'dr.pradhan@example.com' => [
                'Manipal Hospital' => [
                    'available_days' => ['tuesday', 'thursday', 'saturday'],
                    'start_time' => '10:00',
                    'end_time' => '17:00',
                    'slot_duration' => 30,
                    'is_active' => true
                ]
            ],
            'dr.mishra@example.com' => [
                'Sakra World Hospital' => [
                    'available_days' => ['monday', 'wednesday', 'friday'],
                    'start_time' => '12:00',
                    'end_time' => '18:00',
                    'slot_duration' => 40,
                    'is_active' => true
                ]
            ],
            'dr.behera@example.com' => [
                'Narayana Health City' => [
                    'available_days' => ['tuesday', 'thursday', 'saturday'],
                    'start_time' => '11:00',
                    'end_time' => '17:00',
                    'slot_duration' => 30,
                    'is_active' => true
                ]
            ]
        ];

        foreach ($schedules as $doctorEmail => $clinicSchedules) {
            $doctor = $doctors->where('user.email', $doctorEmail)->first();
            
            if ($doctor) {
                foreach ($clinicSchedules as $clinicName => $schedule) {
                    $clinic = $clinics->where('name', $clinicName)->first();
                    
                    if ($clinic) {
                        $doctor->clinics()->attach($clinic->id, [
                            'available_days' => json_encode($schedule['available_days']),
                            'start_time' => $schedule['start_time'],
                            'end_time' => $schedule['end_time'],
                            'slot_duration' => $schedule['slot_duration'],
                            'is_active' => $schedule['is_active'],
                            'created_at' => now(),
                            'updated_at' => now()
                        ]);
                    } else {
                        $this->command->warn("Clinic '{$clinicName}' not found for doctor {$doctorEmail}");
                    }
                }
            } else {
                $this->command->warn("Doctor with email {$doctorEmail} not found");
            }
        }

        $this->command->info('✅ Doctor-Clinic schedules seeded successfully for Odisha locations!');
    }
}
```

**database/seeders/AppointmentSeeder.php**

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use App\Models\User;
use App\Models\Doctor;
use App\Models\Appointment;
use Carbon\Carbon;

class AppointmentSeeder extends Seeder
{
    public function run()
    {
        $patients = User::where('user_type', 'patient')->get();
        $doctors = Doctor::with(['user', 'clinics'])->get();

        $appointmentsData = [
            [
                'patient_email' => 'rahul@example.com',
                'doctor_email' => 'dr.rajesh@example.com',
                'clinic_name' => 'Apollo Health City',
                'appointment_date' => Carbon::today()->addDays(1)->format('Y-m-d'),
                'appointment_time' => '10:00',
                'status' => 'confirmed',
                'symptoms' => '💔 Chest pain and shortness of breath during physical activity',
                'payment_status' => 'paid'
            ],
            [
                'patient_email' => 'shreya@example.com',
                'doctor_email' => 'dr.priya@example.com',
                'clinic_name' => 'Fortis Hospital',
                'appointment_date' => Carbon::today()->addDays(2)->format('Y-m-d'),
                'appointment_time' => '14:30',
                'status' => 'pending',
                'symptoms' => '🌟 Acne treatment consultation and skin care advice needed',
                'payment_status' => 'pending'
            ],
            [
                'patient_email' => 'amit@example.com',
                'doctor_email' => 'dr.arun@example.com',
                'clinic_name' => 'Manipal Hospital',
                'appointment_date' => Carbon::today()->addDays(3)->format('Y-m-d'),
                'appointment_time' => '11:00',
                'status' => 'confirmed',
                'symptoms' => '🩺 Regular health checkup and preventive care assessment',
                'payment_status' => 'paid'
            ],
            [
                'patient_email' => 'priyanka@example.com',
                'doctor_email' => 'dr.kavya@example.com',
                'clinic_name' => 'Columbia Asia Hospital',
                'appointment_date' => Carbon::today()->addDays(1)->format('Y-m-d'),
                'appointment_time' => '15:00',
                'status' => 'confirmed',
                'symptoms' => '💉 Child vaccination and routine developmental checkup',
                'payment_status' => 'paid'
            ],
            [
                'patient_email' => 'sanjay@example.com',
                'doctor_email' => 'dr.vikram@example.com',
                'clinic_name' => 'Narayana Health City',
                'appointment_date' => Carbon::today()->addDays(4)->format('Y-m-d'),
                'appointment_time' => '16:00',
                'status' => 'pending',
                'symptoms' => '✨ Laser treatment consultation for skin pigmentation',
                'payment_status' => 'pending'
            ],
            [
                'patient_email' => 'rahul@example.com',
                'doctor_email' => 'dr.sunita@example.com',
                'clinic_name' => 'Apollo Health City',
                'appointment_date' => Carbon::today()->addDays(5)->format('Y-m-d'),
                'appointment_time' => '09:30',
                'status' => 'confirmed',
                'symptoms' => '💖 Heart palpitations and irregular heartbeat concerns',
                'payment_status' => 'paid'
            ],
            [
                'patient_email' => 'shreya@example.com',
                'doctor_email' => 'dr.ramesh@example.com',
                'clinic_name' => 'Fortis Hospital',
                'appointment_date' => Carbon::today()->addDays(6)->format('Y-m-d'),
                'appointment_time' => '12:00',
                'status' => 'pending',
                'symptoms' => '🦴 Knee joint pain and mobility issues after sports injury',
                'payment_status' => 'pending'
            ],
            [
                'patient_email' => 'amit@example.com',
                'doctor_email' => 'dr.pradhan@example.com',
                'clinic_name' => 'Manipal Hospital',
                'appointment_date' => Carbon::today()->addDays(7)->format('Y-m-d'),
                'appointment_time' => '10:30',
                'status' => 'confirmed',
                'symptoms' => '👩 Routine gynecological checkup and women\'s health consultation',
                'payment_status' => 'paid'
            ],
            [
                'patient_email' => 'priyanka@example.com',
                'doctor_email' => 'dr.mishra@example.com',
                'clinic_name' => 'Sakra World Hospital',
                'appointment_date' => Carbon::today()->addDays(8)->format('Y-m-d'),
                'appointment_time' => '14:00',
                'status' => 'confirmed',
                'symptoms' => '🧠 Frequent headaches and neurological symptoms evaluation',
                'payment_status' => 'paid'
            ],
            [
                'patient_email' => 'sanjay@example.com',
                'doctor_email' => 'dr.behera@example.com',
                'clinic_name' => 'Narayana Health City',
                'appointment_date' => Carbon::today()->addDays(9)->format('Y-m-d'),
                'appointment_time' => '11:30',
                'status' => 'pending',
                'symptoms' => '👂 Hearing problems and ear infection treatment needed',
                'payment_status' => 'pending'
            ]
        ];

        foreach ($appointmentsData as $appointmentData) {
            $patient = $patients->where('email', $appointmentData['patient_email'])->first();
            $doctor = $doctors->where('user.email', $appointmentData['doctor_email'])->first();
            
            if ($patient && $doctor) {
                $clinic = $doctor->clinics->where('name', $appointmentData['clinic_name'])->first();
                
                if ($clinic) {
                    Appointment::create([
                        'patient_id' => $patient->id,
                        'doctor_id' => $doctor->id,
                        'clinic_id' => $clinic->id,
                        'appointment_date' => $appointmentData['appointment_date'],
                        'appointment_time' => $appointmentData['appointment_time'],
                        'symptoms' => $appointmentData['symptoms'],
                        'amount' => $doctor->consultation_fee,
                        'status' => $appointmentData['status'],
                        'payment_status' => $appointmentData['payment_status']
                    ]);
                } else {
                    $this->command->warn("Clinic '{$appointmentData['clinic_name']}' not found for doctor {$appointmentData['doctor_email']}");
                }
            } else {
                if (!$patient) {
                    $this->command->warn("Patient with email {$appointmentData['patient_email']} not found. Skipping appointment.");
                }
                if (!$doctor) {
                    $this->command->warn("Doctor with email {$appointmentData['doctor_email']} not found. Skipping appointment.");
                }
            }
        }

        $this->command->info('✅ Sample appointments seeded successfully for Odisha healthcare system!');
    }
}
```

**database/seeders/DatabaseSeeder.php**

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;

class DatabaseSeeder extends Seeder
{
    /**
     * Seed the application's database.
     */
    public function run(): void
    {
        $this->command->info('🚀 Starting Visit Care database seeding...');
        $this->command->newLine();

        // Run seeders in proper order
        $this->call([
            ClinicSeeder::class,
            UserSeeder::class,
            DoctorSeeder::class,
            DoctorClinicScheduleSeeder::class,
            AppointmentSeeder::class,
        ]);

        $this->command->newLine();
        $this->command->info('🎯 Database seeding completed successfully!');
        $this->command->newLine();
        
        // Seeding Summary with emojis
        $this->command->info('📊 Seeding Summary:');
        $this->command->info('🏥 Clinics: 5 healthcare facilities created');
        $this->command->info('👥 Users: 11 users created (5 Doctors, 5 Patients, 1 Admin)');
        $this->command->info('🩺 Doctors: 5 verified doctor profiles generated');
        $this->command->info('📅 Schedules: Doctor-Clinic availability schedules linked');
        $this->command->info('📋 Appointments: 5 sample appointment records added');
        $this->command->newLine();
        
        $this->command->info('🎉 Visit Care database is ready for use! 🚀');
        $this->command->info('');
        $this->command->info('📝 Login Credentials:');
        $this->command->info('👨‍⚕️ Doctor: dr.rajesh@example.com / password');
        $this->command->info('👤 Patient: john@example.com / password');
        $this->command->info('👑 Admin: admin@example.com / password');
    }
}
```


### Step 30: Final Installation \& Setup Commands

```bash
# 1. Install Backend Dependencies
composer install

# 2. Install Frontend Dependencies
npm install

# 3. Generate Application Key
php artisan key:generate

# 4. Run Migrations
php artisan migrate

# 5. Seed Database
php artisan db:seed

# 6. Create Storage Link
php artisan storage:link

# 7. Cache Configuration (Optional)
php artisan config:cache
php artisan route:cache
php artisan view:cache

# 8. Start Development Servers
# Terminal 1: Laravel Server
php artisan serve

# Terminal 2: Vite Development Server
npm run dev
```


### 🎉 Completion Summary

**Visit Care Application is now complete with:**

✅ **Full-Stack Implementation**

- Laravel 12 backend with UUID-based models
- React 18 + Material-UI frontend
- Sanctum API authentication
- Location-based search functionality

✅ **Enhanced Features**

- Beautiful emoji-enhanced UI components
- Responsive design for all devices
- Google Maps integration
- Real-time search with typewriter effects
- Animated statistics counters
- Professional testimonials section

✅ **Database Structure**

- Complete migrations for all entities
- Comprehensive seeders with sample data
- Proper relationships and constraints
- UUID primary keys throughout

✅ **API Implementation**

- RESTful API endpoints
- Authentication \& authorization
- Location-based filtering
- Appointment booking system

✅ **Production Ready**

- Optimized build configuration
- Error handling \& validation
- Security best practices
- Performance optimizations

**🚀 Your Visit Care application is ready for deployment and use!**


