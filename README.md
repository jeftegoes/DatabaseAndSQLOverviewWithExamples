# Databases & SQL Overview with examples <!-- omit in toc -->

## Contents <!-- omit in toc -->

- [1. Introduction](#1-introduction)
- [2. Database Management System (DBMS)](#2-database-management-system-dbms)
- [3. Types of Databases](#3-types-of-databases)
- [4. Database Structure](#4-database-structure)
  - [4.1. Components of a Table](#41-components-of-a-table)
  - [4.2. Data Types](#42-data-types)
- [5. Types of SQL Commands](#5-types-of-sql-commands)
  - [5.1. DQL (Data Query Language)](#51-dql-data-query-language)
  - [5.2. DDL (Data Definition Language)](#52-ddl-data-definition-language)
  - [5.3. DML (Data Manipulation Language)](#53-dml-data-manipulation-language)
  - [5.4. DCL (Data Control Language)](#54-dcl-data-control-language)
  - [5.5. TCL (Transaction Control Language)](#55-tcl-transaction-control-language)
- [6. Anatomy of a SQL Statement](#6-anatomy-of-a-sql-statement)
  - [6.1. Basic Query Structure](#61-basic-query-structure)
  - [6.2. Filtering and Sorting](#62-filtering-and-sorting)
  - [6.3. Advanced Selection Tools](#63-advanced-selection-tools)
  - [6.4. Aliases (AS)](#64-aliases-as)
  - [6.5. Logical Evaluation Order](#65-logical-evaluation-order)

# 1. Introduction

- **What is SQL?**
  - SQL stands for Structured Query Language.
  - It is often pronounced as **SeQuel**.
  - It is used to interact with data stored in databases, allowing users to perform complex calculations like finding "Total Spending?" across massive datasets.
- **Why Learn SQL?**
  - Talk to Data: It allows you to communicate directly with data systems.
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

## 5.1. DQL (Data Query Language)

- Used to retrieve data from the database.
  - `SELECT` - The primary command used to fetch data.

## 5.2. DDL (Data Definition Language)

- Used to define or modify the database structure.
  - `CREATE` - To create new databases or tables.
  - `ALTER` - To modify an existing structure.
  - `DROP` - To delete a database or table.

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

# 6. Anatomy of a SQL Statement

- A SQL statement is composed of several specific elements that tell the database how to process the request.
- **Key Components**
  - **Comments (--):** Used to document the code. Anything after -- is ignored by the engine (e.g., -- Retrieve Customers Data).
  - **Keywords:** Reserved words that have a special meaning in SQL (e.g., SELECT, FROM, WHERE).
  - **Clauses:** The building blocks of a statement (e.g., the WHERE clause).
  - **Functions:** Built-in tools to transform data (e.g., LOWER(country) converts text to lowercase).
  - **Identifiers:** Names of database objects like tables (customers) or columns (name).
  - **Operators:** Used for comparisons (e.g., =).
  - **Literals:** Constant values or strings (e.g., 'Italy').

## 6.1. Basic Query Structure

- To retrieve data, SQL uses a fundamental structure:
  ```sql
  SELECT column_name
  FROM table_name;
  ```
- `SELECT` - Defines which columns you want to see.
- `FROM` - Specifies the table where the data is located.
- Asterisk (`*`): Use `SELECT *` to retrieve all columns from a table.

## 6.2. Filtering and Sorting

- `WHERE` Clause
  - Used to filter records based on specific conditions.
    - **Example:** `WHERE country = 'Italy'` only returns rows where the country is Italy.
- `ORDER BY` Clause
  - Used to sort the result set in ascending or descending order.
    - `ASC` - Ascending (default, e.g., A-Z, 1-10).
    - `DESC` - Descending (e.g., Z-A, 10-1).

## 6.3. Advanced Selection Tools

- `DISTINCT`
  - Removes duplicate values from the result set, showing only unique entries.
    - **Usage:** `SELECT DISTINCT country FROM customers;`
- `TOP` / `LIMIT`
- Used to specify the number of records to return.
- This is useful for large tables when you only need a "preview" or the highest/lowest values.
  - **SQL Server:** `SELECT TOP 5 * FROM customers;`
  - **PostgreSQL/MySQL:** `SELECT * FROM customers LIMIT 5;`

## 6.4. Aliases (AS)

- Aliases are used to give a table or a column a temporary name, making the output more readable.
- **Example:** `SELECT name AS customer_name FROM customers;`

## 6.5. Logical Evaluation Order

- It is important to note that SQL does not process clauses in the order they are written.
- The standard execution order is:
  - `FROM` (Pick the table).
  - `WHERE` (Filter rows).
  - `SELECT` (Choose columns).
  - `ORDER BY` (Sort results).
