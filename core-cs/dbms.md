# Database Management Systems (DBMS) — Detailed Interview & Exam Notes

> **Focus:** relational modeling, SQL, normalization, transactions, concurrency, indexing, query optimization, storage, recovery, distributed databases, NoSQL, and interview preparation.

## 1. DBMS Fundamentals

A **DBMS** is software that stores, retrieves, updates, protects, and manages data while supporting concurrent users and reliable transactions.

### DBMS vs file system

| DBMS | File system |
|---|---|
| Schema and constraints | Mostly application-defined structure |
| Concurrency control | Usually application-specific |
| Transactions/ACID | Limited/manual |
| Query language | SQL or DB-specific query API |
| Indexes/optimizer | Built in |
| Recovery | Logging/checkpointing |
| Security | Users, roles, privileges |

### Data abstraction

1. **Physical level:** how bytes/pages/records are stored.
2. **Logical level:** tables, relationships, constraints.
3. **View level:** what individual users/applications see.

This supports **data independence**: physical changes should not require application-level schema changes; logical independence isolates user views from many schema changes.

## 2. Relational Model

A relation/table contains tuples/rows and attributes/columns.

Important terms:

- **Domain:** valid values for an attribute.
- **Schema:** database structure.
- **Instance:** data at a particular time.
- **Super key:** any attribute set uniquely identifying a row.
- **Candidate key:** minimal super key.
- **Primary key:** selected candidate key.
- **Foreign key:** references a key in another table.
- **Composite key:** key made from multiple columns.
- **Surrogate key:** artificial identifier such as integer/UUID.

### Integrity constraints

- **Entity integrity:** primary key cannot be null.
- **Referential integrity:** foreign-key values must reference valid parent keys, subject to nullability/action rules.
- **Domain constraints:** values must satisfy type/range/business rules.
- `UNIQUE`, `NOT NULL`, `CHECK`, `DEFAULT`, primary and foreign keys enforce common constraints.

## 3. ER Modeling

An ER model describes entities, attributes and relationships before implementation.

### Cardinality

- 1:1 — one entity maps to one.
- 1:N — one parent has many children.
- M:N — many entities relate to many entities.

M:N relationships are normally converted to a junction table:

```text
Student(student_id, name)
Course(course_id, name)
Enrollment(student_id, course_id, enrolled_at)
```

### Weak entity

A weak entity depends on a strong entity for identification. Its partial key becomes unique only together with the owner's key.

## 4. Keys and Functional Dependencies

A functional dependency `X → Y` means that equal X values imply equal Y values.

Example:

```text
employee_id → employee_name, department_id
```

### Dependency types

- **Full dependency:** depends on the whole candidate key.
- **Partial dependency:** depends on only part of a composite key.
- **Transitive dependency:** key → non-key → another non-key.

Functional dependencies are central to normalization.

## 5. Normalization

Normalization reduces redundancy and update anomalies by decomposing relations according to dependencies.

### 1NF

- Atomic values.
- No repeating groups.
- Each cell represents one value under the chosen relational model.

### 2NF

- In 1NF.
- Every non-prime attribute fully depends on the whole candidate key.
- Mainly removes partial dependency for composite keys.

### 3NF

A relation is in 3NF if for every non-trivial FD `X → A`, either X is a superkey or A is a prime attribute.

Common intuition: remove transitive dependency of non-key attributes on keys.

### BCNF

For every non-trivial FD `X → Y`, X must be a superkey.

BCNF is stricter than 3NF and can require decompositions that sacrifice some dependency preservation.

### 4NF

Addresses non-trivial multivalued dependencies.

### 5NF

Addresses join dependencies and rare decomposition anomalies.

### Practical normalization

Most transactional systems commonly aim around 3NF/BCNF, then deliberately denormalize selected read-heavy paths when measurements justify it.

## 6. Anomalies

Suppose one table stores customer, order and product data together.

- **Update anomaly:** customer address repeated across rows must be changed consistently.
- **Insert anomaly:** cannot insert a customer until an order exists.
- **Delete anomaly:** deleting the last order accidentally removes customer information.

Normalization reduces these problems.

## 7. SQL Fundamentals

### Command categories

| Category | Commands |
|---|---|
| DDL | CREATE, ALTER, DROP, TRUNCATE |
| DML | SELECT, INSERT, UPDATE, DELETE |
| DCL | GRANT, REVOKE |
| TCL | COMMIT, ROLLBACK, SAVEPOINT |

### Example schema

```sql
CREATE TABLE departments (
  id BIGINT PRIMARY KEY,
  name VARCHAR(100) NOT NULL UNIQUE
);

CREATE TABLE employees (
  id BIGINT PRIMARY KEY,
  department_id BIGINT,
  name VARCHAR(100) NOT NULL,
  salary DECIMAL(12,2) CHECK (salary >= 0),
  FOREIGN KEY (department_id) REFERENCES departments(id)
);
```

### Filtering and ordering

```sql
SELECT id, name, salary
FROM employees
WHERE salary >= 50000
ORDER BY salary DESC
LIMIT 20;
```

### NULL

`NULL` means unknown/missing, not zero or empty string.

Use:

```sql
WHERE department_id IS NULL
```

not `department_id = NULL`.

## 8. SQL Logical Query Processing

A useful conceptual order is:

```text
FROM / JOIN
→ WHERE
→ GROUP BY
→ HAVING
→ SELECT
→ DISTINCT
→ ORDER BY
→ LIMIT/OFFSET
```

This explains why a SELECT alias generally cannot be used in WHERE: WHERE is logically evaluated earlier.

## 9. Joins

| Join | Meaning |
|---|---|
| INNER | Matching rows from both sides |
| LEFT | All left + matching right |
| RIGHT | All right + matching left |
| FULL | All rows from both sides |
| CROSS | Cartesian product |
| SELF | Table joined to itself |

```sql
SELECT e.name, d.name AS department
FROM employees e
JOIN departments d ON d.id = e.department_id;
```

### LEFT JOIN trap

This preserves unmatched left rows:

```sql
SELECT e.name
FROM employees e
LEFT JOIN departments d ON d.id = e.department_id
WHERE d.name = 'Engineering';
```

The WHERE condition removes NULL right-side rows, effectively making the result behave like an inner join for that predicate. Put predicates in `ON` when preserving unmatched rows is required.

## 10. Aggregation

```sql
SELECT department_id,
       COUNT(*) AS employee_count,
       AVG(salary) AS avg_salary,
       MAX(salary) AS max_salary
FROM employees
GROUP BY department_id
HAVING AVG(salary) > 70000;
```

`WHERE` filters rows before grouping; `HAVING` filters groups after aggregation.

## 11. Subqueries and CTEs

```sql
SELECT name
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

CTE:

```sql
WITH high_earners AS (
  SELECT * FROM employees WHERE salary > 100000
)
SELECT * FROM high_earners;
```

Recursive CTEs can traverse trees/graphs where supported.

## 12. Window Functions

Window functions calculate values across related rows without collapsing them.

```sql
SELECT name, department_id, salary,
       ROW_NUMBER() OVER (
         PARTITION BY department_id ORDER BY salary DESC
       ) AS rn,
       RANK() OVER (
         PARTITION BY department_id ORDER BY salary DESC
       ) AS rnk,
       SUM(salary) OVER (
         PARTITION BY department_id
       ) AS dept_total
FROM employees;
```

- `ROW_NUMBER`: unique sequence.
- `RANK`: ties share rank; gaps follow.
- `DENSE_RANK`: ties share rank; no gaps.

## 13. Transactions and ACID

A transaction is a logical unit of work.

### ACID

- **Atomicity:** all-or-nothing.
- **Consistency:** constraints/invariants remain valid after commit.
- **Isolation:** concurrent execution is controlled to provide a defined isolation guarantee.
- **Durability:** committed state survives failures according to the database's durability design.

Example money transfer:

```text
BEGIN
  debit A
  credit B
COMMIT
```

If the transaction fails, atomicity prevents only one side from being permanently committed.

## 14. Transaction States

```text
Active → Partially committed → Committed
  |
  v
Failed → Aborted
```

A transaction can be rolled back after failure or explicit cancellation.

## 15. Concurrency Problems

### Dirty read

T2 reads data written by T1 before T1 commits.

### Non-repeatable read

T1 reads a row twice; another committed transaction changes it between reads.

### Phantom read

T1 repeats a predicate query and sees new/deleted matching rows due to another transaction.

### Lost update

Two transactions read the same old value and later overwrite each other's update.

## 16. Isolation Levels

The SQL standard defines four commonly discussed levels:

| Level | Dirty read | Non-repeatable | Phantom |
|---|---:|---:|---:|
| READ UNCOMMITTED | Possible | Possible | Possible |
| READ COMMITTED | Prevented | Possible | Possible |
| REPEATABLE READ | Prevented | Prevented | Implementation-dependent details |
| SERIALIZABLE | Prevented | Prevented | Prevented |

**Important:** exact behavior differs by database engine and implementation. Do not blindly claim that every REPEATABLE READ implementation behaves identically.

## 17. Locking

- **Shared (S) lock:** compatible with other readers depending on lock manager; blocks conflicting writes.
- **Exclusive (X) lock:** used for writes; conflicts with other S/X locks.

### Two-phase locking

- Growing phase: acquire locks.
- Shrinking phase: release locks.

Strict 2PL keeps important write locks until commit/abort, simplifying recovery and preventing many cascading effects.

## 18. MVCC

**Multi-Version Concurrency Control** keeps multiple row versions so readers can often see a consistent snapshot without blocking writers.

Conceptually:

```text
old version ← row history → new version
      ↑                       ↑
   reader snapshot         latest writer
```

MVCC does not mean “no locks”; databases still use locks for operations that require coordination.

## 19. Deadlocks in Databases

Two transactions can wait on each other's locks:

```text
T1 holds A → waits for B
T2 holds B → waits for A
```

Databases can detect the cycle and abort one transaction.

Application code should generally retry safe, idempotent transactions after a deadlock/serialization failure where appropriate.

## 20. Indexes

An index is an auxiliary data structure that speeds retrieval at the cost of storage and write overhead.

### B+ Tree

Common for ordered/range access.

```text
             [30 | 70]
            /    |    \
        [10,20] [40,60] [80,90]
             ↔ leaf links ↔
```

Supports efficient equality and range queries.

### Hash index

Good for equality lookup; generally unsuitable for ordered range scans.

### Composite index

```sql
CREATE INDEX idx_orders_customer_date
ON orders(customer_id, created_at);
```

Column order matters. A composite B-tree can efficiently serve predicates beginning with the leading columns under the engine's optimizer rules.

## 21. Selectivity and Index Design

High-selectivity predicates often benefit more from indexes, but selectivity alone does not decide everything.

Consider:

- Query frequency.
- Cardinality.
- Data distribution.
- Read/write ratio.
- Sort/group needs.
- Covering opportunities.
- Index size.

### Covering index

If an index contains all columns needed by a query, the engine may avoid fetching the base table row, depending on the engine and plan.

## 22. Query Optimization

The optimizer chooses an execution plan using statistics and cost estimates.

Possible plan decisions:

- Index scan vs sequential/table scan.
- Join order.
- Nested-loop vs hash vs merge join.
- Sort strategy.
- Parallel execution.
- Predicate pushdown.

Use `EXPLAIN` / `EXPLAIN ANALYZE` where supported to inspect plans.

### Common performance mistakes

- Missing indexes on selective join/filter paths.
- Functions preventing useful index use.
- Fetching unnecessary columns/rows.
- N+1 queries.
- Huge OFFSET pagination.
- Poor join predicates.
- Stale statistics.

## 23. Pagination

### OFFSET pagination

```sql
SELECT * FROM posts
ORDER BY id
LIMIT 20 OFFSET 100000;
```

Large offsets can require scanning/skipping many rows.

### Keyset pagination

```sql
SELECT *
FROM posts
WHERE id < :last_seen_id
ORDER BY id DESC
LIMIT 20;
```

Often scales better for deep pagination when an appropriate ordered key/index exists.

## 24. Views and Materialized Views

A **view** is a stored query definition.

A **materialized view** stores query results and must be refreshed according to the system's rules.

Use materialized views for expensive, frequently reused aggregations where freshness requirements permit it.

## 25. Stored Procedures and Triggers

Stored procedures execute database-side logic.

Triggers automatically execute on defined database events.

Advantages:

- Centralized data rules.
- Fewer network round trips for some workloads.

Risks:

- Hidden side effects.
- Harder application-level debugging.
- Portability concerns.

Use them deliberately rather than automatically.

## 26. Constraints and Cascades

Foreign keys may specify actions such as:

- `ON DELETE RESTRICT`
- `ON DELETE CASCADE`
- `ON DELETE SET NULL`

Cascade deletes are powerful and dangerous when a parent has a large dependent graph. Design them explicitly.

## 27. Storage and Pages

Most relational databases store data in fixed-size pages/blocks.

A table is organized into pages; indexes are also page-oriented structures.

This matters because query cost is often driven more by **pages read** than by the number of logical rows returned.

### Heap vs clustered organization

A heap stores rows without requiring a particular key order. A clustered organization stores/organizes table data according to an index/key scheme in engines that support it. Exact terminology varies by DBMS.

## 28. WAL and Recovery

**Write-Ahead Logging (WAL):** log records describing changes must be persisted before the corresponding durable data pages, under the database's logging protocol.

After a crash, recovery can use logs to:

- Redo committed work not yet reflected in data pages.
- Undo/ignore incomplete work depending on engine design.

### Checkpoints

Checkpoints reduce the amount of log that must be processed during recovery.

## 29. Replication

### Primary-replica

Writes go to a primary; replicas copy changes and can serve reads.

Trade-offs:

- Read scaling.
- Replication lag.
- Failover complexity.
- Read-after-write consistency requirements.

### Synchronous vs asynchronous

- **Synchronous:** commit may wait for replica acknowledgement; stronger durability/consistency but higher latency.
- **Asynchronous:** primary commits without waiting; lower latency but possible lag/data loss window during failures.

## 30. Partitioning

Partitioning divides one logical table into physical pieces.

Common strategies:

- Range
- List
- Hash

Example range partitioning by date can make old/new data management and partition pruning efficient.

Partitioning is not automatically faster. It helps when queries align with partition boundaries and operational goals.

## 31. Sharding

Sharding distributes data across independent nodes.

```text
Application
   |
   +-- shard 0: users 0..N
   +-- shard 1: users N..M
   +-- shard 2: users M..Z
```

Challenges:

- Choosing shard key.
- Hot shards.
- Cross-shard transactions.
- Rebalancing.
- Global secondary indexes.
- Operational complexity.

## 32. SQL vs NoSQL

| SQL/RDBMS | NoSQL |
|---|---|
| Strong relational model | Model varies by database |
| Rich joins/constraints | Often optimized around access patterns |
| Mature transactions | Transaction semantics vary |
| Structured schema | Often flexible schema |
| Strong ad-hoc SQL | Query model varies |

NoSQL is not “better for big data” by default. Select based on consistency, access patterns, scalability, operational model and data shape.

## 33. NoSQL Families

- **Document:** MongoDB-style JSON/BSON documents.
- **Key-value:** Redis/DynamoDB-style access.
- **Wide-column:** Cassandra-style distributed tables.
- **Graph:** Neo4j-style nodes/relationships.
- **Time-series:** optimized for timestamped measurements.

## 34. CAP Theorem

For a distributed system, during a network partition, a system must trade off between strong **Consistency** and **Availability** in the CAP sense.

- **C:** reads see a single coherent/latest value according to the chosen consistency model.
- **A:** every request to a non-failing node receives a response.
- **P:** system continues operating despite network partitions.

The important interview point: partitions are possible in distributed systems, so the practical trade-off is usually **C vs A when P occurs**, not “choose any two forever.”

## 35. BASE

Often used as a contrast to strict ACID thinking in distributed systems:

- Basically Available
- Soft state
- Eventual consistency

Eventual consistency means replicas converge if updates stop, but reads may temporarily differ.

## 36. Distributed Transactions

Two-phase commit (2PC) uses:

1. Prepare.
2. Commit/abort.

It provides coordination but can block and introduces coordinator/operational complexity. Many modern systems prefer sagas/outbox/event-driven patterns when strict distributed ACID is unnecessary.

## 37. Outbox Pattern

Write the business change and an “event to publish” record in the same local transaction. A background publisher later sends the event.

```text
DB transaction:
  update business row
  insert outbox event
       ↓
Outbox publisher → message broker
```

This reduces the dual-write problem.

## 38. Security

- Use parameterized queries/prepared statements.
- Never concatenate untrusted input into SQL.
- Apply least-privilege database accounts.
- Encrypt traffic with TLS where appropriate.
- Encrypt sensitive data at rest where required.
- Protect backups.
- Rotate credentials.
- Audit privileged operations.

### SQL injection

Bad:

```text
SELECT * FROM users WHERE name = '" + input + "'
```

Good:

```sql
SELECT * FROM users WHERE name = ?;
```

The driver binds the parameter instead of treating user input as SQL syntax.

## 39. DBMS Interview Questions

### Q1. DBMS vs RDBMS?

DBMS is the broader category. An RDBMS specifically implements a relational/table-based model with relationships and relational constraints.

### Q2. Primary key vs unique key?

A primary key identifies the row and has entity-integrity semantics. A table normally has one primary-key constraint but may have multiple unique constraints; NULL behavior for unique constraints is DBMS-specific.

### Q3. Primary key vs foreign key?

A primary key uniquely identifies a row in its table. A foreign key references a key in another table and enforces referential integrity.

### Q4. What is normalization?

Decomposition based on dependencies to reduce redundancy and update anomalies.

### Q5. 2NF vs 3NF?

2NF removes partial dependency on part of a composite key. 3NF additionally removes problematic transitive dependencies, subject to its formal definition.

### Q6. Why BCNF?

It requires every determinant of a non-trivial functional dependency to be a superkey, eliminating dependency anomalies that can remain in 3NF.

### Q7. DELETE vs TRUNCATE vs DROP?

`DELETE` removes rows and can usually filter them. `TRUNCATE` removes all rows using a more specialized operation. `DROP` removes the database object itself. Exact transaction/logging behavior is DBMS-specific.

### Q8. WHERE vs HAVING?

WHERE filters rows before grouping; HAVING filters groups after aggregation.

### Q9. INNER vs LEFT JOIN?

INNER returns matches. LEFT preserves every left row and fills unmatched right columns with NULL.

### Q10. Why use indexes?

To reduce the work needed for frequent filters, joins, ordering or grouping, trading additional storage and write/update cost.

### Q11. Why not index every column?

Indexes consume storage and make inserts/updates/deletes more expensive; some indexes provide little benefit for low-selectivity or rarely queried data.

### Q12. B-tree vs hash index?

B-tree supports ordered traversal and range queries; hash is optimized primarily for equality lookup.

### Q13. What is ACID?

Atomicity, Consistency, Isolation and Durability.

### Q14. What is MVCC?

A concurrency-control technique that keeps multiple row versions so readers can use snapshots while writers proceed, subject to the engine's semantics.

### Q15. Dirty vs non-repeatable vs phantom read?

Dirty reads uncommitted data; non-repeatable reads see a changed existing row; phantom reads see a changed set of rows matching a predicate.

### Q16. What is a deadlock?

Transactions form a cycle of lock/resource waits. The DBMS can detect and abort one transaction.

### Q17. What is WAL?

A logging technique where change information is made durable before corresponding durable data changes, enabling crash recovery.

### Q18. Replication vs sharding?

Replication copies data across nodes; sharding partitions data across nodes.

### Q19. What is a covering index?

An index containing all columns needed by a query, allowing the engine to answer it from the index in suitable cases.

### Q20. Why can OFFSET pagination become slow?

The database may need to locate/skip many earlier rows before returning the requested page. Keyset pagination can avoid deep offsets.

### Q21. What is CAP?

During a network partition, a distributed system must trade off consistency and availability under the CAP definitions.

### Q22. How do you prevent SQL injection?

Use parameterized queries/prepared statements, avoid string concatenation, validate inputs, and use least-privilege accounts.

### Q23. Why can a query ignore an index?

The optimizer may estimate that a table scan is cheaper, the predicate may be non-selective, statistics may be stale, or the query expression may not match the index effectively.

### Q24. How would you optimize a slow query?

Measure first: inspect execution plan, row counts, selectivity, indexes, joins, sort/group operations, statistics and I/O. Change one thing and benchmark again.

### Q25. How would you design a high-volume order database?

Start with normalized transactional tables, appropriate composite indexes, immutable/order identifiers, transaction boundaries, read replicas where useful, partitioning by measured access patterns, and an outbox for asynchronous downstream events. Introduce sharding only when scale requires it.

## 40. Revision Checklist

- [ ] Relational model and keys
- [ ] ER diagrams/cardinality
- [ ] Functional dependencies
- [ ] 1NF/2NF/3NF/BCNF/4NF/5NF
- [ ] SQL DDL/DML/TCL/DCL
- [ ] NULL semantics
- [ ] Joins/grouping/subqueries/CTEs
- [ ] Window functions
- [ ] ACID and transaction states
- [ ] Isolation anomalies and levels
- [ ] Locks/2PL/MVCC
- [ ] Deadlocks
- [ ] B-tree/hash/composite indexes
- [ ] Query plans and EXPLAIN
- [ ] Pagination
- [ ] WAL/recovery/checkpoints
- [ ] Replication/partitioning/sharding
- [ ] CAP/eventual consistency
- [ ] NoSQL families
- [ ] Security/SQL injection
- [ ] Production design scenarios
