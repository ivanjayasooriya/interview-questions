# SQL Interview Questions & Answers — Complete Guide

A comprehensive reference covering SQL fundamentals, joins, subqueries, aggregation, indexing, transactions, normalization, window functions, and database design.

---

## Table of Contents
1. [SQL Basics](#1-sql-basics)
2. [Data Types & DDL](#2-data-types--ddl)
3. [Filtering, Sorting & Basic Queries](#3-filtering-sorting--basic-queries)
4. [Joins](#4-joins)
5. [Aggregation & Grouping](#5-aggregation--grouping)
6. [Subqueries](#6-subqueries)
7. [Set Operations](#7-set-operations)
8. [Window Functions](#8-window-functions)
9. [Constraints & Keys](#9-constraints--keys)
10. [Normalization & Database Design](#10-normalization--database-design)
11. [Indexes](#11-indexes)
12. [Transactions & ACID](#12-transactions--acid)
13. [Concurrency Control & Locking](#13-concurrency-control--locking)
14. [Views, CTEs & Stored Procedures](#14-views-ctes--stored-procedures)
15. [Triggers](#15-triggers)
16. [Query Optimization & Execution Plans](#16-query-optimization--execution-plans)
17. [NULL Handling](#17-null-handling)
18. [String, Date & Numeric Functions](#18-string-date--numeric-functions)
19. [SQL vs NoSQL](#19-sql-vs-nosql)
20. [Common Query Patterns & Tricky Problems](#20-common-query-patterns--tricky-problems)

---

## 1. SQL Basics

**Q1. What is SQL?**
Structured Query Language — a declarative language for defining, querying, and manipulating data in relational database management systems (RDBMS). You specify *what* data you want, not *how* to retrieve it — the query optimizer determines the execution strategy.

**Q2. What are the different categories of SQL commands?**
- **DDL (Data Definition Language):** `CREATE`, `ALTER`, `DROP`, `TRUNCATE` — defines schema structure.
- **DML (Data Manipulation Language):** `SELECT`, `INSERT`, `UPDATE`, `DELETE` — manipulates data.
- **DCL (Data Control Language):** `GRANT`, `REVOKE` — controls access permissions.
- **TCL (Transaction Control Language):** `COMMIT`, `ROLLBACK`, `SAVEPOINT` — manages transactions.

**Q3. What is the difference between a database, a schema, and a table?**
A **database** is a collection of organized data, typically containing multiple schemas. A **schema** is a logical namespace/grouping of database objects (tables, views, procedures) within a database. A **table** is a structured collection of rows and columns storing actual data, belonging to a schema.

**Q4. What is the difference between `DELETE`, `TRUNCATE`, and `DROP`?**
| | Type | Rollback? | Resets identity? | Removes structure? | Fires triggers? |
|---|---|---|---|---|---|
| `DELETE` | DML | Yes (within transaction) | No | No | Yes |
| `TRUNCATE` | DDL | Usually no (implicit commit in most RDBMS) | Yes | No | No |
| `DROP` | DDL | No | N/A | Yes (removes table entirely) | No |

`DELETE` can use a `WHERE` clause (row-level, logged); `TRUNCATE` removes all rows at once (minimally logged, faster); `DROP` removes the table structure and data entirely.

**Q5. What is the order of execution of a SQL query's clauses?**
Logical processing order (differs from the order you *write* the query):
```
FROM → JOIN → WHERE → GROUP BY → HAVING → SELECT → DISTINCT → ORDER BY → LIMIT/OFFSET
```
Understanding this explains why you can't reference a `SELECT` alias in `WHERE` (WHERE is evaluated before SELECT), but you generally can in `ORDER BY` (evaluated after SELECT).

**Q6. What is the difference between a clustered and non-clustered index at a conceptual level (and how does it relate to primary keys)?**
Covered in depth in the Indexes section (Q57–Q58) — briefly, a clustered index determines the physical storage order of table rows (usually built on the primary key), while a non-clustered index is a separate structure with pointers back to the actual rows.

---

## 2. Data Types & DDL

**Q7. What are common SQL data types?**
- **Numeric:** `INT`, `SMALLINT`, `BIGINT`, `DECIMAL`/`NUMERIC` (exact precision), `FLOAT`/`REAL` (approximate).
- **Character:** `CHAR(n)` (fixed-length, padded), `VARCHAR(n)` (variable-length), `TEXT` (large variable text).
- **Date/Time:** `DATE`, `TIME`, `TIMESTAMP`, `DATETIME`.
- **Boolean:** `BOOLEAN` (or `BIT`/`TINYINT` in some RDBMS).
- **Binary:** `BLOB`, `VARBINARY`.
- **Other:** `JSON`, `UUID`, `ENUM`, `ARRAY` (varies by RDBMS).

**Q8. What is the difference between `CHAR` and `VARCHAR`?**
`CHAR(n)` is fixed-length — always stores `n` characters, padding with spaces if shorter, which can waste space but offers slightly faster fixed-offset access. `VARCHAR(n)` is variable-length — stores only the actual characters (plus a small length prefix), saving space for variable-length data but with marginally more overhead to read.

**Q9. What is the difference between `DECIMAL`/`NUMERIC` and `FLOAT`/`DOUBLE`?**
`DECIMAL`/`NUMERIC` store exact fixed-point values (defined precision and scale) — essential for financial/monetary data where rounding errors are unacceptable. `FLOAT`/`DOUBLE` are approximate, binary floating-point representations — faster for scientific computation but can introduce small rounding errors, making them unsuitable for currency.

**Q10. What is the difference between `ALTER`, `UPDATE`, and `MODIFY`?**
`ALTER TABLE` changes the table's *structure* (add/drop/modify columns, constraints). `UPDATE` changes *data* within existing rows. `MODIFY` (syntax varies by RDBMS, e.g., MySQL uses `ALTER TABLE ... MODIFY COLUMN`) changes a column's definition (data type, constraints) as part of an `ALTER` statement.

**Q11. What does `CREATE TABLE ... AS SELECT` (CTAS) do?**
Creates a new table and populates it with the result set of a `SELECT` query in one statement, inferring column names/types from the query. Useful for quickly snapshotting or duplicating data, though it typically doesn't carry over constraints, indexes, or triggers from the source table.

---

## 3. Filtering, Sorting & Basic Queries

**Q12. What is the difference between `WHERE` and `HAVING`?**
`WHERE` filters individual rows *before* grouping/aggregation occurs and cannot reference aggregate functions. `HAVING` filters *groups* after `GROUP BY` has been applied, and can reference aggregate functions (e.g., `HAVING COUNT(*) > 5`).

**Q13. What is the difference between `DISTINCT` and `GROUP BY`?**
`DISTINCT` removes duplicate rows from the result set. `GROUP BY` groups rows sharing common values, typically to apply aggregate functions per group. `SELECT DISTINCT col FROM t` and `SELECT col FROM t GROUP BY col` often produce the same result for simple cases, but `GROUP BY` is more powerful (supports aggregates per group, `HAVING`).

**Q14. What is the difference between `IN`, `BETWEEN`, and `LIKE`?**
- `IN`: matches a value against a discrete list: `WHERE status IN ('active', 'pending')`.
- `BETWEEN`: matches a value within an inclusive range: `WHERE age BETWEEN 18 AND 30`.
- `LIKE`: pattern matching using wildcards (`%` = any sequence, `_` = single character): `WHERE name LIKE 'A%'`.

**Q15. What is the difference between `LIMIT`/`OFFSET`, `TOP`, and `FETCH FIRST`?**
Different RDBMS use different syntax to restrict/paginate result sets: MySQL/PostgreSQL use `LIMIT n OFFSET m`; SQL Server uses `SELECT TOP n` or `OFFSET m ROWS FETCH NEXT n ROWS ONLY`; standard SQL (and Oracle 12c+) uses `FETCH FIRST n ROWS ONLY`. All achieve pagination but with vendor-specific syntax.

**Q16. Why can't you reference a column alias in the `WHERE` clause?**
Because of the logical execution order (Q5) — `WHERE` is processed before `SELECT`, so the alias doesn't exist yet at that point. To filter on a computed value, either repeat the expression in `WHERE`, use a subquery/CTE, or (where supported) use `HAVING` after grouping.

**Q17. What is the difference between `ORDER BY` ascending/descending default behavior, and how does NULL sort?**
`ASC` (ascending, default) and `DESC` (descending). NULL sort position is RDBMS-dependent: PostgreSQL/Oracle sort NULLs last by default in `ASC` (configurable via `NULLS FIRST`/`NULLS LAST`); MySQL/SQL Server sort NULLs first in `ASC` order by default.

---

## 4. Joins

**Q18. What are the different types of SQL joins?**
- **INNER JOIN:** returns only matching rows from both tables.
- **LEFT (OUTER) JOIN:** all rows from the left table, plus matched rows from the right (unmatched right columns are `NULL`).
- **RIGHT (OUTER) JOIN:** all rows from the right table, plus matched rows from the left.
- **FULL (OUTER) JOIN:** all rows from both tables, matched where possible, `NULL` where not.
- **CROSS JOIN:** Cartesian product — every row from table A paired with every row from table B.
- **SELF JOIN:** a table joined with itself (using aliases), often used for hierarchical data.

**Q19. What is the difference between `INNER JOIN` and `WHERE`-clause implicit joins?**
`SELECT * FROM a, b WHERE a.id = b.a_id` (implicit/old-style join) is functionally similar to `SELECT * FROM a INNER JOIN b ON a.id = b.a_id` (explicit ANSI join syntax), but explicit `JOIN` syntax is preferred: clearer separation of join conditions from filter conditions, less error-prone (avoids accidental cross joins from a missing condition), and more readable for complex multi-table queries.

**Q20. What happens with a `LEFT JOIN` when there's no matching row in the right table?**
The result still includes the left table's row, with `NULL` values populated for all columns from the right table.

**Q21. How do you find rows in table A that have no matching row in table B ("anti-join")?**
```sql
SELECT a.*
FROM a
LEFT JOIN b ON a.id = b.a_id
WHERE b.a_id IS NULL;
```
Alternatively: `WHERE a.id NOT IN (SELECT a_id FROM b WHERE a_id IS NOT NULL)` or `WHERE NOT EXISTS (SELECT 1 FROM b WHERE b.a_id = a.id)`.

**Q22. What is a self-join, and when would you use one?**
A table joined to itself via aliases, used when rows in a table relate to other rows in the *same* table — e.g., finding employees and their managers stored in the same `employees` table:
```sql
SELECT e.name AS employee, m.name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.id;
```

**Q23. What is a Cartesian product, and when might it happen unintentionally?**
The result of pairing every row of one table with every row of another (`CROSS JOIN`), producing `m × n` rows. Unintentional Cartesian products commonly occur from a missing or incorrect `JOIN` condition, causing an explosion in result set size — a common performance bug.

**Q24. Can you join more than two tables? How does join order affect the result/performance?**
Yes — chain multiple `JOIN` clauses. The **logical result** is the same regardless of join order (assuming correct join conditions), but the **query optimizer** may choose different physical execution orders for performance, and in poorly-optimized engines, join order can matter for performance (though modern optimizers generally reorder joins based on cost estimation and statistics).

**Q25. What is the difference between `USING` and `ON` in a join clause?**
`ON` specifies an arbitrary join condition: `JOIN b ON a.id = b.a_id`. `USING` is shorthand when the join columns share the same name in both tables: `JOIN b USING (id)` — and it also collapses the duplicate column into a single output column, unlike `ON`.

---

## 5. Aggregation & Grouping

**Q26. What are the common aggregate functions?**
`COUNT()`, `SUM()`, `AVG()`, `MIN()`, `MAX()`. Most (except `COUNT(*)`) ignore `NULL` values in their computation.

**Q27. What is the difference between `COUNT(*)`, `COUNT(1)`, and `COUNT(column)`?**
`COUNT(*)` counts all rows, regardless of `NULL` values. `COUNT(1)` is functionally equivalent to `COUNT(*)` in modern optimizers (counts rows). `COUNT(column)` counts only rows where that specific column is **not** `NULL`.

**Q28. What does `GROUP BY` require regarding non-aggregated columns?**
In standard SQL (and strict modes of most RDBMS), every column in the `SELECT` list that isn't wrapped in an aggregate function must appear in the `GROUP BY` clause — otherwise, the value to display for that column within a group would be ambiguous. (MySQL historically allowed exceptions to this in non-strict mode, picking an arbitrary value — generally discouraged.)

**Q29. How do you filter groups based on an aggregate result?**
Use `HAVING`, since `WHERE` can't reference aggregates (executes before grouping):
```sql
SELECT department, COUNT(*) AS emp_count
FROM employees
GROUP BY department
HAVING COUNT(*) > 10;
```

**Q30. What is the difference between `GROUP BY` with multiple columns vs. `GROUPING SETS`, `ROLLUP`, and `CUBE`?**
- `GROUP BY a, b`: groups by the unique combination of `a` and `b`.
- `ROLLUP(a, b)`: produces the normal grouping plus hierarchical subtotals (group by `a,b`, then by `a` alone, then a grand total).
- `CUBE(a, b)`: produces all possible combinations of subtotals (every combination of grouping columns, including none).
- `GROUPING SETS`: lets you explicitly specify exactly which grouping combinations you want, combining multiple `GROUP BY`s into one query.

---

## 6. Subqueries

**Q31. What is a subquery, and what types exist?**
A query nested inside another query. Types: **scalar subquery** (returns a single value, usable anywhere an expression is valid), **row/column subquery** (returns multiple rows/columns, used with `IN`, `ANY`, `ALL`), **correlated subquery** (references columns from the outer query, re-evaluated per outer row), and **subquery in `FROM`** (derived table/inline view).

**Q32. What is the difference between a correlated and a non-correlated subquery?**
A **non-correlated subquery** executes independently of the outer query (runs once, producing a fixed result used by the outer query). A **correlated subquery** references a column from the outer query, so it must conceptually re-execute once per row of the outer query — often less efficient unless the optimizer can rewrite it as a join.

**Q33. What is the difference between a subquery and a join for retrieving related data?**
They can often produce equivalent results, but: joins typically let the optimizer choose efficient set-based execution strategies and allow selecting columns from both tables in the same result row; subqueries (especially in `WHERE`) are sometimes more readable for existence checks or aggregated comparisons, but correlated subqueries can be slower if not rewritten as joins by the optimizer. Modern query optimizers frequently rewrite semantically equivalent subqueries into joins internally.

**Q34. What is the difference between `EXISTS` and `IN`?**
`EXISTS` checks whether a subquery returns *any* rows (stops at the first match — often efficient, especially for correlated subqueries with indexes), returning a boolean; doesn't care about the actual values returned. `IN` checks whether a value matches any value in a list/subquery result; can behave unexpectedly with `NULL` values in the subquery result set (`NOT IN` with a `NULL` in the list returns no rows at all, a common gotcha). `NOT EXISTS` is generally the safer choice over `NOT IN` when the subquery might contain `NULL`s.

**Q35. What is a derived table (inline view)?**
A subquery used in the `FROM` clause, treated as a temporary, unnamed (must be aliased) table for the duration of the query:
```sql
SELECT dept, avg_salary
FROM (SELECT dept, AVG(salary) AS avg_salary FROM employees GROUP BY dept) AS dept_avg
WHERE avg_salary > 50000;
```

**Q36. What is the difference between `ANY`/`SOME` and `ALL` in subquery comparisons?**
`> ANY (subquery)` is true if the value is greater than *at least one* value returned by the subquery (equivalent to comparing against the minimum). `> ALL (subquery)` is true only if the value is greater than *every* value returned (equivalent to comparing against the maximum).

---

## 7. Set Operations

**Q37. What are the SQL set operators, and what are the requirements to use them?**
`UNION`, `UNION ALL`, `INTERSECT`, `EXCEPT`/`MINUS` (Oracle uses `MINUS`). All require both queries to have the **same number of columns** with **compatible data types**, in the same order.

**Q38. What is the difference between `UNION` and `UNION ALL`?**
`UNION` combines result sets and **removes duplicate rows** (requires an implicit sort/hash to de-duplicate — more expensive). `UNION ALL` combines result sets **keeping all rows, including duplicates** — faster since no de-duplication step is needed. Use `UNION ALL` whenever duplicates are acceptable or known not to occur, for better performance.

**Q39. What do `INTERSECT` and `EXCEPT`/`MINUS` do?**
`INTERSECT` returns only rows present in **both** result sets. `EXCEPT` (standard SQL/PostgreSQL/SQL Server) or `MINUS` (Oracle) returns rows present in the first result set but **not** in the second.

---

## 8. Window Functions

**Q40. What is a window function, and how does it differ from an aggregate function used with `GROUP BY`?**
A window function performs a calculation across a set of rows ("window") related to the current row, **without collapsing rows into groups** — each input row still appears in the output, with the computed value attached. Aggregates with `GROUP BY` collapse multiple rows into one summary row per group.

**Q41. What is the basic syntax of a window function?**
```sql
function() OVER (
    [PARTITION BY col1, col2, ...]
    [ORDER BY col3, col4, ...]
    [ROWS/RANGE BETWEEN ... AND ...]
)
```
`PARTITION BY` divides rows into groups (like `GROUP BY`, but without collapsing); `ORDER BY` defines row order within each partition (needed for ranking/running calculations); the frame clause (`ROWS BETWEEN`) defines which rows within the partition are included in the calculation relative to the current row.

**Q42. What is the difference between `ROW_NUMBER()`, `RANK()`, and `DENSE_RANK()`?**
- `ROW_NUMBER()`: assigns a unique sequential number to each row (1, 2, 3, 4...), even for ties — ties broken arbitrarily (or by additional `ORDER BY` columns).
- `RANK()`: assigns the same rank to tied rows, then **skips** the next rank(s) accordingly (1, 2, 2, 4).
- `DENSE_RANK()`: assigns the same rank to tied rows, but does **not** skip ranks (1, 2, 2, 3).

**Q43. How would you find the top N rows per group (e.g., top 3 highest-paid employees per department)?**
```sql
SELECT * FROM (
    SELECT e.*, ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) AS rn
    FROM employees e
) ranked
WHERE rn <= 3;
```

**Q44. What do `LAG()` and `LEAD()` do?**
`LAG(col, n)` accesses a value from `n` rows *before* the current row within the partition/order (e.g., "previous month's value"). `LEAD(col, n)` accesses a value from `n` rows *after* the current row. Both are useful for period-over-period comparisons (e.g., month-over-month growth) without a self-join.

**Q45. What is the difference between `ROWS` and `RANGE` in a window frame clause?**
`ROWS BETWEEN` defines the frame based on physical row position (e.g., "2 rows before to 1 row after"). `RANGE BETWEEN` defines the frame based on the logical value in the `ORDER BY` column (e.g., all rows with the same or a value-based range), treating peer rows (tied order values) as part of the same frame — subtle but important difference when there are duplicate order-by values.

**Q46. What do `FIRST_VALUE()`, `LAST_VALUE()`, and `NTH_VALUE()` do?**
Return the first, last, or Nth value within the current window frame (order matters, and frame boundaries matter — `LAST_VALUE()` often needs an explicit frame clause like `ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING` to consider the whole partition rather than just up to the current row, which is the default frame).

**Q47. How would you calculate a running total (cumulative sum)?**
```sql
SELECT order_date, amount,
       SUM(amount) OVER (ORDER BY order_date ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running_total
FROM orders;
```

**Q48. What does `NTILE(n)` do?**
Divides the partition's rows into `n` roughly equal-sized buckets (groups), assigning each row a bucket number (1 to n) — commonly used for percentile/quartile bucketing (e.g., `NTILE(4)` for quartiles).

---

## 9. Constraints & Keys

**Q49. What is a primary key, and what properties must it have?**
A column (or set of columns) that uniquely identifies each row in a table. Must be **unique** and **not null** for every row. A table can have only one primary key (though it may be composite, spanning multiple columns), and it typically has a clustered index automatically created on it (RDBMS-dependent).

**Q50. What is a foreign key?**
A column (or set of columns) in one table that references the primary key (or a unique key) of another table, enforcing **referential integrity** — ensuring the referenced value actually exists in the parent table (or is `NULL`, if allowed).

**Q51. What is the difference between a primary key and a unique key?**
Both enforce uniqueness, but a **primary key** disallows `NULL` values and a table can have only one; a **unique key/constraint** allows `NULL` values (typically one `NULL` per column in most RDBMS, since `NULL ≠ NULL`) and a table can have multiple unique constraints.

**Q52. What are `ON DELETE`/`ON UPDATE` referential actions for foreign keys?**
- `CASCADE`: automatically delete/update dependent rows when the referenced row is deleted/updated.
- `SET NULL`: set the foreign key column to `NULL`.
- `SET DEFAULT`: set it to its default value.
- `RESTRICT`/`NO ACTION`: prevent the delete/update if dependent rows exist (raises an error).

**Q53. What is a composite key?**
A primary (or unique) key composed of two or more columns, where the combination of values must be unique, even though individual columns may repeat — common in many-to-many junction/bridge tables.

**Q54. What is a candidate key vs. a super key vs. an alternate key?**
- **Super key:** any set of columns that uniquely identifies a row (may include extra, unnecessary columns).
- **Candidate key:** a *minimal* super key (no redundant columns) — a table can have multiple candidate keys.
- **Primary key:** the candidate key chosen to be the table's main identifier.
- **Alternate key:** any candidate key **not** chosen as the primary key.

**Q55. What is a `CHECK` constraint?**
Enforces that values in a column satisfy a specified boolean condition, e.g., `CHECK (age >= 18)` or `CHECK (salary > 0)` — rejecting inserts/updates that violate the condition.

**Q56. What is the difference between `NOT NULL` and `DEFAULT` constraints?**
`NOT NULL` requires a value to always be provided for that column (rejects `NULL`). `DEFAULT` specifies a value to use automatically when no value is explicitly provided during insert — the two are often combined (`DEFAULT 0 NOT NULL`), but a column can allow `NULL` and still have a default, or require a value with no default.

---

## 10. Normalization & Database Design

**Q57. What is normalization, and why is it used?**
The process of organizing a database's tables and columns to minimize data redundancy and avoid update/insert/delete anomalies, by decomposing tables based on functional dependencies.

**Q58. What are the normal forms (1NF through 3NF, briefly BCNF)?**
- **1NF (First Normal Form):** each column holds atomic (indivisible) values; no repeating groups/arrays in a single column; each row is unique.
- **2NF:** must be in 1NF, and every non-key column must depend on the **entire** primary key (eliminates partial dependency — relevant for composite keys).
- **3NF:** must be in 2NF, and no non-key column depends on another non-key column (eliminates transitive dependency — every non-key attribute depends only on the key, the whole key, and nothing but the key).
- **BCNF (Boyce-Codd Normal Form):** a stricter version of 3NF — every determinant (column that another column depends on) must be a candidate key.

**Q59. What are insertion, update, and deletion anomalies?**
Problems that arise from poorly normalized (redundant) schemas:
- **Insertion anomaly:** unable to add certain data without also having unrelated data (e.g., can't add a new course unless a student is enrolled, if course/student data are combined in one table).
- **Update anomaly:** having to update the same fact in multiple rows, risking inconsistency if not all are updated.
- **Deletion anomaly:** deleting a row unintentionally removes other, still-needed information.

**Q60. What is denormalization, and when would you use it?**
The deliberate introduction of redundancy (e.g., duplicating data, precomputing aggregates) into a normalized schema to improve read performance, typically for reporting/analytics/data-warehouse workloads where read speed matters more than write efficiency/storage — trading some update complexity/redundancy risk for query speed (fewer joins).

**Q61. What is a functional dependency?**
A relationship where the value of one column (or set of columns) determines the value of another — e.g., `student_id → student_name` means knowing the `student_id` uniquely determines the `student_name`. Normalization rules are defined in terms of functional dependencies.

**Q62. What is an Entity-Relationship (ER) diagram, and what are cardinality types?**
A visual representation of entities (tables), their attributes, and relationships between them. Cardinality describes the nature of relationships: **one-to-one**, **one-to-many**, and **many-to-many** (the latter typically implemented via a junction/bridge table with two foreign keys).

---

## 11. Indexes

**Q63. What is an index, and why does it improve query performance?**
A separate data structure (commonly a B-tree or B+tree) that stores a sorted reference to column values and pointers to the corresponding table rows, allowing the database to locate rows without scanning the entire table (`O(log n)` lookup instead of `O(n)` full table scan).

**Q64. What is the difference between a clustered and a non-clustered index?**
A **clustered index** determines the actual physical storage order of the table's rows — there can be only one per table (often the primary key). A **non-clustered index** is a separate structure containing indexed column values plus a pointer (row locator) back to the actual row — a table can have multiple non-clustered indexes.

**Q65. What are the trade-offs of adding indexes?**
**Pros:** dramatically faster reads/lookups/joins/sorts on indexed columns. **Cons:** additional storage space, slower writes (`INSERT`/`UPDATE`/`DELETE` must also update every relevant index), and potential for the optimizer to choose a suboptimal index if statistics are stale — indexes should be added deliberately based on actual query patterns, not indiscriminately.

**Q66. What is a composite (multi-column) index, and how does column order matter?**
An index built on multiple columns together. Column order matters significantly — the index is efficiently usable for queries filtering on a **left-prefix** of the indexed columns (e.g., an index on `(a, b, c)` efficiently supports filtering on `a`, `a+b`, or `a+b+c`, but not efficiently on `b` alone or `c` alone).

**Q67. What is a covering index?**
An index that contains *all* the columns needed to satisfy a query (both filter and selected columns), allowing the database to answer the query directly from the index without accessing the underlying table ("index-only scan") — significantly faster.

**Q68. What is the difference between a unique index and a regular index?**
A **unique index** enforces that all values in the indexed column(s) are distinct (used to implement `UNIQUE`/`PRIMARY KEY` constraints), in addition to speeding up lookups. A **regular (non-unique) index** only speeds up lookups without enforcing any uniqueness constraint.

**Q69. When might an index NOT be used by the query optimizer, even if one exists?**
When: a function/expression is applied to the indexed column in the `WHERE` clause (`WHERE UPPER(name) = 'X'` won't use a plain index on `name` unless a functional index exists); a leading wildcard is used in `LIKE '%abc'`; the optimizer estimates a full table scan is cheaper (e.g., the query would return a large fraction of the table); implicit type conversion prevents index usage; or statistics are outdated, causing poor cardinality estimates.

**Q70. What is a full-text index?**
A specialized index designed for efficient searching of natural language text within large text columns (supporting relevance ranking, word stemming, etc.), rather than exact/prefix matches like a standard B-tree index — used for search features (e.g., `MATCH ... AGAINST` in MySQL, `tsvector` in PostgreSQL).

---

## 12. Transactions & ACID

**Q71. What is a transaction?**
A sequence of one or more SQL operations executed as a single logical unit of work — either all operations succeed and are committed, or none take effect (rolled back), preserving data consistency.

**Q72. What does ACID stand for?**
- **Atomicity:** all operations in a transaction succeed or none do (all-or-nothing).
- **Consistency:** a transaction brings the database from one valid state to another, respecting all constraints/rules.
- **Isolation:** concurrently executing transactions don't interfere with each other's intermediate states (degree depends on isolation level).
- **Durability:** once committed, changes persist even in the event of a system crash (typically via write-ahead logging).

**Q73. What are `COMMIT`, `ROLLBACK`, and `SAVEPOINT`?**
`COMMIT` permanently applies all changes made during the current transaction. `ROLLBACK` undoes all changes made since the transaction began (or since a specified `SAVEPOINT`). `SAVEPOINT` marks an intermediate point within a transaction that you can roll back to, without discarding the entire transaction.

**Q74. What are the SQL standard isolation levels?**
From lowest to highest isolation (and generally lowest to highest overhead):
1. **Read Uncommitted:** transactions can see uncommitted changes from other transactions (dirty reads possible).
2. **Read Committed:** only committed data is visible; but re-reading the same row within a transaction may return different values if another transaction commits in between (non-repeatable reads possible).
3. **Repeatable Read:** guarantees the same row read twice within a transaction returns the same value; but new rows matching a query's criteria may appear on re-execution (phantom reads possible, though some RDBMS like MySQL's InnoDB largely prevent phantoms at this level too).
4. **Serializable:** transactions execute as if run one at a time, sequentially — fully prevents dirty/non-repeatable/phantom reads, at the cost of the most locking/lowest concurrency.

**Q75. What is a dirty read, non-repeatable read, and phantom read?**
- **Dirty read:** reading data written by another transaction that hasn't been committed yet (and might be rolled back).
- **Non-repeatable read:** re-reading the same row within a transaction yields different data because another transaction modified and committed a change in between.
- **Phantom read:** re-running the same query within a transaction returns a different *set* of rows because another transaction inserted/deleted rows matching the query's criteria in between.

**Q76. What is the difference between optimistic and pessimistic locking/concurrency control?**
**Pessimistic** concurrency control acquires locks upfront before accessing data, preventing other transactions from conflicting access (blocking) — assumes conflicts are likely. **Optimistic** concurrency control allows transactions to proceed without locking, but checks for conflicts (e.g., via a version number/timestamp column) before committing — if a conflict is detected, the transaction is rejected/retried — assumes conflicts are rare, better suited for high-read/low-write-contention scenarios.

---

## 13. Concurrency Control & Locking

**Q77. What is a deadlock in the context of databases, and how do RDBMS handle it?**
A deadlock occurs when two or more transactions each hold a lock the other needs, waiting indefinitely on each other. Most RDBMS run a **deadlock detection** algorithm periodically, automatically choosing one transaction as a "victim" to roll back (freeing its locks) so the others can proceed — the application should catch this and retry the rolled-back transaction.

**Q78. What is the difference between a shared (read) lock and an exclusive (write) lock?**
A **shared lock** allows multiple transactions to read the same data concurrently but prevents any of them from writing to it until all shared locks are released. An **exclusive lock** is held by a single transaction for writing, preventing any other transaction from reading or writing that data until it's released.

**Q79. What is row-level locking vs. table-level locking?**
**Row-level locking** locks only the specific rows being modified, allowing high concurrency (other transactions can still access unrelated rows). **Table-level locking** locks the entire table, blocking all other access to it — simpler but drastically reduces concurrency; modern RDBMS (InnoDB, PostgreSQL) default to row-level locking for DML.

**Q80. What is MVCC (Multi-Version Concurrency Control)?**
A concurrency control method (used by PostgreSQL, MySQL's InnoDB, Oracle) where each transaction sees a consistent "snapshot" of the database as of a certain point in time, by maintaining multiple versions of rows. This allows readers to never block writers and vice versa (readers see the pre-modification version until the writer commits), improving concurrency compared to pure lock-based approaches.

---

## 14. Views, CTEs & Stored Procedures

**Q81. What is a view?**
A virtual table defined by a stored `SELECT` query. It doesn't store data itself (unless it's a **materialized view**) — querying a view re-executes its underlying query. Useful for simplifying complex queries, enforcing security (restricting access to specific columns/rows), and providing a stable interface over changing underlying tables.

**Q82. What is the difference between a view and a materialized view?**
A regular **view** is a saved query, re-executed (against live data) each time it's queried — always up to date but potentially slower for complex queries. A **materialized view** physically stores the query's result set on disk, offering much faster reads, but the data can become stale and must be explicitly refreshed (`REFRESH MATERIALIZED VIEW`) to reflect underlying changes.

**Q83. Can you update data through a view?**
Sometimes — a view is updatable if it maps straightforwardly to a single underlying table (no aggregation, `DISTINCT`, `GROUP BY`, joins, or set operations involved, generally). Views involving joins or aggregates are typically read-only unless the RDBMS supports `INSTEAD OF` triggers to define custom update logic.

**Q84. What is a CTE (Common Table Expression), and what's its syntax?**
A named, temporary result set defined using `WITH`, scoped to a single query, improving readability by breaking complex queries into logical, named steps:
```sql
WITH dept_totals AS (
    SELECT department, SUM(salary) AS total FROM employees GROUP BY department
)
SELECT * FROM dept_totals WHERE total > 100000;
```

**Q85. What is a recursive CTE, and when is it used?**
A CTE that references itself, used to query hierarchical or graph-like data (e.g., organizational charts, bill-of-materials, category trees):
```sql
WITH RECURSIVE org_chart AS (
    SELECT id, name, manager_id, 1 AS level FROM employees WHERE manager_id IS NULL
    UNION ALL
    SELECT e.id, e.name, e.manager_id, oc.level + 1
    FROM employees e JOIN org_chart oc ON e.manager_id = oc.id
)
SELECT * FROM org_chart;
```

**Q86. What is the difference between a CTE and a subquery/derived table?**
Functionally similar in many cases, but CTEs are more readable for complex, multi-step logic; can be referenced multiple times within the same query (avoiding repeating the subquery); support recursion (subqueries cannot); however, whether a CTE's result is materialized (computed once) or inlined (like a subquery, and possibly computed per use) depends on the specific RDBMS and optimizer.

**Q87. What is a stored procedure, and what are its benefits?**
A precompiled, named collection of SQL statements (and often procedural logic — loops, conditionals, variables) stored in the database, callable by name. Benefits: reduced network round-trips (multiple statements execute server-side in one call), reusability, centralized business logic, and potential performance benefits from precompilation.

**Q88. What is the difference between a stored procedure and a function (user-defined function)?**
A **stored procedure** may or may not return a value, can perform DML/DDL, supports output parameters, and is invoked via `CALL`/`EXEC`. A **function** must return a single value (or table), is typically restricted from modifying data (side-effect-free in strict implementations), and can be used directly within SQL expressions (e.g., in a `SELECT` list or `WHERE` clause), unlike a procedure.

---

## 15. Triggers

**Q89. What is a trigger?**
A stored procedure that automatically executes in response to a specific DML event (`INSERT`, `UPDATE`, `DELETE`) on a table, either `BEFORE` or `AFTER` the event, at the row or statement level.

**Q90. What are common use cases for triggers?**
Auditing (logging changes to a history table), enforcing complex business rules that can't be expressed via simple constraints, maintaining derived/denormalized data automatically, and cascading custom logic beyond what `ON DELETE CASCADE` foreign keys provide.

**Q91. What are the potential downsides of overusing triggers?**
Hidden logic (behavior not obvious from looking at the application code, making debugging harder), potential performance overhead on every DML operation, and risk of cascading triggers causing unexpected chains of side effects that are hard to trace and test.

---

## 16. Query Optimization & Execution Plans

**Q92. What is a query execution plan?**
A breakdown (produced by the query optimizer, viewable via `EXPLAIN`/`EXPLAIN ANALYZE`) showing exactly how the database engine intends to execute a query — which indexes it will use, the join algorithm/order, estimated row counts, and cost estimates at each step.

**Q93. What is the difference between `EXPLAIN` and `EXPLAIN ANALYZE`?**
`EXPLAIN` shows the query optimizer's *planned* execution strategy with *estimated* costs/row counts, without actually running the query. `EXPLAIN ANALYZE` actually **executes** the query and reports *real* execution statistics (actual time, actual row counts) alongside the plan — useful for identifying where estimates diverge from reality (a common cause of poor performance).

**Q94. What are common join algorithms used by query optimizers?**
- **Nested Loop Join:** for each row in the outer table, scan the inner table for matches — efficient for small tables or when the inner table has a usable index.
- **Hash Join:** builds an in-memory hash table from one input, then probes it with the other — efficient for larger, unsorted datasets without useful indexes.
- **Merge Join (Sort-Merge Join):** requires both inputs sorted on the join key, then merges them in a single pass — efficient when both inputs are already sorted (e.g., via existing indexes).

**Q95. What is a full table scan, and when is it actually the optimal choice?**
Reading every row in a table sequentially, without using an index. It's optimal when a large fraction of the table's rows will be returned/processed anyway (e.g., selecting most rows, or the table is small) — in such cases, the overhead of index lookups (random I/O) can exceed the cost of a straightforward sequential scan.

**Q96. What are database statistics, and why do they matter for optimization?**
Metadata about data distribution (row counts, cardinality/distinct value estimates, histograms of value frequency) that the query optimizer uses to estimate the cost of different execution strategies and choose the best plan. Stale statistics (not updated after significant data changes) can cause the optimizer to make poor choices — most RDBMS provide commands like `ANALYZE`/`UPDATE STATISTICS` to refresh them.

**Q97. What are some general query optimization techniques?**
Select only needed columns (avoid `SELECT *`), use appropriate indexes matching common filter/join/sort columns, avoid applying functions to indexed columns in `WHERE`, use `EXISTS` instead of `IN` for large/correlated subqueries, avoid unnecessary `DISTINCT`/`ORDER BY`, batch large `INSERT`/`UPDATE` operations, use `UNION ALL` instead of `UNION` when duplicates aren't a concern, and periodically review execution plans for expensive queries.

**Q98. What is query caching, and what are its limitations?**
Some databases cache the result of a query so repeated identical queries can be served without re-execution. Limitations: cache invalidates whenever underlying data changes (frequent writes reduce cache effectiveness), memory overhead, and it doesn't help with queries that have varying parameters — many modern systems (e.g., MySQL 8+) have removed built-in query caching in favor of relying on other caching layers (application-level, buffer pool) and better indexing.

---

## 17. NULL Handling

**Q99. What does `NULL` represent in SQL, and how does it differ from zero or an empty string?**
`NULL` represents an **unknown or missing value** — it is not the same as `0` (a numeric value) or `''` (an empty string, which is a defined value of zero length). Any arithmetic or comparison operation involving `NULL` typically yields `NULL` (unknown), not `true`/`false`.

**Q100. Why does `NULL = NULL` evaluate to `NULL` (not `TRUE`)?**
Because `NULL` represents an unknown value — comparing two unknowns can't be determined to be equal or not, so the result itself is unknown (`NULL`), which is treated as false in a `WHERE` clause (excluding the row). To check for `NULL`, you must use `IS NULL` / `IS NOT NULL`, not `= NULL`.

**Q101. How do `COALESCE()` and `ISNULL()`/`NVL()` work?**
`COALESCE(a, b, c, ...)` returns the first non-`NULL` value among its arguments (standard SQL, works across most RDBMS). `ISNULL(a, default)` (SQL Server) and `NVL(a, default)` (Oracle) are vendor-specific two-argument equivalents for substituting a default when a value is `NULL`.

**Q102. How does `NULL` interact with aggregate functions and `COUNT`?**
Most aggregate functions (`SUM`, `AVG`, `MIN`, `MAX`, `COUNT(column)`) **ignore** `NULL` values entirely in their calculation — e.g., `AVG` divides by the count of non-`NULL` values, not the total row count. `COUNT(*)` is the exception — it counts all rows regardless of `NULL`s.

**Q103. Why is `NOT IN` with a subquery containing `NULL` dangerous?**
If the subquery used with `NOT IN` returns even a single `NULL` value, the entire `NOT IN` condition evaluates to `NULL`/unknown for every row, causing the query to return **zero rows** unexpectedly — a common, subtle bug. Prefer `NOT EXISTS` (or explicitly filter `WHERE col IS NOT NULL` in the subquery) to avoid this pitfall.

---

## 18. String, Date & Numeric Functions

**Q104. What are common string functions?**
`CONCAT()` (join strings), `SUBSTRING()`/`SUBSTR()` (extract a portion), `LENGTH()`/`LEN()`, `UPPER()`/`LOWER()`, `TRIM()`/`LTRIM()`/`RTRIM()` (remove whitespace), `REPLACE()`, `LPAD()`/`RPAD()` (pad to a fixed length), `POSITION()`/`INSTR()` (find substring location).

**Q105. What are common date/time functions?**
`NOW()`/`CURRENT_TIMESTAMP`, `CURDATE()`/`CURRENT_DATE`, `DATEADD()`/`DATE_ADD()` (add an interval), `DATEDIFF()` (difference between dates), `EXTRACT(part FROM date)` (pull out year/month/day/etc.), `DATE_FORMAT()`/`TO_CHAR()` (format a date as a string) — exact function names vary significantly by RDBMS.

**Q106. What is the difference between `CAST()` and `CONVERT()`?**
`CAST(expr AS type)` is standard SQL syntax for converting a value's data type, supported across all major RDBMS. `CONVERT(type, expr)` (or `CONVERT(expr, type)` depending on vendor) is a vendor-specific alternative (notably in SQL Server) that also supports additional formatting style options for date conversions.

**Q107. What is the difference between `ROUND()`, `CEILING()`/`CEIL()`, `FLOOR()`, and `TRUNCATE()`?**
`ROUND(n, d)` rounds to the nearest value at `d` decimal places (standard rounding rules). `CEILING()`/`CEIL()` rounds up to the nearest integer. `FLOOR()` rounds down to the nearest integer. `TRUNCATE()`/`TRUNC()` simply cuts off digits beyond the specified decimal place without rounding.

**Q108. What is a `CASE` expression, and what are its two forms?**
Provides conditional (if/then/else) logic within a SQL expression. **Simple form:** `CASE col WHEN val1 THEN r1 WHEN val2 THEN r2 ELSE r3 END`. **Searched form:** `CASE WHEN condition1 THEN r1 WHEN condition2 THEN r2 ELSE r3 END` (more flexible, allows arbitrary boolean conditions).

---

## 19. SQL vs NoSQL

**Q109. What is the fundamental difference between SQL (relational) and NoSQL databases?**
SQL databases use structured, predefined schemas with tables/rows/columns and relationships enforced via constraints, optimized for complex queries/joins and strong consistency (ACID). NoSQL databases use flexible/schema-less data models (document, key-value, column-family, graph), typically favoring horizontal scalability and availability, often with eventual consistency, for use cases with less-structured or rapidly-evolving data and massive scale requirements.

**Q110. What is the CAP theorem, and how does it relate to database choice?**
States that a distributed data system can only guarantee two of three properties simultaneously during a network partition: **Consistency** (all nodes see the same data at the same time), **Availability** (every request receives a response), and **Partition tolerance** (the system continues operating despite network partitions). Since partition tolerance is generally required in distributed systems, the practical trade-off is usually between consistency and availability (CP vs. AP systems) — informing choices like traditional RDBMS with replication (often CP-leaning) vs. many NoSQL systems (often AP-leaning, offering eventual consistency).

**Q111. When would you choose a NoSQL database over a relational one?**
When: the data model is naturally document/hierarchical or highly variable in structure (schema flexibility needed), extreme horizontal write scalability is required beyond what sharding a relational DB comfortably supports, the access patterns are simple key-based lookups without complex relational joins, or eventual consistency is acceptable for the use case (e.g., activity feeds, caching layers, session storage).

---

## 20. Common Query Patterns & Tricky Problems

**Q112. How do you find duplicate rows in a table?**
```sql
SELECT col1, col2, COUNT(*)
FROM my_table
GROUP BY col1, col2
HAVING COUNT(*) > 1;
```

**Q113. How do you delete duplicate rows, keeping only one copy?**
```sql
DELETE FROM my_table
WHERE id NOT IN (
    SELECT MIN(id) FROM my_table GROUP BY col1, col2
);
```
(Using a window function alternative with `ROW_NUMBER()` partitioned by the duplicate-defining columns, then deleting where `rn > 1`, is often more efficient/portable.)

**Q114. How do you find the second-highest (or Nth-highest) salary?**
```sql
-- Using OFFSET/LIMIT
SELECT DISTINCT salary FROM employees
ORDER BY salary DESC
LIMIT 1 OFFSET 1;

-- Using DENSE_RANK (handles ties, more portable)
SELECT salary FROM (
    SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employees
) t WHERE rnk = 2;
```

**Q115. How do you pivot rows into columns (and vice versa)?**
**Pivoting** (rows → columns) is typically done using conditional aggregation:
```sql
SELECT
    product,
    SUM(CASE WHEN quarter = 'Q1' THEN sales ELSE 0 END) AS Q1,
    SUM(CASE WHEN quarter = 'Q2' THEN sales ELSE 0 END) AS Q2
FROM sales_data
GROUP BY product;
```
(Some RDBMS also offer native `PIVOT`/`UNPIVOT` operators, e.g., SQL Server, Oracle.) **Unpivoting** (columns → rows) is typically done via `UNION ALL` of separate `SELECT`s, or a native `UNPIVOT` operator.

**Q116. How would you calculate a month-over-month percentage growth?**
```sql
SELECT month, revenue,
    (revenue - LAG(revenue) OVER (ORDER BY month)) * 100.0
        / LAG(revenue) OVER (ORDER BY month) AS pct_growth
FROM monthly_revenue;
```

**Q117. How do you find employees who earn more than their manager?**
```sql
SELECT e.name AS employee
FROM employees e
JOIN employees m ON e.manager_id = m.id
WHERE e.salary > m.salary;
```

**Q118. How do you write a query to identify gaps in a sequence of numbers or dates?**
```sql
SELECT id + 1 AS gap_start
FROM my_table t
WHERE NOT EXISTS (SELECT 1 FROM my_table WHERE id = t.id + 1)
ORDER BY id;
```
(A more complete "islands and gaps" solution typically combines this with `LEAD()`/`LAG()` or row-number-difference techniques to identify both the start and end of each gap/island.)

**Q119. How would you compute a median in SQL (since there's often no built-in `MEDIAN()` function)?**
Using window functions (RDBMS-dependent — some like PostgreSQL support `PERCENTILE_CONT`):
```sql
SELECT AVG(salary) AS median_salary
FROM (
    SELECT salary,
           ROW_NUMBER() OVER (ORDER BY salary) AS rn,
           COUNT(*) OVER () AS cnt
    FROM employees
) t
WHERE rn IN (FLOOR((cnt + 1) / 2.0), CEIL((cnt + 1) / 2.0));
```

**Q120. What's an efficient way to paginate large result sets, and why is `OFFSET` alone problematic at scale?**
`LIMIT n OFFSET m` requires the database to scan and discard the first `m` rows every time, which grows increasingly expensive for deep pagination (high offsets). A more scalable approach is **keyset (seek) pagination** — using a `WHERE` clause based on the last seen row's sort key (e.g., `WHERE id > :last_seen_id ORDER BY id LIMIT n`), which can leverage an index directly instead of scanning/skipping rows.

---

*This guide covers SQL fundamentals through advanced topics (window functions, query optimization, concurrency control, normalization). Pair this with hands-on practice against a real database (PostgreSQL, MySQL, or SQL Server) and platform-specific study for the RDBMS used in your target role, since exact syntax varies across vendors.*
