# Databases & SQL Overview with examples <!-- omit in toc -->

## Contents <!-- omit in toc -->

- [1. Introduction](#1-introduction)
- [2. Database Management System (DBMS)](#2-database-management-system-dbms)
- [3. Types of Databases](#3-types-of-databases)
- [4. Database Structure](#4-database-structure)
  - [4.1. Components of a Table](#41-components-of-a-table)
  - [4.2. Data Types](#42-data-types)
- [5. Types of SQL Commands](#5-types-of-sql-commands)
  - [5.1. DDL (Data Definition Language)](#51-ddl-data-definition-language)
  - [5.2. DQL (Data Query Language)](#52-dql-data-query-language)
  - [5.3. DML (Data Manipulation Language)](#53-dml-data-manipulation-language)
  - [5.4. DCL (Data Control Language)](#54-dcl-data-control-language)
  - [5.5. TCL (Transaction Control Language)](#55-tcl-transaction-control-language)
- [6. DDL - Data Definition Language](#6-ddl---data-definition-language)
  - [6.1. Core DDL Operations](#61-core-ddl-operations)
  - [6.2. Creating Objects (CREATE)](#62-creating-objects-create)
  - [6.3. Modifying Objects (ALTER)](#63-modifying-objects-alter)
  - [6.4. Deleting Objects (DROP)](#64-deleting-objects-drop)
  - [6.5. DDL Summary Table](#65-ddl-summary-table)
- [7. DML - Data Manipulation Language](#7-dml---data-manipulation-language)
  - [7.1. Core DML Operations](#71-core-dml-operations)
  - [7.2. Adding Data (INSERT)](#72-adding-data-insert)
  - [7.3. Modifying Data (UPDATE)](#73-modifying-data-update)
  - [7.4. Removing Data (DELETE)](#74-removing-data-delete)
  - [7.5. Comparison of Actions](#75-comparison-of-actions)
- [8. DQL - Data Query Language](#8-dql---data-query-language)
  - [8.1. Anatomy of a SQL Statement](#81-anatomy-of-a-sql-statement)
  - [8.2. Basic Query Structure](#82-basic-query-structure)
  - [8.3. Filtering and Sorting](#83-filtering-and-sorting)
  - [8.4. Advanced Selection Tools](#84-advanced-selection-tools)
  - [8.5. Aliases (AS)](#85-aliases-as)
  - [8.6. Logical Evaluation Order](#86-logical-evaluation-order)
  - [8.7. Filtering Data: WHERE Conditions](#87-filtering-data-where-conditions)
  - [8.8. Our Filtering Toolkit](#88-our-filtering-toolkit)
    - [8.8.1. Comparison Operators](#881-comparison-operators)
    - [8.8.2. Logical Operators](#882-logical-operators)
  - [8.9. Range and Membership](#89-range-and-membership)
  - [8.10. Search Operator: LIKE](#810-search-operator-like)
  - [8.11. Handling Missing Data (NULL)](#811-handling-missing-data-null)
  - [8.12. Summary of Operator Usage](#812-summary-of-operator-usage)
  - [8.13. SQL JOINS \& SET Operators: Combining Data](#813-sql-joins--set-operators-combining-data)
    - [8.13.1. Two Methods of Combining Data](#8131-two-methods-of-combining-data)
    - [8.13.2. How We Use Joins](#8132-how-we-use-joins)
    - [8.13.3. How We Use SET Operators](#8133-how-we-use-set-operators)
    - [8.13.4. Summary Table](#8134-summary-table)
- [9. Advanced SQL Techniques](#9-advanced-sql-techniques)
  - [9.1. Subqueries](#91-subqueries)
  - [9.2. CTE (Common Table Expression)](#92-cte-common-table-expression)
  - [9.3. Views](#93-views)
  - [9.4. Temporary Tables \& CTAS](#94-temporary-tables--ctas)
  - [9.5. Stored Procedures](#95-stored-procedures)
  - [9.6. Triggers](#96-triggers)

# 1. Introduction

- **What is SQL?**
  - SQL stands for Structured Query Language.
  - It is often pronounced as **SeQuel**.
  - It is used to interact with data stored in databases, allowing users to perform complex calculations like finding "Total Spending?" across massive datasets.
- **Why Learn SQL?**
  - Talk to Data: It allows we to communicate directly with data systems.
  - High Demand: There is a high demand for SQL skills in various technical roles.
  - Career Paths: Essential for Software Developers, Data Analysts, Data Scientists, and Data Engineers.
  - **Industry Standard:** It is an industry-standard language used with tools like Power BI, Tableau , Kafka, Spark and Synapse.

# 2. Database Management System (DBMS)

- A DBMS (Database Management System) acts as the interface between the user and the database.
  - Server Hosting: Databases are typically hosted on a Server or in the Cloud.
  - 24/7 Availability: These systems generally run 24/7 to ensure data is always accessible.
  - Interaction: Applications (APP </>) , BI tools (Power BI) , and users send SQL queries to the DBMS to manage and retrieve data.

# 3. Types of Databases

| Category     | Examples                                                 |
| ------------ | -------------------------------------------------------- |
| Relational   | Microsoft SQL Server, MySQL, PostgreSQL, Amazon Redshift |
| Document     | MongoDB                                                  |
| Graph        | Neo4j                                                    |
| Key-Value    | Redis, Amazon DynamoDB                                   |
| Column-Based | Apache Cassandra                                         |

# 4. Database Structure

- Relational databases follow a hierarchical organization:

1. Server: The primary host containing one or more databases.
2. Database: High-level containers for data, such as "Sales" or "HR".
3. Schema: Logical groupings within a database, such as "Orders" or "Customers".
4. Table: Where data is physically stored in rows and columns, such as "Items" or "Purchase".

## 4.1. Components of a Table

- Columns: Vertical categories defining data types, such as ID, Name, or Score.
- Rows: Individual horizontal records representing one entry.
- Cell: A single unit of data at the intersection of a row and column.
- Primary Key: A unique identifier for each record in a table.
- Storage: Tables are stored as Database Files on a physical Disk.

## 4.2. Data Types

- SQL uses specific data types to define what kind of information can be stored in a column:
- **Numeric**
  - `INT` - Whole numbers (e.g., 1, 2, 30).
  - `DECIMAL` - Numeric values with fractions, such as 3.14 or 100.50.
- **String/Text**
  - `CHAR` - Fixed-length character strings.
  - `VARCHAR` - Variable-length character strings (e.g., 'Maria').
- **Date & Time**
  - `DATE` - Formatted as 'YYYY-MM-DD' (e.g., '2025-10-30').
  - `TIME` - Formatted as 'HH:MM:SS' (e.g., '09:30:00').

# 5. Types of SQL Commands

- SQL commands are divided into functional languages.

## 5.1. DDL (Data Definition Language)

- Used to define or modify the database structure.
  - `CREATE` - To create new databases or tables.
  - `ALTER` - To modify an existing structure.
  - `DROP` - To delete a database or table.

## 5.2. DQL (Data Query Language)

- Used to retrieve data from the database.
  - `SELECT` - The primary command used to fetch data.

## 5.3. DML (Data Manipulation Language)

- Used to manage the data within the tables.
  - `INSERT` - To add new records.
  - `UPDATE` - To modify existing records.
  - `DELETE` - To remove records.

## 5.4. DCL (Data Control Language)

- Used to manage permissions and access control.
  - `GRANT` - Gives a user access privileges.
  - `REVOKE` - Withdraws access privileges.

## 5.5. TCL (Transaction Control Language)

- Used to manage database transactions.
  - `COMMIT` - Saves changes permanently.
  - `ROLLBACK` - Undoes changes if an error occurs.
  - `SAVEPOINT` - Sets a point within a transaction to roll back to.

# 6. DDL - Data Definition Language

- DDL stands for Data Definition Language.
- While DQL is used to query data and DML is used to manipulate data, DDL is used to define the structure of our data objects (the "skeleton" of the database)

## 6.1. Core DDL Operations

- DDL allows we to manage the **blueprint** of our database through three main actions:
  - `CREATE` - Used to build new objects (Databases or Tables) from scratch.
  - `ALTER` - Used to modify the structure of an existing object (e.g., adding a new column to a table).
  - `DROP` - Used to permanently delete an object and all its data.

## 6.2. Creating Objects (CREATE)

- `CREATE DATABASE Sales;` - To start a new project, we first create a container for our tables.
- `CREATE TABLE` - When creating a table, we must define the Columns and their associated Data Types.
  ```sql
    CREATE TABLE Products (
    ProductID INT,
    ProductName VARCHAR(100),
    Price DECIMAL
    );
  ```

## 6.3. Modifying Objects (ALTER)

- If our requirements change, we use the `ALTER` command to update the structure without deleting the table.
- **Add a Column**
  ```sql
  ALTER TABLE Products
  ADD Category VARCHAR(50);
  ```
- **Drop a Column**
  ```sql
    SQLALTER TABLE Products
    DROP COLUMN Price;
  ```

## 6.4. Deleting Objects (DROP)

- The `DROP` command is destructive.
- It removes the entire structure and all the records stored within it.
  ```sql
    DROP TABLE Products;
  ```
- **Note:** Once a table is dropped, it cannot be undone with a _simple command_; the data is gone.

## 6.5. DDL Summary Table

| Command | Action          | Object Affected         |
| ------- | --------------- | ----------------------- |
| CREATE  | Build new       | Database, Table, Schema |
| ALTER   | Modify existing | Columns, Data Types     |
| DROP    | Delete          | Database, Table, Schema |

# 7. DML - Data Manipulation Language

- We use DML (Data Manipulation Language) to change the actual data stored within our tables.
- While DDL defines the container, DML allows us to manage the content inside.

## 7.1. Core DML Operations

- We rely on three primary commands to manipulate our data:
  - `INSERT` - We use this to add new rows of data into a table.
  - `UPDATE` - We use this to modify existing information within a table.
  - `DELETE` - We use this to remove specific records from a table.

## 7.2. Adding Data (INSERT)

- When we want to populate our tables, we have two main methods for inserting data:
- **Method 1:** Manual Entry (VALUES).
  - We manually specify the values we want to add to specific columns.
    ```sql
      INSERT INTO Products (ProductID, ProductName, Price)`
      VALUES (1, 'Laptop', 1200.00);
    ```
- **Method 2:** `INSERT` Using `SELECT`.
  - We can also insert data into a target table by querying it from a source table.
    ```sql
      SQLINSERT INTO TargetTable
      SELECT * FROM SourceTable
      WHERE condition;
    ```

## 7.3. Modifying Data (UPDATE)

- When we need to edit existing records, we use the `UPDATE` statement.
- We must be very careful to include a `WHERE` clause; otherwise, we will update every single row in the table.
  ```sql
    UPDATE table_name
    SET column1 = value1, column2 = value2
    WHERE <condition>;
  ```
- **Rule:** Always use `WHERE` to avoid unintentional mass updates.

## 7.4. Removing Data (DELETE)

- When data is no longer needed, we use the `DELETE` command.
- Similar to updating, we use a `WHERE` clause to target specific rows.
  ```sql
  DELETE FROM table_name
  WHERE <condition>;
  ```

## 7.5. Comparison of Actions

| Command | Action we take | Effect on Data               |
| ------- | -------------- | ---------------------------- |
| INSERT  | Add            | New rows are created.        |
| UPDATE  | Edit           | Existing values are changed. |
| DELETE  | Remove         | Selected rows are erased.    |

# 8. DQL - Data Query Language

- A SQL statement is composed of several specific elements that tell the database how to process the request.

## 8.1. Anatomy of a SQL Statement

- **Key Components**
  - **Comments (--):** Used to document the code. Anything after -- is ignored by the engine (e.g., -- Retrieve Customers Data).
  - **Keywords:** Reserved words that have a special meaning in SQL (e.g., SELECT, FROM, WHERE).
  - **Clauses:** The building blocks of a statement (e.g., the WHERE clause).
  - **Functions:** Built-in tools to transform data (e.g., LOWER(country) converts text to lowercase).
  - **Identifiers:** Names of database objects like tables (customers) or columns (name).
  - **Operators:** Used for comparisons (e.g., =).
  - **Literals:** Constant values or strings (e.g., 'Italy').

## 8.2. Basic Query Structure

- To retrieve data, SQL uses a fundamental structure:
  ```sql
  SELECT column_name
  FROM table_name;
  ```
- `SELECT` - Defines which columns we want to see.
- `FROM` - Specifies the table where the data is located.
- Asterisk (`*`): Use `SELECT *` to retrieve all columns from a table.

## 8.3. Filtering and Sorting

- `WHERE` Clause
  - Used to filter records based on specific conditions.
    - **Example:** `WHERE country = 'Italy'` only returns rows where the country is Italy.
- `ORDER BY` Clause
  - Used to sort the result set in ascending or descending order.
    - `ASC` - Ascending (default, e.g., A-Z, 1-10).
    - `DESC` - Descending (e.g., Z-A, 10-1).

## 8.4. Advanced Selection Tools

- `DISTINCT`
  - Removes duplicate values from the result set, showing only unique entries.
    - **Usage:** `SELECT DISTINCT country FROM customers;`
- `TOP` / `LIMIT`
- Used to specify the number of records to return.
- This is useful for large tables when we only need a "preview" or the highest/lowest values.
  - **SQL Server:** `SELECT TOP 5 * FROM customers;`
  - **PostgreSQL/MySQL:** `SELECT * FROM customers LIMIT 5;`

## 8.5. Aliases (AS)

- Aliases are used to give a table or a column a temporary name, making the output more readable.
- **Example:** `SELECT name AS customer_name FROM customers;`

## 8.6. Logical Evaluation Order

- It is important to note that SQL does not process clauses in the order they are written.
- The standard execution order is:
  - `FROM` (Pick the table).
  - `WHERE` (Filter rows).
  - `SELECT` (Choose columns).
  - `ORDER BY` (Sort results).

## 8.7. Filtering Data: WHERE Conditions

- When we work with large databases, we rarely need to see every single row.
- We use the `WHERE` clause to filter our results, allowing us to pinpoint exactly the data we need.
- By applying conditions, we tell the database engine to only return records that meet our specific criteria.

## 8.8. Our Filtering Toolkit

- We have several types of operators at our disposal to build these conditions.

### 8.8.1. Comparison Operators

- We use these to compare a column against a value or another column:
  - `=` - Checks if values are equal.
  - `<>` or `!=` - Checks if values are not equal.
  - `> and <` - Greater than or less than.
  - `>= and <=` - Greater than or equal to, or less than or equal to.
-

### 8.8.2. Logical Operators

- We use these when we need to combine multiple conditions:
  - `AND` - We use this when all conditions must be true.
  - `OR` - We use this when at least one condition must be true.
  - `NOT` - We use this to negate a condition (finding everything that does not match).

## 8.9. Range and Membership

- **Range Operator:** `BETWEEN`
  - When we want to find values within a specific spread (like dates or prices), we use `BETWEEN`.
  - **Example:** `WHERE Price BETWEEN 10 AND 50;`
  - Note: This is inclusive, meaning it includes 10 and 50.
- **Membership Operators:** `IN` & `NOT IN`
  - We use `IN` when we want to check if a value matches any item in a specific list.
  - **Example:** `WHERE Country IN ('Italy', 'USA', 'France');`
  - This is a much cleaner way for us to write multiple `OR` conditions.

## 8.10. Search Operator: LIKE

- When we don't know the exact value, we use Pattern Matching with the `LIKE` operator and wildcards:
- `% (Percent)` - Represents zero, one, or multiple characters.
  - `LIKE 'A%'` finds everything starting with "A".
- `_ (Underscore)` - Represents exactly one single character.
  - `LIKE 'L_nd_n'` would find "London".

## 8.11. Handling Missing Data (NULL)

- We must remember that NULL represents a missing or unknown value.
- It is not the same as zero or an empty string.
- To filter for these, we cannot use `=`, so we use:
  - `IS NULL` - To find records with missing data.
  - `IS NOT NULL` - To find records that have any value entered.

## 8.12. Summary of Operator Usage

- As we build our queries, we can combine these tools to create powerful filters:
  | Operator Type | What we use it for | Example |
  |---------------|--------------------|--------------------------------------------|
  | Comparison | Direct matching | Score > 80 |
  | Logical | Combining rules | Country = 'USA' AND Age > 21 |
  | Range | Spread of values | Date BETWEEN '2023-01-01' AND '2023-12-31' |
  | Membership | List matching | ID IN (1, 5, 10) |
  | Search | Partial matching | Name LIKE '%Smith%' |

## 8.13. SQL JOINS & SET Operators: Combining Data

- Why Do We Combine Data?In relational databases, data is often spread across multiple tables to stay organized.
- When we need to see the "Big Picture," we must recombine this data.
- We generally do this for two reasons:
  - **Recombining Data:** Bringing together customers, addresses, and orders to get a complete view.
  - **Data Enrichment:** Adding extra information to a master table from a reference table (e.g., adding city names based on Zip Codes).

### 8.13.1. Two Methods of Combining Data

- We have two primary ways to bring data together, depending on whether we want to add more columns or more rows
- **JOINS (Adding Columns)**
  - We use JOINS when we want to link tables horizontally based on a Key Column (a common piece of information like an ID).
    - Inner Join: We use this to return only the records that have matching values in both tables.
    - Left Join: We use this to keep all records from the left table and the matched records from the right table.
    - Right Join: We use this to keep all records from the right table and the matched records from the left table.
    - Full Join: We use this to return all records when there is a match in either the left or right table.
- **SET Operators (Adding Rows)**
  - We use SET Operators when we want to stack the results of two queries vertically.
  - For this to work, we must ensure both queries have the same number of columns and compatible data types.
  - UNION: We use this to combine rows from both sets and remove any duplicates.
  - UNION ALL: We use this to combine all rows from both sets, including duplicates.
  - EXCEPT (or MINUS): We use this to return unique rows from the first set that do not exist in the second.
  - INTERSECT: We use this to return only the rows that are common to both sets.

### 8.13.2. How We Use Joins

- When we write a JOIN, we must specify the relationship:
  ```sql
    SELECT TableA.Name, TableB.Country
    FROM TableA
    INNER JOIN TableB ON TableA.id = TableB.id;
  ```

### 8.13.3. How We Use SET Operators

- When we use SET operators, we place the keyword between two independent SELECT statements:
  ```sql
    SELECT FirstName FROM Customers
    UNION
    SELECT FirstName FROM Employees;
  ```

### 8.13.4. Summary Table

| Method        | What we add          | Connection Point               |
| ------------- | -------------------- | ------------------------------ |
| JOINS         | Columns (Horizontal) | Based on a Key Column (ID)     |
| SET Operators | Rows (Vertical)      | Based on same column structure |

# 9. Advanced SQL Techniques

- As we move into advanced data environments, we often face challenges like high complexity, performance issues, database stress, and security risks. To manage these, we use specialized tools to simplify our queries and improve efficiency.

## 9.1. Subqueries

- We use a Subquery (or Inner Query) to perform a calculation that will be used by the main query. It is a query nested inside another statement.
  - **Where we use them:** We can place subqueries in the `SELECT`, `FROM`, or `WHERE` clauses.
  - **How they work:** The database engine typically executes the inner query first and passes the result to the outer query.

## 9.2. CTE (Common Table Expression)

- When our queries become too long or hard to read, we use a **CTE**. We define a **CTE** using the `WITH` keyword, creating a temporary result set that we can reference within our main query.
  - **Benefits:** We find CTEs much easier to maintain and read compared to nested subqueries.
  - **Recursive CTEs:** We can even use them to query hierarchical data, like an organizational chart.

## 9.3. Views

- We use Views to save a complex query as a **Virtual Table**.
  - **No Data Storage:** A view doesn't store data itself; it stores the query.
    - Every time we call the view, it runs the underlying script.
  - **Security:** We use views to give users access to specific columns without showing them the entire base table.

## 9.4. Temporary Tables & CTAS

- Sometimes we need to physically store a result set for a short period.
  - **Temp Tables:** We use these for complex multi-step transformations. They only exist during our current session.
  - **CTAS (Create Table As Select):** We use this shortcut to create a new permanent table and populate it with data from a query in one single step.

## 9.5. Stored Procedures

- We use Stored Procedures to save a batch of SQL statements that we can execute repeatedly.
  - **Parameters:** We can pass values into a procedure to make it dynamic.
  - **Logic:** Unlike views, procedures allow us to use programming logic like Error Handling (TRY/CATCH) and Flow Control (IF/ELSE).

## 9.6. Triggers

- We use Triggers to automatically execute a block of code when a specific event occurs in the database, such as an `INSERT`, `UPDATE`, or `DELETE`.
- **Use Case:** We often use triggers for maintaining audit logs or enforcing complex business rules automatically.
