---
tags: [microprocessor, 8086, hardware, CO, exam-prep]
source: Lecture5.pdf — Prof. Dr. Shamim Akhter
---

# 🧠 8086 Hardware Structure — Full Breakdown

> Bro, don't panic. This lecture *looks* scary because of all the pin names (BHE, ALE, DEN, RD, WR...) but it's really just **5 big ideas** wearing a trench coat:
> 1. The 8086 has 40 pins, and because it's small, some pins do **two jobs** (multiplexing).
> 2. There are **two ways to wire it up**: Minimum Mode (simple, 1 CPU) and Maximum Mode (complex, multiple processors).
> 3. Memory is split into an **Odd bank and an Even bank** so it can read/write 16 bits at once.
> 4. There's a **clock + reset + wait-state** system (8284A chip) that keeps everything in sync.
> 5. Every memory/IO access happens in a **4-step "bus cycle"** (T1, T2, T3, T4).
>
> Read this top to bottom once, then use the "Quick Recall" table at the very bottom before your exam.

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

👉 **Exam tip:** If asked "difference between 8086 and 8088" → data bus width (16 vs 8 bit) + those two pins.

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

(S5 = interrupt flag status, S6 = always 0 — mostly trivia, but examiners love asking it.)

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

### The 4 access patterns (this is what shows up in exam MCQs)

**a) 8-bit read, EVEN address only** → `A0=0`, `BHE̅=1` (only even bank active)

![[access_even.png]]

**b) 8-bit read, ODD address only** → `A0=1`, `BHE̅=0` (only odd bank active)

![[access_odd.png]]

**c) 16-bit read starting at an EVEN address** → both banks active together, **1 single bus cycle** (fast!)

![[access_16bit_even.png]]

**d) 16-bit read starting at an ODD address** → this is the "gotcha" one. The data straddles both banks awkwardly, so the CPU needs **2 separate bus cycles**: first grab the odd byte, then grab the even byte next door. **This is slower** — it's why aligned (even-address) data access is preferred in performance-critical code.

![[access_16bit_odd.png]]

> 🎯 **Exam one-liner:** "Why is accessing 16-bit data at an odd address slower?" → Because it needs two separate bus cycles instead of one, since the word is split across the Odd and Even banks.

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

## 7. The 8288 Bus Controller (Maximum Mode's "manager")

In Maximum Mode, the CPU doesn't generate `RD̅`, `WR̅`, etc. directly — it just outputs the 3-bit `S2S1S0` status code, and hands the job of generating actual control signals to a separate chip: the **8288 Bus Controller**.

![[8288_bus_controller.png]]

It has two halves:
1. **Status Decoder → Command Signal Generator** → outputs `MRDC̅` (memory read), `MWTC̅` (memory write), `IORC̅` (I/O read), `IOWC̅` (I/O write), `INTA̅` (interrupt ack), etc.
2. **Control Logic → Control Signal Generator** → outputs `DT/R̅`, `DEN̅`, `ALE` (same jobs as in Minimum Mode, just generated externally now)

### 8288's own pins

![[8288_pin_functions.png]]

- **AEN̅** — Address Enable, lets the 8288 enable the memory control signals
- **CEN** — Control Enable, turns the command output pins on/off
- **IOB** — chooses I/O-bus-mode vs system-bus-mode
- **MCE/PDEN̅** — cascade control for interrupt controllers, or peripheral data enable
- **AMWC̅ / AIOWC̅** — "Advanced" write signals that fire **one clock cycle earlier** than normal — some slower memory/IO chips need the extra head start

---

## 8. The 8284A Clock Generator (Clock + Reset + Ready, all in one chip)

The 8086 doesn't generate its own clock — a separate chip, the **8284A**, does 3 jobs:
1. **Clock** generation
2. **Reset** signal shaping
3. **Ready** signal synchronization

![[8284_clock_generator.png]]

**Clock path:** A crystal (e.g. 15 MHz) or external frequency source → internal oscillator → divided by 3 → gives `PCLK` (peripheral clock, 2.5 MHz) → divided again by 2 → gives `CLK` (5 MHz), which is what actually drives the 8086.

`F/C̅` pin picks internal crystal vs external frequency input (`EFI`).

---

## 9. RESET — how the CPU boots up / recovers

![[reset_operation.png]]

**Rule:** Hold the `RESET` pin HIGH for **at least 4 clock cycles** → CPU resets.

When reset happens:
- CPU starts executing from address **`FFFF0H`**
- **IF (Interrupt Flag)** is cleared → interrupts disabled
- All registers become 0
- PC (instruction pointer) and SP jump to their initial values

### The actual reset circuit (Resistor-Capacitor-Diode filter)

![[reset_figure94.png]]

This little RC circuit makes sure the RESET pin is:
- HIGH no later than **4 clock cycles** after power turns on
- held HIGH for **at least 50 microseconds**

Two ways to trigger reset: **Power-on reset** (automatic, happens every time you turn the system on) or **Manual reset** (a physical reset button, using the `RES̅` pin which is active-low).

### Why is there a "Schmitt trigger" in there?

![[schmitt_trigger.png]]

Because raw analog signals (like the voltage ramping up from a capacitor charging) are noisy/slow, not a clean digital 0/1. A **Schmitt trigger** is a circuit that converts a messy analog input into a clean digital output, using **two different thresholds**:

- Input above the **upper threshold** → output snaps to HIGH
- Input below the **lower threshold** → output snaps to LOW
- Input between the two thresholds → output just **holds its last value** (this gap prevents flickering/noise from causing false triggers)

---

## 10. READY signal & Wait States (how the CPU waits for slow memory)

Not every memory or I/O chip is fast enough to keep up with the CPU. The **READY** pin lets a slow device tell the CPU "hang on a sec."

![[ready_sync_circuit.png]]

- `READY = 1` → no effect, CPU proceeds normally
- `READY = 0` → CPU inserts extra clock cycles called **Wait States (Tw)** and just idles until the device says it's ready

Two READY inputs exist (`RDY1`, `RDY2`) for systems with 2 separate bus masters, each gated by its own `AEN` (Address Enable) qualifier.

![[ready_wait_state.png]]

- If READY is 0 at the end of **T2**, the CPU delays and inserts **Tw** between T2 and T3.
- READY gets re-checked in the **middle of Tw** to decide: insert another Tw, or move on to T3?

### The wait-state generator circuit (how you'd build a "fixed delay" for a slow chip)

![[wait_state_circuit.png]]

A shift register (`LS164`) is clocked along with the CPU clock, and its outputs feed logic that holds `RDY1` low for a fixed number of extra clocks (e.g. "always insert exactly 4 wait states for this slow ROM chip").

---

## 11. Bus Cycles — the actual step-by-step timeline of a memory access

Every single memory/IO access takes **at least 4 clock periods**, called **T1, T2, T3, T4**.

At 5 MHz, one clock period = 200 ns, so a full bus cycle = 4 × 200 ns = **800 ns**.

### Read Cycle

![[bus_cycle_read.png]]

| State | What happens |
|---|---|
| **T1** | CPU puts the **address** on the bus. `ALE`, `DT/R̅`, `M/IO̅` all get set |
| **T2** | `RD̅` and `DEN̅` activate; the bus briefly goes to high-impedance to "turn around" |
| **T3** | Bus is now reserved for incoming data |
| **T4** | Data is actually read in; `RD̅` deactivates |

### Write Cycle

![[bus_cycle_write.png]]

| State | What happens |
|---|---|
| **T1** | CPU puts the **address** on the bus, same setup pins as read |
| **T2** | `WR̅` and `DEN̅` activate; CPU puts data on the data bus |
| **T3 / T4** | Data is actually written out to memory/IO. In T4, all bus signals reset for the next cycle |

> 🎯 **Read vs Write, the one-line difference:** in a read cycle the bus "turns around" (goes high-Z) between address-out and data-in; in a write cycle the CPU keeps driving the bus the whole time since it's the one sending the data.

---

## 12. Bonus Math: RC Charging Circuit (appears with RESET, but also a standalone formula)

This isn't 8086-specific — it's the general capacitor-charging formula used to explain **why** the RESET circuit needs a resistor + capacitor.

![[rc_charging_formula.png]]

$$V_C(t) = V_S \left(1 - e^{-t/RC}\right)$$

Where:
- $V_C$ = voltage across the capacitor at time $t$
- $V_S$ = supply voltage
- $t$ = elapsed time
- $RC$ = the **time constant** ($\tau$) — how "fast" the circuit charges

**Rule of thumb curve shape:**
- After **1τ** → capacitor reaches **63%** of final voltage
- After **4τ** → reaches **98%**
- After **5τ** → essentially fully charged (≈100%)

### Worked example from the slides

![[rc_problem_statement.png]]

Given: $V_S = 5V$, $R = 47k\Omega$, $C = 1000\mu F$

**(a) Time constant?**

![[rc_problem_a.png]]

$$\tau = R \times C = 47\times10^3 \times 1000\times10^{-6} = 47 \text{ seconds}$$

**(b) Time to reach 2.5V?**

![[rc_problem_b.png]]

$$2.5 = 5(1-e^{-t/47}) \Rightarrow e^{-t/47}=0.5 \Rightarrow t = 47 \times \ln(2) \approx 32.58\text{ s}$$

**(c) Voltage after 100 seconds?**

![[rc_problem_c.png]]

$$V_C = 5(1-e^{-100/47}) \approx 4.404\text{ V}$$

> **How to solve any version of this problem type:**
> 1. Find $\tau = RC$ first — always.
> 2. To find **time to reach some voltage**: solve $V_C = V_S(1-e^{-t/RC})$ for $t$ using natural log.
> 3. To find **voltage at some time**: just plug $t$ straight into the formula.

---

## 🔑 Quick Recall Table (skim this the night before the exam)

| Concept | One-line answer |
|---|---|
| Why multiplexed pins? | 59 signals needed, only 40 physical pins |
| ALE | Tells external latch: "grab the address now" |
| BHE̅ | Enables upper byte (D8-D15) of the data bus |
| A0 | 0 = even address, 1 = odd address |
| Min Mode | Single CPU, CPU generates control signals itself, `MN/MX̅=1` |
| Max Mode | Multi-processor, 8288 generates control signals, `MN/MX̅=0` |
| Latch vs Buffer | Buffer = pass-through only; Latch = remembers/holds value |
| Tri-state output | 0, 1, or Z (disconnected) |
| RESET | High for ≥4 clock cycles → boot from `FFFF0H`, IF cleared, regs=0 |
| READY = 0 | CPU inserts Wait States (Tw), stays idle |
| Bus cycle | 4 states minimum: T1 (address) → T2 (turn on RD/WR) → T3 → T4 (data moves) |
| RC time constant | $\tau = RC$; 63% charged at 1τ, ~98% at 4τ |
| 8086 vs 8088 | 16-bit vs 8-bit data bus; pin 34 (BHE̅/S7 vs SS0), pin 28 (M/IO̅ vs IO/M̅) |

---

## 📝 Practice Questions straight from the slides

1. Explain the purpose of the `BHE̅`, `ALE`, and `A0` pins on the 8086 microprocessor.
2. Explain three major functionalities of the 8284 Clock generator circuit.
3. Draw the reset (R-C) circuit and explain the activities of the manual reset pin.
4. Explain the procedure to generate a wait state.

*(Try answering these from memory using the sections above before checking back — that's the fastest way to actually lock this in.)*
