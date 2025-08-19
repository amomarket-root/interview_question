# 🗄️ MySQL Interview Questions & Answers Guide

*A comprehensive collection of 50 most frequently asked MySQL interview questions with detailed answers and practical examples.*

---

## 📚 Table of Contents

- [Basic MySQL Questions](#-basic-mysql-questions)
- [SQL Query Questions](#-sql-query-questions)
- [Database Design Questions](#-database-design-questions)
- [Performance & Optimization](#-performance--optimization)
- [Advanced MySQL Topics](#-advanced-mysql-topics)

---

## 🔰 Basic MySQL Questions

### 1. What is MySQL and what are its key features?

**Answer:** MySQL is an open-source relational database management system (RDBMS) that uses Structured Query Language (SQL). 

**Key Features:**
- Cross-platform compatibility
- High performance and scalability
- Strong data security
- ACID compliance
- Support for multiple storage engines
- Replication and clustering capabilities

**Example:**
```sql
-- Basic MySQL connection and database creation
CREATE DATABASE company_db;
USE company_db;
SHOW DATABASES;
```

### 2. What are the different data types in MySQL?

**Answer:** MySQL supports various data types categorized into:

**Numeric Types:**
- INT, BIGINT, SMALLINT, TINYINT
- DECIMAL, FLOAT, DOUBLE

**String Types:**
- VARCHAR, CHAR, TEXT, BLOB

**Date/Time Types:**
- DATE, TIME, DATETIME, TIMESTAMP, YEAR

**Example:**
```sql
CREATE TABLE employee (
    id INT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    salary DECIMAL(10,2),
    hire_date DATE,
    is_active BOOLEAN DEFAULT TRUE
);
```

### 3. What is the difference between CHAR and VARCHAR?

**Answer:**
- **CHAR:** Fixed-length string (0-255 characters), pads with spaces
- **VARCHAR:** Variable-length string (0-65,535 characters), no padding

**Example:**
```sql
CREATE TABLE comparison (
    fixed_char CHAR(10),        -- Always uses 10 bytes
    variable_char VARCHAR(10)   -- Uses 1-10 bytes + 1 length byte
);

INSERT INTO comparison VALUES ('Hello', 'Hello');
-- CHAR stores: 'Hello     ' (padded with spaces)
-- VARCHAR stores: 'Hello' (no padding)
```

### 4. What are MySQL storage engines?

**Answer:** Storage engines determine how MySQL stores and retrieves data.

**Common Storage Engines:**
- **InnoDB:** Default, ACID compliant, foreign keys, row-level locking
- **MyISAM:** Fast reads, table-level locking, no foreign keys
- **MEMORY:** Data stored in RAM
- **ARCHIVE:** Compressed storage for archival data

**Example:**
```sql
-- Create table with specific storage engine
CREATE TABLE products (
    id INT PRIMARY KEY,
    name VARCHAR(100)
) ENGINE=InnoDB;

-- Check storage engine
SHOW CREATE TABLE products;
```

### 5. What is a Primary Key?

**Answer:** A primary key uniquely identifies each record in a table. It cannot be NULL and must be unique.

**Example:**
```sql
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    email VARCHAR(100) UNIQUE NOT NULL,
    name VARCHAR(100)
);

-- Composite primary key
CREATE TABLE order_items (
    order_id INT,
    product_id INT,
    quantity INT,
    PRIMARY KEY (order_id, product_id)
);
```

---

## 🔍 SQL Query Questions

### 6. What is the difference between INNER JOIN, LEFT JOIN, RIGHT JOIN, and FULL JOIN?

**Answer:**
- **INNER JOIN:** Returns matching records from both tables
- **LEFT JOIN:** Returns all records from left table + matching from right
- **RIGHT JOIN:** Returns all records from right table + matching from left
- **FULL JOIN:** Returns all records from both tables

**Example:**
```sql
-- Sample tables
CREATE TABLE departments (id INT, name VARCHAR(50));
CREATE TABLE employees (id INT, name VARCHAR(50), dept_id INT);

INSERT INTO departments VALUES (1, 'HR'), (2, 'IT'), (3, 'Finance');
INSERT INTO employees VALUES (1, 'John', 1), (2, 'Jane', 2), (3, 'Bob', NULL);

-- INNER JOIN
SELECT e.name, d.name 
FROM employees e 
INNER JOIN departments d ON e.dept_id = d.id;

-- LEFT JOIN
SELECT e.name, d.name 
FROM employees e 
LEFT JOIN departments d ON e.dept_id = d.id;
```

### 7. What is the difference between WHERE and HAVING clauses?

**Answer:**
- **WHERE:** Filters individual rows before grouping
- **HAVING:** Filters groups after GROUP BY operation

**Example:**
```sql
CREATE TABLE sales (
    product VARCHAR(50),
    amount DECIMAL(10,2),
    sale_date DATE
);

-- WHERE clause - filters before grouping
SELECT product, SUM(amount)
FROM sales
WHERE sale_date >= '2024-01-01'
GROUP BY product;

-- HAVING clause - filters after grouping
SELECT product, SUM(amount) as total
FROM sales
GROUP BY product
HAVING total > 1000;
```

### 8. What are aggregate functions in MySQL?

**Answer:** Functions that perform calculations on multiple rows and return a single result.

**Common Functions:** COUNT(), SUM(), AVG(), MIN(), MAX()

**Example:**
```sql
CREATE TABLE orders (
    id INT,
    customer_id INT,
    amount DECIMAL(10,2),
    order_date DATE
);

-- Aggregate function examples
SELECT 
    COUNT(*) as total_orders,
    SUM(amount) as total_revenue,
    AVG(amount) as avg_order_value,
    MIN(amount) as min_order,
    MAX(amount) as max_order
FROM orders;

-- With GROUP BY
SELECT 
    customer_id,
    COUNT(*) as order_count,
    AVG(amount) as avg_amount
FROM orders
GROUP BY customer_id;
```

### 9. What is a subquery? Give examples.

**Answer:** A query nested inside another query.

**Types:**
- Scalar subquery (returns single value)
- Row subquery (returns single row)
- Table subquery (returns multiple rows)

**Example:**
```sql
-- Scalar subquery
SELECT name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);

-- Row subquery with EXISTS
SELECT customer_name
FROM customers c
WHERE EXISTS (
    SELECT 1 FROM orders o 
    WHERE o.customer_id = c.id 
    AND o.order_date >= '2024-01-01'
);

-- Table subquery with IN
SELECT name
FROM products
WHERE category_id IN (
    SELECT id FROM categories 
    WHERE name IN ('Electronics', 'Books')
);
```

### 10. How do you handle NULL values in MySQL?

**Answer:** Use IS NULL, IS NOT NULL, IFNULL(), COALESCE(), and NULLIF() functions.

**Example:**
```sql
-- Check for NULL values
SELECT * FROM employees WHERE manager_id IS NULL;
SELECT * FROM employees WHERE manager_id IS NOT NULL;

-- Handle NULL values
SELECT 
    name,
    IFNULL(phone, 'No Phone') as phone_display,
    COALESCE(email, phone, 'No Contact') as contact_info
FROM employees;

-- Replace NULL with specific value
UPDATE employees 
SET department = IFNULL(department, 'Unassigned')
WHERE department IS NULL;
```

---

## 🏗️ Database Design Questions

### 11. What is database normalization?

**Answer:** Process of organizing data to reduce redundancy and improve data integrity.

**Normal Forms:**
- **1NF:** Atomic values, unique rows
- **2NF:** 1NF + no partial dependencies
- **3NF:** 2NF + no transitive dependencies

**Example:**
```sql
-- Before normalization (violates 1NF)
CREATE TABLE students_bad (
    id INT,
    name VARCHAR(100),
    subjects VARCHAR(200)  -- Multiple values in one field
);

-- After normalization
CREATE TABLE students (
    id INT PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE subjects (
    id INT PRIMARY KEY,
    subject_name VARCHAR(100)
);

CREATE TABLE student_subjects (
    student_id INT,
    subject_id INT,
    PRIMARY KEY (student_id, subject_id),
    FOREIGN KEY (student_id) REFERENCES students(id),
    FOREIGN KEY (subject_id) REFERENCES subjects(id)
);
```

### 12. What is a Foreign Key?

**Answer:** A field that creates a link between two tables and ensures referential integrity.

**Example:**
```sql
CREATE TABLE categories (
    id INT PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE products (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    category_id INT,
    FOREIGN KEY (category_id) REFERENCES categories(id)
        ON DELETE CASCADE
        ON UPDATE CASCADE
);

-- Foreign key constraint prevents invalid references
INSERT INTO categories VALUES (1, 'Electronics');
INSERT INTO products VALUES (1, 'Laptop', 1);  -- Valid
-- INSERT INTO products VALUES (2, 'Phone', 99);  -- Error: Invalid category_id
```

### 13. What are indexes and why are they important?

**Answer:** Data structures that improve query performance by creating shortcuts to data.

**Types:**
- Primary Index (automatically created for primary key)
- Unique Index
- Composite Index
- Partial Index

**Example:**
```sql
-- Create indexes
CREATE INDEX idx_lastname ON employees(last_name);
CREATE INDEX idx_name_dept ON employees(last_name, department);
CREATE UNIQUE INDEX idx_email ON employees(email);

-- Check if index is being used
EXPLAIN SELECT * FROM employees WHERE last_name = 'Smith';

-- Show all indexes on a table
SHOW INDEXES FROM employees;

-- Drop index
DROP INDEX idx_lastname ON employees;
```

### 14. What are constraints in MySQL?

**Answer:** Rules that limit the type of data that can go into a table.

**Types:** PRIMARY KEY, FOREIGN KEY, UNIQUE, NOT NULL, CHECK, DEFAULT

**Example:**
```sql
CREATE TABLE products (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    price DECIMAL(10,2) CHECK (price > 0),
    category_id INT,
    sku VARCHAR(50) UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (category_id) REFERENCES categories(id)
);

-- Add constraint to existing table
ALTER TABLE products 
ADD CONSTRAINT chk_price_positive CHECK (price >= 0);
```

### 15. What is the difference between DELETE, DROP, and TRUNCATE?

**Answer:**
- **DELETE:** Removes specific rows, can be rolled back, triggers fire
- **TRUNCATE:** Removes all rows quickly, cannot be rolled back, no triggers
- **DROP:** Removes entire table structure and data

**Example:**
```sql
-- DELETE - removes specific rows
DELETE FROM employees WHERE department = 'Sales';
DELETE FROM employees;  -- Removes all rows but keeps structure

-- TRUNCATE - fast removal of all rows
TRUNCATE TABLE employees;

-- DROP - removes table entirely
DROP TABLE employees;
```

---

## ⚡ Performance & Optimization

### 16. How do you optimize MySQL queries?

**Answer:** Several techniques can improve query performance:

**Example:**
```sql
-- Use EXPLAIN to analyze query execution
EXPLAIN SELECT * FROM orders o
JOIN customers c ON o.customer_id = c.id
WHERE o.order_date >= '2024-01-01';

-- Optimize with proper indexing
CREATE INDEX idx_order_date ON orders(order_date);
CREATE INDEX idx_customer_order ON orders(customer_id, order_date);

-- Use LIMIT for large datasets
SELECT * FROM products ORDER BY created_at DESC LIMIT 20;

-- Avoid SELECT * - specify needed columns
SELECT id, name, price FROM products WHERE active = 1;
```

### 17. What is query caching in MySQL?

**Answer:** MySQL can cache SELECT query results to improve performance for repeated queries.

**Example:**
```sql
-- Check query cache status
SHOW VARIABLES LIKE 'query_cache%';

-- Query cache configuration
SET GLOBAL query_cache_size = 268435456;  -- 256MB
SET GLOBAL query_cache_type = ON;

-- Check cache statistics
SHOW STATUS LIKE 'Qcache%';

-- Force query to not use cache (for testing)
SELECT SQL_NO_CACHE * FROM products;
```

### 18. What are MySQL execution plans?

**Answer:** Shows how MySQL executes a query, helping identify performance bottlenecks.

**Example:**
```sql
-- Basic EXPLAIN
EXPLAIN SELECT * FROM orders 
WHERE customer_id = 123 
AND order_date >= '2024-01-01';

-- Detailed execution plan
EXPLAIN FORMAT=JSON 
SELECT o.*, c.name 
FROM orders o 
JOIN customers c ON o.customer_id = c.id 
WHERE o.total > 1000;

-- Analyze actual execution
EXPLAIN ANALYZE 
SELECT product_name, SUM(quantity) 
FROM order_items 
GROUP BY product_name 
ORDER BY SUM(quantity) DESC;
```

### 19. How do you handle large datasets in MySQL?

**Answer:** Use partitioning, proper indexing, and query optimization techniques.

**Example:**
```sql
-- Table partitioning by range
CREATE TABLE sales_data (
    id INT,
    sale_date DATE,
    amount DECIMAL(10,2)
) PARTITION BY RANGE (YEAR(sale_date)) (
    PARTITION p2022 VALUES LESS THAN (2023),
    PARTITION p2023 VALUES LESS THAN (2024),
    PARTITION p2024 VALUES LESS THAN (2025),
    PARTITION p_future VALUES LESS THAN MAXVALUE
);

-- Pagination for large results
SELECT * FROM products 
ORDER BY id 
LIMIT 20 OFFSET 100;  -- Page 6 (20 records per page)

-- Better pagination approach
SELECT * FROM products 
WHERE id > 100 
ORDER BY id 
LIMIT 20;
```

### 20. What are slow queries and how do you identify them?

**Answer:** Queries that take longer than expected to execute. Use slow query log and performance monitoring.

**Example:**
```sql
-- Enable slow query log
SET GLOBAL slow_query_log = 'ON';
SET GLOBAL long_query_time = 2;  -- Log queries > 2 seconds
SET GLOBAL slow_query_log_file = '/var/log/mysql/slow.log';

-- Check current slow queries
SHOW PROCESSLIST;

-- Analyze slow queries
SELECT 
    query_time,
    lock_time,
    rows_examined,
    sql_text
FROM mysql.slow_log
ORDER BY query_time DESC
LIMIT 10;

-- Kill a slow running query
KILL QUERY 123;  -- Replace 123 with actual process ID
```

---

## 🚀 Advanced MySQL Topics

### 21. What are stored procedures and functions?

**Answer:** Reusable code blocks stored in the database that can accept parameters and return values.

**Example:**
```sql
-- Stored Procedure
DELIMITER $$
CREATE PROCEDURE GetEmployeesByDept(IN dept_name VARCHAR(100))
BEGIN
    SELECT e.*, d.name as department
    FROM employees e
    JOIN departments d ON e.dept_id = d.id
    WHERE d.name = dept_name;
END $$
DELIMITER ;

-- Call procedure
CALL GetEmployeesByDept('Engineering');

-- Stored Function
DELIMITER $$
CREATE FUNCTION CalculateBonus(salary DECIMAL(10,2)) 
RETURNS DECIMAL(10,2)
DETERMINISTIC
BEGIN
    DECLARE bonus DECIMAL(10,2);
    IF salary > 50000 THEN
        SET bonus = salary * 0.10;
    ELSE
        SET bonus = salary * 0.05;
    END IF;
    RETURN bonus;
END $$
DELIMITER ;

-- Use function
SELECT name, salary, CalculateBonus(salary) as bonus 
FROM employees;
```

### 22. What are triggers in MySQL?

**Answer:** Special procedures that automatically execute in response to database events.

**Example:**
```sql
-- Create audit table
CREATE TABLE employee_audit (
    audit_id INT AUTO_INCREMENT PRIMARY KEY,
    employee_id INT,
    old_salary DECIMAL(10,2),
    new_salary DECIMAL(10,2),
    action VARCHAR(10),
    timestamp DATETIME DEFAULT CURRENT_TIMESTAMP
);

-- BEFORE UPDATE trigger
DELIMITER $$
CREATE TRIGGER employee_salary_audit
BEFORE UPDATE ON employees
FOR EACH ROW
BEGIN
    IF OLD.salary != NEW.salary THEN
        INSERT INTO employee_audit (employee_id, old_salary, new_salary, action)
        VALUES (NEW.id, OLD.salary, NEW.salary, 'UPDATE');
    END IF;
END $$
DELIMITER ;

-- AFTER INSERT trigger
DELIMITER $$
CREATE TRIGGER welcome_new_employee
AFTER INSERT ON employees
FOR EACH ROW
BEGIN
    INSERT INTO notifications (employee_id, message, created_at)
    VALUES (NEW.id, 'Welcome to the company!', NOW());
END $$
DELIMITER ;
```

### 23. What is replication in MySQL?

**Answer:** Process of copying data from master server to one or more slave servers for backup, scaling, and high availability.

**Example:**
```sql
-- Master configuration (my.cnf)
-- server-id=1
-- log-bin=mysql-bin
-- binlog-do-db=production_db

-- Create replication user on master
CREATE USER 'replicator'@'%' IDENTIFIED BY 'secure_password';
GRANT REPLICATION SLAVE ON *.* TO 'replicator'@'%';
FLUSH PRIVILEGES;

-- Get master status
SHOW MASTER STATUS;

-- Slave configuration (my.cnf)
-- server-id=2
-- relay-log=mysql-relay-bin

-- Configure slave
CHANGE MASTER TO
    MASTER_HOST='master_ip',
    MASTER_USER='replicator',
    MASTER_PASSWORD='secure_password',
    MASTER_LOG_FILE='mysql-bin.000001',
    MASTER_LOG_POS=12345;

-- Start replication
START SLAVE;

-- Check slave status
SHOW SLAVE STATUS\G
```

### 24. What are views in MySQL?

**Answer:** Virtual tables based on SELECT queries that simplify complex queries and provide data security.

**Example:**
```sql
-- Create a view
CREATE VIEW employee_summary AS
SELECT 
    e.id,
    e.name,
    e.salary,
    d.name as department,
    CASE 
        WHEN e.salary > 75000 THEN 'Senior'
        WHEN e.salary > 50000 THEN 'Mid-level'
        ELSE 'Junior'
    END as level
FROM employees e
JOIN departments d ON e.dept_id = d.id
WHERE e.active = 1;

-- Use the view
SELECT * FROM employee_summary 
WHERE department = 'Engineering';

-- Updatable view
CREATE VIEW active_employees AS
SELECT id, name, email, salary
FROM employees
WHERE active = 1
WITH CHECK OPTION;  -- Ensures updates maintain the WHERE condition

-- Update through view
UPDATE active_employees 
SET salary = salary * 1.10 
WHERE id = 1;
```

### 25. What are Common Table Expressions (CTEs)?

**Answer:** Temporary result sets that exist only during query execution, useful for complex queries and recursive operations.

**Example:**
```sql
-- Simple CTE
WITH high_earners AS (
    SELECT name, salary, dept_id
    FROM employees
    WHERE salary > 75000
)
SELECT he.name, he.salary, d.name as department
FROM high_earners he
JOIN departments d ON he.dept_id = d.id;

-- Recursive CTE - Employee hierarchy
WITH RECURSIVE employee_hierarchy AS (
    -- Base case: Top-level managers
    SELECT id, name, manager_id, 1 as level
    FROM employees
    WHERE manager_id IS NULL
    
    UNION ALL
    
    -- Recursive case: Employees with managers
    SELECT e.id, e.name, e.manager_id, eh.level + 1
    FROM employees e
    JOIN employee_hierarchy eh ON e.manager_id = eh.id
)
SELECT * FROM employee_hierarchy
ORDER BY level, name;
```

### 26. What are window functions in MySQL?

**Answer:** Functions that perform calculations across related rows without collapsing the result set.

**Example:**
```sql
-- Row numbering and ranking
SELECT 
    name,
    department,
    salary,
    ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) as row_num,
    RANK() OVER (PARTITION BY department ORDER BY salary DESC) as rank_pos,
    DENSE_RANK() OVER (PARTITION BY department ORDER BY salary DESC) as dense_rank
FROM employees;

-- Running totals and moving averages
SELECT 
    order_date,
    amount,
    SUM(amount) OVER (ORDER BY order_date) as running_total,
    AVG(amount) OVER (ORDER BY order_date ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) as moving_avg_3
FROM daily_sales
ORDER BY order_date;

-- Lead and lag functions
SELECT 
    employee_id,
    sale_date,
    amount,
    LAG(amount) OVER (PARTITION BY employee_id ORDER BY sale_date) as prev_sale,
    LEAD(amount) OVER (PARTITION BY employee_id ORDER BY sale_date) as next_sale
FROM sales;
```

### 27. What is the difference between MyISAM and InnoDB?

**Answer:**

| Feature | MyISAM | InnoDB |
|---------|---------|---------|
| ACID Compliance | No | Yes |
| Foreign Keys | No | Yes |
| Locking | Table-level | Row-level |
| Crash Recovery | No | Yes |
| Transactions | No | Yes |

**Example:**
```sql
-- Create MyISAM table (fast reads, no transactions)
CREATE TABLE logs (
    id INT AUTO_INCREMENT PRIMARY KEY,
    message TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
) ENGINE=MyISAM;

-- Create InnoDB table (ACID compliant, supports transactions)
CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT,
    total DECIMAL(10,2),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (customer_id) REFERENCES customers(id)
) ENGINE=InnoDB;

-- Transaction example (only works with InnoDB)
START TRANSACTION;
INSERT INTO orders (customer_id, total) VALUES (1, 100.00);
INSERT INTO order_items (order_id, product_id, quantity) VALUES (LAST_INSERT_ID(), 1, 2);
COMMIT;
```

### 28. How do you backup and restore MySQL databases?

**Answer:** Use mysqldump for logical backups and various tools for physical backups.

**Example:**
```bash
# Full database backup
mysqldump -u username -p database_name > backup.sql

# Backup specific tables
mysqldump -u username -p database_name table1 table2 > tables_backup.sql

# Backup all databases
mysqldump -u username -p --all-databases > all_databases.sql

# Backup with compression
mysqldump -u username -p database_name | gzip > backup.sql.gz

# Restore database
mysql -u username -p database_name < backup.sql

# Restore compressed backup
gunzip < backup.sql.gz | mysql -u username -p database_name
```

```sql
-- Create backup table
CREATE TABLE employees_backup AS SELECT * FROM employees;

-- Point-in-time recovery using binary logs
SHOW BINARY LOGS;
SHOW BINLOG EVENTS IN 'mysql-bin.000001';

-- Restore from binary log
mysqlbinlog mysql-bin.000001 | mysql -u root -p
```

### 29. What are transactions and ACID properties?

**Answer:** Transactions are sequences of operations that are treated as a single unit. ACID ensures data integrity.

**ACID Properties:**
- **Atomicity:** All or nothing execution
- **Consistency:** Database remains in valid state
- **Isolation:** Transactions don't interfere with each other
- **Durability:** Committed changes are permanent

**Example:**
```sql
-- Transaction example: Money transfer
START TRANSACTION;

-- Deduct from source account
UPDATE accounts 
SET balance = balance - 100 
WHERE account_id = 1;

-- Add to destination account
UPDATE accounts 
SET balance = balance + 100 
WHERE account_id = 2;

-- Check if both operations succeeded
SELECT balance FROM accounts WHERE account_id IN (1, 2);

-- Commit or rollback based on conditions
COMMIT;
-- ROLLBACK;  -- Use this if something went wrong

-- Set transaction isolation level
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
```

### 30. What are locks in MySQL?

**Answer:** Mechanisms to control concurrent access to data and prevent conflicts.

**Types:**
- Table locks (LOCK TABLES)
- Row locks (automatic with InnoDB)
- Shared locks (SELECT ... LOCK IN SHARE MODE)
- Exclusive locks (SELECT ... FOR UPDATE)

**Example:**
```sql
-- Explicit table locking
LOCK TABLES employees WRITE, departments READ;
UPDATE employees SET salary = salary * 1.1 WHERE dept_id = 1;
UNLOCK TABLES;

-- Row-level locking for updates
START TRANSACTION;
SELECT balance FROM accounts WHERE id = 1 FOR UPDATE;  -- Exclusive lock
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
COMMIT;

-- Shared lock for consistent reads
START TRANSACTION;
SELECT * FROM inventory WHERE product_id = 1 LOCK IN SHARE MODE;
-- Other transactions can read but not modify
COMMIT;

-- Check for locks
SHOW ENGINE INNODB STATUS;
SELECT * FROM INFORMATION_SCHEMA.INNODB_LOCKS;
```

---

## 🔧 Additional Important Questions

### 31. How do you handle date and time in MySQL?

**Example:**
```sql
-- Date/time functions
SELECT 
    NOW() as current_datetime,
    CURDATE() as current_date,
    CURTIME() as current_time,
    DATE('2024-12-25 14:30:00') as date_only,
    TIME('2024-12-25 14:30:00') as time_only;

-- Date calculations
SELECT 
    DATE_ADD('2024-01-01', INTERVAL 30 DAY) as thirty_days_later,
    DATE_SUB(NOW(), INTERVAL 1 YEAR) as one_year_ago,
    DATEDIFF('2024-12-31', '2024-01-01') as days_difference,
    TIMESTAMPDIFF(MONTH, '2024-01-01', '2024-12-31') as months_diff;

-- Date formatting
SELECT 
    DATE_FORMAT(NOW(), '%Y-%m-%d %H:%i:%s') as formatted_datetime,
    DATE_FORMAT(NOW(), '%W, %M %d, %Y') as readable_date;
```

### 32. What are regular expressions in MySQL?

**Example:**
```sql
-- REGEXP operator
SELECT * FROM users 
WHERE email REGEXP '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$';

-- RLIKE (synonym for REGEXP)
SELECT * FROM products 
WHERE product_code RLIKE '^[A-Z]{3}[0-9]{3}$';

-- Case-insensitive matching
SELECT * FROM customers 
WHERE name REGEXP '(?i)^john';

-- Extract using REGEXP_SUBSTR
SELECT 
    text_field,
    REGEXP_SUBSTR(text_field, '[0-9]+') as extracted_number
FROM sample_table;
```

### 33. How do you work with JSON data in MySQL?

**Example:**
```sql
-- Create table with JSON column
CREATE TABLE user_preferences (
    user_id INT PRIMARY KEY,
    preferences JSON
);

-- Insert JSON data
INSERT INTO user_preferences VALUES 
(1, '{"theme": "dark", "language": "en", "notifications": {"email": true, "sms": false}}'),
(2, '{"theme": "light", "language": "fr", "notifications": {"email": false, "sms": true}}');

-- Query JSON data
SELECT 
    user_id,
    JSON_EXTRACT(preferences, '$.theme') as theme,
    preferences->>'$.language' as language,
    JSON_EXTRACT(preferences, '$.notifications.email') as email_notifications
FROM user_preferences;

-- Update JSON data
UPDATE user_preferences 
SET preferences = JSON_SET(preferences, '$.theme', 'auto')
WHERE user_id = 1;

-- Search in JSON
SELECT * FROM user_preferences
WHERE JSON_EXTRACT(preferences, '$.notifications.email') = true;
```

### 34. What are prepared statements?

**Example:**
```sql
-- Prepare statement
PREPARE stmt FROM 'SELECT * FROM employees WHERE department = ? AND salary > ?';

-- Set variables
SET @dept = 'Engineering';
SET @min_salary = 50000;

-- Execute prepared statement
EXECUTE stmt USING @dept, @min_salary;

-- Deallocate
DEALLOCATE PREPARE stmt;

-- More complex example
PREPARE dynamic_query FROM 'SELECT name, salary FROM employees ORDER BY ? LIMIT ?';
SET @sort_column = 'salary';
SET @limit_count = 10;
EXECUTE dynamic_query USING @sort_column, @limit_count;
```

### 35. How do you handle errors in MySQL?

**Example:**
```sql
-- Error handling in stored procedures
DELIMITER $$
CREATE PROCEDURE TransferMoney(
    IN from_account INT,
    IN to_account INT,
    IN amount DECIMAL(10,2)
)
BEGIN
    DECLARE EXIT HANDLER FOR SQLEXCEPTION
    BEGIN
        ROLLBACK;
        RESIGNAL;
    END;
    
    START TRANSACTION;
    
    -- Check sufficient balance
    IF (SELECT balance FROM accounts WHERE id = from_account) < amount THEN
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Insufficient balance';
    END IF;
    
    UPDATE accounts SET balance = balance - amount WHERE id = from_account;
    UPDATE accounts SET balance = balance + amount WHERE id = to_account;
    
    COMMIT;
END $$
DELIMITER ;

-- Custom error conditions
DELIMITER $$
CREATE PROCEDURE ValidateEmail(IN email_addr VARCHAR(255))
BEGIN
    DECLARE email_valid BOOLEAN DEFAULT FALSE;
    
    IF email_addr REGEXP '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$' THEN
        SET email_valid = TRUE;
    END IF;
    
    IF NOT email_valid THEN
        SIGNAL SQLSTATE '45000' 
        SET MESSAGE_TEXT = 'Invalid email format';
    END IF;
END $$
DELIMITER ;
```

### 36. What is connection pooling?

**Answer:** Connection pooling reuses database connections to improve performance and reduce overhead.

**Example Configuration:**
```sql
-- MySQL configuration variables
SHOW VARIABLES LIKE '%connection%';
SHOW VARIABLES LIKE 'max_connections';

-- Set connection limits
SET GLOBAL max_connections = 200;
SET GLOBAL max_user_connections = 50;

-- Monitor connections
SHOW PROCESSLIST;
SHOW STATUS LIKE 'Conn%';
SHOW STATUS LIKE 'Threads%';

-- Connection timeout settings
SET GLOBAL wait_timeout = 28800;
SET GLOBAL interactive_timeout = 28800;
```

### 37. How do you monitor MySQL performance?

**Example:**
```sql
-- Performance Schema queries
SELECT * FROM performance_schema.events_statements_summary_by_digest
ORDER BY AVG_TIMER_WAIT DESC LIMIT 10;

-- Slow query analysis
SELECT 
    SCHEMA_NAME,
    DIGEST_TEXT,
    COUNT_STAR,
    AVG_TIMER_WAIT/1000000000 as avg_time_seconds,
    SUM_TIMER_WAIT/1000000000 as total_time_seconds
FROM performance_schema.events_statements_summary_by_digest
WHERE SCHEMA_NAME IS NOT NULL
ORDER BY AVG_TIMER_WAIT DESC
LIMIT 10;

-- Table and index usage
SELECT 
    OBJECT_SCHEMA,
    OBJECT_NAME,
    COUNT_READ,
    COUNT_WRITE,
    COUNT_FETCH,
    SUM_TIMER_WAIT/1000000000 as total_time_seconds
FROM performance_schema.table_io_waits_summary_by_table
WHERE OBJECT_SCHEMA NOT IN ('mysql', 'performance_schema', 'information_schema')
ORDER BY SUM_TIMER_WAIT DESC;

-- Connection monitoring
SELECT 
    USER,
    HOST,
    DB,
    COMMAND,
    TIME,
    STATE,
    LEFT(INFO, 100) as QUERY_SNIPPET
FROM INFORMATION_SCHEMA.PROCESSLIST
WHERE USER != 'system user'
ORDER BY TIME DESC;
```

### 38. What is database sharding?

**Answer:** Horizontal partitioning technique where data is distributed across multiple database servers.

**Example:**
```sql
-- Sharding by user ID (hash-based)
-- Shard 1: user_id % 3 = 0
CREATE DATABASE shard_0;
USE shard_0;
CREATE TABLE users (
    id INT PRIMARY KEY,
    username VARCHAR(50),
    email VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Shard 2: user_id % 3 = 1  
CREATE DATABASE shard_1;
-- Similar table structure...

-- Shard 3: user_id % 3 = 2
CREATE DATABASE shard_2;
-- Similar table structure...

-- Application logic for routing
-- function getShardForUser($userId) {
--     return "shard_" . ($userId % 3);
-- }

-- Range-based sharding example
-- Shard by date ranges
CREATE TABLE orders_2023 (
    id INT PRIMARY KEY,
    user_id INT,
    total DECIMAL(10,2),
    order_date DATE,
    CHECK (order_date >= '2023-01-01' AND order_date <= '2023-12-31')
);

CREATE TABLE orders_2024 (
    id INT PRIMARY KEY,
    user_id INT,
    total DECIMAL(10,2),
    order_date DATE,
    CHECK (order_date >= '2024-01-01' AND order_date <= '2024-12-31')
);
```

### 39. What are MySQL configuration files and important parameters?

**Example:**
```sql
-- View current configuration
SHOW VARIABLES;
SHOW VARIABLES LIKE 'innodb%';
SHOW VARIABLES LIKE '%cache%';

-- Important variables to monitor
SELECT 
    VARIABLE_NAME,
    VARIABLE_VALUE
FROM INFORMATION_SCHEMA.GLOBAL_VARIABLES
WHERE VARIABLE_NAME IN (
    'max_connections',
    'innodb_buffer_pool_size',
    'query_cache_size',
    'tmp_table_size',
    'max_heap_table_size',
    'sort_buffer_size',
    'join_buffer_size'
);

-- Runtime configuration changes
SET GLOBAL max_connections = 200;
SET GLOBAL innodb_buffer_pool_size = 1073741824; -- 1GB
SET SESSION sql_mode = 'STRICT_TRANS_TABLES,NO_ZERO_DATE';

-- Check status variables
SHOW STATUS LIKE 'Innodb_buffer_pool%';
SHOW STATUS LIKE 'Created_tmp%';
SHOW STATUS LIKE 'Handler%';
```

### 40. How do you handle MySQL security?

**Example:**
```sql
-- User management
CREATE USER 'app_user'@'localhost' IDENTIFIED BY 'secure_password123';
CREATE USER 'readonly_user'@'%' IDENTIFIED BY 'readonly_pass';

-- Grant specific privileges
GRANT SELECT, INSERT, UPDATE, DELETE ON myapp.* TO 'app_user'@'localhost';
GRANT SELECT ON myapp.* TO 'readonly_user'@'%';

-- Grant with specific columns
GRANT SELECT (id, name, email) ON users TO 'limited_user'@'localhost';

-- Remove privileges
REVOKE DELETE ON myapp.* FROM 'app_user'@'localhost';

-- Change password
ALTER USER 'app_user'@'localhost' IDENTIFIED BY 'new_secure_password';

-- View user privileges
SHOW GRANTS FOR 'app_user'@'localhost';

-- Security best practices
-- 1. Remove anonymous users
DELETE FROM mysql.user WHERE User = '';

-- 2. Remove test database
DROP DATABASE IF EXISTS test;

-- 3. Set password validation
INSTALL PLUGIN validate_password SONAME 'validate_password.so';
SET GLOBAL validate_password.policy = STRONG;

-- 4. Enable SSL
-- ALTER USER 'secure_user'@'%' REQUIRE SSL;
```

### 41. What are MySQL events and how to use them?

**Example:**
```sql
-- Enable event scheduler
SET GLOBAL event_scheduler = ON;

-- Create a simple event
DELIMITER $
CREATE EVENT cleanup_old_logs
ON SCHEDULE EVERY 1 DAY
STARTS '2024-01-01 02:00:00'
DO
BEGIN
    DELETE FROM application_logs 
    WHERE created_at < DATE_SUB(NOW(), INTERVAL 30 DAY);
END $
DELIMITER ;

-- Create recurring event with end date
DELIMITER $
CREATE EVENT monthly_report
ON SCHEDULE EVERY 1 MONTH
STARTS '2024-01-01 09:00:00'
ENDS '2024-12-31 23:59:59'
DO
BEGIN
    INSERT INTO monthly_reports (report_date, total_sales, total_orders)
    SELECT 
        LAST_DAY(CURDATE() - INTERVAL 1 MONTH) as report_date,
        SUM(total) as total_sales,
        COUNT(*) as total_orders
    FROM orders 
    WHERE MONTH(order_date) = MONTH(CURDATE() - INTERVAL 1 MONTH)
    AND YEAR(order_date) = YEAR(CURDATE() - INTERVAL 1 MONTH);
END $
DELIMITER ;

-- Manage events
SHOW EVENTS;
ALTER EVENT cleanup_old_logs DISABLE;
ALTER EVENT cleanup_old_logs ENABLE;
DROP EVENT cleanup_old_logs;
```

### 42. What are MySQL user-defined functions?

**Example:**
```sql
-- Scalar function
DELIMITER $
CREATE FUNCTION GetTaxAmount(amount DECIMAL(10,2), tax_rate DECIMAL(5,4))
RETURNS DECIMAL(10,2)
DETERMINISTIC
READS SQL DATA
BEGIN
    DECLARE tax_amount DECIMAL(10,2);
    SET tax_amount = amount * tax_rate;
    RETURN tax_amount;
END $
DELIMITER ;

-- Use the function
SELECT 
    order_id,
    subtotal,
    GetTaxAmount(subtotal, 0.0825) as tax_amount,
    subtotal + GetTaxAmount(subtotal, 0.0825) as total
FROM orders;

-- Function with conditional logic
DELIMITER $
CREATE FUNCTION GetDiscountRate(customer_type VARCHAR(20), order_amount DECIMAL(10,2))
RETURNS DECIMAL(5,4)
DETERMINISTIC
BEGIN
    DECLARE discount_rate DECIMAL(5,4) DEFAULT 0;
    
    CASE customer_type
        WHEN 'PREMIUM' THEN
            IF order_amount > 1000 THEN
                SET discount_rate = 0.15;
            ELSE
                SET discount_rate = 0.10;
            END IF;
        WHEN 'REGULAR' THEN
            IF order_amount > 500 THEN
                SET discount_rate = 0.05;
            END IF;
        ELSE
            SET discount_rate = 0;
    END CASE;
    
    RETURN discount_rate;
END $
DELIMITER ;
```

### 43. How do you implement full-text search in MySQL?

**Example:**
```sql
-- Create table with FULLTEXT index
CREATE TABLE articles (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255),
    content TEXT,
    FULLTEXT KEY ft_title_content (title, content)
) ENGINE=InnoDB;

-- Insert sample data
INSERT INTO articles (title, content) VALUES
('MySQL Performance Tips', 'Learn how to optimize your MySQL database for better performance...'),
('Advanced SQL Queries', 'Master complex SQL queries with joins, subqueries, and window functions...'),
('Database Design Best Practices', 'Follow these guidelines for designing efficient database schemas...');

-- Basic full-text search
SELECT * FROM articles
WHERE MATCH(title, content) AGAINST ('MySQL performance');

-- Boolean mode search
SELECT *, MATCH(title, content) AGAINST ('MySQL performance' IN BOOLEAN MODE) as score
FROM articles
WHERE MATCH(title, content) AGAINST ('+MySQL +performance' IN BOOLEAN MODE);

-- Natural language mode with relevance score
SELECT 
    id,
    title,
    MATCH(title, content) AGAINST ('database optimization') as relevance_score
FROM articles
WHERE MATCH(title, content) AGAINST ('database optimization' IN NATURAL LANGUAGE MODE)
ORDER BY relevance_score DESC;

-- Query expansion mode
SELECT * FROM articles
WHERE MATCH(title, content) 
AGAINST ('MySQL' WITH QUERY EXPANSION);
```

### 44. What are MySQL spatial data types and functions?

**Example:**
```sql
-- Create table with spatial data
CREATE TABLE locations (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100),
    coordinates POINT NOT NULL,
    service_area POLYGON,
    SPATIAL INDEX idx_coordinates (coordinates),
    SPATIAL INDEX idx_service_area (service_area)
);

-- Insert spatial data
INSERT INTO locations (name, coordinates) VALUES
('Store A', POINT(40.7128, -74.0060)),  -- NYC coordinates
('Store B', POINT(34.0522, -118.2437)), -- LA coordinates
('Store C', POINT(41.8781, -87.6298));  -- Chicago coordinates

-- Find distance between points
SELECT 
    name,
    ST_Distance_Sphere(coordinates, POINT(40.7589, -73.9851)) as distance_meters
FROM locations
ORDER BY distance_meters;

-- Find locations within a radius (10km)
SELECT name
FROM locations
WHERE ST_Distance_Sphere(coordinates, POINT(40.7589, -73.9851)) <= 10000;

-- Create and query with polygons
INSERT INTO locations (name, service_area) VALUES
('Delivery Zone 1', ST_GeomFromText('POLYGON((0 0, 10 0, 10 10, 0 10, 0 0))'));

-- Check if point is within polygon
SELECT name
FROM locations
WHERE service_area IS NOT NULL
AND ST_Contains(service_area, POINT(5, 5));
```

### 45. How do you work with MySQL cursors?

**Example:**
```sql
-- Cursor example in stored procedure
DELIMITER $
CREATE PROCEDURE ProcessEmployeeSalaries()
BEGIN
    DECLARE done INT DEFAULT FALSE;
    DECLARE emp_id INT;
    DECLARE emp_salary DECIMAL(10,2);
    DECLARE bonus_amount DECIMAL(10,2);
    
    -- Declare cursor
    DECLARE salary_cursor CURSOR FOR
        SELECT id, salary FROM employees WHERE active = 1;
    
    -- Declare handler for cursor
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = TRUE;
    
    -- Open cursor
    OPEN salary_cursor;
    
    -- Loop through results
    salary_loop: LOOP
        FETCH salary_cursor INTO emp_id, emp_salary;
        
        IF done THEN
            LEAVE salary_loop;
        END IF;
        
        -- Calculate bonus based on salary
        IF emp_salary > 75000 THEN
            SET bonus_amount = emp_salary * 0.15;
        ELSEIF emp_salary > 50000 THEN
            SET bonus_amount = emp_salary * 0.10;
        ELSE
            SET bonus_amount = emp_salary * 0.05;
        END IF;
        
        -- Insert bonus record
        INSERT INTO employee_bonuses (employee_id, bonus_amount, bonus_date)
        VALUES (emp_id, bonus_amount, CURDATE());
        
    END LOOP;
    
    -- Close cursor
    CLOSE salary_cursor;
END $
DELIMITER ;

-- Call the procedure
CALL ProcessEmployeeSalaries();
```

### 46. What are MySQL partitions and when to use them?

**Example:**
```sql
-- Range partitioning by date
CREATE TABLE sales_data (
    id INT NOT NULL,
    sale_date DATE NOT NULL,
    customer_id INT,
    amount DECIMAL(10,2),
    PRIMARY KEY (id, sale_date)
) PARTITION BY RANGE (YEAR(sale_date)) (
    PARTITION p2022 VALUES LESS THAN (2023),
    PARTITION p2023 VALUES LESS THAN (2024),
    PARTITION p2024 VALUES LESS THAN (2025),
    PARTITION p_future VALUES LESS THAN MAXVALUE
);

-- Hash partitioning for even distribution
CREATE TABLE user_sessions (
    session_id VARCHAR(128),
    user_id INT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    data TEXT,
    PRIMARY KEY (session_id, user_id)
) PARTITION BY HASH(user_id) PARTITIONS 8;

-- List partitioning by specific values
CREATE TABLE regional_sales (
    id INT,
    region VARCHAR(20),
    amount DECIMAL(10,2),
    sale_date DATE
) PARTITION BY LIST COLUMNS(region) (
    PARTITION p_north VALUES IN ('North', 'Northeast', 'Northwest'),
    PARTITION p_south VALUES IN ('South', 'Southeast', 'Southwest'),
    PARTITION p_east VALUES IN ('East', 'Central-East'),
    PARTITION p_west VALUES IN ('West', 'Central-West')
);

-- Manage partitions
ALTER TABLE sales_data ADD PARTITION (
    PARTITION p2025 VALUES LESS THAN (2026)
);

ALTER TABLE sales_data DROP PARTITION p2022;

-- Check partition information
SELECT 
    TABLE_SCHEMA,
    TABLE_NAME,
    PARTITION_NAME,
    TABLE_ROWS,
    DATA_LENGTH,
    INDEX_LENGTH
FROM INFORMATION_SCHEMA.PARTITIONS
WHERE TABLE_SCHEMA = 'your_database'
AND TABLE_NAME = 'sales_data';
```

### 47. How do you handle large imports and exports?

**Example:**
```sql
-- LOAD DATA INFILE for fast imports
LOAD DATA INFILE '/path/to/data.csv'
INTO TABLE employees
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS
(name, email, department, salary);

-- Import with data transformation
LOAD DATA INFILE '/path/to/sales.csv'
INTO TABLE sales
FIELDS TERMINATED BY ','
LINES TERMINATED BY '\n'
(product_name, @sale_date, @amount)
SET 
    sale_date = STR_TO_DATE(@sale_date, '%m/%d/%Y'),
    amount = CAST(@amount AS DECIMAL(10,2)),
    created_at = NOW();

-- Export data using SELECT INTO OUTFILE
SELECT customer_id, name, email, total_orders
FROM customers
WHERE registration_date >= '2024-01-01'
INTO OUTFILE '/path/to/export.csv'
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n';

-- Bulk insert optimization
SET autocommit = 0;
SET unique_checks = 0;
SET foreign_key_checks = 0;

-- Your bulk insert operations here
INSERT INTO large_table VALUES (...);

SET foreign_key_checks = 1;
SET unique_checks = 1;
COMMIT;
SET autocommit = 1;
```

### 48. What are MySQL temporary tables?

**Example:**
```sql
-- Create temporary table
CREATE TEMPORARY TABLE temp_sales_summary (
    product_id INT,
    product_name VARCHAR(100),
    total_quantity INT,
    total_revenue DECIMAL(12,2),
    avg_price DECIMAL(10,2)
);

-- Populate temporary table
INSERT INTO temp_sales_summary
SELECT 
    p.id,
    p.name,
    SUM(oi.quantity) as total_quantity,
    SUM(oi.quantity * oi.price) as total_revenue,
    AVG(oi.price) as avg_price
FROM products p
JOIN order_items oi ON p.id = oi.product_id
JOIN orders o ON oi.order_id = o.id
WHERE o.order_date >= '2024-01-01'
GROUP BY p.id, p.name;

-- Use temporary table in complex queries
SELECT 
    tss.*,
    CASE 
        WHEN total_revenue > 10000 THEN 'High Performer'
        WHEN total_revenue > 5000 THEN 'Medium Performer'
        ELSE 'Low Performer'
    END as performance_category
FROM temp_sales_summary tss
ORDER BY total_revenue DESC;

-- Temporary table with indexes
CREATE TEMPORARY TABLE temp_customer_analysis (
    customer_id INT PRIMARY KEY,
    order_count INT,
    total_spent DECIMAL(12,2),
    avg_order_value DECIMAL(10,2),
    last_order_date DATE,
    INDEX idx_total_spent (total_spent),
    INDEX idx_order_count (order_count)
);
```

### 49. How do you implement database versioning and migrations?

**Example:**
```sql
-- Create schema version tracking table
CREATE TABLE schema_migrations (
    version VARCHAR(50) PRIMARY KEY,
    applied_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    description TEXT
);

-- Migration 001: Create initial tables
-- File: migrations/001_initial_schema.sql
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO schema_migrations (version, description) 
VALUES ('001', 'Initial schema - users table');

-- Migration 002: Add profiles table
-- File: migrations/002_add_profiles.sql
CREATE TABLE user_profiles (
    user_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    bio TEXT,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);

INSERT INTO schema_migrations (version, description) 
VALUES ('002', 'Add user profiles table');

-- Migration 003: Modify existing table
-- File: migrations/003_add_user_status.sql
ALTER TABLE users 
ADD COLUMN status ENUM('active', 'inactive', 'suspended') DEFAULT 'active',
ADD COLUMN last_login TIMESTAMP NULL;

INSERT INTO schema_migrations (version, description) 
VALUES ('003', 'Add status and last_login to users');

-- Check current schema version
SELECT version, applied_at, description 
FROM schema_migrations 
ORDER BY version DESC 
LIMIT 1;

-- Rollback example (Migration 003 rollback)
ALTER TABLE users 
DROP COLUMN status,
DROP COLUMN last_login;

DELETE FROM schema_migrations WHERE version = '003';
```

### 50. What are MySQL best practices for application development?

**Example:**
```sql
-- Connection management
-- Use connection pooling in application
-- Example configuration (application level):
-- max_pool_size = 20
-- min_pool_size = 5
-- pool_timeout = 30000

-- Prepared statements (prevents SQL injection)
PREPARE user_lookup FROM 'SELECT * FROM users WHERE username = ? AND status = ?';
SET @username = 'john_doe';
SET @status = 'active';
EXECUTE user_lookup USING @username, @status;

-- Efficient pagination
-- Bad: OFFSET gets slower with larger offsets
SELECT * FROM products ORDER BY id LIMIT 20 OFFSET 10000;

-- Good: Cursor-based pagination
SELECT * FROM products 
WHERE id > 10000 
ORDER BY id 
LIMIT 20;

-- Batch operations instead of individual queries
-- Bad: Multiple individual inserts
-- INSERT INTO logs (message, level) VALUES ('Error 1', 'ERROR');
-- INSERT INTO logs (message, level) VALUES ('Error 2', 'ERROR');

-- Good: Batch insert
INSERT INTO logs (message, level) VALUES 
('Error 1', 'ERROR'),
('Error 2', 'ERROR'),
('Error 3', 'ERROR');

-- Use appropriate data types
CREATE TABLE optimized_table (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,  -- UNSIGNED for positive IDs
    status TINYINT UNSIGNED,                     -- TINYINT for small ranges
    price DECIMAL(10,2),                         -- DECIMAL for exact money values
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    is_active BOOLEAN DEFAULT TRUE               -- BOOLEAN for true/false values
);

-- Index optimization
-- Create covering indexes
CREATE INDEX idx_user_status_created ON users(status, created_at, id);

-- Query optimization
-- Use EXISTS instead of IN for large subqueries
SELECT u.username
FROM users u
WHERE EXISTS (
    SELECT 1 FROM orders o 
    WHERE o.user_id = u.id 
    AND o.created_at >= '2024-01-01'
);

-- Monitor and analyze queries
-- Enable slow query log
SET GLOBAL slow_query_log = 'ON';
SET GLOBAL long_query_time = 1;

-- Use EXPLAIN to analyze query performance
EXPLAIN FORMAT=JSON
SELECT u.username, COUNT(o.id) as order_count
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
WHERE u.status = 'active'
GROUP BY u.id, u.username
HAVING order_count > 5
ORDER BY order_count DESC;
```

---

## 🎯 Summary

This comprehensive guide covers the 50 most important MySQL interview questions ranging from basic concepts to advanced topics. Key areas include:

**📊 Essential Topics Covered:**
- Database fundamentals and data types
- SQL queries and joins
- Performance optimization and indexing
- Transactions and ACID properties
- Advanced features like stored procedures, triggers, and views
- Security and user management
- Monitoring and troubleshooting

**💡 Interview Tips:**
- Always provide practical examples when answering
- Explain the "why" behind your choices
- Discuss performance implications
- Mention real-world scenarios where applicable
- Be prepared to write SQL queries on the spot

**🚀 Next Steps:**
- Practice writing complex queries
- Set up a local MySQL environment for hands-on experience
- Study MySQL documentation for latest features
- Learn about MySQL 8.0 new features like window functions and CTEs

Good luck with your MySQL interviews! 🍀

---

*This document serves as a comprehensive reference for MySQL interview preparation. Keep practicing and stay updated with the latest MySQL features and best practices.*