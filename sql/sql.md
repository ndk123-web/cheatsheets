# SQL Cheatsheet (PostgreSQL)

## References
- [PostgreSQL Documentation](https://www.postgresql.org/docs/current/)
- [SELECT](https://www.postgresql.org/docs/current/sql-select.html) · [INSERT](https://www.postgresql.org/docs/current/sql-insert.html) · [UPDATE](https://www.postgresql.org/docs/current/sql-update.html) · [DELETE](https://www.postgresql.org/docs/current/sql-delete.html)
- [Aggregate Functions](https://www.postgresql.org/docs/current/functions-aggregate.html) · [Window Functions](https://www.postgresql.org/docs/current/functions-window.html)
- [Date/Time Functions](https://www.postgresql.org/docs/current/functions-datetime.html) · [Data Types](https://www.postgresql.org/docs/current/datatype.html)
- [WITH Queries / CTEs](https://www.postgresql.org/docs/current/queries-with.html) · [Indexes](https://www.postgresql.org/docs/current/indexes.html)

---

## 1. Query Fundamentals
### SELECT
```sql
SELECT name, salary FROM employees;
SELECT * FROM employees;
```

### WHERE and operators
```sql
SELECT * FROM employees WHERE department = 'IT' AND salary > 80000;
SELECT * FROM employees WHERE salary BETWEEN 50000 AND 80000;
SELECT * FROM employees WHERE city IN ('Mumbai', 'Pune');
SELECT * FROM employees WHERE name LIKE 'A%';
SELECT * FROM employees WHERE name ILIKE 'a%'; -- PostgreSQL case-insensitive LIKE
SELECT * FROM employees WHERE salary >= 60000 AND city <> 'Delhi';
```

### NULL, sorting, and pagination
```sql
SELECT * FROM employees WHERE salary IS NULL;
SELECT * FROM employees WHERE salary IS NOT NULL;
SELECT COALESCE(salary, 0) FROM employees; -- fallback value; does not update stored data
SELECT * FROM employees ORDER BY salary DESC, name ASC;
SELECT * FROM employees ORDER BY id LIMIT 10 OFFSET 20;
```

## 2. CRUD and DDL
### INSERT
```sql
INSERT INTO employees (id, name, department, salary, city)
VALUES (7, 'Karan', 'IT', 75000, 'Mumbai');

INSERT INTO employees (id, name, department, salary, city)
VALUES
    (8, 'Riya', 'HR', 65000, 'Pune'),
    (9, 'Dev', 'IT', 95000, 'Delhi');

INSERT INTO archived_employees (id, name, department)
SELECT id, name, department FROM employees WHERE status = 'inactive';
```

### UPDATE and DELETE
```sql
UPDATE employees SET salary = 80000 WHERE id = 1;
UPDATE employees SET salary = salary * 1.10 WHERE department = 'IT';
DELETE FROM employees WHERE id = 7;
TRUNCATE TABLE employees; -- remove all rows, keep structure
DROP TABLE employees; -- remove table and structure
```

### UPSERT (PostgreSQL)
```sql
INSERT INTO employees (id, name, department, salary)
VALUES (1, 'Amit', 'IT', 90000)
ON CONFLICT (id)
DO UPDATE SET salary = EXCLUDED.salary, name = EXCLUDED.name;
```

### Transactions
```sql
BEGIN;
UPDATE accounts SET balance = balance - 1000 WHERE id = 1;
UPDATE accounts SET balance = balance + 1000 WHERE id = 2;
COMMIT;
-- Use ROLLBACK instead of COMMIT to cancel uncommitted changes.
```

## 3. Aggregate Functions
```sql
SELECT
    COUNT(*) AS total_rows,
    COUNT(salary) AS non_null_salaries,
    COUNT(DISTINCT department) AS distinct_departments,
    SUM(salary) AS total_salary,
    AVG(salary) AS average_salary,
    MIN(salary) AS minimum_salary,
    MAX(salary) AS maximum_salary
FROM employees;
```
- `COUNT(*)` counts rows; `COUNT(column)` ignores NULL.
- `SUM`, `AVG`, `MIN`, and `MAX` ignore NULL values.

## 4. GROUP BY and HAVING
```sql
SELECT department, AVG(salary) AS average_salary, COUNT(*) AS employee_count
FROM employees
GROUP BY department;

SELECT department, AVG(salary) AS average_salary
FROM employees
WHERE salary IS NOT NULL -- filters rows before grouping
GROUP BY department
HAVING AVG(salary) > 60000; -- filters groups after aggregation
```

## 5. Joins
Assume `employees(id, name, department_id, manager_id)` and `departments(id, department_name)`.
### INNER, LEFT, RIGHT, FULL OUTER, and CROSS JOIN
```sql
-- Matching rows only
SELECT e.name, d.department_name FROM employees e
INNER JOIN departments d ON e.department_id = d.id;

-- All left-side employees, with matching departments where available
SELECT e.name, d.department_name FROM employees e
LEFT JOIN departments d ON e.department_id = d.id;

-- All right-side departments, with matching employees where available
SELECT e.name, d.department_name FROM employees e
RIGHT JOIN departments d ON e.department_id = d.id;

-- All rows from both sides; unmatched columns become NULL
SELECT e.name, d.department_name FROM employees e
FULL OUTER JOIN departments d ON e.department_id = d.id;

-- Cartesian product: every employee paired with every department
SELECT e.name, d.department_name FROM employees e
CROSS JOIN departments d;
```

### SELF JOIN and unmatched rows
```sql
SELECT e.name AS employee, m.name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.id;

SELECT e.*
FROM employees e
LEFT JOIN departments d ON e.department_id = d.id
WHERE d.id IS NULL; -- employees without a matching department
```

## 6. Subqueries
```sql
-- Scalar subquery: compare against one value
SELECT name, salary FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);

-- IN: match any value from the subquery result
SELECT name FROM employees
WHERE department_id IN (SELECT id FROM departments);

-- EXISTS: check whether at least one matching row exists
SELECT d.department_name FROM departments d
WHERE EXISTS (
    SELECT 1 FROM employees e WHERE e.department_id = d.id
);

-- NOT EXISTS: find departments with no employees
SELECT d.department_name FROM departments d
WHERE NOT EXISTS (
    SELECT 1 FROM employees e WHERE e.department_id = d.id
);

-- Correlated subquery: inner query references the current outer row
SELECT e.name, e.salary, e.department_id
FROM employees e
WHERE e.salary > (
    SELECT AVG(e2.salary) FROM employees e2
    WHERE e2.department_id = e.department_id
);
```
- `EXISTS` checks row existence; the selected value (often `1`) is irrelevant.
- Prefer `NOT EXISTS` over `NOT IN` when the subquery may contain NULL.

## 7. Common Table Expressions (CTEs)
### Basic and chained CTEs
```sql
WITH high_salary AS (
    SELECT * FROM employees WHERE salary > 70000
)
SELECT * FROM high_salary;

WITH department_average AS (
    SELECT department_id, AVG(salary) AS avg_salary
    FROM employees GROUP BY department_id
),
above_average AS (
    SELECT e.name, e.salary, e.department_id
    FROM employees e
    JOIN department_average d ON d.department_id = e.department_id
    WHERE e.salary > d.avg_salary
)
SELECT * FROM above_average;
```

### Recursive CTE
```sql
-- Generate numbers 1 through 5
WITH RECURSIVE numbers AS (
    SELECT 1 AS n
    UNION ALL
    SELECT n + 1 FROM numbers WHERE n < 5
)
SELECT n FROM numbers;

-- Traverse an employee-manager hierarchy
WITH RECURSIVE employee_tree AS (
    SELECT id, name, manager_id, 1 AS level
    FROM employees WHERE manager_id IS NULL
    UNION ALL
    SELECT e.id, e.name, e.manager_id, t.level + 1
    FROM employees e
    JOIN employee_tree t ON e.manager_id = t.id
)
SELECT * FROM employee_tree ORDER BY level, id;
```

## 8. Set Operations
```sql
-- Combine results and remove duplicates
SELECT city FROM employees UNION SELECT city FROM customers;

-- Combine results and keep duplicates
SELECT city FROM employees UNION ALL SELECT city FROM customers;

-- Rows common to both results
SELECT city FROM employees INTERSECT SELECT city FROM customers;

-- Rows in first result but not in second (PostgreSQL)
SELECT city FROM employees EXCEPT SELECT city FROM customers;
```
- Both queries need the same number of columns with compatible data types.

## 9. Window Functions
### PARTITION BY, ROW_NUMBER, RANK, and DENSE_RANK
```sql
SELECT
    name, department, salary,
    AVG(salary) OVER (PARTITION BY department) AS department_average,
    ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) AS row_num,
    RANK() OVER (ORDER BY salary DESC) AS salary_rank,
    DENSE_RANK() OVER (ORDER BY salary DESC) AS dense_rank
FROM employees;
```
- `GROUP BY` collapses rows into groups; window functions retain individual rows.
- `ROW_NUMBER()` assigns unique sequence numbers; `RANK()` leaves gaps after ties; `DENSE_RANK()` does not.

### Top N per group
```sql
WITH ranked_employees AS (
    SELECT e.*,
           ROW_NUMBER() OVER (
               PARTITION BY department_id ORDER BY salary DESC, id
           ) AS rn
    FROM employees e
)
SELECT * FROM ranked_employees WHERE rn <= 2;
```

### LAG, LEAD, and running total
```sql
SELECT
    id, name, salary,
    LAG(salary) OVER (ORDER BY id) AS previous_salary,
    LEAD(salary) OVER (ORDER BY id) AS next_salary
FROM employees;

SELECT id, salary,
       SUM(salary) OVER (
           ORDER BY id
           ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
       ) AS running_total
FROM employees;
```

### Moving average
```sql
SELECT order_date, amount,
       AVG(amount) OVER (
           ORDER BY order_date
           ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
       ) AS moving_average
FROM orders;
```

## 10. Date and Time
### Common types
```sql
DATE
TIME
TIMESTAMP
TIMESTAMPTZ
INTERVAL
```

### Current time, extraction, and arithmetic
```sql
SELECT CURRENT_DATE, CURRENT_TIME, CURRENT_TIMESTAMP, NOW();

SELECT
    EXTRACT(YEAR FROM order_date) AS year,
    EXTRACT(MONTH FROM order_date) AS month,
    EXTRACT(DAY FROM order_date) AS day
FROM orders;

SELECT CURRENT_DATE + INTERVAL '7 days';
SELECT CURRENT_DATE - INTERVAL '1 month';
SELECT NOW() + INTERVAL '2 hours';
```

### Timestamp range filter
```sql
-- All orders in January 2026; half-open range
SELECT * FROM orders
WHERE order_date >= TIMESTAMP '2026-01-01'
  AND order_date < TIMESTAMP '2026-02-01';
```

## 11. Data Types and Useful Functions
### Common PostgreSQL types
```sql
INTEGER
BIGINT
NUMERIC(10, 2)
REAL
DOUBLE PRECISION
VARCHAR(255)
TEXT
BOOLEAN
DATE
TIMESTAMP
TIMESTAMPTZ
UUID
JSONB
```

### String and numeric functions
```sql
SELECT UPPER('hello'), LOWER('HELLO'), LENGTH('Navnath');
SELECT TRIM('  hello  '), CONCAT('Hello ', 'World');
SELECT SUBSTRING('PostgreSQL' FROM 1 FOR 4);
SELECT ROUND(12.567, 2), CEIL(12.2), FLOOR(12.8), ABS(-50);
```

### NULL, casting, and conditional expressions
```sql
SELECT COALESCE(salary, 0) FROM employees;
SELECT NULLIF(10, 10);
SELECT revenue / NULLIF(quantity, 0) FROM sales; -- avoid division by zero
SELECT CAST('123' AS INTEGER);
SELECT '123'::INTEGER; -- PostgreSQL cast shorthand

SELECT name, salary,
       CASE
           WHEN salary IS NULL THEN 'Unknown'
           WHEN salary >= 80000 THEN 'High'
           WHEN salary >= 60000 THEN 'Medium'
           ELSE 'Low'
       END AS salary_category
FROM employees;
```

### JSONB basics
```sql
SELECT profile->>'name' FROM users; -- JSON field as text
SELECT profile->'address'->>'city' FROM users; -- nested field as text
SELECT * FROM users WHERE profile @> '{"role": "admin"}'::jsonb;
```

## 12. Common Interview Query Patterns
### Find duplicate emails
```sql
SELECT email, COUNT(*) AS occurrences
FROM users
GROUP BY email
HAVING COUNT(*) > 1;
```

### Second-highest distinct salary
```sql
SELECT MAX(salary) AS second_highest_salary
FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);
```

### Nth-highest distinct salary
```sql
WITH ranked_salaries AS (
    SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employees
    WHERE salary IS NOT NULL
)
SELECT DISTINCT salary FROM ranked_salaries WHERE rnk = 3; -- replace 3 with N
```

### Highest salary in each department
```sql
SELECT department_id, MAX(salary) AS highest_salary
FROM employees
GROUP BY department_id;
```

### Count rows conditionally
```sql
-- PostgreSQL FILTER syntax
SELECT department_id,
       COUNT(*) AS total_employees,
       COUNT(*) FILTER (WHERE salary >= 80000) AS high_salary_count
FROM employees
GROUP BY department_id;

-- Portable conditional aggregation
SELECT department_id,
       SUM(CASE WHEN salary >= 80000 THEN 1 ELSE 0 END) AS high_salary_count
FROM employees
GROUP BY department_id;
```

### Delete duplicates and keep the smallest ID
```sql
WITH ranked_users AS (
    SELECT id,
           ROW_NUMBER() OVER (PARTITION BY email ORDER BY id) AS rn
    FROM users
)
DELETE FROM users
WHERE id IN (SELECT id FROM ranked_users WHERE rn > 1);
```

### Keyset pagination
```sql
SELECT * FROM employees
WHERE id > 20
ORDER BY id
LIMIT 10;
```

## 13. Logical Query Processing Order
```text
FROM / JOIN
    ↓
WHERE
    ↓
GROUP BY
    ↓
HAVING
    ↓
SELECT
    ↓
ORDER BY
    ↓
LIMIT / OFFSET
```

## 14. Quick Reference
| Requirement | SQL pattern |
|---|---|
| Filter rows | `WHERE` |
| Filter groups | `HAVING` |
| Combine tables | `JOIN` |
| Check matching rows | `EXISTS` |
| Find missing matches | `NOT EXISTS` |
| Name a query result | `WITH` (CTE) |
| Hierarchical traversal | Recursive CTE |
| Combine result sets | `UNION`, `INTERSECT`, `EXCEPT` |
| Number rows | `ROW_NUMBER()` |
| Rank with ties | `RANK()`, `DENSE_RANK()` |
| Previous / next value | `LAG()`, `LEAD()` |
| Running calculation | Window aggregate |
| NULL fallback | `COALESCE()` |
| Handle division by zero | `NULLIF()` |
| Conditional value | `CASE` |
| Conditional aggregate | `FILTER` or `SUM(CASE...)` |

