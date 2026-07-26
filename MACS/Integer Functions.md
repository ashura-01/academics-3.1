---
tags: [discrete-math, integer-functions, floor, ceiling, mod-arithmetic]
course: Recurrent Problems / Discrete Math
---

# Integer Functions

> [!info] Overview
> An **integer function** is a function whose output is always an integer, no matter what real-number input goes in. The three workhorses of this topic are the **floor**, **ceiling**, and **mod** functions — they show up constantly in algorithm analysis (bit-lengths, array indexing, cyclic problems like [[The Josephus Problem]]).

![[floor_ceiling.png]]

---

## 1. Floor Function `⌊x⌋`

**Definition:** the greatest integer ≤ x ("round down").

$$\lfloor x \rfloor = n \iff n \in \mathbb{Z} \text{ and } n \le x < n+1$$

**Key properties**
- Always an integer.
- If $x$ is already an integer, $\lfloor x \rfloor = x$.

**Examples**

| $x$ | $\lfloor x \rfloor$ | why |
|---|---|---|
| 3.7 | 3 | 3 is the greatest integer ≤ 3.7 |
| −2.3 | −3 | −3 is the greatest integer ≤ −2.3 (not −2!) |
| 5 | 5 | already an integer |
| 0.99 | 0 | 0 is the greatest integer ≤ 0.99 |

> [!warning] Common mistake
> For negative numbers, floor rounds **toward negative infinity**, not toward zero. $\lfloor -2.3\rfloor = -3$, not $-2$.

---

## 2. Ceiling Function `⌈x⌉`

**Definition:** the smallest integer ≥ x ("round up").

$$\lceil x \rceil = n \iff n \in \mathbb{Z} \text{ and } n-1 < x \le n$$

**Examples**

| $x$ | $\lceil x \rceil$ | why |
|---|---|---|
| 3.2 | 4 | smallest integer ≥ 3.2 |
| −2.3 | −2 | smallest integer ≥ −2.3 |
| 5 | 5 | already an integer |
| 0.01 | 1 | smallest integer ≥ 0.01 |

---

## 3. Floor/Ceiling Identities

$$\lfloor x \rfloor = x \iff x \text{ is an integer} \qquad \lceil x \rceil = x \iff x \text{ is an integer}$$

$$\lfloor -x \rfloor = -\lceil x \rceil \qquad \lceil -x \rceil = -\lfloor x \rfloor$$

- $\lceil -1.6 \rceil = -1 = -\lfloor 1.6 \rfloor$
- $\lceil -2.9 \rceil = -\lfloor 2.9 \rfloor = -2$
- $\lfloor -2.9 \rfloor = -\lceil 2.9 \rceil = -3$

**Useful rewriting rules**

| Rule | Example |
|---|---|
| $\lfloor x\rfloor = n \iff n \le x < n+1$ | $\lfloor 2.6\rfloor = 2 \iff 2\le 2.6<3$ |
| $\lfloor x\rfloor = n \iff x-1 < n \le x$ | $\lfloor 2.6\rfloor = 2 \iff 1.6<2\le 2.6$ |
| $\lceil x\rceil = n \iff n-1 < x \le n$ | $\lceil 2.6\rceil = 3 \iff 2<2.6\le 3$ |
| $\lceil x\rceil = n \iff x \le n < x+1$ | $\lceil 3.6\rceil = 4 \iff 3.6\le 4<4.6$ |
| $\lfloor x+n\rfloor = \lfloor x\rfloor+n$, $n\in\mathbb{Z}$ | $\lfloor 2.9+2\rfloor = \lfloor 2.9\rfloor+2 = 4$ |
| $n\le x \iff n\le \lfloor x\rfloor$ | $3\le 3.5 \iff 3\le \lfloor 3.5\rfloor$ |
| $x\le n \iff \lceil x\rceil \le n$ | $3.5\le 4 \iff \lceil 3.5\rceil\le 4$ |

---

## 4. Proof: $\lfloor\sqrt{\lfloor x\rfloor}\rfloor = \lfloor\sqrt{x}\rfloor$

**If $x$ is an integer:** $\lfloor x\rfloor = x$, so the claim reduces to $\lfloor\sqrt{x}\rfloor=\lfloor\sqrt{x}\rfloor$ — trivially true.

**If $x$ is not an integer**, let $m = \lfloor\sqrt{\lfloor x\rfloor}\rfloor$. Then:

$$m \le \sqrt{\lfloor x\rfloor} < m+1$$
$$\Rightarrow m^2 \le \lfloor x\rfloor < (m+1)^2$$
$$\Rightarrow m^2 \le x < (m+1)^2 \quad \text{(since } n\le x \iff n\le\lfloor x\rfloor \text{, and } x\le n \iff \lceil x\rceil\le n\text{, with } m^2,(m+1)^2 \text{ integers)}$$
$$\Rightarrow m \le \sqrt{x} < m+1$$
$$\Rightarrow \lfloor\sqrt{x}\rfloor = m$$

So $\lfloor\sqrt{\lfloor x\rfloor}\rfloor = \lfloor\sqrt{x}\rfloor$. $\blacksquare$

---

## 5. Mod Function

For integers $a,b$ with $b>0$:

$$a \bmod b = a - b\left\lfloor \frac{a}{b}\right\rfloor, \qquad 0 \le a\bmod b < b$$

Special case: $x \bmod 0 = x$ (defined by convention, not derived).

> [!example] 10 mod 3
> $10 \bmod 3 = 10 - 3\lfloor 10/3\rfloor = 10 - 3(3) = 1$

**Property:** $C(x\bmod y) = Cx \bmod Cy$

*Verification:*
$$C(x\bmod y) = C\big(x - y\lfloor x/y\rfloor\big) = Cx - Cy\lfloor x/y\rfloor = Cx - Cy\left\lfloor \frac{Cx}{Cy}\right\rfloor = Cx\bmod Cy$$

---

## 6. Proof: $(x \bmod ny) \bmod y = x \bmod y$

**Case 1 — $y=0$:** then $ny=0$ too, so $(x\bmod 0)\bmod 0 = x \bmod 0 = x \bmod y$ (both sides equal $x$ by the $x\bmod 0=x$ convention).

**Case 2 — $y\ne 0$:** let $z = x - ny\lfloor x/ny\rfloor$ (i.e. $z = x\bmod ny$). Then

$$z \bmod y = z - y\left\lfloor \frac{z}{y}\right\rfloor = \Big(x - ny\Big\lfloor\frac{x}{ny}\Big\rfloor\Big) - y\left\lfloor \frac{x}{y} - n\Big\lfloor\frac{x}{ny}\Big\rfloor\right\rfloor$$

Since $n\lfloor x/ny\rfloor$ is an integer, it can be pulled out of the inner floor:

$$= x - ny\Big\lfloor\frac{x}{ny}\Big\rfloor - y\left\lfloor\frac{x}{y}\right\rfloor + ny\Big\lfloor\frac{x}{ny}\Big\rfloor = x - y\left\lfloor\frac{x}{y}\right\rfloor = x\bmod y$$

So $(x\bmod ny)\bmod y = x\bmod y$. $\blacksquare$

> [!tip] Chained application
> Because each modulus below divides the previous one (240 = 4·60, 60 = 2·30, 30 = 3·10, 10 = 2·5), this property telescopes:
> $$\big(\big(\big(\big(X\bmod 240\big)\bmod 60\big)\bmod 30\big)\bmod 10\big)\bmod 5 \;=\; X \bmod 5$$

---

## 7. Proof: bit-length formula $m = \lfloor \lg n\rfloor + 1$

If $n$ is an $m$-bit integer, the smallest such $n$ has a leading 1 followed by $(m{-}1)$ zeros ($=2^{m-1}$), and the largest has $m$ ones ($=2^m-1$). So:

$$2^{m-1} \le n \le 2^m - 1 \;\Rightarrow\; 2^{m-1}\le n < 2^m$$
$$\Rightarrow (m-1) \le \lg n < m \quad\text{(taking } \lg \text{ of all sides)}$$
$$\Rightarrow \lfloor \lg n\rfloor = m - 1 \quad \big(\text{since } \lfloor x\rfloor=n\iff n\le x<n+1\big)$$
$$\Rightarrow m = \lfloor\lg n\rfloor + 1$$

### Worked examples (corrected)

> [!bug] Error in the original slide
> The original slide computed **both** examples using "$\lfloor\lg 35\rfloor$" — that's a copy‑paste slip. The two examples are for $n=31$ and $n=32$; each must use its own $n$. Corrected below.

**How many bits to write 31 in binary?**
$$m = \lfloor\lg 31\rfloor + 1 = 4 + 1 = 5$$
So **5 bits** are required (check: $2^4=16\le 31<32=2^5$ ✓, and $31 = 11111_2$ exactly fills 5 bits).

**How many bits to write 32 in binary?**
$$m = \lfloor\lg 32\rfloor + 1 = 5 + 1 = 6$$
So **6 bits** are required ($32 = 100000_2$, which needs a 6th bit since $2^5=32$ itself is the boundary).

---

## 8. Mod with negative numbers — worked examples

Using $a\bmod b = a - b\lfloor a/b\rfloor$ for any sign of $b\ne0$:

> [!example] 43 mod 10
> $43 - 10\lfloor 43/10\rfloor = 43 - 10(4) = 3$

> [!example] 5 mod 3
> $5 - 3\lfloor 5/3\rfloor = 5 - 3(1) = 2$

> [!example] 5 mod −3
> $\lfloor 5/(-3)\rfloor = \lfloor -1.667\rfloor = -2$
> $5 - (-3)(-2) = 5 - 6 = -1$

> [!example] −5 mod 3
> $\lfloor -5/3\rfloor = \lfloor -1.667\rfloor = -2$
> $-5 - 3(-2) = -5+6 = 1$

> [!example] −5 mod −3
> $\lfloor -5/(-3)\rfloor = \lfloor 1.667\rfloor = 1$
> $-5 - (-3)(1) = -5+3 = -2$

> [!example] 3 mod 0
> $= 3$ (by the defined convention, since $y=0$)

> [!note] Sign convention
> For $b>0$ the result lies in $[0, b)$. For $b<0$ the result lies in $(b, 0]$ instead — that's why $5\bmod(-3) = -1$ and $-5\bmod(-3)=-2$ both come out **non‑positive**.

---

## Related
- [[The Josephus Problem]] — uses floor/mod-style bit manipulation to get a closed-form recurrence solution.
