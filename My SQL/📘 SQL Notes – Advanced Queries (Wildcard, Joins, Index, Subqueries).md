## 🔍 Wildcard Characters (`LIKE`)

### 📌 Concept

Used to search patterns in strings.

### 🔹 `%` → Any number of characters

SELECT * FROM employees  
WHERE first_name LIKE 'A%';

👉 Names starting with **A**

SELECT * FROM employees  
WHERE join_date LIKE '2023%';

👉 Dates in **year 2023**

---

### 🔹 `_` → Exactly one character

SELECT * FROM employees  
WHERE job LIKE '_ern';

👉 Matches: **Intern, Kern**

SELECT * FROM employees  
WHERE join_date LIKE '____-01-__';

👉 Any year, **January month**

SELECT * FROM employees  
WHERE first_name LIKE '_j%';

👉 Second letter is **j**

---

## 📊 ORDER BY & LIMIT

### 📌 Concept

Sort and limit results.

SELECT * FROM employees  
ORDER BY last_name;

👉 Default = ASC

SELECT * FROM transactions  
ORDER BY amount ASC, customer_id DESC;

👉 Multi-column sorting

---

### 🔹 LIMIT

SELECT * FROM customers LIMIT 1;

👉 First row

SELECT * FROM customers  
ORDER BY first_name DESC LIMIT 2;

👉 Top 2 in descending order

SELECT * FROM customers LIMIT 2,1;

👉 Skip 2 rows, fetch 1

---

## 🔗 UNION vs UNION ALL

### 📌 Concept

Combine results of multiple queries

SELECT * FROM customers  
UNION  
SELECT * FROM income;

👉 Removes duplicates

SELECT first_name,last_name FROM customers  
UNION ALL  
SELECT first_name,last_name FROM employees;

👉 Keeps duplicates (faster)

---

## 🔁 SELF JOIN

### 📌 Concept

Join a table with itself

SELECT   
  CONCAT(a.customer_id, ' ', a.first_name, ' ', a.last_name) AS customer,  
  CONCAT(b.first_name, ' ', b.last_name) AS referred_by  
FROM customers AS a  
INNER JOIN customers AS b  
ON a.referral_id = b.customer_id;

👉 Finds **who referred whom**

---

### 🔹 Employee–Supervisor Example

SELECT   
  CONCAT(a.employee_id, ' ', a.first_name, ' ', a.last_name) AS employee,  
  a.supervisor_id,  
  CONCAT(b.first_name, ' ', b.last_name) AS supervisor  
FROM employees AS a  
INNER JOIN employees AS b  
ON a.supervisor_id = b.employee_id;

👉 Links employee → supervisor

---

## 👁️ VIEW

### 📌 Concept

Virtual table (stored query)

CREATE VIEW emails AS  
SELECT customer_email FROM customers;

👉 Use like:

SELECT * FROM emails;

---

## ⚡ INDEX (Performance Optimization)

### 📌 Concept

Speeds up search using **B-Tree**

CREATE INDEX last_name_first_name_idx  
ON customers(last_name, first_name);

---

### 🔥 Important Rule

👉 **Left-most Prefix Rule (MySQL)**

✔ Works:

WHERE last_name = 'K'

✔ Works:

WHERE last_name = 'K' AND first_name = 'Ajay'

❌ Not efficient:

WHERE first_name = 'Ajay'

---

### 🔹 Other Commands

SHOW INDEXES FROM customers;  
  
ALTER TABLE customers DROP INDEX last_name_idx;

---

## 🧠 SUBQUERIES

### 📌 Concept

Query inside another query

---

### 🔹 Average Salary (All Rows)

SELECT first_name, last_name,  
(SELECT AVG(hourly_pay) FROM employees) AS avg_pay  
FROM employees;

---

### 🔹 Employees with Avg Salary

SELECT first_name, last_name, hourly_pay  
FROM employees  
WHERE hourly_pay =   
(SELECT AVG(hourly_pay) FROM employees);

---

### 🔹 IN Operator with Subquery

SELECT * FROM customers  
WHERE customer_id IN (  
  SELECT customer_id   
  FROM transactions   
  WHERE customer_id IS NOT NULL  
);

---

### 🔹 Direct IN

SELECT * FROM customers  
WHERE customer_id IN (1,2,3);

---

## 📊 GROUP BY & AGGREGATION

### 📌 Concept

Group rows and apply functions

---

### 🔹 Examples

SELECT MAX(amount), order_date  
FROM transactions  
GROUP BY order_date;

SELECT SUM(amount), order_date  
FROM transactions  
GROUP BY order_date;

SELECT COUNT(amount), customer_id  
FROM transactions  
GROUP BY customer_id;

---

## 🚫 HAVING (Filter Groups)

SELECT COUNT(amount), customer_id  
FROM transactions  
GROUP BY customer_id  
HAVING COUNT(amount) > 1;

👉 Filters grouped data (after aggregation)

---

### ❌ Your Mistake (Fix)

HAVING count(amount)>1 and is not null;

✔ Correct:

HAVING COUNT(amount) > 1 AND customer_id IS NOT NULL;

---

# ⚡ Quick Revision Summary

|Topic|Key Idea|
|---|---|
|LIKE|Pattern matching|
|ORDER BY|Sorting|
|LIMIT|Restrict rows|
|UNION|Combine results|
|JOIN|Combine tables|
|INDEX|Faster queries|
|SUBQUERY|Query inside query|
|GROUP BY|Aggregate|
|HAVING|Filter groups|