# Database Engineering <!-- omit in toc -->

## Contents <!-- omit in toc -->

- [1. Logical Table](#1-logical-table)
  - [1.1. Row\_ID](#11-row_id)
  - [1.2. Page](#12-page)
  - [1.3. IO](#13-io)
  - [1.4. Heap](#14-heap)
  - [1.5. Index](#15-index)
  - [1.6. B-Tree Indexes](#16-b-tree-indexes)
  - [1.7. Index / Heap](#17-index--heap)
  - [1.8. Without an index](#18-without-an-index)
  - [1.9. With Index](#19-with-index)
  - [1.10. Notes](#110-notes)
- [2. Row-Based vs Column-Based Databases](#2-row-based-vs-column-based-databases)
- [3. Clustered Index](#3-clustered-index)
  - [3.1. Without a Clustered Index (Heap)](#31-without-a-clustered-index-heap)
  - [3.2. With a Clustered Index](#32-with-a-clustered-index)
- [4. Primary Key vs Secondary Index](#4-primary-key-vs-secondary-index)
  - [4.1. Primary Key (Clustered Index / Index Organized Table)](#41-primary-key-clustered-index--index-organized-table)
  - [4.2. Secondary Index](#42-secondary-index)
  - [4.3. Advantages and Disadvantages](#43-advantages-and-disadvantages)
  - [4.4. UUID Consideration](#44-uuid-consideration)
  - [4.5. Database Comparison](#45-database-comparison)
- [5. B-Trees vs B+Trees](#5-b-trees-vs-btrees)
  - [5.1. Full Table Scan](#51-full-table-scan)
  - [5.2. Original B-Tree](#52-original-b-tree)
    - [5.2.1. How B-Tree Helps](#521-how-b-tree-helps)
    - [5.2.2. Limitation B-Tree](#522-limitation-b-tree)
  - [5.3. B+Tree](#53-btree)
  - [5.4. B+Tree of Degree 3](#54-btree-of-degree-3)
  - [5.5. B+Tree \& DBMS Considerations](#55-btree--dbms-considerations)
  - [5.6. Storage Cost in Postgres vs MySQL](#56-storage-cost-in-postgres-vs-mysql)

# 1. Logical Table

- A table is a logical structure composed of rows and columns.
- Regardless of whether the database is relational or document-based, data is ultimately stored as bytes on disk.

| emp_id | emp_name | emp_dob  | emp_salary |
| ------ | -------- | -------- | ---------- |
| 1      | Jefté    | 1/2/1991 | $100,000   |
| 2      | Brenno   | 3/2/2016 | $200,000   |
| 3      | Brunno   | 5/2/2026 | $300,000   |

## 1.1. Row_ID

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

## 1.2. Page

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

## 1.3. IO

- IO operation (input/output) is a read request to the disk.
- We try to minimize this as much as possible.
- An IO can fetch 1 page or more depending on the disk partitions and other factors.
  - An IO **cannot read a single row**, its a page with many rows in them, we get them for free.
- We want to minimize the number of IOs as they are expensive.
- Some IOs in Operating Systems goes to the operating **system cache** and not disk.

## 1.4. Heap

- The Heap is data structure where the table is stored with all its pages one after another.
- This is where the actual data is stored including everything.
- Traversing the heap is expensive as we need to read so may data to find what we want.
- That is why we need indexes that help tell us exactly what part of the heap we need to read. What page(s) of the heap we need to pull.
  ![Heap](/images/heap.png)

## 1.5. Index

- An index is another data structure separate from the heap that has "pointers" to the heap.
- It has part of the data and used to quickly search for something.
- You can index on one column or more..
- Once you find a value of the index, you go to the heap to fetch more information where everything is there.
- Index tells you EXACTLY which page to fetch in the heap instead of taking the hit to scan every page in the heap.
- The index is also stored as pages and cost IO to pull the entries of the index.
- The smaller the index, the more it can fit in memory the faster the search.
- Popular data structure for index is b-trees.

## 1.6. B-Tree Indexes

- The most common index structure is the B-Tree.
- B-Trees allow efficient searching with logarithmic complexity.
- **The index stores**
  - Indexed value.
  - Row ID.
  - Page location.

## 1.7. Index / Heap

![Index & Heap](/images/heap-index-1.png)

## 1.8. Without an index

- The database performs a sequential scan of the heap.
- Every page is read until the matching row is found.
- Performance decreases as table size grows.
  ![No Index](/images/heap-no-index.png)

## 1.9. With Index

1. Search the B-Tree index.
2. Find the Row ID and page location.
3. Read only the required page from the heap.
4. Return the requested row.

- This dramatically reduces I/O operations.
  ![With Index](/images/heap-with-index.png)

## 1.10. Notes

- Sometimes the heap table can be organized around a single index.
  - This is called a clustered index or an Index Organized Table.
- Primary key is usually a clustered index unless otherwise specified.
- MySQL InnoDB always have a primary key (clustered index) other indexes point to the primary key "value".
  - (0, 1) only have secondary indexes and all indexes point directly to the row_id which lives in the heap.

# 2. Row-Based vs Column-Based Databases

# 3. Clustered Index

- A clustered index is an index that determines the physical order of the rows in a table.
- Instead of storing the data separately from the index, the table itself is organized according to the index key.

## 3.1. Without a Clustered Index (Heap)

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

## 3.2. With a Clustered Index

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

# 4. Primary Key vs Secondary Index

- Heap (Heap Organized Table)
  - By default, a table (Heap) stores rows without any physical ordering.
  - New rows are simply appended to the end of the table.
  - Without indexes, finding data requires scanning the Heap.

## 4.1. Primary Key (Clustered Index / Index Organized Table)

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

## 4.2. Secondary Index

- A Secondary Index is a separate data structure (typically a B-Tree).
- The table (Heap) remains unordered.
- **The index stores**
  - Indexed values.
  - References (Row IDs or Primary Key values) to the corresponding rows.
- **Query execution**
  1. Search the index.
  2. Find the row reference.
  3. Access the Heap to retrieve the complete row.

## 4.3. Advantages and Disadvantages

| Primary Key (Clustered Index)                             | Secondary Index                                 |
| --------------------------------------------------------- | ----------------------------------------------- |
| Table is physically ordered.                              | Table remains unordered.                        |
| Excellent for range queries.                              | Excellent for point lookups.                    |
| Faster sequential reads.                                  | Requires an extra lookup to the Heap.           |
| Inserts may be more expensive due to page reorganization. | Lower maintenance cost for the table structure. |

## 4.4. UUID Consideration

- Using a random `UUID` as a clustered Primary Key can reduce write performance because new rows are inserted into random locations instead of sequentially.
- Sequential keys (e.g., auto-increment integers) generally produce better insert performance.

## 4.5. Database Comparison

| Database         | Storage Model                                                                                       |
| ---------------- | --------------------------------------------------------------------------------------------------- |
| **MySQL InnoDB** | Clustered index based on the Primary Key. Secondary indexes point to the Primary Key.               |
| **PostgreSQL**   | Heap Organized Table. All indexes are secondary indexes that point directly to the Row ID (`ctid`). |
| **Oracle**       | Supports both Heap Organized Tables and Index Organized Tables (IOT).                               |
| **SQL Server**   | Supports both clustered and non-clustered indexes.                                                  |

# 5. B-Trees vs B+Trees

- And their impact on production database systems

## 5.1. Full Table Scan

- To find a row in a large table we perform full table scan.
- Reading large tables is slow.
- Requires many I/Os to read all pages.
- We need a way to reduce the search space.
- Find Person with ID 3
  ![Page](/images/page.png)
- Find Person with ID 10000
  ![Page](/images/page.png)

## 5.2. Original B-Tree

- Balanced Data structure for fast traversal.
- B-Tree has Nodes.
- In B-Tree of "m" degree some nodes can have (m) child nodes.
- Node has up to (m-1) elements.
- Each element has a key and a value.
- The value is usually data pointer to the row.
- Data pointer can point to primary key or tuple.
- Root Node, internal node and leaf nodes.
- A node = disk page.

### 5.2.1. How B-Tree Helps

- PostgreSQL uses an internal Tuple ID (`TID`/`CTID`) to identify the physical location of a tuple.
- The TID is a physical identifier, not a logical `PRIMARY KEY`.
- It is associated with the page and position where the tuple is stored.
  ![How B-Tree Helps](/images/b-tree.png)

- [B-Tree paper](https://infolab.usc.edu/csci585/Spring2010/den_ar/indexing.pdf)
- [B-Tree simulator](https://www.cs.usfca.edu/~galles/visualization/BTree.html)

### 5.2.2. Limitation B-Tree

- Elements in all nodes store both the key and the value.
- Internal nodes take more space thus require more IO and can slow down traversal.
- Range queries are slow because of random access (give me all values 1-5).
- B+Tree solves both these problems.
- Hard to fit internal nodes in memory.

## 5.3. B+Tree

- Exactly like B-Tree but only stores keys in internal nodes.
- Values are only stored in leaf nodes.
- Internal nodes are smaller since they only store keys and they can fit more elements.
- Leaf nodes are "linked" so once you find a key you can find all values before and after that key.
- Great for range queries.

## 5.4. B+Tree of Degree 3

- TODO DO

## 5.5. B+Tree & DBMS Considerations

- Cost of leaf pointer (cheap).
- 1 Node fits a DBMS page (most DBMS).
- Can fit internal nodes easily in memory for fast traversal.
- Leaf nodes can live in data files in the heap.
- Most DBMS systems use B+Tree.

## 5.6. Storage Cost in Postgres vs MySQL

- B+Trees secondary index values can either point directly.
  - To the tuple (Postgres) or to the primary key (MySQL).
- If the Primary key data type is expensive this can cause bloat in all secondary indexes for databases such MySQL (innoDB).
- Leaf nodes in MySQL (InnoDB) contains the full row since its an IOT / clustered index.
