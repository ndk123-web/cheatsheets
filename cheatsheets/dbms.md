# Database Management Systems (DBMS) Cheatsheet

> **Study log — 31 October 2026**
>
> Concise revision notes for database fundamentals. Examples use a productivity application (TaskPlexus) where useful.

## Contents

1. [Database and DBMS](#1-database-and-dbms)
2. [Relational vs. Non-Relational Databases](#2-relational-vs-non-relational-databases)
3. [Common Database Models](#3-common-database-models)
4. [OLTP vs. OLAP](#4-oltp-vs-olap)
5. [Quick Revision](#5-quick-revision)

## References

- [PostgreSQL — Database Objects and Data Definition](https://www.postgresql.org/docs/current/ddl.html)
- [PostgreSQL — Introduction to Relational Databases](https://www.postgresql.org/docs/current/tutorial-concepts.html)
- [MongoDB — Documents](https://www.mongodb.com/docs/manual/core/document/)
- [MongoDB — Data Modeling](https://www.mongodb.com/docs/manual/data-modeling/)
- [Redis — Data Types](https://redis.io/docs/latest/develop/data-types/)
- [Apache Cassandra — Data Modeling](https://cassandra.apache.org/doc/latest/cassandra/developing/data-modeling/intro.html)
- [Neo4j — Graph Database Concepts](https://neo4j.com/docs/getting-started/graph-database/)
- [PostgreSQL — Aggregate Functions](https://www.postgresql.org/docs/current/functions-aggregate.html)

---

## 1. Database and DBMS

- **Database:** An organized collection of data that can be stored, accessed, and managed.
- **DBMS (Database Management System):** Software used to define, create, query, update, secure, and manage databases.
- **Database server:** The running database service/process that accepts client connections and handles database operations.

Examples of DBMSs: PostgreSQL, MySQL, MongoDB, Redis, Cassandra, and Neo4j.

### Why use a DBMS?

It provides mechanisms for data storage and retrieval, concurrent access, integrity, security, transactions (depending on the system), backup, and recovery.

---

## 2. Relational vs. Non-Relational Databases

### 2.1 Relational databases (SQL)

**Pattern:** Organize data primarily into tables made of rows and columns. Relationships between tables are commonly represented using keys.

Example — `users` table:

| id | name | email |
|---:|---|---|
| 1 | NDK | ndk@example.com |
| 2 | Rahul | rahul@example.com |

Example — `tasks` table:

| id | title | user_id |
|---:|---|---:|
| 101 | Learn Go | 1 |
| 102 | Learn DBMS | 1 |
| 103 | Build API | 2 |

Here, `tasks.user_id` identifies the user who owns each task. A relational database can enforce this relationship using a foreign key.

**Common examples:** PostgreSQL, MySQL, SQLite.

**Useful when:** Data has structured relationships, integrity constraints matter, and transactions across related records are important.

### 2.2 Non-relational databases (NoSQL)

**Pattern:** Store data using models other than the traditional relational-table model, such as documents, key-value pairs, wide columns, or graphs.

Example — MongoDB document:

```json
{
  "_id": 1,
  "name": "NDK",
  "email": "ndk@example.com",
  "tasks": [
    { "title": "Learn Go" },
    { "title": "Learn DBMS" }
  ]
}
```

This example embeds tasks inside a user document. MongoDB can also store references between documents; embedding is a modeling choice, not a requirement.

**Common examples:** MongoDB, Redis, Cassandra, Neo4j.

**Useful when:** A particular data model, access pattern, scale requirement, or operational characteristic fits a non-relational database.

> **Important:** NoSQL does not mean “no schema” or “no relationships.” It means the database does not primarily use the traditional relational model. Document databases can have validation rules and references; graph databases explicitly model relationships.

---

## 3. Common Database Models

| Model | Data organization | Example |
|---|---|---|
| Relational | Tables, rows, columns | PostgreSQL |
| Document | JSON-like documents in collections | MongoDB |
| Key-value | Keys mapped to values | Redis |
| Wide-column | Rows with flexible columns grouped into column families | Cassandra |
| Graph | Nodes connected by relationships/edges | Neo4j |

### Do relational concepts apply to every database?

**No.** Some database fundamentals are common, but their names and implementations differ by data model.

| Concept | Relational DB | Document DB | Key-value DB | Graph DB |
|---|---|---|---|---|
| Tables / rows / columns | Native structure | Not the primary structure | Not the primary structure | Not the primary structure |
| Schema | Usually defined through database objects and constraints | Often flexible; validation is possible | Usually application-defined | Model-specific |
| Primary key | Explicit primary-key constraint | Unique document identifier (e.g. MongoDB `_id`) | The key identifies the value | Node/relationship identity is model-specific |
| Foreign key | Native constraint support | References can be stored, but no automatic relational FK enforcement | Not a native relational concept | Relationships are represented as edges |
| Relationships | Foreign keys and joins | Embedding or references | Usually modeled by application logic | Explicit graph relationships and traversal |

### Concepts that broadly apply

Most database systems provide ways to store and retrieve data, identify records, query or access data, create indexes, control access, and manage durability/recovery. The exact mechanisms and guarantees vary.

**Do not assume:** A SQL table is equivalent to a MongoDB collection, or a relational foreign key is equivalent to a document reference.

---

## 4. OLTP vs. OLAP

**OLTP and OLAP describe workload/processing categories, not database models or separate query languages.** They describe the kind of work a system is optimized to perform.

### 4.1 OLTP — Online Transaction Processing

**Pattern:** Handle frequent, relatively small operational transactions.

Examples:
- Create a user account.
- Insert a task.
- Update a task's status.
- Read a user's profile.
- Transfer money between accounts.

Typical characteristics:
- Many concurrent operations.
- Small, frequent reads and writes.
- Low-latency request handling.
- Strong correctness and transaction requirements.

### 4.2 OLAP — Online Analytical Processing

**Pattern:** Analyze data to find trends, summaries, and insights.

Examples:
- Calculate yearly revenue.
- Find the 10 most active users.
- Calculate average monthly sales.
- Compare task-completion trends across months.

Typical characteristics:
- Complex analytical queries.
- Aggregations over many records.
- Historical and reporting-oriented analysis.
- Often read-heavy.

### 4.3 Same database language, different workload

Both workloads can use SQL. The purpose and access pattern distinguish them.

**OLTP example — update one task:**

```sql
UPDATE tasks
SET status = 'completed'
WHERE id = 101;
```

**OLAP-style example — count tasks per month:**

```sql
SELECT
    DATE_TRUNC('month', created_at) AS month,
    COUNT(*) AS total_tasks
FROM tasks
GROUP BY month
ORDER BY month;
```

Both queries can run on PostgreSQL. One performs an operational update; the other performs an analytical aggregation.

> **Avoid this misconception:** OLTP is not only writes, and OLAP is not simply “SELECT queries.” OLTP systems also read data, and analytical systems can ingest or write data. Classify the workload by its purpose and access pattern.

| | OLTP | OLAP |
|---|---|---|
| Primary goal | Run daily operations | Analyze data |
| Typical operations | Small, frequent transactions | Complex queries and aggregations |
| Example | Place an order | Analyze yearly sales |
| Data focus | Current operational records | Historical and analytical data |

A single database can support both kinds of workload. Larger systems may separate transactional databases from analytical warehouses to isolate workloads and optimize each one.

---

## 5. Quick Revision

- **Database:** Organized collection of data.
- **DBMS:** Software that manages databases.
- **Relational:** Tables with rows and columns; relationships commonly use keys.
- **Non-relational:** Other data models such as documents, key-value, wide-column, and graph.
- **NoSQL:** Does not mean no schema, no structure, or no relationships.
- **OLTP:** Workload focused on frequent operational transactions.
- **OLAP:** Workload focused on analytical queries and aggregations.
- **Key distinction:** Relational vs. non-relational describes the data model; OLTP vs. OLAP describes the workload.

