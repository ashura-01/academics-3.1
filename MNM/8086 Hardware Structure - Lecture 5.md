---
tags: [microprocessor, 8086, hardware, CO, exam-prep]
source: Lecture5.pdf — Prof. Dr. Shamim Akhter
---

# 🧠 8086 Hardware Structure — Full Breakdown

> Bro, don't panic. This lecture *looks* scary because of all the pin names (BHE, ALE, DEN, RD, WR...) but it's really just a few big ideas wearing a trench coat:
> 1. The 8086 has 40 pins, and because it's small, some pins do **two jobs** (multiplexing).
> 2. There are **two ways to wire it up**: Minimum Mode (simple, 1 CPU) and Maximum Mode (complex, multiple processors).
> 3. Memory is split into an **Odd bank and an Even bank** so it can read/write 16 bits at once.
>
> Read this top to bottom once, then use the **Descriptive Exam Answers** section near the bottom to practice writing full answers, not just recalling facts.

>  **SYLLABUS BOUNDARY — READ THIS FIRST**
> Your exam currently covers **only up to "Maximum Mode"** of this lecture. That means:
> - ✅ **IN SYLLABUS (study hard):** Sections 1–6 below — pin multiplexing, pin-out, pin connections, odd/even memory banks, Minimum Mode, Latches/Buffers/Transceivers, Maximum Mode.
> - ⏳ **NOT YET IN SYLLABUS (reference only, skip for now):** Sections 7 onward — 8288 Bus Controller, 8284A Clock Generator, RESET operation, READY/Wait States, Bus Cycles (T1–T4), and the RC charging math. These are kept in this note so you have them ready when your teacher covers them later, but **don't burn study time on them yet.**
>
> I've marked the boundary again clearly further down so you don't accidentally over-study.

---

## 1. Why 8086 needs tricks: too many jobs, not enough pins

The 8086 needs:
- 20-bit **address bus** (to reach 1 MB of memory)
- 16-bit **data bus**
- ~20 bits of **control/status** signals
- power pins

That's **59 signals**, but the chip only has **40 physical pins**. 😱

**Solution → Multiplexing.** Some pins carry an address at one instant, and carry data a moment later. A separate signal called **ALE (Address Latch Enable)** tells external hardware "hey, right now the pins mean ADDRESS — grab it and store it," and after that, the same pins switch to carrying DATA.

![[minmax_intro.png]]

---

## 2. The Pin-Out (the map of all 40 pins)

![[pinout.png]]

Key things to remember:
- **DIP-40** package = 40 pins in 2 rows of 20.
- **8086** has a 16-bit data bus → pins `AD0–AD15` (Address/Data, multiplexed).
- **8088** (cheaper cousin) has only an 8-bit data bus → pins `AD0–AD7`, and `A8–A15` are plain address-only pins.
- Only real physical difference between 8086 and 8088:
  - Pin 34: 8086 = `BHE̅/S7`, 8088 = `SS0`
  - Pin 28: 8086 = `M/IO̅`, 8088 = `IO/M̅` (same idea, signal just flipped)

---

## 3. Pin Connections — what each multiplexed pin actually means

![[pin_connections_table.png]]

Plain-English translation:

| Pins | What's going on |
|---|---|
| `AD0–AD7` | Lowest 8 bits. Address when ALE=1, Data when ALE=0 |
| `AD8–AD15` (8086 only) | Upper 8 bits, same multiplexing trick |
| `A19/S6 – A16/S3` | Upper 4 address bits, but also carry **status bits** (S3–S6) during the rest of the cycle |

The **S3, S4** status bits tell you *which memory segment* the CPU is currently touching:

| S4 | S3 | Meaning |
|---|---|---|
| 0 | 0 | Extra Segment |
| 0 | 1 | Stack Segment |
| 1 | 0 | Code Segment |
| 1 | 1 | Data Segment |

(S5 = interrupt flag status, S6 = always 0 — minor detail, but examiners sometimes ask it.)

### The other important single-purpose control pins

![[rd_ready_intr_test.png]]

- **RD̅** — "I'm reading from memory/IO right now" (active when = 0)
- **READY** — from external device: "I'm ready, don't wait" (1) or "wait for me!" (0)
- **INTR** — external interrupt request line
- **TEST̅** — used with the `WAIT` instruction, mainly to sync with the 8087 math co-processor
- **NMI** — Non-Maskable Interrupt, like INTR but the CPU **can't ignore it**, even if interrupts are disabled
- **RESET** — hold this HIGH for 4 clock cycles → CPU resets, jumps to address `FFFF0H`
- **CLK** — the heartbeat of the CPU (needs 33% duty cycle: high 1/3 of the time, low 2/3)
- **MN/MX̅** — a single switch pin that decides: are we in **Minimum Mode** or **Maximum Mode**?
- **BHE̅/S7** — "Bus High Enable" → enables the upper byte of the data bus (D8–D15)

---

## 4. Odd/Even memory banks — the part that confuses everyone (but is simple once you see it)

**The big idea:** 8086 memory (1 MB) is physically split into **two parallel banks of 512 KB each**:

- **Even Bank** — holds bytes at addresses `0, 2, 4, 6...` (even addresses)
- **Odd Bank** — holds bytes at addresses `1, 3, 5, 7...` (odd addresses)

Why split it? Because the 8086 has a **16-bit (2-byte) data bus**. If both banks sit side by side, the CPU can grab **2 bytes in one single memory access** — much faster than 1 byte at a time.

![[even_odd_bank.png]]

Two signals decide which bank(s) get talked to:

| Signal | Meaning |
|---|---|
| `A0` | 0 = Even address, 1 = Odd address |
| `BHE̅` | 0 = Bus High Enable (talk to upper/Odd byte), 1 = disabled |

### The 4 access patterns (classic descriptive-question material)

**a) 8-bit read, EVEN address only** → `A0=0`, `BHE̅=1` (only even bank active)

![[access_even.png]]

**b) 8-bit read, ODD address only** → `A0=1`, `BHE̅=0` (only odd bank active)

![[access_odd.png]]

**c) 16-bit read starting at an EVEN address** → both banks active together, **1 single bus cycle** (fast!)

![[access_16bit_even.png]]

**d) 16-bit read starting at an ODD address** → this is the "gotcha" one. The data straddles both banks awkwardly, so the CPU needs **2 separate bus cycles**: first grab the odd byte, then grab the even byte next door. **This is slower** — it's why aligned (even-address) data access is preferred in performance-critical code.

![[access_16bit_odd.png]]

---

## 5. Minimum Mode vs Maximum Mode

This is the "which wiring style do I use" decision, controlled by pin `MN/MX̅`.

| | Minimum Mode | Maximum Mode |
|---|---|---|
| `MN/MX̅` pin | Tied to +5V (logic 1) | Tied to GND (logic 0) |
| Use case | **Single processor** system | **Multiple processors** (e.g., 8086 + 8087 math co-processor) |
| Who generates control signals | The 8086 CPU itself | An external **8288 Bus Controller** chip |
| Complexity | Simple circuit | More complex circuit |
| Performance | Lower | Higher |

### Minimum Mode wiring

![[min_mode_diagram.png]]

Minimum-mode-specific pins:

![[min_mode_pins_table.png]]

- **ALE** — Address Latch Enable: "capture the address NOW" (goes to a `8282` latch chip)
- **DT/R̅** — Data Transmit/Receive: is the data bus sending or receiving?
- **DEN̅** — Data bus Enable: turns on the external data buffer chip (`8286`)
- **HOLD / HLDA** — used by DMA controllers to temporarily "borrow" the bus from the CPU
  - `HOLD=1` → CPU stops, puts its buses into **high-impedance** (electrically disconnected, like an open switch) so someone else can use them
  - `HLDA=1` → CPU's way of saying "ok, go ahead, you have the bus"

### Maximum Mode wiring

![[max_mode_diagram.png]]

Maximum-mode-specific pins:

![[max_mode_pins_table.png]]

- **S2̅, S1̅, S0̅** — 3-bit status code, decoded by the 8288 controller to figure out what kind of bus cycle is happening (read/write/opcode-fetch/interrupt-ack/halt)
- **RQ/GT0̅, RQ/GT1̅** — Request/Grant lines, bidirectional versions of HOLD/HLDA for multiprocessor systems
- **LOCK̅** — "don't let any other bus master touch the bus" — used during atomic Read-Modify-Write operations
- **QS1, QS0** — Queue Status bits, tell the 8087 co-processor what's happening in the instruction prefetch queue

### The S2 S1 S0 status code table (memorize this — very examinable)

![[s2s1s0_timing.png]]

| S2̅ | S1̅ | S0̅ | Function |
|---|---|---|---|
| 0 | 0 | 0 | Interrupt Ack |
| 0 | 0 | 1 | I/O Read |
| 0 | 1 | 0 | I/O Write |
| 0 | 1 | 1 | Halt |
| 1 | 0 | 0 | Opcode Fetch |
| 1 | 0 | 1 | Memory Read |
| 1 | 1 | 0 | Memory Write |
| 1 | 1 | 1 | Passive (idle) |

### The 8088's SS0 table (uses IO/M̅ + DT/R̅ + SS0 instead of the 3 status bits)

![[ss0_table.png]]

---

## 6. Latches, Buffers, and Transceivers — the "helper chips" that de-multiplex everything

Since the CPU's pins carry BOTH address and data at different moments, you need external chips to grab the address at the right instant and hold onto it (**latch**), and to pass data through cleanly in both directions (**buffer / transceiver**).

![[bus_latch_buffer.png]]

**Latch vs Buffer — the key difference:**
- A **buffer** just passes the signal straight through (with a small delay). No memory.
- A **latch** *remembers* the value — it grabs whatever was on the input at the moment its control signal fires, and **holds it steady** even after the input changes. This is exactly what you need to "freeze" the address after ALE pulses.

### Three-state (tri-state) buffer

![[tristate_buffer.png]]

Outputs can be: **0**, **1**, or **Z (high impedance / disconnected)**. When disabled, it's like the wire isn't even connected — this lets multiple chips share the same bus without fighting each other.

### Bidirectional buffer (transceiver) — e.g. the 8286 chip

![[transceiver.png]]

One `DIR` (direction) pin decides which way data flows:
| DIR | Action |
|---|---|
| 0 | B → A |
| 1 | A → B |

### Latches (D-type flip-flops) — e.g. the 8282/74LS373 chip

![[latches.png]]

- While enable `G` is HIGH → output `Q` just follows input `D` (transparent)
- When `G` goes LOW → `Q` freezes at whatever `D` was → **that's the "latch"**

### Real chips used to build an 8086 system

| Chip | Role |
|---|---|
| `74LS373` / `8282` | Octal Latch — captures the address |
| `74LS244` | Octal 3-state Buffer |
| `74LS245` / `8286` | Bus Transceiver — bidirectional data buffer |

---

## 📝 Descriptive Exam Answers (Sections 1–6, in-syllabus)

Your exam is **descriptive**, not MCQ — so you need to *write full explanations*, not just recall a keyword. Below are model answers written the way you should answer in your exam script. Read once, then close the note and try rewriting them yourself.

---

**Q1. Why are the 8086's address, data, and status lines multiplexed? Explain with the role of ALE.**

> The 8086 requires a 20-bit address bus, a 16-bit data bus, and around 20 bits of control/status signals — roughly 59 signal lines in total. However, the chip is packaged in only a 40-pin DIP, which is not enough pins to give every signal its own dedicated line. To solve this, Intel multiplexed several pins so that the same physical pin carries different information at different times within a single bus cycle. For example, pins AD0–AD15 carry the address during the first part of the cycle and carry data during the later part. The signal ALE (Address Latch Enable) tells external circuitry exactly when the switch happens: when ALE = 1, whatever is currently on the multiplexed pins is a valid address, and this address must be captured (latched) into an external latch chip such as the 8282. Once ALE goes back to 0, the same pins are free to carry data instead. This is why external latch/buffer chips are required in any real 8086-based system — they de-multiplex the address and data so each can be used by the rest of the circuit independently.

---

**Q2. Explain the purpose of the BHE̅ and A0 pins, and describe how they control access to the odd and even memory banks.**

> The 8086 has a 16-bit data bus, and to make full use of it, system memory is physically organized into two separate 512 KB banks: an Even bank, which holds all bytes at even addresses, and an Odd bank, which holds all bytes at odd addresses. Two control signals decide which bank(s) are active during a given bus cycle. The A0 line (the least significant address bit) selects between even and odd: A0 = 0 selects the Even bank, and A0 = 1 selects the Odd bank. The BHE̅ (Bus High Enable, active-low) signal separately enables the upper byte of the data bus (D8–D15), which corresponds to the Odd bank. By combining these two signals, the 8086 can perform four distinct types of memory access: an 8-bit access to only the even bank (A0=0, BHE̅=1), an 8-bit access to only the odd bank (A0=1, BHE̅=0), a 16-bit access starting at an even address where both banks are read/written together in a single bus cycle (A0=0, BHE̅=0), and a 16-bit access starting at an odd address, which requires two separate bus cycles because the word is split across both banks — making unaligned (odd-address) word access noticeably slower than aligned (even-address) access.

---

**Q3. Differentiate between the Minimum Mode and Maximum Mode of operation of the 8086.**

> The 8086 can operate in one of two modes, selected by the state of a single pin, MN/MX̅. In Minimum Mode, this pin is tied permanently to +5V (logic 1), and the system is designed around a single processor. In this mode, the 8086 itself directly generates all the bus control signals — such as RD̅, WR̅, DT/R̅, and DEN̅ — needed to drive memory and I/O devices. Because the CPU handles this internally, the surrounding circuit is relatively simple, but this simplicity comes at the cost of lower overall performance. In Maximum Mode, the MN/MX̅ pin is tied to ground (logic 0), and the system is designed for a multiprocessor environment, such as one that includes the 8087 numeric co-processor or the 8089 I/O co-processor. In this mode, several of the CPU's pins change function: instead of directly outputting control signals, the CPU outputs a 3-bit status code (S2̅S1̅S0̅) that describes the current bus cycle. This code is fed into an external chip, the 8288 Bus Controller, which decodes it and generates the actual control signals (memory read/write, I/O read/write, interrupt acknowledge, etc.). Because of this extra coordination between multiple processors and an external bus controller, Maximum Mode requires a more complex circuit, but it allows significantly higher performance and supports systems with more than one processor sharing the same bus.

---

**Q4. What is the difference between a latch and a buffer? Why does an 8086 system need both?**

> Both latches and buffers are used to condition signals as they pass from the CPU's multiplexed pins to the rest of the system, but they behave differently. A buffer simply passes an input signal through to its output after a short propagation delay, and in three-state buffers, it can also be electrically disconnected (put into a high-impedance "Z" state) when disabled — but a buffer has no memory of its own; if the input changes, the output changes with it immediately. A latch, on the other hand, adds memory: it captures and holds ("latches") the value of its input at the moment a separate control signal is asserted, and continues to output that captured value even if the input subsequently changes, until the control signal is asserted again. In an 8086 system, a latch (such as the 8282, triggered by ALE) is needed to capture the address that briefly appears on the multiplexed AD lines and hold it steady for the rest of the bus cycle, since the same physical pins will soon switch over to carrying data. A buffer or bidirectional transceiver (such as the 8286) is then used to control the flow of the actual data between the CPU and the external data bus, since data needs to travel in different directions (into the CPU during a read, out of the CPU during a write) and needs to be electrically isolated from the bus when not actively transmitting.

---

**Q5. Explain the function of the status bits S2̅, S1̅, and S0̅ in Maximum Mode.**

> In Maximum Mode, the 8086 does not directly drive the memory and I/O control lines. Instead, at the start of every bus cycle, it outputs a 3-bit status code on the pins S2̅, S1̅, and S0̅. This code identifies exactly what kind of bus cycle is about to take place — for example, 000 indicates an interrupt acknowledge cycle, 001 indicates an I/O read, 010 an I/O write, 011 a halt, 100 an opcode fetch, 101 a memory read, 110 a memory write, and 111 a passive (idle) state. The external 8288 Bus Controller continuously monitors these three status lines and decodes them to generate the appropriate command signals (such as MRDC̅, MWTC̅, IORC̅, IOWC̅, or INTA̅) that actually drive the memory and I/O devices. This separation allows multiple bus masters (like a math co-processor) to share and coordinate access to the same system bus.

---

# ⏳ NOT YET IN SYLLABUS — Reference Material Only

> Everything from here down covers material **after** "Maximum Mode" in Lecture 5 — the 8288 Bus Controller's internal pin functions, the 8284A clock/reset/wait circuitry, and bus cycle timing. **Your teacher hasn't reached this yet**, so you don't need to memorize it for the current exam. It's kept here so the note is complete and ready for when the syllabus catches up — just scroll past this for now.

---

## 7. The 8288 Bus Controller — pin-level detail

![[8288_bus_controller.png]]

![[8288_pin_functions.png]]

- **AEN̅** — Address Enable, lets the 8288 enable the memory control signals
- **CEN** — Control Enable, turns the command output pins on/off
- **IOB** — chooses I/O-bus-mode vs system-bus-mode
- **MCE/PDEN̅** — cascade control for interrupt controllers, or peripheral data enable
- **AMWC̅ / AIOWC̅** — "Advanced" write signals that fire one clock cycle earlier than normal — some slower memory/IO chips need the extra head start

---

## 8. The 8284A Clock Generator

![[8284_clock_generator.png]]

The 8086 doesn't generate its own clock — a separate chip, the 8284A, does 3 jobs: **Clock generation**, **Reset shaping**, and **Ready synchronization**.

Clock path: crystal (e.g. 15 MHz) → internal oscillator → ÷3 → `PCLK` (2.5 MHz) → ÷2 → `CLK` (5 MHz, drives the 8086).

---

## 9. RESET operation

![[reset_operation.png]]
![[reset_figure94.png]]
![[schmitt_trigger.png]]

Hold `RESET` HIGH for ≥4 clock cycles → CPU jumps to `FFFF0H`, IF cleared, all registers = 0. Built using a Resistor-Capacitor-Diode filter circuit + a Schmitt trigger (converts a slow analog ramp into a clean digital edge).

---

## 10. READY signal & Wait States

![[ready_sync_circuit.png]]
![[ready_wait_state.png]]
![[wait_state_circuit.png]]

`READY = 0` → CPU inserts extra clock cycles (Tw) between T2 and T3 until the slow device is ready.

---

## 11. Bus Cycles (T1–T4)

![[bus_cycle_read.png]]
![[bus_cycle_write.png]]

Every memory/IO access takes at least 4 clock states: T1 (address out) → T2 (RD̅/WR̅ + DEN̅ activate) → T3 → T4 (data actually moves).

---

## 12. RC Charging Math (used to explain the RESET circuit)

![[rc_charging_formula.png]]
![[rc_problem_statement.png]]
![[rc_problem_a.png]]
![[rc_problem_b.png]]
![[rc_problem_c.png]]

$$V_C(t) = V_S(1-e^{-t/RC})$$

Worked example: $R=47k\Omega$, $C=1000\mu F$ → $\tau = 47s$ → time to reach 2.5V ≈ 32.58s → voltage after 100s ≈ 4.404V.

---

## 📝 Practice Questions straight from the slides (mixed — check against the boundary above)

1. Explain the purpose of the `BHE̅`, `ALE`, and `A0` pins on the 8086 microprocessor. *(✅ in syllabus — see Q2 model answer above, and add ALE from Q1)*
2. Explain three major functionalities of the 8284 Clock generator circuit. *(⏳ not yet in syllabus)*
3. Draw the reset (R-C) circuit and explain the activities of the manual reset pin. *(⏳ not yet in syllabus)*
4. Explain the procedure to generate the wait state. *(⏳ not yet in syllabus)*
