# problem 1

Using your **Employee(Name, Age, Salary)** table:

|Name|Age|Salary (USD)|
|---|---|---|
|Marly|24|9000|
|Lucky|40|3000|
|Mark|26|4500|
|John|42|3900|

### Query 1: Employees whose age is less than 30

**Relational Algebra:**

```
σ Age < 30 (Employee)
```

**SQL:**

```sql
SELECT * FROM Employee WHERE Age < 30;
```

**Result:** Marly (24), Mark (26)

---

### Query 2: Age less than 25 **and** salary more than 5000 USD

**Relational Algebra:**

```
σ Age < 25 ∧ Salary > 5000 (Employee)
```

(∧ = AND — both conditions must be true)

**SQL:**

```sql
SELECT * FROM Employee WHERE Age < 25 AND Salary > 5000;
```

**Result:** Marly (24, 9000) → she's the only one under 25 with salary above 5000.

---

### Query 3: Age below 25 **or** higher than 40

**Relational Algebra:**

```
σ Age < 25 ∨ Age > 40 (Employee)
```

(∨ = OR — at least one condition true)

**SQL:**

```sql
SELECT * FROM Employee WHERE Age < 25 OR Age > 40;
```

**Result:** Marly (24 — below 25), John (42 — above 40). _(Lucky is exactly 40, not "higher than 40," so he's excluded.)_

---

### Query 4: Name starts with "mar"

**Relational Algebra:**

```
σ Name = "mar%" (Employee)
```

(% is the wildcard for "anything after this prefix")

**SQL:**

```sql
SELECT * FROM Employee WHERE Name LIKE 'mar%';
```

**Result:** Marly, Mark — both names start with "mar".

---

**How to solve any of these, step by step:**

1. Find the relation → `Employee`
2. Translate the English condition into a comparison (`<, >, =`, etc.)
3. If there are two conditions joined by "and" → use `∧`; joined by "or" → use `∨`
4. Wrap it as `σ condition (Relation)` for algebra, or `WHERE condition` for SQL
5. Scan the table row by row and keep the ones that satisfy the condition