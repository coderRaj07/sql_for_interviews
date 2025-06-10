### 🔹 1. Find the second highest salary without using LIMIT, OFFSET, or TOP

**📌 Question**
Find the second-highest salary in the `Employee` table without using `LIMIT`, `OFFSET`, or `TOP`.

**📋 Example Table**

*Employee:*

| id | name  | salary |
| -- | ----- | ------ |
| 1  | Alice | 100000 |
| 2  | Bob   | 80000  |
| 3  | Carol | 120000 |
| 4  | Dave  | 120000 |

**✅ Expected Output:**

| secondHighestSalary |
| ------------------- |
| 100000              |

**🛠️ Solution**

Use a subquery to find the maximum salary less than the top salary:

```sql
SELECT MAX(salary) AS secondHighestSalary
FROM Employee
WHERE salary < (
  SELECT MAX(salary) FROM Employee
);
```

Alternatively, use a window function:

```sql
WITH Ranked AS (
  SELECT salary,
         DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
  FROM Employee
)
SELECT salary AS secondHighestSalary
FROM Ranked
WHERE rnk = 2;
```

**🧾 Final Output Table**

| secondHighestSalary |
| ------------------- |
| 100000              |

---

### 🔹 2. Running total of daily revenue

**📌 Question**
Given an `Orders` table with daily revenues, write a query to compute the running total (cumulative sum) of revenue for each date.

**📋 Example Table**

*Orders*:

| orderDate  | revenue |
| ---------- | ------- |
| 2025-01-01 | 100     |
| 2025-01-02 | 150     |
| 2025-01-03 | 120     |
| 2025-01-04 | 200     |

**✅ Expected Output:**

| orderDate  | revenue | runningTotal |
| ---------- | ------- | ------------ |
| 2025-01-01 | 100     | 100          |
| 2025-01-02 | 150     | 250          |
| 2025-01-03 | 120     | 370          |
| 2025-01-04 | 200     | 570          |

**🛠️ Solution**

Use a window function with `SUM OVER`:

```sql
SELECT
  orderDate,
  revenue,
  SUM(revenue) OVER (ORDER BY orderDate) AS runningTotal
FROM Orders
ORDER BY orderDate;
```

**🧾 Final Output Table**

| orderDate  | revenue | runningTotal |
| ---------- | ------- | ------------ |
| 2025-01-01 | 100     | 100          |
| 2025-01-02 | 150     | 250          |
| 2025-01-03 | 120     | 370          |
| 2025-01-04 | 200     | 570          |


### 🔹 3. Identify employees who earn more than their managers

**📌 Question**
Find all employees who earn more than their manager. Use the `Employee` table, where each employee has a `managerId` referencing another `id`.

**📋 Example Table**

*Employee:*

| id | name  | salary | managerId |
| -- | ----- | ------ | --------- |
| 1  | Alice | 100000 | NULL      |
| 2  | Bob   | 90000  | 1         |
| 3  | Carol | 110000 | 1         |
| 4  | Dave  | 80000  | 2         |

**✅ Expected Output:**

| name  |
| ----- |
| Carol |

**🛠️ Solution**

```sql
SELECT e.name
FROM Employee e
JOIN Employee m ON e.managerId = m.id
WHERE e.salary > m.salary;
```

**🧾 Final Output Table**

| name  |
| ----- |
| Carol |

---

### 🔹 4. Find top N customers by purchase amount (remove duplicates)

**📌 Question**
Return the top N customers who made the highest total purchases. Ensure no duplicate amounts (i.e., use `DENSE_RANK`).

**📋 Example Table**

*Transactions:*

| customerId | amount |
| ---------- | ------ |
| 101        | 300    |
| 102        | 500    |
| 103        | 500    |
| 104        | 400    |

**✅ Expected Output for N=2:**

| customerId | amount |
| ---------- | ------ |
| 102        | 500    |
| 103        | 500    |
| 104        | 400    |

**🛠️ Solution**

```sql
WITH Ranked AS (
  SELECT customerId, amount,
         DENSE_RANK() OVER (ORDER BY amount DESC) AS rnk
  FROM Transactions
)
SELECT customerId, amount
FROM Ranked
WHERE rnk <= 2;
```

**🧾 Final Output Table**

| customerId | amount |
| ---------- | ------ |
| 102        | 500    |
| 103        | 500    |
| 104        | 400    |


### 🔹 5. Identify users with at least 3-day login streaks

**📌 Question**
Given a `Logins` table, identify users who logged in for at least **three consecutive days**.

**📋 Example Table**

*Logins:*

| userId | loginDate  |
| ------ | ---------- |
| 1      | 2025-06-01 |
| 1      | 2025-06-02 |
| 1      | 2025-06-03 |
| 1      | 2025-06-05 |
| 2      | 2025-06-01 |
| 2      | 2025-06-03 |
| 2      | 2025-06-04 |

**✅ Expected Output:**

| userId |
| ------ |
| 1      |

**🛠️ Solution**
Use date difference partitioned by user:

```sql
WITH Ranked AS (
  SELECT userId, loginDate,
         ROW_NUMBER() OVER (PARTITION BY userId ORDER BY loginDate) AS rn
  FROM Logins
),
Grouped AS (
  SELECT userId, loginDate,
         DATE_SUB(loginDate, INTERVAL rn DAY) AS grp
  FROM Ranked
),
Streaks AS (
  SELECT userId, COUNT(*) AS streakLength
  FROM Grouped
  GROUP BY userId, grp
)
SELECT DISTINCT userId
FROM Streaks
WHERE streakLength >= 3;
```

**🧾 Final Output Table**

| userId |
| ------ |
| 1      |


### 🔹 6. Delete duplicate records but keep one copy

**📌 Question**
Detect and delete duplicate rows from a table, retaining **only one copy** of each.

**📋 Example Table**

*Users:*

| id | name    | email                                     |
| -- | ------- | ----------------------------------------- |
| 1  | Alice   | [alice@email.com](mailto:alice@email.com) |
| 2  | Bob     | [bob@email.com](mailto:bob@email.com)     |
| 3  | Alice   | [alice@email.com](mailto:alice@email.com) |
| 4  | Charlie | [charlie@ex.com](mailto:charlie@ex.com)   |

**✅ Expected Output After Deletion:**

| id | name    | email                                     |
| -- | ------- | ----------------------------------------- |
| 1  | Alice   | [alice@email.com](mailto:alice@email.com) |
| 2  | Bob     | [bob@email.com](mailto:bob@email.com)     |
| 4  | Charlie | [charlie@ex.com](mailto:charlie@ex.com)   |

**🛠️ Solution** (Using `ROW_NUMBER` to retain one copy)

```sql
WITH Duplicates AS (
  SELECT *, ROW_NUMBER() OVER (
    PARTITION BY name, email ORDER BY id
  ) AS rn
  FROM Users
)
DELETE FROM Users
WHERE id IN (
  SELECT id FROM Duplicates WHERE rn > 1
);
```

**🧾 Final Output Table**

| id | name    | email                                     |
| -- | ------- | ----------------------------------------- |
| 1  | Alice   | [alice@email.com](mailto:alice@email.com) |
| 2  | Bob     | [bob@email.com](mailto:bob@email.com)     |
| 4  | Charlie | [charlie@ex.com](mailto:charlie@ex.com)   |

---

### 🔹 7. Optimize a slow query with joins and aggregations

**📌 Question**
Given millions of records with joins and aggregations, how would you optimize a slow query?

**📋 Example Scenario**

You have a query joining `Orders`, `Products`, and `Customers`, calculating total revenue per region.

**🛠️ Solution Steps**

1. **Indexes**: Ensure indexes exist on `JOIN` and `GROUP BY` keys:

   * `Orders(customer_id)`
   * `Orders(product_id)`
   * `Customers(region)`
   * `Products(id)`

2. **Avoid SELECT \***: Select only needed columns.

3. **Materialize Intermediate Results**: Use CTEs or temp tables.

4. **Filter Early**: Apply `WHERE` before joins if possible.

5. **Use EXPLAIN ANALYZE**: Debug query plan.

**Optimized Example Query:**

```sql
WITH OrderRevenue AS (
  SELECT customer_id, product_id, quantity * price AS revenue
  FROM Orders
  JOIN Products ON Orders.product_id = Products.id
)
SELECT c.region, SUM(o.revenue) AS total_revenue
FROM OrderRevenue o
JOIN Customers c ON o.customer_id = c.id
GROUP BY c.region;
```

**🧾 Final Output Table**

| region | total\_revenue |
| ------ | -------------- |
| East   | 50000          |
| West   | 65000          |

---

### 🔹 8. First order per customer (handle ties)

**📌 Question**
Retrieve the **first order** for each customer, including **all orders** if there's a tie on the same earliest date.

**📋 Example Table**

*Orders:*

| orderId | customerId | orderDate  |
| ------- | ---------- | ---------- |
| 1       | 101        | 2023-01-01 |
| 2       | 101        | 2023-01-01 |
| 3       | 101        | 2023-01-03 |
| 4       | 102        | 2023-01-02 |

**✅ Expected Output:**

| orderId | customerId | orderDate  |
| ------- | ---------- | ---------- |
| 1       | 101        | 2023-01-01 |
| 2       | 101        | 2023-01-01 |
| 4       | 102        | 2023-01-02 |

**🛠️ Solution**

```sql
WITH FirstOrders AS (
  SELECT customerId, MIN(orderDate) AS firstOrderDate
  FROM Orders
  GROUP BY customerId
)
SELECT o.*
FROM Orders o
JOIN FirstOrders f
  ON o.customerId = f.customerId AND o.orderDate = f.firstOrderDate;
```

**🧾 Final Output Table**

| orderId | customerId | orderDate  |
| ------- | ---------- | ---------- |
| 1       | 101        | 2023-01-01 |
| 2       | 101        | 2023-01-01 |
| 4       | 102        | 2023-01-02 |

### 🔹 9. Find products that were never purchased by any customer

**📌 Question**
Given `Products` and `Orders` tables, find all products that have **never been purchased**.

**📋 Example Tables**

*Products:*

| productId | productName |
| --------- | ----------- |
| 1         | Apple       |
| 2         | Banana      |
| 3         | Mango       |

*Orders:*

| orderId | productId |
| ------- | --------- |
| 101     | 1         |
| 102     | 1         |
| 103     | 2         |

**✅ Expected Output:**

| productId | productName |
| --------- | ----------- |
| 3         | Mango       |

**🛠️ Solution**

```sql
SELECT p.productId, p.productName
FROM Products p
LEFT JOIN Orders o ON p.productId = o.productId
WHERE o.productId IS NULL;
```

**🧾 Final Output Table**

| productId | productName |
| --------- | ----------- |
| 3         | Mango       |

---

### 🔹 10. Customers who made transactions in **every month** of a year

**📌 Question**
Given a `Transactions` table, find customers who made at least one transaction **in every month** of a specific year.

**📋 Example Table**

*Transactions:*

| customerId | txnDate    |
| ---------- | ---------- |
| 1          | 2024-01-05 |
| 1          | 2024-02-10 |
| 1          | 2024-03-22 |
| ...        | ...        |
| 1          | 2024-12-01 |
| 2          | 2024-01-08 |
| 2          | 2024-03-15 |

**✅ Expected Output:**

| customerId |
| ---------- |
| 1          |

**🛠️ Solution**

```sql
SELECT customerId
FROM (
  SELECT customerId, COUNT(DISTINCT MONTH(txnDate)) AS monthCount
  FROM Transactions
  WHERE YEAR(txnDate) = 2024
  GROUP BY customerId
) AS monthly
WHERE monthCount = 12;
```

**🧾 Final Output Table**

| customerId |
| ---------- |
| 1          |

---

### 🔹 11. Employees with same salary in same department

**📌 Question**
Find employees who have **the same salary** as someone else in **the same department**.

**📋 Example Table**

*Employee:*

| id | name  | salary | deptId |
| -- | ----- | ------ | ------ |
| 1  | Alice | 50000  | 10     |
| 2  | Bob   | 60000  | 10     |
| 3  | Carol | 50000  | 10     |
| 4  | Dave  | 60000  | 20     |
| 5  | Emma  | 60000  | 20     |

**✅ Expected Output:**

| name  | salary | deptId |
| ----- | ------ | ------ |
| Alice | 50000  | 10     |
| Carol | 50000  | 10     |
| Dave  | 60000  | 20     |
| Emma  | 60000  | 20     |

**🛠️ Solution**

```sql
SELECT name, salary, deptId
FROM Employee
WHERE (salary, deptId) IN (
  SELECT salary, deptId
  FROM Employee
  GROUP BY salary, deptId
  HAVING COUNT(*) > 1
);
```

**🧾 Final Output Table**

| name  | salary | deptId |
| ----- | ------ | ------ |
| Alice | 50000  | 10     |
| Carol | 50000  | 10     |
| Dave  | 60000  | 20     |
| Emma  | 60000  | 20     |


### 🔹 12. Department with the highest total salary

**📌 Question**
Given an `Employee` table with `deptId` and `salary`, find the department with the **highest total salary** paid to employees.

**📋 Example Table**

*Employee:*

| id | name  | salary | deptId |
| -- | ----- | ------ | ------ |
| 1  | Alice | 70000  | 10     |
| 2  | Bob   | 50000  | 20     |
| 3  | Carol | 60000  | 10     |
| 4  | Dave  | 40000  | 20     |
| 5  | Emma  | 90000  | 30     |

**✅ Expected Output:**

| deptId | total\_salary |
| ------ | ------------- |
| 10     | 130000        |

**🛠️ Solution**

```sql
WITH DeptTotal AS (
  SELECT deptId, SUM(salary) AS total_salary
  FROM Employee
  GROUP BY deptId
)
SELECT deptId, total_salary
FROM DeptTotal
WHERE total_salary = (
  SELECT MAX(total_salary) FROM DeptTotal
);
```

**🧾 Final Output Table**

| deptId | total\_salary |
| ------ | ------------- |
| 10     | 130000        |

---

### 🔹 13. Top 3 orders per customer by value using window function

**📌 Question**
Rank orders by order value per customer and return only the **top 3 orders** for each customer.

**📋 Example Table**

*Orders:*

| orderId | customerId | orderValue |
| ------- | ---------- | ---------- |
| 1       | 101        | 100        |
| 2       | 101        | 200        |
| 3       | 101        | 300        |
| 4       | 101        | 150        |
| 5       | 102        | 400        |
| 6       | 102        | 100        |

**✅ Expected Output:**

| orderId | customerId | orderValue |
| ------- | ---------- | ---------- |
| 3       | 101        | 300        |
| 2       | 101        | 200        |
| 4       | 101        | 150        |
| 5       | 102        | 400        |
| 6       | 102        | 100        |

**🛠️ Solution**

```sql
WITH RankedOrders AS (
  SELECT *,
         ROW_NUMBER() OVER (
           PARTITION BY customerId
           ORDER BY orderValue DESC
         ) AS rnk
  FROM Orders
)
SELECT orderId, customerId, orderValue
FROM RankedOrders
WHERE rnk <= 3;
```

**🧾 Final Output Table**

| orderId | customerId | orderValue |
| ------- | ---------- | ---------- |
| 3       | 101        | 300        |
| 2       | 101        | 200        |
| 4       | 101        | 150        |
| 5       | 102        | 400        |
| 6       | 102        | 100        |

---

### 🔹 14. Find the median salary of employees

**📌 Question**
Write an SQL query to find the **median salary** without using a built-in median function.

**📋 Example Table**

*Employee:*

| id | salary |
| -- | ------ |
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
| 4  | 400    |
| 5  | 500    |

**✅ Expected Output:**

| median |
| ------ |
| 300    |

**🛠️ Solution** (Works in MySQL 8+ or PostgreSQL)

```sql
WITH Ordered AS (
  SELECT salary,
         ROW_NUMBER() OVER (ORDER BY salary) AS rn,
         COUNT(*) OVER () AS cnt
  FROM Employee
)
SELECT AVG(salary) AS median
FROM Ordered
WHERE rn IN (FLOOR((cnt + 1) / 2.0), CEIL((cnt + 1) / 2.0));
```

**🧾 Final Output Table**

| median |
| ------ |
| 300    |

### 🔹 15. Pivot monthly sales data

**📌 Question**
Given a sales table where each row is a transaction, pivot it so each **month** becomes a **column** with total sales.

**📋 Example Table**

*Sales:*

| id | product | sale\_amount | sale\_date |
| -- | ------- | ------------ | ---------- |
| 1  | Apple   | 100          | 2024-01-15 |
| 2  | Apple   | 200          | 2024-02-10 |
| 3  | Banana  | 150          | 2024-01-20 |
| 4  | Banana  | 100          | 2024-03-25 |

**✅ Expected Output:**

| product | Jan | Feb | Mar |
| ------- | --- | --- | --- |
| Apple   | 100 | 200 | 0   |
| Banana  | 150 | 0   | 100 |

**🛠️ Solution** (MySQL 8+ / PostgreSQL)

```sql
SELECT
  product,
  SUM(CASE WHEN MONTH(sale_date) = 1 THEN sale_amount ELSE 0 END) AS Jan,
  SUM(CASE WHEN MONTH(sale_date) = 2 THEN sale_amount ELSE 0 END) AS Feb,
  SUM(CASE WHEN MONTH(sale_date) = 3 THEN sale_amount ELSE 0 END) AS Mar
FROM Sales
GROUP BY product;
```

**🧾 Final Output Table**

| product | Jan | Feb | Mar |
| ------- | --- | --- | --- |
| Apple   | 100 | 200 | 0   |
| Banana  | 150 | 0   | 100 |

---

### 🔹 16. Moving average of last 7 days per product

**📌 Question**
For each product, calculate the **7-day moving average** of sales.

**📋 Example Table**

*Sales:*

| product | sale\_date | amount |
| ------- | ---------- | ------ |
| Apple   | 2024-06-01 | 100    |
| Apple   | 2024-06-02 | 200    |
| Apple   | 2024-06-07 | 300    |
| Apple   | 2024-06-08 | 400    |

**✅ Expected Output:**

| product | sale\_date | 7d\_avg |
| ------- | ---------- | ------- |
| Apple   | 2024-06-07 | 200.0   |
| Apple   | 2024-06-08 | 300.0   |

**🛠️ Solution**

```sql
SELECT
  s1.product,
  s1.sale_date,
  AVG(s2.amount) AS 7d_avg
FROM Sales s1
JOIN Sales s2
  ON s1.product = s2.product
 AND s2.sale_date BETWEEN s1.sale_date - INTERVAL 6 DAY AND s1.sale_date
GROUP BY s1.product, s1.sale_date;
```

**🧾 Final Output Table**

| product | sale\_date | 7d\_avg |
| ------- | ---------- | ------- |
| Apple   | 2024-06-07 | 200.0   |
| Apple   | 2024-06-08 | 300.0   |

---

### 🔹 17. First and last occurrence of each event per user

**📌 Question**
Find the **first and last date** each user performed a specific event.

**📋 Example Table**

*Events:*

| user\_id | event\_type | event\_date |
| -------- | ----------- | ----------- |
| 1        | login       | 2024-01-01  |
| 1        | login       | 2024-01-04  |
| 2        | login       | 2024-01-02  |
| 2        | login       | 2024-01-06  |

**✅ Expected Output:**

| user\_id | event\_type | first\_date | last\_date |
| -------- | ----------- | ----------- | ---------- |
| 1        | login       | 2024-01-01  | 2024-01-04 |
| 2        | login       | 2024-01-02  | 2024-01-06 |

**🛠️ Solution**

```sql
SELECT
  user_id,
  event_type,
  MIN(event_date) AS first_date,
  MAX(event_date) AS last_date
FROM Events
GROUP BY user_id, event_type;
```

**🧾 Final Output Table**

| user\_id | event\_type | first\_date | last\_date |
| -------- | ----------- | ----------- | ---------- |
| 1        | login       | 2024-01-01  | 2024-01-04 |
| 2        | login       | 2024-01-02  | 2024-01-06 |

### 🔹 18. Users who ordered in two consecutive months but not the third

**📌 Question**
Find users who placed an order in **two consecutive months**, but did **not order in the third** month that follows.

**📋 Example Table**

*Orders:*

| user\_id | order\_date |
| -------- | ----------- |
| 1        | 2024-01-15  |
| 1        | 2024-02-10  |
| 1        | 2024-04-05  |
| 2        | 2024-02-01  |
| 2        | 2024-03-01  |
| 2        | 2024-04-01  |
| 3        | 2024-01-01  |
| 3        | 2024-02-01  |

**✅ Expected Output:**

| user\_id |
| -------- |
| 1        |
| 3        |

**🛠️ Solution** (simplified for readability)

```sql
WITH user_months AS (
  SELECT user_id, EXTRACT(YEAR FROM order_date) AS yr, EXTRACT(MONTH FROM order_date) AS mon
  FROM Orders
  GROUP BY user_id, EXTRACT(YEAR FROM order_date), EXTRACT(MONTH FROM order_date)
),
sequence AS (
  SELECT
    a.user_id,
    a.yr,
    a.mon AS m1,
    b.mon AS m2,
    c.mon AS m3
  FROM user_months a
  JOIN user_months b ON a.user_id = b.user_id AND b.mon = a.mon + 1 AND b.yr = a.yr
  LEFT JOIN user_months c ON a.user_id = c.user_id AND c.mon = a.mon + 2 AND c.yr = a.yr
  WHERE c.mon IS NULL
)
SELECT DISTINCT user_id
FROM sequence;
```

**🧾 Final Output Table**

| user\_id |
| -------- |
| 1        |
| 3        |

---

### 🔹 19. Most frequently purchased product category by each user

**📌 Question**
For each user, find the **most frequently purchased product category** in the past year.

**📋 Example Table**

*Purchases:*

| user\_id | category    | purchase\_date |
| -------- | ----------- | -------------- |
| 1        | Electronics | 2024-02-01     |
| 1        | Grocery     | 2024-02-05     |
| 1        | Electronics | 2024-03-10     |
| 2        | Grocery     | 2024-02-20     |
| 2        | Grocery     | 2024-04-01     |

**✅ Expected Output:**

| user\_id | category    |
| -------- | ----------- |
| 1        | Electronics |
| 2        | Grocery     |

**🛠️ Solution**

```sql
WITH CategoryCount AS (
  SELECT user_id, category, COUNT(*) AS cnt
  FROM Purchases
  WHERE purchase_date >= DATE_SUB(CURDATE(), INTERVAL 1 YEAR)
  GROUP BY user_id, category
),
Ranked AS (
  SELECT *,
         ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY cnt DESC) AS rnk
  FROM CategoryCount
)
SELECT user_id, category
FROM Ranked
WHERE rnk = 1;
```

**🧾 Final Output Table**

| user\_id | category    |
| -------- | ----------- |
| 1        | Electronics |
| 2        | Grocery     |

---

### 🔹 20. Yearly product sales rank (reset each year)

**📌 Question**
Rank products by total sales **per year**, resetting the ranking each year.

**📋 Example Table**

*Sales:*

| product\_id | year | sales |
| ----------- | ---- | ----- |
| A           | 2022 | 100   |
| B           | 2022 | 200   |
| C           | 2022 | 200   |
| A           | 2023 | 300   |
| B           | 2023 | 100   |

**✅ Expected Output:**

| year | product\_id | sales | rank |
| ---- | ----------- | ----- | ---- |
| 2022 | B           | 200   | 1    |
| 2022 | C           | 200   | 1    |
| 2022 | A           | 100   | 3    |
| 2023 | A           | 300   | 1    |
| 2023 | B           | 100   | 2    |

**🛠️ Solution**

```sql
SELECT
  year,
  product_id,
  sales,
  RANK() OVER (PARTITION BY year ORDER BY sales DESC) AS rank
FROM Sales;
```

**🧾 Final Output Table**

| year | product\_id | sales | rank |
| ---- | ----------- | ----- | ---- |
| 2022 | B           | 200   | 1    |
| 2022 | C           | 200   | 1    |
| 2022 | A           | 100   | 3    |
| 2023 | A           | 300   | 1    |
| 2023 | B           | 100   | 2    |

