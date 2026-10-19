---
title: Database Normalization
tags:
  - database
date: 2026-10-19
---
# Database normalization

**Definition**: the process of organizing data in a relational DB to reduce redundancy & improve data integrity

**Advantages**:
- data is stored efficiently 
- update/insert/delete wont cause anomolies
- relationships between data are logical and consistent

**Example:**

| eid | sid | sname | cname | instructor |
| --- | --- | ----- | ----- | ---------- |
| 1   | 1   | jamal | DB    | Mr Ali     |
| 2   | 1   | jamal | AI    | Mr Khan    |
| 3   | 2   | kamal | DB    | Mr Ali     |

### Steps in DB normalization

- First Normal Form `1NF`
- Second Normal Form `2NF`
- 3NF
- Boyce-Codd Normal `BCNF`
- 4NF
- 5NF
- 6NF
---
## First Normal Form (`1NF`)

- each column must contain atomic values (no multi value columns)
- there must be no repeative group
![[Drawing 2026-10-19 23.11.22.excalidraw|800]]
---

## Second Normal Form (`2NF`)

- the table is already in `1NF`
- there is no partial dependency
  -> All non-key attributes must depend **entirely on the primary key**, rather than just a part of a composite primary key


>[!important] What is partial dependency
>when a table uses a **composite primary key** a partial dependency occurs if a non-key attribute depends on only one of those columns instead of entire combination

**Example:**

| SID | CID | student Name | Course Name |
| --- | --- | ------------ | ----------- |
| 1   | 101 | jamal        | Data base   |
| 1   | 102 | Jamal        | Algorithm   |
| 2   | 101 | Kamal        | Data base   |

this violets **2NF**:

- `Student Name` depends only on `SID` not on `CID`
- `Course Name` depends only on `CID` not on `SID`

since the `student name` and `course name` only part of the primary key (`sid`,`cid`) the table contains partial dependencies and violates the **2NF**

**Converting to 2NF (Decomposition):**

to remove the partial dependencies the table is split into three separate tables

![[Drawing 2026-10-19 23.40.50.excalidraw|800]]