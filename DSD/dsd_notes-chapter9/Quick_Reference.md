# Quick Reference — Control Logic Design

## How to Solve a Control Design Problem (5 Steps)

1. **State the problem** — what operation, what inputs/outputs
2. **Define equipment** — what registers, ALU, counters are needed
3. **Derive algorithm** — flowchart with decision diamonds and operation boxes
4. **Specify data processor** — connect registers to ALU, label control lines
5. **Design control logic** — state diagram → Boolean equations or microprogram

---

## Sign-Magnitude Arithmetic Rules

```
Add if signs match:    result sign = common sign
Subtract if different: subtract smaller magnitude from larger
                       result sign = sign of larger magnitude
```

Detecting sign difference: `S = As XOR Bs`
- S = 0: same signs, add
- S = 1: different signs, subtract

Subtraction in hardware: `A + ~B + 1` (2's complement of B)
Check which is larger: if Cout (E) = 1 after subtract → A >= B
If E = 0 after subtract → A < B → result needs complementing

---

## Binary Multiplication (Add-and-Shift)

```
For i = 1 to k (k = number of multiplier bits):
    if Q1 (LSB of Q) = 1:
        A ← A + B          (add multiplicand)
        E ← Cout
    P ← P - 1              (decrement counter)
    shr EAQ                 (right shift composite register)
    E ← 0
```

Product sign: `As = Bs XOR Qs`

---

## JK Flip-Flop Excitation Table

| Present Q | Next Q | J | K |
|---|---|---|---|
| 0 | 0 | 0 | X |
| 0 | 1 | 1 | X |
| 1 | 0 | X | 1 |
| 1 | 1 | X | 0 |

Use this when deriving J and K input equations from state table.

---

## ALU Function Select (Table 9-4)

| s2 | s1 | s0 | Cin | ALU Output |
|---|---|---|---|---|
| 0 | 0 | 0 | 0 | A (transfer) |
| 0 | 0 | 1 | 0 | A + B (add) |
| 0 | 1 | 0 | 1 | A + ~B + 1 (subtract) |
| 1 | 1 | 1 | 0 | ~A (complement) |
| 0 | 0 | 0 | 1 | A + 1 (increment) |

---

## CAR Select Bits (Microprogram)

| Bit 13 | Bit 14 | Action |
|---|---|---|
| 0 | 0 | Sequential (CAR+1) |
| 0 | 1 | Load address field unconditionally |
| 1 | 0 | Load if S=1, else CAR+1 |
| 1 | 1 | Load if E=1, else CAR+1 |
