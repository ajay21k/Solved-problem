# 📌 6. Built-in Functions

---

## ➤ COUNT

SELECT COUNT(amount) AS total_count  
FROM transactions;

---

## ➤ MAX

SELECT MAX(amount) AS max_amount  
FROM transactions;

---

## ➤ MIN

SELECT MIN(amount) AS minimum_amount  
FROM transactions;

---

## ➤ CONCAT

SELECT CONCAT(first_name, last_name) AS full_name  
FROM customers;

---

# 📌 7. Logical Operators

---

## ➤ NOT

SELECT * FROM employees  
WHERE NOT job = 'HR';

---

## ➤ OR

SELECT * FROM employees  
WHERE job = 'HR' OR job = 'Intern';

---

## ➤ AND

SELECT * FROM employees  
WHERE join_date <= '2006-06-21' AND job = 'HR';

---

## ➤ BETWEEN

SELECT * FROM employees  
WHERE join_date BETWEEN '2002-01-01' AND '2005-01-01';