

---

## 📌 1. INSERT Data

### ➤ Add a Row

INSERT INTO employees (employee_id, first_name, last_name)  
VALUES (23154017, 'Vijay', 'Joseph');

### 💡 Notes

- Column order must match values
- Use `' '` for strings
- Numbers → no quotes (best practice)

---

## 📌 2. SELECT (Fetch Data)

### ➤ Select All Columns

SELECT * FROM employees;

---

### ➤ Select Specific Columns

SELECT first_name, last_name FROM employees;

---

## 📌 3. WHERE Clause (Filtering)

### ➤ Condition with Numbers

SELECT * FROM employees   
WHERE hourly_pay >= 100;

👉 Returns employees with salary ≥ 100

---

### ➤ Condition with Date

SELECT * FROM employees   
WHERE join_date > '2006-06-21';

👉 Returns employees joined after given date

⚠️ Make sure:

- Column name is correct (`join_date`)
- Data type = DATE

---

### ➤ Check NULL Values

SELECT * FROM employees   
WHERE hourly_pay IS NULL;

👉 Returns rows where salary is missing

---

## ⚠️ Common Mistake (Your Case)

WHERE join_data > '2006-06-21'; ❌

👉 ❌ Wrong column name (`join_data`)  
👉 ✔️ Correct:

WHERE join_date > '2006-06-21';

---

## 📝 Key Points

- `INSERT INTO` → add data
- `SELECT *` → all columns
- `SELECT col1, col2` → specific columns
- `WHERE` → filter rows
- `IS NULL` → check missing values
- Always use **correct column names**

---

## ⚡ Quick Revision

- Insert → `INSERT INTO ... VALUES`
- Select All → `SELECT *`
- Select Columns → `SELECT col1, col2`
- Filter → `WHERE condition`
- NULL → `IS NULL`

---

## 🚀 Example Flow

INSERT INTO employees (employee_id, first_name, last_name)  
VALUES (23154017, 'Vijay', 'Joseph');  
  
SELECT * FROM employees;  
  
SELECT first_name, last_name FROM employees;  
  
SELECT * FROM employees WHERE hourly_pay >= 100;  
  
SELECT * FROM employees WHERE hourly_pay IS NULL;