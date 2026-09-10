# Partitioning <!-- omit in toc -->

## Contents <!-- omit in toc -->

- [1. What is Partitioning?](#1-what-is-partitioning)
- [2. Vertical vs Horizontal Partitioning](#2-vertical-vs-horizontal-partitioning)
- [3. Partitioning Types](#3-partitioning-types)
- [4. Horizontal Partitioning vs Sharding](#4-horizontal-partitioning-vs-sharding)
- [5. Pros of Partitioning](#5-pros-of-partitioning)
- [6. Cons of Partitioning](#6-cons-of-partitioning)
- [7. Example](#7-example)
  - [7.1. Without partition](#71-without-partition)
  - [7.2. With partitions](#72-with-partitions)
  - [7.3. Analysis](#73-analysis)

# 1. What is Partitioning?

- Without Partition
  ![Without Partition](/images/without-partitioning.png)
- With Partition
  ![With Partition](/images/with-partitioning.png)

# 2. Vertical vs Horizontal Partitioning

- **Horizontal Partitioning** splits rows into partitions.
  - Range or list
- **Vertical partitioning** splits columns partitions.
  - Large column (blob) that you can store in a slow access drive in its own tablespace.

# 3. Partitioning Types

- **By Range**
  - Dates, ids (e.g. by logdate or customerid from to).
- **By List**
  - Discrete values (e.g. states CA, AL, etc.) or zip codes.
- **By Hash**
  - Hash functions (consistent hashing).

# 4. Horizontal Partitioning vs Sharding

- HP splits big table into multiple tables in the same database, client is agnostic.
- Sharding splits big table into multiple tables across multiple database servers
- HP table name changes (or schema)
- Sharding everything is the same but server changes

# 5. Pros of Partitioning

- Improves query performance when accessing a single partition.
- Sequential scan vs scattered index scan.
- Easy bulk loading (attach partition).
- Archive old data that are barely accessed into cheap storage.

# 6. Cons of Partitioning

- Updates that move rows from a partition to another (slow or fail sometimes).
- Inefficient queries could accidently scan all partitions resulting in slower performance.
- Schema changes can be challenging (DBMS could manage it though).

# 7. Example

## 7.1. Without partition

1. `CREATE TABLE grades_without_partition (id SERIAL NOT NULL, grade INT NOT NULL);`
2. `INSERT INTO grades_without_partition (grade) SELECT FLOOR(RANDOM() * 100) FROM GENERATE_SERIES(0, 10000000);`
3. `CREATE INDEX grades_without_partition_idx ON grades_without_partition(grade);`
4. `SELECT * FROM grades_without_partition WHERE grade = 10;`
5. `EXPLAIN ANALYZE SELECT * FROM grades_without_partition WHERE grade = 10;`

## 7.2. With partitions

1. `CREATE TABLE grades_partitioned (id SERIAL NOT NULL, grade INT NOT NULL) PARTITION BY RANGE (grade);`
2. `CREATE TABLE grades_0_35 (LIKE grades_partitioned INCLUDING INDEXES);`
3. `CREATE TABLE grades_35_60 (LIKE grades_partitioned INCLUDING INDEXES);`
4. `CREATE TABLE grades_60_80 (LIKE grades_partitioned INCLUDING INDEXES);`
5. `CREATE TABLE grades_80_100 (LIKE grades_partitioned INCLUDING INDEXES);`
6. `ALTER TABLE grades_partitioned ATTACH PARTITION grades_0_35 FOR VALUES FROM (0) TO (35);`
7. `ALTER TABLE grades_partitioned ATTACH PARTITION grades_35_60 FOR VALUES FROM (35) TO (60);`
8. `ALTER TABLE grades_partitioned ATTACH PARTITION grades_60_80 FOR VALUES FROM (60) TO (80);`
9. `ALTER TABLE grades_partitioned ATTACH PARTITION grades_80_100 FOR VALUES FROM (80) TO (100);`
10. `INSERT INTO grades_partitioned SELECT * FROM grades_without_partition;`
11. `SELECT COUNT(*) FROM grades_partitioned;`
12. `SELECT MAX(grade) FROM grades_0_35;`
13. `SELECT MIN(grade) FROM grades_35_60;`
14. `CREATE INDEX grades_partitioned_idx ON grades_partitioned(grade);`
15. `SELECT * FROM grades_partitioned WHERE grade = 10;`
16. `EXPLAIN ANALYZE SELECT * FROM grades_partitioned WHERE grade = 10;`
17. `SELECT * FROM grades_partitioned WHERE grade = 75;`
18. `EXPLAIN ANALYZE SELECT * FROM grades_partitioned WHERE grade = 75;`
19. `SELECT * FROM grades_partitioned WHERE grade BETWEEN 20 AND 70;`
20. `EXPLAIN ANALYZE SELECT * FROM grades_partitioned WHERE grade BETWEEN 20 AND 70;`

## 7.3. Analysis

1. `SELECT relname, pg_size_pretty(pg_relation_size(oid)) AS table_size FROM pg_class ORDER BY pg_relation_size(oid) DESC;`
