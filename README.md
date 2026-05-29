# sql-quick-reference

---

## SQL Functions

### `COALESCE()`
* **Definition:** A built-in conditional function that evaluates a list of arguments in order from left to right and returns the **first non-null value** it encounters. If all arguments evaluate to `NULL`, it returns `NULL`.
* **Use Case:** Handling missing data or default values in reports, preventing math operations from breaking when a `NULL` is present, or combining backup contact methods (like falling back to a home phone if a mobile phone isn't listed).
* **Example:**
  ```sql
  -- Replacing missing email addresses with a fallback text string
  SELECT 
      username, 
      COALESCE(email, 'No Email Provided') AS contact_email
  FROM users;

### 💡 Why `COALESCE` is a Database Lifesaver

In databases, `NULL` represents a complete absence of data, and it behaves like an infection in SQL. If you try to run arithmetic or combine strings with a `NULL` value (e.g., `50 + NULL`), the entire result instantly becomes `NULL`. 

`COALESCE` acts as a shield or a fallback safety net. You can string multiple columns together as backups:

```sql
-- Checks mobile first; if empty, checks home_phone; if empty, defaults to a string
SELECT 
    customer_id,
    COALESCE(mobile_phone, home_phone, 'No Phone on File') AS active_contact
FROM customers;

```
---

### `NULLIF()`
* **Definition:** A built-in conditional function that compares two arguments. It returns `NULL` if the two arguments are completely equal; otherwise, it returns the first argument.
* **Use Case:** Preventing "divide-by-zero" mathematical crashes, or converting empty strings (`''`) or dummy default values (like `0` or `'N/A'`) into proper database `NULL` values so they don't skew data analytics calculations.
* **Example:**
  ```sql
  -- Safely calculating average order values without crashing if total_orders is 0
  SELECT 
      revenue,
      total_orders,
      revenue / NULLIF(total_orders, 0) AS average_order_value
  FROM sales_data;

### 💡 Why `NULLIF` is a Database Lifesaver

The most common superpower of `NULLIF` is safeguarding your math. In SQL, dividing any number by zero will cause your entire query to fail with a loud crash. 



When you wrap the divisor in `NULLIF(total_orders, 0)`:
* If `total_orders` is **5**, the two values are *not* equal. `NULLIF` returns the first value: **5**. (Math works perfectly: `revenue / 5`).
* If `total_orders` is **0**, the two values *are* equal. `NULLIF` returns **NULL**. Because dividing a number by `NULL` safely results in `NULL` instead of a crash, your query completes smoothly without breaking!

### 🤝 How `NULLIF` and `COALESCE` Work Together
Data analysts frequently pair these two together to clean dirty data inputs. For example, if a web form submits an empty string (`''`) instead of a proper blank value, you can convert it to `NULL` and immediately swap it for a clean default placeholder:

```sql
SELECT 
    -- Converts empty strings to NULL, then COALESCE replaces that NULL with 'Unknown'
    COALESCE(NULLIF(city, ''), 'Unknown City') AS clean_city
FROM users;
```
## 1. `NVL()` — Null Value Substitution

### Purpose & Definition
The `NVL()` function is a scalar function used to handle `NULL` values by replacing them with a predefined fallback value. If a database scans a column and encounters a blank, missing, or unknown value (`NULL`), `NVL()` acts as a safety net, letting you swap that `NULL` out for a static value like a `0`, an empty string, or a placeholder string. 

### Syntax
```sql
NVL(expression_to_test, value_if_null)
```
---
## 2. `LISTAGG()` — String Aggregation

### Purpose & Definition
The `LISTAGG()` function is a specialized aggregate function used to transform vertical dataset structures into a horizontal layout. It takes multiple rows of string or text attributes from a single column and collapses them into a single, combined string row per grouping bucket, separated by a custom delimiter of your choice (such as a comma, hyphen, or pipe).

### Syntax
```sql
LISTAGG(measure_column, 'delimiter') WITHIN GROUP (ORDER BY sort_column)
