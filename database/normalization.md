---
title: Database Normalization
tags:
  - database
  - normalization
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
## 3rd Normal Form (`3NF`)

- **Must be in 2NF** -> the table must already satisfy second normal Form
- **NO transitive dependency** -> No non-key attribute should depend on another non-key attribute

formally for every non-trivial functional dependency   $x \rightarrow y$  at least one of the following condition must hold

- $x$ is a superkey
- $y$ is a prime attribute (a member of a candidate key)

A transitive dependency occurs when an attribute depends indirectly on the primary key through another non-key attribute. if attribute `A` determines `B` **(A -> B)** and B determines `C`**(B -> C)**, then `C` transitively depends on `A` through `B`.

> 
> Indirect relationship with primary key, it's called transitive dependency
> 

>[!important] Basic Knowledge
>**Prime Attributes:** Attributes (columns) that participate in or help form the candidate key (or primary key) of a relation/table 
>
>**Non-Prime Attributes:** Attributes that do not participate in forming any candidate key 
>
>**candidate key**: is a minimal set of attributes (columns) that can **uniquely identify** every single tuple (row) in a relation (table).



| SID | Student Name | DID | Department Name |
| --- | ------------ | --- | --------------- |
| 101 | Jamal        | D1  | CSE             |
| 102 | Kamal        | D2  | EEE             |
| 103 | hamal        | D1  | CSE             |

**Functional Dependencies in this table**

1. SID -> student name
2. SID -> DID
3. DID -> department name

Why this violates `3NF`:
- Both `DID` and `DN` are non-key attributes
- `DN` depends on `DID` which creates an indirect relationship with the primary key `SID`

>[!note]  When it **CAUSES A PROBLEM** (Violation):
>- **Non-Prime $\rightarrow$ Non-Prime** 
 >       _Example:_ If a non-prime attribute determines another non-prime attribute (e.g., `State ->  City` , where both are non-prime), it creates a transitive dependency that violates the **Third Normal Form (3NF)** 

**Example:**

![[Drawing 2026-10-20 14.10.13.excalidraw|800]]


## Boyce - Codd Normal Form

**Boyce-Codd Normal Form** (`BCNF`) is a stricter version of ***Third Normal From (3NF)*** while `3NF` resolves most data redundancies `BCNF` addresses edge cases involving overlapping candidate keys. 

>[!important] **The Rule of BCNF**
>A relation *R* is in **BCNF** if and only if for **every non-trival functional dependency** *X -> Y* that holds in R
>- ***X must be a Super key of R***

**Key difference between 3NF and BCNF**

to see why `BCNF` is stricter, compare their requirements for non-trivial dependency  ***X ->Y**
- **3NF rule:** `X` must be a superkey, or `y` must be a prime attri

