- [1. ACID](#1-acid)
  - [1.1. What is a Transaction?](#11-what-is-a-transaction)
    - [1.1.1. Nature of Transactions](#111-nature-of-transactions)
  - [1.2. Atomicity](#12-atomicity)
  - [1.3. Isolation](#13-isolation)
- [2. Logical Table](#2-logical-table)
  - [2.1. Row\_ID](#21-row_id)
  - [2.2. Page](#22-page)
  - [2.3. IO](#23-io)
  - [2.4. Heap](#24-heap)
  - [2.5. Index](#25-index)
  - [2.6. B-Tree Indexes](#26-b-tree-indexes)
  - [2.7. Index / Heap](#27-index--heap)
  - [2.8. Without an index](#28-without-an-index)
  - [2.9. With Index](#29-with-index)
  - [2.10. Notes](#210-notes)
- [3. Row-Based vs Column-Based Databases](#3-row-based-vs-column-based-databases)
- [4. Clustered Index](#4-clustered-index)
  - [4.1. Without a Clustered Index (Heap)](#41-without-a-clustered-index-heap)
  - [4.2. With a Clustered Index](#42-with-a-clustered-index)
- [5. Primary Key vs Secondary Index](#5-primary-key-vs-secondary-index)
  - [5.1. Primary Key (Clustered Index / Index Organized Table)](#51-primary-key-clustered-index--index-organized-table)
  - [5.2. Secondary Index](#52-secondary-index)
  - [5.3. Advantages and Disadvantages](#53-advantages-and-disadvantages)
  - [5.4. UUID Consideration](#54-uuid-consideration)
  - [5.5. Database Comparison](#55-database-comparison)

# 1. ACID

## 1.1. What is a Transaction?

- A collection of queries.
- One unit of work.
- E.g. Account deposit (`SELECT`, `UPDATE`, `UPDATE`).
- **Transaction Lifespan**
  - Transaction `BEGIN`.
  - Transaction `COMMIT`.
  - Transaction `ROLLBACK`.
  - Transaction unexpected ending = `ROLLBACK` (e.g. crash).

### 1.1.1. Nature of Transactions

- Usually Transactions are used to change and modify data
- However, it is perfectly normal to have a read only transaction
- E.g. We want to generate a report and you want to get consistent snapshot based at the time of transaction.
  ![ACID - Transaction](/images/acid-transaction.png)

## 1.2. Atomicity

- All queries in a transaction must succeed.
- If one query fails, all prior successful queries in the transaction should rollback.
- If the database went down prior to a commit of a transaction, all the successful queries in the transactions should rollback.
- **Situation**
  ![ACID - Atomicity](/images/acid-atomicity.png)
  - After we restarted the machine the first account has been debited but the other account has not been credited.
  - This is really bad as we just lost data, and the information is inconsistent.
  - An atomic transaction is a transaction that will rollback all queries if one or more queries failed.
  - The database should clean this up after restart.

## 1.3. Isolation

# 2. Logical Table

- A table is a logical structure composed of rows and columns.
- Regardless of whether the database is relational or document-based, data is ultimately stored as bytes on disk.

| emp_id | emp_name | emp_dob  | emp_salary |
| ------ | -------- | -------- | ---------- |
| 1      | Jefté    | 1/2/1991 | $100,000   |
| 2      | Brenno   | 3/2/2016 | $200,000   |
| 3      | Brunno   | 5/2/2026 | $300,000   |

## 2.1. Row_ID

- Internal and system maintained.
- In certain databases (mysql -innoDB) it is the same as the primary key but other databases like Postgres have a system column row_id (tuple_id).
- **Postgres**
  - **Structure:** (block_number, tuple_index).
    - Block Number (Page Number): The specific data page/block on disk where the row lives (starting at 0).
    - Tuple Index (Line Pointer): The specific slot index within that block pointing to the row data.
    - `select ctid, * from users`
      - **Table**
        | ctid | emp_id | emp_name | emp_dob | emp_salary |
        | ------ | ------ | -------- | -------- | ---------- |
        | (0, 1) | 1 | Jefté | 1/2/1991 | $100,000 |
        | (0, 2) | 2 | Brenno | 3/2/2016 | $200,000 |
        | (0, 3) | 3 | Brunno | 5/2/2026 | $300,000 |

## 2.2. Page

- Depending on the storage model (row vs column store), the rows are stored and read in logical pages.
- The database doesn't read a single row, it reads a page or more in a single IO and we get a lot of rows in that IO.
- Each page has a size (e.g. 8KB in postgres, 16KB in MySQL).
- Assume each page holds 3 rows in this example, with 1001 rows we will have 1001/3 = 333~ pages.
- **Table**
  | emp_id | emp_name | emp_dob | emp_salary |
  | ------ | -------- | -------- | ---------- |
  | 1 | Jefté | 1/2/1991 | $100,000 |
  | 2 | Brenno | 3/2/2016 | $200,000 |
  | 3 | Brunno | 5/2/2026 | $300,000 |
  | ... | ... | ... | ... |
  | 1000 | Brunno | 5/2/2026 | $300,000 |
- **Page 0**
  - 1,Jefté,1/2/1991,$100,000|2,Brenno,3/2/2016,$200,000|3,Brunno,5/2/2026,$300,000
- **Page 1**
  - ( Rows 4,5,6 ) ......
- **Page 2**
  - ( Rows 7,8,9 ) ......
- **Page 333**
  - More rows....1000,10000 ,Bárbara,1/27/1988,$250,000
    ![Page](/images/page.png)

## 2.3. IO

- IO operation (input/output) is a read request to the disk.
- We try to minimize this as much as possible.
- An IO can fetch 1 page or more depending on the disk partitions and other factors.
  - An IO **cannot read a single row**, its a page with many rows in them, we get them for free.
- We want to minimize the number of IOs as they are expensive.
- Some IOs in Operating Systems goes to the operating **system cache** and not disk.

## 2.4. Heap

- The Heap is data structure where the table is stored with all its pages one after another.
- This is where the actual data is stored including everything.
- Traversing the heap is expensive as we need to read so may data to find what we want.
- That is why we need indexes that help tell us exactly what part of the heap we need to read. What page(s) of the heap we need to pull.
  ![Heap](/images/heap.png)

## 2.5. Index

- An index is another data structure separate from the heap that has "pointers" to the heap.
- It has part of the data and used to quickly search for something.
- You can index on one column or more..
- Once you find a value of the index, you go to the heap to fetch more information where everything is there.
- Index tells you EXACTLY which page to fetch in the heap instead of taking the hit to scan every page in the heap.
- The index is also stored as pages and cost IO to pull the entries of the index.
- The smaller the index, the more it can fit in memory the faster the search.
- Popular data structure for index is b-trees.

## 2.6. B-Tree Indexes

- The most common index structure is the B-Tree.
- B-Trees allow efficient searching with logarithmic complexity.
- **The index stores**
  - Indexed value.
  - Row ID.
  - Page location.

## 2.7. Index / Heap

![Index & Heap](/images/heap-index-1.png)

## 2.8. Without an index

- The database performs a sequential scan of the heap.
- Every page is read until the matching row is found.
- Performance decreases as table size grows.
  ![No Index](/images/heap-no-index.png)

## 2.9. With Index

1. Search the B-Tree index.
2. Find the Row ID and page location.
3. Read only the required page from the heap.
4. Return the requested row.

- This dramatically reduces I/O operations.
  ![With Index](/images/heap-with-index.png)

## 2.10. Notes

- Sometimes the heap table can be organized around a single index.
  - This is called a clustered index or an Index Organized Table.
- Primary key is usually a clustered index unless otherwise specified.
- MySQL InnoDB always have a primary key (clustered index) other indexes point to the primary key "value".
  - (0, 1) only have secondary indexes and all indexes point directly to the row_id which lives in the heap.

# 3. Row-Based vs Column-Based Databases

# 4. Clustered Index

- A clustered index is an index that determines the physical order of the rows in a table.
- Instead of storing the data separately from the index, the table itself is organized according to the index key.

## 4.1. Without a Clustered Index (Heap)

- Rows are stored in the order they are inserted.
- Heap
  ```
    Row 1: emp_id = 7
    Row 2: emp_id = 20
    Row 3: emp_id = 1
    Row 4: emp_id = 15
  ```
- To find emp_id = 15, the database uses an index (if one exists) to locate the row in the heap.
  ```
    B-Tree Index
    1 -> Row 3
    7 -> Row 1
    15 -> Row 4
    20 -> Row 2
  ```
- **The query performs**
  1. Search the index.
  2. Follow the pointer to the heap.
  3. Read the row.

## 4.2. With a Clustered Index

- The table itself is stored in key order.
- Clustered Table
  ```
    Row 1: emp_id = 1
    Row 2: emp_id = 7
    Row 3: emp_id = 15
    Row 4: emp_id = 20
  ```
- The index and the data are organized together.
- Searching for emp_id = 15 goes directly to the page containing the row.

# 5. Primary Key vs Secondary Index

- Heap (Heap Organized Table)
  - By default, a table (Heap) stores rows without any physical ordering.
  - New rows are simply appended to the end of the table.
  - Without indexes, finding data requires scanning the Heap.

## 5.1. Primary Key (Clustered Index / Index Organized Table)

- A Primary Key is often implemented as a **Clustered Index**, meaning the table is physically organized according to the primary key values.
- **Because rows are stored in key order**
  - Range queries (e.g., `WHERE id BETWEEN 100 AND 200`) are very efficient.
  - Sequential reads require fewer I/O operations.
- **Maintaining this order has a cost**
  - **INSERTS** and **UPDATES** may require reorganizing pages to preserve the ordering.
- **Different databases implement this differently**
  - **MySQL InnoDB:** Uses a clustered index by default (organized by the Primary Key).
  - **Oracle:** Calls this an **Index Organized Table (IOT)**.
  - **SQL Server:** Supports clustered indexes as an option.
  - **PostgreSQL:** Primary Key is simply a unique B-Tree index.
    - It is not a clustered index.

## 5.2. Secondary Index

- A Secondary Index is a separate data structure (typically a B-Tree).
- The table (Heap) remains unordered.
- **The index stores**
  - Indexed values.
  - References (Row IDs or Primary Key values) to the corresponding rows.
- **Query execution**
  1. Search the index.
  2. Find the row reference.
  3. Access the Heap to retrieve the complete row.

## 5.3. Advantages and Disadvantages

| Primary Key (Clustered Index)                             | Secondary Index                                 |
| --------------------------------------------------------- | ----------------------------------------------- |
| Table is physically ordered.                              | Table remains unordered.                        |
| Excellent for range queries.                              | Excellent for point lookups.                    |
| Faster sequential reads.                                  | Requires an extra lookup to the Heap.           |
| Inserts may be more expensive due to page reorganization. | Lower maintenance cost for the table structure. |

## 5.4. UUID Consideration

- Using a random `UUID` as a clustered Primary Key can reduce write performance because new rows are inserted into random locations instead of sequentially.
- Sequential keys (e.g., auto-increment integers) generally produce better insert performance.

## 5.5. Database Comparison

| Database         | Storage Model                                                                                       |
| ---------------- | --------------------------------------------------------------------------------------------------- |
| **MySQL InnoDB** | Clustered index based on the Primary Key. Secondary indexes point to the Primary Key.               |
| **PostgreSQL**   | Heap Organized Table. All indexes are secondary indexes that point directly to the Row ID (`ctid`). |
| **Oracle**       | Supports both Heap Organized Tables and Index Organized Tables (IOT).                               |
| **SQL Server**   | Supports both clustered and non-clustered indexes.                                                  |
