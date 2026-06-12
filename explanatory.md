# Beginner Explanatory Guide: DATA-201: Fix Broken Sales Reporting Queries

> **Task Type**: Product Task  
> **Domain/Focus**: Database queries, SQL, Reporting

---

## 1. The Goal (In-Depth Beginner Explanation)

### The Core Problem
The task at hand involves fixing broken SQL queries that are responsible for generating the quarterly sales dashboard. Currently, the dashboard displays incorrect sales figures due to several issues in the SQL queries used to retrieve data from the database. Specifically, there are problems with JOIN operations, missing WHERE clauses, and incorrect GROUP BY statements. 

These issues lead to inaccurate reporting, which can misinform the Finance Team and other stakeholders about the company's sales performance. For instance, if the total revenue query does not correctly sum the sales per region, it could lead to misguided business decisions, such as misallocating resources or misjudging market performance. Therefore, fixing these queries is crucial for ensuring that the sales data is reliable and actionable.

### Jargon Buster (Key Terms Explained)
* **SQL (Structured Query Language)**: SQL is a programming language used to communicate with databases. It allows users to create, read, update, and delete data. For example, a simple SQL query to retrieve all customers from a database might look like this: `SELECT * FROM customers;`.

* **JOIN**: A JOIN operation in SQL combines rows from two or more tables based on a related column between them. For instance, if you want to get a list of customers along with their orders, you would use a JOIN to connect the `customers` table with the `orders` table on the `customer_id`.

* **WHERE Clause**: This clause is used to filter records that meet a certain condition. For example, if you want to find all completed orders, you would write: `SELECT * FROM orders WHERE status = 'completed';`.

* **GROUP BY**: This SQL clause groups rows that have the same values in specified columns into summary rows. For example, if you want to calculate total sales per region, you would use: `SELECT region, SUM(amount) FROM orders GROUP BY region;`.

### Expected Outcome
After implementing the necessary fixes, the system should accurately reflect sales data in the quarterly sales dashboard. 

**Before vs. After Comparison**:
- **Before**: The total revenue query might return inflated or incorrect sums due to improper JOINs and missing filters, leading to misleading insights.
- **After**: The total revenue query will correctly sum the sales per region, only including completed orders, and the customer report will accurately reflect customers who have placed orders, not all customers. Additionally, the monthly breakdown will group sales data correctly by month.

---

## 2. Related Coding Concepts & Syntax (50% Theory, 50% Practice)

### Concept 1: SQL Queries
#### 📘 Theoretical Overview (50%)
SQL queries are the backbone of data retrieval and manipulation in relational databases. They allow users to interact with the database to perform various operations such as selecting data, inserting new records, updating existing records, and deleting records. Understanding how to write effective SQL queries is essential for any software engineer working with databases.

If SQL queries are not constructed correctly, they can lead to incorrect data being retrieved or manipulated. For example, failing to include a WHERE clause might result in retrieving all records instead of a specific subset, which can skew analysis and reporting.

#### 💻 Syntax & Practical Examples (50%)
* **Language Syntax**:
  ```sql
  SELECT column1, column2
  FROM table_name
  WHERE condition
  GROUP BY column1;
  ```
  - `SELECT`: Specifies the columns to retrieve.
  - `FROM`: Indicates the table from which to retrieve the data.
  - `WHERE`: Filters the results based on a condition.
  - `GROUP BY`: Groups the results by specified columns.

* **Real-World Application**:
  ```sql
  SELECT region, SUM(amount) AS total_revenue
  FROM orders
  WHERE status = 'completed'
  GROUP BY region;
  ```
  In this example, we are retrieving the total revenue from completed orders, grouped by region. This query will help the Finance Team understand how much revenue each region is generating.

---

## 3. Step-by-Step Logic & Walkthrough

1. **Step 1: Locate and Analyze the Target File**
   * Navigate to the `salesReporter.py` file within the `p-w10-task-03` folder. This file contains the SQL queries that need to be fixed.
   * Focus on the `revenue_by_region` method, which is responsible for calculating total revenue by region.

2. **Step 2: Input Verification & Validation**
   * Ensure that the database is seeded with data by calling the `seed_data` method before running the queries. This will provide the necessary context for testing the queries.

3. **Step 3: Core Implementation / Modification**
   * Modify the SQL query in the `revenue_by_region` method to use an INNER JOIN instead of a LEFT JOIN and add a WHERE clause to filter out cancelled orders:
   ```python
   cursor.execute('''
       SELECT c.region, SUM(o.amount) as total_revenue
       FROM customers c
       INNER JOIN orders o ON c.id = o.customer_id
       WHERE o.status = 'completed'
       GROUP BY c.region
   ''')
   ```

4. **Step 4: Output Verification & Testing**
   * Run the test suite using pytest to ensure that all tests pass. This will confirm that the changes made to the SQL queries are functioning as expected and that the reporting module is now providing accurate data.

---

## 4. Detailed Walkthrough of Test Cases

### Test Case 1: Standard / Success Case
* **Description**: This test checks that the revenue calculation for the North region is correct.
* **Inputs**:
  ```json
  {
    "region": "North",
    "expected_revenue": 650.00
  }
  ```
* **Step-by-Step Execution Trace**:
  1. The `revenue_by_region` method is called.
  2. The SQL query executes, retrieving the total revenue for the North region.
  3. The result is compared against the expected revenue of 650.00.
* **Expected Output**: The test should pass, confirming that the total revenue for the North region is correctly calculated.

### Test Case 2: Edge Case / Validation Fail
* **Description**: This test checks that cancelled orders are excluded from the revenue calculation.
* **Inputs**:
  ```json
  {
    "region": "South",
    "expected_revenue": 75.00
  }
  ```
* **Step-by-Step Execution Trace**:
  1. The `revenue_by_region` method is called.
  2. The SQL query executes, retrieving the total revenue for the South region, excluding cancelled orders.
  3. The result is compared against the expected revenue of 75.00.
* **Expected Output**: The test should pass, confirming that the revenue calculation correctly excludes cancelled orders.