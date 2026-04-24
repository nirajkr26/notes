# DBMS — Super Detailed Notes for Exams & Interviews

---

## 1) DBMS Fundamentals

A **DBMS** is software that stores, organizes, and retrieves data while ensuring consistency, security, and concurrency.

### Why DBMS over files?
- Reduced redundancy
- Better consistency
- Multi-user concurrency
- Security and authorization
- Backup and recovery
- Data independence

### Data independence
- **Physical independence:** storage changes do not affect logical schema
- **Logical independence:** logical changes minimally affect applications

---

## 2) Database Architecture

### ANSI/SPARC 3-level architecture
1. External level (views)
2. Conceptual level (logical schema)
3. Internal level (physical storage)

### DBMS components
- Query processor
- Storage manager
- Transaction manager
- Recovery manager
- Authorization manager

---

## 3) Data Models

- Relational
- Hierarchical
- Network
- Object-oriented
- NoSQL (document/key-value/column/graph)

---

## 4) Keys and Constraints

### Key types
- Super key
- Candidate key
- Primary key
- Alternate key
- Composite key
- Foreign key
- Surrogate key

### Constraints
- NOT NULL
- UNIQUE
- PRIMARY KEY
- FOREIGN KEY
- CHECK
- DEFAULT

---

## 5) SQL Master Section

### 5.1 SQL categories
- DDL
- DML
- DCL
- TCL

### 5.2 Core commands (must know)
- CREATE, ALTER, DROP, TRUNCATE
- INSERT, UPDATE, DELETE, SELECT
- COMMIT, ROLLBACK, SAVEPOINT
- GRANT, REVOKE

### 5.3 SELECT execution order
`FROM -> JOIN -> WHERE -> GROUP BY -> HAVING -> SELECT -> DISTINCT -> ORDER BY -> LIMIT`

### 5.4 Joins
- INNER
- LEFT
- RIGHT
- FULL OUTER
- CROSS
- SELF

### 5.5 Subqueries
- Scalar
- Correlated
- EXISTS/NOT EXISTS

### 5.6 Window functions
- ROW_NUMBER, RANK, DENSE_RANK
- LEAD/LAG
- SUM/AVG OVER(PARTITION BY)

---

## 6) ER Modeling and Mapping

### ER basics
- Entity, attribute, relationship
- Strong and weak entities
- Cardinality (1:1, 1:N, M:N)
- Participation (total/partial)

### Mapping to relations
- 1:1 via FK in one table
- 1:N via FK on N-side
- M:N via junction table

---

## 7) Relational Algebra and Calculus

### Algebra operations
- Selection (sigma)
- Projection (pi)
- Union, intersection, difference
- Cartesian product
- Join
- Division

### Why asked in exams?
Foundational for query processing and optimization.

---

## 8) Normalization (Very Important)

### Anomalies
- Insertion anomaly
- Update anomaly
- Deletion anomaly

### Functional dependency terms
- Full dependency
- Partial dependency
- Transitive dependency

### Normal forms
- 1NF: atomic values
- 2NF: no partial dependency
- 3NF: no transitive dependency
- BCNF: every determinant is candidate key
- 4NF: no non-trivial multivalued dependency
- 5NF: no non-trivial join dependency

### Denormalization
Used for read-heavy workloads and analytics.

---

## 9) Transactions and ACID

### ACID
- Atomicity
- Consistency
- Isolation
- Durability

### Transaction states
Active -> partially committed -> committed
or
Active -> failed -> aborted

### Concurrency anomalies
- Dirty read
- Non-repeatable read
- Phantom read
- Lost update

### Isolation levels
- Read uncommitted
- Read committed
- Repeatable read
- Serializable

---

## 10) Concurrency Control

### Locking
- Shared lock (S)
- Exclusive lock (X)
- Intention locks (for hierarchy)

### Two-phase locking (2PL)
- Growing phase
- Shrinking phase
- Strict 2PL for recoverability

### Timestamp ordering
Transaction order enforced by timestamps.

### MVCC
Multiple row versions improve read-write concurrency.

---

## 11) Deadlocks in DBMS

### Necessary condition
Circular wait in lock graph.

### Handling
- Prevention
- Avoidance
- Detection (wait-for graph)
- Recovery (abort victim)

---

## 12) Storage and File Organization

- Heap files
- Sequential files
- Hash files
- Clustered storage

### Record and page concepts
- Blocking factor
- Spanned vs unspanned records

---

## 13) Indexing and Hashing

### Index categories
- Primary/secondary
- Clustered/non-clustered
- Dense/sparse
- Composite
- Bitmap
- Full-text

### B-tree vs B+ tree
- B+ tree: all data at leaves, linked leaves for range scans
- Standard in most RDBMS engines

### Hash indexing
Fast equality search, poor for range conditions.

---

## 14) Query Processing and Optimization

### Query processing phases
1. Parsing and translation
2. Optimization
3. Execution

### Heuristic optimizations
- Push selections early
- Push projections early
- Choose best join order

### Cost-based optimization
Uses table stats, cardinality estimates, and index selectivity.

### `EXPLAIN` usage
Analyze scan type, join strategy, and estimated cost.

---

## 15) Recovery System

### Log-based recovery
- Write-Ahead Logging (WAL)
- Undo/redo logs

### Checkpoints
Reduce recovery time by flushing consistent metadata points.

### Shadow paging
Alternative recovery mechanism used in some systems.

---

## 16) Distributed Databases and NoSQL

### CAP theorem
Pick at most two among Consistency, Availability, Partition tolerance simultaneously.

### SQL vs NoSQL
| Aspect | SQL | NoSQL |
|---|---|---|
| Schema | Fixed | Flexible |
| Scaling | Vertical | Horizontal |
| Transactions | Strong ACID | Often eventual consistency |
| Joins | Native | Usually limited |

### BASE model
Basically available, soft state, eventually consistent.

---

## 17) Practical Design and Tuning

- Correct datatype selection
- PK/FK constraints from day one
- Proper indexing strategy
- Avoid over-indexing writes-heavy tables
- Partitioning and sharding for scale
- Backup strategy and restore testing

---

## 18) High-Frequency Interview Questions

1. ACID vs BASE?
2. 2NF, 3NF, BCNF with examples?
3. Clustered vs non-clustered index?
4. How does MVCC work?
5. Explain deadlock handling in databases.
6. Difference between WHERE and HAVING?
7. Why is B+ tree preferred?
8. Explain query plan analysis.
9. DELETE vs TRUNCATE vs DROP?
10. How would you design schema for an e-commerce app?

---

## 19) Exam-Focused Topics

- Normalization-based decomposition problems
- Relational algebra conversions
- ACID and transaction state diagram
- Isolation anomalies with examples
- Index choice for given query workload
- Recovery numericals with logs/checkpoints

---

## 20) Last-Minute Revision Checklist

- [ ] Keys + constraints
- [ ] SQL categories and join types
- [ ] Normal forms up to BCNF
- [ ] ACID + isolation anomalies
- [ ] Locking, 2PL, MVCC
- [ ] Deadlock graph concepts
- [ ] B+ tree and hashing
- [ ] Query optimization basics
- [ ] Recovery and WAL
- [ ] CAP theorem + SQL vs NoSQL

---

*Last updated: 2026 | Target: university exams, GATE, and technical interviews*
