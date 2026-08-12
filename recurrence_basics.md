---

## tags: [discrete-math, recurrence, midterm]

# Recurrent Basics

## 1. What is a Recurrence?

A **recurrent problem** is a problem that can be solved by breaking it down into smaller, _similar_ sub-problems (same shape, smaller size).

A **recurrence relation** is an equation that defines a sequence by expressing each term using the previous term(s).

$$t_n = A\cdot t_{n-1} + B\cdot t_{n-2}$$

**Why do we care?** Many natural functions/algorithms are easiest to _define_ recursively but we want a **closed form** (a formula with no recursion, e.g. $n^2$, $2^n$) so we can compute the answer instantly instead of unwinding the recursion by hand. Recurrences are also the standard tool for analyzing the running time of recursive algorithms.

> **Mental model:** A recurrence has two parts —
> 
> 1. **General (recursive) case** — breaks the problem into a smaller version of itself.
> 2. **Base/boundary case** — stops the recursion (this is _why_ the recursion terminates).
---

## 2. The 6-Step Method for Solving Recurrent Problems

Memorize this — it is literally the algorithm you should apply to _every_ exam question.

1. **Assign appropriate notation** — name the quantity you want, e.g. $T(n)$, $S_n$, $a_n$.
2. **Find the recurrence** — express $T(n)$ in terms of smaller $T(\cdot)$.
3. **Write the recurrence relation/equation** — include the base case.
4. **Guess a solution** by computing the first few terms ($T(0), T(1), T(2), \dots$) and spotting the pattern.
5. **Break down (unfold/expand) the recurrence** to _derive_ the closed form rigorously (don't just trust the guess).
6. **Prove the solution is valid** — usually by **mathematical induction**.

---

## 3. Analyzing Recursive Algorithms — Worked Examples

### Example A — Halving recursion (just the recurrence, not solved here)

```
A(n) {
  if(condition)
    return A(n/2) + A(n/2)
}
```

If the `if`-check itself costs constant time $c$, and the function calls itself **twice** on half the input:

$$T(n) = c + 2,T(n/2)$$

This is the classic **Divide and Conquer** recurrence shape (seen again in the Sums lecture as $T(n)=aT(n/b)+cn$).

---

### Example B — Simple linear recursion: $T(n) = 1 + T(n-1)$

```
A(n) {
  if(n>1)
    return A(n-1)
}
```

Assume the `if` check costs constant time $1$.

$$ T(n)=\begin{cases}1 & n=1\ 1+T(n-1) & n>1\end{cases} $$

**Step-by-step unfolding (expansion):**

$$ \begin{aligned} T(n) &= 1+T(n-1)\ &= 1+\big[1+T(n-2)\big] = 2+T(n-2)\ &= 2+\big[1+T(n-3)\big] = 3+T(n-3)\ &;;\vdots\ &= k + T(n-k) \end{aligned} $$

**When does it stop?** It stops when the argument of $T$ hits the base case, i.e. when $n-k=1 \Rightarrow k = n-1$.

Substitute $k=n-1$:

$$ T(n) = (n-1) + T\big(n-(n-1)\big) = (n-1)+T(1) = (n-1)+1 = n $$

$$\boxed{T(n) = n}$$

_(Makes sense — the function calls itself once per unit decrease from $n$ down to $1$, i.e. $n-1$ recursive calls + 1 unit of work each = $n$.)_

![[basics-A-n-1-open-form.png]] ![[basics-A-n-1-closed-form.png]]

---

### Example C — Linear recursion with growing cost: $T(n) = n + T(n-1)$

$$ T(n)=\begin{cases}1 & n=1\ n+T(n-1) & n>1\end{cases} $$

**Unfold:**

$$ \begin{aligned} T(n) &= n+T(n-1)\ &= n+(n-1)+T(n-2)\ &= n+(n-1)+(n-2)+T(n-3)\ &;;\vdots\ &= n+(n-1)+(n-2)+\dots+(n-k)+T\big(n-(k+1)\big) \end{aligned} $$

**Stopping condition:** the argument reaches the base case when $n-(k+1)=1 \Rightarrow k = n-2$.

Substitute $k = n-2$:

$$ T(n) = n+(n-1)+(n-2)+\dots+2+T(1) = n+(n-1)+\dots+2+1 $$

This is just the sum $1+2+\dots+n$, so:

$$\boxed{T(n) = \dfrac{n(n+1)}{2}}$$

![[basics-A-n-plus-Tn1-work.png]] ![[basics-A-n-plus-Tn1-closed.png]]

**Key exam technique demonstrated:** to find _when the recursion stops_, always set the **inner argument of $T$ equal to the base-case value** and solve for $k$.

---

## 4. Recurrence Relations — General Patterns to Memorize

|Recurrence|Initial condition|Closed form|Type|
|---|---|---|---|
|$a_n = a_{n-1}+1$|$a_1=1$|$a_n = n$|Polynomial (linear)|
|$a_n = 2a_{n-1}$|$a_1=1$|$a_n = 2^n$|Exponential|
|$a_n = n,a_{n-1}$|$a_1=1$|$a_n = n!$|Factorial|

Every recurrence has:

- a **general condition** (breaks problem into smaller pieces), and
- an **initial/boundary condition** (terminates the recursion).

### Standard recurrence equation notation

$$ T(n) = T(n-1)+T(n-2), \quad n>1, \qquad T(0)=T(1)=1 $$ (the base case is written separately, or inline with a "for convenience" note).

### More recurrence "shapes" you should recognize on sight

$$ s(n)=\begin{cases}0 & n=0\ c+s(n-1) & n>0\end{cases} \qquad\Rightarrow\qquad \text{unfolds to } s(n) = cn $$

$$ s(n)=\begin{cases}0 & n=0\ n+s(n-1) & n>0\end{cases} \qquad\Rightarrow\qquad \text{unfolds to } s(n) = \dfrac{n(n+1)}{2} $$

$$ T(n)=\begin{cases}c & n=1\ 2T(n/2)+c & n>1\end{cases} \qquad (\text{Divide and Conquer, halving, e.g. Merge Sort–like}) $$

$$ T(n)=\begin{cases}c & n=1\ aT(n/b)+cn & n>1\end{cases} \qquad (\text{General D&C recurrence — Master Theorem territory}) $$

![[basics-recurrence-examples.png]]

---

## 5. Worked "Guess-and-Check" Examples (Exam Style)

### Example 1 — Find a recurrence relation and initial condition for the sequence

$$1,,5,,17,,53,,161,,485,\dots$$

**Method:** Look at the _differences_ between consecutive terms: $$5-1=4,\quad 17-5=12,\quad 53-17=36,\quad 161-53=108,\dots$$ $$\Rightarrow 4,12,36,108,\dots$$

These differences themselves grow by a factor of $3$ each time. Try relating $a_n$ directly to $a_{n-1}$ via multiplication by 3: $$1\cdot 3 = 3,\quad 5\cdot 3=15,\quad 17\cdot 3 = 51,\dots$$ Compare to the actual next terms ($5, 17, 53,\dots$): each is $2$ more than $3\times$ previous term ($3+2=5$, $15+2=17$, $51+2=53$ ✓).

$$\boxed{a_n = 3a_{n-1}+2,\qquad a_0 = 1}$$

**Verify:** for $n=1$: $a_1 = 3a_0+2 = 3(1)+2 = 5$ ✓ matches the given sequence.

---

### Example 2 — Verify a candidate closed form is a valid solution

**Claim:** $a_n = 2^n+1$ solves $a_n = 2a_{n-1}-1$ with $a_1=3$.

**Method — substitute the candidate formula into the RHS and simplify; check it equals the candidate formula for $a_n$:**

$$ \begin{aligned} 2a_{n-1}-1 &= 2(2^{,n-1}+1)-1\ &= 2^{n}+2-1\ &= 2^{n}+1\ &= a_n \quad\checkmark \end{aligned} $$

Also check base case: $a_1 = 2^1+1 = 3$ ✓. So the formula is verified.

**General technique:** to _check_ (not derive) a closed form, plug $a_{n-1}$'s formula into the recurrence's right-hand side and algebraically reduce it to $a_n$'s formula.

---

### Example 3 — Solve $a_n = a_{n-1}+n$ with $a_0 = 4$ (Telescoping Sum Method)

**Step 1 — list first few terms** to see the pattern: $$4,\ 5,\ 7,\ 10,\ 14,\ 19,\dots$$

**Step 2 — write the recurrence as a _difference_ for each index:** $$ \begin{aligned} a_1-a_0 &= 1\ a_2-a_1 &= 2\ a_3-a_2 &= 3\ &\vdots\ a_n - a_{n-1} &= n \end{aligned} $$

**Step 3 — sum both sides from index $1$ to $n$ (this is the Telescoping Sum trick).**

_Right-hand side_ (sum of $1+2+3+\dots+n$): $$1+2+3+\dots+n = \frac{n(n+1)}{2}$$

_Left-hand side_ — a **telescoping sum**: consecutive terms cancel, leaving only the first and last: $$(a_1-a_0)+(a_2-a_1)+(a_3-a_2)+\dots+(a_n-a_{n-1}) = a_n - a_0$$

> **What is a telescoping sum?** A sum where each middle term is added once and subtracted once, so it cancels out — like a collapsible telescope. Example: $(2-1)+(3-2)+(4-3)+(5-4) = 5-1 = 4$.

**Step 4 — equate both sides and solve for $a_n$:** $$a_n - a_0 = \frac{n(n+1)}{2} \quad\Rightarrow\quad a_n = \frac{n(n+1)}{2}+a_0$$

Since $a_0=4$: $$\boxed{a_n = \dfrac{n(n+1)}{2}+4}$$

![[basics-telescoping-sum.png]]

---

### Exercise 1 (from slides) — Solve $a_n = 3a_{n-1}+2$, $a_0=1$

**Answer (given):** $$\boxed{a_n = 2\cdot 3^{n} - 1}$$

**How to derive it yourself (method: unfold + geometric series), since the slide only gives "HOW?":**

$$ \begin{aligned} a_n &= 3a_{n-1}+2\ &= 3(3a_{n-2}+2)+2 = 3^2a_{n-2}+3\cdot2+2\ &= 3^2(3a_{n-3}+2)+3\cdot2+2 = 3^3a_{n-3}+3^2\cdot2+3\cdot2+2\ &;;\vdots\ &= 3^n a_0 + 2(3^{n-1}+3^{n-2}+\dots+3+1) \end{aligned} $$

The bracket is a geometric series: $3^{n-1}+\dots+1 = \dfrac{3^n-1}{3-1} = \dfrac{3^n-1}{2}$.

$$ a_n = 3^n(1) + 2\cdot\frac{3^n-1}{2} = 3^n + 3^n - 1 = 2\cdot 3^n - 1 $$

**Verify base case:** $a_0 = 2\cdot 3^0 - 1 = 2-1 = 1$ ✓. **Verify recurrence:** $3a_{n-1}+2 = 3(2\cdot3^{n-1}-1)+2 = 2\cdot3^n-3+2 = 2\cdot3^n-1 = a_n$ ✓.

> **Takeaway pattern:** any recurrence of the shape $a_n = c,a_{n-1}+d$ (constant multiplier $c$, constant additive $d$) unfolds to $$a_n = c^n a_0 + d\cdot\frac{c^n-1}{c-1} \quad (c\neq 1)$$ This is worth memorizing as a shortcut formula.

---

## 6. Quick-Reference Cheat Sheet (for exam)

|Recurrence shape|Closed form|Derivation trick|
|---|---|---|
|$T(n)=1+T(n-1),\ T(1)=1$|$T(n)=n$|unfold, find stop condition $n-k=1$|
|$T(n)=n+T(n-1),\ T(1)=1$|$T(n)=\dfrac{n(n+1)}{2}$|unfold, sum $1+2+\dots+n$|
|$a_n=a_{n-1}+n,\ a_0$ given|$a_n=\dfrac{n(n+1)}{2}+a_0$|telescoping sum|
|$a_n=c,a_{n-1}+d$|$a_n=c^na_0+d\dfrac{c^n-1}{c-1}$|unfold + geometric series|
|$T(n)=2T(n/2)+c$|(Divide & Conquer — see Master Theorem)|unfold in powers of 2|

### Verifying/Proving a closed form: two methods

1. **Direct substitution check** — plug proposed formula for $a_{n-1}$ into RHS of recurrence, simplify, confirm it equals proposed formula for $a_n$ (Example 2 above).
2. **Mathematical Induction** (rigorous proof, used heavily in the Tower of Hanoi note):
    - **Basis:** verify formula for smallest $n$.
    - **Hypothesis:** assume formula true for $n-1$ (or up to $n$).
    - **Induction step:** prove formula true for $n$ (or $n+1$) using the hypothesis and the recurrence.

---

## 7. Common Exam Pitfalls

- Forgetting to state/find the **base case** — without it your "closed form" is meaningless.
- Getting the **stopping condition** wrong when unfolding (always set inner argument $=$ base index, solve for $k$).
- Confusing **open form** (the not-yet-simplified expanded sum, e.g. $k+T(n-k)$) with **closed form** (final formula with no recursive term and no "..." ).
- When summing a telescoping difference, remember only the **first and last terms survive**.