# 🗄️ Laravel Migrations - Complete Guide & Commands Reference

## 📋 Table of Contents
- [What are Laravel Migrations?](#-what-are-laravel-migrations)
- [Migration File Structure](#-migration-file-structure)
- [Migration Commands](#-migration-commands)
- [Creating Migrations](#-creating-migrations)
- [Column Types & Modifiers](#-column-types--modifiers)
- [Indexes & Constraints](#-indexes--constraints)
- [Advanced Migration Techniques](#-advanced-migration-techniques)
- [Best Practices](#-best-practices)
- [Troubleshooting](#-troubleshooting)

---

## 📖 What are Laravel Migrations?

### Definition
Laravel migrations are **version control for your database**. They allow you to define and share the database schema definition, making it easy to modify and track database changes across different environments.

### 🎯 Key Benefits
- **Version Control**: Track database changes like code changes
- **Team Collaboration**: Share database structure with team members
- **Environment Consistency**: Same database structure across dev/staging/production
- **Rollback Capability**: Undo database changes when needed
- **Automated Deployment**: Include database changes in deployment process

### 🔄 How Migrations Work
```
Developer writes migration → Migration runs → Database updated → Migration tracked
```

---

## 🏗️ Migration File Structure

### Basic Migration Anatomy
```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * Run the migrations - What to do
     */
    public function up(): void
    {
        Schema::create('users', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('email')->unique();
            $table->timestamp('email_verified_at')->nullable();
            $table->string('password');
            $table->rememberToken();
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations - How to undo
     */
    public function down(): void
    {
        Schema::dropIfExists('users');
    }
};
```

### 📁 File Naming Convention
```
YYYY_MM_DD_HHMMSS_migration_description.php

Examples:
2024_08_16_143022_create_users_table.php
2024_08_16_143055_add_email_to_users_table.php
2024_08_16_143123_create_posts_table.php
```

### 🗂️ Directory Structure
```
database/
├── migrations/
│   ├── 2014_10_12_000000_create_users_table.php
│   ├── 2014_10_12_100000_create_password_resets_table.php
│   ├── 2019_08_19_000000_create_failed_jobs_table.php
│   └── 2024_08_16_143022_create_posts_table.php
```

---

## ⚡ Migration Commands

### 1. 🎨 Creating Migrations

#### Basic Creation
```bash
# Create a basic migration
php artisan make:migration create_users_table

# Create migration for existing table
php artisan make:migration add_phone_to_users_table --table=users

# Create migration with table creation
php artisan make:migration create_posts_table --create=posts
```

#### Advanced Creation Options
```bash
# Create migration in subdirectory
php artisan make:migration create_users_table --path=database/migrations/users

# Create migration with specific timestamp
php artisan make:migration create_posts_table --realpath

# Create migration without timestamps in filename (Laravel 11+)
php artisan make:migration create_categories_table --anonymous
```

### 2. 🚀 Running Migrations

#### Basic Migration Commands
```bash
# Run all pending migrations
php artisan migrate

# Run migrations and show SQL output
php artisan migrate --pretend

# Force run migrations (bypass confirmation in production)
php artisan migrate --force

# Run migrations for specific environment
php artisan migrate --env=production
```

#### Step-by-Step Migration
```bash
# Run migrations one step at a time
php artisan migrate --step

# Run specific number of migrations
php artisan migrate --step=3
```

#### Path-Specific Migrations
```bash
# Run migrations from specific path
php artisan migrate --path=/database/migrations/users

# Run specific migration file
php artisan migrate --path=/database/migrations/2024_08_16_create_posts_table.php
```

### 3. 🔄 Rollback Commands

#### Basic Rollback
```bash
# Rollback last batch of migrations
php artisan migrate:rollback

# Rollback specific number of batches
php artisan migrate:rollback --batch=3

# Rollback specific number of steps
php artisan migrate:rollback --step=5

# Rollback all migrations
php artisan migrate:reset
```

#### Advanced Rollback
```bash
# Rollback and re-run migrations
php artisan migrate:refresh

# Rollback, re-run, and seed database
php artisan migrate:refresh --seed

# Rollback specific number of steps and re-run
php artisan migrate:refresh --step=3
```

### 4. 🔍 Migration Status & Information

#### Status Commands
```bash
# Show migration status
php artisan migrate:status

# Show detailed migration information
php artisan migrate:status --pending

# Show SQL that would be executed
php artisan migrate --pretend
```

#### Fresh Migration Commands
```bash
# Drop all tables and re-run migrations
php artisan migrate:fresh

# Fresh migrations with seeding
php artisan migrate:fresh --seed

# Fresh migrations for specific database
php artisan migrate:fresh --database=testing
```

### 5. 🛠️ Database Schema Commands

#### Schema Information
```bash
# Show database information
php artisan schema:dump

# Show specific table schema
php artisan schema:dump --table=users

# Show database schema as SQL
php artisan schema:dump --database=mysql
```

---

## 🎨 Creating Migrations

### 1. 📊 Table Creation Migrations

#### Basic Table Creation
```php
// Migration: create_users_table.php
public function up(): void
{
    Schema::create('users', function (Blueprint $table) {
        $table->id();                              // Primary key
        $table->string('name');                    // VARCHAR(255)
        $table->string('email')->unique();        // VARCHAR(255) with unique index
        $table->timestamp('email_verified_at')->nullable();
        $table->string('password');
        $table->rememberToken();                   // VARCHAR(100) nullable
        $table->timestamps();                      // created_at, updated_at
    });
}

public function down(): void
{
    Schema::dropIfExists('users');
}
```

#### Advanced Table Creation
```php
// Migration: create_posts_table.php
public function up(): void
{
    Schema::create('posts', function (Blueprint $table) {
        $table->id();
        $table->string('title', 200);             // VARCHAR(200)
        $table->string('slug')->unique();
        $table->text('content');
        $table->text('excerpt')->nullable();
        $table->enum('status', ['draft', 'published', 'archived'])->default('draft');
        $table->boolean('featured')->default(false);
        $table->integer('views')->default(0);
        $table->decimal('rating', 3, 2)->default(0.00); // 3 digits, 2 decimal places
        $table->json('meta_data')->nullable();
        
        // Foreign key relationships
        $table->foreignId('user_id')->constrained()->onDelete('cascade');
        $table->foreignId('category_id')->constrained();
        
        // Indexes
        $table->index(['status', 'created_at']);
        $table->index('featured');
        
        $table->timestamps();
        $table->softDeletes();                     // deleted_at column
    });
}
```

### 2. ➕ Adding Columns Migration

#### Command to Create
```bash
php artisan make:migration add_phone_to_users_table --table=users
```

#### Migration Content
```php
// Migration: add_phone_to_users_table.php
public function up(): void
{
    Schema::table('users', function (Blueprint $table) {
        $table->string('phone')->nullable()->after('email');
        $table->date('birth_date')->nullable()->after('phone');
        $table->enum('gender', ['male', 'female', 'other'])->nullable()->after('birth_date');
        $table->text('bio')->nullable()->after('gender');
        
        // Add index
        $table->index('phone');
    });
}

public function down(): void
{
    Schema::table('users', function (Blueprint $table) {
        $table->dropIndex(['phone']);
        $table->dropColumn(['phone', 'birth_date', 'gender', 'bio']);
    });
}
```

### 3. 🔧 Modifying Columns Migration

#### Command to Create
```bash
php artisan make:migration modify_users_table_columns --table=users
```

#### Migration Content
```php
// Migration: modify_users_table_columns.php
public function up(): void
{
    Schema::table('users', function (Blueprint $table) {
        // Modify existing columns
        $table->string('name', 100)->change();          // Change length
        $table->string('email', 150)->nullable(false)->change(); // Make non-nullable
        
        // Rename columns
        $table->renameColumn('birth_date', 'date_of_birth');
        
        // Change column type
        $table->text('bio')->change();                  // VARCHAR to TEXT
    });
}

public function down(): void
{
    Schema::table('users', function (Blueprint $table) {
        $table->string('name', 255)->change();
        $table->string('email', 255)->change();
        $table->renameColumn('date_of_birth', 'birth_date');
        $table->string('bio')->change();
    });
}
```

### 4. ❌ Dropping Columns Migration

#### Command to Create
```bash
php artisan make:migration remove_bio_from_users_table --table=users
```

#### Migration Content
```php
// Migration: remove_bio_from_users_table.php
public function up(): void
{
    Schema::table('users', function (Blueprint $table) {
        // Drop single column
        $table->dropColumn('bio');
        
        // Drop multiple columns
        $table->dropColumn(['phone', 'gender']);
        
        // Drop column with index
        $table->dropIndex(['email']); // Drop index first
        $table->dropColumn('email');
    });
}

public function down(): void
{
    Schema::table('users', function (Blueprint $table) {
        $table->text('bio')->nullable();
        $table->string('phone')->nullable();
        $table->enum('gender', ['male', 'female', 'other'])->nullable();
        $table->string('email')->unique();
    });
}
```

---

## 🗂️ Column Types & Modifiers

### 1. 📊 Numeric Column Types

```php
Schema::create('products', function (Blueprint $table) {
    // Integer types
    $table->id();                           // BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY
    $table->bigIncrements('id');           // Same as id()
    $table->increments('id');              // INT UNSIGNED AUTO_INCREMENT PRIMARY KEY
    $table->smallIncrements('id');         // SMALLINT UNSIGNED AUTO_INCREMENT PRIMARY KEY
    $table->tinyIncrements('id');          // TINYINT UNSIGNED AUTO_INCREMENT PRIMARY KEY
    
    // Standard integers
    $table->bigInteger('big_number');       // BIGINT
    $table->integer('normal_number');       // INT
    $table->mediumInteger('medium_number'); // MEDIUMINT
    $table->smallInteger('small_number');   // SMALLINT
    $table->tinyInteger('tiny_number');     // TINYINT
    
    // Unsigned integers
    $table->unsignedBigInteger('user_id');
    $table->unsignedInteger('count');
    $table->unsignedSmallInteger('priority');
    $table->unsignedTinyInteger('status');
    
    // Decimal types
    $table->decimal('price', 8, 2);         // DECIMAL(8,2)
    $table->double('latitude', 8, 6);       // DOUBLE(8,6)
    $table->float('rating', 8, 2);          // FLOAT(8,2)
});
```

### 2. 🔤 String Column Types

```php
Schema::create('content', function (Blueprint $table) {
    // String types
    $table->string('name');                 // VARCHAR(255)
    $table->string('title', 100);          // VARCHAR(100)
    $table->char('code', 10);              // CHAR(10)
    
    // Text types
    $table->text('description');           // TEXT
    $table->mediumText('content');         // MEDIUMTEXT
    $table->longText('article');           // LONGTEXT
    $table->tinyText('summary');           // TINYTEXT (MySQL)
    
    // Binary types
    $table->binary('data');                // BLOB
    
    // Special string types
    $table->uuid('identifier');            // CHAR(36)
    $table->ulid('identifier');            // CHAR(26) (Laravel 9+)
    $table->ipAddress('visitor_ip');       // VARCHAR(45)
    $table->macAddress('device_mac');      // VARCHAR(17)
    $table->json('metadata');              // JSON
    $table->jsonb('settings');             // JSONB (PostgreSQL)
});
```

### 3. 📅 Date & Time Column Types

```php
Schema::create('events', function (Blueprint $table) {
    // Timestamp types
    $table->timestamp('created_at');        // TIMESTAMP
    $table->timestampTz('created_at');      // TIMESTAMP WITH TIME ZONE
    $table->timestamps();                   // created_at + updated_at
    $table->timestampsTz();                 // Timezone-aware timestamps
    $table->nullableTimestamps();           // Nullable timestamps
    
    // Date types
    $table->date('event_date');             // DATE
    $table->dateTime('start_time');         // DATETIME
    $table->dateTimeTz('start_time');       // DATETIME WITH TIME ZONE
    $table->time('duration');               // TIME
    $table->timeTz('duration');             // TIME WITH TIME ZONE
    $table->year('birth_year');             // YEAR
});
```

### 4. ✅ Boolean & Enum Column Types

```php
Schema::create('settings', function (Blueprint $table) {
    // Boolean
    $table->boolean('is_active');           // TINYINT(1)
    $table->boolean('featured')->default(false);
    
    // Enum
    $table->enum('status', ['pending', 'approved', 'rejected']);
    $table->enum('priority', ['low', 'medium', 'high'])->default('medium');
    
    // Set (MySQL only)
    $table->set('permissions', ['read', 'write', 'delete']);
});
```

### 5. 🔗 Foreign Key Column Types

```php
Schema::create('posts', function (Blueprint $table) {
    // Foreign ID (recommended)
    $table->foreignId('user_id')->constrained();
    $table->foreignId('category_id')->constrained('categories');
    
    // Foreign UUID
    $table->foreignUuid('user_id')->constrained();
    
    // Foreign ULID
    $table->foreignUlid('user_id')->constrained();
    
    // Manual foreign keys
    $table->unsignedBigInteger('author_id');
    $table->foreign('author_id')->references('id')->on('users');
});
```

### 6. 🎨 Column Modifiers

```php
Schema::create('users', function (Blueprint $table) {
    // Nullable
    $table->string('middle_name')->nullable();
    
    // Default values
    $table->boolean('is_active')->default(true);
    $table->integer('views')->default(0);
    $table->timestamp('created_at')->useCurrent();
    $table->timestamp('updated_at')->useCurrentOnUpdate();
    
    // Column positioning
    $table->string('first_name')->after('id');
    $table->string('status')->first();
    
    // Comments
    $table->string('email')->comment('User email address');
    
    // Character set and collation
    $table->string('name')->charset('utf8mb4')->collation('utf8mb4_unicode_ci');
    
    // Virtual/Generated columns
    $table->string('full_name')->virtualAs("CONCAT(first_name, ' ', last_name)");
    $table->integer('total_price')->storedAs('price * quantity');
    
    // Auto-increment starting value
    $table->id()->startingValue(1000);
    
    // Unsigned
    $table->integer('quantity')->unsigned();
    
    // Auto-incrementing
    $table->integer('sequence')->autoIncrement();
});
```

---

## 🔗 Indexes & Constraints

### 1. 📇 Index Types

```php
Schema::create('posts', function (Blueprint $table) {
    $table->id();
    $table->string('title');
    $table->string('slug');
    $table->text('content');
    $table->string('status');
    $table->foreignId('user_id');
    $table->timestamps();
    
    // Primary key (automatic with id())
    // $table->primary('id');
    
    // Unique indexes
    $table->unique('slug');
    $table->unique(['user_id', 'title']); // Composite unique
    
    // Regular indexes
    $table->index('status');
    $table->index(['status', 'created_at']); // Composite index
    $table->index('title', 'posts_title_index'); // Named index
    
    // Full-text indexes (MySQL/PostgreSQL)
    $table->fulltext('content');
    $table->fulltext(['title', 'content']);
    
    // Spatial indexes (MySQL)
    $table->spatialIndex('location');
});
```

### 2. 🔗 Foreign Key Constraints

```php
Schema::create('posts', function (Blueprint $table) {
    $table->id();
    $table->string('title');
    $table->foreignId('user_id');
    $table->foreignId('category_id');
    
    // Simple foreign key
    $table->foreign('user_id')->references('id')->on('users');
    
    // Foreign key with cascade delete
    $table->foreign('user_id')
          ->references('id')
          ->on('users')
          ->onDelete('cascade');
    
    // Foreign key with cascade update
    $table->foreign('category_id')
          ->references('id')
          ->on('categories')
          ->onUpdate('cascade')
          ->onDelete('set null');
    
    // Named foreign key
    $table->foreign('user_id', 'posts_user_foreign')
          ->references('id')
          ->on('users');
});

// Using foreignId with constraints (recommended)
Schema::create('comments', function (Blueprint $table) {
    $table->id();
    $table->text('content');
    
    // Shorthand foreign keys
    $table->foreignId('user_id')->constrained(); // References users.id
    $table->foreignId('post_id')->constrained()->onDelete('cascade');
    $table->foreignId('parent_id')->nullable()->constrained('comments');
});
```

### 3. ❌ Dropping Indexes & Constraints

```php
Schema::table('posts', function (Blueprint $table) {
    // Drop indexes
    $table->dropIndex(['status']);           // Drop index by columns
    $table->dropIndex('posts_status_index'); // Drop index by name
    $table->dropUnique(['slug']);           // Drop unique index
    $table->dropPrimary(['id']);            // Drop primary key
    $table->dropFulltext(['content']);      // Drop fulltext index
    $table->dropSpatialIndex(['location']); // Drop spatial index
    
    // Drop foreign keys
    $table->dropForeign(['user_id']);       // Drop by column
    $table->dropForeign('posts_user_foreign'); // Drop by name
    
    // Drop foreign key and column
    $table->dropConstrainedForeignId('user_id');
});
```

---

## 🚀 Advanced Migration Techniques

### 1. 🔄 Raw SQL in Migrations

```php
public function up(): void
{
    // Execute raw SQL
    DB::statement('CREATE INDEX CONCURRENTLY idx_posts_title ON posts (title)');
    
    // Raw SQL with bindings
    DB::statement('UPDATE users SET status = ? WHERE created_at < ?', ['inactive', now()->subYear()]);
    
    // Multiple statements
    DB::unprepared('
        CREATE VIEW active_users AS 
        SELECT * FROM users WHERE status = "active";
        
        CREATE TRIGGER update_modified_time 
        BEFORE UPDATE ON users 
        FOR EACH ROW 
        SET NEW.updated_at = NOW();
    ');
}

public function down(): void
{
    DB::statement('DROP INDEX CONCURRENTLY IF EXISTS idx_posts_title');
    DB::unprepared('
        DROP VIEW IF EXISTS active_users;
        DROP TRIGGER IF EXISTS update_modified_time;
    ');
}
```

### 2. 📊 Data Seeding in Migrations

```php
public function up(): void
{
    Schema::create('roles', function (Blueprint $table) {
        $table->id();
        $table->string('name')->unique();
        $table->string('display_name');
        $table->timestamps();
    });
    
    // Seed initial data
    DB::table('roles')->insert([
        ['name' => 'admin', 'display_name' => 'Administrator', 'created_at' => now(), 'updated_at' => now()],
        ['name' => 'editor', 'display_name' => 'Editor', 'created_at' => now(), 'updated_at' => now()],
        ['name' => 'viewer', 'display_name' => 'Viewer', 'created_at' => now(), 'updated_at' => now()],
    ]);
}
```

### 3. 🔧 Conditional Migrations

```php
public function up(): void
{
    // Check if column exists before adding
    if (!Schema::hasColumn('users', 'email_verified_at')) {
        Schema::table('users', function (Blueprint $table) {
            $table->timestamp('email_verified_at')->nullable()->after('email');
        });
    }
    
    // Environment-specific migrations
    if (app()->environment('production')) {
        Schema::table('users', function (Blueprint $table) {
            $table->index('email'); // Add index only in production
        });
    }
    
    // Database-specific migrations
    if (DB::getDriverName() === 'mysql') {
        DB::statement('ALTER TABLE posts ADD FULLTEXT(title, content)');
    }
}
```

### 4. 🎭 Table Copying & Renaming

```php
public function up(): void
{
    // Copy table structure
    Schema::create('posts_backup', function (Blueprint $table) {
        $table->id();
        $table->string('title');
        $table->text('content');
        $table->timestamps();
    });
    
    // Copy data
    DB::statement('INSERT INTO posts_backup SELECT * FROM posts');
    
    // Rename table
    Schema::rename('old_posts', 'posts_archive');
    
    // Drop and recreate
    Schema::drop('temporary_table');
}

public function down(): void
{
    Schema::rename('posts_archive', 'old_posts');
    Schema::dropIfExists('posts_backup');
}
```

### 5. 🔀 Complex Data Transformations

```php
public function up(): void
{
    // Add new column
    Schema::table('users', function (Blueprint $table) {
        $table->json('preferences')->nullable()->after('email');
    });
    
    // Transform existing data
    $users = DB::table('users')->whereNotNull('settings')->get();
    foreach ($users as $user) {
        $preferences = [
            'theme' => $user->theme ?? 'light',
            'language' => $user->language ?? 'en',
            'notifications' => $user->email_notifications ?? true
        ];
        
        DB::table('users')
            ->where('id', $user->id)
            ->update(['preferences' => json_encode($preferences)]);
    }
    
    // Drop old columns
    Schema::table('users', function (Blueprint $table) {
        $table->dropColumn(['theme', 'language', 'email_notifications']);
    });
}
```

---

## ✅ Best Practices

### 1. 🎯 Migration Naming Conventions

```bash
# Good naming patterns
create_table_name_table.php           # Creating new table
add_column_to_table_table.php         # Adding columns
remove_column_from_table_table.php    # Removing columns
modify_table_table_columns.php        # Modifying columns
create_index_on_table_table.php       # Adding indexes
add_foreign_key_to_table_table.php    # Adding foreign keys

# Examples
create_users_table.php
add_email_verification_to_users_table.php
remove_unused_columns_from_posts_table.php
create_index_on_posts_status.php
```

### 2. 🔄 Always Write Rollback Logic

```php
// ✅ Good - Complete rollback
public function up(): void
{
    Schema::table('users', function (Blueprint $table) {
        $table->string('phone')->nullable()->after('email');
        $table->index('phone');
    });
}

public function down(): void
{
    Schema::table('users', function (Blueprint $table) {
        $table->dropIndex(['phone']);
        $table->dropColumn('phone');
    });
}

// ❌ Bad - Incomplete rollback
public function down(): void
{
    Schema::table('users', function (Blueprint $table) {
        $table->dropColumn('phone'); // Missing index drop!
    });
}
```

### 3. 🎨 Use Descriptive Column Names

```php
// ✅ Good column names
$table->timestamp('email_verified_at');
$table->timestamp('last_login_at');
$table->boolean('is_active');
$table->boolean('has_newsletter_subscription');
$table->integer('login_attempts_count');

// ❌ Avoid unclear names
$table->timestamp('verified');
$table->timestamp('login');
$table->boolean('active');
$table->boolean('newsletter');
$table->integer('attempts');
```

### 4. 🔗 Consistent Foreign Key Patterns

```php
// ✅ Good foreign key pattern
Schema::create('posts', function (Blueprint $table) {
    $table->id();
    $table->string('title');
    $table->text('content');
    
    // Consistent naming: table_id
    $table->foreignId('user_id')->constrained()->onDelete('cascade');
    $table->foreignId('category_id')->constrained();
    
    $table->timestamps();
});

// ✅ Alternative consistent pattern
Schema::create('comments', function (Blueprint $table) {
    $table->id();
    $table->text('content');
    
    // Explicit table references
    $table->foreignId('author_id')->constrained('users');
    $table->foreignId('post_id')->constrained('posts')->onDelete('cascade');
    
    $table->timestamps();
});
```

### 5. 📊 Performance Considerations

```php
Schema::create('large_table', function (Blueprint $table) {
    $table->id();
    $table->string('title');
    $table->text('content');
    $table->string('status');
    $table->timestamp('published_at')->nullable();
    $table->foreignId('user_id');
    $table->timestamps();
    
    // Add indexes for frequently queried columns
    $table->index('status');                    // Single column index
    $table->index(['status', 'published_at']);  // Composite index
    $table->index('user_id');                   // Foreign key index
    $table->index('created_at');                // Timestamp queries
    
    // Foreign key constraints
    $table->foreign('user_id')->references('id')->on('users');
});
```

---

## 🚨 Troubleshooting

### 1. ⚠️ Common Migration Errors

#### Error: Column Already Exists
```php
// ❌ Problem
Schema::table('users', function (Blueprint $table) {
    $table->string('email'); // Column already exists!
});

// ✅ Solution
Schema::table('users', function (Blueprint $table) {
    if (!Schema::hasColumn('users', 'email')) {
        $table->string('email');
    }
});
```

#### Error: Foreign Key Constraint Fails
```php
// ❌ Problem - Referenced table/column doesn't exist
$table->foreignId('category_id')->constrained(); // categories table doesn't exist

// ✅ Solution - Create referenced table first or check existence
if (Schema::hasTable('categories')) {
    $table->foreignId('category_id')->constrained();
} else {
    $table->unsignedBigInteger('category_id'); // Temporary, add constraint later
}
```

#### Error: Cannot Drop Column with Index
```php
// ❌ Problem
Schema::table('users', function (Blueprint $table) {
    $table->dropColumn('email'); // Has unique index!
});

// ✅ Solution - Drop index first
Schema::table('users', function (Blueprint $table) {
    $table->dropUnique(['email']);
    $table->dropColumn('email');
});
```

### 2. 🔧 Migration Recovery Techniques

#### Rollback Failed Migration
```bash
# Check migration status
php artisan migrate:status

# Rollback last batch
php artisan migrate:rollback

# Rollback specific steps
php artisan migrate:rollback --step=2

# Force rollback (if needed)
php artisan migrate:rollback --force
```

#### Reset Database
```bash
# Complete reset
php artisan migrate:fresh

# Reset with seeding
php artisan migrate:fresh --seed

# Reset specific database
php artisan migrate:fresh --database=testing
```

#### Manual Migration Table Fix
```sql
-- Check migrations table
SELECT * FROM migrations ORDER BY batch DESC;

-- Remove failed migration record
DELETE FROM migrations WHERE migration = '2024_08_16_143022_create_posts_table';

-- Reset batch numbers if needed
UPDATE migrations SET batch = 1 WHERE batch > 1;
```

### 3. 🛠️ Development Tips

#### Test Migrations Locally
```bash
# Test migration without running
php artisan migrate --pretend

# Run on testing database
php artisan migrate --database=testing

# Create and test rollback
php artisan migrate
php artisan migrate:rollback
php artisan migrate # Should work without errors
```

#### Backup Before Major Changes
```bash
# MySQL backup
mysqldump -u username -p database_name > backup.sql

# PostgreSQL backup
pg_dump database_name > backup.sql

# SQLite backup
cp database/database.sqlite database/backup.sqlite
```

---

## 🎯 Quick Reference Commands

### Migration Commands Cheat Sheet

```bash
# Creation Commands
php artisan make:migration create_table_name_table
php artisan make:migration add_column_to_table_table --table=table_name
php artisan make:migration create_table_name_table --create=table_name

# Execution Commands
php artisan migrate                    # Run pending migrations
php artisan migrate --force           # Force run in production
php artisan migrate --pretend         # Show SQL without executing
php artisan migrate --step            # Run one step at a time
php artisan migrate --path=path       # Run specific migration file

# Rollback Commands
php artisan migrate:rollback           # Rollback last batch
php artisan migrate:rollback --step=3  # Rollback 3 steps
php artisan migrate:reset             # Rollback all migrations
php artisan migrate:refresh           # Rollback and re-run all
php artisan migrate:fresh             # Drop all tables and re-run

# Status Commands
php artisan migrate:status            # Show migration status
php artisan schema:dump               # Show database schema
```

### Column Types Quick Reference

```php
// Primary Keys
$table->id();                         // Auto-increment BIGINT primary key
$table->uuid('id')->primary();        // UUID primary key
$table->ulid('id')->primary();        // ULID primary key

// Strings
$table->string('name');               // VARCHAR(255)
$table->string('name', 100);          // VARCHAR(100)
$table->text('description');          // TEXT
$table->char('code', 10);             // CHAR(10)

// Numbers
$table->integer('count');             // INT
$table->bigInteger('big_count');      // BIGINT
$table->decimal('price', 8, 2);       // DECIMAL(8,2)
$table->boolean('active');            // TINYINT(1)

// Dates
$table->timestamps();                 // created_at, updated_at
$table->date('birth_date');           // DATE
$table->datetime('event_time');       // DATETIME
$table->timestamp('verified_at');     // TIMESTAMP

// Relationships
$table->foreignId('user_id')->constrained();
$table->morphs('taggable');           // taggable_type, taggable_id

// Special
$table->json('metadata');             // JSON
$table->enum('status', ['active', 'inactive']);
$table->ipAddress('ip');              // IP address
$table->macAddress('mac');            // MAC address
```

---

## 🎨 Advanced Migration Examples

### 1. 🏪 E-commerce Database Structure

#### Products Table Migration
```php
// Migration: create_products_table.php
public function up(): void
{
    Schema::create('products', function (Blueprint $table) {
        $table->id();
        $table->string('name');
        $table->string('slug')->unique();
        $table->text('description');
        $table->text('short_description')->nullable();
        $table->string('sku')->unique();
        $table->decimal('price', 10, 2);
        $table->decimal('sale_price', 10, 2)->nullable();
        $table->integer('stock_quantity')->default(0);
        $table->boolean('manage_stock')->default(true);
        $table->boolean('in_stock')->default(true);
        $table->decimal('weight', 8, 2)->nullable();
        $table->json('dimensions')->nullable(); // length, width, height
        $table->string('status')->default('draft'); // draft, published, private
        $table->boolean('featured')->default(false);
        $table->json('gallery')->nullable(); // Array of image URLs
        $table->string('featured_image')->nullable();
        $table->json('attributes')->nullable(); // Color, size, etc.
        $table->text('meta_title')->nullable();
        $table->text('meta_description')->nullable();
        $table->integer('views')->default(0);
        $table->decimal('average_rating', 3, 2)->default(0);
        $table->integer('review_count')->default(0);
        
        // Foreign keys
        $table->foreignId('category_id')->constrained()->onDelete('cascade');
        $table->foreignId('brand_id')->nullable()->constrained()->onDelete('set null');
        
        // Indexes
        $table->index(['status', 'featured']);
        $table->index('category_id');
        $table->index('price');
        $table->index('created_at');
        $table->fulltext(['name', 'description']);
        
        $table->timestamps();
        $table->softDeletes();
    });
}

public function down(): void
{
    Schema::dropIfExists('products');
}
```

#### Orders Table Migration
```php
// Migration: create_orders_table.php
public function up(): void
{
    Schema::create('orders', function (Blueprint $table) {
        $table->id();
        $table->string('order_number')->unique();
        $table->enum('status', [
            'pending',
            'processing', 
            'shipped',
            'delivered',
            'cancelled',
            'refunded'
        ])->default('pending');
        $table->decimal('subtotal', 10, 2);
        $table->decimal('tax_amount', 10, 2)->default(0);
        $table->decimal('shipping_amount', 10, 2)->default(0);
        $table->decimal('discount_amount', 10, 2)->default(0);
        $table->decimal('total_amount', 10, 2);
        $table->string('currency', 3)->default('USD');
        $table->json('billing_address');
        $table->json('shipping_address');
        $table->string('payment_method')->nullable();
        $table->string('payment_status')->default('pending');
        $table->timestamp('shipped_at')->nullable();
        $table->timestamp('delivered_at')->nullable();
        $table->text('notes')->nullable();
        
        // Customer relationship
        $table->foreignId('user_id')->constrained()->onDelete('cascade');
        
        // Coupon relationship (optional)
        $table->foreignId('coupon_id')->nullable()->constrained()->onDelete('set null');
        
        // Indexes
        $table->index(['user_id', 'status']);
        $table->index('order_number');
        $table->index('status');
        $table->index('created_at');
        
        $table->timestamps();
    });
}
```

### 2. 📚 Blog System with Polymorphic Relationships

#### Posts Table with Polymorphic Comments
```php
// Migration: create_posts_table.php
public function up(): void
{
    Schema::create('posts', function (Blueprint $table) {
        $table->id();
        $table->string('title');
        $table->string('slug')->unique();
        $table->text('excerpt')->nullable();
        $table->longText('content');
        $table->string('featured_image')->nullable();
        $table->json('meta_data')->nullable();
        $table->enum('status', ['draft', 'published', 'scheduled', 'private'])->default('draft');
        $table->timestamp('published_at')->nullable();
        $table->integer('views')->default(0);
        $table->integer('likes_count')->default(0);
        $table->integer('comments_count')->default(0);
        $table->boolean('allow_comments')->default(true);
        $table->boolean('featured')->default(false);
        
        // Relationships
        $table->foreignId('author_id')->constrained('users')->onDelete('cascade');
        $table->foreignId('category_id')->constrained()->onDelete('cascade');
        
        // SEO fields
        $table->string('seo_title')->nullable();
        $table->text('seo_description')->nullable();
        $table->json('seo_keywords')->nullable();
        
        // Indexes
        $table->index(['status', 'published_at']);
        $table->index(['author_id', 'status']);
        $table->index('featured');
        $table->fulltext(['title', 'content']);
        
        $table->timestamps();
        $table->softDeletes();
    });
}
```

#### Polymorphic Comments Table
```php
// Migration: create_comments_table.php
public function up(): void
{
    Schema::create('comments', function (Blueprint $table) {
        $table->id();
        $table->text('content');
        $table->enum('status', ['pending', 'approved', 'rejected'])->default('pending');
        $table->integer('likes_count')->default(0);
        $table->integer('replies_count')->default(0);
        
        // Polymorphic relationship
        $table->morphs('commentable'); // commentable_type, commentable_id
        
        // User relationship
        $table->foreignId('user_id')->constrained()->onDelete('cascade');
        
        // Self-referencing for replies
        $table->foreignId('parent_id')->nullable()->constrained('comments')->onDelete('cascade');
        
        // Indexes
        $table->index(['commentable_type', 'commentable_id']);
        $table->index(['user_id', 'status']);
        $table->index('parent_id');
        
        $table->timestamps();
        $table->softDeletes();
    });
}
```

### 3. 👥 User Management with Roles & Permissions

#### Roles Table
```php
// Migration: create_roles_table.php
public function up(): void
{
    Schema::create('roles', function (Blueprint $table) {
        $table->id();
        $table->string('name')->unique();
        $table->string('display_name');
        $table->text('description')->nullable();
        $table->boolean('is_active')->default(true);
        $table->integer('level')->default(1); // Hierarchy level
        $table->json('permissions')->nullable();
        $table->timestamps();
        
        $table->index('name');
        $table->index('level');
    });
    
    // Seed default roles
    DB::table('roles')->insert([
        [
            'name' => 'super_admin',
            'display_name' => 'Super Administrator',
            'description' => 'Full system access',
            'level' => 10,
            'created_at' => now(),
            'updated_at' => now()
        ],
        [
            'name' => 'admin',
            'display_name' => 'Administrator',
            'description' => 'Administrative access',
            'level' => 8,
            'created_at' => now(),
            'updated_at' => now()
        ],
        [
            'name' => 'editor',
            'display_name' => 'Editor',
            'description' => 'Content management access',
            'level' => 5,
            'created_at' => now(),
            'updated_at' => now()
        ],
        [
            'name' => 'user',
            'display_name' => 'Regular User',
            'description' => 'Basic user access',
            'level' => 1,
            'created_at' => now(),
            'updated_at' => now()
        ]
    ]);
}
```

#### User Roles Pivot Table
```php
// Migration: create_user_roles_table.php
public function up(): void
{
    Schema::create('user_roles', function (Blueprint $table) {
        $table->id();
        $table->foreignId('user_id')->constrained()->onDelete('cascade');
        $table->foreignId('role_id')->constrained()->onDelete('cascade');
        $table->timestamp('assigned_at')->useCurrent();
        $table->foreignId('assigned_by')->nullable()->constrained('users')->onDelete('set null');
        $table->timestamp('expires_at')->nullable();
        $table->boolean('is_active')->default(true);
        
        // Unique constraint
        $table->unique(['user_id', 'role_id']);
        
        // Indexes
        $table->index('user_id');
        $table->index('role_id');
        $table->index('assigned_at');
        
        $table->timestamps();
    });
}
```

### 4. 📊 Analytics & Logging Tables

#### Page Views Tracking
```php
// Migration: create_page_views_table.php
public function up(): void
{
    Schema::create('page_views', function (Blueprint $table) {
        $table->id();
        $table->string('url', 500);
        $table->string('page_title')->nullable();
        $table->ipAddress('ip_address');
        $table->text('user_agent')->nullable();
        $table->string('referrer', 500)->nullable();
        $table->string('utm_source')->nullable();
        $table->string('utm_medium')->nullable();
        $table->string('utm_campaign')->nullable();
        $table->string('device_type')->nullable(); // desktop, mobile, tablet
        $table->string('browser')->nullable();
        $table->string('platform')->nullable();
        $table->string('country_code', 2)->nullable();
        $table->string('city')->nullable();
        $table->decimal('latitude', 10, 8)->nullable();
        $table->decimal('longitude', 11, 8)->nullable();
        $table->integer('session_duration')->nullable(); // in seconds
        $table->timestamp('viewed_at');
        
        // User relationship (nullable for anonymous users)
        $table->foreignId('user_id')->nullable()->constrained()->onDelete('set null');
        
        // Indexes for analytics queries
        $table->index('viewed_at');
        $table->index(['url', 'viewed_at']);
        $table->index('user_id');
        $table->index('ip_address');
        $table->index(['utm_source', 'utm_medium', 'utm_campaign']);
        
        // Partition by date (for large datasets)
        // $table->index(DB::raw('DATE(viewed_at)'));
    });
}
```

#### Activity Log Table
```php
// Migration: create_activity_logs_table.php
public function up(): void
{
    Schema::create('activity_logs', function (Blueprint $table) {
        $table->id();
        $table->string('action'); // created, updated, deleted, viewed, etc.
        $table->string('description')->nullable();
        $table->json('properties')->nullable(); // Old and new values
        $table->string('batch_uuid')->nullable(); // Group related activities
        
        // Subject (what was acted upon) - polymorphic
        $table->nullableMorphs('subject');
        
        // Causer (who performed the action) - polymorphic
        $table->nullableMorphs('causer');
        
        // Additional context
        $table->ipAddress('ip_address')->nullable();
        $table->text('user_agent')->nullable();
        $table->json('context')->nullable(); // Additional metadata
        
        $table->timestamps();
        
        // Indexes
        $table->index(['subject_type', 'subject_id']);
        $table->index(['causer_type', 'causer_id']);
        $table->index('action');
        $table->index('created_at');
        $table->index('batch_uuid');
    });
}
```

---

## 🔄 Complex Migration Scenarios

### 1. 🔀 Data Migration Between Tables

```php
// Migration: migrate_user_profiles_to_users_table.php
public function up(): void
{
    // Add new columns to users table
    Schema::table('users', function (Blueprint $table) {
        $table->string('first_name')->nullable()->after('name');
        $table->string('last_name')->nullable()->after('first_name');
        $table->date('birth_date')->nullable()->after('last_name');
        $table->string('phone')->nullable()->after('birth_date');
        $table->text('bio')->nullable()->after('phone');
        $table->string('avatar')->nullable()->after('bio');
        $table->json('social_links')->nullable()->after('avatar');
    });
    
    // Migrate data from user_profiles table
    $profiles = DB::table('user_profiles')->get();
    
    foreach ($profiles as $profile) {
        DB::table('users')
            ->where('id', $profile->user_id)
            ->update([
                'first_name' => $profile->first_name,
                'last_name' => $profile->last_name,
                'birth_date' => $profile->birth_date,
                'phone' => $profile->phone,
                'bio' => $profile->bio,
                'avatar' => $profile->avatar_url,
                'social_links' => json_encode([
                    'facebook' => $profile->facebook_url,
                    'twitter' => $profile->twitter_url,
                    'linkedin' => $profile->linkedin_url,
                ]),
                'updated_at' => now()
            ]);
    }
    
    // Drop the old table (be careful!)
    // Schema::dropIfExists('user_profiles');
}

public function down(): void
{
    // Recreate user_profiles table
    Schema::create('user_profiles', function (Blueprint $table) {
        $table->id();
        $table->foreignId('user_id')->constrained()->onDelete('cascade');
        $table->string('first_name')->nullable();
        $table->string('last_name')->nullable();
        $table->date('birth_date')->nullable();
        $table->string('phone')->nullable();
        $table->text('bio')->nullable();
        $table->string('avatar_url')->nullable();
        $table->string('facebook_url')->nullable();
        $table->string('twitter_url')->nullable();
        $table->string('linkedin_url')->nullable();
        $table->timestamps();
    });
    
    // Migrate data back
    $users = DB::table('users')
        ->whereNotNull('first_name')
        ->orWhereNotNull('last_name')
        ->orWhereNotNull('birth_date')
        ->get();
    
    foreach ($users as $user) {
        $socialLinks = json_decode($user->social_links, true) ?? [];
        
        DB::table('user_profiles')->insert([
            'user_id' => $user->id,
            'first_name' => $user->first_name,
            'last_name' => $user->last_name,
            'birth_date' => $user->birth_date,
            'phone' => $user->phone,
            'bio' => $user->bio,
            'avatar_url' => $user->avatar,
            'facebook_url' => $socialLinks['facebook'] ?? null,
            'twitter_url' => $socialLinks['twitter'] ?? null,
            'linkedin_url' => $socialLinks['linkedin'] ?? null,
            'created_at' => $user->created_at,
            'updated_at' => now()
        ]);
    }
    
    // Remove columns from users table
    Schema::table('users', function (Blueprint $table) {
        $table->dropColumn([
            'first_name', 'last_name', 'birth_date', 
            'phone', 'bio', 'avatar', 'social_links'
        ]);
    });
}
```

### 2. 🔧 Database Structure Optimization

```php
// Migration: optimize_posts_table_for_performance.php
public function up(): void
{
    // Add new optimized columns
    Schema::table('posts', function (Blueprint $table) {
        $table->string('status_cached')->nullable()->after('status');
        $table->integer('word_count')->default(0)->after('content');
        $table->integer('reading_time')->default(0)->after('word_count'); // in minutes
        $table->json('search_vector')->nullable()->after('reading_time');
    });
    
    // Update existing data
    $posts = DB::table('posts')->select('id', 'content', 'status')->get();
    
    foreach ($posts as $post) {
        $wordCount = str_word_count(strip_tags($post->content));
        $readingTime = max(1, ceil($wordCount / 200)); // 200 words per minute
        
        DB::table('posts')
            ->where('id', $post->id)
            ->update([
                'status_cached' => $post->status,
                'word_count' => $wordCount,
                'reading_time' => $readingTime,
                'search_vector' => json_encode([
                    'content' => strtolower(strip_tags($post->content)),
                    'keywords' => $this->extractKeywords($post->content)
                ])
            ]);
    }
    
    // Add new indexes for better performance
    Schema::table('posts', function (Blueprint $table) {
        $table->index(['status_cached', 'published_at']);
        $table->index('word_count');
        $table->index('reading_time');
    });
}

private function extractKeywords($content)
{
    // Simple keyword extraction logic
    $words = str_word_count(strtolower(strip_tags($content)), 1);
    $commonWords = ['the', 'a', 'an', 'and', 'or', 'but', 'in', 'on', 'at', 'to', 'for', 'of', 'with', 'by'];
    $keywords = array_diff($words, $commonWords);
    
    return array_slice(array_unique($keywords), 0, 20); // Top 20 keywords
}

public function down(): void
{
    Schema::table('posts', function (Blueprint $table) {
        $table->dropIndex(['status_cached', 'published_at']);
        $table->dropIndex(['word_count']);
        $table->dropIndex(['reading_time']);
        
        $table->dropColumn(['status_cached', 'word_count', 'reading_time', 'search_vector']);
    });
}
```

---

## 🎯 Migration Testing Strategies

### 1. 🧪 Testing Migration Scripts

```php
// tests/Feature/MigrationTest.php
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class MigrationTest extends TestCase
{
    use RefreshDatabase;
    
    /** @test */
    public function it_can_run_create_posts_migration()
    {
        // Test migration runs without errors
        $this->artisan('migrate', ['--path' => '/database/migrations/2024_08_16_create_posts_table.php'])
             ->assertExitCode(0);
        
        // Test table was created with correct structure
        $this->assertTrue(Schema::hasTable('posts'));
        $this->assertTrue(Schema::hasColumn('posts', 'title'));
        $this->assertTrue(Schema::hasColumn('posts', 'content'));
        $this->assertTrue(Schema::hasColumn('posts', 'user_id'));
        
        // Test indexes were created
        $indexes = DB::select("SHOW INDEX FROM posts");
        $indexNames = collect($indexes)->pluck('Key_name')->toArray();
        
        $this->assertContains('posts_user_id_foreign', $indexNames);
        $this->assertContains('posts_status_index', $indexNames);
    }
    
    /** @test */
    public function it_can_rollback_posts_migration()
    {
        // Run migration
        $this->artisan('migrate', ['--path' => '/database/migrations/2024_08_16_create_posts_table.php']);
        
        // Rollback migration
        $this->artisan('migrate:rollback', ['--step' => 1])
             ->assertExitCode(0);
        
        // Test table was dropped
        $this->assertFalse(Schema::hasTable('posts'));
    }
    
    /** @test */
    public function it_preserves_data_during_column_migration()
    {
        // Create test data
        $user = User::factory()->create();
        $post = Post::factory()->create(['user_id' => $user->id, 'title' => 'Test Post']);
        
        // Run migration that adds columns
        $this->artisan('migrate', ['--path' => '/database/migrations/2024_08_16_add_slug_to_posts_table.php']);
        
        // Verify data is preserved
        $post->refresh();
        $this->assertEquals('Test Post', $post->title);
        $this->assertTrue(Schema::hasColumn('posts', 'slug'));
    }
}
```

### 2. 🔄 Performance Testing

```php
// tests/Performance/MigrationPerformanceTest.php
class MigrationPerformanceTest extends TestCase
{
    /** @test */
    public function migration_completes_within_reasonable_time()
    {
        // Create large dataset
        User::factory()->count(10000)->create();
        
        $startTime = microtime(true);
        
        // Run migration
        $this->artisan('migrate', ['--path' => '/database/migrations/2024_08_16_add_index_to_users_table.php']);
        
        $endTime = microtime(true);
        $executionTime = $endTime - $startTime;
        
        // Assert migration completes within 30 seconds
        $this->assertLessThan(30, $executionTime, 'Migration took too long: ' . $executionTime . ' seconds');
    }
}
```

---

This completes the comprehensive Laravel Migrations guide. The document now covers:

✅ **Complete migration concepts and workflow**
✅ **All migration commands with examples**
✅ **Detailed column types and modifiers**
✅ **Advanced techniques and real-world examples**
✅ **Complex scenarios like data migration**
✅ **Performance optimization strategies**
✅ **Testing approaches for migrations**
✅ **Troubleshooting common issues**
✅ **Best practices and conventions**

The guide provides everything needed to master Laravel migrations from basic table creation to complex database transformations!