---
tags: [discrete-math, recurrences, josephus, combinatorics, binary-tricks]
course: Recurrent Problems (cont'd)
related: ["[[Integer Functions]]"]
---

# The Josephus Problem

> [!quote] Legend
> During the Jewish–Roman war, Flavius Josephus and 40 other rebels were trapped in a cave. Rather than surrender, they formed a circle and killed every third remaining person until one was left. Josephus (and a friend) worked out in advance where to stand to survive.

**Our variation:** $n$ people stand in a circle, numbered $1$ to $n$. Going around, we eliminate **every 2nd remaining person** until one survives. Find the survivor's number, $J(n)$.

![[josephus_n10.png]]

> [!example]- Elimination order for n = 10
> Order eliminated: **2, 4, 6, 8, 10, 3, 7, 1, 9** → survivor is **5**, so $J(10)=5$.

---

## 1. Building the Recurrence

**Small-value table**

| n | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 |
|---|---|---|---|---|---|---|---|---|---|----|----|----|----|----|----|----|
| J(n) | 1 | 1 | 3 | 1 | 3 | 5 | 7 | 1 | 3 | 5 | 7 | 9 | 11 | 13 | 15 | 1 |

**Observation 1:** $J(n)$ is always odd.

### Case n = 2m (even)

The first full trip around the circle eliminates every even-numbered person, leaving the odd numbers $1,3,5,\dots,2n-1$ in a circle, with position 1 poised to survive the *next* elimination (position 3 goes next). Relabel the survivors:

| Old number $k$ | 1 | 3 | 5 | 7 | 9 |
|---|---|---|---|---|---|
| New number $k'$ | 1 | 2 | 3 | 4 | 5 |

so $k = 2k'-1$. Since the *new* circle is just a fresh Josephus problem of size $n$, the new survivor is $J(n)$, and translating back to the *old* numbering:

$$\boxed{J(2n) = 2J(n) - 1, \quad n\ge 1}$$

### Case n = 2m+1 (odd)

With $2n+1$ people, the first trip eliminates all evens, and person **1** is also eliminated right after person $2n$ (since the pattern wraps). We're left with odd numbers $3,5,7,\dots,2n+1$. Relabeling:

| Old number $k$ | 1 | 3 | 5 | 7 | 9 | 11 |
|---|---|---|---|---|---|---|
| New number $k'$ | 0 | 1 | 2 | 3 | 4 | 5 |

so $k = 2k'+1$. The remaining circle is again a Josephus problem of size $n$, giving new survivor $J(n)$, and translating back:

$$\boxed{J(2n+1) = 2J(n) + 1, \quad n \ge 1}$$

### Full recurrence

$$J(1) = 1$$
$$J(2n) = 2J(n) - 1, \qquad n\ge 1$$
$$J(2n+1) = 2J(n) + 1, \qquad n\ge 1$$

---

## 2. Applying the Recurrence (worked example)

> [!example] Find J(86)
> $$J(86) = 2J(43) - 1$$
> $$J(43) = 2J(22) + 1$$
> $$J(22) = 2J(11) - 1$$
> $$J(11) = 7$$
> Back-substituting: $J(22) = 2(7)-1 = 13$, $J(43) = 2(13)+1 = 27$, $J(86) = 2(27)-1 = 53$.

> [!example] Find J(100) using the recurrence
> $$J(100) = 2J(50) - 1$$
> $$= 4J(25) - 2 - 1$$
> $$= 8J(12) + 4 - 2 - 1$$
> $$= 16J(6) - 8 + 4 - 2 - 1$$
> $$= 32J(3) - 16 - 8 + 4 - 2 - 1$$
> $$= 64J(1) + 32 - 16 - 8 + 4 - 2 - 1$$
> $$= 64 + 32 - 16 - 8 + 4 - 2 - 1 = \mathbf{73}$$

---

## 3. Closed Form

Since $J(n)$ is always 1 at the start of each "power-of-two group" and increases by 2 within the group, write $n$ as

$$n = 2^m + l, \qquad 2^m = \text{largest power of 2 not exceeding } n, \qquad 0\le l < 2^m$$

Then:

$$\boxed{J(2^m + l) = 2l + 1, \qquad m\ge 0,\ 0\le l < 2^m}$$

> [!example] J(1030)
> $1030 = 2^{10} + 6$, so $J(1030) = 2(6)+1 = 13$.

> [!example] Find J(100) using the closed form
> $100 = 2^6 + 36$, so $J(100) = 2(36)+1 = \mathbf{73}$ — matches the recurrence result above.

### Proof of the closed form (induction on m)

**Base case ($m=0$):** then $0\le l<1 \Rightarrow l=0$.
$$J(2^0+0) = 2(0)+1 = 1 = J(1) \checkmark$$

**Inductive hypothesis:** assume $J(2^m+l) = 2l+1$ holds for all $l$ with $0\le l<2^m$. Show it holds for $m+1$.

**Sub-case: $l$ even.** Since $2^{m+1}+l$ is even, write $2^{m+1}+l = 2n$, so $n = 2^m + \tfrac{l}{2}$. Then:

$$J(2^{m+1}+l) = J(2n) = 2J(n)-1 = 2J\!\left(2^m+\tfrac{l}{2}\right) - 1 = 2\left(2\cdot\tfrac{l}{2}+1\right) - 1 = 2l+2-1 = 2l+1$$

**Sub-case: $l$ odd.** Since $2^{m+1}+l$ is odd, write $2^{m+1}+l = 2n+1$, so $n = 2^m+\tfrac{l-1}{2}$. Then:

$$J(2^{m+1}+l) = J(2n+1) = 2J(n)+1 = 2J\!\left(2^m+\tfrac{l-1}{2}\right)+1 = 2\left(2\cdot\tfrac{l-1}{2}+1\right)+1 = 2(l-1+1)+1 = 2l+1$$

Both sub-cases give $2l+1$, so the formula holds for $m+1$, completing the induction. $\blacksquare$

---

## 4. Binary ("bit rotation") Property

Power-of-2 structure hints we should look at $n$ and $J(n)$ in **binary**. Write

$$n = (b_m b_{m-1}\cdots b_1 b_0)_2, \qquad b_m = 1 \text{ (leading bit)}$$

Since $n = 2^m + l$, the remaining bits $b_{m-1}\cdots b_0$ are exactly the binary digits of $l$. The closed form $J(n) = 2l+1$ means: **shift $l$ left by one bit and add 1** — which is exactly the same digit string as $n$, but with the leading bit **moved to the end**:

$$J\big((b_m b_{m-1}\cdots b_1 b_0)_2\big) = (b_{m-1}\cdots b_1 b_0\, b_m)_2$$

> [!tip] Rule of thumb
> **Rotate the binary representation of $n$ left by one bit** (move the MSB to the LSB position) to get $J(n)$.

![[bit_rotation.png]]

> [!example] Find J(10) using the rotation trick
> $10 = 1010_2$. Rotate the leading bit to the end: $0101_2 = 5$. So $J(10)=5$ ✓ (matches the table).

> [!example] Find J(100) using the rotation trick
> $100 = 1100100_2$. Rotate: $1001001_2 = 64+8+1 = \mathbf{73}$ ✓ (matches both earlier results).

> [!example] Find J(20) using the rotation trick
> $20 = 10100_2$ (5 bits, $m=4$, $l=4=0100_2$). Rotate the leading 1 to the end: $01001_2 = 9$.
> Cross-check with the closed form: $20 = 2^4+4 \Rightarrow J(20) = 2(4)+1 = \mathbf{9}$ ✓

![[josephus_n20.png]]

---

## 5. Special-Case Problems (all solved)

All of these use the closed form $J(n) = 2l+1$ where $n = 2^m + l,\ 0\le l<2^m$.

### Find when J(n) = n/2

$$2l+1 = \frac{2^m+l}{2} \;\Rightarrow\; 2(2l+1) = 2^m+l \;\Rightarrow\; l = \frac{2^m-2}{3}$$

Needs $l$ to be a non-negative integer $<2^m$, i.e. $2^m\equiv 2 \pmod 3$, which happens for **odd** $m$:

| m | l | n = 2^m+l | J(n) = n/2 |
|---|---|---|---|
| 1 | 0 | 2 | 1 |
| 3 | 2 | 10 | 5 |
| 5 | 10 | 42 | 21 |
| 7 | 42 | 170 | 85 |

### Find the minimum three values of n for which J(n) = n/3

Set $k=3$: $2l+1 = \dfrac{2^m+l}{3} \Rightarrow l = \dfrac{2^m-3}{5}$.

Need $2^m \equiv 3 \pmod 5$. Since $2^m \bmod 5$ cycles $2,4,3,1,2,4,3,1,\dots$ (period 4), this holds when $m\equiv 3\pmod 4$: $m = 3, 7, 11,\dots$

| m | l = (2^m−3)/5 | n = 2^m+l | check J(n) = n/3 |
|---|---|---|---|
| 3 | 1 | **9** | J(9)=2(1)+1=3=9/3 ✓ |
| 7 | 25 | **153** | J(153)=51=153/3 ✓ |
| 11 | 409 | **2457** | J(2457)=819=2457/3 ✓ |

**Minimum three values: n = 9, 153, 2457**

### Find three values of n for which J(n) = n

Set $k=1$: $l = \dfrac{2^m-1}{2\cdot 1-1} = 2^m - 1$, which is automatically a valid integer $< 2^m$ for every $m\ge 0$. Then

$$n = 2^m + (2^m-1) = 2^{m+1}-1$$

i.e. $n$ is **all 1's in binary**. Smallest three: $m=0,1,2 \Rightarrow n = 1, 3, 7$.

*Check:* $J(7)$: $7=2^2+3$, $J(7)=2(3)+1=7$ ✓.

**Three values: n = 1, 3, 7**

### Find FIVE values of n for which the last person survives

"Last person survives" means the survivor's number equals $n$ itself — same condition as above, $n = 2^{k}-1$:

**n = 1, 3, 7, 15, 31**

*Check n=15:* $15=2^3+7$, $J(15)=2(7)+1=15$ ✓.

### Find the minimum three values of n for which J(n) = n/4

Set $k=4$: $l = \dfrac{2^m-4}{7}$. Need $2^m\equiv 4\pmod 7$. Since $2^m\bmod 7$ cycles $2,4,1,2,4,1,\dots$ (period 3), this holds when $m\equiv 2\pmod 3$: $m=2,5,8,11,\dots$

| m | l = (2^m−4)/7 | n = 2^m+l | check |
|---|---|---|---|
| 2 | 0 | **4** | J(4)=1=4/4 ✓ |
| 5 | 4 | **36** | J(36)=9=36/4 ✓ |
| 8 | 36 | **292** | J(292)=73=292/4 ✓ |

**Minimum three values: n = 4, 36, 292**

### Find the minimum three values of n such that the person at the n/4-th position survives, given n ≥ 100

Continue the same sequence ($m = 2,5,8,11,14,\dots$) and keep only terms $\ge 100$:

| m | n = 2^m+l |
|---|---|
| 8 | 292 |
| 11 | 2340 |
| 14 | 18724 |

**Minimum three values (n ≥ 100): n = 292, 2340, 18724**

### Find the minimum three values of n such that J(n) = 25

$2l+1 = 25 \Rightarrow l = 12$. Any $m$ with $2^m > 12$ works (so $l=12$ is a valid remainder), i.e. $m\ge 4$:

| m | n = 2^m+12 | check |
|---|---|---|
| 4 | **28** | J(28)=2(12)+1=25 ✓ |
| 5 | **44** | J(44)=25 ✓ |
| 6 | **76** | J(76)=25 ✓ |

**Minimum three values: n = 28, 44, 76**

---

## 6. Generalization: the Repertoire Method

Replace the fixed constants $1,-1,+1$ with free parameters $\alpha,\beta,\gamma$:

$$f(1) = \alpha$$
$$f(2n) = 2f(n) + \beta, \qquad n\ge 1$$
$$f(2n+1) = 2f(n) + \gamma, \qquad n\ge 1$$

Note $J = f$ exactly when $\alpha=1,\ \beta=-1,\ \gamma=1$.

The **repertoire method**: plug in simple special values of $(\alpha,\beta,\gamma)$ where the recurrence is easy to solve exactly, then combine those special solutions linearly to get the general solution — since the recurrence is *linear* in $\alpha,\beta,\gamma$:

$$f(n) = A(n)\,\alpha + B(n)\,\beta + C(n)\,\gamma$$

where $A(n), B(n), C(n)$ are universal coefficients (independent of $\alpha,\beta,\gamma$) found by solving three special cases.

### Special case 1 — find A(n): let α=1, β=γ=0

$$A(1)=1, \qquad A(2n) = 2A(n), \qquad A(2n+1) = 2A(n)$$

Unrolling doubles $A$ every time we peel off a bit, until we hit $A(1)=1$:

$$A(8) = A(2\cdot4) = 2A(4) = 2\big[2A(2)\big] = 2\big[2\big[2A(1)\big]\big] = 2^3$$
$$A(9) = A(2\cdot4+1) = 2A(4) = 2^3 \qquad A(11) = A(2\cdot5+1) = 2A(5) = 2^3$$

In general, if $n = 2^m+l$: **$A(n) = 2^m$** — the number of full doublings equals the number of bits below the leading 1.

### Special case 2 — find B(n): let α=0, β=1, γ=0

$$B(1)=0,\qquad B(2n)=2B(n)+1,\qquad B(2n+1)=2B(n)$$

Computing: $B(1){=}0,\ B(2){=}1,\ B(3){=}0,\ B(4){=}3,\ B(5){=}2,\ B(6){=}1,\ B(7){=}0,\ B(8){=}7,\ B(9){=}6$ — matching the table below.

### Special case 3 — find C(n): let α=0, β=0, γ=1

$$C(1)=0,\qquad C(2n)=2C(n),\qquad C(2n+1)=2C(n)+1$$

**Combined table for small n** (from the original slide, using $\alpha,\beta,\gamma$ as placeholders):

| n | f(n) |
|---|---|
| 1 | $\alpha$ |
| 2 | $2\alpha+\beta$ |
| 3 | $2\alpha+\gamma$ |
| 4 | $4\alpha+3\beta$ |
| 5 | $4\alpha+2\beta+\gamma$ |
| 6 | $4\alpha+\beta+2\gamma$ |
| 7 | $4\alpha+3\gamma$ |
| 8 | $8\alpha+7\beta$ |
| 9 | $8\alpha+6\beta+\gamma$ |

### Closed forms for A, B, C

Write $n=2^m+l$ with $0\le l<2^m$, and let $l$'s binary digits (padded to $m$ bits) be $b_{m-1}\cdots b_0$. Unrolling the general recurrence bit-by-bit (same radix argument as §7 below) gives the reusable digit-string identity:

$$f\big((b_m b_{m-1}\cdots b_1 b_0)_2\big) = \big(\alpha\,\beta_{b_{m-1}}\,\beta_{b_{m-2}}\cdots \beta_{b_1}\,\beta_{b_0}\big)_2, \qquad \beta_0 := \beta,\ \ \beta_1:=\gamma$$

i.e. each lower bit position contributes $\beta$ if that bit is 0, or $\gamma$ if that bit is 1, each weighted by its place value. Reading off the coefficients of $\beta$ and $\gamma$ separately:

$$\boxed{A(n) = 2^m} \qquad \boxed{C(n) = l} \qquad \boxed{B(n) = 2^m - 1 - l}$$

(since $C$ collects exactly the "1" bits of $l$, which reconstructs $l$ itself; and $B$ collects the "0" bits, which is everything else, i.e. $2^m-1-l$.)

**Full closed form:**

$$f(2^m+l) = 2^m\alpha + (2^m-1-l)\beta + l\gamma$$

> [!success] Sanity check against Josephus
> Plug in $\alpha=1,\beta=-1,\gamma=1$:
> $$f(n) = 2^m - (2^m-1-l) + l = 2l+1 = J(n) \checkmark$$

> [!example] Solve f(1)=2, f(2n)=2f(n)+2, f(2n+1)=2f(n)+3. Find f(n) closed form and f(6).
> Here $\alpha=2,\ \beta=2,\ \gamma=3$:
> $$f(2^m+l) = 2\cdot 2^m + 2(2^m-1-l) + 3l = 2^{m+2} - 2 + l$$
> **Check base:** $m=0,l=0 \Rightarrow f(1)=4-2+0=2$ ✓
>
> **f(6):** $6 = 2^2+2$ so $m=2,l=2$: $f(6) = 2^4 - 2 + 2 = \mathbf{16}$.
> *Direct unrolling check:* $f(1){=}2,\ f(2){=}2(2){+}2{=}6,\ f(3){=}2(2){+}3{=}7,\ f(6){=}2f(3){+}2{=}2(7){+}2=\mathbf{16}$ ✓

---

## 7. Radix-Based Generalization

Instead of splitting every number by its **remainder mod 2** (binary), split it by remainder **mod $d$**, and let the recurrence rescale by a different constant $c$:

$$f(j) = \alpha_j, \qquad 1\le j < d$$
$$f(dn+j) = c\,f(n) + \beta_j, \qquad 0\le j<d,\ n\ge 1$$

Unrolling this bit-by-bit (radix-$d$ digit by radix-$d$ digit) gives a **radix-changing solution**: read $n$'s digits in base $d$, but evaluate the output digit-string in base $c$:

$$\boxed{f\big((b_m b_{m-1}\cdots b_1 b_0)_d\big) = \big(\alpha_{b_m}\,\beta_{b_{m-1}}\,\beta_{b_{m-2}}\cdots\beta_{b_1}\,\beta_{b_0}\big)_c}$$

The ordinary Josephus recurrence is the special case $d=c=2$, $\alpha_1=1$, $\beta_0=-1$, $\beta_1=+1$.

> [!example] Verify J(100) with the radix-based property
> $100 = (1100100)_2$. Substitute $\alpha=1$ (for the leading bit) and $\beta_0=-1,\ \beta_1=+1$ for each remaining bit (reading left to right after the leading 1: $1,0,0,1,0,0$):
>
> | bit | 1 | 1 | 0 | 0 | 1 | 0 | 0 |
> |---|---|---|---|---|---|---|---|
> | digit value | $\alpha=1$ | $\beta_1=1$ | $\beta_0=-1$ | $\beta_0=-1$ | $\beta_1=1$ | $\beta_0=-1$ | $\beta_0=-1$ |
> | place value | $2^6$ | $2^5$ | $2^4$ | $2^3$ | $2^2$ | $2^1$ | $2^0$ |
>
> $$J(100) = 64+32-16-8+4-2-1 = \mathbf{73}$$

> [!example] Find J(20) using the radix-based property
> $20 = (10100)_2$. Same substitution ($\alpha=1$, then $\beta_1$ for 1-bits, $\beta_0$ for 0-bits):
>
> digits after the leading 1: $0,1,0,0$ → coefficients $\beta_0,\beta_1,\beta_0,\beta_0 = -1,+1,-1,-1$
>
> $$J(20) = 16 - 8 + 4 - 2 - 1 = \mathbf{9}$$
>
> matches the bit-rotation result and the closed form ($20=2^4+4 \Rightarrow J=2(4)+1=9$).

---

## 8. Controlling the Elimination Order with LCM

A different flavor of problem: instead of solving for the survivor, we choose the **step size $m$** (eliminate every $m$-th person, not just every 2nd) so that a *whole group* of people is eliminated before any other group.

### Two groups: n good, n bad ($2n$ people total)

> [!question] Setup
> $2n$ people stand in a circle. Positions $1,\dots,n$ are "good," positions $n+1,\dots,2n$ are "bad." Find an integer $m$ such that executing every $m$-th person eliminates **all bad guys first**.

**Claim:** $m = \operatorname{lcm}(n+1, n+2, \dots, 2n)$ works.

**Justification:** While $k$ people remain in the circle, with $n+1\le k\le 2n$, we have $k\mid m$ by construction (every integer in that range divides the LCM). Counting $m$ steps around a $k$-person circle is the same as going around exactly $m/k$ full times — an integer number of laps — so the $m$-th count always lands back on the same (currently "next-up") person, which forces the eliminations to proceed strictly through the circle in the fixed descending order $2n, 2n-1,\dots,n+1$ while $k\ge n+1$ people remain — i.e. through the bad half before ever reaching a good position.

$$\boxed{m = \operatorname{lcm}(n+1,n+2,\dots,2n)}$$

> [!example] n = 3
> Total people $=2n=6$. Good $=\{1,2,3\}$, bad $=\{4,5,6\}$.
> $$m = \operatorname{lcm}(4,5,6) = \mathbf{60}$$

### Three groups: n good, n mixed, n bad ($3n$ people total)

> [!question] Setup
> $3n$ people in a circle: first $n$ good, middle $n$ mixed (good and bad), last $n$ bad. Find $m$ such that executing every $m$-th person eliminates **all bad first, then all middle, then all good.**

**Claim:** $m = \operatorname{lcm}(n+1, n+2, \dots, 3n)$ works, by the identical argument: for every remaining-count $k$ with $n+1\le k\le 3n$, $k\mid m$, so the elimination order while $k$ people remain is simply

$$3n,\ 3n-1,\ \dots,\ n+1$$

i.e. strictly descending position numbers. Consequently:
- positions $2n+1$ to $3n$ (bad) are eliminated first,
- positions $n+1$ to $2n$ (middle) are eliminated next,
- positions $1$ to $n$ (good) are eliminated last.

$$\boxed{m = \operatorname{lcm}(n+1,n+2,\dots,3n)}$$

---

## 9. Counter-Clockwise Variant

> [!question] Setup
> Same rules (eliminate every 2nd remaining person), but the circle is **traversed counter-clockwise**, while people are still **numbered sequentially clockwise**. With $n$ people, counting starts at person $n$ (moving counter-clockwise from "1" means the first step lands on $n$). Let $J_{acw}(n)$ be the survivor.

### Case n = 2m (even)

Starting at $2n$ and moving anti-clockwise, the first lap eliminates $2n-1, 2n-3,\dots,3,1$ — i.e. **all odd-numbered people**. The remaining people are $2,4,6,\dots,2n$. Renumbering consecutively, old $k \to$ new $k'$ via $k=2k'$, and the reduced circle is again a size-$n$ Josephus (CCW) problem, so:

$$\boxed{J_{acw}(2n) = 2\,J_{acw}(n), \qquad n\ge 1}$$

### Case n = 2m+1 (odd)

Starting at $2n+1$ and moving anti-clockwise, the first lap eliminates $2n, 2n-2,\dots,2$ (all evens), and immediately after eliminating person $2$, person $2n+1$ is also eliminated (the count wraps). Remaining: $1,3,5,\dots,2n-1$. Renumbering via $k = 2k'-1$:

$$\boxed{J_{acw}(2n+1) = 2\,J_{acw}(n) - 1, \qquad n\ge 1}$$

### Full recurrence

$$J_{acw}(1) = 1$$
$$J_{acw}(2n) = 2\,J_{acw}(n), \qquad n\ge 1$$
$$J_{acw}(2n+1) = 2\,J_{acw}(n) - 1, \qquad n\ge 1$$

**Small-value table**

| n | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | 13 | 14 | 15 | 16 |
|---|---|---|---|---|---|---|---|---|---|----|----|----|----|----|----|----|
| $J_{acw}(n)$ | 1 | 2 | 1 | 4 | 3 | 2 | 1 | 8 | 7 | 6 | 5 | 4 | 3 | 2 | 1 | 16 |

**Observations:**
- For $n=1,2,4,8,16,\dots$ (powers of 2): $J_{acw}(2^m) = 2^m$.
- For $n$ between $2^m$ and $2^{m+1}$, $J_{acw}(n)$ runs **downward** sequentially: $2^m, 2^m-1,\dots,1$ — the mirror image of the clockwise case (which increases by 2 within a group, this one decreases by 1).

### Closed form

Write $n = 2^m+l$, $0\le l < 2^m$ ($2^m$ = largest power of 2 not exceeding $n$). Then

$$\boxed{J_{acw}(2^m+l) = 2^m - l, \qquad m\ge 0,\ 0\le l<2^m}$$

**Proof (induction on m):**

*Base case ($m=0$):* $l=0$, so $J_{acw}(1) = 2^0 - 0 = 1$ ✓.

*Inductive hypothesis:* assume $J_{acw}(2^m+l) = 2^m - l$ for all $0\le l<2^m$.

*Sub-case $l$ even:* $2^{m+1}+l = 2n$ with $n = 2^m + \tfrac{l}{2}$:
$$J_{acw}(2^{m+1}+l) = J_{acw}(2n) = 2J_{acw}(n) = 2\left(2^m - \tfrac{l}{2}\right) = 2^{m+1} - l$$

*Sub-case $l$ odd:* $2^{m+1}+l = 2n+1$ with $n = 2^m + \tfrac{l-1}{2}$:
$$J_{acw}(2^{m+1}+l) = J_{acw}(2n+1) = 2J_{acw}(n) - 1 = 2\left(2^m - \tfrac{l-1}{2}\right) - 1 = 2^{m+1} - (l-1) - 1 = 2^{m+1} - l$$

Both cases give $2^{m+1}-l$, completing the induction. $\blacksquare$

> [!example] J_acw(20)
> $20 = 2^4 + 4$, so $J_{acw}(20) = 2^4 - 4 = \mathbf{12}$.

> [!tip] Relationship to the clockwise result
> $J_{cw}(n) = 2l+1$ climbs by 2 through each power-of-two block; $J_{acw}(n) = 2^m-l$ descends by 1 through each block — reflecting that CCW traversal effectively reverses the direction in which surviving numbers get filled in.

---

## Summary Cheat-Sheet

| Result | Formula |
|---|---|
| Recurrence | $J(1)=1;\ J(2n)=2J(n)-1;\ J(2n+1)=2J(n)+1$ |
| Closed form | $J(2^m+l) = 2l+1,\ 0\le l<2^m$ |
| Binary trick | rotate $n$'s binary digits left by 1 bit |
| Generalized recurrence | $f(1)=\alpha;\ f(2n)=2f(n)+\beta;\ f(2n+1)=2f(n)+\gamma$ |
| Generalized closed form | $f(2^m+l) = 2^m\alpha + (2^m-1-l)\beta + l\gamma$ |
| Radix-$d$ generalization | $f((b_m\cdots b_0)_d) = (\alpha_{b_m}\beta_{b_{m-1}}\cdots\beta_{b_0})_c$ |
| LCM trick (2 groups, size n each) | $m = \operatorname{lcm}(n+1,\dots,2n)$ eliminates bad half first |
| LCM trick (3 groups, size n each) | $m = \operatorname{lcm}(n+1,\dots,3n)$ eliminates bad → middle → good |
| CCW recurrence | $J_{acw}(1)=1;\ J_{acw}(2n)=2J_{acw}(n);\ J_{acw}(2n+1)=2J_{acw}(n)-1$ |
| CCW closed form | $J_{acw}(2^m+l) = 2^m - l,\ 0\le l<2^m$ |

## Related
- [[Integer Functions]] — floor/mod machinery used throughout this derivation.
