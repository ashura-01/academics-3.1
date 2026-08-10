---
tags: [database, CSE3103, dbms, relational-algebra, lecture-06]
course: CSE3103 - Database
source: "CSE3103 DATABASE - 06.pdf"
---

# Aggregate Functions & the Assignment Operation

> [!abstract] In this note
> Aggregate functions (Max, Min, Sum, Count, Average, group-by), and the Assignment Operation used to break a complex query into simple sequential steps.

## Aggregate Functions

Take a **collection of values** (from the same attribute/domain) and return a **single** value as a result.

**Notation:**
```
_G F(A) (R)
```

| Symbol | Meaning |
|---|---|
| `G` (subscript, before `F`) | **Group By** attribute(s) |
| `F` | **Function** to apply (SUM, COUNT, AVG, …) |
| `A` | **Attribute** the function is applied to |
| `R` | **Relation** |
| `𝒢` (script G before the whole expression) | Aggregation symbol |

**Commonly used functions:** `Max`, `Min`, `Sum`, `Count`, `Count-distinct`, `Average`, etc.

### Worked Example

**Instructor**

| ID | Name | Department | Salary |
|---|---|---|---|
| 11 | Alen | CSE | 25000 |
| 23 | Brown | EEE | 25000 |
| 54 | Cook | EEE | 35000 |
| 29 | Dawson | MCE | 10000 |
| 45 | Emly | CEE | 15000 |
| 56 | Frank | CSE | 40000 |
| 67 | Givson | MCE | 30000 |

| # | Question | Relational Algebra |
|---|---|---|
| 1 | Total salary of all instructors | `𝒢 SUM(Salary) (Instructor)` |
| 2 | How many instructors are there? | `𝒢 COUNT(ID) (Instructor)` |
| 3 | How many distinct departments are there? | `𝒢 COUNT-DISTINCT(department) (Instructor)` |
| 4 | Average salary **per department** | `Department 𝒢 AVERAGE(Salary) (Instructor)` |

For Q4, `Department` sits to the *left* of `𝒢` — meaning the instructors are first **grouped by department**, and the average is computed **within each group**, e.g.:

| Department | AVERAGE(Salary) |
|---|---|
| CSE | 32500 |
| EEE | 30000 |
| MCE | 20000 |
| CEE | 15000 |

---

## Assignment Operation (←)

- Provides a convenient way to express **complex queries**.
- Write the query as a **sequential program**: a series of assignments, followed by a final expression whose value is the displayed result.
- Assignment is always made to a **temporary relation variable**.

**Notation:** `←`

### Worked Example — Bank Scenario Revisited

**Borrower**

| Name | Loan_no |
|---|---|
| Mr. Kamal | L-17 |
| Mr. Jamal | L-23 |

**Loan**

| Loan_No | Branch | Amount |
|---|---|---|
| L-14 | Banani | 75,000 |
| L-23 | Gulshan | 50,000 |

**Question:** Find the names of customers of a bank who have a loan at the Gulshan branch.

**Step-by-step assignment version:**
```
ρ_(Loan_No → Borrow.Loan_No) (Borrower)
ρ_(Loan_No → Loan.Loan_No) (Loan)

Temp1 ← Borrower × Loan
Temp2 ← Borrow.Loan_No = Loan.Loan_No
Temp3 ← Branch = "Gulshan"

π_name (σ_(Temp2 ∧ Temp3) (Temp1))
```

This produces exactly the same result as the single-expression version from [[04 - Joins in Relational Algebra]] — Mr. Jamal, Loan `L-23`, Gulshan, 50,000 — but broken into readable, reusable steps. This is especially useful for **long queries with many conditions**, where a single nested expression would become hard to read.

---
## Related Notes
- [[04 - Joins in Relational Algebra]]
- [[03 - Relational Algebra - Binary Operations]]
