# Quick Reference — Control Logic Design

## 5-Step Design Process

| Step | Action |
|---|---|
| 1 | State the problem clearly |
| 2 | Define equipment (registers, ALU, counters) |
| 3 | Derive algorithm as flowchart |
| 4 | Specify data processor (connect components, label control lines) |
| 5 | Design control logic (state diagram → Boolean equations or microprogram) |

---

## Sign-Magnitude Arithmetic Decision Table

| As | Bs | S = As⊕Bs | Action | Result Sign |
|---|---|---|---|---|
| 0 (pos) | 0 (pos) | 0 | Add magnitudes | Positive |
| 1 (neg) | 1 (neg) | 0 | Add magnitudes | Negative |
| 0 (pos) | 1 (neg) | 1 | Subtract, A≥B → A−B | Positive |
| 0 (pos) | 1 (neg) | 1 | Subtract, A<B → B−A | Negative |
| 1 (neg) | 0 (pos) | 1 | Subtract, A≥B → A−B | Negative |
| 1 (neg) | 0 (pos) | 1 | Subtract, A<B → B−A | Positive |

**Detecting A≥B after subtraction:**
- Compute `A + ~B + 1` (2's complement subtract)
- If Cout (E) = 1 → A ≥ B
- If Cout (E) = 0 → A < B → complement result and negate sign

---

## JK Flip-Flop Excitation Table

| Present Q | Next Q | J | K |
|---|---|---|---|
| 0 | 0 | 0 | X |
| 0 | 1 | 1 | X |
| 1 | 0 | X | 1 |
| 1 | 1 | X | 0 |

---

## ALU Function Select (s2, s1, s0, Cin)

| s2 | s1 | s0 | Cin | ALU Output |
|---|---|---|---|---|
| 0 | 0 | 0 | 0 | Transfer A (no operation) |
| 0 | 0 | 1 | 0 | A + B (add) |
| 0 | 1 | 0 | 1 | A + ~B + 1 (subtract via 2's complement) |
| 1 | 1 | 1 | 0 | ~A (complement) |
| 0 | 0 | 0 | 1 | A + 1 (increment) |

---

## Microprogram CAR Select Bits

| Bit 13 | Bit 14 | CAR Action |
|---|---|---|
| 0 | 0 | Increment CAR (sequential next instruction) |
| 0 | 1 | Load address field unconditionally (jump) |
| 1 | 0 | Load if S=1, else increment (branch on sign) |
| 1 | 1 | Load if E=1, else increment (branch on carry) |

---

## Binary Multiplication — Add and Shift Rules

| Q1 (LSB of multiplier) | Action this cycle |
|---|---|
| 1 | A←A+B, E←Cout, P←P−1, then shr EAQ, E←0 |
| 0 | P←P−1, then shr EAQ, E←0 |

Product sign: `As = Bs XOR Qs`

Termination: when P counter reaches 0 (Pz=1)
