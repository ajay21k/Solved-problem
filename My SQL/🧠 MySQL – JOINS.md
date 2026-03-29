# 📌 5. JOINS (VERY IMPORTANT ⭐)

---

## ➤ INNER JOIN

SELECT *  
FROM transactions  
INNER JOIN customers  
ON transactions.customer_id = customers.customer_id;

👉 Returns matching rows only

---

## ➤ Specific Columns

SELECT transaction_id, first_name, last_name, transaction_time, amount  
FROM transactions  
INNER JOIN customers  
ON transactions.customer_id = customers.customer_id;

---

## ➤ LEFT JOIN

SELECT *  
FROM transactions  
LEFT JOIN customers  
ON transactions.customer_id = customers.customer_id;

👉 All transactions + matching customers

---

## ➤ RIGHT JOIN

SELECT *  
FROM transactions  
RIGHT JOIN customers  
ON transactions.customer_id = customers.customer_id;

👉 All customers + matching transactions