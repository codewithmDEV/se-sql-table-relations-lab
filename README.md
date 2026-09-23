# SQL Table Relations Lab — Northwinds CRM

A Python + SQLite lab exploring advanced SQL techniques: JOINs, subqueries, GROUP BY, HAVING, DISTINCT, and multi-table relations.

## 📌 Overview

This lab uses the Northwinds CRM database (`data.sqlite`) to practice advanced SQL queries across multiple related tables. Queries are loaded into pandas DataFrames and verified with `pytest`.

## 🛠️ Tech Stack

- **Python 3**
- **SQLite** — database engine
- **pandas** — data loading and manipulation
- **pytest** — automated testing

## 📁 Project Structure

```text
se-sql-table-relations-lab/
│
├── main.py           # All SQL queries + DataFrame assignments
├── test_main.py      # Autotests for each step
├── data.sqlite       # Northwinds CRM database
├── Pipfile           # Original dependencies (pandas, pytest)
├── Pipfile.lock
├── assets/           # ERD and images
└── README.md
```

## 🚀 How to Run

### 1. Create a virtual environment

```bash
python3 -m venv .venv
```

### 2. Activate the virtual environment

Linux/macOS:

```bash
source .venv/bin/activate
```

Windows:

```bash
.venv\Scripts\activate
```

### 3. Install dependencies

```bash
pip install pandas pytest
```

### 4. Start the script

```bash
python3 main.py
```

### 5. Run the tests

```bash
pytest
```

## 🧠 What This Lab Covers

| Step | Skill | Query Feature |
| :--- | :--- | :--- |
| 1 | Join and filter | `INNER JOIN` + `WHERE` |
| 2 | Find non-matches | `LEFT JOIN` + `IS NULL` |
| 3 | All rows from one table | `LEFT JOIN` + `ORDER BY` |
| 4 | Customers without orders | `LEFT JOIN` + `IS NULL` |
| 5 | Sort by casted value | `CAST(... AS REAL)` |
| 6 | Filter after grouping | `GROUP BY` + `HAVING` |
| 7 | Aggregate per product | `COUNT()` + `SUM()` |
| 8 | Multi-table joins | 4-table `JOIN` + `DISTINCT` |
| 9 | Customers per office | 3-table chain + `DISTINCT` |
| 10 | Subquery | `WHERE ... IN (SELECT ...)` |

## 📊 Key Queries

### Inner join with filter
```sql
SELECT firstName, lastName
FROM employees
JOIN offices ON employees.officeCode = offices.officeCode
WHERE offices.city = 'Boston';
```

### Left join to find non-matches
```sql
SELECT offices.officeCode, offices.city
FROM offices
LEFT JOIN employees ON offices.officeCode = employees.officeCode
WHERE employees.employeeNumber IS NULL;
```

### Group and filter with HAVING
```sql
SELECT employees.employeeNumber, employees.firstName, employees.lastName,
       COUNT(customers.customerNumber) AS num_customers
FROM employees
JOIN customers ON employees.employeeNumber = customers.salesRepEmployeeNumber
GROUP BY employees.employeeNumber
HAVING AVG(customers.creditLimit) > 90000
ORDER BY num_customers DESC;
```

### Multi-table join with DISTINCT
```sql
SELECT products.productName, products.productCode,
       COUNT(DISTINCT customers.customerNumber) AS numpurchasers
FROM products
JOIN orderdetails ON products.productCode = orderdetails.productCode
JOIN orders ON orderdetails.orderNumber = orders.orderNumber
JOIN customers ON orders.customerNumber = customers.customerNumber
GROUP BY products.productCode, products.productName
ORDER BY numpurchasers DESC;
```

### Subquery
```sql
SELECT DISTINCT employees.employeeNumber, employees.firstName, employees.lastName,
       offices.city, offices.officeCode
FROM employees
JOIN offices ON employees.officeCode = offices.officeCode
JOIN customers ON employees.employeeNumber = customers.salesRepEmployeeNumber
JOIN orders ON customers.customerNumber = orders.customerNumber
JOIN orderdetails ON orders.orderNumber = orderdetails.orderNumber
WHERE orderdetails.productCode IN (
    SELECT productCode
    FROM orderdetails
    JOIN orders ON orderdetails.orderNumber = orders.orderNumber
    JOIN customers ON orders.customerNumber = customers.customerNumber
    GROUP BY productCode
    HAVING COUNT(DISTINCT customers.customerNumber) < 20
)
ORDER BY employees.lastName;
```

## ✅ Test Results

```text
6 passed
```

All 6 autotests pass across:
- Join and filter
- Type of join
- Built-in function
- Joining and grouping
- Multiple joins
- Subquery

## 🧠 Lessons Learned

- **INNER JOIN** returns only matching rows
- **LEFT JOIN** keeps all rows from the left table — use `IS NULL` to find non-matches
- **WHERE** filters rows before grouping; **HAVING** filters groups after aggregation
- **DISTINCT** inside `COUNT()` prevents double-counting when joins fan out
- **CAST** matters — SQLite stores some numbers as text, breaking sort order
- **Subqueries** let you filter based on aggregated results from another query
- **Multi-table joins** require chaining `JOIN ... ON` for each shared column
- **Every opening parenthesis needs a closing one** — or SQLite says "incomplete input"

## 👤 Author

**Mohamed Ahmed** — Full-Stack Engineer  
[Portfolio](https://codewithmdev.netlify.app) · [GitHub](https://github.com/codewithmDEV)