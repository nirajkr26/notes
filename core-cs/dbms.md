# Database Management Systems (DBMS) — Complete Detailed Notes

> **Goal:** Deep, interview-ready and exam-ready DBMS notes covering theory, internals, SQL, concurrency, recovery, indexing, optimization, distributed databases, and practical system design.

---

# Table of Contents

1. DBMS Fundamentals
2. Database Architecture and Data Independence
3. Relational Model and Constraints
4. ER Modeling and ER-to-Relational Mapping
5. Relational Algebra and Calculus
6. Functional Dependencies, Attribute Closure, and Keys
7. Normalization and Decomposition
8. SQL: Fundamentals to Advanced
9. Transactions and ACID
10. Schedules, Serializability, and Recoverability
11. Concurrency Control, Locks, 2PL, Timestamp Ordering, and MVCC
12. Deadlocks and Isolation Levels
13. Storage, Pages, Records, and Buffer Management
14. Indexing: B/B+ Trees, Hashing, and Composite Indexes
15. Query Processing, Join Algorithms, and Optimization
16. WAL, Recovery, and Checkpoints
17. Replication, Partitioning, Sharding, and Distributed Transactions
18. CAP, BASE, and NoSQL
19. Security and Production Database Design
20. Interview Questions and Revision Checklist

---

# 1. DBMS Fundamentals

A **Database Management System (DBMS)** is software responsible for storing, organizing, retrieving, updating, protecting, and recovering data.

A DBMS solves several difficult problems that applications would otherwise need to solve themselves:

- persistent storage,
- concurrent access,
- query execution,
- data integrity,
- authorization,
- transaction atomicity,
- crash recovery,
- indexing,
- backup and restore.

## 1.1 Database vs DBMS

- **Database:** organized collection of data.
- **DBMS:** software that manages that collection.

Example:

    Database = users, products, orders, payments
    DBMS     = PostgreSQL/MySQL/Oracle managing the data

## 1.2 DBMS vs file system

A file system can store bytes but does not automatically provide database-level guarantees.

| DBMS | File system |
|---|---|
| Declarative query language | Application manually reads files |
| Schema and constraints | Mostly application-defined |
| Transactions | Manual coordination |
| Concurrency control | Usually custom |
| Indexes | Application responsibility |
| Recovery | DBMS logging/checkpointing |
| Authorization | Database users/roles |
| Query optimizer | Application decides algorithms |

### Why redundancy is dangerous

Suppose customer address is repeated in every order:

    Order 1 -> customer address = X
    Order 2 -> customer address = X
    Order 3 -> customer address = X

Changing the address requires updating every copy. Missing one produces inconsistency.

A normalized design stores the fact once:

    Customer(id, address)
    Order(id, customer_id)

## 1.3 DBMS vs RDBMS

DBMS is a broad category.

An **RDBMS** primarily follows the relational model:

- data represented as relations/tables,
- rows represented as tuples,
- columns represented as attributes,
- relationships represented with keys and constraints.

## 1.4 Core responsibilities

A DBMS must coordinate several layers:

    Application
        |
       SQL
        |
    Parser / Rewriter
        |
    Query Optimizer
        |
    Execution Engine
        |
    Transaction / Concurrency Manager
        |
    Buffer / Storage Manager
        |
    Data Pages + Indexes + WAL
        |
      Disk / SSD

The query is only the visible surface. A production DBMS is a large system coordinating correctness and performance underneath.

---

# 2. Database Architecture and Data Independence

The classic ANSI/SPARC model has three levels.

## 2.1 External or view level

What a specific user or application sees.

Example:

    HR sees:
    Employee(id, name, salary)

    Manager sees:
    Employee(id, name, department)

Views can hide sensitive data.

## 2.2 Conceptual or logical level

Describes the logical structure:

    Employee(id, name, department_id)
    Department(id, name)

It focuses on what information exists and how it is related.

## 2.3 Internal or physical level

Describes storage details:

- files,
- pages,
- record layout,
- indexes,
- partitions,
- compression.

Diagram:

    +---------------------------+
    | External / View Schema    |
    +---------------------------+
                 |
    +---------------------------+
    | Conceptual / Logical      |
    +---------------------------+
                 |
    +---------------------------+
    | Internal / Physical       |
    +---------------------------+

## 2.4 Physical data independence

Ability to change physical storage without changing the logical schema or application.

Example:

    Before: no index
    After:  B+ tree index

Application SQL can remain unchanged.

## 2.5 Logical data independence

Ability to change logical design while minimizing changes required by external views/applications.

Example:

    Old:
    Employee(id, full_name)

    New:
    Employee(id, first_name, last_name)

A compatibility view may preserve an older interface.

Logical data independence is generally harder than physical data independence.

## 2.6 Schema vs instance

**Schema** is the structure.

Example:

    Employee(id, name, salary)

**Instance** is actual data at a specific moment.

    1 | Niraj | 90000
    2 | Alice | 85000

Schema changes infrequently. Instances change continuously.

---

# 3. Relational Model and Integrity Constraints

A relation is conceptually represented as a table.

    Employee
    +----+-------+---------+
    | id | name  | dept_id |
    +----+-------+---------+
    | 1  | Niraj | 10      |
    | 2  | Alice | 20      |
    +----+-------+---------+

## 3.1 Terminology

- **Relation:** table.
- **Tuple:** row.
- **Attribute:** column.
- **Domain:** permitted set/type of values.
- **Degree:** number of attributes.
- **Cardinality:** number of tuples.
- **Relation schema:** table definition.
- **Relation instance:** current rows.

## 3.2 Keys

### Superkey

Any set of attributes that uniquely identifies a row.

For:

    Student(id, email, name)

Possible superkeys:

    {id}
    {email}
    {id, name}
    {email, name}

The last two are unique but not minimal.

### Candidate key

A minimal superkey.

If id alone uniquely identifies every row, id is a candidate key.

### Primary key

The candidate key selected as the main row identifier.

### Alternate key

Candidate keys not chosen as primary key.

### Composite key

Key containing multiple attributes:

    Enrollment(student_id, course_id)

Primary key:

    (student_id, course_id)

### Foreign key

References a key in another relation.

    Employee.department_id
            |
            v
    Department.id

### Surrogate key

Artificial identifier:

- sequence/integer,
- auto-increment,
- UUID.

### Natural key

Business attribute used as identifier.

Example:

    ISO country code

Natural keys are useful when truly stable and unique, but business rules can change.

## 3.3 Integrity constraints

### Domain constraints

Examples:

    salary >= 0
    age between 0 and 150

### Entity integrity

Primary-key values cannot be NULL.

### Referential integrity

A foreign key must reference a valid parent key unless its definition allows NULL.

### Business constraints

Examples:

    end_date > start_date
    quantity >= 0
    order_total >= 0

Constraints can be enforced using:

- NOT NULL,
- UNIQUE,
- CHECK,
- PRIMARY KEY,
- FOREIGN KEY,
- triggers,
- application logic.

Critical invariants are often safest when enforced at the database layer as well.

---

# 4. ER Modeling and ER-to-Relational Mapping

The Entity-Relationship model is used to model the domain before implementing tables.

## 4.1 Entity

A distinguishable real-world object:

- Student,
- Course,
- Employee,
- Order.

## 4.2 Attributes

### Simple vs composite

    Name
     |- first_name
     |- last_name

### Single-valued vs multivalued

Single-valued:

    date_of_birth

Multivalued:

    phone_numbers

A relational model usually converts multivalued attributes into a separate relation.

### Stored vs derived

Stored:

    date_of_birth

Derived:

    age

Age is normally derived from date of birth rather than stored, because stored age becomes stale.

## 4.3 Relationships

    Student ---- ENROLLS_IN ---- Course

Relationships can also contain attributes:

    Enrollment:
      enrolled_at
      grade
      status

## 4.4 Cardinality

### One-to-one

    Person 1 ---- 1 Passport

### One-to-many

    Department 1 ---- N Employee

### Many-to-many

    Student M ---- N Course

Convert M:N into an associative relation:

    Enrollment(student_id, course_id, enrolled_at)

## 4.5 Participation

**Total participation:** every entity must participate.

Example:

    Every dependent belongs to an employee.

**Partial participation:** relationship is optional.

Example:

    An employee may not have an assigned parking space.

## 4.6 Weak entity

A weak entity cannot be uniquely identified without an owner.

Example:

    Employee(employee_id)
    Dependent(dependent_name, age)

Dependent key may be:

    (employee_id, dependent_name)

## 4.7 Generalization and specialization

Generalization:

    Student
    Teacher
       |
       v
    Person

Specialization:

    Employee
      |- Engineer
      |- Manager

Important questions:

- disjoint or overlapping?
- total or partial specialization?

## 4.8 Mapping ER to relations

### Strong entity

    Student(id, name, email)

### 1:N relationship

Place parent key on many side:

    Department(id)
    Employee(id, department_id)

### M:N relationship

Create a new table:

    Student(id)
    Course(id)

    Enrollment(
      student_id,
      course_id,
      grade
    )

### Multivalued attribute

    Employee(id)
    EmployeePhone(employee_id, phone)

### Weak entity

    Employee(employee_id)
    Dependent(employee_id, dependent_name, age)

---

# 5. Relational Algebra and Relational Calculus

Relational algebra is procedural: it describes operations.

Important operators:

    sigma  = selection
    pi     = projection
    union  = set union
    minus  = set difference
    times  = Cartesian product
    join   = join
    rho    = rename

## 5.1 Selection

Choose rows satisfying a predicate.

    sigma salary > 50000 (Employee)

Equivalent SQL idea:

    SELECT *
    FROM Employee
    WHERE salary > 50000;

## 5.2 Projection

Choose attributes:

    pi name, salary (Employee)

## 5.3 Union

    R union S

Requires compatible schemas/domains.

## 5.4 Set difference

    R - S

Rows in R but not S.

## 5.5 Cartesian product

    R x S

Every row of R paired with every row of S.

A join can conceptually be understood as:

    Cartesian product
        +
    filtering matching pairs

## 5.6 Join

Theta join:

    R join_condition S

Natural join matches same-named attributes automatically.

Be careful with natural joins because adding a same-named column later can unexpectedly change behavior.

## 5.7 Division

Used for “related to all”.

Example:

    Enrollment(student, course)
    /
    RequiredCourse(course)

Result:

    students enrolled in every required course

## 5.8 Relational calculus

Relational calculus is declarative.

General tuple form:

    { t | P(t) }

Meaning:

    all tuples t for which P(t) is true

This is conceptually closer to SQL's declarative nature: SQL specifies what is desired, while the DBMS decides how to execute it.

---

# 6. Functional Dependencies, Attribute Closure, and Keys

A functional dependency:

    X -> Y

means:

> If two tuples have the same X value, they must also have the same Y value.

Example:

    employee_id -> employee_name, department_id

## 6.1 Determinant

In:

    X -> Y

X is the determinant.

## 6.2 Full functional dependency

Y depends on all of X and no proper subset of X determines Y.

Example:

    (student_id, course_id) -> grade

Grade belongs to the enrollment combination.

## 6.3 Partial dependency

A non-key attribute depends on part of a composite key.

Example:

    student_id -> student_name

If table key is:

    (student_id, course_id)

then student_name has a partial dependency.

## 6.4 Transitive dependency

    A -> B
    B -> C

Therefore:

    A -> C

If A is a key and B/C are non-key attributes, normalization may be needed.

## 6.5 Trivial dependency

    X -> Y

is trivial if:

    Y is a subset of X

Example:

    (A, B) -> A

## 6.6 Attribute closure

Given attributes X and functional dependencies F, X+ contains all attributes derivable from X.

Algorithm:

1. Start with X+ = X.
2. Find Y -> Z where Y is contained in X+.
3. Add Z.
4. Repeat until no attributes can be added.

Example:

    R(A, B, C, D, E)

FDs:

    A -> B
    B -> C
    AC -> D
    D -> E

Find A+:

    {A}
    A -> B      => {A, B}
    B -> C      => {A, B, C}
    AC -> D     => {A, B, C, D}
    D -> E      => {A, B, C, D, E}

Therefore A determines every attribute and is a superkey.

Since A has no smaller non-empty subset, A is a candidate key.

## 6.7 Finding candidate keys

Useful process:

1. Find attributes that never appear on the RHS of non-trivial dependencies.
2. These usually must be included in every candidate key.
3. Compute closure.
4. Add attributes until all relation attributes are derived.
5. Remove unnecessary attributes to ensure minimality.

---

# 7. Armstrong's Axioms

Armstrong's axioms are sound and complete inference rules for functional dependencies.

## 7.1 Reflexivity

If Y is a subset of X:

    X -> Y

Example:

    (A, B) -> A

## 7.2 Augmentation

If:

    X -> Y

then:

    XZ -> YZ

## 7.3 Transitivity

If:

    X -> Y
    Y -> Z

then:

    X -> Z

## 7.4 Derived union rule

If:

    X -> Y
    X -> Z

then:

    X -> YZ

## 7.5 Decomposition

If:

    X -> YZ

then:

    X -> Y
    X -> Z

## 7.6 Pseudotransitivity

If:

    X -> Y
    WY -> Z

then:

    WX -> Z

These are important for normalization proofs and FD reasoning.

---

# 8. Normalization and Decomposition

Normalization reduces unnecessary redundancy and anomalies.

## 8.1 Update anomaly

Repeated data must be updated in many rows.

## 8.2 Insert anomaly

A fact cannot be stored until an unrelated fact exists.

## 8.3 Delete anomaly

Deleting one fact accidentally removes another independent fact.

## 8.4 First Normal Form (1NF)

Values should be represented according to the chosen relational model without repeating groups.

Bad:

    Student
    id | name | phones
    1  | A    | 111,222

Better:

    Student(id, name)
    StudentPhone(student_id, phone)

## 8.5 Second Normal Form (2NF)

Requirements:

1. Relation is in 1NF.
2. Non-prime attributes do not depend on a proper subset of a candidate key.

Example:

    Enrollment(
      student_id,
      course_id,
      student_name,
      course_name,
      grade
    )

Key:

    (student_id, course_id)

Dependencies:

    student_id -> student_name
    course_id -> course_name
    (student_id, course_id) -> grade

Decompose:

    Student(student_id, student_name)
    Course(course_id, course_name)
    Enrollment(student_id, course_id, grade)

## 8.6 Third Normal Form (3NF)

For every non-trivial dependency:

    X -> A

at least one must be true:

1. X is a superkey.
2. A is a prime attribute.

A common intuition is removal of problematic transitive dependencies.

Example:

    Employee(
      employee_id,
      department_id,
      department_name
    )

Dependencies:

    employee_id -> department_id
    department_id -> department_name

Decompose:

    Employee(employee_id, department_id)
    Department(department_id, department_name)

## 8.7 BCNF

For every non-trivial dependency:

    X -> Y

X must be a superkey.

BCNF is stricter than 3NF.

Trade-off:

- less redundancy,
- but dependency preservation may be harder.

## 8.8 4NF

Addresses multivalued dependencies.

Example:

    Person(person, hobby, language)

If hobbies and languages are independent:

    PersonHobby(person, hobby)
    PersonLanguage(person, language)

## 8.9 5NF

Addresses rare join-dependency anomalies.

It is less common in everyday application design but important conceptually.

## 8.10 Lossless decomposition

A decomposition should reconstruct exactly the original relation.

No:

- information loss,
- spurious tuples.

For binary decomposition, a useful criterion is that the common attributes functionally determine one decomposed relation.

## 8.11 Dependency preservation

A dependency-preserving decomposition allows important FDs to be enforced without joining decomposed relations.

This is why practical normalization often balances BCNF against dependency preservation.

---

# 9. SQL Fundamentals

## 9.1 DDL

Examples:

    CREATE
    ALTER
    DROP
    TRUNCATE

## 9.2 DML

Examples:

    SELECT
    INSERT
    UPDATE
    DELETE

## 9.3 DCL

    GRANT
    REVOKE

## 9.4 TCL

    COMMIT
    ROLLBACK
    SAVEPOINT

## 9.5 Example schema

    CREATE TABLE departments (
      id BIGINT PRIMARY KEY,
      name VARCHAR(100) NOT NULL UNIQUE
    );

    CREATE TABLE employees (
      id BIGINT PRIMARY KEY,
      department_id BIGINT,
      name VARCHAR(100) NOT NULL,
      email VARCHAR(255) NOT NULL UNIQUE,
      salary DECIMAL(12,2) CHECK (salary >= 0),
      FOREIGN KEY (department_id)
        REFERENCES departments(id)
    );

## 9.6 INSERT

    INSERT INTO employees
      (id, department_id, name, email, salary)
    VALUES
      (1, 10, 'Niraj', 'niraj@example.com', 90000);

## 9.7 SELECT

    SELECT id, name, salary
    FROM employees
    WHERE salary >= 50000
    ORDER BY salary DESC;

Avoid SELECT * in long-lived production queries unless every column is actually needed.

## 9.8 NULL

NULL does not mean zero or empty string.

Wrong:

    WHERE department_id = NULL

Correct:

    WHERE department_id IS NULL

SQL commonly uses three-valued logic:

    TRUE
    FALSE
    UNKNOWN

Therefore:

    NULL = NULL

is generally UNKNOWN, not TRUE.

---

# 10. SQL Logical Processing

A useful conceptual order:

    FROM / JOIN
    -> ON
    -> WHERE
    -> GROUP BY
    -> HAVING
    -> SELECT
    -> DISTINCT
    -> ORDER BY
    -> LIMIT/OFFSET

This is logical processing order, not necessarily physical execution order.

It explains why SELECT aliases generally cannot be referenced by WHERE.

---

# 11. Joins, Aggregation, Subqueries, CTEs, and Windows

## 11.1 INNER JOIN

Returns matching rows.

    SELECT e.name, d.name AS department
    FROM employees e
    JOIN departments d
      ON d.id = e.department_id;

## 11.2 LEFT JOIN

Preserves every left row.

    SELECT e.name, d.name
    FROM employees e
    LEFT JOIN departments d
      ON d.id = e.department_id;

Unmatched right-side values become NULL.

## 11.3 LEFT JOIN trap

This:

    SELECT e.name
    FROM employees e
    LEFT JOIN departments d
      ON d.id = e.department_id
    WHERE d.name = 'Engineering';

removes NULL right-side rows and behaves like an inner join for that predicate.

If unmatched employees must remain, predicate placement in ON may be appropriate:

    LEFT JOIN departments d
      ON d.id = e.department_id
     AND d.name = 'Engineering'

## 11.4 Aggregation

    SELECT department_id,
           COUNT(*) AS employee_count,
           AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department_id
    HAVING AVG(salary) > 70000;

WHERE filters rows before grouping.

HAVING filters groups after aggregation.

## 11.5 EXISTS

Useful when testing whether related data exists.

    SELECT c.*
    FROM customers c
    WHERE EXISTS (
      SELECT 1
      FROM orders o
      WHERE o.customer_id = c.id
    );

## 11.6 Correlated subquery

References outer query values.

    SELECT e.*
    FROM employees e
    WHERE salary > (
      SELECT AVG(e2.salary)
      FROM employees e2
      WHERE e2.department_id = e.department_id
    );

The optimizer may rewrite this into another plan.

## 11.7 CTE

    WITH high_earners AS (
      SELECT *
      FROM employees
      WHERE salary > 100000
    )
    SELECT *
    FROM high_earners;

Whether a CTE is materialized or inlined depends on DBMS and optimizer behavior.

## 11.8 Recursive CTE

Useful for:

- organization hierarchies,
- categories,
- trees,
- dependency graphs.

## 11.9 Window functions

Unlike GROUP BY, window functions do not collapse rows.

    SELECT name,
           department_id,
           salary,
           ROW_NUMBER() OVER (
             PARTITION BY department_id
             ORDER BY salary DESC
           ) AS row_num,
           RANK() OVER (
             PARTITION BY department_id
             ORDER BY salary DESC
           ) AS rank_num
    FROM employees;

### ROW_NUMBER

Unique sequence.

### RANK

Ties share rank and gaps occur.

    100 -> 1
    100 -> 1
    90  -> 3

### DENSE_RANK

No gaps:

    100 -> 1
    100 -> 1
    90  -> 2

### Running total

    SUM(amount) OVER (
      ORDER BY created_at
    )

---

# 12. Transactions and ACID

A transaction is a logical unit of work.

Example bank transfer:

    BEGIN
      debit account A
      credit account B
    COMMIT

## Atomicity

All-or-nothing.

## Consistency

Successful transaction preserves database invariants.

Important:

ACID consistency is not identical to CAP consistency.

## Isolation

Concurrent execution follows a defined isolation guarantee.

## Durability

Committed changes survive failure according to the DBMS durability protocol.

---

# 13. Transaction States

Typical state model:

    Active
      |
      v
    Partially Committed
      |
      +--> Committed

    Active
      |
      v
    Failed
      |
      v
    Aborted

A transaction may be restarted after rollback.

---

# 14. Schedules and Serializability

A schedule is an ordering/interleaving of operations from multiple transactions.

## 14.1 Serial schedule

    T1 completely
    then
    T2 completely

## 14.2 Serializable schedule

Concurrent execution equivalent to some serial execution.

## 14.3 Conflicting operations

Two operations conflict when:

1. different transactions,
2. same data item,
3. at least one is write.

Therefore:

    Read/Read   -> no conflict
    Read/Write  -> conflict
    Write/Read  -> conflict
    Write/Write -> conflict

## 14.4 Conflict serializability

Build precedence graph.

If T1's conflicting operation occurs before T2's:

    T1 -> T2

If graph is acyclic, schedule is conflict serializable.

Cycle:

    T1 -> T2
    ^      |
    |______|

means not conflict serializable.

A topological ordering gives an equivalent serial order.

---

# 15. Recoverability

## Recoverable schedule

If T2 reads data written by T1, T2 must not commit before T1 commits.

Otherwise T2 could commit based on data that later disappears.

## Cascading rollback

    T1 writes X
    T2 reads uncommitted X
    T1 aborts
    T2 may need abort

## Cascadeless schedule

Transactions read only committed values.

## Strict schedule

No transaction may read or overwrite a value written by an uncommitted transaction.

Strict schedules simplify recovery.

---

# 16. Concurrency Anomalies

## Lost update

Initial:

    balance = 100

T1 reads 100.

T2 reads 100.

T1 writes 90.

T2 writes 80.

T1's update is lost.

## Dirty read

T1 writes an uncommitted value.

T2 reads it.

T1 rolls back.

T2 observed data that never committed.

## Non-repeatable read

T1 reads:

    salary = 50000

T2 updates and commits:

    salary = 70000

T1 reads again and sees different value.

## Phantom

T1 runs:

    WHERE amount > 1000

T2 inserts matching row and commits.

T1 reruns query and sees additional row.

## Write skew

Two transactions independently read a shared invariant and update different rows, jointly violating the invariant.

Important in snapshot-based systems.

---

# 17. Lock-Based Concurrency Control

## Shared lock (S)

For reading.

Multiple compatible readers can often hold S locks simultaneously.

## Exclusive lock (X)

For writing.

Conflicts with S and X.

Basic compatibility:

    Requested/Held | S | X
    ---------------+---+---
    S              | Y | N
    X              | N | N

Real DBMS lock managers may support more modes:

- intention locks,
- update locks,
- predicate/range locks.

## Lock upgrade

    S -> X

Can itself participate in deadlocks.

---

# 18. Two-Phase Locking (2PL)

## Growing phase

Acquire locks; do not release.

## Shrinking phase

Release locks; do not acquire new locks.

    Acquire -> Acquire -> Acquire
                          |
                          v
                    Release -> Release

2PL ensures conflict serializability.

## Strict 2PL

Write locks are retained until commit or abort.

Benefits:

- prevents dirty reads of locked writes,
- avoids many cascading rollbacks,
- simplifies recovery.

## Conservative/static 2PL

Acquire all required locks before execution.

Can prevent deadlocks but reduces flexibility and requires knowing resources in advance.

---

# 19. Timestamp Ordering

Each transaction receives a timestamp.

Operations are allowed only if they preserve required timestamp order.

If an operation would violate ordering, a transaction may be aborted/restarted.

Important variants:

- basic timestamp ordering,
- Thomas write rule,
- multiversion timestamp ordering.

---

# 20. MVCC

**Multi-Version Concurrency Control** stores multiple versions of logical rows.

Example:

    Version 1 -> salary = 50000
    Version 2 -> salary = 60000
    Version 3 -> salary = 70000

A transaction's snapshot determines which version is visible.

Benefits:

- readers often avoid blocking writers,
- consistent snapshots,
- improved concurrency.

Important:

> MVCC does not mean no locks.

Conflicting writers and schema/transaction operations still require coordination.

## Version cleanup

Old versions are removed once no active transaction needs them.

Names vary:

- vacuum,
- purge,
- garbage collection.

---

# 21. Isolation Levels

Common SQL levels:

    Level             Dirty  Non-repeatable  Phantom
    READ UNCOMMITTED  Yes    Yes             Yes
    READ COMMITTED    No     Yes             Yes
    REPEATABLE READ   No     No              implementation-dependent
    SERIALIZABLE      No     No              No

Exact behavior varies across DBMS products.

## READ UNCOMMITTED

May permit dirty reads.

## READ COMMITTED

Each statement sees committed data according to DBMS semantics.

## REPEATABLE READ

Repeated reads get stronger stability, but details vary.

## SERIALIZABLE

Result equivalent to some serial execution.

Serializable does not necessarily mean:

    one transaction physically runs at a time

Implementations may use:

- locking,
- serializable snapshot isolation,
- validation,
- predicate/range locking,
- transaction abort/retry.

---

# 22. Deadlocks

Example:

    T1 holds A, waits for B
    T2 holds B, waits for A

Wait-for graph:

    T1 -> T2
    ^      |
    |______|

## Detection

DBMS detects cycle and chooses a victim.

## Prevention

Useful application technique:

Always acquire resources in a consistent order.

Example:

    lower account_id first
    higher account_id second

instead of arbitrary ordering.

## Recovery

Victim transaction is rolled back.

Applications should safely handle:

- deadlock errors,
- serialization failures,
- transient transaction conflicts.

Retry logic should be idempotent or otherwise safe.

---

# 23. Storage: Pages, Records, and Buffer Management

Databases generally manage persistent data in pages/blocks.

    Database file
      |- Page 1
      |- Page 2
      |- Page 3

Each page contains records.

## Why pages matter

Cost depends heavily on:

- page reads,
- page writes,
- cache hits,
- sequential vs random I/O.

## Record layout

Conceptually:

    Record Header
    NULL bitmap
    Fixed-length fields
    Variable-length offsets
    Variable-length data

Exact format is DBMS-specific.

## Heap storage

Rows are not necessarily maintained in key order.

Good for flexible insertion.

## Clustered storage

Rows are organized around an ordering/index structure.

Exact meaning of clustered index differs by DBMS.

---

# 24. Buffer Pool

The buffer manager caches pages in memory.

    Disk/SSD
       |
       v
    Buffer Pool
       |
       v
    Query Execution

When a page is needed:

1. Search buffer pool.
2. Cache hit -> use memory copy.
3. Cache miss -> load page.

A modified page is often called a **dirty page** until written to durable storage.

Replacement may resemble:

- LRU,
- CLOCK,
- adaptive policies.

The DBMS must balance caching:

- table pages,
- index pages,
- metadata,
- temporary working data.

---

# 25. Indexing

An index is an auxiliary access structure.

Trade-off:

    Faster lookup
        vs
    More storage + slower writes

Every modification can require index maintenance.

## Dense index

Entry for every search-key/record pattern.

## Sparse index

Entries for selected points/pages, requiring scan from a nearby location.

## Clustered vs non-clustered

Clustered organization affects physical row order/layout.

Non-clustered index is a separate structure referencing table rows.

A table generally cannot have multiple physical orderings even if products expose multiple index concepts.

---

# 26. B-Tree and B+ Tree

Balanced multiway trees keep height small.

    [30 | 70]
      / |   [10] [50] [90]

Nodes are often page-sized.

## B-tree

Internal and leaf nodes may contain data references.

## B+ tree

Common conceptual design:

- internal nodes guide navigation,
- leaves contain searchable entries,
- leaves are linked.

    [internal routing]
          |
          v
    10 <-> 20 <-> 40 <-> 60 <-> 80

Linked leaves make range scans efficient.

Search complexity intuition:

    O(log_B N)

B is large because nodes hold many keys.

---

# 27. Hash Indexes

Hash function:

    hash(key) -> bucket

Excellent for equality:

    WHERE id = 10

Not naturally ideal for ordered ranges:

    WHERE id BETWEEN 10 AND 20

Dynamic hashing techniques include:

- extendible hashing,
- linear hashing.

---

# 28. Composite Indexes

Example:

    CREATE INDEX idx_orders_customer_date
    ON orders(customer_id, created_at);

Column order matters.

This can align well with:

    WHERE customer_id = ?
    ORDER BY created_at

A query filtering only created_at may not benefit equivalently from this ordering.

Exact optimizer behavior varies.

## Covering index

If index contains all required query information, database may avoid fetching base-table rows.

Whether an actual index-only scan occurs depends on DBMS visibility and plan details.

---

# 29. Query Processing

Typical pipeline:

    SQL
     |
    Parse
     |
    Semantic Analysis
     |
    Rewrite
     |
    Logical Plan
     |
    Cost-Based Optimization
     |
    Physical Plan
     |
    Execution

A query is declarative.

Optimizer chooses physical operations such as:

- table scan,
- index scan,
- nested loop,
- hash join,
- merge join,
- sort,
- aggregation.

---

# 30. Query Optimization

Optimizer estimates cost using:

- table statistics,
- cardinality estimates,
- histograms,
- selectivity,
- index availability,
- memory,
- join alternatives.

## Index scan vs table scan

Index is not always faster.

If query returns most rows:

    Sequential table scan

may be cheaper than:

    Index lookup
      +
    millions of random row fetches

## Join ordering

For:

    A JOIN B JOIN C

Different orders may have very different cost:

    (A JOIN B) JOIN C

versus:

    A JOIN (B JOIN C)

## Predicate pushdown

Apply filters early:

    Filter
      |
      v
    Join fewer rows

instead of:

    Join everything
      |
      v
    Filter later

## Projection pruning

Avoid carrying unnecessary columns through expensive operators.

---

# 31. Join Algorithms

## Nested-loop join

Concept:

    for each outer row:
        find matching inner rows

Very effective when:

- outer relation is small,
- inner side has useful index.

## Hash join

1. Build hash table from one input.
2. Probe with other.

Good for large equality joins.

## Merge join

Inputs sorted on join key.

Scan both in order.

Useful when data is already ordered or sorting is worthwhile.

---

# 32. EXPLAIN and Query Tuning

Use EXPLAIN and, where supported, EXPLAIN ANALYZE.

Look at:

- estimated rows,
- actual rows,
- loops,
- index usage,
- sort operations,
- disk spills,
- execution time.

A dangerous mismatch:

    estimated = 100 rows
    actual    = 1,000,000 rows

can cause bad join choices.

Possible causes:

- stale statistics,
- skew,
- correlated columns,
- inaccurate assumptions.

## Tuning workflow

1. Measure.
2. Capture execution plan.
3. Compare estimates with actuals.
4. Identify largest expensive operator.
5. Check indexes/statistics/query shape.
6. Change one thing.
7. Benchmark again.

Avoid guessing.

---

# 33. Pagination

## OFFSET

    SELECT *
    FROM posts
    ORDER BY id DESC
    LIMIT 20 OFFSET 100000;

Deep offsets may become expensive.

## Keyset/cursor pagination

    SELECT *
    FROM posts
    WHERE id < :last_seen_id
    ORDER BY id DESC
    LIMIT 20;

Often scales better.

Use deterministic ordering, commonly including a unique tie-breaker.

---

# 34. WAL and Recovery

Failures include:

- transaction failure,
- process crash,
- OS crash,
- power loss,
- storage failure.

Recovery uses logs to reason about committed and incomplete work.

## REDO

Reapply a change.

Useful when committed work was not yet reflected in data pages.

## UNDO

Reverse incomplete work.

## Write-Ahead Logging

Core idea:

> Required recovery log information becomes durable before corresponding data-page changes are allowed to become durable.

Conceptually:

    1. Write log record
    2. Ensure required log durability
    3. Data page can flush later

WAL supports:

- atomicity,
- durability,
- crash recovery.

---

# 35. Checkpoints and ARIES Concepts

Without checkpoints, recovery may need excessive log processing.

A checkpoint records useful recovery state.

    [old log] ---- [checkpoint] ---- [crash]

Recovery can begin with useful checkpoint information.

ARIES-style recovery is commonly summarized as:

    Analysis
      |
      v
    Redo
      |
      v
    Undo

Important ideas:

- repeating history during redo,
- undoing loser transactions,
- compensation log records.

Not every DBMS implements ARIES exactly.

---

# 36. Replication

Replication copies data across nodes.

    Client writes
         |
         v
      Primary
       /         v     v
   Replica Replica

Benefits:

- read scaling,
- redundancy,
- failover.

## Synchronous replication

Commit may wait for replica acknowledgement.

Trade-off:

    stronger replication guarantees
    vs
    higher latency

## Asynchronous replication

Primary commits before replicas necessarily catch up.

Trade-off:

    lower latency
    vs
    lag / possible failure loss window

Important application question:

> After a write, can the user read from a lagging replica and temporarily not see their own write?

---

# 37. Partitioning

Partitioning divides a logical table into physical pieces.

## Range

Example:

    orders_2025
    orders_2026
    orders_2027

## List

Example:

    India
    USA
    Europe

## Hash

Hash key determines partition.

Useful for even distribution.

## Partition pruning

Query:

    WHERE created_at >= start
      AND created_at < end

may access only relevant partitions.

Partitioning is not automatically faster.

Bad partition key can cause:

- hot partitions,
- unnecessary scans,
- operational complexity.

---

# 38. Sharding

Sharding distributes data across independent nodes.

    Application
       |
    +--+--+--+
    |  |  |  |
   S0 S1 S2 S3

## Shard key

Examples:

- tenant_id,
- customer_id,
- region.

Good shard key:

- distributes load,
- avoids hotspots,
- aligns with common queries.

Challenges:

- cross-shard joins,
- distributed transactions,
- rebalancing,
- global indexes,
- operational complexity.

Sharding is usually a later scaling step after:

1. query optimization,
2. indexing,
3. vertical scaling,
4. replicas,
5. partitioning.

---

# 39. Distributed Transactions

## Two-Phase Commit (2PC)

### Prepare phase

Coordinator asks participants:

    Can you commit?

Participants prepare and respond.

### Decision phase

Coordinator sends:

    COMMIT

or:

    ABORT

Trade-offs:

- coordination latency,
- failure complexity,
- possible blocking.

## Sagas

Distributed workflow uses local transactions and compensating actions.

    Reserve inventory
       |
       v
    Charge payment
       |
       v
    Create shipment

Failure may trigger:

    Refund payment
    Release inventory

Compensation is business-specific and may not perfectly reverse all external effects.

---

# 40. CAP Theorem

CAP concerns distributed systems when network partitions occur.

- **Consistency:** operations satisfy a coherent consistency model.
- **Availability:** every request to a non-failing node receives a response.
- **Partition tolerance:** system continues despite communication partitions.

Correct interview intuition:

> During a network partition, a distributed system must trade off consistency and availability under CAP definitions.

Avoid simplistic statement:

    choose any two forever

because real distributed systems normally must tolerate possible partitions.

---

# 41. BASE and Eventual Consistency

BASE commonly expands to:

- Basically Available,
- Soft state,
- Eventual consistency.

Eventual consistency means replicas converge if updates stop, but reads may temporarily return different versions.

Modern systems often mix:

- strong consistency for critical paths,
- asynchronous/eventual mechanisms for other paths.

---

# 42. NoSQL Databases

NoSQL is a collection of models.

## Key-value

    key -> value

Use cases:

- cache,
- sessions,
- simple high-speed lookup.

## Document

JSON-like nested documents.

Useful for flexible and naturally hierarchical data.

## Wide-column

Designed for distributed high-throughput workloads.

## Graph

Nodes and edges.

Useful for:

- social networks,
- recommendations,
- dependency traversal.

## Time-series

Optimized for timestamped observations:

- metrics,
- sensors,
- market data.

Choose database based on:

- access pattern,
- consistency,
- data shape,
- scalability,
- operational needs.

---

# 43. Security

## SQL injection

Bad:

    "SELECT * FROM users WHERE name = '" + input + "'"

Correct:

    parameterized/prepared query

Example:

    SELECT *
    FROM users
    WHERE name = ?;

Parameters are data, not SQL syntax.

## Least privilege

Application accounts should only receive required permissions.

Avoid using database superuser credentials in application servers.

## Encryption

Consider:

- TLS in transit,
- encryption at rest,
- encrypted backups,
- secure key management.

## Sensitive data

Use:

- role-based access,
- auditing,
- retention rules,
- masking/tokenization where appropriate.

## Backups

A backup strategy must answer:

- How often are backups created?
- What is RPO?
- What is RTO?
- Are restores tested?
- Are backups protected?

A backup that has never been restored successfully is not fully trusted.

---

# 44. Production Database Design Workflow

## Step 1: Understand requirements

What facts exist?

What operations are common?

What invariants must never break?

## Step 2: Identify entities

Example:

    User
    Product
    Order
    Payment

## Step 3: Identify relationships

    User -> Order
    Order -> Payment
    Order <-> Product

## Step 4: Choose keys

Natural vs surrogate based on stability and business meaning.

## Step 5: Define constraints

Use:

- PK,
- FK,
- UNIQUE,
- NOT NULL,
- CHECK.

## Step 6: Normalize

Reduce unnecessary redundancy.

## Step 7: Design indexes from queries

Do not blindly index every column.

## Step 8: Define transaction boundaries

Which operations must succeed together?

## Step 9: Measure

Use:

- EXPLAIN,
- slow query logs,
- metrics,
- load tests.

## Step 10: Scale deliberately

Typical progression:

    better schema/query/index
      ->
    more powerful machine
      ->
    replicas/caching
      ->
    partitioning
      ->
    sharding when necessary

---

# 45. Practical Design Scenarios

## Prevent overselling inventory

Naive pattern:

    Read quantity
    If quantity > 0
    Decrease quantity

Two transactions can both observe quantity = 1.

Safer approaches include row locking, serializable transactions, optimistic concurrency, or atomic conditional update.

Example conceptual pattern:

    UPDATE inventory
    SET quantity = quantity - 1
    WHERE product_id = ?
      AND quantity > 0;

Then inspect affected row count.

## Reliable event publishing

Bad dual write:

    Commit DB
    Publish event

Crash between steps creates inconsistency.

Transactional outbox:

    Transaction:
      update business data
      insert outbox event

    Background publisher:
      read outbox
      publish message

Consumers should tolerate duplicate delivery through idempotency.

## High-volume orders system

Start with:

    Customer
    Order
    OrderItem
    Payment

Then consider:

- normalized writes,
- indexes based on measured queries,
- read replicas,
- partitioning for time-based growth,
- outbox for asynchronous events,
- sharding only when genuinely required.

---

# 46. Common Interview Questions

## Q1. DBMS vs RDBMS?

DBMS is the broad category. RDBMS follows the relational model using tables, keys, relationships, and constraints.

## Q2. Candidate key vs superkey?

Candidate key is a minimal superkey.

## Q3. Primary key vs foreign key?

Primary key identifies a row. Foreign key references another table's key.

## Q4. What is normalization?

Dependency-based decomposition reducing redundancy and anomalies.

## Q5. 2NF vs 3NF?

2NF removes partial dependencies. 3NF additionally addresses problematic transitive dependencies and follows the formal superkey/prime rule.

## Q6. 3NF vs BCNF?

BCNF requires every determinant of a non-trivial FD to be a superkey. 3NF is less strict and can preserve more dependencies.

## Q7. What is attribute closure?

All attributes derivable from a set under the given functional dependencies.

## Q8. What is ACID?

Atomicity, Consistency, Isolation, Durability.

## Q9. What is conflict serializability?

A schedule equivalent to a serial schedule based on ordering of conflicting operations.

## Q10. How do you check conflict serializability?

Build precedence graph. A cycle means not conflict serializable.

## Q11. Dirty vs non-repeatable vs phantom read?

- Dirty: reads uncommitted data.
- Non-repeatable: same row changes between reads.
- Phantom: set of rows matching predicate changes.

## Q12. What is MVCC?

Multiple versions allow snapshot reads and reduced reader/writer blocking.

## Q13. What is 2PL?

Growing phase acquires locks; shrinking phase releases locks. Strict 2PL retains write locks until commit/abort.

## Q14. What is a deadlock?

Transactions form circular resource waits.

## Q15. B-tree vs hash index?

B-tree/B+ tree supports equality and range access. Hashing is primarily optimized for equality.

## Q16. Why not index every column?

Indexes consume storage and add write/update/delete overhead.

## Q17. Why can database ignore an index?

A scan may be cheaper, predicate may be non-selective, statistics inaccurate, or query expression may not match index effectively.

## Q18. What is WAL?

Recovery log information is made durable before corresponding data-page changes become durable.

## Q19. Replication vs sharding?

Replication copies data. Sharding distributes different subsets.

## Q20. Partitioning vs sharding?

Partitioning splits data into pieces, often managed within one logical database. Sharding distributes data across independent nodes/databases.

## Q21. What is CAP?

During network partition, system trades off consistency and availability under CAP definitions.

## Q22. How do you optimize a slow query?

Measure, inspect plan, compare estimated and actual rows, identify expensive operators, improve indexes/statistics/query shape, benchmark again.

---

# 47. Final Revision Checklist

## Fundamentals

- [ ] DBMS vs file system
- [ ] DBMS vs RDBMS
- [ ] Schema vs instance
- [ ] Data independence
- [ ] Three-schema architecture

## Relational model

- [ ] Tuple, attribute, domain
- [ ] Superkey
- [ ] Candidate key
- [ ] Primary key
- [ ] Foreign key
- [ ] Integrity constraints

## ER model

- [ ] Entity
- [ ] Relationship
- [ ] Cardinality
- [ ] Participation
- [ ] Weak entity
- [ ] Specialization/generalization
- [ ] ER mapping

## Theory

- [ ] Relational algebra
- [ ] Relational calculus
- [ ] Functional dependency
- [ ] Attribute closure
- [ ] Armstrong's axioms
- [ ] Candidate-key derivation

## Normalization

- [ ] 1NF
- [ ] 2NF
- [ ] 3NF
- [ ] BCNF
- [ ] 4NF
- [ ] 5NF
- [ ] Lossless decomposition
- [ ] Dependency preservation

## SQL

- [ ] DDL/DML/DCL/TCL
- [ ] NULL semantics
- [ ] Joins
- [ ] Aggregation
- [ ] Subqueries
- [ ] CTEs
- [ ] Window functions

## Transactions

- [ ] ACID
- [ ] Transaction states
- [ ] Schedules
- [ ] Conflict serializability
- [ ] Precedence graph
- [ ] Recoverability

## Concurrency

- [ ] Lost update
- [ ] Dirty read
- [ ] Non-repeatable read
- [ ] Phantom
- [ ] Write skew
- [ ] Locks
- [ ] 2PL
- [ ] Timestamp ordering
- [ ] MVCC
- [ ] Isolation levels
- [ ] Deadlocks

## Internals

- [ ] Pages
- [ ] Records
- [ ] Buffer pool
- [ ] B-tree/B+ tree
- [ ] Hash index
- [ ] Composite index
- [ ] Covering index

## Performance

- [ ] Query plans
- [ ] EXPLAIN
- [ ] Join algorithms
- [ ] Cardinality estimates
- [ ] Statistics
- [ ] Predicate pushdown
- [ ] Pagination

## Recovery and distributed systems

- [ ] WAL
- [ ] REDO/UNDO
- [ ] Checkpoints
- [ ] Replication
- [ ] Partitioning
- [ ] Sharding
- [ ] 2PC
- [ ] Sagas
- [ ] CAP
- [ ] Eventual consistency

---

# 48. Final Mental Model

A DBMS is not merely a place to store tables.

It is a coordinated system that:

1. accepts declarative queries,
2. parses and validates them,
3. transforms them into logical plans,
4. chooses efficient physical operations,
5. coordinates concurrent transactions,
6. enforces constraints,
7. manages memory and storage pages,
8. maintains indexes,
9. records recovery information,
10. recovers correctly after failure,
11. protects data through authorization and security.

Final architecture:

    APPLICATION
         |
       SQL/API
         |
    +-----------------------+
    | Parser / Rewriter     |
    | Query Optimizer       |
    | Execution Engine      |
    +-----------------------+
         |
    +-----------------------+
    | Transactions          |
    | Locks / MVCC          |
    | Isolation             |
    +-----------------------+
         |
    +-----------------------+
    | Buffer Manager        |
    | Storage Manager       |
    | Indexes               |
    +-----------------------+
         |
    +-----------------------+
    | Data Pages            |
    | WAL / Recovery        |
    +-----------------------+
         |
       DISK / SSD

> **The key idea:** DBMS is the combination of data modeling, declarative querying, correctness guarantees, concurrency control, physical storage, optimization, and recovery. Understanding how these layers connect is what turns DBMS from a memorization subject into a system you can reason about.