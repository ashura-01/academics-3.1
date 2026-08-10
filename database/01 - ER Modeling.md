---
tags: [database, CSE3103, dbms, er-diagram, lecture-02]
course: CSE3103 - Database
source: "CSE3103 DATABASE - 02.pdf"
---

# ER (Entity–Relationship) Modeling

> [!abstract] In this note
> Entities, relationships, attributes, ERD symbols, a full worked ERD example, keys, cardinality/participation constraints, weak entities, attribute types, degree of relationships, and converting non-binary to binary relationships.

## From Idea to Database

```
Idea → Requirement Analysis → High-Level Description (ER Modeling) → Relationship Schema (DDL/DML) → RDBMS Implementation
```

**Requirement Analysis**: first generate an idea about a project, then develop a **High-Level Description** — this is ER Modeling. The symbols used in an ER diagram represent the requirements.

## ER Diagram (ERD)

The diagram that represents an ER Model. It has **3 main parts**:
1. **Entity**
2. **Relationship**
3. **Attributes**

(plus several smaller supporting symbols, covered below)

## Entity

- Always a **singular noun**, kept concise.
- Found *after* requirement analysis.
- A **real object** from the problem domain; data is stored in the database on entities.
- Distinguishable from other objects; described using a set of **attributes**.

**Example categories:**
| Category | Examples |
|---|---|
| People | employee, students, patients |
| Place | store, warehouse |
| Objects | machine, products, vehicle |
| Events | lectures, sales, registration |
| Concept | account, course |

### Worked Example
> "There are many students in AUST. Students can enroll in different courses and receive grades."

Possible entities: `Student`, `AUST`, `Courses`, `Grades`

- Every entity **set** has attributes.
- Every entity set has a **key**.
- Every attribute has a **domain**.

## Relationship

- The **interaction** between entity sets.
- Always a **verb**, and can have its own attributes.
- An **association** among two or more entities.

### Worked Example (same scenario)
Possible relationships: `are`, `Enroll`, `receive`

## Attributes

The **properties** of an entity or a relationship.

- Properties stay the same in *kind* for the entity, but the **value** can change.
- If attributes are given in the problem story, use those.
- Otherwise, infer meaningful attributes yourself.

**Example — Student attributes:**
`ID, Name, Parent's name, Address, DOB, Blood Group, Phone, E-mail, Eye Color, Height, Hair Color, NID, Passport Number, Birth Certificate`

## ERD Symbols (Crow's Foot / Chen Notation mix used here)

| Symbol | Represents |
|---|---|
| Rectangle | Entity |
| Diamond | Relationship |
| Ellipse | Attribute |

- To connect one entity to another, you must join them through a **relationship**.
- A relationship is essentially a **table** — it takes attributes from the associated tables and may add its own attributes.

![[attachments/erd-symbols.jpg]]

## Full Worked Example: University ERD

**Scenario:** AUST is a private university in Bangladesh with 8 departments, located at Tejgaon I/A, Dhaka. Students are admitted into departments, enrolled in courses each semester, and receive grades.

**Step 1 — Identify entities, relationships, attributes:**

| Possible Entity | Possible Relationship | Possible Attributes |
|---|---|---|
| Student | Admit | Department – Name, HoD, Accreditation |
| Courses | Enroll | Student – S.ID, S.Name, DOB, Phone, BG |
| Department | | Course – C.ID, C.Name, Credit |
| | | Admit – Session, Quota |
| | | Enroll – Semester, Grades |

**Step 2 — Draw the ERD:**

![[attachments/erd-university-example.jpg]]

`Departments —(Admit)— Students —(Enroll)— Courses`, with each entity/relationship carrying its own attribute ellipses (primary keys underlined, e.g. `Name` for Department, `S.ID` for Student, `C.ID` for Course).

## Keys

1. **Super Key** — *any* set of attributes that can uniquely identify a tuple. Formally, all subsets of the power set of attributes are candidate super keys.
   - e.g. if `A = {1, 2, 3, 4}`, then `P(A) = {{1},{2},{3},{4},{1,2},{2,3},{3,4}, … {1,2,3,4}, {}}` — each non-empty combination is a potential super key.
2. **Candidate Key** — a super key with **no redundant attributes** (minimal).
3. **Primary Key** — the candidate key chosen, whose value is guaranteed **unique**.
4. **Alternate Key** — a candidate key that was **not** chosen as the primary key.
5. **Composite Key** — more than one attribute jointly forms the primary key.
6. **Foreign Key** — a primary key of one table used as a reference (attribute) in another table.

```
Super Key ⊇ Candidate Key ⊇ Primary Key
```

### Primary Key rules
- Distinguishes one entity/tuple from another.
- Underlined in the ERD.
- Can be composed of more than one attribute.
- Should use the **minimum** number of attributes needed.
- Prefer the attribute that is most frequently used / most natural to reference.

## Cardinality Constraints

Express the **maximum number** of entities that can be associated with another entity via a relationship.

| Type | Notation | Example |
|---|---|---|
| Many-to-Many | `m : n` | `Students —(Enroll)— Courses` |
| One-to-Many | `1 : m` | `Instructor —(Teach)— Courses` |
| Many-to-One | `m : 1` | `Courses —(Taught By)— Instructor` |
| One-to-One | `1 : 1` | `Student —(Owns)— IUMS AC` |

## Participation Constraints

| Symbol | Meaning |
|---|---|
| Double Line | **Total** participation — every entity instance *must* participate in the relationship |
| Single Line | **Partial** participation — participation is optional |

## Weak Entity Set

- Arises from a **one-to-many** relationship where one side is **dominant** and the other is **subordinate**.
- The subordinate (weak) entity always **depends on** the dominant entity to exist.
- Related terms: *weak entity*, *identifying entity*, *discriminator*.

## Attribute Types

**Simple vs Composite**
- *Simple*: an attribute that can't be broken down further (e.g. `S.ID`).
- *Composite*: an attribute made of sub-parts, e.g. `S.Name` → `First Name`, `Mid Name`, `Last Name`.

**Single-value / Multi-value / Null / Derived**
- *Single-valued*: one value per entity, e.g. `Phone` (as used here for a single number).
- *Multi-valued*: can hold several values, e.g. `Join Date`, `Terminate Date` on `Students`.
- *Null*: attribute has no applicable value for some entity.
- *Derived*: computed from other attributes, e.g. `Service time` (derived from Join Date and Terminate Date).

## Degree of a Relationship

How many entities participate in one relationship instance:

| Degree | Meaning | Example |
|---|---|---|
| **Binary** | 2 entities connect via one relationship | `teacher —(Teaches)— Students` |
| **Ternary** | 3 entities connect via one relationship | `Employee —(Works in)— Projects`, with `manager` as a third participant |
| **Unary / Recursive** | A single entity relates to *itself* | `employee —(Trains)— employee` (roles: `trainer`, `trainee`) |
| **N-ary** | More than 3 entities | `Employee —(Works in)— Projects`, plus `Director`/`manager` |

## Converting a Non-Binary Relationship to Binary

A ternary (or higher) relationship `R` between entities `A`, `B`, `C` can be broken into multiple binary relationships by introducing an intermediate entity:

```
Before (non-binary):        B ──R── C
                                 │
                                 A

After (converted to binary):
        B ──Rb── E ──Rc── C
                 │
                 Ra
                 │
                 A
```

A new entity `E` is introduced to represent the original relationship `R`; each original participant now connects to `E` via its own binary relationship (`Ra`, `Rb`, `Rc`).

---
## Related Notes
- [[00 - Introduction to DBMS]]
- [[02 - Relational Algebra - Unary Operations]]
