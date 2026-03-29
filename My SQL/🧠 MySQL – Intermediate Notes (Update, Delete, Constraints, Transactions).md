## 📌 1. UPDATE (Modify Data)

### ➤ Update Multiple Columns

UPDATE employees   
SET hourly_pay = 20,  
    join_date = '2026-04-23'  
WHERE first_name = 'Vijay'  
LIMIT 1;

### 💡 Notes

- Updates **only matching rows**
- `LIMIT 1` → avoids multiple updates
- Always use `WHERE` (important ⚠️)

---

## 📌 2. DELETE (Remove Rows)

DELETE FROM employees  
WHERE employee_id = 23154016  
LIMIT 1;

### 💡 Notes

- Deletes specific row
- `LIMIT` → prevents accidental mass delete

---

## 📌 3. TRANSACTIONS (Commit & Rollback)

### ➤ Disable Auto Commit

SET autocommit = OFF;

---

### ➤ Save Changes

COMMIT;

---

### ➤ Undo Changes

ROLLBACK;

---

### ⚠️ Important

DROP TABLE employees;  
ROLLBACK; ❌ (won’t work)

👉 `DROP` is **permanent (DDL)** → cannot rollback

---

## 📌 4. Date & Time Functions

### ➤ Insert Current Date & Time

INSERT INTO test  
VALUES (CURRENT_DATE(), CURRENT_TIME(), NOW());

---

### ➤ Add 1 Day

INSERT INTO test  
VALUES (CURRENT_DATE() + 1, CURRENT_TIME(), NOW());

---

## 📌 5. CREATE TABLE with Constraints

CREATE TABLE products (  
    product_id INT,  
    product_name VARCHAR(100) UNIQUE,  
    prices DECIMAL(60,2)  
);

---

## 📌 6. UNIQUE Constraint

### ➤ Add UNIQUE Constraint

ALTER TABLE products  
ADD CONSTRAINT unique_product_id UNIQUE(product_id);

---

### ➤ Insert Data

INSERT INTO products VALUES  
(101, 'Phone', 1000),  
(102, 'Earbuds', 100),  
(103, 'Hard Disk', 125);

---

## 📌 7. MODIFY Column (NOT NULL)

ALTER TABLE products  
MODIFY prices DECIMAL(60,2) NOT NULL;

---

## 📌 8. CHECK Constraint

### ➤ Add CHECK

ALTER TABLE employees  
ADD CONSTRAINT chk_hourly_pay  
CHECK (hourly_pay >= 10);

---

### ➤ Drop CHECK (MySQL 8.0+)

ALTER TABLE employees  
DROP CONSTRAINT chk_hourly_pay;

---

### ⚠️ Notes

- MySQL < 8 → CHECK ignored
- Name must match exactly

---

## 📌 9. View Table Structure

DESC products;

---

### ➤ View Full Table (Constraints)

SHOW CREATE TABLE employees;

---

## 📝 Key Concepts Summary

- `UPDATE` → modify rows
- `DELETE` → remove rows
- `LIMIT` → restrict impact
- `COMMIT` → save changes
- `ROLLBACK` → undo (only DML)
- `DROP` → permanent
- `UNIQUE` → no duplicates
- `CHECK` → restrict values
- `NOT NULL` → mandatory field

---

## ⚡ Quick Revision

- Update → `UPDATE + SET + WHERE`
- Delete → `DELETE + WHERE`
- Transaction → `COMMIT / ROLLBACK`
- Constraint → `UNIQUE / CHECK / NOT NULL`
- View Table → `DESC / SHOW CREATE TABLE`

---

## 🚀 Interview Tips

- Always use **WHERE in UPDATE/DELETE**
- Know difference:
    - `DELETE` vs `DROP`
    - `COMMIT` vs `ROLLBACK`
- Constraints are **very important**

---

