# SQL Cheat Sheet

### Common SQL Commands

| Command    | Description                                          |
| ---------- | ---------------------------------------------------- |
| `SELECT`   | Retrieve data from a table                           |
| `INSERT`   | Add new data into a table                            |
| `UPDATE`   | Modify existing data                                 |
| `DELETE`   | Remove data from a table                             |
| `CREATE`   | Create database objects (tables, indexes, views)     |
| `DROP`     | Delete database objects                              |
| `ALTER`    | Modify database structure                            |
| `TRUNCATE` | Remove all records from a table (faster than DELETE) |
| `USE`      | Select a database                                    |

---

##  Database & Table Management

### Create Database

```sql
CREATE DATABASE db_name;
```

### Use Database

```sql
USE db_name;
```

### Create Table

```sql
CREATE TABLE table_name (
    column1 datatype [constraints],
    column2 datatype [constraints],
    ...
);
```

### Drop Table

```sql
DROP TABLE table_name;
```

### Alter Table

Add a column:

```sql
ALTER TABLE table_name ADD column_name datatype;
```

Modify column:

```sql
ALTER TABLE table_name MODIFY column_name new_datatype;
```

Drop column:

```sql
ALTER TABLE table_name DROP COLUMN column_name;
```

---

##  Data Manipulation

### Insert Data

```sql
INSERT INTO table_name (column1, column2) VALUES (value1, value2);
```

### Update Data

```sql
UPDATE table_name SET column1 = value1 WHERE condition;
```

### Delete Data

```sql
DELETE FROM table_name WHERE condition;
```

### Truncate Table

```sql
TRUNCATE TABLE table_name;
```

---

## Data Retrieval

### Select Data

```sql
SELECT column1, column2 FROM table_name;
SELECT * FROM table_name;
```

### Filtering Results

```sql
SELECT * FROM table_name WHERE condition;
```

### Logical Operators

```sql
SELECT * FROM table_name WHERE column1 = value AND column2 <> value;
SELECT * FROM table_name WHERE column1 = value OR column2 = value;
SELECT * FROM table_name WHERE NOT condition;
```

### Pattern Matching

```sql
SELECT * FROM table_name WHERE column LIKE 'A%';
SELECT * FROM table_name WHERE column LIKE '_a%';
```

### Sorting

```sql
SELECT * FROM table_name ORDER BY column1 ASC, column2 DESC;
```

### Limiting Results

```sql
SELECT * FROM table_name LIMIT 10 OFFSET 5;
```

---

## Functions

### Aggregate Functions

| Function  | Description      |
| --------- | ---------------- |
| `COUNT()` | Counts rows      |
| `SUM()`   | Adds up values   |
| `AVG()`   | Computes average |
| `MIN()`   | Finds minimum    |
| `MAX()`   | Finds maximum    |

### Grouping

```sql
SELECT column, COUNT(*) FROM table_name GROUP BY column;
```

### Filtering Groups

```sql
SELECT column, COUNT(*) FROM table_name GROUP BY column HAVING COUNT(*) > 1;
```

---

## Joins

### Inner Join

```sql
SELECT a.column1, b.column2
FROM table1 a
INNER JOIN table2 b ON a.id = b.foreign_id;
```

### Left Join

```sql
SELECT a.column1, b.column2
FROM table1 a
LEFT JOIN table2 b ON a.id = b.foreign_id;
```

### Right Join

```sql
SELECT a.column1, b.column2
FROM table1 a
RIGHT JOIN table2 b ON a.id = b.foreign_id;
```

### Full Outer Join

```sql
SELECT a.column1, b.column2
FROM table1 a
FULL OUTER JOIN table2 b ON a.id = b.foreign_id;
```

---

## Subqueries

### Scalar Subquery

```sql
SELECT column1 FROM table WHERE column2 = (SELECT MAX(column2) FROM table);
```

### Correlated Subquery

```sql
SELECT name FROM employees e
WHERE salary > (SELECT AVG(salary) FROM employees WHERE department_id = e.department_id);
```

---

##  Constraints

| Constraint    | Description                   |
| ------------- | ----------------------------- |
| `PRIMARY KEY` | Unique identifier             |
| `FOREIGN KEY` | Links to another table        |
| `NOT NULL`    | Disallows NULLs               |
| `UNIQUE`      | Enforces unique values        |
| `CHECK`       | Restricts values              |
| `DEFAULT`     | Default value if not provided |

```sql
CREATE TABLE example (
    id INT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    age INT CHECK (age >= 18),
    role VARCHAR(50) DEFAULT 'user'
);
```

---

## Views

### Create View

```sql
CREATE VIEW view_name AS
SELECT column1, column2 FROM table_name WHERE condition;
```

### Drop View

```sql
DROP VIEW view_name;
```

---

##  Users and Permissions (MySQL)

### Create User

```sql
CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';
```

### Grant Privileges

```sql
GRANT ALL PRIVILEGES ON db_name.* TO 'username'@'localhost';
FLUSH PRIVILEGES;
```

### Revoke Privileges

```sql
REVOKE ALL PRIVILEGES ON db_name.* FROM 'username'@'localhost';
```

---

## Indexes

### Create Index

```sql
CREATE INDEX idx_name ON table_name (column1);
```

### Drop Index

```sql
DROP INDEX idx_name ON table_name;
```

---

##  Transactions

```sql
START TRANSACTION;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
-- Or ROLLBACK;
```

---

## Data Types (MySQL)

| Type           | Description                       |
| -------------- | --------------------------------- |
| `INT`          | Integer                           |
| `FLOAT/DOUBLE` | Floating point numbers            |
| `DECIMAL(p,s)` | Fixed-point numbers               |
| `VARCHAR(n)`   | Variable-length string            |
| `CHAR(n)`      | Fixed-length string               |
| `TEXT`         | Large text field                  |
| `DATE`         | Date (YYYY-MM-DD)                 |
| `DATETIME`     | Date and time                     |
| `TIMESTAMP`    | UNIX timestamp                    |
| `BOOLEAN`      | True/False (alias for TINYINT(1)) |

---

##  Optimization Tips

- Use indexes on columns used in `WHERE`, `JOIN`, `ORDER BY`
- Avoid `SELECT *`
- Use `EXPLAIN` to analyze queries
- Normalize data but consider denormalization for read-heavy workloads
- Limit subqueries in `SELECT` or `WHERE`

###  Optimization Examples (Before & After)

#### 1. Avoiding SELECT \*

Bad:

```sql
SELECT * FROM users;
```

Fixed:

```sql
SELECT id, name, email FROM users;
```

#### 2. Adding Index for Faster WHERE

Bad:

```sql
SELECT * FROM orders WHERE customer_id = 123;
```

Fixed:

```sql
-- Add index first:
CREATE INDEX idx_customer_id ON orders(customer_id);
SELECT id, total FROM orders WHERE customer_id = 123;
```

#### 3. Replacing Subquery with JOIN

Bad:

```sql
SELECT name FROM employees WHERE department_id IN (SELECT id FROM departments WHERE name = 'HR');
```

Fixed:

```sql
SELECT e.name FROM employees e
JOIN departments d ON e.department_id = d.id
WHERE d.name = 'HR';
```

#### 4. Filtering Before JOIN (Push Down Filter)

Bad:

```sql
SELECT * FROM sales s
JOIN customers c ON s.customer_id = c.id
WHERE c.region = 'West';
```

Fixed:

```sql
SELECT * FROM sales s
JOIN (SELECT * FROM customers WHERE region = 'West') c ON s.customer_id = c.id;
```

#### 5. Avoiding Functions in WHERE Clause

Bad:

```sql
SELECT * FROM products WHERE YEAR(created_at) = 2024;
```

Fixed:

```sql
SELECT * FROM products WHERE created_at BETWEEN '2024-01-01' AND '2024-12-31';
```

---

##  Public Documentation Resources

- [MySQL Documentation](https://dev.mysql.com/doc/)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [SQLite Documentation](https://sqlite.org/docs.html)
- [SQL Server Docs](https://learn.microsoft.com/en-us/sql/?view=sql-server-ver15)
- [MariaDB Documentation](https://mariadb.com/kb/en/documentation/)
- [Oracle SQL Docs](https://docs.oracle.com/en/database/oracle/oracle-database/)

---

**Note:** SQL syntax varies slightly between dialects (MySQL, PostgreSQL, SQL Server, etc.). Always refer to your DBMS documentation for specifics.
This cheat sheet covers essential SQL commands, it is basic so there are many things can be added for different purposes, Contributions welcome on GitHub! Enjoy! - Yetkin

