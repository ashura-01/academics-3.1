---
tags: [microprocessor, 8086, hardware, CO, exam-prep]
source: Lecture5.pdf — Prof. Dr. Shamim Akhter
---

# 🧠 8086 Hardware Structure — Every Figure, Fully Explained

> ## 📌 SYLLABUS BOUNDARY
> Your exam currently covers **only up to "Maximum Mode"** of this lecture.
> - ✅ **IN SYLLABUS:** Sections 1–6 (pin multiplexing → pin-out → pin connections → odd/even banks → Minimum Mode → Latches/Buffers → Maximum Mode). Go deep here.
> - ⏳ **NOT YET IN SYLLABUS:** Sections 7 onward (8288 controller detail, 8284 clock generator, RESET, wait states, bus cycles, RC math). Still explained fully below, but you don't need it for the current exam — banner repeated before that section.

---

## 1. The core problem: 59 signals, 40 pins

The 8086 internally needs to move around:
- a **20-bit address** (so it can address 2²⁰ = 1,048,576 locations = 1 MB of memory),
- a **16-bit data word**,
- roughly **20 bits worth of control/status signals** (read, write, interrupt handling, mode selection, etc.),
- plus power and ground.

Add that up and you get about **59 separate signals**. But Intel packaged the chip in a standard **40-pin DIP (Dual In-line Package)** — physically, there just aren't 59 holes to put wires into.

**The fix: multiplexing.** Instead of giving every signal its own permanent pin, some pins are made to carry *different information at different moments in time*. The lower 16 pins (`AD0–AD15`) are the clearest example: for the first part of every bus cycle they carry the **address**, and immediately after, the very same physical wires carry the **data**. A separate signal, `ALE` (Address Latch Enable), acts like a timestamp — it tells any external circuit "the value on these pins RIGHT NOW is an address, not data — go grab it and remember it, because it's about to change."

![[minmax_intro.png]]

**What this slide is showing you:** it lists the 59-bit total budget (20 address + 16 data + 20 control/status + 3 power = 59) versus the 40 physical pins available, and gives you the one-word answer written in blue: **Multiplexing**. This single picture is the "why" behind almost every weird-looking dual-purpose pin name you'll see for the rest of the lecture (`AD`, `A19/S6`, `BHE̅/S7`, etc.) — the slash in the pin name literally means "this pin means the left side of the slash sometimes, and the right side of the slash at other times."

---

## 2. The Pin-Out diagram — reading it pin by pin

![[pinout.png]]

This figure shows the 8086 (left, purple) and the 8088 (right, teal) side by side, both as 40-pin DIP packages, pins numbered 1–40 going around the outside (1 to 20 down the left side, 21 to 40 up the right side — that's the standard way DIP pin numbering works, starting bottom-left, going down and around).

Let's walk down the 8086's pin list exactly as drawn:

- **Pin 1 & Pin 20 — GND**: two separate ground pins. Having two grounds (instead of one) reduces electrical noise and voltage drop across the chip.
- **Pins 2–9 — `AD14` down to `AD7`**: these are the multiplexed Address/Data lines. Notice how the numbering goes AD14, AD13, AD12... down to AD7 as you move down the package — pin 2 is the *second-highest* data bit shown on this side.
- **Pin 10–16 — `AD6` to `AD0`**: continuing the same multiplexed Address/Data lines down to bit 0.
- **Pin 17 — `NMI`**: Non-Maskable Interrupt input.
- **Pin 18 — `INTR`**: (Maskable) Interrupt Request input.
- **Pin 19 — `CLK`**: clock input — the chip's heartbeat.
- Now flipping to the right-hand column, read from pin 40 down to pin 21:
- **Pin 40 — `VCC`**: power supply (+5V).
- **Pin 39 — `AD15`**: the last (highest) Address/Data bit — together with pins 2–16 this completes the full 16-bit multiplexed AD0–AD15 bus.
- **Pin 38 — `A16/S3`**, **Pin 37 — `A17/S4`**, **Pin 36 — `A18/S5`**, **Pin 35 — `A19/S6`**: these four pins are also multiplexed, but differently — during the address phase they're plain upper address bits (A16–A19), and during the rest of the cycle they output status bits (S3–S6), which tell you things like which memory segment is being accessed.
- **Pin 34 — `BHE̅/S7`**: Bus High Enable during the address phase, and a status bit (always logic 1) afterward. **This is one of only two pins that physically differ between the 8086 and 8088** — on the 8088 this same physical pin is called `SS0` instead.
- **Pin 33 — `MN/MX̅`**: the mode-select pin — tie it high for Minimum Mode, low for Maximum Mode.
- **Pin 32 — `RD̅`**: Read strobe.
- **Pin 31 — `HOLD`**, **Pin 30 — `HLDA`**: the DMA request/acknowledge pair.
- **Pin 29 — `WR̅`**: Write strobe.
- **Pin 28 — `M/IO̅`**: tells you if the current access is to Memory or an I/O port. **This is the second pin that differs between 8086 and 8088** — the 8088 calls this same pin `IO/M̅` (the logic sense is simply flipped compared to the 8086's naming).
- **Pin 27 — `DT/R̅`**: Data Transmit/Receive direction control.
- **Pin 26 — `DEN̅`**: Data bus Enable.
- **Pin 25 — `ALE`**: Address Latch Enable — the signal explained above.
- **Pin 24 — `INTA̅`**: Interrupt Acknowledge.
- **Pin 23 — `TEST̅`**: input tested by the WAIT instruction.
- **Pin 22 — `READY`**: tells the CPU whether the addressed device is ready or needs the CPU to wait.
- **Pin 21 — `RESET`**: reset input.

**What to actually remember from this figure for the exam:** you don't need to recite every pin number. What you *do* need is: (1) it's a 40-pin DIP, (2) 8086 = 16-bit data bus (AD0–AD15), 8088 = 8-bit data bus (AD0–AD7 only, with A8–A15 as separate non-multiplexed address-only pins), and (3) the two physical differences: pin 34 (`BHE̅/S7` vs `SS0`) and pin 28 (`M/IO̅` vs `IO/M̅`).

---

## 3. The Pin Connections table — what each multiplexed pin means, in detail

![[pin_connections_table.png]]

This table is explaining, row by row, *exactly what each multiplexed pin group means depending on the state of ALE (or in the last row, S3/S4)*.

**Row 1 — `AD7–AD0` (both 8086 and 8088):** "Lines are multiplexed and represent the rightmost 8-bit memory address or I/O port number whenever ALE is active (1), or data whenever ALE is inactive (0)." In other words: watch ALE. The instant ALE pulses high, whatever voltage pattern is sitting on AD7–AD0 *is* the low byte of the address — capture it right then. As soon as ALE drops back to 0, those same 8 wires are now carrying an actual data byte being read or written.

**Row 2 — `AD15–AD8` (8086 only):** Same multiplexing trick, but this is the *upper* data byte / upper-middle address bits. When ALE=1 these lines represent address bits A15–A8; when ALE=0 they represent data bits D15–D8. (The 8088 doesn't have this row because its data bus is only 8 bits wide — it uses separate dedicated `A15–A8` pins instead, shown in the next row.)

**Row 3 — `A15–A8` (8088 only):** On the 8088, these are *not* multiplexed at all — they permanently just carry the upper half of the memory address. This is precisely why the 8088 needs fewer AD-multiplexed pins (only AD0–AD7) — it sacrifices data bus width to simplify addressing.

**Row 4 — `A19/S6 – A16/S3`:** This is the most subtle multiplexed group. During the address phase these 4 lines carry the top 4 bits of the 20-bit address (A19 down to A16). During the rest of the bus cycle, the *same 4 physical pins* switch to carrying status information:
- **S6** is always logic 0 (it's essentially a placeholder/reserved bit).
- **S5** reflects the current state of the IF (Interrupt Flag) inside the CPU — so external hardware can literally "see" whether interrupts are currently enabled or disabled.
- **S3 and S4 together** tell you which of the four internal memory segments (Code, Data, Stack, Extra) is being accessed during this particular bus cycle, according to this table shown in the figure:

| S4 | S3 | Function |
|---|---|---|
| 0 | 0 | Extra Segment |
| 0 | 1 | Stack Segment |
| 1 | 0 | Code or No segment |
| 1 | 1 | Data Segment |

Think about *why* this is useful: a memory or interrupt controller watching the bus can tell, just from S3/S4, whether the CPU is currently fetching an instruction (Code segment) or reading/writing ordinary data (Data segment) or touching the stack — without needing to know anything about the actual address value.

### The other single-purpose (non-multiplexed) control pins

![[rd_ready_intr_test.png]]

This second table walks through pins that are **not** multiplexed — each one always means exactly one thing:

- **`RD̅` (Read):** "Represents the Read Signal and activated at logic 0." Meaning: when this pin is pulled low (0), the CPU is telling the outside world "I am reading data right now." It stays high (inactive) the rest of the time. The little bar over RD (RD̅) is the standard notation for "active-low" — active when the signal is 0, not 1.

- **`READY`:** This is an *input* to the CPU, driven by whatever memory or I/O device it's talking to. If READY = 1, nothing special happens — the device responded fast enough, business as usual. If READY = 0, the device is saying "I need more time," and the microprocessor freezes in place (enters extra "wait states") until READY goes back to 1.

- **`INTR` (Interrupt Request):** When this pin is pulled to 1 *and* the internal Interrupt Flag (IF) is also 1 (meaning interrupts are currently allowed), the CPU begins preparing to service the interrupt. It doesn't jump immediately — it finishes whatever instruction is currently executing first, and only then does `INTA̅` (Interrupt Acknowledge) become active as the CPU's reply.

- **`TEST̅`:** An input pin that's specifically checked by the `WAIT` machine instruction. If TEST̅ = 1, the `WAIT` instruction just keeps looping, doing nothing, until TEST̅ eventually becomes 0. This pin is most commonly wired up to the 8087 math co-processor, so the main CPU can literally "wait" for the co-processor to finish a calculation.

- **`NMI` (Non-Maskable Interrupt):** Behaves almost exactly like INTR, with one crucial difference highlighted in red on the slide: NMI **does not check the IF flag at all**. Even if the programmer has disabled interrupts, NMI still gets through. It's reserved for genuinely critical events (like an imminent power failure) that the system must never be allowed to ignore.

- **`RESET`:** If this pin is held at logic 1 for at least 4 full clock periods, the microprocessor resets itself completely — it starts fetching its very next instruction from address `FFFF0H`, and it clears the IF flag (so interrupts start out disabled after a reset).

- **`CLK`:** Supplies the basic timing heartbeat to the whole chip. It's not a simple 50/50 square wave — the slide specifically notes it needs a **33% duty cycle**: high for 1/3 of each period and low for the other 2/3. Every internal operation of the CPU is paced against this clock.

- **`VCC`:** the +5.0V power supply pin.

- **`GND` (×2):** the 0V ground pins (there are two of them on the chip, as we saw in the pinout diagram).

- **`MN/MX̅`:** Selects Minimum vs Maximum mode. Tying this pin to +5V selects Minimum Mode.

- **`BHE̅/S7`:** "Bus High Enable. Enables the most significant data bus bits (D15–D8) during a read or write operation. S7 is always logic 1" — meaning during the status phase, this same pin outputs a constant 1 (unlike S3–S6 which actually vary and carry information, S7 is just a fixed placeholder bit).

---

## 4. Odd and Even Address Banks — explained slowly, figure by figure

This is genuinely the trickiest concept in the whole lecture, so let's go extra slow.

### Why split memory into two banks at all?

The 8086's data bus is 16 bits wide — it *wants* to move 2 bytes at a time whenever possible, because that's twice as fast as moving 1 byte at a time. But ordinary memory chips are usually built to output 8 bits per access. Intel's solution: use **two separate memory chips side-by-side**, each handling half of the address space, wired so the CPU can talk to either one alone, or both simultaneously.

![[even_odd_bank.png]]

**Reading this figure directly:**
- On the right is the **Even Address Bank** — 512K × 8 bits — connected to the **lower** data lines `D0–D7`.
- On the left is the **Higher/Odd Address Bank** — 512K × 8 bits — connected to the **upper** data lines `D8–D15`.
- Both banks share the same address lines `A1–A19` (notice it starts from A1, not A0 — more on that below).
- The Odd bank additionally has a control input labeled `BHE̅` at the top, with the note "0 = Bus high enable, 1 = Bus high disable" — meaning pulling BHE̅ to 0 is what actually switches the Odd bank ON.
- The Even bank has a control input labeled `A0` at the top, with the note "0 = Even address, 1 = Odd address" — this is the CPU's least-significant address bit, and it doubles as the Even bank's own enable signal.
- At the bottom, both banks feed into the shared 16-bit Data Bus (`D0–D15`), captioned "2 Bytes Data" — reinforcing that when both banks are active together, you get a full 16-bit (2-byte) transfer in a single access.

**Why does addressing start from A1, not A0?** Because A0 isn't being used as a normal address bit here at all — it's been repurposed purely as the Even/Odd bank *selector*. Since each bank is a separate 512 KB chip, you only need 19 address bits (2¹⁹ = 512K) to address a location *within* one bank — that's exactly `A19...A1`, which is what feeds each bank in the diagram.

### The address table — seeing the pattern in raw binary

![[access_16bit_odd.png]]

(This is actually reused visually from the earlier "Accessing 16-bit Odd Address" slide, but conceptually it follows the address table shown right after the banks diagram in your source, which listed addresses 0 through 4 in binary alongside their hex values 8, 9, A, B, C.)

**The pattern to notice:** address `0` → full binary `0000 0000 0000 0000 000`**`0`**, so `A0=0` → goes to the **Even bank**. Address `1` → full binary `0000 0000 0000 0000 000`**`1`**, so `A0=1` → goes to the **Odd bank**. Address `2` → `0000 0000 0000 0000 001`**`0`**, `A0=0` → **Even bank** again. Address `3` → `...001`**`1`**, `A0=1` → **Odd bank**. This strict alternation (Even, Odd, Even, Odd...) is exactly why the two physical chips are literally called the Even Bank and the Odd Bank — every consecutive byte address alternates which physical chip it actually lives in.

But here's the part the original explanation got wrong: **it's not that each address gets its own new row.** Look again at addresses 0 and 1 — their binary is identical except for that last bolded bit. That means `A19...A1` (everything left of the bold digit) is exactly the same for both — so both banks are pointed at the **same row number** simultaneously. `A0` doesn't pick a row; it picks **which of the two banks is allowed to answer**. Same story for addresses 2 and 3 — identical upper bits, different only in `A0`, landing on the _next_ row (row 1) in both banks.

| Address | Full 20-bit binary               | Row inside its bank | Bank |
| ------- | -------------------------------- | ------------------- | ---- |
| 0       | `0000 0000 0000 0000 000`**`0`** | row 0               | Even |
| 1       | `0000 0000 0000 0000 000`**`1`** | row 0               | Odd  |
| 2       | `0000 0000 0000 0000 001`**`0`** | row 1               | Even |
| 3       | `0000 0000 0000 0000 001`**`1`** | row 1               | Odd  |
| 4       | `0000 0000 0000 0000 010`**`0`** | row 2               | Even |

### Now the four access patterns, one at a time

**Pattern 1 — Accessing an EVEN address only (8-bit read/write)**

![[access_even.png]]

The example code shown is:

```elm
MOV SI, 4000H
MOV AL, [SI+2]
```

This loads register `SI` with `4000H`, then reads a single byte from address `4002H` into `AL`. Since `4002H` is an even address, `A0 = 0`. Looking at the diagram: the arrow into the **Even Bank** is active (data flows on `D0–D7`), while `BHE̅ = 1` (disabled), so the **Odd Bank stays completely silent** — it doesn't drive the bus at all. Only 8 bits move, and they move on the *lower* half of the data bus.

**Pattern 2 — Accessing an ODD address only (8-bit read/write)**

![[access_odd.png]]

Example code:
```
MOV SI, 4000H
MOV AL, [SI+3]
```
Now we're reading from `4003H`, an odd address, so `A0 = 1`. This time the **Odd Bank** is the one that activates (`BHE̅ = 0`), and it places its byte on the *upper* half of the data bus
(`D8–D15`) — even though we're only moving one byte, notice it appears on the upper wires, not the lower ones, simply because that's which physical chip it lives in. The Even bank is silent this time (`A0=1` disables it).

**Pattern 3 — Accessing 16-bit data starting at an EVEN address (the "fast path")**

![[access_16bit_even.png]]

Example code:
```elm
MOV SI, 4000H
MOV AX, [SI+2]
```
This reads a full 16-bit word starting at `4002H` into register `AX`. Because the starting address is even, the low byte of the word (at `4002H`) sits in the Even bank, and the high byte (at `4003H`) sits right next door in the Odd bank. **Both banks activate together, at the same time, in a single bus cycle** — `A0=0` enables the Even bank and `BHE̅=0` enables the Odd bank simultaneously. The full 16 bits move across `D0–D15` in one shot. This is the ideal, fastest case.

**Pattern 4 — Accessing 16-bit data starting at an ODD address (the "slow path" / gotcha case)**

![[access_16bit_odd.png]]

Example code:
```
MOV SI, 4000H
MOV AX, [SI+5]
```
This wants a 16-bit word starting at address `4005H` — an *odd* address. Here's the problem: the low byte of this word lives at `4005H` (Odd bank) but the high byte lives at `4006H` (Even bank) — they're split across *both* physical chips in a way that a single access can't cleanly capture, because the "aligned" wiring assumes a word starts on an even boundary. So the CPU is forced to do **two separate bus cycles**:

- **Cycle ① — First Access, from the Odd bank:** `BHE̅=0, A0=1`. The CPU grabs the byte at `4005H` from the Odd bank.
- **Cycle ② — Next Access, from the Even bank:** `BHE̅=1, A0=0`. The CPU then grabs the byte at `4006H` from the Even bank.

These two 8-bit accesses are then stitched together internally into the final 16-bit value in `AX`. Because this takes **two bus cycles instead of one**, it is measurably slower than an aligned (even-start) 16-bit access — this is the single most commonly asked descriptive question from this whole section.

---

## 5. Minimum Mode — explained pin by pin, then the whole circuit

![[min_mode_pins_table.png]]

This table lists the pins that only mean something specific in Minimum Mode (in Maximum Mode, some of these same physical pins carry different signals instead — that's the whole "mode" concept: the *function* of certain pins literally changes based on how you wire `MN/MX̅`).

- **`IO/M̅` (8086) or `M/IO̅` (8088):** tells external circuitry whether the current address on the bus is a memory address or an I/O port address.
- **`WR̅`:** "a strobe that indicates the processor is outputting data to a memory or I/O device. The pin activates at logic 0." The slide adds an important general definition here: *"A strobe line is employed to signal the receiving circuit when the input data is valid."* In other words, a strobe isn't carrying data itself — it's a timing signal that says "the data on the other wires is good right now, go ahead and use it."
- **`INTA̅`:** the CPU's reply to an `INTR` request. Interrupt-driven devices use this signal as their cue to place their interrupt vector number onto the data bus, which the microprocessor then reads to figure out exactly which Interrupt Service Routine (ISR) to jump to.
- **`ALE`:** "Address Latch Enable. When 1, the address/data bus contains a memory or I/O address." (Same signal explained back in Section 1 — this table is just re-confirming its role specifically within Minimum Mode.)
- **`DT/R̅` (Data Transmit/Receive):** tells the external transceiver chip which direction data should currently flow — is the CPU transmitting (writing) or receiving (reading)?
- **`DEN̅` (Data bus Enable):** turns on the external data buffer chips so they actually start passing signals through — without DEN̅ active, the buffer sits in its high-impedance "disconnected" state.
- **`HOLD` (input pin):** used by a DMA (Direct Memory Access) controller to request temporary control of the bus. When `HOLD = 1`, the CPU finishes its current bus activity, then stops executing, and places its address bus, data bus, and control bus all into the **high-impedance state**. The slide explains high-impedance carefully: *"a state when the output is not driven by the input(s), that means output is neither high (1) nor low (0). The output is electrically disconnected from the circuit."* Think of it like the CPU physically lifting its hands off the bus wires — it's not pulling them high or low, it's just... letting go. This lets the DMA controller safely drive the same wires without any electrical conflict. When `HOLD = 0`, the CPU takes the bus back and resumes normal execution.

**`HLDA` (Hold Acknowledge) — output pin:** the CPU's reply to HOLD, confirming "yes, I have released the bus, you may proceed" — this is how the requesting device (like a DMA controller) knows it's actually safe to start driving the bus itself.

**`SS0̅` (8088 only):** works together with `IO/M̅` and `DT/R̅` to fully decode exactly what kind of bus cycle is currently happening — see the table below.

### The 8088's SS0 decode table

![[ss0_table.png]]

Since the 8088 doesn't have the 3-bit S2/S1/S0 status group that Maximum Mode uses (that's a Maximum Mode thing, explained in Section 6), it instead combines `IO/M̅`, `DT/R̅`, and `SS0̅` into this small 3-bit code to tell you the exact bus cycle type:

| IO/M̅ | DT/R̅ | SS0̅ | Function |
|---|---|---|---|
| 0 | 0 | 0 | Interrupt Acknowledge |
| 0 | 0 | 1 | Memory Read |
| 0 | 1 | 0 | Memory Write |
| 0 | 1 | 1 | Halt |
| 1 | 0 | 0 | Opcode Fetch |
| 1 | 0 | 1 | I/O Read |
| 1 | 1 | 0 | I/O Write |
| 1 | 1 | 1 | Passive |

The slide adds a helpful side-note on the **Halt** row specifically: *"In the x86 computer architecture, HLT (halt) is an instruction that halts the central processing unit (CPU) until the next external interrupt is fired."* So when the CPU executes a HLT instruction, this particular 3-bit code (011) is what gets output onto the bus so external hardware can tell the CPU has stopped and is just waiting for something to wake it up.

### The full Minimum Mode circuit diagram

![[min_mode_diagram.png]]

Reading this circuit left to right:
- On the far left, a **Clock generator** (this is the 8284A chip, covered properly in Section 8 once your syllabus reaches it) feeds `CLK`, `READY`, and `RESET` into the 8086 CPU block in the middle. It's also fed by a small **Wait-State Generator** block and an RC "RES̅" reset network.
- The `MN/MX̅` pin is explicitly tied to **+5V**, which is what forces the chip into Minimum Mode — that's the single wire that decides everything about this diagram.
- Below that, `ALE` comes out of the CPU and feeds an **8282 Latch** chip. Notice its inputs are labeled `STB` (strobe — this is where ALE actually connects) and `OE` (output enable, tied to ground here meaning it's permanently enabled). The latch's output on the right becomes the clean, stable, de-multiplexed 20-bit **Address Bus** (`A0–A19`).
- The CPU's `AD0–AD15` and `A16–A19` pins feed directly into this latch as its data input.
- `BHE̅` comes straight out of the CPU and goes straight out to the system, unlatched — it doesn't need latching in this simplified diagram because it's already stable enough during the time it matters.
- Below the latch, an **8286 Transceiver** chip handles the actual data — its `T` (direction/transmit) and `OE̅` (output enable) control inputs are driven by the CPU's `DT/R̅` and `DEN̅` pins respectively. This transceiver's output becomes the clean 16-bit **Data Bus** (`D0–D15`) that the rest of the system uses.
- The note in blue text on the left of the diagram summarizes the whole point: *"Only one processor. MN/MX̅ = Logical 1. Simple Circuit. Performance is lower."*

**The plain-English story of this diagram:** the raw CPU chip alone can't drive a clean system bus, because its own pins are multiplexed and momentary. So you *always* need a latch (to freeze the address) and a transceiver (to safely pass data both directions) sitting right outside the CPU — this diagram is literally showing you the minimum extra hardware required to turn the 8086's confusing multiplexed pins into a normal, stable, usable address bus and data bus.

---

## 6. Latches, Buffers, and Transceivers — the helper chips, explained circuit by circuit

We already met the 8282 latch and 8286 transceiver inside the Minimum Mode diagram above — now let's actually understand *how* those chips work internally.

![[bus_latch_buffer.png]]

This slide gives you the core vocabulary distinction, and then shows you 5 real chip symbols side by side:

- **Buffers** pass an input through to the output after some propagation delay — and, importantly, they can also *increase drive strength*, meaning the output can supply more current than the original input signal could, which matters because a bus might have several chips all listening to it and each one draws a little current.
- **Latches** do everything a buffer does, but *additionally add memory* — they capture and hold ("persist") the input value at a specific point in time. The exact moment of capture is triggered by a third, separate control signal.
- The slide is explicit that a latch is **not RAM**: *"It differs from a register in that the storage takes place while a control input is at a particular level (0 or 1), while a register stores the input data upon receipt of an edge (rising or falling)."* This is a genuinely important distinction: a **latch** is *level-triggered* (it stays "open" and transparent the whole time its enable pin is held high, for example), while a **register** (a different kind of flip-flop) is *edge-triggered* (it only grabs a new value at the exact instant the clock transitions from low-to-high or high-to-low, and ignores everything else).

The five chip symbols shown, left to right:
1. **74LS373 — Octal Latch:** 8 D-inputs, 8 Q-outputs, an `OE̅` (output enable) pin and a `G` (gate/enable) pin.
2. **74LS244 — Octal 3-State Buffer:** simple IN→OUT pass-through buffer with an `OE̅` pin, no memory.
3. **74LS245 — Bus Transceiver:** bidirectional, A-side and B-side, with a `G`/`Dir` control to pick direction.
4. **8282 — Tri-state Octal Latch:** the specific chip used in the Minimum/Maximum Mode diagrams, functionally similar to the 74LS373 but with `STB` (strobe) instead of `G`.
5. **8286 — Tri-state Octal Bus Transceiver:** the specific chip used for data, functionally similar to the 74LS245 but with a `T` (direction) pin instead of `Dir`.

### Three-state (tri-state) buffer — the "switch" idea

![[tristate_buffer.png]]

This is the simplest of the bunch: a triangle symbol with an `In`, an `Out`, and a `Control` line underneath. The three possible output states are labeled right in the title: **0, 1, Z**.
- When the `Control` line **enables** the buffer, the output simply follows the input, faithfully, just like a wire (with a small delay).
- When `Control` **disables** the buffer, the output goes to **Z (high impedance)** — it's not driven high, not driven low, it's just *disconnected*, as if someone unplugged that wire entirely. This means it can no longer interfere with (or "load down") whatever else might be connected to that same output line.
- The slide's summary line: *"In effect, it is like a switch."* — that's really the whole concept in one sentence: a tri-state buffer is an electronic on/off switch for a signal line.

### Bidirectional buffers (transceivers) — how data flows both ways safely

![[transceiver.png]]

This is a logic-gate-level diagram of something like the 74LS245. There are two triangular buffer symbols (one pointing each direction — B→A across the top, A→B implied on the return path) whose enables are controlled by a combination of two AND gates fed by two external control lines: **Direction** and **Enable**.

The truth table given:

| DIR | Action |
|---|---|
| 0 | B → A |
| 1 | A → B |

So this single chip can pass data left-to-right or right-to-left depending on just one control bit, and the separate `Enable` line can shut the whole thing off entirely (both directions disabled, high-impedance) when the bus needs to be shared with someone else. This is exactly the behavior the 8086's `DT/R̅` pin controls when driving an 8286 in the Minimum Mode diagram — `DT/R̅` is effectively that `DIR` input.

### Latches (D-type flip-flops) — how the address actually gets "frozen"

![[latches.png]]

This diagram shows the internal NAND-gate construction of a transparent D-latch (like inside a 74LS373). It has two inputs, `D` (the data you want to store) and `G` (the enable/gate), and produces `Q` and its complement `Q̅`.

**Behavior, explained slowly:**
- While `G` (enable) is **HIGH**, the latch is "transparent" — whatever value is on `D` immediately flows through to `Q`. If `D` changes, `Q` changes right along with it, essentially instantly.
- The moment `G` goes **LOW**, the latch "closes" — `Q` freezes at whatever value `D` happened to be at that exact instant, and it *stays* at that frozen value no matter what `D` does afterward, right up until `G` goes high again.

The slide gives two real chip examples with a subtle but important difference:
- **74LS373** — latched on the **falling edge** (i.e., transparent while high, freezes when G drops to low)
- **74LS374** — latched on the **rising edge** (this one is actually a true edge-triggered flip-flop rather than a level-triggered latch, included here for comparison)

**Connecting this back to the CPU:** In the Minimum Mode diagram, `ALE` connects to this latch's `G`/`STB` input. `ALE` pulses briefly HIGH right at the start of every bus cycle — during that brief high pulse, the address flowing out of the CPU's multiplexed `AD` pins passes straight through the latch to the system's real address bus. The instant `ALE` drops back to LOW, the latch freezes and holds that address steady on the output — even though the CPU's own `AD` pins are, at that very moment, already switching over to carry data instead. Without this latch, the external address bus would glitch and become garbage the moment the CPU's pins switched purpose.

---

# ⏳ NOT YET IN SYLLABUS — Fully Explained, But For Later

> Reminder: everything below covers material **after** Maximum Mode. Read it whenever your teacher actually gets here — it's fully explained now so you don't have to come back and ask again later, but it is **not** required for your current exam.

---

## 7. Maximum Mode — pin by pin, then the 8288 Bus Controller

![[max_mode_pins_table.png]]

- **`S2̅, S1̅, S0̅` (Status pins):** a 3-bit code, output at the start of every bus cycle, that tells the external 8288 Bus Controller exactly what kind of cycle is about to happen. Decoded in the table below.
- **`RQ/GT0̅` and `RQ/GT1̅` (Request/Grant):** bidirectional lines that let another bus master (like a co-processor) both *request* the bus and receive a *grant* to use it, all over the same single wire, using a specific pulse protocol.
- **`LOCK̅`:** signals that no other bus master is currently allowed to take over the system bus, because the CPU is in the middle of an operation that must not be interrupted — for example a Bit-Test-and-Set (BTS) instruction doing a Read-Modify-Write on a single memory location, where letting another processor sneak in between the read and the write could corrupt the result.
- **`QS1` and `QS0` (Queue Status):** report the status of the CPU's internal instruction prefetch queue, mainly so the 8087 numeric co-processor can track which bytes the main CPU has already consumed:

| QS1 | QS0 | Function |
|---|---|---|
| 0 | 0 | Queue is idle / No operation |
| 0 | 1 | First byte of opcode |
| 1 | 0 | Queue is empty |
| 1 | 1 | Subsequent byte of opcode |

### The S2 S1 S0 status code, fully decoded, plus the timing diagram

![[s2s1s0_timing.png]]

The table on this slide gives the full 8-way decode:

| S2̅ | S1̅ | S0̅ | Function |
|---|---|---|---|
| 0 | 0 | 0 | Interrupt Ack |
| 0 | 0 | 1 | I/O Read |
| 0 | 1 | 0 | I/O Write |
| 0 | 1 | 1 | Halt |
| 1 | 0 | 0 | Opcode Fetch |
| 1 | 0 | 1 | Memory Read |
| 1 | 1 | 0 | Memory Write |
| 1 | 1 | 1 | Passive |

Below that, the timing diagram shows two full bus cycles back to back — a **Memory read cycle** followed by a **Memory write cycle** — each broken into T1, T2, T3, (possibly Tw), T4. The explanatory text tells you precisely when S2/S1/S0 are valid: *"These pins are active during T4, T1, and T2 states and are returned to the End of the BUS Cycle or passive state (1,1,1 during T3 or Tw when ready is inactive). These are used by the 8288 bus controller for generating all the memory and I/O access control signals. Any change in S2, S1, S0 during T4 indicates the beginning of a bus cycle."* In plain terms: the CPU announces the *type* of cycle early (during T4 of the previous cycle and T1/T2 of the new one), giving the 8288 controller enough lead time to generate the right control signal before data actually needs to move.

### The full Maximum Mode circuit diagram

![[max_mode_diagram.png]]

This looks a lot like the Minimum Mode diagram, but with one major addition: sitting between the CPU and the Latch/Transceiver pair, there's now an **8288 Bus Controller** chip. The CPU feeds it `S0̅, S1̅, S2̅` and `CLK`; the 8288 in turn outputs the actual memory/IO command signals (`MRDC̅, MWTC̅, AMWC̅, IORC̅, IOWC̅, AIOWC̅, INTA̅`) plus `DEN̅`, `DT/R̅`, and `ALE` — all of which the CPU used to generate *itself* back in Minimum Mode. The blue side-note: *"Multiple co-processors — 8087 (numeric calculation), 8089 (I/O coprocessor). MN/MX̅ is logical 0. Circuit is more complex. Performance is very high."*

### The 8288 Bus Controller, internally

![[8288_bus_controller.png]]

Internally the 8288 is drawn as four blocks:
- **Status Decoder** (top-left) reads `S0̅, S1̅, S2̅` in.
- Feeding into the **Command Signal Generator** (top-right), which outputs the "BUS Command Signals" group: `MRDC̅, MWTC̅, AMWC̅, IORC̅, IOWC̅, AIOWC̅, INTA̅`. The slide notes this whole group is "Enable by CEN."
- **Control Logic** (bottom-left) reads `CLK, AEN̅, CEN, IOB` in.
- Feeding into the **Control Signal Generator** (bottom-right), which outputs the "Address Latch, Data Transceiver, and Interrupt Control Signal" group: `DT/R̅, DEN̅, MCE/PDEN̅, ALE`.

![[8288_pin_functions.png]]

- **`AEN̅`:** Address Enable input — when active, causes the 8288 to enable the memory control signal outputs.
- **`CEN`:** Control Enable input — enables the command output pins on the 8288.
- **`IOB`:** selects between I/O-bus mode or system-bus mode of operation.
- **`MCE/PDEN̅`:** the master cascade / peripheral data enable output — selects cascade operation for an interrupt controller if `IOB` is grounded, or enables the I/O bus transceivers if `IOB` is tied high.
- **`AMWC̅` / `AIOWC̅`** (Advanced Memory Write / Advanced I/O Write Control): these two output signals are deliberately enabled **one clock cycle earlier** than the normal write commands (`MWTC̅`/`IOWC̅`). The reason: some slower memory and I/O devices need this wider pulse width — the extra head-start gives them more total time to actually latch the data being written.

---

## 8. The 8284A Clock Generator — full internal walkthrough

![[8284_clock_generator.png]]

This single chip does **three jobs at once**: generating the Clock, shaping the Reset signal, and synchronizing the Ready signal. Let's trace each path through the diagram.

**Clock path:** An external crystal (`X1`/`X2`, e.g. 15 MHz) or an external frequency input (`EFI`) feeds the **XTAL OSC** (crystal oscillator) block, which produces a clean square wave. A **2-to-1 mux**, controlled by the `F/C̅` pin, picks between the crystal-derived signal and the raw external `EFI` signal. That selected signal then passes through a **divide-by-3 counter**, producing `PCLK` (Peripheral Clock, e.g. 2.5 MHz) — this is a lower-speed clock made available for peripheral chips that don't need to run as fast as the CPU. The same divide-by-3 output *also* feeds a **divide-by-2 counter**, producing the final `CLK` signal (e.g. 5 MHz) that actually drives the 8086 CPU itself. (So overall CLK = crystal frequency ÷ 6, and PCLK = crystal frequency ÷ 3 — the diagram's side-note literally says "Peripheral Clock -1/6 of crystal/EFI freq" for CLK's relationship, and PCLK sits at double that frequency.)

**Reset path:** An external `RES̅` pin (active-low reset button/circuit) feeds through a **Schmitt trigger** (explained fully below) into a **negative-edge-triggered D flip-flop**, whose output becomes the clean digital `RESET` signal sent to the CPU. The note: *"MP reset at positive edge (0-1)"* — meaning the flip-flop is specifically arranged so the final RESET output transitions cleanly on a rising edge, giving the microprocessor a crisp, glitch-free reset pulse instead of the slow, noisy ramp coming directly off the RC circuit.

**Ready path (bottom half of the diagram):** Two independent READY inputs, `RDY1`/`AEN1` and `RDY2`/`AEN2`, are combined through AND/OR gates and a "1st Stage Synchronization" D flip-flop, then further conditioned by the `ASYNC̅` pin and a "Negative edge FF (1-0)" second flip-flop, before producing the final, clean `READY` output sent to the CPU. This two-stage synchronization exists because RDY1/RDY2 are asynchronous signals coming from potentially slow external devices — synchronizing them properly through two flip-flop stages avoids a phenomenon called "metastability," where a flip-flop can otherwise get stuck in an undefined state if its input changes at the exact wrong moment relative to the clock.

**CSYNC pin:** used specifically when the `EFI` input is providing synchronization across a system with *multiple* processors sharing one clock source. The note explains: *"CSYNC HIGH will reset the internal counters, when CSYNC goes LOW the counters will resume counting."* If you're using the internal crystal oscillator instead (a single-processor system), this pin should simply be grounded (tied permanently low).

---

## 9. RESET Operation — the full sequence and circuit

![[reset_operation.png]]

**The rule, stated plainly:** *"The reset input causes the microprocessor to reset itself if this pin is held high for at least four clocking periods."*

**What actually happens the instant the 8086 (or 8088) is reset:**
1. It begins executing instructions starting at memory location **`FFFF0H`** (this is deliberately near the very top of the 1 MB address space — real systems place their boot-up ROM there specifically so this hardwired reset address makes sense).
2. It **disables future interrupts** by clearing the IF flag bit.
3. **All registers become 0.**
4. **PC and SP** (program counter and stack pointer) are set to their initial origin address.

**The reset circuit itself:**

![[reset_figure94.png]]

A 15 MHz crystal feeds `X1/X2` on the 8284A, which outputs the `CLK` (5 MHz here) that drives the 8086/8088. Separately, a resistor-capacitor network — labeled `10K` resistor, a diode, and a `10µF` capacitor — feeds into the `RES̅` pin (note it's active-low, marked "Active on 0v" in the slide). This RC network is what creates the *slow, ramping* voltage rise needed at power-on. The 8284A takes that slow ramp, cleans it up (via the Schmitt trigger, below), and outputs a crisp digital `RESET` pulse to the CPU. The slide notes two distinct ways to trigger a reset: **Power-on Reset** (happens automatically every time the system is powered up) and **Manual Reset** (a physical button the user can press, which also pulls `RES̅` low/high through the same RC path).

**Timing requirement, stated precisely:** *"RESET=1 no later than 4 CC [clock cycles] after system power is applied, and to be high for at least 50 microseconds. FF ensures RESET=1 after 4 CC and RC ensures high for 50 microseconds."* — meaning the flip-flop inside the 8284A guarantees the *timing relative to the clock* (the 4-cycle minimum), while the external RC network's charging curve guarantees the *absolute duration* (the 50 microsecond minimum).

### Why a Schmitt trigger, specifically?

![[schmitt_trigger.png]]

An RC circuit charging up produces a **smooth, gradual, analog** voltage ramp — not a sharp digital edge. If you fed that slow ramp directly into ordinary logic gates, the gates could sit in an ambiguous "half-on" state for a relatively long time while the voltage crawls through the middle region, which can cause erratic, noisy, or multiple-triggering behavior downstream.

A Schmitt trigger fixes this by using **two different thresholds** instead of one:
- **Upper threshold voltage (VUT):** once the input climbs above this, the output snaps cleanly to HIGH.
- **Lower threshold voltage (VLT):** once the input falls below this, the output snaps cleanly to LOW.
- **Anywhere in between the two thresholds**, the output simply **holds whatever value it last had** — it refuses to change until the input has moved decisively past one threshold or the other.

This gap between the two thresholds (called *hysteresis*) is exactly what makes the output immune to noise and slow ramps: a small wobble in a slowly-rising signal won't cause the output to flicker back and forth, because the input has to travel the *entire distance* between VLT and VUT before the output will flip again. This is precisely why the 8284A places a Schmitt trigger right after the RC reset network — it converts that slow analog charging curve into one single, clean, decisive digital transition.

---

## 10. READY signal and Wait States — full mechanism

![[ready_wait_state.png]]

- If `READY` reads as logic 1 at the sampling point, it has **no effect** — the bus cycle proceeds normally into its next state.
- If `READY` reads as logic 0, the microprocessor **enters a wait state** and simply remains idle, repeating that same wait state over and over, until READY finally goes back to 1.

Reading the timing diagram: normally a bus cycle is T1 → T2 → T3 → T4. But if `READY` is sampled as logic 0 **at the end of T2**, the CPU does *not* proceed to T3. Instead, it inserts an extra clock period called **Tw** (a wait state) between T2 and T3. `READY` then gets re-sampled again at the *middle* of that Tw period — if it's still 0, another Tw gets inserted; if it's now 1, the CPU finally proceeds on into T3 as normal. This can repeat for as many Tw cycles as the slow device needs.

**Why this matters:** *"Wait states are required for slower memory and I/O components."* A wait state (Tw) is simply an extra clock period inserted specifically to lengthen the bus cycle so a slow device has enough time to respond. Numeric example from the slide: *"If the normal effective memory access time is 460 ns with a 5 MHz clock, by lengthening [it by] one clock period (200 ns) [it becomes] 660 ns."* — i.e., one inserted wait state adds exactly one clock period's worth of extra time (200 ns at 5 MHz) to the total access time.

### The Ready Synchronization circuitry itself

![[ready_sync_circuit.png]]

Two independent READY inputs, `RDY1` and `RDY2`, each gated by their own qualifier pin (`AEN1`, `AEN2`), exist specifically *"to accommodate two Multi-Master system busses."* If your system doesn't actually use a multi-master bus, the corresponding `AEN` pin should simply be tied LOW so it doesn't interfere.

The slide explains carefully *why* synchronization is even needed here: *"Synchronization is required for all asynchronous active-going edges of either RDY input to guarantee that the RDY setup and hold times are met. Inactive-going edges of RDY in normally-ready systems do not require synchronization but must satisfy RDY setup and hold as a matter of proper system design."* This connects to the general concept of **setup time** (data must be stable *before* the clock edge that captures it) and **hold time** (data must remain stable for a bit *after* that clock edge too) — shown in the small waveform diagram with `tsu` (setup) and `thd` (hold) marked around a "Stable" window. If an external READY signal changes value too close to the exact moment the CPU is trying to sample it, the sampling flip-flop can behave unpredictably — so the 8284A inserts a D flip-flop between the raw asynchronous RDY input and the 8284's clock, specifically to "clean up" that timing relationship before READY ever reaches the CPU.

### The Wait-State Generator circuit (building a fixed, predictable delay)

![[wait_state_circuit.png]]

*"Usually FOUR (4) clock signals are required to complete a memory read or a memory write operation."* — meaning even a "normal" bus cycle already inherently uses 4 clock periods (T1–T4); a wait-state generator is for when you deliberately need *more* than that, for a chip that's simply too slow even for the standard 4-cycle timing.

The circuit shown uses a **LS164 shift register** — an "8-bit serial shift register" fed by the system clock, with its parallel outputs (`Q0` through `Q7`) tied through some logic gates back into the `RDY1` line of the 8284A. Since a `CS̅` (Chip Select) signal from the addressed memory device gates this whole arrangement (noted "CS=logic 0 enable memory device"), the circuit only activates its forced-wait behavior while that specific slow device is actually being accessed. Essentially, this shift register counts out a fixed number of extra clock pulses before it finally lets `RDY1` go high again — giving you a hardware-configurable, predictable number of wait states tailored to exactly how slow a particular memory or I/O chip is.

---

## 11. Bus Cycles (T1–T4) — the full read and write timelines

*"There are at least four clock periods in a bus cycle of the 8086 microprocessor. These four clock periods are called T1, T2, T3, and T4 states. These four clock states give a bus cycle duration T of 200 ns × 4 = 800 ns in a 5-MHz 8086 system."*

### Read Cycle, state by state

![[bus_cycle_read.png]]

- **T1:** the microprocessor puts a valid address onto the Address and Address/Data bus. The `ALE`, `DT/R̅`, and `IO/M̅`/`M/IO̅` signals are also output right here, during T1, so any latching hardware can capture the address at exactly the right moment.
- **T2:** `RD̅` and `DEN̅` become active, and — critically — the bus is momentarily put into the **high-impedance state**. This "turn-around" gap exists because during T1 the CPU was *driving* the address/data lines (as an output), but now for a read it needs those same lines to become an *input* instead; briefly going high-impedance avoids any moment where the CPU and the memory chip might both be trying to drive the same wire at once.
- **T3:** looking at the timing diagram, the Address/Data lines are now labeled "DATA FROM MEMORY" — the addressed memory device is actively driving its data onto the bus.
- **T4:** the data that appeared during T3 is finally, actually latched/read by the CPU, and the `RD̅` signal deactivates (goes back to inactive/high), signaling the end of the read.

### Write Cycle, state by state

![[bus_cycle_write.png]]

- **T1:** exactly like the read cycle — the microprocessor puts the address onto the address bus, with `ALE`, `DT/R̅`, and `IO/M̅`/`M/IO̅` all set appropriately.
- **T2:** `WR̅` and `DEN̅` become active. Unlike the read cycle, there's **no** high-impedance turn-around needed here, because the CPU continues to be the one driving the bus the whole time — it simply switches from outputting an address to outputting data on the very same wires. You can see in the timing diagram the Address/Data line goes straight from "ADDRESS" to "DATA WRITTEN TO MEMORY" without any gap.
- **T3 and T4:** the data is actually written out to the memory or I/O device during these states. By the end of T4, all bus signals are deactivated in preparation for the next bus cycle, and `WR̅` returns to logic 1.

**The single cleanest way to state the read-vs-write difference (good exam line):** in a read cycle the bus must briefly "turn around" to high-impedance between the address-out phase and the data-in phase, because control of the bus is passing from the CPU to the memory device; in a write cycle no such turn-around is needed, because the CPU remains in control of the bus for the entire cycle, first outputting the address and then outputting the data itself.

---

## 12. RC Charging Math — the formula, and the fully worked example

This isn't 8086-specific circuitry — it's the general electronics formula that *explains why* the RESET circuit needs a resistor and a capacitor in the first place (to create that slow, controlled voltage ramp discussed in Section 9).

![[rc_charging_formula.png]]

The setup: a switch, a resistor `R`, and a capacitor `C` in series with a supply voltage `Vs`. When the switch closes at time t=0, current (`I_charging`) flows and charges the capacitor. The voltage across the capacitor over time follows:

$$V_C(t) = V_S(1-e^{-t/RC})$$

- **`Vc`** — the voltage across the capacitor at time t (this is what's actually rising over time).
- **`Vs`** — the fixed supply voltage (the "final" value Vc is heading toward).
- **`t`** — how much time has elapsed since the switch closed.
- **`RC`** — the *time constant* of the circuit (often written as τ, "tau") — literally just Resistance × Capacitance, and it sets the overall *speed* of the charging curve.

**The shape of this curve, in plain terms:** at t=0, Vc=0 (uncharged). As time passes, Vc climbs quickly at first, then more and more slowly, gradually approaching (but mathematically never *quite* reaching) Vs. Specific milestones worth memorizing:
- After **1τ** has elapsed → Vc has reached **63%** of Vs.
- After **4τ** → Vc has reached **98%** of Vs (essentially charged, for practical purposes).
- After **5τ** → Vc is considered *"virtually = Vs"* (fully charged for all practical purposes).

### The worked example from the slides, explained step by step

![[rc_problem_statement.png]]

Given circuit: `Vs = 5V`, `R = 47kΩ`, `C = 1000µF`.

**(a) What is the time constant of the circuit?**

![[rc_problem_a.png]]

Just multiply R and C directly:
$$\tau = R \times C = (47\times10^3) \times (1000\times10^{-6}) = 47 \text{ seconds}$$
(The units work out because kΩ × µF conveniently gives you seconds directly — this is a standard shortcut worth remembering.)

**(b) How long will it take to charge the capacitor to 2.5V?**

![[rc_problem_b.png]]

Start from the main formula and solve for `t`:
$$2.5 = 5(1-e^{-t/47})$$
Divide both sides by 5: $0.5 = 1 - e^{-t/47}$
Rearrange: $e^{-t/47} = 1 - 0.5 = 0.5$
Take the natural log of both sides: $\ln(e^{-t/47}) = \ln(0.5)$, which simplifies to $-t/47 = -0.693147$
Solve for t: $t = 47 \times 0.693147 \approx 32.578$ seconds.

(Notice 2.5V is exactly half of 5V — and half-life-style problems like this always come out to $t = \tau \times \ln(2)$, which is exactly what happened here.)

**(c) What is the voltage across the capacitor after 100 seconds?**

![[rc_problem_c.png]]

This time you already know `t`, so just plug directly into the original formula — no rearranging needed:
$$V_C = 5(1-e^{-100/47}) = 5(1-e^{-2.128}) \approx 5(1-0.1191) \approx 4.404 \text{ V}$$

**General method for solving any version of this problem type:**
1. Always find $\tau = RC$ first.
2. If asked for **time to reach a given voltage**: rearrange the formula and solve for `t` using natural log, as in part (b).
3. If asked for **voltage at a given time**: just plug `t` straight into the formula, as in part (c).

---

## 📝 Practice Questions from the slides (checked against your syllabus boundary)

1. Explain the purpose of the `BHE̅`, `ALE`, and `A0` pins on the 8086 microprocessor. — **✅ in syllabus.** (Use Section 3 for ALE, Section 4 for BHE̅ and A0 — combine into one full answer covering all three.)
2. Explain three major functionalities of the 8284 Clock generator circuit. — **⏳ not yet in syllabus** (Section 8 has the full answer whenever you need it — the three functions are Clock, Reset, and Ready generation.)
3. Draw the reset (R-C) circuit and explain the activities of the manual reset pin. — **⏳ not yet in syllabus** (Section 9.)
4. Explain the procedure to generate the wait state. — **⏳ not yet in syllabus** (Section 10.)
