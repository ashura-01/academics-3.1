# Database Symbols — What Each One Means (with Examples & Worked Solutions)

This guide covers two symbol systems from the CSE3103 Database slides:
1. **ER Diagram Symbols** (used to draw Entity-Relationship diagrams)
2. **Relational Algebra Symbols** (used to write queries mathematically)

For each symbol: **what it means → example → step-by-step problem solve.**

---

## PART 1: ER DIAGRAM SYMBOLS

| Symbol / Shape | Meaning |
|---|---|
| **Rectangle** ▭ | **Entity** — a real object from the problem (e.g., Student, Course, Department) |
| **Diamond** ◇ | **Relationship** — the verb/interaction connecting two entities (e.g., Enrolls, Teaches) |
| **Ellipse** ⬭ | **Attribute** — a property of an entity or relationship (e.g., Name, ID, DOB) |
| **Double Line** ═ | **Total Participation** — every entity instance MUST take part in the relationship |
| **Single Line** ─ | **Partial Participation** — an entity instance MAY or may not take part |
| **Underline** (under attribute name) | Marks the **Primary Key** |

### Cardinality Notations
| Notation | Meaning |
|---|---|
| `1 : 1` | One-to-One — one entity relates to exactly one of another |
| `1 : m` | One-to-Many — one entity relates to many of another |
| `m : 1` | Many-to-One — many entities relate to one of another |
| `m : n` | Many-to-Many — many entities relate to many of another |

### Worked Example: Identifying Entities, Relationships & Attributes
**Problem:** "AUST has eight departments. Students are admitted into departments, and enrolled in courses within a semester, receiving grades."

**Step-by-step solve:**
1. **Find the nouns → these become Entities:** `Student`, `Department`, `Course`
2. **Find the verbs connecting entities → these become Relationships:**
   - `Department --Admit--> Student`
   - `Student --Enroll--> Course`
3. **Find descriptive properties → these become Attributes:**
   - Department: Name, HoD, Accreditation
   - Student: S.ID (primary key), S.Name, DOB, Phone, Blood Group
   - Course: C.ID (primary key), C.Name, Credit
   - Admit (relationship attributes): Session, Quota
   - Enroll (relationship attributes): Semester, Grade
4. **Draw it:** Rectangles for Student/Department/Course → Diamonds for Admit/Enroll connecting them → Ellipses branching off each rectangle/diamond for their attributes → underline S.ID and C.ID as primary keys.

---

## PART 2: RELATIONAL ALGEBRA SYMBOLS

### Quick Reference Table

| Symbol               | Name                 | Meaning                                               |
| -------------------- | -------------------- | ----------------------------------------------------- |
| **σ** (sigma)        | Selection            | Picks specific **rows** matching a condition          |
| **π** (pi)           | Projection           | Picks specific **columns**                            |
| **ρ** (rho)          | Rename               | Renames a relation or attribute                       |
| **∪**                | Union                | Combines rows from two relations (no duplicates)      |
| **∩**                | Intersection         | Rows common to both relations                         |
| **−**                | Difference           | Rows in one relation but not the other                |
| **×**                | Cartesian Product    | Combines every row of R with every row of S           |
| **⋈**                | Natural Join         | Combines relations on matching common attribute(s)    |
| **⋈ θ**              | Theta Join           | Join using a comparison condition (θ = <, >, =, etc.) |
| **⟕**                | Left Outer Join      | Keeps all rows from the left table, NULL if no match  |
| **⟖**                | Right Outer Join     | Keeps all rows from the right table, NULL if no match |
| **⟗**                | Full Outer Join      | Keeps all rows from both tables, NULL where no match  |
| **G / 𝒢**           | Aggregation symbol   | Used with group-by + aggregate functions              |
| **←**                | Assignment           | Stores a query result into a temporary relation       |
| **<, >, ≤, ≥, ≠, =** | Comparison operators | Used inside selection conditions                      |
| **∧ (˄)**            | Logical AND          | Combines conditions — both must be true               |
| **∨ (˅)**            | Logical OR           | Combines conditions — at least one true               |
| **¬ (˥)**            | Logical NOT          | Negates a condition                                   |
| **÷**                | Division             | Finds rows in R related to ALL rows in S              |

---

### 1. σ — Selection (filters ROWS)

**Meaning:** Picks out rows (tuples) from a relation that satisfy a condition. Works like a `WHERE` clause.

**Syntax:** `σ condition (Relation)`

**Symbols used inside:** `<, >, ≤, ≥, ≠, =` for comparisons, and `∧ (AND), ∨ (OR), ¬ (NOT)` to combine conditions.

**Example table — Student(Roll, Name, GPA):**

| Roll | Name | GPA |
|---|---|---|
| 01 | Arif | 3.51 |
| 02 | Rupa | 3.25 |
| 03 | Rimi | 2.56 |
| 04 | Shuvo | 3.14 |
| 05 | Sami | 2.89 |
| 06 | Poly | 3.81 |
| 07 | Keya | 3.65 |

**Problem:** Find the students whose GPA is more than 3.50.

**Step-by-step solve:**
1. Identify the target relation → `Student`
2. Identify the condition → `GPA > 3.50`
3. Apply selection: `σ GPA > 3.50 (Student)`
4. Scan every row and keep only those where GPA > 3.50 → Roll 01 (3.51), 06 (3.81), 07 (3.65)

**Result:**

| Roll | Name | GPA |
|---|---|---|
| 01 | Arif | 3.51 |
| 06 | Poly | 3.81 |
| 07 | Keya | 3.65 |

**Second example — combining conditions with ∧ / ∨:**
Given `Employee(Name, Age, Salary)`, solve:
1. Employees younger than 30 → `σ Age < 30 (Employee)`
2. Employees younger than 25 **and** earning more than 5000 → `σ Age < 25 ∧ Salary > 5000 (Employee)` (both conditions must hold — use ∧)
3. Employees younger than 25 **or** older than 40 → `σ Age < 25 ∨ Age > 40 (Employee)` (either condition — use ∨)
4. Employees whose name starts with "mar" → `σ Name = "mar%" (Employee)`

---

### 2. π — Projection (filters COLUMNS)

**Meaning:** Picks out specific columns (attributes) from a relation, like `SELECT column1, column2`.

**Syntax:** `π a1, a2, …, aN (Relation)`

**Example table — Staff(Name, Age, Salary):**

| Name | Age | Salary |
|---|---|---|
| Marly | 24 | 9000 |
| Lucky | 40 | 3000 |
| Mark | 26 | 4500 |
| John | 42 | 3900 |

**Problem:** Show only the names and salaries of staff earning more than 5000.

**Step-by-step solve:**
1. First filter the rows (selection) → `σ Salary > 5000 (Staff)` → keeps only Marly (9000)
2. Then pick the required columns (projection) around that result → `π Name, Age, Salary (σ Salary > 5000 (Staff))`
3. Combine inner-to-outer: selection runs first, then projection narrows the columns shown.

**Result:**

| Name | Age | Salary |
|---|---|---|
| Marly | 24 | 9000 |

---

### 3. ρ — Rename

**Meaning:** Renames a relation or an attribute — useful when two relations aren't directly compatible for an operation, or you need to compare a relation with itself.

**Syntax:**
- Rename an attribute: `ρ oldname → newname (Relation)`
- Rename a relation: `ρ newtable (Oldtable)`

**Problem:** Employee(name, branch, salary) → rename `branch` to `location`, `salary` to `pay`, and rename the whole table to `Staff`.

**Step-by-step solve:**
1. Rename the attributes: `ρ branch, salary → location, pay (Employee)`
2. Rename the table: `ρ Staff (Employee)`
3. Result schema: `Staff(name, location, pay)`

---

### 4. ∪, ∩, − — Union, Intersection, Difference (Binary Set Operations)

**Meaning:** Operate on two relations at once, like set operations. **Requirement:** the two relations must be **union-compatible** — same number of columns, same domain types.

**Given relations R(A,B) and S(A,B):**

R:

| A | B |
|---|---|
| α | 1 |
| α | 2 |
| β | 1 |

S:

| A | B |
|---|---|
| α | 2 |
| β | 3 |

**∪ Union — R ∪ S: all rows from both, duplicates removed**

Step-by-step: List all rows from R, then add rows from S that aren't already there.
Result:

| A | B |
|---|---|
| α | 1 |
| α | 2 |
| β | 1 |
| β | 3 |

**∩ Intersection — R ∩ S: rows appearing in BOTH**

Step-by-step: Compare row by row; keep only rows present in both R and S.
Result:

| A | B |
|---|---|
| α | 2 |

**− Difference — R − S: rows in R but NOT in S**

Step-by-step: Go through R's rows; remove any row that also appears in S.
Result:

| A | B |
|---|---|
| α | 1 |
| β | 1 |

(Note: `S − R` gives a different answer — order matters for difference.)

---

### 5. × — Cartesian Product

**Meaning:** Pairs every row of R with every row of S (no matching condition needed) — result has (rows of R × rows of S) rows and (columns of R + columns of S) columns.

**Problem:** Borrower(Name, Loan_no) × Loan(Loan_No, Branch, Amount)

**Step-by-step solve:**
1. Because both relations have a column named `Loan_no`/`Loan_No`, rename first to avoid ambiguity: `ρ Loan_No → Borrow.Loan_No (Borrower)` and `ρ Loan_No → Loan.Loan_No (Loan)`
2. Multiply: `Borrower × Loan` → pair every Borrower row with every Loan row
3. Since this produces many irrelevant combinations, filter afterward with σ and project with π to get a meaningful answer (see Theta Join below for the cleaner way).

---

### 6. ⋈ — Natural Join

**Meaning:** Combines two relations automatically on their common attribute(s) — much more efficient than Cartesian Product + selection, and no need to rename common columns.

**Syntax:** `R ⋈ S`

**Example — Faculty(Name, Department) and Head(Department, Head):**

Faculty:

| Name | Department |
|---|---|
| Smith | CSE |
| John | EEE |
| Paul | EEE |

Head:

| Department | Head |
|---|---|
| EEE | Brown |
| CSE | Alen |
| MCE | White |

**Problem:** Find each faculty member's department head.

**Step-by-step solve:**
1. Identify the common attribute → `Department` (this is the automatic join key)
2. Join: `Faculty ⋈ Head`
3. For each Faculty row, match it to the Head row with the same Department value; rows with no match are dropped (a dropped row is called a **dangling tuple**)

**Result:**

| Name | Department | Head |
|---|---|---|
| Smith | CSE | Alen |
| John | EEE | Brown |
| Paul | EEE | Brown |

---

### 7. ⋈ θ — Theta Join

**Meaning:** Like natural join, but the matching condition (θ) can be any comparison, not just equality — combines a join with a selection condition.

**Syntax:** `(R ⋈ S)θ` or equivalently `σ condition (R × S)`

**Problem:** Depositor(Customer_no, Name, Account_No) and Account(Account_no, Branch, Balance). Find names of customers with account balance greater than 5000.

**Step-by-step solve:**
1. Join the two relations on their common key: `Depositor ⋈ Account`
2. Apply the condition: `Balance > 5000`
3. Project only the `name` column: `π Name (σ Balance > 5000 (Depositor ⋈ Account))`
4. (Equivalently written as: `π Name (Depositor ⋈ Account) Balance > 5000` — both forms are correct.)

---

### 8. ⟕ ⟖ ⟗ — Outer Joins

**Meaning:** Like natural join, but instead of dropping unmatched rows, they're kept and padded with `NULL`.

| Symbol | Type | Keeps unmatched rows from |
|---|---|---|
| ⟕ | Left Outer Join | Left table |
| ⟖ | Right Outer Join | Right table |
| ⟗ | Full Outer Join | Both tables |

**Example — Faculty(Name, Department) and Head(Department, Head):**

Faculty:

| Name | Department |
|---|---|
| Smith | CSE |
| John | EEE |
| Paul | BBA |

Head:

| Department | Head |
|---|---|
| EEE | Brown |
| CSE | Alen |
| MCE | White |

**Left Outer Join — `Faculty ⟕ Head`:**
Step-by-step: Keep every Faculty row. For each, find a matching Head; if `BBA` has no match, fill Head with `NULL`.

Result:

| Name | Department | Head |
|---|---|---|
| Smith | CSE | Alen |
| John | EEE | Brown |
| Paul | BBA | NULL |

**Right Outer Join — `Faculty ⟖ Head`:**
Step-by-step: Keep every Head row. `MCE` has no matching Faculty, so Name is `NULL`.

Result:

| Name | Department | Head |
|---|---|---|
| John | EEE | Brown |
| Smith | CSE | Alen |
| NULL | MCE | White |

**Full Outer Join — `Faculty ⟗ Head`:**
Step-by-step: Keep all rows from both sides; pad `NULL` wherever there's no match on either side (both `BBA` and `MCE` appear, each with one `NULL` side).

---

### 9. G / 𝒢 — Aggregation Symbol

**Meaning:** Applies an aggregate function (SUM, COUNT, AVG, MAX, MIN, COUNT-DISTINCT) to a relation, optionally grouped by an attribute.

**Syntax:** `Grouping_Attribute 𝒢 Function(Attribute) (Relation)`

**Example table — Instructor(ID, Name, Department, Salary):**

| ID | Name | Department | Salary |
|---|---|---|---|
| 11 | Alen | CSE | 25000 |
| 23 | Brown | EEE | 25000 |
| 54 | Cook | EEE | 35000 |
| 29 | Dawson | MCE | 10000 |
| 45 | Emly | CEE | 15000 |
| 56 | Frank | CSE | 40000 |
| 67 | Givson | MCE | 30000 |

**Problem 1:** Find the total salary of all instructors.
**Step-by-step:** No grouping needed (whole table) → apply SUM on Salary → `𝒢 SUM(Salary) (Instructor)`

**Problem 2:** How many instructors are there?
**Step-by-step:** Count the ID column across the whole relation → `𝒢 COUNT(ID) (Instructor)`

**Problem 3:** How many distinct departments exist?
**Step-by-step:** Count unique values of Department → `𝒢 COUNT-DISTINCT(Department) (Instructor)`

**Problem 4:** Find the average salary **per department**.
**Step-by-step:**
1. This needs grouping, so put the grouping attribute before `𝒢` → `Department`
2. Apply AVERAGE on Salary → `Department 𝒢 AVERAGE(Salary) (Instructor)`
3. Conceptually: split rows into groups by Department (CSE, EEE, MCE, CEE), then average Salary within each group.

---

### 10. ← — Assignment

**Meaning:** Stores the result of a sub-query into a temporary relation, so a complex query can be broken into simple sequential steps.

**Example — Borrower(Name, Loan_no) and Loan(Loan_No, Branch, Amount):**

**Problem:** Find the names of customers who have a loan at the Gulshan branch.

**Step-by-step solve using assignment:**
1. Rename common columns to avoid ambiguity:
   `ρ Loan_No → Borrow.Loan_No (Borrower)`
   `ρ Loan_No → Loan.Loan_No (Loan)`
2. Store the Cartesian product in a temp relation:
   `Temp1 ← Borrower × Loan`
3. Store the join condition as a temp condition:
   `Temp2 ← Borrow.Loan_No = Loan.Loan_No`
4. Store the branch condition as another temp condition:
   `Temp3 ← Branch = "Gulshan"`
5. Combine everything with selection + projection:
   `π Name (σ Temp2 ∧ Temp3 (Temp1))`

This breaks one complex nested query into 5 readable steps — same final result as writing it in a single line.

---

### 11. ÷ — Division (mentioned as an Extended Operation)

**Meaning:** Used when a question asks "find X that is related to **ALL** of Y" (e.g., "find students enrolled in every course"). It divides relation R by relation S, returning rows of R that pair with every row in S.

**When to use it:** Whenever the problem uses the word "**all**" or "**every**" — that's the signal to reach for ÷ instead of a plain join/selection.

---

## Cheat-Sheet: How to Recognize Which Operation to Use

| If the question asks... | Use this symbol |
|---|---|
| "Find rows where..." | σ (selection) |
| "Show only these columns..." | π (projection) |
| "Rename this column/table..." | ρ |
| "Combine rows from two tables (all of them)" | ∪ |
| "Rows common to both" | ∩ |
| "Rows in A but not B" | − |
| "Every combination of two tables" | × |
| "Combine tables using their shared column" | ⋈ |
| "Combine tables using a comparison (>, <...)" | ⋈ θ |
| "Keep unmatched rows from left/right/both" | ⟕ / ⟖ / ⟗ |
| "Total, count, average, max, min..." | G / 𝒢 |
| "Break a complex query into steps" | ← |
| "Related to ALL / every..." | ÷ |
