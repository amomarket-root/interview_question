# 🎯 MySQL Scenario-Based Interview Questions

*20 Real-world MySQL problems with multiple solution approaches and detailed explanations*

---

## 📚 Table of Contents

- [Salary and Ranking Problems](#-salary-and-ranking-problems)
- [Data Analysis Scenarios](#-data-analysis-scenarios)
- [Business Logic Implementation](#-business-logic-implementation)
- [Performance and Optimization](#-performance-and-optimization)
- [Data Manipulation Challenges](#-data-manipulation-challenges)

---

## 💰 Salary and Ranking Problems

### 1. 🥈 Find the 2nd Highest Salary from Employee Table

**Scenario:** You have an employee table and need to find the employee with the second-highest salary.

**Sample Data:**
```sql
CREATE TABLE employees (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    department VARCHAR(50),
    salary DECIMAL(10,2)
);

INSERT INTO employees VALUES
(1, 'John Doe', 'Engineering', 75000),
(2, 'Jane Smith', 'Engineering', 85000),
(3, 'Bob Johnson', 'Marketing', 65000),
(4, 'Alice Brown', 'Engineering', 95000),
(5, 'Charlie Davis', 'Marketing', 70000),
(6, 'Eva Wilson', 'HR', 85000);
```

**📋 Multiple Solutions:**

**Solution 1: Using LIMIT with OFFSET**
```sql
SELECT name, salary
FROM employees
ORDER BY salary DESC
LIMIT 1 OFFSET 1;
```

**Solution 2: Using Subquery**
```sql
SELECT name, salary
FROM employees
WHERE salary = (
    SELECT salary 
    FROM employees 
    ORDER BY salary DESC 
    LIMIT 1 OFFSET 1
);
```

**Solution 3: Using Window Functions (MySQL 8.0+)**
```sql
SELECT name, salary
FROM (
    SELECT name, salary,
           DENSE_RANK() OVER (ORDER BY salary DESC) as rank_num
    FROM employees
) ranked
WHERE rank_num = 2;
```

**Solution 4: Using MAX() with Subquery**
```sql
SELECT name, salary
FROM employees
WHERE salary = (
    SELECT MAX(salary)
    FROM employees
    WHERE salary < (SELECT MAX(salary) FROM employees)
);
```

**🎯 Result:** Alice Brown, 95000 (if we want 2nd highest unique salary)

---

### 2. 🏆 Find Nth Highest Salary in Each Department

**Scenario:** Find the 3rd highest salary in each department.

**Solution:**
```sql
-- Using Window Functions (Recommended)
SELECT department, name, salary
FROM (
    SELECT department, name, salary,
           DENSE_RANK() OVER (PARTITION BY department ORDER BY salary DESC) as dept_rank
    FROM employees
) ranked
WHERE dept_rank = 3;

-- Alternative: Using Correlated Subquery
SELECT e1.department, e1.name, e1.salary
FROM employees e1
WHERE 3 = (
    SELECT COUNT(DISTINCT e2.salary)
    FROM employees e2
    WHERE e2.department = e1.department
    AND e2.salary >= e1.salary
);
```

---

### 3. 📊 Find Employees with Salary Above Department Average

**Scenario:** List all employees whose salary is above their department's average salary.

**Solution:**
```sql
SELECT e.name, e.department, e.salary, dept_avg.avg_salary
FROM employees e
JOIN (
    SELECT department, AVG(salary) as avg_salary
    FROM employees
    GROUP BY department
) dept_avg ON e.department = dept_avg.department
WHERE e.salary > dept_avg.avg_salary;

-- Alternative using Window Functions
SELECT name, department, salary, avg_dept_salary
FROM (
    SELECT name, department, salary,
           AVG(salary) OVER (PARTITION BY department) as avg_dept_salary
    FROM employees
) emp_with_avg
WHERE salary > avg_dept_salary;
```

---

## 📈 Data Analysis Scenarios

### 4. 🛒 Find Customers with No Orders

**Scenario:** You have customers and orders tables. Find customers who haven't placed any orders.

**Sample Data:**
```sql
CREATE TABLE customers (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100)
);

CREATE TABLE orders (
    id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    total DECIMAL(10,2)
);

INSERT INTO customers VALUES
(1, 'John Smith', 'john@email.com'),
(2, 'Jane Doe', 'jane@email.com'),
(3, 'Bob Wilson', 'bob@email.com'),
(4, 'Alice Brown', 'alice@email.com');

INSERT INTO orders VALUES
(1, 1, '2024-01-15', 150.00),
(2, 2, '2024-01-20', 200.00),
(3, 1, '2024-02-01', 175.00);
```

**Solutions:**
```sql
-- Solution 1: LEFT JOIN with NULL check
SELECT c.id, c.name, c.email
FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id
WHERE o.customer_id IS NULL;

-- Solution 2: NOT EXISTS
SELECT c.id, c.name, c.email
FROM customers c
WHERE NOT EXISTS (
    SELECT 1 FROM orders o 
    WHERE o.customer_id = c.id
);

-- Solution 3: NOT IN (be careful with NULLs)
SELECT c.id, c.name, c.email
FROM customers c
WHERE c.id NOT IN (
    SELECT DISTINCT customer_id 
    FROM orders 
    WHERE customer_id IS NOT NULL
);
```

---

### 5. 📅 Find Consecutive Days with Sales

**Scenario:** Find periods where sales occurred on consecutive days.

**Sample Data:**
```sql
CREATE TABLE daily_sales (
    sale_date DATE PRIMARY KEY,
    total_amount DECIMAL(10,2)
);

INSERT INTO daily_sales VALUES
('2024-01-01', 1000.00),
('2024-01-02', 1200.00),
('2024-01-03', 1100.00),
('2024-01-05', 900.00),
('2024-01-06', 1300.00),
('2024-01-08', 800.00);
```

**Solution:**
```sql
-- Find consecutive sequences using LAG/LEAD
SELECT 
    sale_date,
    total_amount,
    CASE 
        WHEN DATEDIFF(sale_date, LAG(sale_date) OVER (ORDER BY sale_date)) = 1 
        THEN 'Consecutive'
        ELSE 'Gap'
    END as sequence_status
FROM daily_sales
ORDER BY sale_date;

-- Find start and end of consecutive periods
WITH consecutive_groups AS (
    SELECT 
        sale_date,
        total_amount,
        sale_date - INTERVAL ROW_NUMBER() OVER (ORDER BY sale_date) DAY as group_date
    FROM daily_sales
)
SELECT 
    MIN(sale_date) as sequence_start,
    MAX(sale_date) as sequence_end,
    COUNT(*) as consecutive_days,
    SUM(total_amount) as total_sales
FROM consecutive_groups
GROUP BY group_date
HAVING COUNT(*) > 1;
```

---

### 6. 🔄 Find Duplicate Records

**Scenario:** Find duplicate customer records based on email and phone.

**Sample Data:**
```sql
CREATE TABLE customer_records (
    id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    email VARCHAR(100),
    phone VARCHAR(15)
);

INSERT INTO customer_records VALUES
(1, 'John', 'Smith', 'john@email.com', '123-456-7890'),
(2, 'Jane', 'Doe', 'jane@email.com', '234-567-8901'),
(3, 'John', 'Smith', 'john@email.com', '123-456-7890'),
(4, 'Bob', 'Wilson', 'bob@email.com', '345-678-9012'),
(5, 'Jane', 'Doe', 'jane@email.com', '234-567-8901');
```

**Solutions:**
```sql
-- Find duplicate groups
SELECT email, phone, COUNT(*) as duplicate_count
FROM customer_records
GROUP BY email, phone
HAVING COUNT(*) > 1;

-- Find all duplicate records with details
SELECT cr.*
FROM customer_records cr
JOIN (
    SELECT email, phone
    FROM customer_records
    GROUP BY email, phone
    HAVING COUNT(*) > 1
) duplicates ON cr.email = duplicates.email AND cr.phone = duplicates.phone
ORDER BY cr.email, cr.phone, cr.id;

-- Find duplicates using Window Functions
SELECT *
FROM (
    SELECT *,
           COUNT(*) OVER (PARTITION BY email, phone) as duplicate_count,
           ROW_NUMBER() OVER (PARTITION BY email, phone ORDER BY id) as row_num
    FROM customer_records
) ranked
WHERE duplicate_count > 1;
```

---

## 🏢 Business Logic Implementation

### 7. 💳 Calculate Running Balance for Bank Account

**Scenario:** Calculate running balance for a bank account based on transactions.

**Sample Data:**
```sql
CREATE TABLE transactions (
    id INT PRIMARY KEY,
    account_id INT,
    transaction_date DATETIME,
    transaction_type ENUM('CREDIT', 'DEBIT'),
    amount DECIMAL(10,2)
);

INSERT INTO transactions VALUES
(1, 101, '2024-01-01 10:00:00', 'CREDIT', 1000.00),
(2, 101, '2024-01-02 11:00:00', 'DEBIT', 150.00),
(3, 101, '2024-01-03 12:00:00', 'CREDIT', 500.00),
(4, 101, '2024-01-04 13:00:00', 'DEBIT', 200.00),
(5, 101, '2024-01-05 14:00:00', 'DEBIT', 75.00);
```

**Solution:**
```sql
SELECT 
    id,
    account_id,
    transaction_date,
    transaction_type,
    amount,
    SUM(
        CASE 
            WHEN transaction_type = 'CREDIT' THEN amount
            WHEN transaction_type = 'DEBIT' THEN -amount
        END
    ) OVER (
        PARTITION BY account_id 
        ORDER BY transaction_date, id
        ROWS UNBOUNDED PRECEDING
    ) as running_balance
FROM transactions
WHERE account_id = 101
ORDER BY transaction_date, id;
```

---

### 8. 📊 Find Top 3 Products by Sales in Each Category

**Scenario:** Get the top 3 best-selling products in each category.

**Sample Data:**
```sql
CREATE TABLE products (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    category VARCHAR(50),
    price DECIMAL(10,2)
);

CREATE TABLE order_items (
    id INT PRIMARY KEY,
    product_id INT,
    quantity INT,
    unit_price DECIMAL(10,2)
);

INSERT INTO products VALUES
(1, 'Laptop', 'Electronics', 999.99),
(2, 'Mouse', 'Electronics', 29.99),
(3, 'Keyboard', 'Electronics', 79.99),
(4, 'Chair', 'Furniture', 199.99),
(5, 'Desk', 'Furniture', 299.99),
(6, 'Monitor', 'Electronics', 299.99);

INSERT INTO order_items VALUES
(1, 1, 50, 999.99),
(2, 2, 200, 29.99),
(3, 3, 100, 79.99),
(4, 4, 75, 199.99),
(5, 5, 30, 299.99),
(6, 6, 80, 299.99);
```

**Solution:**
```sql
WITH product_sales AS (
    SELECT 
        p.id,
        p.name,
        p.category,
        SUM(oi.quantity * oi.unit_price) as total_sales,
        SUM(oi.quantity) as total_quantity
    FROM products p
    JOIN order_items oi ON p.id = oi.product_id
    GROUP BY p.id, p.name, p.category
),
ranked_products AS (
    SELECT *,
           ROW_NUMBER() OVER (PARTITION BY category ORDER BY total_sales DESC) as sales_rank
    FROM product_sales
)
SELECT category, name, total_sales, total_quantity, sales_rank
FROM ranked_products
WHERE sales_rank <= 3
ORDER BY category, sales_rank;
```

---

### 9. 📈 Calculate Month-over-Month Growth Rate

**Scenario:** Calculate monthly sales growth rate.

**Sample Data:**
```sql
CREATE TABLE monthly_sales (
    year_month DATE,
    total_sales DECIMAL(12,2)
);

INSERT INTO monthly_sales VALUES
('2024-01-01', 50000.00),
('2024-02-01', 55000.00),
('2024-03-01', 48000.00),
('2024-04-01', 62000.00),
('2024-05-01', 58000.00);
```

**Solution:**
```sql
SELECT 
    year_month,
    total_sales,
    LAG(total_sales) OVER (ORDER BY year_month) as prev_month_sales,
    ROUND(
        (total_sales - LAG(total_sales) OVER (ORDER BY year_month)) / 
        LAG(total_sales) OVER (ORDER BY year_month) * 100, 2
    ) as growth_rate_percent,
    total_sales - LAG(total_sales) OVER (ORDER BY year_month) as growth_amount
FROM monthly_sales
ORDER BY year_month;
```

---

### 10. 🎯 Find Products Never Ordered

**Scenario:** Find products that have never been ordered.

**Solution:**
```sql
-- Using LEFT JOIN
SELECT p.id, p.name, p.category
FROM products p
LEFT JOIN order_items oi ON p.id = oi.product_id
WHERE oi.product_id IS NULL;

-- Using NOT EXISTS
SELECT p.id, p.name, p.category
FROM products p
WHERE NOT EXISTS (
    SELECT 1 FROM order_items oi 
    WHERE oi.product_id = p.id
);

-- Include products with zero total sales
SELECT 
    p.id, 
    p.name, 
    p.category,
    COALESCE(SUM(oi.quantity * oi.unit_price), 0) as total_sales
FROM products p
LEFT JOIN order_items oi ON p.id = oi.product_id
GROUP BY p.id, p.name, p.category
HAVING total_sales = 0;
```

---

## ⚡ Performance and Optimization

### 11. 🚀 Optimize Slow Query with Large Dataset

**Scenario:** You have a slow query that joins multiple tables. Optimize it.

**Original Slow Query:**
```sql
SELECT 
    c.name as customer_name,
    COUNT(o.id) as total_orders,
    SUM(o.total) as total_spent
FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id
LEFT JOIN order_items oi ON o.id = oi.order_id
LEFT JOIN products p ON oi.product_id = p.id
WHERE o.order_date >= '2024-01-01'
GROUP BY c.id, c.name
HAVING total_orders > 5;
```

**Optimized Solutions:**
```sql
-- Solution 1: Pre-filter before joining
WITH filtered_orders AS (
    SELECT customer_id, id, total
    FROM orders
    WHERE order_date >= '2024-01-01'
),
customer_stats AS (
    SELECT 
        customer_id,
        COUNT(*) as total_orders,
        SUM(total) as total_spent
    FROM filtered_orders
    GROUP BY customer_id
    HAVING COUNT(*) > 5
)
SELECT 
    c.name as customer_name,
    cs.total_orders,
    cs.total_spent
FROM customer_stats cs
JOIN customers c ON c.id = cs.customer_id;

-- Solution 2: Add proper indexes
CREATE INDEX idx_orders_customer_date ON orders(customer_id, order_date);
CREATE INDEX idx_order_items_order_id ON order_items(order_id);
CREATE INDEX idx_order_items_product_id ON order_items(product_id);
```

---

### 12. 🔍 Find Missing Sequential IDs

**Scenario:** Find missing ID numbers in a sequence.

**Sample Data:**
```sql
CREATE TABLE user_ids (
    id INT PRIMARY KEY
);

INSERT INTO user_ids VALUES (1), (2), (3), (5), (6), (8), (10);
```

**Solution:**
```sql
-- Generate sequence and find missing numbers
WITH RECURSIVE number_sequence AS (
    SELECT 1 as num
    UNION ALL
    SELECT num + 1
    FROM number_sequence
    WHERE num < (SELECT MAX(id) FROM user_ids)
)
SELECT ns.num as missing_id
FROM number_sequence ns
LEFT JOIN user_ids ui ON ns.num = ui.id
WHERE ui.id IS NULL;

-- Alternative using self-join to find gaps
SELECT (u1.id + 1) as missing_start,
       (MIN(u2.id) - 1) as missing_end
FROM user_ids u1
LEFT JOIN user_ids u2 ON u1.id < u2.id
WHERE u1.id + 1 NOT IN (SELECT id FROM user_ids)
GROUP BY u1.id
HAVING missing_start <= missing_end;
```

---

## 🔧 Data Manipulation Challenges

### 13. 🔄 Pivot Table Implementation

**Scenario:** Transform rows to columns (pivot operation).

**Sample Data:**
```sql
CREATE TABLE sales_data (
    year INT,
    quarter VARCHAR(2),
    sales_amount DECIMAL(10,2)
);

INSERT INTO sales_data VALUES
(2024, 'Q1', 100000),
(2024, 'Q2', 120000),
(2024, 'Q3', 110000),
(2024, 'Q4', 130000),
(2023, 'Q1', 90000),
(2023, 'Q2', 95000),
(2023, 'Q3', 100000),
(2023, 'Q4', 105000);
```

**Solution:**
```sql
SELECT 
    year,
    SUM(CASE WHEN quarter = 'Q1' THEN sales_amount END) as Q1_sales,
    SUM(CASE WHEN quarter = 'Q2' THEN sales_amount END) as Q2_sales,
    SUM(CASE WHEN quarter = 'Q3' THEN sales_amount END) as Q3_sales,
    SUM(CASE WHEN quarter = 'Q4' THEN sales_amount END) as Q4_sales,
    SUM(sales_amount) as total_yearly_sales
FROM sales_data
GROUP BY year
ORDER BY year;

-- With percentage calculations
SELECT 
    year,
    SUM(CASE WHEN quarter = 'Q1' THEN sales_amount END) as Q1_sales,
    ROUND(SUM(CASE WHEN quarter = 'Q1' THEN sales_amount END) / SUM(sales_amount) * 100, 2) as Q1_percent,
    SUM(CASE WHEN quarter = 'Q2' THEN sales_amount END) as Q2_sales,
    ROUND(SUM(CASE WHEN quarter = 'Q2' THEN sales_amount END) / SUM(sales_amount) * 100, 2) as Q2_percent,
    SUM(sales_amount) as total_yearly_sales
FROM sales_data
GROUP BY year;
```

---

### 14. 📋 Update Records Based on Complex Conditions

**Scenario:** Update employee salaries based on performance and department rules.

**Business Rules:**
- Engineering: +15% if performance > 4, +10% if performance > 3
- Marketing: +12% if performance > 4, +8% if performance > 3
- HR: +10% if performance > 4, +5% if performance > 3

**Sample Data:**
```sql
CREATE TABLE employee_performance (
    employee_id INT,
    name VARCHAR(100),
    department VARCHAR(50),
    current_salary DECIMAL(10,2),
    performance_rating DECIMAL(3,2)
);

INSERT INTO employee_performance VALUES
(1, 'John Doe', 'Engineering', 75000, 4.2),
(2, 'Jane Smith', 'Marketing', 65000, 3.8),
(3, 'Bob Johnson', 'HR', 55000, 4.5),
(4, 'Alice Brown', 'Engineering', 80000, 3.2);
```

**Solution:**
```sql
UPDATE employee_performance
SET current_salary = current_salary * (
    1 + 
    CASE 
        WHEN department = 'Engineering' AND performance_rating > 4 THEN 0.15
        WHEN department = 'Engineering' AND performance_rating > 3 THEN 0.10
        WHEN department = 'Marketing' AND performance_rating > 4 THEN 0.12
        WHEN department = 'Marketing' AND performance_rating > 3 THEN 0.08
        WHEN department = 'HR' AND performance_rating > 4 THEN 0.10
        WHEN department = 'HR' AND performance_rating > 3 THEN 0.05
        ELSE 0
    END
);

-- Verify the updates
SELECT 
    name,
    department,
    current_salary,
    performance_rating,
    CASE 
        WHEN department = 'Engineering' AND performance_rating > 4 THEN '15%'
        WHEN department = 'Engineering' AND performance_rating > 3 THEN '10%'
        WHEN department = 'Marketing' AND performance_rating > 4 THEN '12%'
        WHEN department = 'Marketing' AND performance_rating > 3 THEN '8%'
        WHEN department = 'HR' AND performance_rating > 4 THEN '10%'
        WHEN department = 'HR' AND performance_rating > 3 THEN '5%'
        ELSE '0%'
    END as raise_percentage
FROM employee_performance;
```

---

### 15. 🗂️ Hierarchical Data Query (Employee Manager Structure)

**Scenario:** Query organizational hierarchy to find all subordinates of a manager.

**Sample Data:**
```sql
CREATE TABLE employee_hierarchy (
    employee_id INT PRIMARY KEY,
    name VARCHAR(100),
    manager_id INT,
    position VARCHAR(100)
);

INSERT INTO employee_hierarchy VALUES
(1, 'CEO John', NULL, 'Chief Executive Officer'),
(2, 'VP Jane', 1, 'Vice President'),
(3, 'VP Bob', 1, 'Vice President'),
(4, 'Manager Alice', 2, 'Engineering Manager'),
(5, 'Manager Charlie', 2, 'Product Manager'),
(6, 'Dev Dave', 4, 'Senior Developer'),
(7, 'Dev Eva', 4, 'Junior Developer'),
(8, 'Sales Sam', 3, 'Sales Manager'),
(9, 'Rep Rita', 8, 'Sales Representative');
```

**Solution:**
```sql
-- Find all subordinates of a specific manager (recursive)
WITH RECURSIVE subordinates AS (
    -- Base case: Direct reports
    SELECT employee_id, name, manager_id, position, 1 as level
    FROM employee_hierarchy
    WHERE manager_id = 2  -- VP Jane's direct reports
    
    UNION ALL
    
    -- Recursive case: Subordinates of subordinates
    SELECT e.employee_id, e.name, e.manager_id, e.position, s.level + 1
    FROM employee_hierarchy e
    JOIN subordinates s ON e.manager_id = s.employee_id
)
SELECT 
    employee_id,
    name,
    position,
    level,
    CONCAT(REPEAT('  ', level - 1), name) as indented_name
FROM subordinates
ORDER BY level, name;

-- Complete organizational chart
WITH RECURSIVE org_chart AS (
    SELECT employee_id, name, manager_id, position, 1 as level, CAST(name AS CHAR(500)) as path
    FROM employee_hierarchy
    WHERE manager_id IS NULL
    
    UNION ALL
    
    SELECT e.employee_id, e.name, e.manager_id, e.position, oc.level + 1, 
           CONCAT(oc.path, ' -> ', e.name)
    FROM employee_hierarchy e
    JOIN org_chart oc ON e.manager_id = oc.employee_id
)
SELECT level, CONCAT(REPEAT('  ', level - 1), name) as org_structure, path
FROM org_chart
ORDER BY path;
```

---

### 16. 📊 Moving Average Calculation

**Scenario:** Calculate 3-month moving average for sales data.

**Sample Data:**
```sql
CREATE TABLE monthly_revenue (
    month_year DATE,
    revenue DECIMAL(10,2)
);

INSERT INTO monthly_revenue VALUES
('2024-01-01', 100000),
('2024-02-01', 120000),
('2024-03-01', 110000),
('2024-04-01', 130000),
('2024-05-01', 115000),
('2024-06-01', 125000),
('2024-07-01', 135000);
```

**Solution:**
```sql
SELECT 
    month_year,
    revenue,
    ROUND(AVG(revenue) OVER (
        ORDER BY month_year 
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
    ), 2) as three_month_moving_avg,
    ROUND(AVG(revenue) OVER (
        ORDER BY month_year 
        ROWS BETWEEN 5 PRECEDING AND CURRENT ROW
    ), 2) as six_month_moving_avg,
    -- Calculate trend
    CASE 
        WHEN revenue > AVG(revenue) OVER (
            ORDER BY month_year 
            ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
        ) THEN 'Above Average'
        ELSE 'Below Average'
    END as trend_indicator
FROM monthly_revenue
ORDER BY month_year;
```

---

### 17. 🔍 Find Overlapping Date Ranges

**Scenario:** Find overlapping booking periods in a hotel reservation system.

**Sample Data:**
```sql
CREATE TABLE bookings (
    booking_id INT PRIMARY KEY,
    room_id INT,
    guest_name VARCHAR(100),
    check_in DATE,
    check_out DATE
);

INSERT INTO bookings VALUES
(1, 101, 'John Smith', '2024-01-01', '2024-01-05'),
(2, 101, 'Jane Doe', '2024-01-03', '2024-01-07'),
(3, 101, 'Bob Wilson', '2024-01-08', '2024-01-12'),
(4, 102, 'Alice Brown', '2024-01-01', '2024-01-03'),
(5, 101, 'Charlie Davis', '2024-01-06', '2024-01-09');
```

**Solution:**
```sql
-- Find overlapping bookings
SELECT DISTINCT
    b1.booking_id as booking1,
    b1.guest_name as guest1,
    b1.check_in as checkin1,
    b1.check_out as checkout1,
    b2.booking_id as booking2,
    b2.guest_name as guest2,
    b2.check_in as checkin2,
    b2.check_out as checkout2,
    GREATEST(b1.check_in, b2.check_in) as overlap_start,
    LEAST(b1.check_out, b2.check_out) as overlap_end
FROM bookings b1
JOIN bookings b2 ON b1.room_id = b2.room_id 
    AND b1.booking_id < b2.booking_id
WHERE b1.check_in < b2.check_out 
    AND b1.check_out > b2.check_in
ORDER BY b1.room_id, b1.check_in;

-- Find available dates for a room
SELECT 
    DATE_ADD(b1.check_out, INTERVAL 1 DAY) as available_from,
    DATE_SUB(MIN(b2.check_in), INTERVAL 1 DAY) as available_until
FROM bookings b1
LEFT JOIN bookings b2 ON b1.room_id = b2.room_id 
    AND b2.check_in > b1.check_out
WHERE b1.room_id = 101
GROUP BY b1.booking_id, b1.check_out
HAVING available_from <= available_until;
```

---

### 18. 💹 Stock Price Analysis

**Scenario:** Analyze stock price data to find trends and patterns.

**Sample Data:**
```sql
CREATE TABLE stock_prices (
    price_date DATE,
    symbol VARCHAR(10),
    open_price DECIMAL(8,2),
    close_price DECIMAL(8,2),
    high_price DECIMAL(8,2),
    low_price DECIMAL(8,2),
    volume INT
);

INSERT INTO stock_prices VALUES
('2024-01-01', 'AAPL', 180.00, 185.00, 186.00, 179.50, 1000000),
('2024-01-02', 'AAPL', 185.50, 182.00, 187.00, 181.00, 1200000),
('2024-01-03', 'AAPL', 182.50, 188.00, 189.00, 182.00, 1100000),
('2024-01-04', 'AAPL', 188.50, 186.00, 190.00, 185.50, 1300000),
('2024-01-05', 'AAPL', 186.50, 184.00, 187.00, 183.00, 1150000);
```

**Solution:**
```sql
-- Calculate daily returns and moving averages
SELECT 
    price_date,
    symbol,
    close_price,
    LAG(close_price) OVER (PARTITION BY symbol ORDER BY price_date) as prev_close,
    ROUND(
        (close_price - LAG(close_price) OVER (PARTITION BY symbol ORDER BY price_date)) / 
        LAG(close_price) OVER (PARTITION BY symbol ORDER BY price_date) * 100, 2
    ) as daily_return_pct,
    ROUND(AVG(close_price) OVER (
        PARTITION BY symbol 
        ORDER BY price_date 
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
    ), 2) as ma_3day,
    ROUND(AVG(volume) OVER (
        PARTITION BY symbol 
        ORDER BY price_date 
        ROWS BETWEEN 4 PRECEDING AND CURRENT ROW
    ), 0) as avg_volume_5day,
    CASE 
        WHEN close_price > open_price THEN 'Bullish'
        WHEN close_price < open_price THEN 'Bearish'
        ELSE 'Neutral'
    END as daily_trend
FROM stock_prices
WHERE symbol = 'AAPL'
ORDER BY price_date;

-- Find highest and lowest prices in last N days
SELECT 
    symbol,
    MAX(high_price) as highest_price_30d,
    MIN(low_price) as lowest_price_30d,
    AVG(close_price) as avg_close_30d,
    STDDEV(close_price) as volatility_30d
FROM stock_prices 
WHERE price_date >= DATE_SUB(CURDATE(), INTERVAL 30 DAY)
GROUP BY symbol;
```

---

### 19. 🛍️ E-commerce Analytics: Customer Segmentation

**Scenario:** Segment customers based on RFM analysis (Recency, Frequency, Monetary).

**Sample Data:**
```sql
CREATE TABLE customer_orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    order_total DECIMAL(10,2)
);

INSERT INTO customer_orders VALUES
(1, 101, '2024-01-15', 250.00),
(2, 102, '2024-01-20', 180.00),
(3, 101, '2024-02-01', 320.00),
(4, 103, '2024-02-05', 90.00),
(5, 102, '2024-02-10', 210.00),
(6, 101, '2024-03-01', 150.00),
(7, 104, '2024-03-05', 420.00),
(8, 103, '2024-03-10', 75.00);
```

**Solution:**
```sql
-- RFM Analysis
WITH rfm_calc AS (
    SELECT 
        customer_id,
        DATEDIFF(CURDATE(), MAX(order_date)) as recency_days,
        COUNT(*) as frequency,
        SUM(order_total) as monetary_value,
        AVG(order_total) as avg_order_value
    FROM customer_orders
    GROUP BY customer_id
),
rfm_scores AS (
    SELECT *,
        CASE 
            WHEN recency_days <= 30 THEN 5
            WHEN recency_days <= 60 THEN 4
            WHEN recency_days <= 90 THEN 3
            WHEN recency_days <= 120 THEN 2
            ELSE 1
        END as recency_score,
        CASE 
            WHEN frequency >= 5 THEN 5
            WHEN frequency >= 4 THEN 4
            WHEN frequency >= 3 THEN 3
            WHEN frequency >= 2 THEN 2
            ELSE 1
        END as frequency_score,
        CASE 
            WHEN monetary_value >= 1000 THEN 5
            WHEN monetary_value >= 500 THEN 4
            WHEN monetary_value >= 300 THEN 3
            WHEN monetary_value >= 100 THEN 2
            ELSE 1
        END as monetary_score
    FROM rfm_calc
)
SELECT 
    customer_id,
    recency_days,
    frequency,
    monetary_value,
    avg_order_value,
    CONCAT(recency_score, frequency_score, monetary_score) as rfm_segment,
    CASE 
        WHEN recency_score >= 4 AND frequency_score >= 4 AND monetary_score >= 4 THEN 'Champions'
        WHEN recency_score >= 3 AND frequency_score >= 3 AND monetary_score >= 3 THEN 'Loyal Customers'
        WHEN recency_score >= 4 AND frequency_score <= 2 THEN 'New Customers'
        WHEN recency_score <= 2 AND frequency_score >= 3 AND monetary_score >= 3 THEN 'At Risk'
        WHEN recency_score <= 2 AND frequency_score <= 2 THEN 'Lost Customers'
        ELSE 'Developing'
    END as customer_segment
FROM rfm_scores
ORDER BY monetary_value DESC;
```

---

### 20. 🏥 Healthcare: Patient Visit Analysis

**Scenario:** Analyze patient visit patterns and identify frequent visitors.

**Sample Data:**
```sql
CREATE TABLE patient_visits (
    visit_id INT PRIMARY KEY,
    patient_id INT,
    visit_date DATE,
    diagnosis_code VARCHAR(10),
    doctor_id INT,
    visit_cost DECIMAL(8,2)
);

INSERT INTO patient_visits VALUES
(1, 1001, '2024-01-15', 'A01', 201, 150.00),
(2, 1002, '2024-01-16', 'B02', 202, 200.00),
(3, 1001, '2024-01-20', 'A01', 201, 150.00),
(4, 1003, '2024-01-22', 'C03', 203, 300.00),
(5, 1001, '2024-02-01', 'A01', 201, 150.00),
(6, 1002, '2024-02-05', 'B02', 202, 200.00),
(7, 1004, '2024-02-10', 'D04', 204, 250.00),
(8, 1001, '2024-02-15', 'A02', 201, 180.00);
```

**Solution:**
```sql
-- Patient visit analysis
WITH patient_stats AS (
    SELECT 
        patient_id,
        COUNT(*) as total_visits,
        COUNT(DISTINCT diagnosis_code) as unique_diagnoses,
        SUM(visit_cost) as total_cost,
        AVG(visit_cost) as avg_visit_cost,
        MIN(visit_date) as first_visit,
        MAX(visit_date) as last_visit,
        DATEDIFF(MAX(visit_date), MIN(visit_date)) as days_between_first_last
    FROM patient_visits
    GROUP BY patient_id
),
visit_frequency AS (
    SELECT 
        patient_id,
        visit_date,
        LAG(visit_date) OVER (PARTITION BY patient_id ORDER BY visit_date) as prev_visit,
        DATEDIFF(visit_date, LAG(visit_date) OVER (PARTITION BY patient_id ORDER BY visit_date)) as days_since_last_visit
    FROM patient_visits
)
SELECT 
    ps.patient_id,
    ps.total_visits,
    ps.unique_diagnoses,
    ps.total_cost,
    ROUND(ps.avg_visit_cost, 2) as avg_visit_cost,
    ps.first_visit,
    ps.last_visit,
    ROUND(AVG(vf.days_since_last_visit), 1) as avg_days_between_visits,
    CASE 
        WHEN ps.total_visits >= 5 AND AVG(vf.days_since_last_visit) <= 30 THEN 'Frequent Visitor'
        WHEN ps.total_visits >= 3 AND AVG(vf.days_since_last_visit) <= 60 THEN 'Regular Patient'
        WHEN ps.total_visits = 1 THEN 'New Patient'
        ELSE 'Occasional Patient'
    END as patient_category,
    -- Identify patients with same diagnosis multiple times
    CASE 
        WHEN ps.total_visits > ps.unique_diagnoses * 1.5 THEN 'Chronic Condition Likely'
        ELSE 'Varied Conditions'
    END as condition_pattern
FROM patient_stats ps
LEFT JOIN visit_frequency vf ON ps.patient_id = vf.patient_id
GROUP BY ps.patient_id, ps.total_visits, ps.unique_diagnoses, ps.total_cost, 
         ps.avg_visit_cost, ps.first_visit, ps.last_visit
ORDER BY ps.total_visits DESC, ps.total_cost DESC;

-- Find most common diagnosis patterns
SELECT 
    diagnosis_code,
    COUNT(*) as frequency,
    COUNT(DISTINCT patient_id) as unique_patients,
    AVG(visit_cost) as avg_cost,
    COUNT(DISTINCT doctor_id) as doctors_treating
FROM patient_visits
GROUP BY diagnosis_code
ORDER BY frequency DESC;

-- Identify patients with potential readmission issues
SELECT 
    patient_id,
    diagnosis_code,
    COUNT(*) as visits_for_diagnosis,
    MIN(visit_date) as first_occurrence,
    MAX(visit_date) as last_occurrence,
    DATEDIFF(MAX(visit_date), MIN(visit_date)) as days_span
FROM patient_visits
GROUP BY patient_id, diagnosis_code
HAVING COUNT(*) > 2  -- More than 2 visits for same diagnosis
ORDER BY patient_id, visits_for_diagnosis DESC;
```

---

## 🎯 Bonus Complex Scenarios

### 21. 🔄 Data Migration Validation

**Scenario:** Validate data after migration between two systems.

**Solution:**
```sql
-- Compare record counts
SELECT 'source_table' as table_name, COUNT(*) as record_count FROM source_employees
UNION ALL
SELECT 'target_table' as table_name, COUNT(*) as record_count FROM target_employees;

-- Find missing records in target
SELECT s.employee_id, s.name, 'Missing in target' as status
FROM source_employees s
LEFT JOIN target_employees t ON s.employee_id = t.employee_id
WHERE t.employee_id IS NULL

UNION ALL

-- Find extra records in target
SELECT t.employee_id, t.name, 'Extra in target' as status
FROM target_employees t
LEFT JOIN source_employees s ON t.employee_id = s.employee_id
WHERE s.employee_id IS NULL;

-- Validate data integrity
SELECT 
    s.employee_id,
    CASE WHEN s.name != t.name THEN 'Name mismatch' END as name_check,
    CASE WHEN s.salary != t.salary THEN 'Salary mismatch' END as salary_check,
    CASE WHEN s.department != t.department THEN 'Department mismatch' END as dept_check
FROM source_employees s
JOIN target_employees t ON s.employee_id = t.employee_id
WHERE s.name != t.name OR s.salary != t.salary OR s.department != t.department;
```

---

## 🏆 Summary and Key Takeaways

### 📊 Problem Categories Covered:

1. **🔢 Ranking & Analytics**
   - Nth highest values
   - Window functions usage
   - Statistical calculations

2. **🔍 Data Discovery**
   - Finding missing data
   - Duplicate detection
   - Gap analysis

3. **📈 Business Intelligence**
   - Growth calculations
   - Trend analysis
   - Customer segmentation

4. **⚡ Performance**
   - Query optimization
   - Index usage
   - Large dataset handling

5. **🔄 Data Manipulation**
   - Complex updates
   - Pivot operations
   - Hierarchical queries

### 💡 Key SQL Techniques Used:

- **Window Functions:** `ROW_NUMBER()`, `RANK()`, `LAG()`, `LEAD()`
- **Common Table Expressions (CTEs):** For complex multi-step queries
- **Recursive Queries:** For hierarchical data
- **Conditional Logic:** `CASE WHEN` statements
- **Date Functions:** `DATEDIFF()`, `DATE_ADD()`, `DATE_SUB()`
- **Aggregate Functions:** `SUM()`, `COUNT()`, `AVG()` with `OVER` clause
- **Subqueries:** Correlated and non-correlated
- **JOIN Operations:** All types with proper conditions

### 🎯 Interview Tips:

1. **📝 Always ask clarifying questions** about the business requirements
2. **🚀 Start with a simple solution**, then optimize
3. **📊 Consider performance implications** for large datasets
4. **🔍 Think about edge cases** (NULL values, empty results)
5. **💬 Explain your thought process** while solving
6. **⚡ Know multiple approaches** to solve the same problem
7. **🔧 Consider indexing strategies** for optimization

### 🏃‍♂️ Practice Approach:

1. **Understand the business scenario** thoroughly
2. **Design sample data** that covers edge cases
3. **Write the query step by step**
4. **Test with different data sets**
5. **Optimize for performance**
6. **Document assumptions and limitations**

---

*These scenario-based questions represent real-world problems you'll encounter in MySQL interviews. Practice them thoroughly and understand the underlying concepts rather than memorizing solutions. Good luck! 🍀*