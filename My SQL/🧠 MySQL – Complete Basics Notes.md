## 📌 1. Database Operations

### ➤ Create Database

CREATE DATABASE company;

### ➤ Use Database

USE company;

### ➤ Drop Database

DROP DATABASE company;

---

## 📌 2. Table Operations

### ➤ Create Table

CREATE TABLE employees (  
    employee_id INT,  
    first_name VARCHAR(50),  
    last_name VARCHAR(50),  
    salary INT  
);

---

### ➤ Rename Table

RENAME TABLE employees TO workers;

---

### ➤ Drop Table

DROP TABLE workers;

---

## 📌 3. ALTER Table Operations

### ➤ Add Column

ALTER TABLE workers  
ADD email VARCHAR(100);

---

### ➤ Modify Column (Datatype / Structure)

ALTER TABLE workers  
MODIFY salary BIGINT;

---

### ➤ Move Column AFTER another column

ALTER TABLE workers  
MODIFY email VARCHAR(100) AFTER last_name;

---

### ➤ Move Column to FIRST

ALTER TABLE workers  
MODIFY employee_id INT FIRST;

---

### ➤ Drop Column

ALTER TABLE workers  
DROP COLUMN email;

---

## 📌 4. Data Manipulation (VERY IMPORTANT ⭐)

---

### ➤ Insert Data

INSERT INTO workers (employee_id, first_name, last_name, salary)  
VALUES (1, 'Ajay', 'Kumar', 50000);

---

### ➤ Insert Multiple Rows

INSERT INTO workers VALUES  
(2, 'Rahul', 'Sharma', 60000),  
(3, 'Priya', 'Singh', 55000);

---

### ➤ Select All Data

SELECT * FROM workers;

---

### ➤ Select Specific Columns

SELECT first_name, salary FROM workers;

---

### ➤ WHERE Condition

SELECT * FROM workers  
WHERE salary > 55000;

---

### ➤ UPDATE Data

UPDATE workers  
SET salary = 70000  
WHERE employee_id = 1;

---

### ➤ DELETE Data

DELETE FROM workers  
WHERE employee_id = 2;

----- Create Database
CREATE DATABASE company;

-- Use Database
USE company;

-- Create Table
CREATE TABLE employees (
    employee_id INT,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    salary INT
);

-- Rename Table
RENAME TABLE employees TO workers;

-- Add Column
ALTER TABLE workers ADD email VARCHAR(100);

-- Insert Data
INSERT INTO workers VALUES
(1, 'Ajay', 'Kumar', 50000, 'ajay@mail.com'),
(2, 'Rahul', 'Sharma', 60000, 'rahul@mail.com');

-- View Data
SELECT * FROM workers;

-- Update Data
UPDATE workers SET salary = 70000 WHERE employee_id = 1;

-- Delete Row
DELETE FROM workers WHERE employee_id = 2;

-- Drop Column
ALTER TABLE workers DROP COLUMN email;

-- Drop Table
DROP TABLE workers;

-- Drop Database
DROP DATABASE company;

---

## 📝 Key Concepts Summary

- `CREATE` → create database/table
- `USE` → select database
- `RENAME` → change table name
- `ALTER` → modify structure
- `INSERT` → add data
- `SELECT` → retrieve data
- `UPDATE` → modify data
- `DELETE` → remove rows
- `DROP` → delete permanently

---

## ⚡ Ultimate Quick Revision

- Create DB → `CREATE DATABASE`
- Use DB → `USE`
- Create Table → `CREATE TABLE`
- Rename Table → `RENAME TABLE`
- Add Column → `ALTER + ADD`
- Modify Column → `ALTER + MODIFY`
- Delete Column → `ALTER + DROP COLUMN`
- Insert → `INSERT INTO`
- Select → `SELECT`
- Update → `UPDATE`
- Delete → `DELETE`

---

## 🚀 Interview Tips

- Always remember **order of execution**
- Practice with **real tables (employees, students)**
- Focus more on:
    - `SELECT + WHERE`
    - `UPDATE`
    - `DELETE`