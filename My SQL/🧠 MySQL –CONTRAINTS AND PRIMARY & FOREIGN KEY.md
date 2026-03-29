# 📌 1. DEFAULT Constraint

### ➤ Set Default Value

ALTER TABLE products  
ALTER prices SET DEFAULT 0.00;

### 💡 Note

- If value not provided → default is used

---

# 📌 2. AUTO_INCREMENT

### ➤ Create Table

CREATE TABLE transactions (  
    transaction_id INT PRIMARY KEY AUTO_INCREMENT,  
    amount DECIMAL(10,2)  
);

---

### ➤ Set Starting Value

ALTER TABLE transactions  
AUTO_INCREMENT = 1000;

---

### ➤ Insert Data

INSERT INTO transactions (amount)  
VALUES (1500);

👉 ID auto-generated (1000, 1001...)

---

# 📌 3. PRIMARY KEY

### ➤ Define Primary Key

CREATE TABLE transactions (  
    transaction_id INT PRIMARY KEY,  
    amount DECIMAL(10,2)  
);

---

### 💡 Rules

- Unique ✔️
- Not NULL ✔️
- One per table ✔️

---

# 📌 4. FOREIGN KEY

### ➤ Create Parent Table

CREATE TABLE customers (  
    customer_id INT PRIMARY KEY AUTO_INCREMENT,  
    first_name VARCHAR(25),  
    last_name VARCHAR(25)  
);

---

### ➤ Create Child Table

CREATE TABLE transactions (  
    transaction_id INT PRIMARY KEY AUTO_INCREMENT,  
    transaction_time DATETIME DEFAULT NOW(),  
    customer_id INT,  
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)  
);

---

### ➤ Drop Foreign Key

ALTER TABLE transactions  
DROP FOREIGN KEY transactions_ibfk_1;

---

### ➤ Add Foreign Key

ALTER TABLE transactions  
ADD CONSTRAINT fk_customer_id  
FOREIGN KEY (customer_id)  
REFERENCES customers(customer_id);