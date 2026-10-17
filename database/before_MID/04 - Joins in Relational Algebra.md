---
tags: [database, CSE3103, dbms, relational-algebra, joins, lecture-05]
course: CSE3103 - Database
source: "CSE3103 DATABASE - 05.pdf"
---

# Joins in Relational Algebra

> [!abstract] In this note
> Cartesian Product vs Join, then **Natural Join**, **Theta Join**, **Left/Right/Full Outer Join** — every type explained with the *original* tables, the operation used, and exactly **what the resulting table looks like**, using both the lecture's Bank/Faculty examples and a few extra worked examples for practice.

## Cartesian Product vs Join — When to Use Which

| | **Cartesian Product** | **Join** |
|---|---|---|
| Data set size | Small | Large |
| Number of columns | Not many | Can be many |
| Common columns | None needed (can share if present) | At least **one** common column required to perform the operation |
| Unused rows | Many rows returned are unused/meaningless | Only **useful** rows are returned |
| Renaming | Need to rename columns if they share a name | No need to rename — matching columns are merged automatically |

In short: **Cartesian Product = multiply everything together, filter later. Join = multiply and filter (match) in one step, keeping only rows that make sense.**

---

## Natural Join (⋈)

**Definition:** Combines two relations by matching on **all attributes that share the same name**, keeping only one copy of each shared column.

**Syntax:** `R ⋈ S`

### Worked Example 1 — Faculty & Head

**Faculty**

| Name | Department |
|---|---|
| Smith | CSE |
| John | EEE |
| Paul | EEE |

**Head**

| Department | Head |
|---|---|
| EEE | Brown |
| CSE | Alen |
| MCE | White |

**Operation:** `Faculty ⋈ Head`

Both tables share the column `Department`, so the natural join matches rows **where `Department` is equal**, and merges them into one row (the shared column appears only once).

**Result table:**

| Name | Department | Head |
|---|---|---|
| Smith | CSE | Alen |
| John | EEE | Brown |
| Paul | EEE | Brown |

> [!note] Dangling Tuple
> Any row that is **lost** during a join (because it had no match on the other side) is called a **dangling tuple**. Natural join silently drops these — that's exactly the behaviour Outer Joins are designed to fix (see below).

### Worked Example 2 — Bank Scenario (Borrower & Loan)

**Question:** Find the names of customers of a bank who have a loan at the **Gulshan** branch.

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

**Operation:** `Borrower ⋈ Loan`

Only `Loan_no` (Borrower) matches `Loan_No` (Loan) — here only `L-23` exists in **both** tables, so only Mr. Jamal's row survives the join.

**Result table:**

| Name | Loan_No | Branch | Amount |
|---|---|---|---|
| Mr. Jamal | L-23 | Gulshan | 50,000 |

**Final answer** — filter by branch, then project just the name:
```
π_name (σ_(Branch = "Gulshan") (result_table))
```
which is the same as:
```
π_name (σ_(Branch = "Gulshan") (Borrower ⋈ Loan))
```
Both forms are correct — the second skips naming the intermediate `result_table`.

### Compare: Same Data via Cartesian Product

This is the earlier ("use Cartesian product") version of the *same* bank question — shown so you can see exactly what a join saves you from.

**Operation:** `Borrower × Loan`, with columns renamed first (`ρ_(Loan_No → Borrow.Loan_No)(Borrower)`, `ρ_(Loan_No → Loan.Loan_No)(Loan)`) since both tables have a `Loan_No`-type column.

**Result_Table (Cartesian Product — all combinations):**

| Name | Borrow.Loan_no | Loan.Loan_No | Branch | Amount |
|---|---|---|---|---|
| Mr. Kamal | L-17 | L-14 | Banani | 75,000 |
| Mr. Kamal | L-17 | L-23 | Gulshan | 50,000 |
| Mr. Jamal | L-23 | L-14 | Banani | 75,000 |
| Mr. Jamal | L-23 | L-23 | Gulshan | 50,000 |

Then you'd need **two extra conditions** to get the correct answer:
```
Borrow.Loan_No = Loan.Loan_No   AND   Branch = "Gulshan"

π_name (σ_(Borrow.Loan_No = Loan.Loan_No ∧ Branch = "Gulshan") (result_table))
```
Notice this produces the **same final answer** (`Mr. Jamal`) as the Natural Join — but you had to manually write the matching condition (`Borrow.Loan_No = Loan.Loan_No`) yourself. **Natural Join does that matching automatically**, which is the whole point of using a join instead of a raw Cartesian product.

---

## Theta Join (⋈θ)

**Definition:** A join that uses a **comparison operator** (not necessarily `=`) as the matching condition — e.g. `>`, `<`, `≥`, `≤`, `≠` as well as `=`.

**Notation:**
```
(R ⋈ S)θ
   or
σ_condition (R × S)
```

### Worked Example

**Schemas:**
- `Depositor(Customer_no, Name, Account_No)`
- `Account(Account_no, Branch, Balance)`

**Question:** Find the names of customers who have an account with balance greater than 5000.

**Condition:** `Balance > 5000`

**Answer (both forms are correct):**
```
π_name (σ_(Balance > 5000) (Depositor ⋈ Account))
π_name (Depositor ⋈ Account) Balance > 5000
```

> [!tip] Theta Join vs Natural Join vs Equijoin
> - **Natural Join** matches on *all* identically-named columns using `=`.
> - **Theta Join** matches using *any* comparison operator (`>`, `<`, `=`, etc.) on a condition you specify explicitly.
> - An **Equijoin** is simply a Theta Join where the condition happens to use `=` — it's the most common special case of a theta join.

---

## Outer Joins — Keeping the Dangling Tuples

A **Natural/Theta (inner) Join** throws away rows that don't find a match. **Outer Joins** keep them, filling the missing side with **NULL**.

| Type | Symbol | Behaviour |
|---|---|---|
| **Left Outer Join** | ⟕ | Keeps **all** rows from the **left** table. Matching data comes from the right table; unmatched → **NULL** on the right side. |
| **Right Outer Join** | ⟖ | Keeps **all** rows from the **right** table. Matching data comes from the left table; unmatched → **NULL** on the left side. |
| **Full Outer Join** | ⟗ | Combines Left Outer Join **and** Right Outer Join — keeps everything from both sides, NULL-filling wherever there's no match. |

We'll use the same two tables throughout to see the difference clearly:

**Faculty**

| Name | Department |
|---|---|
| Smith | CSE |
| John | EEE |
| Paul | BBA |

**Head**

| Department | Head |
|---|---|
| EEE | Brown |
| CSE | Alen |
| MCE | White |

Notice: `Faculty` has a department `BBA` that `Head` doesn't have, and `Head` has a department `MCE` that `Faculty` doesn't have. This mismatch is exactly what makes the three outer joins behave differently.

### Left Outer Join — `Faculty ⟕ Head`

Priority on the **left** table (`Faculty`) — every Faculty row is kept, even `Paul` (BBA), which has **no match** in `Head`.

| Name | Department | Head |
|---|---|---|
| Smith | CSE | Alen |
| John | EEE | Brown |
| Paul | BBA | **NULL** |

`MCE`/`White` from `Head` is **dropped** — it's not in the left table.

### Right Outer Join — `Faculty ⟖ Head`

Priority on the **right** table (`Head`) — every Head row is kept, even `MCE`/`White`, which has **no match** in `Faculty`.

| Name | Department | Head |
|---|---|---|
| John | EEE | Brown |
| Smith | CSE | Alen |
| **NULL** | MCE | White |

`Paul`/`BBA` from `Faculty` is **dropped** — it's not in the right table.

### Full Outer Join — `Faculty ⟗ Head`

Keeps **everything** from both sides — nothing is dropped, NULLs fill in wherever there's no partner.

| Name | Department | Head |
|---|---|---|
| Smith | CSE | Alen |
| John | EEE | Brown |
| Paul | BBA | **NULL** |
| **NULL** | MCE | White |

> [!summary] The pattern to remember
> - **Inner/Natural Join** → only matching rows survive.
> - **Left** → all of left + matches from right (NULL if none).
> - **Right** → all of right + matches from left (NULL if none).
> - **Full** → all of both + NULL wherever there's no partner.

---

## Extra Practice Example (Employees / Departments)

To cement the pattern, here's a fresh example not from the slides — same logic, different data.

**Employee**

| EmpID | EmpName | DeptID |
|---|---|---|
| 1 | Rina | D1 |
| 2 | Tanvir | D2 |
| 3 | Farhan | D4 |

**Department**

| DeptID | DeptName |
|---|---|
| D1 | Sales |
| D2 | IT |
| D3 | HR |

Note: Employee has `DeptID = D4`, which doesn't exist in Department. Department has `DeptID = D3`, which no employee belongs to.

| Join type | Operation | Result |
|---|---|---|
| **Natural Join** | `Employee ⋈ Department` | `(1, Rina, D1, Sales)`, `(2, Tanvir, D2, IT)` — Farhan (D4) and HR (D3) are both dropped, since neither has a match. |
| **Left Outer Join** | `Employee ⟕ Department` | Same 2 matched rows **plus** `(3, Farhan, D4, NULL)` — every employee is kept. |
| **Right Outer Join** | `Employee ⟖ Department` | Same 2 matched rows **plus** `(NULL, NULL, D3, HR)` — every department is kept. |
| **Full Outer Join** | `Employee ⟗ Department` | All 4 rows: the 2 matches, `(3, Farhan, D4, NULL)`, **and** `(NULL, NULL, D3, HR)`. |

---
## Related Notes
- [[03 - Relational Algebra - Binary Operations]] — Cartesian Product, Union, Intersection, Difference
- [[05 - Aggregate Functions and Assignment Operation]]
