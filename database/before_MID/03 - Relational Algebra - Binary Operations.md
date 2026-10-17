---
tags: [database, CSE3103, dbms, relational-algebra, lecture-04]
course: CSE3103 - Database
source: "CSE3103 DATABASE - 04.pdf"
---

# Relational Algebra — Binary Operations

> [!abstract] In this note
> Union-compatibility, then Union, Intersection, Difference, and Cartesian Product — each with fully worked before/after table examples.

## Union-Compatibility

To perform **Union**, **Intersection**, or **Difference**, the two relations must be **union-compatible**:

- Same **number of attributes** (columns).
- Attributes belong to the **same domain** (matching data types), column by column.

```
Relation X   Relation Y            Relation X   Relation Y
  A   B        A   B                 A   B         C   D

     ✅ compatible                       ❌ not compatible
```

If column names differ but types line up, you first **rename** (ρ) one relation's columns to match the other before combining them (see [[02 - Relational Algebra - Unary Operations]] for rename syntax).

---

## Union (∪)

Combines all **distinct** tuples from both relations.

**R**

| A | B |
|---|---|
| α | 1 |
| α | 2 |
| β | 1 |

**S**

| A | B |
|---|---|
| α | 2 |
| β | 3 |

**R ∪ S:**

| A | B |
|---|---|
| α | 1 |
| α | 2 |
| β | 1 |
| β | 3 |

> Note `(α, 2)` appears in **both** R and S but only shows up **once** in the union — duplicates are removed.

---

## Intersection (∩)

Keeps only tuples that appear in **both** relations.

**R**

| A | B |
|---|---|
| α | 1 |
| α | 2 |
| β | 1 |

**S**

| A | B |
|---|---|
| α | 2 |
| β | 3 |

**R ∩ S:**

| A | B |
|---|---|
| α | 2 |

Only `(α, 2)` exists in **both** R and S.

---

## Difference (−)

Keeps tuples that are in the **first** relation but **not** in the second. **Order matters** — `R − S` ≠ `S − R` in general.

**R**

| A | B |
|---|---|
| α | 1 |
| α | 2 |
| β | 1 |

**S**

| A | B |
|---|---|
| α | 2 |
| β | 3 |

**R − S** (in R, not in S):

| A | B |
|---|---|
| α | 1 |
| β | 1 |

**S − R** (in S, not in R):

| A | B |
|---|---|
| β | 3 |

---

## Cartesian Product (×)

Pairs **every** tuple of R with **every** tuple of S — no matching condition required. If `R` has `n` rows and `S` has `m` rows, `R × S` has `n × m` rows.

### Simple example

**R**

| A | B |
|---|---|
| α | 1 |
| β | 2 |
| | 3 |

**S**

| B |
|---|
| (values 1,2,3 combine with A) |

**R × S:**

| A | B |
|---|---|
| α | 1 |
| α | 2 |
| α | 3 |
| β | 1 |
| β | 2 |
| β | 3 |

### Example with a shared column name (why renaming matters)

**R**

| A | B |
|---|---|
| α | 1 |
| α | 2 |

**S**

| B | C |
|---|---|
| 1 | X |
| 2 | Y |

Both R and S have a column called `B` — a **naming clash**. Before multiplying, rename each `B` to distinguish its origin:

```
ρ_(B → R.B) (R)
ρ_(B → S.B) (S)
```

**R × S (final, with disambiguated columns):**

| A | R.B | S.B | C |
|---|---|---|---|
| α | 1 | 1 | X |
| α | 1 | 2 | Y |
| α | 2 | 1 | X |
| α | 2 | 2 | Y |

> [!warning] Cartesian product gives you *everything*, matched or not
> Notice the result includes rows like `(α, 1, 2, Y)` where `R.B ≠ S.B` — these are **meaningless combinations**. This is exactly the problem that **Join** operations solve by adding a matching condition. See [[04 - Joins in Relational Algebra]].

---

## Quick Comparison

| Operation | Symbol | Requires union-compatibility? | Result size |
|---|---|---|---|
| Union | ∪ | ✅ Yes | ≤ \|R\| + \|S\| (duplicates removed) |
| Intersection | ∩ | ✅ Yes | ≤ min(\|R\|, \|S\|) |
| Difference | − | ✅ Yes | ≤ \|R\| |
| Cartesian Product | × | ❌ No | \|R\| × \|S\| exactly |

---
## Related Notes
- [[02 - Relational Algebra - Unary Operations]]
- [[04 - Joins in Relational Algebra]]
