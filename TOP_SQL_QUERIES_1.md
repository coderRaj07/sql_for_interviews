### ➤ 𝗪𝗶𝗻𝗱𝗼𝘄 𝗙𝘂𝗻𝗰𝘁𝗶𝗼𝗻𝘀

---

#### 1. Calculate the moving average of sales for the past 3 months.
**Similar Problem:**
👉 [Rolling Average Tweets – DataLemur](https://datalemur.com/questions/rolling-average-tweets)

**Input Table: `sales`**

| sale\_id | sale\_date | amount |
| -------- | ---------- | ------ |
| 1        | 2025-01-15 | 100    |
| 2        | 2025-02-10 | 150    |
| 3        | 2025-03-05 | 200    |
| 4        | 2025-04-12 | 250    |
| 5        | 2025-05-20 | 300    |

**SQL Query:**

```sql
SELECT
  sale_id,
  sale_date,
  amount,
  AVG(amount) OVER (
    ORDER BY sale_date
    ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
  ) AS moving_avg
FROM sales;
```

**Output Table:**

| sale\_id | sale\_date | amount | moving\_avg |
| -------- | ---------- | ------ | ----------- |
| 1        | 2025-01-15 | 100    | 100.00      |
| 2        | 2025-02-10 | 150    | 125.00      |
| 3        | 2025-03-05 | 200    | 150.00      |
| 4        | 2025-04-12 | 250    | 200.00      |
| 5        | 2025-05-20 | 300    | 250.00      |

---

#### 2. Assign a dense rank to employees based on their salary.
**Similar Problem:**
👉 [Top Three Salaries– DataLemur](http://datalemur.com/questions/sql-top-three-salaries)

**Input Table: `employees`**

| emp\_id | name  | salary |
| ------- | ----- | ------ |
| 1       | Alice | 5000   |
| 2       | Bob   | 7000   |
| 3       | Carol | 7000   |
| 4       | Dave  | 6000   |
| 5       | Eve   | 5000   |

**SQL Query:**

```sql
SELECT
  emp_id,
  name,
  salary,
  DENSE_RANK() OVER (ORDER BY salary DESC) AS salary_rank
FROM employees;
```

**Output Table:**

| emp\_id | name  | salary | salary\_rank |
| ------- | ----- | ------ | ------------ |
| 2       | Bob   | 7000   | 1            |
| 3       | Carol | 7000   | 1            |
| 4       | Dave  | 6000   | 2            |
| 1       | Alice | 5000   | 3            |
| 5       | Eve   | 5000   | 3            |

---

#### 3. Retrieve the first and last order date for each customer.

**Input Table: `orders`**

| order\_id | customer\_id | order\_date |
| --------- | ------------ | ----------- |
| 1         | 101          | 2025-01-10  |
| 2         | 102          | 2025-01-15  |
| 3         | 101          | 2025-02-20  |
| 4         | 102          | 2025-03-05  |
| 5         | 101          | 2025-04-12  |

**SQL Query:**

```sql
SELECT DISTINCT
  customer_id,
  FIRST_VALUE(order_date) OVER (PARTITION BY customer_id ORDER BY order_date) AS first_order,
  LAST_VALUE(order_date) OVER (
    PARTITION BY customer_id ORDER BY order_date
    ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
  ) AS last_order
FROM orders;
```

**Output Table:**

| customer\_id | first\_order | last\_order |
| ------------ | ------------ | ----------- |
| 101          | 2025-01-10   | 2025-04-12  |
| 102          | 2025-01-15   | 2025-03-05  |

---

#### 4. Find the Nth highest salary for each department using window functions.

**Input Table: `employees`**

| emp\_id | name  | department | salary |
| ------- | ----- | ---------- | ------ |
| 1       | Alice | HR         | 5000   |
| 2       | Bob   | HR         | 7000   |
| 3       | Carol | IT         | 7000   |
| 4       | Dave  | IT         | 6000   |
| 5       | Eve   | HR         | 6000   |

**SQL Query (3rd highest salary per department):**

```sql
SELECT *
FROM (
  SELECT
    emp_id,
    name,
    department,
    salary,
    DENSE_RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS salary_rank
  FROM employees
) ranked
WHERE salary_rank = 3;
```

**Output Table:**

| emp\_id | name  | department | salary | salary\_rank |
| ------- | ----- | ---------- | ------ | ------------ |
| 1       | Alice | HR         | 5000   | 3            |

---

#### 5. Determine the percentage of total sales contributed by each employee.

**Input Table: `sales`**

| sale\_id | emp\_id | amount |
| -------- | ------- | ------ |
| 1        | 1       | 100    |
| 2        | 2       | 200    |
| 3        | 1       | 150    |
| 4        | 3       | 250    |
| 5        | 2       | 300    |

**SQL Query:**

```sql
SELECT
  emp_id,
  SUM(amount) AS total_sales,
  ROUND(SUM(amount) * 100.0 / SUM(SUM(amount)) OVER (), 2) AS percentage
FROM sales
GROUP BY emp_id;
```

or

```sql
WITH sales_with_employee_totals AS (
    SELECT 
        employee_id,
        employee_name,
        sale_amount,
        SUM(sale_amount) OVER (PARTITION BY employee_id) AS employee_total_sales,
        SUM(sale_amount) OVER () AS total_sales
    FROM sales
)
SELECT 
    DISTINCT employee_id,
    employee_name,
    employee_total_sales,
    total_sales,
    ROUND((employee_total_sales * 100.0) / total_sales, 2) AS percentage_of_total_sales
FROM 
    sales_with_employee_totals;
```

**Output Table:**

| emp\_id | total\_sales | percentage |
| ------- | ------------ | ---------- |
| 1       | 250          | 20.83      |
| 2       | 500          | 41.67      |
| 3       | 250          | 20.83      |

---

### ➤ 𝗖𝗼𝗺𝗺𝗼𝗻 𝗧𝗮𝗯𝗹𝗲 𝗘𝘅𝗽𝗿𝗲𝘀𝘀𝗶𝗼𝗻𝘀 (𝗖𝗧𝗘)

---

#### 1. Use a CTE to split a full name into first and last names.

**Input Table: `employees`**

| emp\_id | full\_name    |
| ------- | ------------- |
| 1       | John Doe      |
| 2       | Jane Smith    |
| 3       | Alice Johnson |

**SQL Query:**

```sql
WITH name_split AS (
  SELECT
    emp_id,
    SPLIT_PART(full_name, ' ', 1) AS first_name,
    SPLIT_PART(full_name, ' ', 2) AS last_name
  FROM employees
)
SELECT * FROM name_split;
```

**Output Table:**

| emp\_id | first\_name | last\_name |
| ------- | ----------- | ---------- |
| 1       | John        | Doe        |
| 2       | Jane        | Smith      |
| 3       | Alice       | Johnson    |

---

#### 2. Write a CTE to find the longest consecutive streak of sales for an employee.

**Input Table: `sales`**

| sale\_id | emp\_id | sale\_date |
| -------- | ------- | ---------- |
| 1        | 1       | 2025-01-01 |
| 2        | 1       | 2025-01-02 |
| 3        | 1       | 2025-01-04 |
| 4        | 2       | 2025-01-01 |
| 5        | 2       | 2025-01-02 |
| 6        | 2       | 2025-01-03 |

**SQL Query:**

```sql
WITH sales_with_rn AS (
  SELECT
    emp_id,
    sale_date,
    ROW_NUMBER() OVER (PARTITION BY emp_id ORDER BY sale_date) AS rn
  FROM sales
),
streaks AS (
  SELECT
    emp_id,
    sale_date,
    sale_date - INTERVAL '1 day' * (rn - 1) AS streak_group
  FROM sales_with_rn
),
streak_counts AS (
  SELECT
    emp_id,
    COUNT(*) AS streak_length
  FROM streaks
  GROUP BY emp_id, streak_group
)
SELECT emp_id, MAX(streak_length) AS longest_streak
FROM streak_counts
GROUP BY emp_id;
```

**Output Table:**

| emp\_id | longest\_streak |
| ------- | --------------- |
| 1       | 2               |
| 2       | 3               |

---

#### 3. Generate Fibonacci numbers up to a given limit using a recursive CTE.

**SQL Query:**

```sql
WITH RECURSIVE fibonacci(a, b) AS (
  SELECT 0, 1
  UNION ALL
  SELECT b, a + b FROM fibonacci
  WHERE a + b <= 100
)
SELECT a FROM fibonacci;
```

**Output Table:**

| a  |
| -- |
| 0  |
| 1  |
| 1  |
| 2  |
| 3  |
| 5  |
| 8  |
| 13 |
| 21 |
| 34 |
| 55 |
| 89 |

---

#### 4. Use a CTE to identify duplicate records in a table.

**Input Table: `orders`**

| order\_id | customer\_id | order\_date |
| --------- | ------------ | ----------- |
| 1         | 101          | 2025-01-01  |
| 2         | 101          | 2025-01-01  |
| 3         | 102          | 2025-01-02  |
| 4         | 103          | 2025-01-03  |
| 5         | 103          | 2025-01-03  |

**SQL Query:**

```sql
WITH duplicates AS (
  SELECT
    customer_id,
    order_date,
    COUNT(*) AS cnt
  FROM orders
  GROUP BY customer_id, order_date
  HAVING COUNT(*) > 1
)
SELECT o.*
FROM orders o
JOIN duplicates d
  ON o.customer_id = d.customer_id AND o.order_date = d.order_date;
```

**Output Table:**

| order\_id | customer\_id | order\_date |
| --------- | ------------ | ----------- |
| 1         | 101          | 2025-01-01  |
| 2         | 101          | 2025-01-01  |
| 4         | 103          | 2025-01-03  |
| 5         | 103          | 2025-01-03  |

---

#### 5. Find the total sales for each category and filter categories with sales greater than a threshold using a CTE.

**Input Table: `products`**

| product\_id | category | sales\_amount |
| ----------- | -------- | ------------- |
| 1           | A        | 100           |
| 2           | B        | 200           |
| 3           | A        | 150           |
| 4           | C        | 50            |
| 5           | B        | 100           |

**SQL Query:**

```sql
WITH category_sales AS (
  SELECT
    category,
    SUM(sales_amount) AS total_sales
  FROM products
  GROUP BY category
)
SELECT *
FROM category_sales
WHERE total_sales > 200;
```

**Output Table:**

| category | total\_sales |
| -------- | ------------ |
| B        | 300          |

---

### ➤ 𝗝𝗼𝗶𝗻𝘀 (𝗜𝗻𝗻𝗲𝗿, 𝗢𝘂𝘁𝗲𝗿, 𝗖𝗿𝗼𝘀𝘀, 𝗦𝗲𝗹𝗳)

---

#### 1. Retrieve a list of customers who have placed orders and those who have not placed orders (Full Outer Join).

**Input Tables:**

**`customers`**

| customer\_id | customer\_name |
| ------------ | -------------- |
| 1            | Alice          |
| 2            | Bob            |
| 3            | Charlie        |
| 4            | David          |

**`orders`**

| order\_id | customer\_id | order\_date |
| --------- | ------------ | ----------- |
| 101       | 1            | 2025-01-10  |
| 102       | 2            | 2025-01-15  |
| 103       | 1            | 2025-02-20  |

**SQL Query:**

```sql
SELECT
  c.customer_id,
  c.customer_name,
  o.order_id,
  o.order_date
FROM customers c
FULL OUTER JOIN orders o
  ON c.customer_id = o.customer_id;
```

**Output Table:**

| customer\_id | customer\_name | order\_id | order\_date |
| ------------ | -------------- | --------- | ----------- |
| 1            | Alice          | 101       | 2025-01-10  |
| 1            | Alice          | 103       | 2025-02-20  |
| 2            | Bob            | 102       | 2025-01-15  |
| 3            | Charlie        | NULL      | NULL        |
| 4            | David          | NULL      | NULL        |

---

#### 2. Find employees working on multiple projects using a self join.

**Input Table: `employee_projects`**

| emp\_id | project\_id |
| ------- | ----------- |
| 1       | 1001        |
| 1       | 1002        |
| 2       | 1001        |
| 3       | 1003        |
| 3       | 1004        |
| 4       | 1005        |

**SQL Query:**

```sql
SELECT DISTINCT
  ep1.emp_id
FROM employee_projects ep1
JOIN employee_projects ep2
  ON ep1.emp_id = ep2.emp_id AND ep1.project_id <> ep2.project_id;
```

**Output Table:**

| emp\_id |
| ------- |
| 1       |
| 3       |

---

#### 3. Match orders with customers and also display unmatched orders (Left Join).

**Input Tables:**

**`orders`**

| order\_id | customer\_id | order\_date |
| --------- | ------------ | ----------- |
| 201       | 1            | 2025-03-01  |
| 202       | 2            | 2025-03-05  |
| 203       | 5            | 2025-03-10  |

**`customers`**

| customer\_id | customer\_name |
| ------------ | -------------- |
| 1            | Alice          |
| 2            | Bob            |
| 3            | Charlie        |

**SQL Query:**

```sql
SELECT
  o.order_id,
  o.customer_id,
  c.customer_name,
  o.order_date
FROM orders o
LEFT JOIN customers c
  ON o.customer_id = c.customer_id;
```

**Output Table:**

| order\_id | customer\_id | customer\_name | order\_date |
| --------- | ------------ | -------------- | ----------- |
| 201       | 1            | Alice          | 2025-03-01  |
| 202       | 2            | Bob            | 2025-03-05  |
| 203       | 5            | NULL           | 2025-03-10  |

---

#### 4. Generate a product pair list but exclude pairs with identical products (Cross Join with condition).

**Input Table: `products`**

| product\_id | product\_name |
| ----------- | ------------- |
| 1           | Laptop        |
| 2           | Mouse         |
| 3           | Keyboard      |

**SQL Query:**

```sql
SELECT
  p1.product_name AS product_a,
  p2.product_name AS product_b
FROM products p1
CROSS JOIN products p2
WHERE p1.product_id < p2.product_id;
```

**Output Table:**

| product\_a | product\_b |
| ---------- | ---------- |
| Laptop     | Mouse      |
| Laptop     | Keyboard   |
| Mouse      | Keyboard   |

---

#### 5. Retrieve employees and their managers using a self join.

**Input Table: `employees`**

| emp\_id | emp\_name | manager\_id |
| ------- | --------- | ----------- |
| 1       | John      | NULL        |
| 2       | Alice     | 1           |
| 3       | Bob       | 1           |
| 4       | Carol     | 2           |

**SQL Query:**

```sql
SELECT
  e.emp_name AS employee,
  m.emp_name AS manager
FROM employees e
LEFT JOIN employees m
  ON e.manager_id = m.emp_id;
```

**Output Table:**

| employee | manager |
| -------- | ------- |
| John     | NULL    |
| Alice    | John    |
| Bob      | John    |
| Carol    | Alice   |

---

### ➤ **Subqueries**

---

#### 1. Find customers whose total order amount is greater than the average order amount.

**Input Tables:**

**`customers`**

| customer\_id | customer\_name |
| ------------ | -------------- |
| 1            | Alice          |
| 2            | Bob            |
| 3            | Charlie        |

**`orders`**

| order\_id | customer\_id | order\_amount |
| --------- | ------------ | ------------- |
| 101       | 1            | 250           |
| 102       | 1            | 300           |
| 103       | 2            | 100           |
| 104       | 3            | 150           |
| 105       | 2            | 200           |

---

**SQL Query:**

```sql
SELECT customer_id, SUM(order_amount) AS total_order_amount
FROM orders
GROUP BY customer_id
HAVING SUM(order_amount) > (
    SELECT AVG(total)
    FROM (
        SELECT SUM(order_amount) AS total
        FROM orders
        GROUP BY customer_id
    ) sub
);
```

---

**Output Table:**

| customer\_id | total\_order\_amount |
| ------------ | -------------------- |
| 1            | 550                  |

---

#### 2. Retrieve employees who earn the lowest salary in their department.

**Input Table: `employees`**

| emp\_id | emp\_name | department | salary |
| ------- | --------- | ---------- | ------ |
| 1       | John      | HR         | 5000   |
| 2       | Alice     | HR         | 4000   |
| 3       | Bob       | IT         | 6000   |
| 4       | Carol     | IT         | 6000   |
| 5       | Dave      | IT         | 3500   |

---

**SQL Query:**

```sql
SELECT emp_id, emp_name, department, salary
FROM employees e1
WHERE salary = (
    SELECT MIN(salary)
    FROM employees e2
    WHERE e1.department = e2.department
);
```

---

**Output Table:**

| emp\_id | emp\_name | department | salary |
| ------- | --------- | ---------- | ------ |
| 2       | Alice     | HR         | 4000   |
| 5       | Dave      | IT         | 3500   |

---

#### 3. Identify products that have been ordered more than 10 times using a subquery.

**Input Tables:**

**`products`**

| product\_id | product\_name |
| ----------- | ------------- |
| 1           | Laptop        |
| 2           | Mouse         |
| 3           | Keyboard      |

**`order_items`**

| order\_item\_id | order\_id | product\_id | quantity |
| --------------- | --------- | ----------- | -------- |
| 1               | 101       | 1           | 1        |
| 2               | 102       | 2           | 5        |
| 3               | 103       | 2           | 6        |
| 4               | 104       | 3           | 2        |

---

**SQL Query:**

```sql
SELECT product_id, product_name
FROM products
WHERE product_id IN (
    SELECT product_id
    FROM order_items
    GROUP BY product_id
    HAVING SUM(quantity) > 10
);
```

---

**Output Table:**

| product\_id | product\_name |
| ----------- | ------------- |
| 2           | Mouse         |

---

#### 4. Find regions where the maximum sales are below a given threshold.

**Input Table: `sales`**

| sale\_id | region | sale\_amount |
| -------- | ------ | ------------ |
| 1        | North  | 5000         |
| 2        | South  | 3000         |
| 3        | East   | 2000         |
| 4        | West   | 7000         |
| 5        | South  | 2500         |

---

**SQL Query:**

```sql
SELECT region
FROM sales
GROUP BY region
HAVING MAX(sale_amount) < 6000;
```

---

**Output Table:**

| region |
| ------ |
| South  |
| East   |

---

### ➤ **Aggregate Functions**

---

#### 1. Calculate the median salary for each department.

**Input Table: `employees`**

| emp\_id | emp\_name | department | salary |
| ------- | --------- | ---------- | ------ |
| 1       | John      | HR         | 4000   |
| 2       | Alice     | HR         | 6000   |
| 3       | Bob       | IT         | 3500   |
| 4       | Carol     | IT         | 4500   |
| 5       | Dave      | IT         | 5500   |

---

**SQL Query:**

```sql
SELECT department,
       PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY salary) AS median_salary
FROM employees
GROUP BY department;
```

---

**Output Table:**

| department | median\_salary |
| ---------- | -------------- |
| HR         | 5000           |
| IT         | 4500           |

---

#### 2. Find the total sales for each month and rank them in descending order.

**Input Table: `sales`**

| sale\_id | sale\_date | sale\_amount |
| -------- | ---------- | ------------ |
| 1        | 2024-01-15 | 1000         |
| 2        | 2024-01-20 | 1500         |
| 3        | 2024-02-10 | 2000         |
| 4        | 2024-02-25 | 2500         |
| 5        | 2024-03-05 | 3000         |

---

**SQL Query:**

```sql
SELECT DATE_TRUNC('month', sale_date) AS sale_month,
       SUM(sale_amount) AS total_sales,
       RANK() OVER (ORDER BY SUM(sale_amount) DESC) AS sales_rank
FROM sales
GROUP BY sale_month
ORDER BY sales_rank;
```

---

**Output Table:**

| sale\_month | total\_sales | sales\_rank |
| ----------- | ------------ | ----------- |
| 2024-03-01  | 3000         | 1           |
| 2024-02-01  | 4500         | 2           |
| 2024-01-01  | 2500         | 3           |

---

#### 3. Count the number of distinct customers for each product.

**Input Tables:**

**`products`**

| product\_id | product\_name |
| ----------- | ------------- |
| 1           | Laptop        |
| 2           | Mouse         |

**`orders`**

| order\_id | customer\_id | product\_id |
| --------- | ------------ | ----------- |
| 101       | 1            | 1           |
| 102       | 2            | 1           |
| 103       | 1            | 2           |
| 104       | 3            | 1           |
| 105       | 2            | 2           |

---

**SQL Query:**

```sql
SELECT p.product_id, p.product_name, COUNT(DISTINCT o.customer_id) AS distinct_customers
FROM products p
LEFT JOIN orders o ON p.product_id = o.product_id
GROUP BY p.product_id, p.product_name;
```

---

**Output Table:**

| product\_id | product\_name | distinct\_customers |
| ----------- | ------------- | ------------------- |
| 1           | Laptop        | 3                   |
| 2           | Mouse         | 2                   |

---

#### 4. Retrieve the top 5 regions by total sales.

**Input Table: `sales`**

| sale\_id | region | sale\_amount |
| -------- | ------ | ------------ |
| 1        | North  | 1000         |
| 2        | South  | 2000         |
| 3        | East   | 1500         |
| 4        | West   | 2500         |
| 5        | North  | 3000         |
| 6        | South  | 1200         |

---

**SQL Query:**

```sql
SELECT region, SUM(sale_amount) AS total_sales
FROM sales
GROUP BY region
ORDER BY total_sales DESC
LIMIT 5;
```

---

**Output Table:**

| region | total\_sales |
| ------ | ------------ |
| North  | 4000         |
| West   | 2500         |
| South  | 3200         |
| East   | 1500         |

---

#### 5. Calculate the average order value for each customer.

**Input Tables:**

**`customers`**

| customer\_id | customer\_name |
| ------------ | -------------- |
| 1            | Alice          |
| 2            | Bob            |

**`orders`**

| order\_id | customer\_id | order\_amount |
| --------- | ------------ | ------------- |
| 101       | 1            | 100           |
| 102       | 1            | 300           |
| 103       | 2            | 200           |
| 104       | 2            | 400           |

---

**SQL Query:**

```sql
SELECT c.customer_id, c.customer_name, AVG(o.order_amount) AS avg_order_value
FROM customers c
JOIN orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.customer_name;
```

---

**Output Table:**

| customer\_id | customer\_name | avg\_order\_value |
| ------------ | -------------- | ----------------- |
| 1            | Alice          | 200               |
| 2            | Bob            | 300               |

---
Great! Here are the **Indexing and Performance** questions with sample tables, queries, and results explained clearly:

---

### ➤ **Indexing and Performance**

---

#### 1. Write a query to find duplicate values in an indexed column.

**Input Table: `users`**

| user\_id | email                                         | username |
| -------- | --------------------------------------------- | -------- |
| 1        | [alice@example.com](mailto:alice@example.com) | alice1   |
| 2        | [bob@example.com](mailto:bob@example.com)     | bob      |
| 3        | [alice@example.com](mailto:alice@example.com) | alice2   |
| 4        | [carol@example.com](mailto:carol@example.com) | carol    |
| 5        | [bob@example.com](mailto:bob@example.com)     | bobby    |

**Note:** `email` column is indexed.

---

**SQL Query:**

```sql
SELECT email, COUNT(*) AS count_duplicates
FROM users
GROUP BY email
HAVING COUNT(*) > 1;
```

---

**Output Table:**

| email                                         | count\_duplicates |
| --------------------------------------------- | ----------------- |
| [alice@example.com](mailto:alice@example.com) | 2                 |
| [bob@example.com](mailto:bob@example.com)     | 2                 |

---

#### 2. Analyze the impact of adding a composite index on query performance.

**Input Table: `orders`**

| order\_id | customer\_id | order\_date | total\_amount |
| --------- | ------------ | ----------- | ------------- |
| 101       | 1            | 2024-01-01  | 100           |
| 102       | 2            | 2024-01-02  | 200           |
| 103       | 1            | 2024-01-05  | 150           |
| 104       | 3            | 2024-01-07  | 300           |

---

**Scenario:**

* Query to find orders by a specific customer in a date range:

```sql
SELECT * FROM orders
WHERE customer_id = 1
AND order_date BETWEEN '2024-01-01' AND '2024-01-31';
```

* Without an index, this query scans the whole table.
* Adding a **composite index** on `(customer_id, order_date)` improves query speed by allowing the DB engine to quickly find relevant rows.

---

**Composite index:**

```sql
CREATE INDEX idx_customer_date ON orders(customer_id, order_date);
```

---

**Explanation:**

* The composite index helps PostgreSQL quickly filter by `customer_id` first, then by `order_date` within that customer.
* This reduces the scanned rows significantly, boosting performance especially for large tables.

---

#### 3. Identify columns with high cardinality that could benefit from indexing.

**Input Table: `products`**

| product\_id | category    | sku    | price |
| ----------- | ----------- | ------ | ----- |
| 1           | Electronics | ABC123 | 100   |
| 2           | Electronics | ABC124 | 150   |
| 3           | Clothing    | XYZ987 | 50    |
| 4           | Electronics | ABC125 | 200   |
| 5           | Clothing    | XYZ988 | 60    |

---

**Analysis:**

* `sku` has very high cardinality (unique values).
* `category` has low cardinality (few unique values).
* **Recommendation:**

  * Index `sku` to speed up searches or joins using SKU.
  * Indexing `category` is less useful because it has few distinct values (low cardinality).

---

#### 4. Compare query execution times before and after adding a clustered index.

**Input Table: `logs`**

| log\_id | user\_id | event\_time         | event\_type |
| ------- | -------- | ------------------- | ----------- |
| 1       | 1        | 2024-05-01 10:00:00 | login       |
| 2       | 2        | 2024-05-01 10:05:00 | purchase    |
| 3       | 1        | 2024-05-01 10:10:00 | logout      |
| ...     | ...      | ...                 | ...         |

---

**Explanation:**

* Before clustering, data is stored unordered; query scanning event\_time ranges is slower.
* Create a clustered index on `event_time`:

```sql
CREATE INDEX idx_event_time ON logs(event_time);
CLUSTER logs USING idx_event_time;
```

* After clustering, related rows by `event_time` are physically stored together, making range queries faster.

---

#### 5. Write a query that avoids the use of an index to test performance differences.

**Input Table: `customers`**

| customer\_id | customer\_name | city     |
| ------------ | -------------- | -------- |
| 1            | Alice          | New York |
| 2            | Bob            | Chicago  |
| 3            | Carol          | New York |
| 4            | Dave           | Chicago  |

---

**Scenario:**

* Assume `city` is indexed.
* To avoid using the index for testing, add a function or cast that disables index use:

```sql
SELECT * FROM customers
WHERE LOWER(city) = 'new york';
```

* Using `LOWER(city)` disables the index on `city` if the index is on the column as is.

---

**Alternative:**

Use `NOT` on indexed columns or cast to a different type to prevent index usage.

---



