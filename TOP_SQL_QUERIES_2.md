### 1. What is the difference between INNER JOIN, LEFT JOIN, and RIGHT JOIN?

**Input tables:**

**Employees**

| employee\_id | name    | dept\_id |
| ------------ | ------- | -------- |
| 1            | Alice   | 10       |
| 2            | Bob     | 20       |
| 3            | Charlie | NULL     |

**Departments**

| dept\_id | dept\_name |
| -------- | ---------- |
| 10       | Sales      |
| 20       | Marketing  |
| 30       | HR         |

---

**Solution & Queries:**

* **INNER JOIN:** Returns only matching rows

```sql
SELECT e.name, d.dept_name
FROM Employees e
INNER JOIN Departments d ON e.dept_id = d.dept_id;
```

* **LEFT JOIN:** Returns all employees, matching departments or NULL if no match

```sql
SELECT e.name, d.dept_name
FROM Employees e
LEFT JOIN Departments d ON e.dept_id = d.dept_id;
```

* **RIGHT JOIN:** Returns all departments, matching employees or NULL if no match

```sql
SELECT e.name, d.dept_name
FROM Employees e
RIGHT JOIN Departments d ON e.dept_id = d.dept_id;
```

---

**Results:**

| INNER JOIN Result |           |
| ----------------- | --------- |
| Alice             | Sales     |
| Bob               | Marketing |

| LEFT JOIN Result |           |
| ---------------- | --------- |
| Alice            | Sales     |
| Bob              | Marketing |
| Charlie          | NULL      |

| RIGHT JOIN Result |           |
| ----------------- | --------- |
| Alice             | Sales     |
| Bob               | Marketing |
| NULL              | HR        |

---

### 2. How do you calculate the median of a numeric column in SQL?

**Input table:**

**Scores**

| student\_id | score |
| ----------- | ----- |
| 1           | 80    |
| 2           | 90    |
| 3           | 70    |
| 4           | 85    |
| 5           | 95    |

---

**Solution:**

Using PostgreSQL’s window functions:

```sql
SELECT AVG(score) AS median_score FROM (
  SELECT score,
         ROW_NUMBER() OVER (ORDER BY score) AS rn,
         COUNT(*) OVER () AS cnt
  FROM Scores
) sub
WHERE rn IN ((cnt + 1) / 2, (cnt + 2) / 2);
```

---

**Result:**

| median\_score |
| ------------- |
| 85            |

---

### 3. Write a query to find duplicate rows in a dataset.

**Input table:**

**Orders**

| order\_id | customer\_id | order\_date |
| --------- | ------------ | ----------- |
| 1         | 101          | 2024-01-01  |
| 2         | 101          | 2024-01-01  |
| 3         | 102          | 2024-01-02  |
| 4         | 101          | 2024-01-01  |

---

**Solution:**

Find duplicates by grouping all columns (except id if unique):

```sql
SELECT customer_id, order_date, COUNT(*)
FROM Orders
GROUP BY customer_id, order_date
HAVING COUNT(*) > 1;
```

---

**Result:**

| customer\_id | order\_date | count |
| ------------ | ----------- | ----- |
| 101          | 2024-01-01  | 3     |

---

### 4. What is the difference between GROUP BY and ORDER BY?

**Input table:**

**Sales**

| product\_id | sale\_amount |
| ----------- | ------------ |
| 1           | 100          |
| 1           | 150          |
| 2           | 200          |

---

**Solution:**

* **GROUP BY:** Aggregates rows

```sql
SELECT product_id, SUM(sale_amount) AS total_sales
FROM Sales
GROUP BY product_id;
```

* **ORDER BY:** Sorts result

```sql
SELECT product_id, sale_amount
FROM Sales
ORDER BY sale_amount DESC;
```

---

**Result:**

* GROUP BY Result:

| product\_id | total\_sales |
| ----------- | ------------ |
| 1           | 250          |
| 2           | 200          |

* ORDER BY Result:

| product\_id | sale\_amount |
| ----------- | ------------ |
| 2           | 200          |
| 1           | 150          |
| 1           | 100          |

---

### 5. Explain the purpose of window functions and give an example.

**Input table:**

**Employees**

| employee\_id | department | salary |
| ------------ | ---------- | ------ |
| 1            | Sales      | 5000   |
| 2            | Sales      | 6000   |
| 3            | HR         | 4500   |
| 4            | HR         | 4700   |

---

**Solution:**

Window functions perform calculations across rows related to current row, without collapsing rows.

Example: Calculate average salary by department, keeping all rows:

```sql
SELECT employee_id, department, salary,
       AVG(salary) OVER (PARTITION BY department) AS avg_dept_salary
FROM Employees;
```

---

**Result:**

| employee\_id | department | salary | avg\_dept\_salary |
| ------------ | ---------- | ------ | ----------------- |
| 1            | Sales      | 5000   | 5500              |
| 2            | Sales      | 6000   | 5500              |
| 3            | HR         | 4500   | 4600              |
| 4            | HR         | 4700   | 4600              |

---

### 6. Write a query to calculate the rolling average of sales for the past 7 days.

**Input table:**

**DailySales**

| sale\_date | amount |
| ---------- | ------ |
| 2024-05-01 | 100    |
| 2024-05-02 | 200    |
| 2024-05-03 | 150    |
| 2024-05-04 | 300    |
| 2024-05-05 | 250    |
| 2024-05-06 | 400    |
| 2024-05-07 | 350    |
| 2024-05-08 | 500    |

---

**Solution:**

```sql
SELECT sale_date, amount,
       AVG(amount) OVER (ORDER BY sale_date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS rolling_avg_7days
FROM DailySales;
```

---

**Result:**

| sale\_date | amount | rolling\_avg\_7days |
| ---------- | ------ | ------------------- |
| 2024-05-01 | 100    | 100                 |
| 2024-05-02 | 200    | 150                 |
| 2024-05-03 | 150    | 150                 |
| 2024-05-04 | 300    | 187.5               |
| 2024-05-05 | 250    | 200                 |
| 2024-05-06 | 400    | 233.33              |
| 2024-05-07 | 350    | 264.29              |
| 2024-05-08 | 500    | 307.14              |

---

### 7. How would you identify the second-highest value in a column?

**Input table:**

**Scores**

| student\_id | score |
| ----------- | ----- |
| 1           | 85    |
| 2           | 92    |
| 3           | 87    |
| 4           | 92    |

---

**Solution:**

Use `DISTINCT` and `ORDER BY` with `LIMIT` and `OFFSET`:

```sql
SELECT DISTINCT score
FROM Scores
ORDER BY score DESC
LIMIT 1 OFFSET 1;
```

---

**Result:**

| score |
| ----- |
| 87    |

---

### 8. Explain how HAVING differs from WHERE with examples.

**Input table:**

**Sales**

| product\_id | amount |
| ----------- | ------ |
| 1           | 100    |
| 1           | 200    |
| 2           | 300    |

---

**Solution:**

* `WHERE` filters **rows before grouping**.
* `HAVING` filters **groups after aggregation**.

Example:

```sql
-- Filter rows where amount > 100
SELECT product_id, SUM(amount)
FROM Sales
WHERE amount > 100
GROUP BY product_id;
```

```sql
-- Filter groups where sum(amount) > 200
SELECT product_id, SUM(amount)
FROM Sales
GROUP BY product_id
HAVING SUM(amount) > 200;
```

---

**Result:**

* WHERE result:

| product\_id | sum |
| ----------- | --- |
| 1           | 200 |
| 2           | 300 |

* HAVING result:

| product\_id | sum |
| ----------- | --- |
| 1           | 300 |
| 2           | 300 |

---

### 9. Write a query to extract users who performed at least 5 transactions in a month.

**Input table:**

**Transactions**

| transaction\_id | user\_id | transaction\_date |
| --------------- | -------- | ----------------- |
| 1               | 101      | 2024-05-01        |
| 2               | 101      | 2024-05-02        |
| 3               | 101      | 2024-05-05        |
| 4               | 101      | 2024-05-10        |
| 5               | 101      | 2024-05-15        |
| 6               | 102      | 2024-05-02        |
| 7               | 102      | 2024-05-03        |

---

**Solution:**

Using `DATE_TRUNC`:

```sql
SELECT user_id, DATE_TRUNC('month', transaction_date) AS month, COUNT(*) AS txn_count
FROM Transactions
GROUP BY user_id, DATE_TRUNC('month', transaction_date)
HAVING COUNT(*) >= 5;
```

---

**Result:**

| user\_id | month      | txn\_count |
| -------- | ---------- | ---------- |
| 101      | 2024-05-01 | 5          |

---

### 10. How would you detect anomalies or outliers in a dataset using SQL?

**Input table:**

**Measurements**

| measurement\_id | value |
| --------------- | ----- |
| 1               | 10    |
| 2               | 12    |
| 3               | 11    |
| 4               | 100   |
| 5               | 9     |

---

**Solution:**

Detect outliers by defining values outside 1.5 \* IQR (Interquartile Range):

```sql
WITH percentiles AS (
  SELECT
    percentile_cont(0.25) WITHIN GROUP (ORDER BY value) AS q1,
    percentile_cont(0.75) WITHIN GROUP (ORDER BY value) AS q3
  FROM Measurements
),
iqr_values AS (
  SELECT q1, q3, (q3 - q1) AS iqr FROM percentiles
)
SELECT m.*
FROM Measurements m, iqr_values i
WHERE m.value < i.q1 - 1.5 * i.iqr OR m.value > i.q3 + 1.5 * i.iqr;
```

---

**Result:**

| measurement\_id | value |
| --------------- | ----- |
| 4               | 100   |

---
### 11. How can you efficiently paginate query results in PostgreSQL?

**Input table:**

**Products**

| product\_id | name     | price |
| ----------- | -------- | ----- |
| 1           | Keyboard | 50    |
| 2           | Mouse    | 20    |
| 3           | Monitor  | 200   |
| 4           | Speaker  | 80    |
| 5           | Webcam   | 70    |

---

**Solution:**

Using `LIMIT` and `OFFSET`:

```sql
-- Page 2, 2 rows per page
SELECT * FROM Products
ORDER BY product_id
LIMIT 2 OFFSET 2;
```

---

**Result:**

| product\_id | name    | price |
| ----------- | ------- | ----- |
| 3           | Monitor | 200   |
| 4           | Speaker | 80    |

---

### 12. Write a query to find all employees who joined in the last 30 days.

**Input table:**

**Employees**

| employee\_id | name    | join\_date |
| ------------ | ------- | ---------- |
| 1            | Alice   | 2024-04-20 |
| 2            | Bob     | 2024-05-10 |
| 3            | Charlie | 2024-05-15 |

Assume today is 2024-05-19.

---

**Solution:**

```sql
SELECT *
FROM Employees
WHERE join_date >= CURRENT_DATE - INTERVAL '30 days';
```

---

**Result:**

| employee\_id | name    | join\_date |
| ------------ | ------- | ---------- |
| 2            | Bob     | 2024-05-10 |
| 3            | Charlie | 2024-05-15 |

---

### 13. How do you calculate the cumulative sum in PostgreSQL?

**Input table:**

**Sales**

| sale\_date | amount |
| ---------- | ------ |
| 2024-05-01 | 100    |
| 2024-05-02 | 200    |
| 2024-05-03 | 150    |

---

**Solution:**

```sql
SELECT sale_date, amount,
       SUM(amount) OVER (ORDER BY sale_date) AS cumulative_sum
FROM Sales;
```

---

**Result:**

| sale\_date | amount | cumulative\_sum |
| ---------- | ------ | --------------- |
| 2024-05-01 | 100    | 100             |
| 2024-05-02 | 200    | 300             |
| 2024-05-03 | 150    | 450             |

---

### 14. Write a query to find users who have never made a transaction.

**Input tables:**

**Users**

| user\_id | name    |
| -------- | ------- |
| 1        | Alice   |
| 2        | Bob     |
| 3        | Charlie |

**Transactions**

| transaction\_id | user\_id | amount |
| --------------- | -------- | ------ |
| 1               | 1        | 100    |
| 2               | 1        | 150    |
| 3               | 2        | 200    |

---

**Solution:**

Using `LEFT JOIN` and checking for NULL in transactions:

```sql
SELECT u.user_id, u.name
FROM Users u
LEFT JOIN Transactions t ON u.user_id = t.user_id
WHERE t.user_id IS NULL;
```

---

**Result:**

| user\_id | name    |
| -------- | ------- |
| 3        | Charlie |

---

### 15. How do you find the nth highest salary from the employee table?

**Input table:**

**Employees**

| employee\_id | name  | salary |
| ------------ | ----- | ------ |
| 1            | Alice | 7000   |
| 2            | Bob   | 8000   |
| 3            | Cara  | 8000   |
| 4            | Dave  | 6000   |

Find 2nd highest salary.

---

**Solution:**

```sql
SELECT DISTINCT salary
FROM Employees
ORDER BY salary DESC
LIMIT 1 OFFSET 1;
```

---

**Result:**

| salary |
| ------ |
| 7000   |

---

### 16. Write a query to pivot rows into columns in PostgreSQL.

**Input table:**

**Sales**

| product | month | amount |
| ------- | ----- | ------ |
| A       | Jan   | 100    |
| A       | Feb   | 150    |
| B       | Jan   | 200    |
| B       | Feb   | 250    |

---

**Solution:**

Use `FILTER` with `GROUP BY`:

```sql
SELECT product,
       SUM(amount) FILTER (WHERE month = 'Jan') AS jan_sales,
       SUM(amount) FILTER (WHERE month = 'Feb') AS feb_sales
FROM Sales
GROUP BY product;
```

---

**Result:**

| product | jan\_sales | feb\_sales |
| ------- | ---------- | ---------- |
| A       | 100        | 150        |
| B       | 200        | 250        |

---

### 17. How do you use Common Table Expressions (CTEs) in PostgreSQL?

**Input table:**

**Orders**

| order\_id | customer\_id | amount |
| --------- | ------------ | ------ |
| 1         | 101          | 500    |
| 2         | 102          | 300    |
| 3         | 101          | 200    |

---

**Solution:**

CTE example to calculate total spent per customer and then filter:

```sql
WITH CustomerTotals AS (
  SELECT customer_id, SUM(amount) AS total_spent
  FROM Orders
  GROUP BY customer_id
)
SELECT *
FROM CustomerTotals
WHERE total_spent > 400;
```

---

**Result:**

| customer\_id | total\_spent |
| ------------ | ------------ |
| 101          | 700          |

---

### 18. Write a query to find gaps in a sequence of numbers.

**Input table:**

**Numbers**

| num |
| --- |
| 1   |
| 2   |
| 4   |
| 5   |
| 7   |

---

**Solution:**

Find missing numbers between min and max:

```sql
WITH seq AS (
  SELECT generate_series(MIN(num), MAX(num)) AS n
  FROM Numbers
)
SELECT n AS missing_num
FROM seq
LEFT JOIN Numbers ON seq.n = Numbers.num
WHERE Numbers.num IS NULL;
```

---

**Result:**

| missing\_num |
| ------------ |
| 3            |
| 6            |

---

### 19. How do you write a recursive query in PostgreSQL?

**Input table:**

**Employees**

| employee\_id | manager\_id | name    |
| ------------ | ----------- | ------- |
| 1            | NULL        | Alice   |
| 2            | 1           | Bob     |
| 3            | 2           | Charlie |

---

**Solution:**

Find all employees under Alice (id=1):

```sql
WITH RECURSIVE subordinates AS (
  SELECT employee_id, manager_id, name
  FROM Employees
  WHERE employee_id = 1
  
  UNION ALL
  
  SELECT e.employee_id, e.manager_id, e.name
  FROM Employees e
  INNER JOIN subordinates s ON e.manager_id = s.employee_id
)
SELECT * FROM subordinates;
```

---

**Result:**

| employee\_id | manager\_id | name    |
| ------------ | ----------- | ------- |
| 1            | NULL        | Alice   |
| 2            | 1           | Bob     |
| 3            | 2           | Charlie |

---

### 20. Write a query using `to_char` to format date.

**Input table:**

**Events**

| event\_id | event\_date         |
| --------- | ------------------- |
| 1         | 2024-05-19 14:30:00 |

---

**Solution:**

```sql
SELECT event_id, to_char(event_date, 'DD Mon YYYY HH12:MI AM') AS formatted_date
FROM Events;
```

---

**Result:**

| event\_id | formatted\_date      |
| --------- | -------------------- |
| 1         | 19 May 2024 02:30 PM |

---

### 21. How would you handle missing values in SQL?

**Input table:**

**Customers**

| customer\_id | name    | email                                             |
| ------------ | ------- | ------------------------------------------------- |
| 1            | Alice   | [alice@example.com](mailto:alice@example.com)     |
| 2            | Bob     | NULL                                              |
| 3            | Charlie | [charlie@example.com](mailto:charlie@example.com) |

---

**Solution:**

Use `COALESCE` to replace `NULL` with a default value:

```sql
SELECT customer_id,
       name,
       COALESCE(email, 'noemail@domain.com') AS email
FROM Customers;
```

---

**Result:**

| customer\_id | name    | email                                             |
| ------------ | ------- | ------------------------------------------------- |
| 1            | Alice   | [alice@example.com](mailto:alice@example.com)     |
| 2            | Bob     | [noemail@domain.com](mailto:noemail@domain.com)   |
| 3            | Charlie | [charlie@example.com](mailto:charlie@example.com) |

---

### 22. Write a query to remove duplicate rows from a dataset.

**Input table:**

**Visitors**

| visitor\_id | visit\_date | page\_visited |
| ----------- | ----------- | ------------- |
| 1           | 2024-05-01  | Home          |
| 1           | 2024-05-01  | Home          |
| 2           | 2024-05-02  | About         |
| 3           | 2024-05-03  | Contact       |
| 3           | 2024-05-03  | Contact       |

---

**Solution:**

Use `ROW_NUMBER()` to identify duplicates and keep only one:

```sql
WITH ranked AS (
  SELECT *,
         ROW_NUMBER() OVER (PARTITION BY visitor_id, visit_date, page_visited ORDER BY visitor_id) AS rn
  FROM Visitors
)
SELECT visitor_id, visit_date, page_visited
FROM ranked
WHERE rn = 1;
```

---

**Result:**

| visitor\_id | visit\_date | page\_visited |
| ----------- | ----------- | ------------- |
| 1           | 2024-05-01  | Home          |
| 2           | 2024-05-02  | About         |
| 3           | 2024-05-03  | Contact       |

---

### 23. How can you split a single column into multiple columns in SQL?

**Input table:**

**FullNames**

| id | fullname      |
| -- | ------------- |
| 1  | John Doe      |
| 2  | Alice Johnson |

---

**Solution:**

Use `split_part()` to split by space:

```sql
SELECT id,
       split_part(fullname, ' ', 1) AS first_name,
       split_part(fullname, ' ', 2) AS last_name
FROM FullNames;
```

---

**Result:**

| id | first\_name | last\_name |
| -- | ----------- | ---------- |
| 1  | John        | Doe        |
| 2  | Alice       | Johnson    |

---

### 24. Explain how you would clean a dataset with inconsistent formats (e.g., date or string issues).

**Input table:**

**Orders**

| order\_id | order\_date | customer\_email                                   |
| --------- | ----------- | ------------------------------------------------- |
| 1         | 05/18/2024  | [ALICE@EXAMPLE.com](mailto:ALICE@EXAMPLE.com)     |
| 2         | 2024-05-17  | [bob@example.COM](mailto:bob@example.COM)         |
| 3         | May 16 2024 | [Charlie@example.com](mailto:Charlie@example.com) |

---

**Solution:**

* Use `TO_DATE()` with appropriate format to standardize dates
* Use `LOWER()` to standardize emails

```sql
SELECT order_id,
       TO_DATE(order_date, 'MM/DD/YYYY') AS standardized_date -- for first format
FROM Orders
WHERE order_date ~ '^\d{2}/\d{2}/\d{4}$';  -- filter only those with MM/DD/YYYY format
```

For multiple formats, use `CASE`:

```sql
SELECT order_id,
       CASE
         WHEN order_date ~ '^\d{2}/\d{2}/\d{4}$' THEN TO_DATE(order_date, 'MM/DD/YYYY')
         WHEN order_date ~ '^\d{4}-\d{2}-\d{2}$' THEN TO_DATE(order_date, 'YYYY-MM-DD')
         WHEN order_date ~ '^[A-Za-z]{3} \d{2} \d{4}$' THEN TO_DATE(order_date, 'Mon DD YYYY')
         ELSE NULL
       END AS standardized_date,
       LOWER(customer_email) AS standardized_email
FROM Orders;
```

---

**Result:**

| order\_id | standardized\_date | standardized\_email                               |
| --------- | ------------------ | ------------------------------------------------- |
| 1         | 2024-05-18         | [alice@example.com](mailto:alice@example.com)     |
| 2         | 2024-05-17         | [bob@example.com](mailto:bob@example.com)         |
| 3         | 2024-05-16         | [charlie@example.com](mailto:charlie@example.com) |

---

### 25. Write a query to standardize data by scaling numeric columns between 0 and 1.

**Input table:**

**Scores**

| id | score |
| -- | ----- |
| 1  | 10    |
| 2  | 20    |
| 3  | 15    |
| 4  | 30    |

---

**Solution:**

Min-max scaling formula: (score - min) / (max - min)

```sql
WITH stats AS (
  SELECT MIN(score) AS min_score,
         MAX(score) AS max_score
  FROM Scores
)
SELECT id,
       score,
       (score - stats.min_score)::float / NULLIF((stats.max_score - stats.min_score), 0) AS scaled_score
FROM Scores, stats;
```

---

**Result:**

| id | score | scaled\_score |
| -- | ----- | ------------- |
| 1  | 10    | 0.0           |
| 2  | 20    | 0.5           |
| 3  | 15    | 0.25          |
| 4  | 30    | 1.0           |

---
