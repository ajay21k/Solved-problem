Here is a clean **Obsidian-style revision note** version of your SQL topics.

You can paste this directly into Obsidian.

---

# 

## 1. `GROUP BY` with `COUNT()`

### Explanation

`GROUP BY` is used to group rows that have the same value in a column.  
`COUNT()` counts how many rows are present in each group.

### Syntax

```sql
SELECT aggregate_function(column_name), group_column
FROM table_name
GROUP BY group_column;
```

### Example

```sql
SELECT COUNT(transaction_id) AS "# of orders", customer_id
FROM transactions
GROUP BY customer_id;
```

### Meaning

This query counts how many orders each customer made.

### Sample Output Idea

|# of orders|customer_id|
|---|---|
|3|1|
|2|2|
|1|3|

---

## 2. `GROUP BY` with `SUM()` and `WITH ROLLUP`

### Explanation

`SUM()` adds values in a column.  
`WITH ROLLUP` adds an extra row at the end showing the grand total.

### Syntax

```sql
SELECT SUM(column_name), group_column
FROM table_name
GROUP BY group_column WITH ROLLUP;
```

### Example

```sql
SELECT SUM(hourly_pay) AS "hourly pay", employee_id
FROM employees
GROUP BY employee_id WITH ROLLUP;
```

### Meaning

This query shows total hourly pay for each employee and one extra total row.

### Note

In MySQL, the rollup row usually has `NULL` in the grouped column.

---

## 3. Disable Foreign Key Check

### Explanation

Foreign key constraints normally prevent deletion of parent rows if child rows still reference them.  
`SET foreign_key_checks = 0;` temporarily disables this protection.

### Syntax

```sql
SET foreign_key_checks = 0;
```

### Example

```sql
SET foreign_key_checks = 0;
DELETE FROM customers
WHERE customer_id = 4;

SELECT * FROM customers;
```

### Meaning

This allows deleting customer `4` even if another table refers to that customer.

### Warning

Use carefully, because this may create invalid data relationships.

### Re-enable

```sql
SET foreign_key_checks = 1;
```

---

## 4. Create Table with Foreign Key

### Explanation

A foreign key creates a relationship between two tables.  
Here, `customer_id` in `transactions` refers to `customer_id` in `customers`.

### Syntax

```sql
CREATE TABLE table_name (
    column1 datatype,
    column2 datatype,
    ...
    FOREIGN KEY (column_name) REFERENCES parent_table(parent_column)
);
```

### Example

```sql
CREATE TABLE transactions (
    transaction_id INT PRIMARY KEY,
    amount DECIMAL(4,2),
    customer_id INT,
    order_date DATE,
    FOREIGN KEY (customer_id)
        REFERENCES customers(customer_id)
        ON DELETE SET NULL
);
```

### Meaning

- `transaction_id` is the primary key
    
- `customer_id` is a foreign key
    
- if a customer is deleted, the `customer_id` in `transactions` becomes `NULL`
    

### `ON DELETE SET NULL`

This means:

- parent row deleted from `customers`
    
- child row remains in `transactions`
    
- its foreign key value becomes `NULL`
    

### Important

For `ON DELETE SET NULL` to work, the foreign key column must allow `NULL`.

---

## 5. Add Foreign Key Using `ALTER TABLE`

### Explanation

If the table already exists, we can add a foreign key later using `ALTER TABLE`.

### Syntax

```sql
ALTER TABLE table_name
ADD CONSTRAINT constraint_name
FOREIGN KEY (column_name)
REFERENCES parent_table(parent_column);
```

### Example

```sql
ALTER TABLE transactions
ADD CONSTRAINT fk_transactions_id
FOREIGN KEY (customer_id)
REFERENCES customers(customer_id);
```

### Meaning

This adds a foreign key constraint named `fk_transactions_id` to `transactions.customer_id`.

---

## 6. Stored Procedure Basics

### Explanation

A stored procedure is a saved SQL program that can be executed later.  
It is useful for repeating queries or logic.

### Advantages

- reduces network traffic
    
- improves performance in repeated tasks
    
- increases security
    
- admin can give permission to use procedure without giving full table access
    

### Disadvantage

- increases memory usage for every connection
    

---

## 7. Create Simple Stored Procedure

### Explanation

This procedure returns all rows from the `customers` table.

### Syntax

```sql
DELIMITER $$

CREATE PROCEDURE procedure_name()
BEGIN
    SQL statements;
END $$

DELIMITER ;
```

### Example

```sql
DELIMITER $$

CREATE PROCEDURE get_customer()
BEGIN
    SELECT * FROM customers;
END $$

DELIMITER ;
```

### Call Procedure

```sql
CALL get_customer();
```

---

## 8. Stored Procedure with Input Parameter

### Explanation

A procedure can take input values using `IN`.

### Syntax

```sql
DELIMITER $$

CREATE PROCEDURE procedure_name(IN parameter_name datatype)
BEGIN
    SELECT * FROM table_name
    WHERE column_name = parameter_name;
END $$

DELIMITER ;
```

### Example

```sql
DELIMITER @@

CREATE PROCEDURE get_name(IN name1 VARCHAR(25))
BEGIN
    SELECT * FROM employees
    WHERE first_name = name1;
END @@

DELIMITER ;
```

### Call

```sql
CALL get_name("Ajay");
```

### Meaning

This finds employees whose first name is `Ajay`.

---

## 9. Stored Procedure with Multiple Input Parameters

### Explanation

A procedure can accept more than one input value.

### Example

```sql
DELIMITER !!

CREATE PROCEDURE find_customer(
    IN f_name VARCHAR(25),
    IN l_name VARCHAR(25)
)
BEGIN
    SELECT * FROM employees
    WHERE first_name = f_name
      AND last_name = l_name;
END !!

DELIMITER ;
```

### Call

```sql
CALL find_customer("Ajay", "K");
```

### Meaning

This returns employee rows where first name is `Ajay` and last name is `K`.

---

## 10. Finding Duplicates Using `GROUP BY` and `HAVING`

### Explanation

To find duplicate values, use:

- `GROUP BY` to group rows
    
- `COUNT()` to count rows in each group
    
- `HAVING COUNT(*) > 1` to keep only duplicates
    

### Basic Syntax

```sql
SELECT column_name, COUNT(*)
FROM table_name
GROUP BY column_name
HAVING COUNT(*) > 1;
```

---

## 11. Duplicate Rows Problem (Mr. Cooper Interview Type)

### Explanation

This query shows habitats that appear more than once in the `alien` table, along with habitat details and alien names.

### Example

```sql
SELECT a.HabitatID,
       a.HabitatNAME,
       b.alienName,
       dup.cnt - 1 AS duplicate_count
FROM habitat a
INNER JOIN alien b
    ON a.HabitatID = b.HabitatID
INNER JOIN (
    SELECT HabitatID, COUNT(*) AS cnt
    FROM alien
    GROUP BY HabitatID
    HAVING COUNT(*) > 1
) dup
    ON a.HabitatID = dup.HabitatID;
```

### Meaning

- subquery finds repeated `HabitatID`
    
- outer query joins habitat details and alien names
    
- `dup.cnt - 1` shows extra duplicate count
    

### Example Idea

If `HabitatID = 2` appears 3 times, then:

- total count = 3
    
- duplicate count = `3 - 1 = 2`
    

---

## 12. `HAVING` vs `WHERE`

### Explanation

`WHERE` filters rows before grouping.  
`HAVING` filters groups after grouping.

### Example

```sql
SELECT HabitatID, COUNT(*)
FROM alien
GROUP BY HabitatID
HAVING COUNT(*) > 1;
```

### Meaning

Only habitat groups with more than one row are shown.

---

## 13. Trigger Basics

### Explanation

A trigger is a special SQL block that automatically runs when an event happens on a table.

### Events

- `BEFORE INSERT`
    
- `AFTER INSERT`
    
- `BEFORE UPDATE`
    
- `AFTER UPDATE`
    
- `BEFORE DELETE`
    
- `AFTER DELETE`
    

### Syntax

```sql
CREATE TRIGGER trigger_name
BEFORE/AFTER INSERT/UPDATE/DELETE
ON table_name
FOR EACH ROW
BEGIN
    statements;
END;
```

---

## 14. Trigger Before Update

### Your Given Query

```sql
CREATE TRIGGER update_hourly_pay
BEFORE UPDATE ON employees
FOR EACH ROW
SET NEW.salary = (NEW.hourly_pay);
```

### Explanation

Before updating a row in `employees`, this trigger sets `salary` equal to `hourly_pay`.

### Note

This works only if your intention is directly assigning hourly pay into salary, but logically salary and hourly pay are usually different things.

### Better Example

```sql
CREATE TRIGGER update_hourly_pay
BEFORE UPDATE ON employees
FOR EACH ROW
SET NEW.salary = NEW.hourly_pay * 8 * 30;
```

### Meaning

This could estimate monthly salary from hourly pay.

---

## 15. `NEW` and `OLD` in Triggers

### Explanation

Inside triggers:

- `NEW.column_name` = new value
    
- `OLD.column_name` = old value before change
    

### Used In

- `NEW` for `INSERT` and `UPDATE`
    
- `OLD` for `DELETE` and `UPDATE`
    

---

## 16. Update Summary Value Using Subquery

### Explanation

This query updates the `expenses` table by setting the salary total equal to the sum of all employee salaries.

### Example

```sql
UPDATE expenses
SET expTOT = (SELECT SUM(salary) FROM employees)
WHERE expNAME = "salary";
```

### Meaning

The row in `expenses` where `expNAME = 'salary'` gets updated with total employee salary.

---

## 17. Trigger After Delete

### Example

```sql
CREATE TRIGGER after_salary_delete
AFTER DELETE ON employees
FOR EACH ROW
UPDATE expenses
SET expTOT = expTOT - OLD.salary
WHERE expNAME = "salary";
```

### Explanation

When an employee row is deleted:

- the deleted employee’s salary is taken from `OLD.salary`
    
- that amount is subtracted from total salary expense
    

### Meaning

This keeps the `expenses` table updated automatically after deletion.

---

## 18. Why Use Triggers?

### Advantages

- automatic execution
    
- keeps related tables synchronized
    
- reduces repeated manual updates
    
- useful for auditing and summary maintenance
    

### Disadvantages

- can make debugging harder
    
- hidden logic may confuse developers
    
- too many triggers may reduce performance
    

---

# Quick Revision Summary

## `GROUP BY`

Used to group rows with same value.

```sql
SELECT COUNT(transaction_id), customer_id
FROM transactions
GROUP BY customer_id;
```

## `WITH ROLLUP`

Adds final total row.

```sql
SELECT SUM(hourly_pay), employee_id
FROM employees
GROUP BY employee_id WITH ROLLUP;
```

## Foreign Key

Creates relationship between tables.

```sql
FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
```

## `ON DELETE SET NULL`

If parent row is deleted, child foreign key becomes `NULL`.

## Stored Procedure

Saved SQL code for reuse.

```sql
CALL get_customer();
CALL get_name("Ajay");
CALL find_customer("Ajay","K");
```

## `HAVING`

Used after grouping.

```sql
HAVING COUNT(*) > 1
```

## Trigger

Runs automatically on table events.

```sql
CREATE TRIGGER trigger_name
AFTER DELETE ON employees
FOR EACH ROW ...
```

## `NEW` and `OLD`

- `NEW` = new row value
    
- `OLD` = old row value
    

---

# Important Corrections in Your Notes

## 1. Duplicate `CREATE TABLE transactions`

You wrote the same `CREATE TABLE transactions` twice.  
Only one is needed.

## 2. Trigger column mismatch

You wrote:

```sql
SET NEW.salary = (NEW.hourly_pay);
```

This is syntactically okay if both columns exist, but logically salary may not be equal to hourly pay directly.

## 3. Procedure name `find_customer`

Your procedure actually searches the `employees` table, not `customers`.  
So the name may be slightly misleading.

Better name:

```sql
find_employee
```

---

# Exam Tips

## For theory questions

Remember:

- Stored procedures reduce network traffic
    
- Triggers execute automatically
    
- Foreign keys maintain referential integrity
    
- `HAVING` is used with aggregate functions
    
- `WITH ROLLUP` gives grand total
    

## For practical questions

Check:

- delimiter properly changed
    
- procedure uses `BEGIN ... END`
    
- trigger uses `FOR EACH ROW`
    
- foreign key parent table must exist first
    

---

I can also convert this into a more polished **Obsidian markdown with headings, callouts, and tables** for easier revision.