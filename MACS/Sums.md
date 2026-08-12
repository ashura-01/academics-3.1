---
tags: [math, discrete-math, sums, recurrences, midterm]
---

# SUMS — Complete Notes

> [!info] Topics covered
> Notation → Sums & Recurrences → Repertoire Method (General solution) → Worked Examples 1–6 (all solved) → Converting a Recurrence into a Sum (Tower of Hanoi) → General Summation Factor → Manipulation of Sums (3 Laws) → Multiple Sums (all practice problems solved)

---

## 1. Notation

For a finite set $K=\{1,2,\dots,m\}$ and a sequence $f:K\to\mathbb R$ with $f(n)=a_n$, we write:

$$\sum_{k=1}^{m} a_k = a_1+a_2+\cdots+a_m$$

**Anatomy of $\displaystyle\sum_{i=1}^{10} i$:**

| Part | Meaning |
|---|---|
| $\Sigma$ | Greek letter *sigma* — "sum of" |
| $i=1$ (bottom) | **lower limit** of summation |
| $10$ (top) | **upper limit** of summation |
| $i$ (index) | **index of summation** — the variable that changes |
| $i$ (after $\Sigma$) | represents **each addend/term** |

> [!tip] Key idea
> The index variable ($k$, $i$, etc.) is just a placeholder — it disappears after the sum is evaluated. $k$ is called the **index variable**.

### Number of terms — be careful!

$$a_0+a_1+a_2+\cdots+a_n \quad\Rightarrow\quad n+1 \text{ terms (starts at 0)}$$
$$1+2+3+\cdots+n \quad\Rightarrow\quad n \text{ terms (starts at 1)}$$
$$1+2+4+8+\cdots+2^{n-1}\quad\Rightarrow\quad n \text{ terms, but written as } n+1 \text{ terms depending on indexing — always COUNT, don't assume.}$$

### Two equivalent notations

$$\sum_{k=0}^{n} a_k \;\equiv\; \sum_{0\le k\le n} a_k$$

The "set-builder" style $\sum_{0\le k\le n}$ is more flexible — it lets you sum over **any condition**, not just a contiguous range (very useful later in "Multiple Sums").

### Worked mini-example: Sum of squares of odd numbers 0–100

$$1^2+3^2+5^2+\cdots+99^2 = \sum_{k=0}^{49}(2k+1)^2$$

**Why $k=0$ to $49$?** The odd numbers from 1 to 99 are $2k+1$ for $k=0,1,2,\dots$. Setting $2k+1=99\Rightarrow k=49$. So there are $50$ terms, $k$ running $0\to49$.

---

## 2. Sums and Recurrences

**Big idea:** Every sum can be rewritten as an equivalent recurrence, and (often) every recurrence of a certain shape can be solved to get a **closed form** — a formula with no $\Sigma$ and no self-reference.

### Deriving the recurrence

$$S_n = 0+1+2+3+\cdots+(n-1)+n$$

Notice that everything *except* the last term is just $S_{n-1}$:

$$S_n = \underbrace{0+1+2+\cdots+(n-1)}_{S_{n-1}} + n$$

So in general, for
$$S_n=\sum_{k=0}^{n}a_k$$

this is **equivalent** to the recurrence:

$$\boxed{S_0=a_0,\qquad S_n = S_{n-1}+a_n \ \ (n>0)}$$

> [!success] Why this matters
> This recurrence form is the bridge that lets us use recurrence-solving techniques (like the **Repertoire Method** below) to get closed forms for sums.

---

## 3. How to Convert a SUM into a RECURRENCE (General Method)

### Step 1 — Write the sum as a recurrence

$$S_n = 0+1+2+3+\cdots+(n-1)+n = S_{n-1}+n$$
$$S_0 = 0$$

This matches the **general linear recurrence template**:

$$\begin{cases}R_0=\alpha\\ R_n = R_{n-1}+\beta+\gamma n\end{cases}$$

where $\alpha,\beta,\gamma$ are constants that depend on the specific problem (given/derived from the problem's recurrence).

For $S_n=S_{n-1}+n,\ S_0=0$: comparing term by term, $\alpha=0,\ \beta=0,\ \gamma=1$.

### Step 2 — Guess a general solution shape

We assume the solution has the form:

$$R_n = A(n)\,\alpha + B(n)\,\beta + C(n)\,\gamma \qquad \text{...[i]}$$

We must find the **unknown functions** $A(n), B(n), C(n)$. Once found, this formula works for **any** $\alpha,\beta,\gamma$ — that's the power of the method.

### Step 3 — The Repertoire Method (finding A(n), B(n), C(n))

**Idea:** Plug in simple, known functions for $R_n$ (like $1$, $n$, $n^2$), figure out what $(\alpha,\beta,\gamma)$ they correspond to, then match coefficients in [i].

---

#### Case 1: Let $R_n = 1$ (constant)

From the recurrence template:
$$R_0=\alpha \;\Rightarrow\; 1=\alpha \;\Rightarrow\; \alpha=1$$
$$R_n=R_{n-1}+\beta+\gamma n \;\Rightarrow\; 1=1+\beta+\gamma n \text{ for all } n$$

For this to hold for **every** $n$, the coefficient of $n$ must vanish:
$$\gamma=0 \quad\text{and then}\quad \beta=0$$

So $(\alpha,\beta,\gamma)=(1,0,0)$ produces $R_n=1$.

Substitute into [i]:
$$1 = A(n)(1)+B(n)(0)+C(n)(0)=A(n)$$
$$\boxed{A(n)=1}$$

---

#### Case 2: Let $R_n = n$

$$R_0=\alpha \Rightarrow 0=\alpha \Rightarrow \alpha=0$$
$$R_1 = R_0+\beta+\gamma(1) = \beta+\gamma,\quad\text{and } R_1=1 \Rightarrow \beta+\gamma=1 \quad(i)$$
$$R_2 = R_1+\beta+\gamma(2)=(\beta+\gamma)+\beta+2\gamma=2\beta+3\gamma,\quad R_2=2 \Rightarrow 2\beta+3\gamma=2\quad(ii)$$

Solve (i) & (ii): from (i), $\beta=1-\gamma$. Substitute in (ii):
$$2(1-\gamma)+3\gamma=2 \Rightarrow 2+\gamma=2 \Rightarrow \gamma=0,\ \beta=1$$

So $(\alpha,\beta,\gamma)=(0,1,0)$ produces $R_n=n$.

Substitute into [i]:
$$n = A(n)(0)+B(n)(1)+C(n)(0)=B(n)$$
$$\boxed{B(n)=n}$$

---

#### Case 3: Let $R_n = n^2$

$$R_0=\alpha \Rightarrow \alpha=0$$
$$R_1=\beta+\gamma=1 \quad(i)\qquad(\text{since } R_1=1^2=1)$$
$$R_2=2\beta+3\gamma=4 \quad(ii)\qquad(\text{since } R_2=2^2=4)$$

From (i): $\beta=1-\gamma$. Sub into (ii):
$$2(1-\gamma)+3\gamma=4 \Rightarrow 2+\gamma=4 \Rightarrow \gamma=2,\ \beta=-1$$

So $(\alpha,\beta,\gamma)=(0,-1,2)$ produces $R_n=n^2$.

Substitute into [i]:
$$n^2 = A(n)(0)+B(n)(-1)+C(n)(2) = -n + 2C(n) \quad(\text{using } B(n)=n)$$
$$2C(n) = n^2+n \Rightarrow \boxed{C(n)=\dfrac{n^2+n}{2}}$$

---

### 🎯 The General Solution (memorize this!)

$$\boxed{R_n = \alpha + n\beta + \dfrac{n^2+n}{2}\gamma}$$

This single formula solves **every** recurrence of the form $R_0=\alpha,\ R_n=R_{n-1}+\beta+\gamma n$ — just plug in the $\alpha,\beta,\gamma$ from your specific problem.

> [!warning] How to read off $\alpha,\beta,\gamma$
> Write your sum's recurrence as $S_0=(\text{value}),\ S_n=S_{n-1}+(\text{something in terms of } n)$.
> Match "something" to the pattern $\beta+\gamma n$, and match $S_0$ to $\alpha$.

---

## 4. Worked Examples (Full Solutions)

### Example 1 — General arithmetic sum

**Find the closed form for:**
$$S_n=\sum_{k=0}^{n}(a+bk)$$

**Step 1 — Expand & spot the recurrence:**
$$S_n = (a)+(a+b)+(a+2b)+\cdots+(a+(n-1)b)+(a+nb)$$
$$\underbrace{(a)+\cdots+(a+(n-1)b)}_{S_{n-1}} + (a+nb)$$

Put $k=0$: $S_0=a$.
So: $S_n = S_{n-1}+(a+nb)$

**Step 2 — Match to template** $R_n=R_{n-1}+\beta+\gamma n$:
$$\alpha=a,\quad \beta=a,\quad \gamma=b$$

**Step 3 — Plug into general solution** $R_n=\alpha+n\beta+\frac{n^2+n}{2}\gamma$:
$$S_n = a+an+\frac{n^2+n}{2}b$$

**Step 4 — Simplify (factor):**
$$S_n = a(1+n)+\frac{n(n+1)}{2}b = \boxed{a(n+1)+\dfrac{n(n+1)}{2}b}$$

---

### Example 2 — Numeric application

**Find $S_{80}$ for** $\displaystyle S_n=\sum_{k=0}^{n}(3+2k)$

Compare to Example 1's form $(a+bk)$: $a=3,\ b=2$.

Use the derived formula:
$$S_n = a(n+1)+\frac{n(n+1)}{2}b$$
$$S_{80} = 3(81) + \frac{80(81)}{2}(2) = 243 + 80\times81 = 243+6480 = \boxed{6723}$$

---

### Example 3 — Sum of natural numbers (Gauss's sum)

**Find recurrence and closed form for:** $0+1+2+3+\cdots+n$, i.e. $S_n=\sum_{k=0}^n k$

**Recurrence:** $S_0=0,\quad S_n=S_{n-1}+n$

Match to template: $\alpha=0,\ \beta=0,\ \gamma=1$

**Plug into general solution:**
$$R_n=\alpha+n\beta+\frac{n^2+n}{2}\gamma = 0+0+\frac{n^2+n}{2}(1)$$

$$\boxed{S_n=\dfrac{n(n+1)}{2}}$$

This is the famous **Gauss sum** formula.

---

### Example 4 — Sum of first odd numbers

**Find recurrence and closed form for:** $1+3+5+7+\cdots+(2n+1)$, i.e. $S_n=\sum_{k=0}^n(2k+1)$

**Recurrence:** $S_0=1,\quad S_n=S_{n-1}+(2n+1)$

Match to template ($\beta+\gamma n$): here the added term is $2n+1 = 1+2n$, so $\beta=1,\ \gamma=2$. And $\alpha=S_0=1$.

$$\alpha=1,\ \beta=1,\ \gamma=2$$

**Plug into general solution:**
$$R_n = 1 + n(1) + \frac{n^2+n}{2}(2) = 1+n+(n^2+n) = n^2+2n+1$$

$$\boxed{S_n=(n+1)^2}$$

**Sanity check:** sum of first $n+1$ odd numbers ($1,3,5,\dots$) is always a perfect square — a classic identity!

---

### Example 5 — Sum of even numbers

**Find recurrence and closed form for:** $0+2+4+\cdots$, i.e. $S_n=\sum_{k=0}^n 2k$

**Recurrence:** $S_0=0,\quad S_n=S_{n-1}+2n$

Match to template: $\alpha=0,\ \beta=0,\ \gamma=2$

**Plug in:**
$$R_n = 0+0+\frac{n^2+n}{2}(2) = n^2+n$$

$$\boxed{S_n = n(n+1)}$$

---

### Example 6 — Arithmetic sum, find $n$ from the last term (HOMEWORK — solved)

**Find the recurrence and closed form for:**
$$3+10+17+\cdots+703 \quad\text{OR}\quad 3+10+17+\cdots \text{ up to the } 101^{\text{th}} \text{ term}$$

**Hint given:** $S_n=\sum_{k=0}^n (a+bk)$

**Step 1 — Identify $a$ and $b$:**
- First term ($k=0$): $a=3$
- Common difference: $10-3=7 \Rightarrow b=7$

So the general term is $a+bk = 3+7k$.

**Step 2 — Find $n$ from the last term 703:**

If $703$ is the $k^{\text{th}}$ term (0-indexed), then:
$$3+7k=703 \Rightarrow 7k=700 \Rightarrow k=100$$

So $n=100$. (This matches "up to the $101^{\text{th}}$ term" since $k=0,\dots,100$ gives $101$ terms total ✓.)

**Step 3 — Recurrence** (from Example 1's derivation, general to any $a,b$):
$$S_0=a=3,\qquad S_n=S_{n-1}+(3+7n)$$
with $\alpha=3,\ \beta=3,\ \gamma=7$.

**Step 4 — Closed form (reuse Example 1's result):**
$$S_n = a(n+1)+\frac{n(n+1)}{2}b$$

**Step 5 — Plug in $n=100,\ a=3,\ b=7$:**
$$S_{100} = 3(101) + \frac{100(101)}{2}(7) = 303 + 5050\times 7 = 303+35350$$

$$\boxed{S_{100}=35653}$$

**Quick check with arithmetic series formula** $\left(\dfrac{\text{number of terms}}{2}\right)(\text{first}+\text{last})$:
$$\frac{101}{2}(3+703)=\frac{101}{2}(706)=101\times353=35653\ ✓$$

---

## 5. Converting a RECURRENCE into a SUM

Sometimes it's the recurrence that's given, and we need to go the *other* direction — turn it into a sum to solve it. Classic example: **Tower of Hanoi**.

### Tower of Hanoi Recurrence → Sum

$$T_0=0,\qquad T_n = 2T_{n-1}+1$$

**Trick:** Multiply both sides by a **summation factor** $2^{-n}$ (this is chosen specifically to make the recursion "telescope" nicely — explained generally in section 6):

$$\frac{T_0}{2^0}=\frac{0}{2^0}=0$$

$$\frac{T_n}{2^n} = \frac{2T_{n-1}}{2^n}+\frac{1}{2^n} = \frac{T_{n-1}}{2^{n-1}}+\frac{1}{2^n} \qquad \text{...[i]}$$

**Substitute** $S_n = \dfrac{T_n}{2^n}$. Then [i] becomes a simple additive recurrence:

$$\boxed{S_0=0,\qquad S_n=S_{n-1}+\frac{1}{2^n}}$$

This is now solvable directly by *unrolling* (repeated substitution):

$$S_n = S_{n-1}+2^{-n}$$
$$=S_{n-2}+2^{-(n-1)}+2^{-n}$$
$$=S_{n-3}+2^{-(n-2)}+2^{-(n-1)}+2^{-n}$$
$$\vdots$$
$$=S_0 + 2^{-1}+2^{-2}+\cdots+2^{-(n-1)}+2^{-n}$$

$$S_n = \sum_{k=1}^n 2^{-k}$$

**Solve the geometric sum** — add $2^0=1$ to both sides to make it a full geometric series from $k=0$:

$$S_n+1 = \sum_{k=0}^n 2^{-k} = \frac{1-\left(\frac12\right)^{n+1}}{1-\frac12}$$

$$S_n = 2\left(1-\frac{1}{2^{n+1}}\right)-1$$

**Convert back:** recall $S_n=T_n/2^n$:

$$\frac{T_n}{2^n}=1-\frac{1}{2^n} \Rightarrow \boxed{T_n = 2^n-1}$$

This is the well-known **Tower of Hanoi** closed-form solution: $2^n-1$ moves for $n$ disks.

---

## 6. General Summation Factor Method

The Tower of Hanoi trick (multiplying by $2^{-n}$) is a special case of a **general technique** that reduces *any* recurrence of the form:

$$a_n T_n = b_n T_{n-1}+c_n \qquad \text{...(3)}$$

into a sum.

### Derivation

**Step 1 — Multiply both sides by a summation factor $s_n$:**
$$s_n a_n T_n = s_n b_n T_{n-1}+s_n c_n$$

**Step 2 — Choose $s_n$ cleverly** so that:
$$s_n b_n = s_{n-1}a_{n-1}$$

This is the key design choice — it makes the recursive term line up for telescoping.

**Step 3 — Define** $S_n = s_n a_n T_n$. Then:
$$S_n = s_n b_n T_{n-1}+s_n c_n = s_{n-1}a_{n-1}T_{n-1}+s_n c_n = S_{n-1}+s_n c_n$$

Now we have the **simplest possible sum-recurrence**:
$$\boxed{S_n = S_{n-1}+s_n c_n}$$

**Step 4 — Telescope / unroll:**
$$S_n = S_{n-2}+s_{n-1}c_{n-1}+s_n c_n = \cdots = S_0+\sum_{k=1}^n s_k c_k$$

$$S_n = s_0a_0T_0 + \sum_{k=1}^n s_kc_k = s_1b_1T_0+\sum_{k=1}^{n}s_kc_k$$

### Finding the value of $s_n$

At $n=1$: $T_1 = \dfrac{s_1b_1T_0+s_1c_1}{s_1a_1}=\dfrac{b_1T_0+c_1}{a_1}$ — notice $s_1$ **cancels out**, so it can be chosen to be *anything nonzero* (usually $s_1=1$).

To find general $s_n$, **unfold the relation** $s_nb_n=s_{n-1}a_{n-1}$ repeatedly:
$$s_n = \frac{s_{n-1}a_{n-1}}{b_n}=\frac{s_{n-2}a_{n-2}a_{n-1}}{b_{n-1}b_n}=\cdots=\frac{s_1a_1\cdots a_{n-2}a_{n-1}}{b_2\cdots b_{n-1}b_n}$$

$$\boxed{s_n = \dfrac{a_1a_2\cdots a_{n-1}}{b_2b_3\cdots b_n}}$$

(or any convenient constant multiple of this — since $s_1$ was arbitrary).

### Final closed-form solution formula

$$\boxed{T_n = \dfrac{S_n}{s_na_n} = \dfrac{1}{s_na_n}\left(s_1b_1T_0+\sum_{k=1}^n s_kc_k\right)}$$

---

### Applying the General Formula to Tower of Hanoi

Recurrence: $a_nT_n=b_nT_{n-1}+c_n$, with $T_0=0,\ T_n=2T_{n-1}+1$.

Identify: $a_n=1,\ b_n=2,\ c_n=1$.

**Find $s_n$:**
$$s_n = \frac{a_1a_2\cdots a_{n-1}}{b_2b_3\cdots b_n} = \frac{1\cdot1\cdots1}{2\cdot2\cdots2} = \frac{1}{2^{n-1}}=2^{-n+1}$$

**Plug into the final formula:**
$$T_n = \frac{1}{s_na_n}\left(s_1b_1T_0+\sum_{k=1}^n s_kc_k\right)$$

- $s_na_n = 2^{-n+1}\cdot1=2^{1-n}$
- $s_1b_1T_0 = 2^{0}\cdot2\cdot0=0$
- $\displaystyle\sum_{k=1}^n s_kc_k=\sum_{k=1}^n 2^{-k+1}\cdot1 = 2^{n-1}\sum_{k=1}^n 2^{-k}$ (pulling out constant differently, or directly):

$$\sum_{k=1}^n 2^{-k+1} = 2\sum_{k=1}^n2^{-k}=2(1-2^{-n})=2-2^{1-n}$$

**Combine:**
$$T_n = \frac{0+2-2^{1-n}}{2^{1-n}} = \frac{2}{2^{1-n}}-1 = 2^{1-(1-n)}-1=2^n-1$$

$$\boxed{T_n=2^n-1}$$

Matches the direct derivation in Section 5 exactly ✓ — confirms the general method works.

> [!note] Homework mentioned in slides
> The slides assign **Quick Sort recurrence → Sum** as homework, referencing **Chapter 2, Section 2.2 (problem 2.12)** of the textbook (Concrete Mathematics). Apply the exact same general summation-factor method above: identify $a_n, b_n, c_n$ from the Quick Sort recurrence, compute $s_n=\frac{a_1\cdots a_{n-1}}{b_2\cdots b_n}$, then plug into $T_n=\frac{1}{s_na_n}\left(s_1b_1T_0+\sum s_kc_k\right)$.

---

## 7. Sum over a general condition — Example

**Compute** $\displaystyle\sum_{\{0\le k\le 5\}} a_k$ and $\displaystyle\sum_{\{0\le k^2\le5\}} a_{k^2}$

**First sum:** $\{0\le k\le5\}=\{0,1,2,3,4,5\}$

$$\sum_{\{0\le k\le5\}}a_k = a_0+a_1+a_2+a_3+a_4+a_5$$

**Second sum:** Here the condition is on $k^2$, not $k$ directly! Solve $0\le k^2\le5$:
$$k^2\le 5 \Rightarrow -\sqrt5 \le k \le \sqrt5 \Rightarrow k\in\{-2,-1,0,1,2\}\ (\text{integers only})$$

So $\{0\le k^2\le5\}=\{0,1,2,-1,-2\}$ (as a set of valid $k$ values).

$$\sum a_{k^2} = a_{0^2}+a_{1^2}+a_{2^2}+a_{(-1)^2}+a_{(-2)^2} = a_0+a_1+a_4+a_1+a_4$$

Wait — carefully: $a_{k^2}$ evaluated at each valid $k$:
- $k=0\Rightarrow a_{0}$
- $k=1\Rightarrow a_{1}$
- $k=2\Rightarrow a_{4}$
- $k=-1\Rightarrow a_{1}$
- $k=-2\Rightarrow a_{4}$

$$\sum = a_0+a_1+a_1+a_4+a_4 = \boxed{a_0+2a_1+2a_4}$$

> [!tip] Lesson
> When the index appears **inside a function** (like $k^2$), first find *all* integer $k$ satisfying the condition — don't just take a simple range. Positive and negative $k$ can map to the **same** subscript value, causing terms to double up (this is exactly what the associative/commutative laws below let us reorganize).

---

## 8. Manipulation of Sums — The Three Laws

Let $P$ be any finite set of integers. Sums over elements of $P$ obey three simple rules:

### 1. Distributive Law
$$\sum_{k\in P} c\,a_k = c\sum_{k\in P}a_k$$
*A constant multiplier can be pulled out of (or pushed into) the sum.*

### 2. Associative Law
$$\sum_{k\in P}(a_k+b_k) = \sum_{k\in P}a_k+\sum_{k\in P}b_k$$
*A sum of two sequences can be split into two separate sums.*

### 3. Commutative Law
$$\sum_{k\in P}a_k = \sum_{m\in P}a_m$$
*Renaming the index variable (or reordering the terms) doesn't change the sum — order within a finite sum doesn't matter.*

### Worked check: $K=\{-1,0,1\}$, $m=-k$

$$ca_{-1}+ca_0+ca_1 = c(a_{-1}+a_0+a_1) \quad \text{[Distributive]}$$
$$(a_{-1}+b_{-1})+(a_0+b_0)+(a_1+b_1) = (a_{-1}+a_0+a_1)+(b_{-1}+b_0+b_1)\quad\text{[Associative]}$$
$$a_{-1}+a_0+a_1 = a_1+a_0+a_{-1}\quad\text{[Commutative]}$$

> [!tip] Why this matters
> These three laws are the "algebra rules" for sums — they let you split, factor, and reorder sums freely, which is essential for simplifying multi-index sums (next section) and for proving closed forms.

---

## 9. Multiple Sums

A sum can have **more than one index** running simultaneously.

### Basic double sum (independent indices)

$$\sum_{1\le j,k\le3} a_jb_k = a_1b_1+a_1b_2+a_1b_3+a_2b_1+a_2b_2+a_2b_3+a_3b_1+a_3b_2+a_3b_3$$

This is simply **every combination** of $j\in\{1,2,3\}$ with $k\in\{1,2,3\}$ — like a $3\times3$ grid of products.

### Swapping order of summation (Iverson bracket notation)

$$\sum_j\sum_k a_{j,k}\,[P(j,k)] \;=\; \sum_{P(j,k)}a_{j,k} \;=\; \sum_k\sum_j a_{j,k}\,[P(j,k)]$$

Here $[P(j,k)]$ is an **Iverson bracket**: it equals $1$ if condition $P(j,k)$ is true, and $0$ otherwise. This is a powerful trick — it lets you convert between "sum with a constraint written under $\Sigma$" and "sum over all $j,k$ but multiplied by a 0/1 filter," and crucially, **the order of summation ($j$ first vs $k$ first) doesn't matter** as long as the *set of $(j,k)$ pairs* being summed is the same. This is just the Commutative + Associative laws applied to two dimensions.

> [!info] Textbook reference
> Chapter 2 → Section 2.4 (Multiple Sums)

---

## 10. Multiple Sum Practice Problems — All Solved

### Problem 1 — Triple sum

**Find the sum:**
$$\sum_{i=1}^{4}\sum_{j=0}^{2}\sum_{k=3}^{5}(ik+j)$$

**Strategy:** work from the **innermost** sum outward. Treat $i$ and $j$ as constants while summing over $k$.

**Step 1 — Innermost sum over $k=3,4,5$ (3 terms):**
$$\sum_{k=3}^{5}(ik+j) = i\sum_{k=3}^5 k + \sum_{k=3}^5 j = i(3+4+5)+3j = 12i+3j$$

(Used Distributive + Associative Laws: split $ik+j$ into $i\cdot k$ summed, plus $j$ summed 3 times.)

**Step 2 — Middle sum over $j=0,1,2$ (3 terms), applied to result $12i+3j$:**
$$\sum_{j=0}^{2}(12i+3j) = \sum_{j=0}^2 12i + \sum_{j=0}^2 3j = 3(12i) + 3(0+1+2) = 36i+9$$

**Step 3 — Outer sum over $i=1,2,3,4$, applied to $36i+9$:**
$$\sum_{i=1}^{4}(36i+9) = 36\sum_{i=1}^4 i + \sum_{i=1}^4 9 = 36(1+2+3+4)+4(9) = 36(10)+36 = 360+36$$

$$\boxed{=396}$$

---

### Problem 2 — Simple arithmetic sum

**Find the sum:**
$$\sum_{n=1}^{5}(2n+3)$$

**Split using Distributive + Associative Laws:**
$$= 2\sum_{n=1}^5 n + \sum_{n=1}^5 3 = 2(1+2+3+4+5)+3(5) = 2(15)+15 = 30+15$$

$$\boxed{=45}$$

*(Cross-check with Example 1's formula: $a=3,b=2$, but here it's $\sum_{n=1}^5$ not $\sum_{n=0}^5$ — using direct expansion above is safest and matches.)*

---

### Problem 3 — Arithmetic sum

**Find the sum:**
$$\sum_{i=1}^{16}(5i-4)$$

**Split:**
$$= 5\sum_{i=1}^{16}i - \sum_{i=1}^{16}4 = 5\left(\frac{16\times17}{2}\right) - 4(16) = 5(136)-64 = 680-64$$

$$\boxed{=616}$$

---

## 11. Quick-Reference Formula Sheet (for exam day)

| Sum | Closed Form |
|---|---|
| $\sum_{k=1}^n k$ (or $\sum_{k=0}^n k$) | $\dfrac{n(n+1)}{2}$ |
| $\sum_{k=0}^n (2k+1)$ (odd numbers) | $(n+1)^2$ |
| $\sum_{k=0}^n 2k$ (even numbers) | $n(n+1)$ |
| $\sum_{k=0}^n(a+bk)$ (general arithmetic) | $a(n+1)+\dfrac{n(n+1)}{2}b$ |
| General repertoire solution $R_n=\alpha+n\beta+\gamma n$ recurrence | $R_n=\alpha+n\beta+\dfrac{n^2+n}{2}\gamma$ |
| Tower of Hanoi $T_n=2T_{n-1}+1,\,T_0=0$ | $T_n=2^n-1$ |
| Geometric series $\sum_{k=0}^n r^k$ | $\dfrac{1-r^{n+1}}{1-r}$ |
| Summation factor for $a_nT_n=b_nT_{n-1}+c_n$ | $s_n=\dfrac{a_1\cdots a_{n-1}}{b_2\cdots b_n}$ |
| Solution using summation factor | $T_n=\dfrac{1}{s_na_n}\left(s_1b_1T_0+\sum_{k=1}^n s_kc_k\right)$ |

### The 3 Laws of Sum Manipulation
1. **Distributive:** $\sum ca_k = c\sum a_k$
2. **Associative:** $\sum(a_k+b_k)=\sum a_k+\sum b_k$
3. **Commutative:** $\sum_{k\in P}a_k=\sum_{m\in P}a_m$ (reindex freely)

### The Repertoire Method — 3-Step Recipe
1. Write your recurrence in the form $R_0=\alpha,\ R_n=R_{n-1}+\beta+\gamma n$.
2. Read off $\alpha,\beta,\gamma$ by matching your specific problem to this template.
3. Plug into $R_n=\alpha+n\beta+\dfrac{n^2+n}{2}\gamma$.

### The General Summation Factor — 4-Step Recipe
1. Write recurrence as $a_nT_n=b_nT_{n-1}+c_n$.
2. Compute $s_n=\dfrac{a_1a_2\cdots a_{n-1}}{b_2b_3\cdots b_n}$.
3. Compute $\sum_{k=1}^n s_kc_k$.
4. Plug into $T_n=\dfrac{1}{s_na_n}\left(s_1b_1T_0+\sum_{k=1}^n s_kc_k\right)$.

### Multiple Sums — How to Solve
- Work from the **innermost** $\Sigma$ outward.
- At each stage, treat outer-loop variables as **constants**.
- Use the 3 Laws to split sums of $(\text{term}_1+\text{term}_2)$ into separate sums you already know formulas for.

---
