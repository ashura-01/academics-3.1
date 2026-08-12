---
tags: [discrete-math, recurrence, tower-of-hanoi, midterm]
---
# Recurrent Problems: The Tower of Hanoi

Lecture also mentions **Lines in the Plane** and **The Josephus Problem** as sibling topics (not detailed in this deck — check separate notes if provided).



## 1. About the Puzzle

The **Tower of Hanoi** (also _Tower of Brahma_ / _Lucas' Tower_) is a puzzle with:

- **3 rods**
- **$n$ disks** of different sizes, stacked in ascending size (smallest on top) on one rod, forming a cone.

![[toh-diagram.png]]

**Rules:**

1. Only **one disk** may be moved at a time.
2. Each move takes the **top disk** of one rod and places it on **top** of another rod's stack.
3. **No disk may be placed on a smaller disk.**

**Goal:** Move the entire stack from the starting rod to another rod.

With 3 disks, the puzzle takes **7 moves** minimum.

![[toh-3disks-moves.png]]

### Origins (background/trivia — may appear as a short-answer question)

- Publicized in the West by French mathematician **Édouard Lucas**, 1883.
- Legend: an Indian temple (Kashi Vishwanath) has 3 posts and 64 golden disks; when priests finish moving all 64 disks (following the rules), the world will end.
- If priests move 1 disk/second using the optimal number of moves: $2^{64}-1$ seconds $\approx 585$ billion years — far longer than the age of the universe (about 5× longer).

---

## 2. Deriving the Recurrence (Step 1–3 of the general method)

**Key insight (recursive decomposition):** To move $n$ disks from peg A to peg C using peg B as auxiliary:

1. Move the top $n-1$ disks from **A → B** (using C as auxiliary). This leaves the largest disk ($n$) alone on A.
2. Move disk $n$ from **A → C** (1 move).
3. Move the $n-1$ disks from **B → C** (using A as auxiliary), stacking them on top of disk $n$.

**Why this works:** Steps 1 and 3 are themselves smaller Tower-of-Hanoi problems (same rules, $n-1$ disks) — this is exactly what makes it _recurrent_.

Let $T_n$ = minimum number of moves for $n$ disks.

- Step 1 costs $T_{n-1}$ moves.
- Step 2 costs $1$ move.
- Step 3 costs $T_{n-1}$ moves.

$$T_n = T_{n-1} + 1 + T_{n-1} = 2T_{n-1}+1$$

**Base case:** $0$ disks require $0$ moves: $T_0 = 0$.

$$ \boxed{T_n = \begin{cases}0 & n=0\ 2T_{n-1}+1 & n>0\end{cases}} $$

![[basics-recurrence-examples.png]] _(General recurrence-writing pattern — same idea as previous note.)_

---

## 3. Guessing the Pattern (Step 4)

Compute successively:

$$ \begin{aligned} T_0 &= 0 \quad(\text{no move required})\ T_1 &= 2(0)+1 = 1\ T_2 &= 2(1)+1 = 3\ T_3 &= 2(3)+1 = 7\ T_4 &= 2(7)+1 = 15\ T_5 &= 2(15)+1 = 31 \end{aligned} $$

Pattern: $1, 3, 7, 15, 31, \dots$ — each is one less than a power of 2!

$$\text{Guess: } T_n = 2^n - 1, \quad n>0$$

_(Still just a guess — must be derived rigorously and then proven — Steps 5 & 6.)_

---

## 4. Deriving the Closed Form Rigorously (Step 5 — Unfolding)

Start from the recurrence $T_n = 2T_{n-1}+1$ and substitute repeatedly:

$$ \begin{aligned} T_n &= 2T_{n-1}+1\ &= 2(2T_{n-2}+1)+1 = 2^2T_{n-2}+2+1\ &= 2^2(2T_{n-3}+1)+2+1 = 2^3T_{n-3}+2^2+2+1\ &;;\vdots\ &= 2^n T_{n-n} + 2^{(n-1)}+2^{(n-2)}+\dots+2^2+2+1\ &= 2^n T_0 + 2^{(n-1)}+2^{(n-2)}+\dots+2^1+2^0 \end{aligned} $$

Since $T_0 = 0$, the first term vanishes:

$$T_n = 2^0+2^1+2^2+\dots+2^{(n-1)}$$

This is a **geometric series** with ratio $2$, first term $1$, $n$ terms. Use the geometric series sum formula: $$a^0+a^1+a^2+\dots+a^n = \frac{a^{n+1}-1}{a-1}$$ Here we're summing up to exponent $n-1$ (i.e., $n$ terms of ratio 2), so:

$$T_n = \frac{2^{n}-1}{2-1} = 2^n - 1$$

$$\boxed{T_n = 2^n - 1 \quad \text{— the closed form solution of Tower of Hanoi}}$$

![[toh-closed-form-derivation.png]]

**Sanity check against Step 3's computed values:** $T_1=2^1-1=1$ ✓, $T_2=2^2-1=3$ ✓, $T_3=2^3-1=7$ ✓, $T_4=2^4-1=15$ ✓, $T_5=2^5-1=31$ ✓. All match!

---

## 5. Proving the Closed Form by Mathematical Induction (Step 6)

**Mathematical induction** has 3 parts:

- **Basis:** prove formula true for the smallest value.
- **Hypothesis:** assume formula is true for $n$ (or up to $n$).
- **Induction:** prove formula true for $n+1$ using the hypothesis + the recurrence.

### Formal Proof for $T_n = 2^n - 1$

**Basis:** $T_0 = 2^0 - 1 = 1-1 = 0$. This matches the given base case $T_0=0$. ✓

**Hypothesis:** Assume $T_n = 2^n - 1$ is true (for some general $n$).

**Induction step:** Show $T_{n+1} = 2^{n+1}-1$ using the recurrence $T_{n+1}=2T_n+1$ and the hypothesis:

$$ \begin{aligned} T_{n+1} &= 2T_n + 1\ &= 2(2^n - 1) + 1 \qquad \text{(substituting the hypothesis)}\ &= 2^{n+1} - 2 + 1\ &= 2^{n+1} - 1 \qquad \checkmark \end{aligned} $$

This is exactly the formula with $n$ replaced by $n+1$, so by induction the formula holds for **all** $n \geq 0$. $\blacksquare$

![[toh-induction-proof.png]]

**Alternate phrasing seen in the slide (equivalent, shifting index by one):**

- Hypothesis: $T_{n-1} = 2^{n-1}-1$
- Induction: $T_n = 2T_{n-1}+1 = 2(2^{n-1}-1)+1 = 2^n - 2 + 1 = 2^n - 1$ (Proved)

Both versions prove the same thing — just indexed differently. Use whichever the exam phrasing matches.

---

## 6. Alternative Derivation: Converting the Recurrence into a Sum (Summation-Factor Method)

This is an important **alternative technique** (also covered in the Sums lecture) — useful when direct unfolding is messy.

### 6.1 The Trick: Multiply by a "Summation Factor"

Start again with: $$T_n = 2T_{n-1}+1, \qquad T_0 = 0$$

**Multiply both sides by the summation factor $2^{-n}$ (i.e. $\frac{1}{2^n}$):**

$$ \frac{T_n}{2^n} = \frac{2T_{n-1}}{2^n}+\frac{1}{2^n} = \frac{T_{n-1}}{2^{n-1}}+\frac{1}{2^n} \qquad \text{...[i]} $$

**Why multiply by $2^{-n}$?** Because it turns the "$2T_{n-1}$" term into something with the _same_ denominator pattern as $T_{n-1}/2^{n-1}$, letting us define a new sequence $S_n$ that follows a simple **additive** (sum-type) recurrence instead of a multiplicative one.

**Let** $S_n = \dfrac{T_n}{2^n}$. Then Eqn [i] becomes:

$$S_n = S_{n-1}+\frac{1}{2^n}, \qquad S_0 = \frac{T_0}{2^0} = 0$$

![[sums-toh-recurrence-derivation.png]]

### 6.2 Unfold $S_n$ (this is now a simple additive recurrence, like Example C in Note 1!)

$$ \begin{aligned} S_n &= S_{n-1}+2^{-n}\ &= S_{n-2}+2^{-(n-1)}+2^{-n}\ &= S_{n-3}+2^{-(n-2)}+2^{-(n-1)}+2^{-n}\ &;;\vdots\ &= S_0 + 2^{-1}+2^{-2}+\dots+2^{-(n-1)}+2^{-n}\ &= \sum_{k=1}^{n} 2^{-k} \qquad (\text{since } S_0=0) \end{aligned} $$

### 6.3 Sum the Geometric Series

Add $2^0=1$ to both sides (a standard trick to make the series start from exponent 0):

$$S_n + 2^0 = \sum_{k=1}^{n}2^{-k}+2^0 = \frac{1}{2^0}+\frac{1}{2^1}+\dots+\frac{1}{2^n}$$

$$\Rightarrow S_n + 1 = \frac{1-\left(\tfrac12\right)^{n+1}}{1-\tfrac12} \qquad(\text{geometric series sum formula } \tfrac{1-r^{n+1}}{1-r})$$

$$\Rightarrow S_n = 2\left(1-\frac{1}{2^{n+1}}\right) - 1$$

### 6.4 Recover $T_n$

Recall $S_n = T_n/2^n$:

$$\frac{T_n}{2^n} = 1 - \frac{1}{2^n} \quad\Rightarrow\quad T_n = 2^n - 1$$

$$\boxed{T_n = 2^n - 1}$$ — **Same answer as the direct unfolding method — confirms correctness!**

---

## 7. The General Summation-Factor Technique (for ANY linear recurrence)

This generalizes the trick above to _any_ recurrence of the form:

$$a_n T_n = b_n T_{n-1}+c_n \qquad \text{...(3)}$$

**Procedure:**

1. Multiply both sides by a summation factor $s_n$: $$s_na_nT_n = s_nb_nT_{n-1}+s_nc_n$$
2. Choose $s_n$ so that $s_nb_n = s_{n-1}a_{n-1}$ (this makes consecutive terms "line up" so they telescope).
3. Define $S_n = s_na_nT_n$. Then: $$S_n = S_{n-1}+s_nc_n$$
4. Unfold (telescoping sum): $$S_n = S_0 + \sum_{k=1}^{n}s_kc_k = s_1b_1T_0+\sum_{k=1}^n s_kc_k$$

![[sums-summation-factor-general.png]]

### 7.1 Formula for the Solution $T_n$ and the Summation Factor $s_n$

$$T_n = \frac{S_n}{s_na_n} = \frac{1}{s_na_n}\left(s_1b_1T_0+\sum_{k=1}^{n}s_kc_k\right) \qquad \text{...(4)}$$

To find the correct $s_n$, unfold the relation $s_n = \dfrac{s_{n-1}a_{n-1}}{b_n}$:

$$s_n = \frac{s_{n-1}a_{n-1}}{b_n} = \frac{s_{n-2}a_{n-2}a_{n-1}}{b_{n-1}b_n} = \dots = \frac{s_1a_1\cdots a_{n-2}a_{n-1}}{b_2\cdots b_{n-1}b_n}$$

$$\boxed{s_n = \frac{a_1a_2\cdots a_{n-1}}{b_2b_3\cdots b_n}} \quad \text{(or any convenient constant multiple of this)}$$

_(Note: the value of $s_1$ always cancels out of the final formula for $T_n$, so $s_1$ can be **any nonzero constant** — usually chosen as $1$.)_

![[sums-summation-factor-solution.png]]

### 7.2 Worked Example: Apply the General Method to Tower of Hanoi

For TOH: $T_n = 2T_{n-1}+1$, i.e., in the form $a_nT_n = b_nT_{n-1}+c_n$ with: $$a_n = 1,\quad b_n = 2,\quad c_n=1,\quad T_0=0$$

**Find summation factor:** $$s_n = \frac{1\cdot1\cdot1\cdots1}{2\cdot2\cdots2} = \frac{1}{2^{n-1}} = 2^{-n+1}$$

**Apply formula (4):** $$ T_n = \frac{1}{s_na_n}\left(s_1b_1T_0+\sum_{k=1}^n s_kc_k\right) = \frac{1}{2^{-n+1}\cdot 1}\left(2^{-1+1}\cdot2\cdot0+\sum_{k=1}^n 2^{-k+1}\cdot1\right) $$

$$ = 2^{n-1}\sum_{k=1}^{n}2^{-k+1} = 2^{n-1}\sum_{k=0}^{n-1}2^{-k} $$

Sum the geometric series $\sum_{k=0}^{n-1}2^{-k} = \dfrac{1-(1/2)^n}{1-1/2} = 2\left(1-(1/2)^n\right)$:

$$ T_n = 2^{n-1}\cdot 2\left(1-\left(\tfrac12\right)^n\right) = 2^n\left(1-\left(\tfrac12\right)^n\right) = 2^n - 1 $$

$$\boxed{T_n = 2^n - 1} \quad\text{— confirmed a THIRD time, via the general summation-factor formula.}$$

![[sums-toh-summation-factor-worked.png]]

> **Exam tip:** You now have **three independent ways** to reach $T_n = 2^n-1$ for TOH:
> 
> 1. Direct unfolding + geometric series (Section 4).
> 2. Multiply-by-summation-factor manually, then unfold (Section 6).
> 3. Plug into the **general formula (4)** for $a_nT_n = b_nT_{n-1}+c_n$ recurrences (Section 7). If the exam gives you a **new** linear recurrence you haven't seen, use Method 3 — identify $a_n, b_n, c_n$, compute $s_n$, then plug into formula (4).

---

## 8. Summary Table

|Quantity|Value|
|---|---|
|Recurrence|$T_n = 2T_{n-1}+1,\ T_0=0$|
|Closed form|$T_n = 2^n - 1$|
|$T_1$|$1$|
|$T_2$|$3$|
|$T_3$|$7$|
|$T_4$|$15$|
|$T_5$|$31$|
|Interpretation|minimum number of moves to transfer $n$ disks between 3 pegs|

## 9. Homework Mentioned in Slides (practice on your own)

- **Solve DTOH (Double/variant Tower of Hanoi)** and **TTOH (Triple-peg variant)** with full induction proofs — apply the same 6-step method above.
- **Quick Sort recurrence → convert to a Sum** — apply the general summation-factor technique from Section 7 (Chapter 2, Sums §2.2/2.12 reference in slides).

---

## 10. Common Exam Pitfalls

- Writing $T_n = 2T_{n-1}+1$ but forgetting **why**: it's because you solve TWO subproblems of size $n-1$ (move $n-1$ disks off, then back on) **plus 1** move for the biggest disk.
- Sign/index errors in geometric series formula $\dfrac{a^{n+1}-1}{a-1}$ — double check how many terms are actually being summed.
- In induction proofs, clearly separate **Basis / Hypothesis / Induction** — professors grade these as distinct steps.
- When choosing a summation factor, remember $s_1$ is arbitrary (cancels out) — don't worry if it looks weird.