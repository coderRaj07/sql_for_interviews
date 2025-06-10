### 🔗 1. Combine two tables: [https://lnkd.in/dumdCXC7](https://lnkd.in/dumdCXC7)

**📌 Question**
Combine the `Person` and `Address` tables to output first name, last name, city, and state of each person.

**📋 Example Tables**

*Person:*

| personId | firstName | lastName |
| -------- | --------- | -------- |
| 1        | John      | Doe      |
| 2        | Jane      | Smith    |

*Address:*

| personId | city        | state |
| -------- | ----------- | ----- |
| 1        | New York    | NY    |
| 2        | Los Angeles | CA    |

**✅ Expected Output:**

| firstName | lastName | city        | state |
| --------- | -------- | ----------- | ----- |
| John      | Doe      | New York    | NY    |
| Jane      | Smith    | Los Angeles | CA    |

**🛠️ Solution**

```sql
SELECT
  p.firstName,
  p.lastName,
  a.city,
  a.state
FROM Person p
JOIN Address a
  ON p.personId = a.personId;
```

**🧾 Final Output Table**

| firstName | lastName | city        | state |
| --------- | -------- | ----------- | ----- |
| John      | Doe      | New York    | NY    |
| Jane      | Smith    | Los Angeles | CA    |

---

### 🔗 2. Employees earning > managers: [https://Inkd.in/dKqcFmzY](https://Inkd.in/dKqcFmzY)

**📌 Question**
Find employees who earn more than their managers, showing their names, salaries, and managers’ names.

**📋 Example Tables**

*Employee:*

| id | name  | salary | managerId |
| -- | ----- | ------ | --------- |
| 1  | Joe   | 70000  | 3         |
| 2  | Henry | 80000  | 4         |
| 3  | Sam   | 60000  | NULL      |
| 4  | Max   | 90000  | NULL      |

**✅ Expected Output:**

| Employee | Salary | Manager |
| -------- | ------ | ------- |
| Henry    | 80000  | Max     |

**🛠️ Solution**

```sql
SELECT e.name AS Employee,
       e.salary AS Salary,
       m.name AS Manager
FROM Employee e
JOIN Employee m
  ON e.managerId = m.id
WHERE e.salary > m.salary;
```

**🧾 Final Output Table**

| Employee | Salary | Manager |
| -------- | ------ | ------- |
| Henry    | 80000  | Max     |

---

### 🔗 3. Duplicate emails: [https://lnkd.in/dJSYfs89](https://lnkd.in/dJSYfs89)

**📌 Question**
Find all duplicate email addresses in a `Person` table.

**📋 Example Table**

*Person:*

| id | email                     |
| -- | ------------------------- |
| 1  | [a@x.com](mailto:a@x.com) |
| 2  | [b@y.com](mailto:b@y.com) |
| 3  | [a@x.com](mailto:a@x.com) |
| 4  | [c@z.com](mailto:c@z.com) |
| 5  | [b@y.com](mailto:b@y.com) |

**✅ Expected Output:**

| email                     |
| ------------------------- |
| [a@x.com](mailto:a@x.com) |
| [b@y.com](mailto:b@y.com) |

**🛠️ Solution**

```sql
SELECT email
FROM Person
GROUP BY email
HAVING COUNT(*) > 1;
```

**🧾 Final Output Table**

| email                     |
| ------------------------- |
| [a@x.com](mailto:a@x.com) |
| [b@y.com](mailto:b@y.com) |

---

### 🔗 4. Customers who never order: [https://lnkd.in/dPZPAKFf](https://lnkd.in/dPZPAKFf)

**📌 Question**
Find all customers who have never placed an order.

**📋 Example Tables**

*Customer:*

| customerId | name  |
| ---------- | ----- |
| 1          | Alice |
| 2          | Bob   |
| 3          | Carol |

*Order:*

| orderId | customerId |
| ------- | ---------- |
| 100     | 1          |
| 101     | 3          |

**✅ Expected Output:**

| customerId | name |
| ---------- | ---- |
| 2          | Bob  |

**🛠️ Solution**

```sql
SELECT c.customerId, c.name
FROM Customer c
LEFT JOIN Orders o
  ON c.customerId = o.customerId
WHERE o.orderId IS NULL;
```

**🧾 Final Output Table**

| customerId | name |
| ---------- | ---- |
| 2          | Bob  |

---

### 🔗 5. Delete duplicate emails: [https://lnkd.in/dEKnD9s3](https://lnkd.in/dEKnD9s3)

**📌 Question**
Delete all rows with duplicate emails in the `Person` table, keeping only the row with the smallest `id`.

**📋 Example Table**

*Person:*

| id | email                     |
| -- | ------------------------- |
| 1  | [a@x.com](mailto:a@x.com) |
| 2  | [b@y.com](mailto:b@y.com) |
| 3  | [a@x.com](mailto:a@x.com) |
| 4  | [c@z.com](mailto:c@z.com) |
| 5  | [b@y.com](mailto:b@y.com) |

**✅ Resulting Table After Deletion:**

| id | email                     |
| -- | ------------------------- |
| 1  | [a@x.com](mailto:a@x.com) |
| 2  | [b@y.com](mailto:b@y.com) |
| 4  | [c@z.com](mailto:c@z.com) |

**🛠️ Solution**

```sql
DELETE p1
FROM Person p1
JOIN Person p2
  ON p1.email = p2.email
  AND p1.id > p2.id;
```

**🧾 Final Output Table**

| id | email                     |
| -- | ------------------------- |
| 1  | [a@x.com](mailto:a@x.com) |
| 2  | [b@y.com](mailto:b@y.com) |
| 4  | [c@z.com](mailto:c@z.com) |

Here are Questions 6–8 in the format you requested:

---

## 🔹 Questions 6–8

### 🔗 6. Rising temperature: [https://lnkd.in/dyZvVYvP](https://lnkd.in/dyZvVYvP)

**📌 Question**
Write an SQL query to find all dates’ `id` where the temperature was higher than the previous day's.

**📋 Example Table**

*Weather:*

| id | recordDate | temperature |
| -- | ---------- | ----------- |
| 1  | 2015-01-01 | 10          |
| 2  | 2015-01-02 | 25          |
| 3  | 2015-01-03 | 20          |
| 4  | 2015-01-04 | 30          |

**✅ Expected Output:**

| id |
| -- |
| 2  |
| 4  |

**🛠️ Solution**
Using a self-join + date comparison:

```sql
SELECT w1.id
FROM Weather w1
JOIN Weather w2
  ON DATEDIFF(w1.recordDate, w2.recordDate) = 1
WHERE w1.temperature > w2.temperature;
```

Alternatively, using `DATE_SUB`:

```sql
SELECT Today.id
FROM Weather AS Today
JOIN Weather AS Yesterday
  ON DATE_SUB(Today.recordDate, INTERVAL 1 DAY) = Yesterday.recordDate
WHERE Today.temperature > Yesterday.temperature;
```

**🧾 Final Output Table**

| id |
| -- |
| 2  |
| 4  |

---

### 🔗 7. Employee bonus: [https://Inkd.in/d9iTXt-V](https://Inkd.in/d9iTXt-V)

**📌 Question**
List all employees and their bonuses, but only include those with a bonus less than 1000 or who have no bonus at all.

**📋 Example Tables**

*Employee:*

| empId | name   | supervisor | salary |
| ----- | ------ | ---------- | ------ |
| 1     | John   | 3          | 1000   |
| 2     | Dan    | 3          | 2000   |
| 3     | Brad   | null       | 4000   |
| 4     | Thomas | 3          | 4000   |

*Bonus:*

| empId | bonus |
| ----- | ----- |
| 2     | 500   |
| 4     | 2000  |

**✅ Expected Output:**

| name | bonus |
| ---- | ----- |
| John | null  |
| Brad | null  |
| Dan  | 500   |

**🛠️ Solution**
Use `LEFT JOIN` with a filter:

```sql
SELECT e.name, b.bonus
FROM Employee e
LEFT JOIN Bonus b ON e.empId = b.empId
WHERE b.bonus < 1000 OR b.bonus IS NULL;
```

**🧾 Final Output Table**

| name | bonus |
| ---- | ----- |
| John | NULL  |
| Brad | NULL  |
| Dan  | 500   |

---

### 🔗 8. Find customer referee: [https://lnkd.in/d5cXjXdb](https://lnkd.in/d5cXjXdb)

**📌 Question**
Find pairs of customers and their referees (if any). Each customer may have been referred by another customer.

**📋 Example Table**

*Customer:*

| id | name  | refereeId |
| -- | ----- | --------- |
| 1  | Alice | NULL      |
| 2  | Bob   | 1         |
| 3  | Carol | 1         |
| 4  | Dave  | 2         |
| 5  | Eve   | NULL      |

**✅ Expected Output:**

| customer | referee |
| -------- | ------- |
| Bob      | Alice   |
| Carol    | Alice   |
| Dave     | Bob     |

**🛠️ Solution**
Self-join on refereeId:

```sql
SELECT c.name AS customer,
       r.name AS referee
FROM Customer c
JOIN Customer r
  ON c.refereeId = r.id;
```

**🧾 Final Output Table**

| customer | referee |
| -------- | ------- |
| Bob      | Alice   |
| Carol    | Alice   |
| Dave     | Bob     |

### 🔗 10. Big countries: [https://lnkd.in/dAnS54qK](https://lnkd.in/dAnS54qK)

**📌 Question**
Find countries with a `population` greater than 50 million, sorted by size.

**📋 Example Table**

*Country:*

| name | population  | area    |
| ---- | ----------- | ------- |
| A    | 80,000,000  | 500,000 |
| B    | 40,000,000  | 300,000 |
| C    | 120,000,000 | 700,000 |

**✅ Expected Output:**

| name | population  |
| ---- | ----------- |
| C    | 120,000,000 |
| A    | 80,000,000  |

**🛠️ Solution**

```sql
SELECT name, population
FROM Country
WHERE population > 50000000
ORDER BY population DESC;
```

**🧾 Final Output Table**

| name | population  |
| ---- | ----------- |
| C    | 120,000,000 |
| A    | 80,000,000  |

---

### 🔗 11. Classes more than 5 students: [https://lnkd.in/dfrUiutd](https://lnkd.in/dfrUiutd)

**📌 Question**
Find classes with more than 5 students enrolled.

**📋 Example Table**

*Enrollment:*

| classId | studentId |
| ------- | --------- |
| 1       | 10        |
| 1       | 11        |
| 1       | 12        |
| 2       | 13        |
| 1       | 14        |
| 1       | 15        |
| 1       | 16        |
| 2       | 17        |

**✅ Expected Output:**

| classId | numStudents |
| ------- | ----------- |
| 1       | 6           |

**🛠️ Solution**

```sql
SELECT classId,
       COUNT(DISTINCT studentId) AS numStudents
FROM Enrollment
GROUP BY classId
HAVING COUNT(DISTINCT studentId) > 5;
```

**🧾 Final Output Table**

| classId | numStudents |
| ------- | ----------- |
| 1       | 6           |

---

### 🔗 12. Sales person: [https://lnkd.in/dwRnf5Df](https://lnkd.in/dwRnf5Df)

**📌 Question**
Find the salesperson with the highest total sales.

**📋 Example Tables**

*Sales:*

| id | salesperson | amount |
| -- | ----------- | ------ |
| 1  | Alice       | 200    |
| 2  | Bob         | 350    |
| 3  | Alice       | 150    |
| 4  | Charlie     | 400    |

**✅ Expected Output:**

| salesperson | totalSales |
| ----------- | ---------- |
| Charlie     | 400        |

**🛠️ Solution**

```sql
SELECT salesperson,
       SUM(amount) AS totalSales
FROM Sales
GROUP BY salesperson
ORDER BY totalSales DESC
LIMIT 1;
```

**🧾 Final Output Table**

| salesperson | totalSales |
| ----------- | ---------- |
| Charlie     | 400        |

---

### 🔗 13. Triangle judgement: [https://lnkd.in/dwzD-hFn](https://lnkd.in/dwzD-hFn)

**📌 Question**
Given three side lengths `a`, `b`, `c`, determine if they can form a valid triangle.

**📋 Example Table**

*TriangleSides:*

| a | b | c |
| - | - | - |
| 3 | 4 | 5 |
| 1 | 2 | 3 |
| 5 | 5 | 9 |

**✅ Expected Output:**

| a | b | c | isTriangle |
| - | - | - | ---------- |
| 3 | 4 | 5 | yes        |
| 1 | 2 | 3 | no         |
| 5 | 5 | 9 | no         |

**🛠️ Solution**

```sql
SELECT a, b, c,
       CASE
         WHEN a + b > c
           AND a + c > b
           AND b + c > a THEN 'yes'
         ELSE 'no'
       END AS isTriangle
FROM TriangleSides;
```

**🧾 Final Output Table**

| a | b | c | isTriangle |
| - | - | - | ---------- |
| 3 | 4 | 5 | yes        |
| 1 | 2 | 3 | no         |
| 5 | 5 | 9 | no         |

### 🔗 14. Biggest single number: [https://lnkd.in/d4F5zHDs](https://lnkd.in/d4F5zHDs)

**📌 Question**
Find the largest number in each row of a table with three columns (a, b, c).

**📋 Example Table**

*Numbers:*

| a | b | c |
| - | - | - |
| 1 | 3 | 2 |
| 5 | 4 | 7 |
| 9 | 9 | 8 |

**✅ Expected Output:**

| greatest |
| -------- |
| 3        |
| 7        |
| 9        |

**🛠️ Solution**

```sql
SELECT GREATEST(a, b, c) AS greatest
FROM Numbers;
```

**🧾 Final Output Table**

| greatest |
| -------- |
| 3        |
| 7        |
| 9        |

---

### 🔗 15. Not boring movies: [https://lnkd.in/d5w8\_z65](https://lnkd.in/d5w8_z65)

**📌 Question**
Find all movies that aren’t boring. A movie is boring if its rating is less than 50 or description contains 'boring'.

**📋 Example Table**

*Movie:*

| id | title          | rating | description       |
| -- | -------------- | ------ | ----------------- |
| 1  | Action Hero    | 80     | Lots of action    |
| 2  | Boring Drama   | 60     | A bit boring plot |
| 3  | Low Rated Fun  | 40     | Fun surprise      |
| 4  | Comedy Classic | 70     | Great laughs      |

**✅ Expected Output:**

| id | title          |
| -- | -------------- |
| 1  | Action Hero    |
| 4  | Comedy Classic |

**🛠️ Solution**

```sql
SELECT id, title
FROM Movie
WHERE rating >= 50
  AND description NOT LIKE '%boring%';
```

**🧾 Final Output Table**

| id | title          |
| -- | -------------- |
| 1  | Action Hero    |
| 4  | Comedy Classic |

---

### 🔗 16. Swap salary: [https://lnkd.in/dnbCtecM](https://lnkd.in/dnbCtecM)

**📌 Question**
Swap the salaries of two employees given their IDs in a table.

**📋 Example Table**

*Employee:*

| id | name  | salary |
| -- | ----- | ------ |
| 1  | Alice | 50000  |
| 2  | Bob   | 60000  |
| 3  | Carol | 70000  |

**✅ Resulting Table After Swap (IDs 1 & 2):**

| id | name  | salary |
| -- | ----- | ------ |
| 1  | Alice | 60000  |
| 2  | Bob   | 50000  |
| 3  | Carol | 70000  |

**🛠️ Solution**

```sql
UPDATE Employee e1
JOIN Employee e2
  ON (e1.id = 1 AND e2.id = 2)
  OR (e1.id = 2 AND e2.id = 1)
SET
  e1.salary = CASE e1.id WHEN 1 THEN e2.salary WHEN 2 THEN e2.salary END;
```

Alternatively, using variables:

```sql
SET @s1 = (SELECT salary FROM Employee WHERE id = 1);
SET @s2 = (SELECT salary FROM Employee WHERE id = 2);

UPDATE Employee
SET salary = CASE id
  WHEN 1 THEN @s2
  WHEN 2 THEN @s1
END
WHERE id IN (1,2);
```

**🧾 Final Output Table**

| id | name  | salary |
| -- | ----- | ------ |
| 1  | Alice | 60000  |
| 2  | Bob   | 50000  |
| 3  | Carol | 70000  |

---

### 🔗 18. Product sales analysis 1: [https://lnkd.in/dRj3EBuK](https://lnkd.in/dRj3EBuK)

**📌 Question**
For each product, compute the average, minimum, and maximum sale amount.

**📋 Example Tables**

*Sales:*

| saleId | productId | amount |
| ------ | --------- | ------ |
| 1      | 101       | 100    |
| 2      | 101       | 150    |
| 3      | 102       | 200    |
| 4      | 101       | 130    |
| 5      | 102       | 220    |

**✅ Expected Output:**

| productId | avgAmount | minAmount | maxAmount |
| --------- | --------- | --------- | --------- |
| 101       | 126.67    | 100       | 150       |
| 102       | 210.00    | 200       | 220       |

**🛠️ Solution**

```sql
SELECT productId,
       ROUND(AVG(amount),2) AS avgAmount,
       MIN(amount) AS minAmount,
       MAX(amount) AS maxAmount
FROM Sales
GROUP BY productId;
```

**🧾 Final Output Table**

| productId | avgAmount | minAmount | maxAmount |
| --------- | --------- | --------- | --------- |
| 101       | 126.67    | 100       | 150       |
| 102       | 210.00    | 200       | 220       |

---

### 🔗 19. Project employees 1: [https://lnkd.in/dvGHsbKQ](https://lnkd.in/dvGHsbKQ)

**📌 Question**
List projects and the count of employees in each project.

**📋 Example Tables**

*ProjectEmployee:*

| projectId | empId |
| --------- | ----- |
| 1         | 100   |
| 1         | 101   |
| 2         | 100   |
| 2         | 102   |
| 2         | 103   |

**✅ Expected Output:**

| projectId | numEmployees |
| --------- | ------------ |
| 1         | 2            |
| 2         | 3            |

**🛠️ Solution**

```sql
SELECT projectId,
       COUNT(empId) AS numEmployees
FROM ProjectEmployee
GROUP BY projectId;
```

**🧾 Final Output Table**

| projectId | numEmployees |
| --------- | ------------ |
| 1         | 2            |
| 2         | 3            |

---

### 🔗 20. Sales analysis III: [https://lnkd.in/d\_y\_rxPh](https://lnkd.in/d_y_rxPh)

**📌 Question**
For each salesperson and month, report total sales and the count of orders.

**📋 Example Table**

*Sales:*

| id | salesperson | amount | saleDate   |
| -- | ----------- | ------ | ---------- |
| 1  | Alice       | 100    | 2025‑05‑10 |
| 2  | Alice       | 150    | 2025‑05‑20 |
| 3  | Bob         | 200    | 2025‑05‑15 |
| 4  | Bob         | 120    | 2025‑06‑01 |

**✅ Expected Output:**

| salesperson | month   | totalAmount | orderCount |
| ----------- | ------- | ----------- | ---------- |
| Alice       | 2025‑05 | 250         | 2          |
| Bob         | 2025‑05 | 200         | 1          |
| Bob         | 2025‑06 | 120         | 1          |

**🛠️ Solution**

```sql
SELECT salesperson,
       DATE_FORMAT(saleDate, '%Y-%m') AS month,
       SUM(amount) AS totalAmount,
       COUNT(*) AS orderCount
FROM Sales
GROUP BY salesperson, DATE_FORMAT(saleDate, '%Y-%m')
ORDER BY salesperson, month;
```

**🧾 Final Output Table**

| salesperson | month   | totalAmount | orderCount |
| ----------- | ------- | ----------- | ---------- |
| Alice       | 2025-05 | 250         | 2          |
| Bob         | 2025-05 | 200         | 1          |
| Bob         | 2025-06 | 120         | 1          |


