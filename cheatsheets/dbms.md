# Database Management Systems (DBMS) Cheatsheet

> **Study log — 31 October 2026**
> Concise revision notes. Examples use a productivity application (TaskPlexus).

## References
- [PostgreSQL — Data Definition](https://www.postgresql.org/docs/current/ddl.html)
- [PostgreSQL — Constraints and Foreign Keys](https://www.postgresql.org/docs/current/ddl-constraints.html)
- [PostgreSQL — Joins](https://www.postgresql.org/docs/current/queries-table-expressions.html)
- [MongoDB — Documents](https://www.mongodb.com/docs/manual/core/document/)
- [MongoDB — Data Modeling](https://www.mongodb.com/docs/manual/data-modeling/)
- [MongoDB — $lookup](https://www.mongodb.com/docs/manual/reference/operator/aggregation/lookup/)
- [Redis — Data Types](https://redis.io/docs/latest/develop/data-types/)
- [Cassandra — Data Modeling](https://cassandra.apache.org/doc/latest/cassandra/developing/data-modeling/intro.html)
- [Neo4j — Graph Database Concepts](https://neo4j.com/docs/getting-started/graph-database/)
- [PostgreSQL — Aggregate Functions](https://www.postgresql.org/docs/current/functions-aggregate.html)
- [PostgreSQL — Constraints and Referential Integrity](https://www.postgresql.org/docs/current/ddl-constraints.html)
- [Database System Concepts — Functional Dependencies and Normalization (Silberschatz, Korth, Sudarshan)](https://www.db-book.com/)

## Contents
1. [Database and DBMS](#1-database-and-dbms)
2. [Relational vs. Non-Relational](#2-relational-vs-non-relational)
3. [Common Database Models](#3-common-database-models)
4. [Relationships: Foreign Keys and Joins](#4-relationships-foreign-keys-and-joins)
5. [Normalization: 1NF, 2NF, 3NF, BCNF](#5-normalization-1nf-2nf-3nf)
6. [OLTP vs. OLAP](#6-oltp-vs-olap)
7. [Quick Revision](#7-quick-revision)

---

## 1. Database and DBMS
- **Database:** An organized collection of data.
- **DBMS:** Software used to define, store, query, update, secure, and manage databases.
- **Database server:** A running database service that accepts client connections and handles operations.
- A DBMS provides storage/retrieval, concurrency, integrity, security, transactions (depending on the system), backup, and recovery.

Examples: PostgreSQL, MySQL, MongoDB, Redis, Cassandra, Neo4j.

## 2. Relational vs. Non-Relational

### 2.1 Relational databases (SQL)
**Pattern:** Data is organized into tables of rows and columns. Relationships commonly use keys.

`users`

| id | name |
|---:|---|
| 1 | NDK |
| 2 | Rahul |

`tasks`

| id | title | user_id |
|---:|---|---:|
| 101 | Learn Go | 1 |
| 102 | Learn DBMS | 1 |
| 103 | Build API | 2 |

`tasks.user_id` identifies the task owner and can be enforced as a foreign key.

**Examples:** PostgreSQL, MySQL, SQLite.  
**Useful when:** Structured relationships, integrity constraints, and transactions across related records matter.

### 2.2 Non-relational databases (NoSQL)
**Pattern:** Use models other than the traditional relational-table model: documents, key-value, wide-column, or graph.

MongoDB document example:

```json
{
  "_id": 1,
  "name": "NDK",
  "tasks": [
    { "title": "Learn Go" },
    { "title": "Learn DBMS" }
  ]
}
```

Tasks are embedded in this example. MongoDB can also store references; embedding is a modeling choice.

**Examples:** MongoDB, Redis, Cassandra, Neo4j.  
**Useful when:** A particular data model, access pattern, scale requirement, or operational characteristic fits.

> NoSQL does not mean “no schema” or “no relationships.” Document databases can validate data and store references; graph databases explicitly model relationships.

## 3. Common Database Models

| Model | Data organization | Example |
|---|---|---|
| Relational | Tables, rows, columns | PostgreSQL |
| Document | JSON-like documents | MongoDB |
| Key-value | Keys mapped to values | Redis |
| Wide-column | Rows with flexible columns/column families | Cassandra |
| Graph | Nodes connected by edges | Neo4j |

### Do relational concepts apply to every database?
**No.** Some fundamentals are common, but their implementation differs.

| Concept | Relational | Document | Key-value | Graph |
|---|---|---|---|---|
| Tables/rows/columns | Native | Not primary | Not primary | Not primary |
| Schema | Usually explicit | Often flexible; validation possible | Often application-defined | Model-specific |
| Primary key | Explicit constraint | Unique document ID (e.g. `_id`) | Key identifies value | Model-specific identity |
| Foreign key | Native constraint | References, no native relational FK enforcement | Not a native concept | Relationships are edges |
| Relationships | Keys and joins | Embedding or references | Application logic | Edges and traversal |

Most databases provide ways to store, identify, retrieve, index, and protect data. Exact mechanisms and guarantees vary. A SQL table is not equivalent to a MongoDB collection; a document reference is not equivalent to a relational foreign key.

## 4. Relationships: Foreign Keys and Joins

### 4.1 Foreign key
**Pattern:** A foreign key references a candidate key (commonly a primary key) in another table and helps enforce referential integrity.

```sql
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(100)
);

CREATE TABLE tasks (
  id INT PRIMARY KEY,
  title VARCHAR(100),
  user_id INT REFERENCES users(id)
);
```

The database rejects a task whose `user_id` does not reference an existing user, subject to the constraint configuration.

### 4.2 JOIN
**Pattern:** Combine related rows from tables using a matching condition.

```sql
SELECT users.name, tasks.title
FROM users
INNER JOIN tasks ON users.id = tasks.user_id;
```

Example output:

| name | title |
|---|---|
| NDK | Learn Go |
| NDK | Learn DBMS |
| Rahul | Build API |

### 4.3 How other databases handle relationships

| Database | Native relational FK? | Related-data access |
|---|---|---|
| PostgreSQL | Yes | SQL joins |
| MongoDB | No | Embed, reference, or use `$lookup` |
| Redis | No | Fetch related keys; application combines results |
| Cassandra | No | Query-oriented tables; often denormalized |
| Neo4j | Not relational FKs | Native graph edges and traversal |

MongoDB JOIN-like aggregation:

```javascript
db.users.aggregate([
  {
    $lookup: {
      from: "tasks",
      localField: "_id",
      foreignField: "userId",
      as: "tasks"
    }
  }
]);
```

`$lookup` matches documents and adds matching documents to the result. It does not enforce a relational FK constraint.

Redis application-level lookup:

```redis
HSET user:1 name NDK
HSET task:101 title "Learn Go" userId 1
HGETALL user:1
HGETALL task:101
```

The application fetches related keys and combines the data; Redis does not run a traditional SQL JOIN.

Cassandra query-oriented table:

```sql
CREATE TABLE tasks_by_user (
  user_id INT,
  task_id INT,
  title TEXT,
  PRIMARY KEY (user_id, task_id)
);

SELECT * FROM tasks_by_user WHERE user_id = 1;
```

Cassandra generally avoids general-purpose relational joins and models tables around required queries.

Neo4j graph relationship:

```cypher
CREATE (u:User {id: 1, name: "NDK"})
CREATE (t:Task {id: 101, title: "Learn Go"})
CREATE (u)-[:OWNS]->(t);

MATCH (u:User)-[:OWNS]->(t:Task)
RETURN u.name, t.title;
```

**Key idea:** Relational databases use keys and joins; document databases embed or reference; key-value applications connect keys in application logic; graph databases connect nodes with edges.

## 5. Normalization: 1NF, 2NF, 3NF, BCNF

**Purpose:** Organize relational tables to reduce unnecessary duplication and prevent insert, update, and delete anomalies.

### 5.1 First Normal Form (1NF)
**Rule:** Each cell contains one atomic value; avoid repeating groups or multiple values in one cell.

Not in 1NF:

| student_id | student_name | courses |
|---:|---|---|
| 1 | NDK | DBMS, OS |
| 2 | Rahul | DBMS, CN |

Convert to 1NF:

| student_id | student_name | course |
|---:|---|---|
| 1 | NDK | DBMS |
| 1 | NDK | OS |
| 2 | Rahul | DBMS |
| 2 | Rahul | CN |

**Remember:** One value per cell.

### 5.2 Second Normal Form (2NF)
**Rule:** Must be in 1NF, and every non-key attribute must depend on the whole candidate key—not just part of a composite candidate key.

Suppose the key is `(student_id, course)`. Dependencies:
- `student_id → student_name`
- `(student_id, course)` identifies an enrollment.

`student_name` depends only on `student_id`, part of the composite key. This is a **partial dependency**.

Split into:
- `Students(student_id, student_name)`
- `Enrollments(student_id, course)`

**Remember:** No partial dependency on a composite key. If every candidate key is single-column, a 1NF table is automatically in 2NF.

### 5.3 Third Normal Form (3NF)
**Rule (simplified):** Must be in 2NF and have no transitive dependency of non-key attributes on a candidate key.

Before 3NF:

| student_id | student_name | department_id | department_name |
|---:|---|---:|---|
| 1 | NDK | 10 | Computer |
| 2 | Rahul | 10 | Computer |
| 3 | Amit | 20 | Mechanical |

Dependencies:
- `student_id → department_id`
- `department_id → department_name`

Therefore `student_id → department_id → department_name`. This is a **transitive dependency**.

Split into:
- `Students(student_id, student_name, department_id)`
- `Departments(department_id, department_name)`

**Remember:** Non-key attributes should not depend transitively on a candidate key.


### 5.4 BCNF (Boyce–Codd Normal Form)
**Rule:** For every non-trivial functional dependency `X → Y`, X must be a superkey.

A **determinant** is the attribute (or attribute set) on the left side of a functional dependency.

Example: `ClassAssignments(student, subject, instructor)`

Assume:
- Each instructor teaches exactly one subject.
- A subject may have multiple instructors.
- A student may take multiple subjects.

Functional dependencies:
- `(student, subject) → instructor`
- `instructor → subject`

Candidate keys:
- `(student, subject)`
- `(student, instructor)`

But `instructor` alone is not a superkey: one instructor can teach multiple students. Therefore `instructor → subject` violates BCNF.

Decompose into:
- `InstructorSubject(instructor, subject)`
- `StudentInstructor(student, instructor)`

**3NF vs. BCNF:** This example can satisfy 3NF because `subject` is a prime attribute (part of a candidate key), while failing BCNF. BCNF does not allow this exception.

**Remember:** Every determinant must be a superkey.

### 5.5 Functional Dependencies and Keys
A **functional dependency (FD)** `X → Y` means that a value of X uniquely determines the value of Y under the table's business rules.

Example:
- `student_id → student_name`
- `course_id → course_name`
- `(student_id, course_id) → grade`

A functional dependency is a rule about the data model, not merely a pattern that happens to appear in the current sample rows.

| Term | Meaning | Example |
|---|---|---|
| Superkey | Any attribute set that uniquely identifies a row | `{student_id, name}` |
| Candidate key | A minimal superkey; removing any attribute means it no longer uniquely identifies a row | `{student_id}` |
| Primary key | Candidate key selected as the table's main identifier | `student_id` |
| Alternate key | Candidate key not selected as the primary key | `email` |
| Composite key | Key made from multiple attributes | `(student_id, course_id)` |
| Prime attribute | Attribute that belongs to at least one candidate key | `subject` in the BCNF example |

Example: if both `student_id` and `email` are individually unique:
- `{student_id}` and `{email}` are candidate keys.
- `{student_id, name}` is a superkey, but not a candidate key because it is not minimal.

### 5.6 Normalization Anomalies
Redundant data can cause:
- **Update anomaly:** The same fact must be updated in multiple rows; missing one creates inconsistent data.
- **Insert anomaly:** A fact cannot be inserted without inserting an unrelated fact.
- **Delete anomaly:** Deleting one row unintentionally removes the only stored copy of another fact.

Normalization separates facts according to their dependencies, reducing these anomalies.

### 5.7 Denormalization
**Pattern:** Intentionally duplicate or precompute data to optimize a measured read workload.

Example: an `orders` table may store `customer_name` alongside `customer_id`, even though the name also exists in `customers`.

**Why use it?**
- Reduce joins for frequent reads.
- Serve precomputed aggregates or read models.
- Improve performance for known access patterns.

**Trade-offs:** More storage, duplicated facts, update anomalies, and extra consistency work. Measure first; do not denormalize blindly.

Common examples include materialized views, cached aggregates, and CQRS read models.

### 5.8 Referential Integrity
**Meaning:** A foreign-key value must match a referenced key in the parent table, unless the FK column is `NULL` and nullable.

PostgreSQL example:

```sql
CREATE TABLE departments (
  id BIGSERIAL PRIMARY KEY,
  name VARCHAR(100) NOT NULL
);

CREATE TABLE employees (
  id BIGSERIAL PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  department_id BIGINT,
  CONSTRAINT fk_department
    FOREIGN KEY (department_id)
    REFERENCES departments(id)
    ON DELETE SET NULL
);
```

Common FK actions:
- `CASCADE`: propagate the parent update/delete to referencing rows.
- `SET NULL`: set the referencing FK column to `NULL`.
- `SET DEFAULT`: set it to its default value (which must still satisfy the constraint).
- `RESTRICT`: prevent the referenced row from being deleted/updated while references exist.
- `NO ACTION`: check the constraint at its normal check time; it may be deferrable.

**Index note:** PostgreSQL automatically indexes primary/unique referenced keys, but does not automatically index the referencing FK column. Add an index when joins or parent-row updates/deletes make it useful:

```sql
CREATE INDEX idx_employees_department_id
ON employees(department_id);
```

### Normal Forms — Final Comparison

| Normal form | What it prevents | Core test |
|---|---|---|
| 1NF | Repeating groups / non-atomic cells | One value per cell |
| 2NF | Partial dependency | A non-prime attribute must not depend on only part of a candidate key |
| 3NF | Disallowed transitive dependencies | For every non-trivial FD X → A, X is a superkey or A is prime |
| BCNF | Non-superkey determinants | For every non-trivial FD X → Y, X is a superkey |

Implication: `BCNF ⇒ 3NF ⇒ 2NF ⇒ 1NF`. The reverse implications do not generally hold.

### Normalization quick view

- **1NF:** One atomic value per cell.
- **2NF:** No partial dependency of a non-prime attribute on a candidate key.
- **3NF:** For each non-trivial FD `X → A`, X is a superkey or A is a prime attribute.
- **BCNF:** For each non-trivial FD `X → Y`, X is a superkey.

`BCNF ⇒ 3NF ⇒ 2NF ⇒ 1NF`.

Normalization is primarily taught for relational design. NoSQL systems often use different modeling strategies and may intentionally denormalize data to optimize access patterns.

## 6. OLTP vs. OLAP

**OLTP and OLAP describe workload/processing categories, not database models or separate query languages.** They describe the kind of work a system is optimized to perform.

### 6.1 OLTP — Online Transaction Processing
**Pattern:** Handle frequent, relatively small operational transactions.

Examples: create an account, insert a task, update task status, read a profile, transfer money.

Typical characteristics:
- Many concurrent operations.
- Small, frequent reads and writes.
- Low-latency request handling.
- Strong correctness and transaction requirements.

### 6.2 OLAP — Online Analytical Processing
**Pattern:** Analyze data to find trends, summaries, and insights.

Examples: yearly revenue, most active users, monthly sales averages, task-completion trends.

Typical characteristics:
- Complex analytical queries.
- Aggregations over many records.
- Historical/reporting-oriented analysis.
- Often read-heavy.

### 6.3 Same language, different workload

Both workloads can use SQL. Purpose and access pattern distinguish them.

OLTP — update one task:

```sql
UPDATE tasks
SET status = 'completed'
WHERE id = 101;
```

OLAP-style — count tasks per month:

```sql
SELECT DATE_TRUNC('month', created_at) AS month,
       COUNT(*) AS total_tasks
FROM tasks
GROUP BY month
ORDER BY month;
```

Both can run on PostgreSQL. One performs an operational update; the other performs an analytical aggregation.

> OLTP is not only writes, and OLAP is not simply “SELECT queries.” OLTP also reads data; analytical systems can ingest or write data. Classify by purpose and access pattern.

| | OLTP | OLAP |
|---|---|---|
| Goal | Run daily operations | Analyze data |
| Operations | Small, frequent transactions | Complex queries and aggregations |
| Example | Place an order | Analyze yearly sales |
| Data focus | Current operational records | Historical/analytical data |

One database can support both workloads. Larger systems may separate transactional databases from analytical warehouses to isolate and optimize workloads.

## 7. Quick Revision
- **Database:** Organized collection of data.
- **DBMS:** Software that manages databases.
- **Relational:** Tables; relationships commonly use keys.
- **Non-relational:** Document, key-value, wide-column, and graph models.
- **NoSQL:** Does not mean no schema, structure, or relationships.
- **Foreign key:** Enforces a reference to a key in another relational table.
- **JOIN:** Combines related rows from tables.
- **MongoDB `$lookup`:** JOIN-like aggregation; not a relational FK constraint.
- **1NF:** Atomic values.
- **2NF:** No partial dependency on a composite candidate key.
- **3NF:** For every non-trivial FD `X → A`, X is a superkey or A is prime.
- **BCNF:** Every determinant of a non-trivial FD is a superkey.
- **Superkey:** Any attribute set that uniquely identifies a row.
- **Candidate key:** A minimal superkey.
- **Functional dependency:** `X → Y` means X determines Y.
- **Denormalization:** Intentional redundancy for a measured read-performance benefit.
- **Referential integrity:** Foreign keys keep references valid.
- **OLTP:** Frequent operational transactions.
- **OLAP:** Analytical queries and aggregations.
- **Key distinction:** Relational vs. non-relational describes the data model; OLTP vs. OLAP describes the workload.
