---
tags: [database, CSE3103, dbms, relational-algebra, lecture-03]
course: CSE3103 - Database
source: "CSE3103 DATABASE - 03.pdf"
---

# Relational Algebra — Unary Operations

> [!abstract] In this note
> What relational algebra is, the full list of its operations, and a deep dive into the three **unary** operations: Selection (σ), Projection (π), and Rename (ρ) — each with worked table examples.

## What is Relational Algebra?

- A **mathematical/theoretical** query language.
- Operations work on **one or more relations** (tables) and produce **another relation** — the original relation(s) are never changed.
- Used to **manipulate relations** to obtain a desired result.

## Operation Categories

| Category | Operations |
|---|---|
| **Unary** (1 relation in) | Selection, Projection, Renaming |
| **Binary** (2 relations in) | Union, Difference, Cartesian Product |
| **Extended** | Intersection, Join, Division |

This note covers **Unary** operations. Binary operations are in [[03 - Relational Algebra - Binary Operations]], and Joins get their own deep-dive in [[04 - Joins in Relational Algebra]].

---

## Selection (σ) — picking rows

- Filters a relation by a **condition** (predicate).
- Works on selecting **specific rows**.

**Syntax:**
```
σ_predicate (R)
```
- `σ` = selection (sigma)
- `predicate` = the condition
- `R` = the relation

**Comparison/logical symbols used:**
`<>` or `≠`, `≤`, `≥`, `>`, `<`, and logical `∧` (AND), `∨` (OR), `¬` (NOT)

### Example 1

**Student**

| Roll | Name | GPA |
|---|---|---|
| 01 | Arif | 3.51 |
| 02 | Rupa | 3.25 |
| 03 | Rimi | 2.56 |
| 04 | Shuvo | 3.14 |
| 05 | Sami | 2.89 |
| 06 | Poly | 3.81 |
| 07 | Keya | 3.65 |

**Question:** Find the students whose GPA is more than 3.50.

**Answer:** `σ_(gpa > 3.50) (Student)`

**Result:**

| Roll | Name | GPA |
|---|---|---|
| 01 | Arif | 3.51 |
| 06 | Poly | 3.81 |
| 07 | Keya | 3.65 |

### Example 2

**Employee**

| Name | Age | Salary (USD) |
|---|---|---|
| Marly | 24 | 9000 |
| Lucky | 40 | 3000 |
| Mark | 26 | 4500 |
| John | 42 | 3900 |

| # | Question | Answer (Relational Algebra) |
|---|---|---|
| 1 | Employees younger than 30 | `σ_(age < 30) (employee)` |
| 2 | Age < 25 **and** salary > 5000 | `σ_(age < 25 ∧ salary > 5000) (employee)` |
| 3 | Age < 25 **or** age > 40 | `σ_(age < 25 ∨ age > 40) (employee)` |
| 4 | Name starts with "mar" | `σ_(name = "mar%") (employee)` |

---

## Projection (π) — picking columns

- Shows a **specific result** by keeping only certain **columns**.
- Removes duplicate rows from the result (set semantics).

**Syntax:**
```
π_(a1, a2, …, aN) (R)
```
- `π` = projection (pi)
- `a1 … aN` = the attributes (columns) to keep
- `R` = the relation

### Example

**Staff**

| Name | Age | Salary (USD) |
|---|---|---|
| Marly | 24 | 9000 |
| Lucky | 40 | 3000 |
| Mark | 26 | 4500 |
| John | 42 | 3900 |

| # | Question | Answer | Result |
|---|---|---|---|
| 1 | Show only the names | `π_name (staff)` | Marly, Lucky, Mark, John |
| 2 | Show name & salary | `π_(name, salary) (staff)` | *(Name, Salary)* pairs |
| 3 | Show all details of staff earning > 5000 | `π_(name, age, salary) (σ_(salary > 5000) (staff))` | See below |

**Result for Q2 — `π_(name, salary) (staff)`:**

| Name | Salary(USD) |
|---|---|
| Marly | 9000 |
| Lucky | 3000 |
| Mark | 4500 |
| John | 3900 |

**Result for Q3 — `π_(name, age, salary) (σ_(salary > 5000) (staff))`:**

| Name | Age | Salary(USD) |
|---|---|---|
| Marly | 24 | 9000 |

> [!tip] Order of operations
> Q3 shows **selection inside projection**: first filter rows with `σ`, *then* pick the columns you want with `π`. This nesting pattern (`π( σ (R) )`) is extremely common in relational algebra queries.

---

## Rename (ρ) — renaming attributes or relations

Used when:
- Two tables are **not compatible** for a binary operation (e.g. different column names).
- You need to **compare a relation with itself**.

**Syntax — rename an attribute:**
```
ρ_(old_name → new_name) (Relation)
```

**Syntax — rename a whole table:**
```
ρ_(new_table) (Old_table)
```

### Example

We have an entity `Employee(name, branch, salary)`. We want to:
- Rename attribute `branch` → `location`, and `salary` → `pay`
- Rename the entity `Employee` → `Staff`

**Schema before:** `Employee(name, branch, salary)`

**Rename attributes:**
```
ρ_(branch, salary → location, pay) (Employee)
```

**Rename the table:**
```
ρ_staff (Employee)
```

**Schema after:** `Staff(name, location, pay)`

---
## Related Notes
- [[00 - Introduction to DBMS]]
- [[03 - Relational Algebra - Binary Operations]]
- [[04 - Joins in Relational Algebra]]
