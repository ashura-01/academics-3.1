---
title: "Lecture 1.1 — Introduction to Digital System Design"
course: Digital System Design
tags: [DSD, lecture, combinational-circuits, adders]
book: "Digital Logic and Computer Design (Indian Edition) — M. Morris Mano"
chapters: "9.1 to 9.18"
date: 2026-06-18
---

# Lecture 1.1 — What is a System?

> Prepared By - Mohsena Ashraf

##  What is a System?

A **system** is a set of related components working as a whole to achieve a definite goal.

A system contains:
- **a.** Inputs
- **b.** A function that converts the inputs to outputs
- **c.** Outputs

```mermaid
flowchart LR
    I[Inputs] --> F[Function / Algorithm]
    F --> O[Outputs]
```

> **Design** = Noun → *Plan*, Verb → *Process*

---

## What is a Digital System?

A system in which signals have a **finite number of discrete values**.

| Signal Type | Description |
|---|---|
| **Analog** | Continuous, complex waveform |
| **Discrete** | Finite set of distinct values |
| **Digital** | $n \times 2^n$ style discrete-valued signals |

### Advantages
1. Easy to design
2. Low cost, automated design and fabrication

###  Disadvantages
1. Physical world is analog
2. Less precision

### Examples
- Calculator
- Digital Voltmeter

---

## Combinational vs Sequential Circuits

### A Combinational Circuit
Consists of logic gates whose outputs **at any time** are determined directly from the *present* combination of inputs, without regard to previous inputs.

> **Example:** Half Adder, Full Adder

### A Sequential Circuit
The outputs depend not only on **present inputs**, but also on **past inputs**. The circuit behavior (function) must be specified by a time sequence of inputs and internal states.

> **Example:** Flip-Flop

```mermaid
flowchart LR
    In1[Inputs] --> Comb[Combinational Circuit]
    Comb --> Out1[Outputs]
```

---

## Adders

| Adder | Inputs | Operation | Outputs |
|---|---|---|---|
| **Half Adder** | 2 (A, B) | A + B | Sum, Carry |
| **Full Adder** | 3 (A, B, Cin) | A + B + Cin | Sum, Carry-out |

###  Half Adder

The half adder accepts two binary digits as input and produces two binary digits as output — a **sum** bit and a **carry** bit.

**Truth Table:**

| A | B | S | C |
|---|---|---|---|
| 0 | 0 | 0 | 0 |
| 0 | 1 | 1 | 0 |
| 1 | 0 | 1 | 0 |
| 1 | 1 | 0 | 1 |

**Output Function:**

$$S = A'B + AB' = A \oplus B$$
$$C = AB$$

**Logic Circuit:**

```mermaid
flowchart LR
    A((A)) --> XOR{{XOR}}
    B((B)) --> XOR
    XOR --> S((S))
    A --> AND{{AND}}
    B --> AND
    AND --> C((C))
```

---

###  Full Adder

The full adder accepts three binary digits as input and produces two binary digits as output — a **sum** bit and a **carry** bit.

**Truth Table:**

| A | B | Cin | S | Cout |
|---|---|---|---|---|
| 0 | 0 | 0 | 0 | 0 |
| 0 | 0 | 1 | 1 | 0 |
| 0 | 1 | 0 | 1 | 0 |
| 0 | 1 | 1 | 0 | 1 |
| 1 | 0 | 0 | 1 | 0 |
| 1 | 0 | 1 | 0 | 1 |
| 1 | 1 | 0 | 0 | 1 |
| 1 | 1 | 1 | 1 | 1 |

**Output Function:**

$$S = A \oplus B \oplus C_{in}$$
$$C_{out} = AB + (A \oplus B)C_{in}$$

**Logic Circuit:**

```mermaid
flowchart LR
    A((A)) --> X1{{XOR}}
    B((B)) --> X1
    X1 --> X2{{XOR}}
    Cin((Cin)) --> X2
    X2 --> S((S))

    A --> AND1{{AND}}
    B --> AND1
    X1 --> AND2{{AND}}
    Cin --> AND2
    AND1 --> OR{{OR}}
    AND2 --> OR
    OR --> Cout((Cout))
```

#### Full Adder — Carry Recurrence (Generate/Propagate form)

Let $C_{in} = C_i$. Then:

$$S_i = A_i \oplus B_i \oplus C_i$$

$C_{out}$ becomes the $C_{in}$ for the next stage:

$$C_{i+1} = A_iB_i + (A_i \oplus B_i)C_i$$

Let $P_i = A_i \oplus B_i$ (**propagate**) and $G_i = A_iB_i$ (**generate**). Then:

$$C_{i+1} = G_i + P_iC_i$$

Substituting $i = 1, 2, \dots$:

$$C_2 = G_1 + P_1C_1$$
$$C_3 = G_2 + P_2C_2 = G_2 + P_2(G_1 + P_1C_1)$$

$$\vdots$$

---

##  Binary Adder (Ripple Carry Adder)

A chain of Full Adders, where the carry-out of one stage feeds the carry-in of the next.

```mermaid
flowchart RL
    subgraph FA0[Full Adder]
    end
    subgraph FA1[Full Adder]
    end
    subgraph FA2[Full Adder]
    end
    subgraph FAn[Full Adder]
    end

    B0((B0)) --> FA0
    A0((A0)) --> FA0
    FA0 --> S0((S0))
    FA0 -- Cout --> FA1

    B1((B1)) --> FA1
    A1((A1)) --> FA1
    FA1 --> S1((S1))
    FA1 -- Cout --> FA2

    B2((B2)) --> FA2
    A2((A2)) --> FA2
    FA2 --> S2((S2))
    FA2 -- Cout --> FAn

    Bn((Bn)) --> FAn
    An((An)) --> FAn
    FAn --> Sn((Sn))
    FAn -- Cout --> Cfinal((Cout))
```

---

## Full Adder using Half Adders

A Full Adder can be built by cascading **two Half Adders** and an OR gate.

**Block Diagram:**

```mermaid
flowchart LR
    A((A)) --> HA1[Half Adder 1]
    B((B)) --> HA1
    HA1 -- S --> HA2[Half Adder 2]
    Cin((Cin)) --> HA2
    HA1 -- C --> OR{{OR}}
    HA2 -- C --> OR
    OR --> Cout((Cout))
    HA2 -- S --> Sout((S))
```

**Circuit Diagram (Gate Level):**

```mermaid
flowchart LR
    X((X)) --> XOR1{{XOR}}
    Y((Y)) --> XOR1
    X --> AND1{{AND}}
    Y --> AND1

    XOR1 --> XOR2{{XOR}}
    Z((Z)) --> XOR2
    XOR1 --> AND2{{AND}}
    Z --> AND2

    XOR2 --> S((S))
    AND1 --> OR{{OR}}
    AND2 --> OR
    OR --> C((C))
```

> **Note:** X, Y, Z correspond to A, B, Cin — each of the two half-adders computes Sum (⊕) and Carry (AND), and a final OR combines the two carry terms into C = Cout.

---

## 🗂 Related Notes
- [[Lecture3]] — Decoders, Encoders, and Sequential Circuits

## ✍️ Handwritten Reference (18/06/26)
- Book: *Digital Logic and Computer Design (Indian Edition)* — M. Morris Mano, Ch. 9.1–9.18
- Sketch: Analog vs Discrete vs Digital signal → Function/Algorithm block → Output
- Adder section mirrors slide content: Half Adder (2 no./digit add → sum, cout), Full Adder (3 no./digit add → sum, cout)
- Binary Adder ripple chain sketch: $B_3A_3 \to B_2A_2 \to B_1A_1 \to B_0A_0$ through cascaded Full Adders producing $S_3S_2S_1S_0$
