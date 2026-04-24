# Database Management Systems (DBMS) — Interview & Exam Notes

---

## 1. Introduction / Overview

A **Database Management System (DBMS)** is software that enables users to create, read, update, and delete data in a structured way, while ensuring data integrity, security, and concurrent access.

**Advantages over File Systems:**
- Eliminates data redundancy and inconsistency
- Enforces data integrity constraints
- Provides concurrent access with ACID guarantees
- Centralized security and access control
- Data independence (logical & physical)

**Types of DBMS:**

| Type            | Description                           | Examples              |
|-----------------|---------------------------------------|-----------------------|
| Relational (RDBMS)| Data in tables with relationships   | MySQL, PostgreSQL, Oracle |
| Document        | Semi-structured (JSON/BSON documents) | MongoDB, CouchDB      |
| Key-Value       | Simple key→value pairs                | Redis, DynamoDB       |
| Column-family   | Wide-column store                     | Cassandra, HBase      |
| Graph           | Nodes and edges                       | Neo4j, Amazon Neptune |
| Time-series     | Timestamped data                      | InfluxDB, TimescaleDB |

---

## 2. Key Concepts & Terminology

| Term            | Definition                                                       |
|-----------------|------------------------------------------------------------------|
| Table/Relation  | 2D structure with rows and columns                               |
| Tuple/Row       | A single record in a table                                       |
| Attribute/Column| A field in a table                                               |
| Domain          | Set of permissible values for an attribute                       |
| Schema          | Structure/design of a database                                   |
| Instance        | Actual data stored at a given moment                             |
| Primary Key     | Uniquely identifies each row; NOT NULL + UNIQUE                  |
| Foreign Key     | References primary key in another table; enforces referential integrity |
| Candidate Key   | Any attribute/set that can uniquely identify a tuple             |
| Super Key       | Superset of candidate key (may have extra attributes)            |
| Composite Key   | Primary key consisting of multiple attributes                    |
| Surrogate Key   | Artificially generated key (e.g., auto-increment ID)             |
| Null            | Unknown or missing value (NOT same as 0 or empty string)         |

---

## 3. ACID Properties

> **ACID** guarantees reliable transaction processing.

| Property    | Description                                                        |
|-------------|-------------------------------------------------------------------|
| **Atomicity**   | Transaction is all-or-nothing; if any part fails, entire transaction rolls back |
| **Consistency** | Database moves from one valid state to another; all constraints satisfied |
| **Isolation**   | Concurrent transactions execute as if sequential; one doesn't see intermediate state of another |
| **Durability**  | Committed transactions persist even after system failure (stored on non-volatile storage) |

**Implementation:**
- Atomicity → **Undo logs / rollback**
- Durability → **Redo logs / write-ahead logging (WAL)**
- Isolation → **Locking / MVCC (Multi-Version Concurrency Control)**
- Consistency → **Integrity constraints, triggers**

---

## 4. SQL — Structured Query Language

### 4.1 SQL Command Categories

| Category | Commands                               | Description                     |
|----------|----------------------------------------|---------------------------------|
| **DDL**  | CREATE, ALTER, DROP, TRUNCATE, RENAME  | Define/modify schema            |
| **DML**  | SELECT, INSERT, UPDATE, DELETE         | Manipulate data                 |
| **DCL**  | GRANT, REVOKE                          | Access control                  |
| **TCL**  | COMMIT, ROLLBACK, SAVEPOINT            | Transaction management          |

### 4.2 Basic SQL Syntax

```sql
-- Create a table
CREATE TABLE Employees (
    emp_id    INT PRIMARY KEY AUTO_INCREMENT,
    name      VARCHAR(100) NOT NULL,
    dept_id   INT,
    salary    DECIMAL(10,2),
    FOREIGN KEY (dept_id) REFERENCES Departments(dept_id)
);

-- Insert data
INSERT INTO Employees (name, dept_id, salary)
VALUES ('Alice', 1, 75000.00);

-- Query data
SELECT name, salary
FROM Employees
WHERE salary > 50000
ORDER BY salary DESC
LIMIT 10;

-- Update data
UPDATE Employees
SET salary = salary * 1.10
WHERE dept_id = 2;

-- Delete data
DELETE FROM Employees
WHERE emp_id = 5;
```

### 4.3 Aggregate Functions

```sql
SELECT COUNT(*), AVG(salary), MAX(salary), MIN(salary), SUM(salary)
FROM Employees;

-- GROUP BY with HAVING
SELECT dept_id, AVG(salary) AS avg_salary
FROM Employees
GROUP BY dept_id
HAVING AVG(salary) > 60000;
```

**Order of SQL clause execution:**  
`FROM` → `JOIN` → `WHERE` → `GROUP BY` → `HAVING` → `SELECT` → `DISTINCT` → `ORDER BY` → `LIMIT`

### 4.4 Joins

```
TableA:  id | name       TableB:  id | dept
         1  | Alice               1  | HR
         2  | Bob                 3  | IT
         3  | Carol
```

| Join Type     | Returns                                                       |
|---------------|---------------------------------------------------------------|
| INNER JOIN    | Matching rows in both tables                                  |
| LEFT JOIN     | All rows from left + matched from right (NULL if no match)    |
| RIGHT JOIN    | All rows from right + matched from left (NULL if no match)    |
| FULL OUTER JOIN| All rows from both, NULLs for non-matching sides            |
| CROSS JOIN    | Cartesian product (every row × every row)                     |
| SELF JOIN     | Table joined with itself                                      |

```sql
-- INNER JOIN: Alice(1), Carol(3)
SELECT E.name, D.dept
FROM Employees E
INNER JOIN Departments D ON E.id = D.id;

-- LEFT JOIN: Alice(1), Bob(2→NULL), Carol(3)
SELECT E.name, D.dept
FROM Employees E
LEFT JOIN Departments D ON E.id = D.id;
```

**Visual representation:**
```
INNER JOIN:  A ∩ B
LEFT JOIN:   A (all) + A ∩ B
RIGHT JOIN:  B (all) + A ∩ B
FULL JOIN:   A ∪ B
```

### 4.5 Subqueries & CTEs

```sql
-- Subquery (correlated)
SELECT name FROM Employees
WHERE salary > (SELECT AVG(salary) FROM Employees);

-- CTE (Common Table Expression)
WITH HighEarners AS (
    SELECT * FROM Employees WHERE salary > 80000
)
SELECT name FROM HighEarners WHERE dept_id = 1;
```

### 4.6 Window Functions

```sql
-- ROW_NUMBER, RANK, DENSE_RANK
SELECT name, salary,
    ROW_NUMBER() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS row_num,
    RANK()       OVER (PARTITION BY dept_id ORDER BY salary DESC) AS rank,
    DENSE_RANK() OVER (PARTITION BY dept_id ORDER BY salary DESC) AS dense_rank
FROM Employees;
```

| Function      | Gaps in ranking? | Notes                          |
|---------------|-----------------|--------------------------------|
| ROW_NUMBER()  | — (unique)      | Always unique sequence         |
| RANK()        | Yes             | Ties share rank; next skipped  |
| DENSE_RANK()  | No              | Ties share rank; no gaps       |

---

## 5. Normalization

> Process of organizing a database to reduce **data redundancy** and improve **data integrity** by decomposing tables.

### 5.1 Functional Dependency (FD)

`A → B` means: knowing A determines B uniquely.  
- **Partial Dependency:** Non-key attribute depends on part of a composite primary key  
- **Transitive Dependency:** A → B → C (non-key depends on another non-key)

### 5.2 Normal Forms

| NF   | Rule                                                                    | Removes                  |
|------|-------------------------------------------------------------------------|--------------------------|
| **1NF** | Atomic values; no repeating groups; each column has unique name      | Repeating groups         |
| **2NF** | 1NF + No partial dependencies (every non-key attr depends on whole PK) | Partial dependencies   |
| **3NF** | 2NF + No transitive dependencies                                      | Transitive dependencies  |
| **BCNF**| 3NF + For every FD X→Y, X must be a superkey                         | FD anomalies from non-superkey |
| **4NF** | BCNF + No multi-valued dependencies                                   | Multi-valued dependencies|
| **5NF** | 4NF + No join dependencies                                            | Join anomalies           |

**Practical tip:** Most production databases target **3NF** or **BCNF**.

### 5.3 Normalization Example

**Unnormalized (0NF):**
```
OrderID | Customer | Items          | Prices
1       | Alice    | Pen, Book      | 10, 50
```

**1NF (atomic values):**
```
OrderID | Customer | Item | Price
1       | Alice    | Pen  | 10
1       | Alice    | Book | 50
```

**2NF (no partial dependency — assume PK = {OrderID, Item}):**
```
Orders:  OrderID | Customer
         1       | Alice

OrderItems: OrderID | Item | Price
            1       | Pen  | 10
            1       | Book | 50
```

**3NF (no transitive dependency):**  
Split further if Customer info depends on CustomerID, not OrderID.

### 5.4 Denormalization
> Intentionally introducing redundancy to improve **read performance** (fewer joins).  
> Common in data warehouses (OLAP) and high-read applications.

---

## 6. Transactions and Concurrency

### 6.1 Transaction States

```
Active → Partially Committed → Committed
   └──────────────────────────► Failed → Aborted (Rolled Back)
```

### 6.2 Concurrency Problems

| Problem              | Description                                                   |
|----------------------|---------------------------------------------------------------|
| **Dirty Read**       | Transaction reads data written by uncommitted transaction     |
| **Non-repeatable Read**| Same query returns different results within one transaction |
| **Phantom Read**     | New rows added by another transaction appear in re-execution  |
| **Lost Update**      | Two transactions read then update; one overwrites the other   |

### 6.3 Isolation Levels

| Isolation Level   | Dirty Read | Non-repeatable Read | Phantom Read | Notes              |
|-------------------|-----------|--------------------|--------------|--------------------|
| READ UNCOMMITTED  | ✅ Possible| ✅ Possible        | ✅ Possible  | Lowest isolation   |
| READ COMMITTED    | ❌ Prevented| ✅ Possible       | ✅ Possible  | Default in many DBs|
| REPEATABLE READ   | ❌ Prevented| ❌ Prevented      | ✅ Possible  | Default in MySQL   |
| SERIALIZABLE      | ❌ Prevented| ❌ Prevented      | ❌ Prevented | Highest isolation  |

### 6.4 Locking

- **Shared Lock (S):** Multiple transactions can read; no write while held  
- **Exclusive Lock (X):** Only one transaction can hold; blocks reads & writes  
- **Two-Phase Locking (2PL):** Growing phase (acquire locks) → Shrinking phase (release locks)  
  - Ensures serializability  
  - **Strict 2PL:** Release all locks after commit/abort  
  - **Deadlock** possible in 2PL

### 6.5 MVCC (Multi-Version Concurrency Control)
- Maintain multiple versions of data  
- Readers don't block writers; writers don't block readers  
- Used in: PostgreSQL, Oracle, MySQL InnoDB

---

## 7. Indexing

> An **index** is a data structure that speeds up data retrieval at the cost of additional storage and write overhead.

### 7.1 Types of Indexes

| Type             | Description                                          |
|------------------|------------------------------------------------------|
| **Primary Index**| On ordered key field; one entry per block            |
| **Secondary Index**| On non-ordering field; dense or sparse             |
| **Clustered**    | Data physically sorted by index key; one per table   |
| **Non-clustered**| Separate structure with pointers; multiple allowed   |
| **Composite**    | Index on multiple columns                            |
| **Unique**       | Enforces uniqueness on indexed column(s)             |
| **Full-text**    | For text search (inverted index)                     |
| **Bitmap**       | Bit array per value; good for low-cardinality columns|

### 7.2 B-Tree and B+ Tree

**B+ Tree (most common in RDBMS):**
- All data records at leaf nodes  
- Leaf nodes linked (efficient range queries)  
- Balanced; O(log n) search, insert, delete  
- Used by: MySQL (InnoDB), PostgreSQL

```
        [30 | 70]
       /    |    \
   [10|20] [40|60] [80|90]
    ↓↓↓↓   ↓↓↓↓    ↓↓↓↓
(Leaf nodes contain actual data, linked together)
```

**Hash Index:**
- O(1) average lookup; no range queries  
- Used for equality conditions only

### 7.3 When to Index?

**Index:** columns used in `WHERE`, `JOIN`, `ORDER BY`, `GROUP BY`, foreign keys  
**Avoid indexing:** small tables, columns with high update frequency, very low cardinality (e.g., boolean)

---

## 8. ER Model (Entity-Relationship)

### 8.1 ER Diagram Notation

```
Rectangle    → Entity (e.g., Student)
Ellipse      → Attribute
Double Ellipse → Multivalued Attribute
Dashed Ellipse → Derived Attribute
Diamond      → Relationship
Double Rectangle → Weak Entity
Double Diamond → Identifying Relationship
```

### 8.2 Cardinality

```
1:1   One-to-One     (one person → one passport)
1:N   One-to-Many    (one department → many employees)
M:N   Many-to-Many   (students ↔ courses)
```

### 8.3 Keys in ER Model

- **Partial Key:** Attribute that partially identifies a weak entity (depends on strong entity)  
- **Discriminator:** The partial key of a weak entity

---

## 9. Relational Algebra

> Formal query language for relational databases.

| Operation     | Symbol | SQL Equivalent                  |
|---------------|--------|---------------------------------|
| Selection     | σ      | WHERE clause                    |
| Projection    | π      | SELECT (columns)                |
| Union         | ∪      | UNION                           |
| Intersection  | ∩      | INTERSECT                       |
| Difference    | −      | EXCEPT / MINUS                  |
| Cartesian Product | × | CROSS JOIN                      |
| Join          | ⋈      | JOIN                            |
| Rename        | ρ      | AS                              |

---

## 10. Stored Procedures, Views, and Triggers

### Views
```sql
-- Create a view
CREATE VIEW HighSalaryEmployees AS
SELECT name, salary FROM Employees WHERE salary > 80000;

-- Query the view
SELECT * FROM HighSalaryEmployees;
```
- Virtual table; does not store data (unless materialized)  
- **Materialized View:** stores the result; needs refreshing

### Stored Procedures
```sql
DELIMITER //
CREATE PROCEDURE GetEmployeesByDept(IN dept INT)
BEGIN
    SELECT * FROM Employees WHERE dept_id = dept;
END //
DELIMITER ;

CALL GetEmployeesByDept(1);
```
- Pre-compiled; reduced network round trips; reusable logic

### Triggers
```sql
CREATE TRIGGER before_salary_update
BEFORE UPDATE ON Employees
FOR EACH ROW
BEGIN
    IF NEW.salary < 0 THEN
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Salary cannot be negative';
    END IF;
END;
```
- Automatically fired on INSERT/UPDATE/DELETE events  
- Types: BEFORE / AFTER; row-level / statement-level

---

## 11. NoSQL vs SQL

| Feature          | SQL (RDBMS)                        | NoSQL                              |
|------------------|------------------------------------|------------------------------------|
| Schema           | Fixed schema                       | Dynamic / schema-less              |
| Data model       | Relational (tables)                | Document, Key-Value, Graph, Column |
| Scalability      | Vertical (scale up)                | Horizontal (scale out)             |
| ACID             | Full ACID                          | Often BASE (eventual consistency)  |
| Query language   | SQL (standardized)                 | Varies per DB                      |
| Joins            | Supported                          | Generally not supported            |
| Use cases        | Financial, ERP, CRM systems        | Big data, real-time, flexible data |

**CAP Theorem:**  
> A distributed system can only guarantee **two of three** simultaneously:
- **C**onsistency — every read receives the latest write  
- **A**vailability — every request receives a (non-error) response  
- **P**artition Tolerance — system works despite network partitions

```
        Consistency
           /  \
          /    \
Availability - Partition Tolerance
(choose 2 of 3)
```

**BASE (NoSQL):**  
**B**asically Available, **S**oft state, **E**ventually consistent — contrast to ACID.

---

## 12. Query Optimization

### 12.1 Query Execution Plan
- Use `EXPLAIN` (MySQL/PostgreSQL) to see how DB executes a query  
- Look for: full table scans, missing indexes, expensive joins

### 12.2 Optimization Tips

- Use indexes on filter/join columns  
- Avoid `SELECT *`; specify only needed columns  
- Use `LIMIT` for large result sets  
- Avoid functions on indexed columns in `WHERE` (prevents index use)  
- Prefer `JOIN` over correlated subqueries  
- Use query caching where applicable  
- Partition large tables  

---

## 13. Database Design Best Practices

- Design entity-relationship diagram before coding  
- Choose appropriate data types (avoid oversized types)  
- Apply normalization up to 3NF/BCNF  
- Define primary/foreign keys explicitly  
- Use constraints (NOT NULL, CHECK, UNIQUE)  
- Add indexes on frequently queried columns  
- Plan for partitioning/sharding at scale  
- Implement proper backup and recovery strategy  

---

## 14. Real-world Use Cases

| Concept           | Real-world Example                                            |
|-------------------|---------------------------------------------------------------|
| Normalization     | Banking database keeping customer & account tables separate   |
| Indexing (B+ Tree)| MySQL index on `email` column for fast user lookup            |
| Transactions/ACID | Bank transfer: debit one account, credit another atomically   |
| Stored Procedures | Complex business logic in ERP systems                         |
| NoSQL (MongoDB)   | E-commerce product catalog with varying attributes            |
| Redis (Key-Value) | Session storage, caching, rate limiting                       |
| Cassandra         | Netflix viewing history (high write, distributed)             |
| CAP Theorem       | Google Spanner (CP), Amazon DynamoDB (AP)                     |

---

## 15. Frequently Asked Questions (FAQs)

**Q1. What are ACID properties?**  
> Atomicity (all-or-nothing), Consistency (valid state), Isolation (concurrent = sequential), Durability (committed = permanent).

**Q2. What is the difference between DELETE, TRUNCATE, and DROP?**  
> `DELETE`: DML; removes specific rows; rollback possible; fires triggers; slow.  
> `TRUNCATE`: DDL; removes all rows; faster; no rollback (usually); resets auto-increment.  
> `DROP`: DDL; removes entire table structure + data; cannot be rolled back.

**Q3. What is normalization? Why do we do it?**  
> Normalization organizes tables to reduce redundancy and dependency. It prevents update/insert/delete anomalies.

**Q4. What is the difference between primary key and unique key?**  
> Primary key: cannot be NULL; only one per table. Unique key: can have one NULL; multiple allowed per table. Both enforce uniqueness.

**Q5. Explain the difference between clustered and non-clustered indexes.**  
> Clustered index: data is physically sorted by the index. Non-clustered: separate structure with row pointers. A table can have only one clustered index.

**Q6. What is a deadlock in DBMS?**  
> When two or more transactions wait for each other to release locks, causing all to be stuck. Resolved by detection (timeout/wait-for graph) and aborting one transaction.

**Q7. What is a view? When would you use it?**  
> A view is a virtual table defined by a query. Used to simplify complex queries, restrict access to sensitive columns, and present a consistent interface.

**Q8. Explain the difference between HAVING and WHERE.**  
> `WHERE` filters rows before grouping. `HAVING` filters groups after `GROUP BY`. Aggregate functions can be used in `HAVING` but not in `WHERE`.

**Q9. What is the CAP theorem?**  
> A distributed system can guarantee at most two of: Consistency, Availability, Partition Tolerance. In practice, partition tolerance is required, so the choice is C or A.

**Q10. What is the difference between INNER JOIN and OUTER JOIN?**  
> INNER JOIN returns only matching rows. OUTER JOIN (LEFT/RIGHT/FULL) returns matching rows plus non-matching rows from one or both tables (with NULLs for missing matches).

---

## 16. Common Misconceptions

- ❌ *"TRUNCATE can always be rolled back"* → In MySQL, TRUNCATE is DDL and cannot be rolled back; in PostgreSQL, it can be (inside a transaction).  
- ❌ *"NULL == NULL is true"* → `NULL = NULL` evaluates to UNKNOWN. Use `IS NULL` to check for null.  
- ❌ *"More indexes = faster queries"* → Too many indexes slow down INSERT/UPDATE/DELETE operations.  
- ❌ *"Normalization is always better"* → Over-normalized schemas require complex joins; denormalization is appropriate for analytics (OLAP).  
- ❌ *"Foreign keys automatically create indexes"* → MySQL (InnoDB) does create an index; other databases may not.

---

## 17. Quick Revision Checklist

- [ ] ACID properties and implementation  
- [ ] SQL commands: DDL, DML, DCL, TCL  
- [ ] All join types with diagrams  
- [ ] Aggregate functions + GROUP BY + HAVING  
- [ ] Normal forms (1NF through BCNF)  
- [ ] Functional dependencies  
- [ ] Transaction isolation levels + concurrency problems  
- [ ] Index types (clustered vs non-clustered, B+tree, hash)  
- [ ] DELETE vs TRUNCATE vs DROP  
- [ ] CAP theorem  
- [ ] SQL vs NoSQL trade-offs  
- [ ] ER diagram symbols  
- [ ] Stored procedures, views, triggers  
- [ ] Query optimization (EXPLAIN, index tips)  
- [ ] Locks (shared/exclusive) and 2PL  

---

*Last updated: 2026 | Suitable for: GATE, university exams, software engineering interviews*
