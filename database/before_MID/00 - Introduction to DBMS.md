---
tags: [database, CSE3103, dbms, lecture-01]
course: CSE3103 - Database
source: "CSE3103 DATABASE - 01.pdf"
---

# Introduction to Database Management Systems

> [!abstract] In this note
> What a database is, why we don't just use files, the abstraction levels of a DBMS, DDL vs DML, and the four classic database architectures.

## What is a Database?

A **database (DB)** is a huge amount of **interrelated data** that is easy to retrieve, with good speed for access and search.

Think of it this way:
1. Start with **types of data**.
2. **Relate/interrelate** each data item to another.
3. Think about how much data you personally generate every day (e.g. on a social network) — a database is what organizes all of that at scale.

### Common Database Applications
- Banking
- Airlines
- Universities
- Sales / Purchases
- Order Tracking
- Customized Recommendations
- Supply Chain
- Human Resources
- Tax Deduction

## Database Management System (DBMS)

A **DBMS** is a software package that stores and manages a database. It contains information about a particular *enterprise*.

- A **set of programs** to access the data
- Provides an environment that is **convenient and efficient** to use
- Lets users **store, update, retrieve, organize, and protect** their data

## Why Not Just Use File Systems?

Storing data directly in flat files (instead of a DBMS) creates real problems:

| Problem | Explanation |
|---|---|
| **Data redundancy & inconsistency** | Same data duplicated across multiple files → copies can disagree |
| **Difficulty accessing data** | No query mechanism; need custom code for every new question |
| **Data isolation** | Data scattered across files in different formats |
| **Integrity problems** | Hard to add/enforce new constraints (e.g. "balance must be > 0") |
| **Atomicity of updates** | A crash mid-update can leave data half-changed |
| **Concurrent access issues** | Multiple users writing at once → inconsistency if uncontrolled |
| **Security problems** | Hard to give a user access to *some* data but not *all* of it |

> [!tip] Why concurrency control matters
> Concurrent access is *needed* for performance (many users at once), but **uncontrolled** concurrent access leads to inconsistencies — this is exactly why DBMSs implement transactions and locking.

## Levels of Abstraction

A DBMS hides *how* data is physically stored behind layers of abstraction:

1. **Physical level** — describes how a record (e.g. `instructor`) is actually stored on disk.
2. **Logical level** — describes *what* data is stored and the relationships among the data.
   ```
   type instructor = record
        ID         : string;
        name       : string;
        dept_name  : string;
        salary     : integer;
   end;
   ```
3. **View level** — application programs see only the parts of the data they need; some data (e.g. an employee's salary) can be hidden here for security.

![[attachments/view-of-data.jpg]]

*The View Level sits above the Logical Level, which sits above the Physical Level. On the right: a more detailed 4-tier picture — External → Logical/Conceptual → Internal → Physical — connected by well-defined interfaces (User Interface, Logical Record Interface, Physical Record Interface).*

## Instances and Schemas

- **Schema** — describes the overall **design** of the database (like a variable's *type*).
  - **Physical schema** — overall physical structure of the DB.
  - **Logical schema** — overall logical structure of the DB (e.g. "customers and their addresses, and the relationship between them").
  - **Sub-schema** — describes the different *views* of the database.
- **Instance** — the actual **content** of the database at one point in time (like a variable's *value*).

### Data Independence

The ability to change a schema at one level **without** needing to change the level above it.

- **Physical Data Independence** — modify the physical schema without changing the logical schema. Applications only depend on the logical schema, so storage-level changes shouldn't ripple upward.
- **Logical Data Independence** — modify the logical schema without changing the view level.

> [!note] Core design principle
> Interfaces between levels should be well-defined so that a change in one part does **not** seriously affect the others.

## Database Users & DBA Activities

- **Application Programmer** — writes programs that use the DB
- **Sophisticated User** — writes/uses queries directly without full application programs
- **Specialized User** — writes specialized applications not typical of standard data processing
- **Naïve User** — interacts with the system only via pre-written application programs (e.g. an ATM user)

## DDL vs DML

### Data Definition Language (DDL)
The notation used to **define** the database schema.

```sql
create table instructor (
    ID          char(5),
    name        varchar(20),
    dept_name   varchar(20),
    salary      numeric(8,2)
)
```

- A DDL compiler generates table templates stored in the **data dictionary**.
- The data dictionary holds **metadata** (data *about* data):
  - Database schema
  - Integrity constraints (e.g. primary key `ID` must be unique)
  - Authorization (who can access what)

### Data Manipulation Language (DML)
The language used for **accessing and manipulating** data. Also called the **query language** (procedural & declarative).

Two classes:
- **Pure** — used to prove properties about computational power / optimization
  - Relational Algebra
  - Tuple Relational Calculus
  - Domain Relational Calculus
- **Commercial** — used in real systems
  - **SQL** is the most widely used commercial language

## Database Architectures

There are four classic deployment architectures for a DBMS:

### 1. Centralized Database
All departments (Finance, Sales, HR, Marketing) push/pull data from **one** central database. Local offices connect to a single **HQ** database over the network.

![[attachments/architecture-centralized.jpg]]

### 2. Client–Server Database
Clients talk to an **Application Server**, which talks to a **Database Server**, which owns the actual **Database**. The app server sends a *data request*; the DB server returns the *selected data*.

![[attachments/architecture-client-server.jpg]]

### 3. Parallel Database
Multiple **nodes**, each with their own CPUs and shared memory, connected via a **common high-speed bus** to a set of **shared disks**. Used for high throughput / heavy workloads on one physical site.

![[attachments/architecture-parallel.jpg]]

### 4. Distributed Database
Data (and processing) is spread across **multiple geographically separate sites**, each with its own local database, coordinated as one logical database. Local offices each keep a database and stay synced with HQ.

![[attachments/architecture-distributed.jpg]]

| Architecture | Key idea | Typical use case |
|---|---|---|
| Centralized | One DB, many departments/users hit it | Small org, single site |
| Client–Server | App server + dedicated DB server | Typical web/enterprise app |
| Parallel | Many nodes share disks over a fast bus | High-performance computing, big single-site workloads |
| Distributed | DB physically spread across sites | Multinational orgs, geo-redundancy |

---
## Related Notes
- [[01 - ER Modeling]]
- [[02 - Relational Algebra - Unary Operations]]
