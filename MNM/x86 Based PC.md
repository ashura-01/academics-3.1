---
tags: [lecture2, motherboard, chipset, buses, bios, memory-map]
---

← [[Basics of Computer Systems|← Lecture 1]] | [[Home]] | Next: [[8086 Architecture|Lecture 3 →]]

# Lecture 2 — x86 Based Personal Computer

## 1. µP-Based Personal Computer System (big picture)

The **Microprocessor** sits between the **Memory system** and the **I/O system**, connected by **Buses**. A separate **Memory BUS** connects Memory ↔ Processor.

- **Memory system** contains: Dynamic RAM (DRAM), Static RAM (SRAM), Cache memory, Read-only memory (ROM), Flash memory.
- **I/O system** contains: Printer, Serial communications, Floppy disk drive, Hard disk drive, Mouse, CD-ROM drive, Plotter, Keyboard, Video monitor, Tape backup.
- Processor generations mapped to PC form factors:
  - **8086 / 8088 → XT machines**
  - **80286 / 80386 / 80486 → AT machines**
  - **Pentium → ATX machines** (with VESA(80486), PCI, USB, AGP, SATA buses)
- Bus architectures used in microcomputers: **ISA, EISA, VESA Local Bus, PCI**, etc.

![[attachments/L2_pc-system-block-diagram.png]]

---

## 2. The Motherboard

> The **motherboard** (a.k.a. system board, "the board") is the most important part of the PC.

- Houses the **CPU (processor)**, **RAM**, **ROM**, other hardware, and provides slots for all peripherals to plug into. Contains all control circuitry for the system to work.
- Has **slots/sockets** for connecting peripherals/support hardware.
- **AGP (Accelerated Graphics Port)**: used exclusively for video cards.
- **ATA adapter**: interfaces for hard disk drives.
- **PCI (Peripheral Component Interconnect) connectors**: electronic connections for sound cards, network cards, etc.

![[attachments/L2_motherboard-diagram-asus.png]]

### Rear I/O ports & Jumpers
Typical rear panel: **Mouse, Keyboard, Ethernet, USB, Parallel Port, VGA, Serial Port, Audio In/Out, MIC**.

- **Jumpers**: used on some motherboards to **configure voltage and operating speeds**. A jumper closes an electrical circuit, letting electricity flow to perform a function.
  - Example: pins **1-2 jumped = Normal mode**, **2-3 jumped = Config mode**, **open (no jumper) = Recovery mode**.
- **Pins**: connect things like the reset button, hard-disk-activity LED, built-in speaker.
- Before **Plug and Play (PnP)**, jumpers were used to adjust device resources like which **IRQ (Interrupt ReQuest)** a device uses.

![[attachments/L2_motherboard-rear-ports-jumper.png]]

---

## 3. Chipset

> A **chipset** = a group of ICs (controllers) working together to control the flow of data to/from and within the motherboard.

The processor needs various supporting chips for: buffering, decoding, generating control signals, interfacing, handling parallel/serial data, bus control, clock generation, system timing, disk controllers, DMA controllers, memory controller, etc.

A classic chipset = **2 major microchips**: **North Bridge** + **South Bridge**.

### North Bridge vs South Bridge

| North Bridge | South Bridge |
|---|---|
| Situated **close to the processor**; covered by a heat sink (handles large heat). | Handles data from **PCI, USB, ATA, and ISA (now obsolete)** buses. |
| Handles the **most important/high-speed** tasks: connection between **CPU and main memory**. | Controller for **I/O**, which can afford to be **slower** than the main system bus. |
| Also handles data for **AGP (Accelerated Graphics Port)**. | Some ports connect I/O to the motherboard via adapters plugged into slots. |
| Connects to: CPU, RAM, Video Card. | Connects to: PCI Express/PCI, USB, ATA; also LPC/Super I/O → Floppy, COM, LPT, Keyboard, Mouse, other I/O devices. |

- **FSB (Front Side Bus)** = another name for the **system bus** (between North Bridge and CPU/RAM/Video Card).
- **LPC (Low Pin Count) Bus**: basically a version of PCI, connects **Super I/O chip** → interfaces serial port, parallel port, PS/2 mouse/keyboard, line printer terminal, floppy drive (**low-speed devices**).
- Sound Card, Modem, Network, Firewire act as **PnP devices**; modern chipsets build them in.

![[attachments/L2_north-south-bridge.png]]

### Intel Hub Architecture (IHA) — Intel's 800 series chipsets
- **Replaced** the classic North Bridge/South Bridge chipset.
- Two parts:
  - **GMCH — Graphics and Memory Controller Hub**: interfaces between the high-speed processor bus, the hub interface, and the **AGP bus**.
  - **ICH — I/O Controller Hub**: interfaces between the hub interface and **ATA(IDE) ports, SATA ports, PCI bus**. Also includes the **LPC bus** (supports motherboard ROM BIOS and Super I/O chips).

![[attachments/L2_intel-hub-architecture.png]]

---

## 4. Bus Signal Transfer & Speed Terminology

### Processor Bus: FSB vs BSB

| Front-Side Bus (FSB) | Back-Side Bus (BSB) |
|---|---|
| Highest-speed bus in the system, core of chipset/motherboard | Connects **L2 cache ↔ CPU core** |
| Carries info between processor and **main memory** and North Bridge | Carries info between processor and **cache** |
| Runs at **800 MHz or higher**, normally **64 bits wide** | — |

![[attachments/L2_fsb-vs-bsb.png]]

### AGP — Accelerated (Advanced) Graphics Port
- Special video cards need extra **specialized graphics processors** and features, but are useless without **high-speed RAM access**.
- Intel designed a dedicated port connecting the processor to the chipset's North Bridge/Memory Hub — appears as a single **AGP slot**.
- **AGP clock = 66.66 MHz**, basic mode = 1 channel of 32 bits (4 bytes) → bandwidth ≈ 266 MBps (66.66 × 4).
- **AGP card speeds**: 2x, 4x, 8x → number = multiplier of channels; e.g. **8x data rate ≈ 266 × 8 MBps**.
- By 2006, **PCI Express (PCIe)** graphics interface largely replaced AGP.

![[attachments/L2_agp-and-memory-slot.png]]

---

## 5. Expansion Buses — Generational Comparison

### 1st Generation

| Bus | Details |
|---|---|
| **ISA** (Industry Standards Architecture) | 16-bit devices, **8 MHz, 16 MB/s**. Standard peripheral interface bus for 80286. |
| **EISA** (Extended ISA) | 32-bit devices, **8 MHz, 33 MB/s**. For 80386–80486 (compatible with earlier MCA/Microchannel Architecture). |
| **VESA** (Video Electronics Standards Association, "VL-Bus") | 32-bit µP (latest 64-bit); interfaces disk and video to µP. |

### 2nd Generation

| Bus | Details |
|---|---|
| **PCI** (Peripheral Component Interconnect) | 32/64-bit bus. Transfers at **33 MHz**; bandwidth = 33.33 MHz × 32 bits = **133 MBps**. Features: (a) Supports **3.3V and 5V** signaling, (b) **Processor-independent**, (c) **Plug and Play**, (d) Allows **bus mastering** (any peripheral can take charge of the bus — important for **DMA**). |

![[attachments/L2_isa-pci-slots.png]]

### Why buses moved from parallel → serial
- All buses discussed so far were **parallel buses**: 8/16/32/64 bits moved together → needs a **large number of connector pins**.
- Problem: **"skew"** — not all bit information arrives at the destination at the same time.
- More data lines → more **crosstalk and electromagnetic interference (EMI)**.
- Trend moved toward **serial** communication to avoid these problems.

![[attachments/L2_serial-vs-parallel-communication.png]]

### 3rd Generation

**PCIe (PCI-Express)**
- Data sent **full duplex** (two separate one-way paths) over pairs of differentially-signaled wires called a **lane**.
- Multiplication factor: **1, 2, 4, 8, 16, or 32 lanes**.
- Each **lane = 250 MBps** throughput per direction.
- 8 lanes → bandwidth = 250 × 8 = **2000 MBps** (one way).
- **PCIe 16x → 4000 MBps.** Compare: PCI = 133 MBps (one way) and needs many more pins (parallel port).
- Different slot lengths exist because of the different **number of lanes** — 16x connector = advanced graphics card needing high-speed transfer.
- **Important features: Hot-pluggable and Hot-swappable.**
  - **Hot-swapping**: changing components **without significant interruption** to the system.
  - **Hot-plugging**: changing/adding components that **interact with the OS** while running.
  - No need to power off the computer (similar to USB).

![[attachments/L2_pcie-lanes-diagram.png]]
![[attachments/L2_pcie-hotswap-slots.png]]

**USB (Universal Serial Bus)**
- Connects keyboard, mouse, modem, sound cards to µP.
- Speeds: **USB1 = 10 Mbps, USB2 = 480 Mbps, USB3 = 4.8 Gbps** (fiber optics).
- A USB system = **1 host controller + multiple devices** in a tree-like fashion via **hub devices**. Hubs can be cascaded up to **5 levels**. Up to **127 devices** per host controller.
- Uses **4 shielded wires**: 2 are power (**+5V and GND**), 2 are **twisted-pair differential data signals**.
- Uses **NRZI (Non-Return to Zero Invert)** encoding to synchronize host & receiver clocks.
- Supports **plug-and-play** with dynamically loadable/unloadable drivers.
- USB connector provides a single **5V wire**; a bus segment delivers up to **500 mA**. Devices needing more use an **external power source** (e.g., printers, some external hard disks); mice/keyboards don't need extra power.

![[attachments/L2_usb-ata-sata-diagram.png]]

**ATA (IDE / Integrated Drive Electronics) / SATA**
- The hard-disk drive's **controller is integrated into the drive itself**, instead of a separate add-on ISA card.
- **Serial ATA (SATA)** interface connects HD ↔ PC. **SATA-2 = 300 MBps/second.**

---

## 6. SIMM and DIMM (Memory Modules)

- Early RAM = **DIP (Dual In-Line Package)** chip.
- Modern memory = cards ("modules") with RAM chips embedded, plug into sockets.
- **SIMM (Single In-Line Memory Module)**:
  - **30-connector** SIMM = **8-bit** memory.
  - **72-connector** SIMM = **32-bit** memory.
- **DIMM (Dual In-Line Memory Module)** = **64-bit** memory. **Two SIMMs** (matched pair) can be replaced by **one DIMM**.

![[attachments/L2_simm-dimm.png]]

---

## 7. System BIOS (Basic Input Output System)

- Last ~10 years, BIOS lives in **flash ROM**. Advantage: flash ROM is **re-writable** while on the circuit board — contents can be changed (a "**flash upgrade**").
- ROM family / hierarchy of read-only memory tech:
  - **PROM** — Programmable ROM
  - **EPROM** — Erasable Programmable ROM (fully erasable, dense)
  - **EEPROM** — Electrically Erasable PROM, **byte-writable**, less dense
  - **Flash memory** — combines EPROM + EEPROM ideas, **dense, 1 transistor/bit**

---

## 8. Intel Motherboard (labeled components)

Key labeled parts on a real Intel motherboard: **ATX Connector, ATA, SATA, DIMM Sockets, Processor socket, Fan, Heat Sink, DIMM (installed RAM), PCIe Slots, PCI Slots.**

![[attachments/L2_intel-motherboard-labeled.png]]

---

## 9. Memory Map of the PC

### XMS — Extended Memory System
- **No Extended Memory System on the 8086/8088** (real mode only supports 1 MB).
- Extended memory capacity by processor generation:
  - 80286 / 80386SX → **15 MB**
  - 80386SL/SLC → **31 MB**
  - 80386EX → **63 MB**
  - 80386DX, 80486, Pentium → **4,095 MB**
  - Pentium Pro → **64 GB**
  - Convention: **64G less 1M for servers**, **4G less 1M for PC**.
- **Before 80286**: Real mode gives **no support** for memory protection, multitasking, or code privilege levels.
- **After 80286**: **Protected mode** introduced (memory protection, multitasking, privilege levels).
- **TPA (Transient Program Area)**: "**transient**" = a property of any element in the system that is **temporary**. TPA = the first **640 KB** of real-mode conventional memory (below the 1 MB line), where DOS/programs actually run.

![[attachments/L2_memory-map-of-pc.png]]

### 0x00000–9FFFF: Transient Program Area (TPA) — DOS Concept
Layout (low → high): **Interrupt vectors → BIOS communications area → DOS communications area → IO.SYS program → MSDOS program → Device drivers (e.g. MOUSE.SYS) → COMMAND.COM → Free TPA → MSDOS program (top)**.

- **BIOS and DOS Communication Area**: transient data used by programs to access I/O devices and internal features.
- **MSDOS Area**: controls the operation of the computer system.
- **IO.SYS**: loads into TPA from hard disk when DOS starts; contains link programs for keyboard/video display/printer; links DOS to programs stored in system BIOS ROM.
- **COMMAND.COM**: controls the PC and I/O devices (e.g. keyboard) when working in DOS.
- **Free TPA**: holds **TSR** programs.
- **Interrupt Vectors**: access various features of DOS, BIOS, applications, via the 8086 instruction `INT x`. Located in the **first 1024 bytes** of memory (addresses 000000H–0003FFH). **256 entries × 4 bytes** each (segment + offset, real mode) → format per entry: `Seg high | Seg low | Offset high | Offset low`.

![[attachments/L2_tpa-interrupt-vector-table.png]]
![[attachments/L2_tpa-detail-diagram.png]]

### TSR — Terminate and Stay Resident (why it exists)
- Normally, DOS runs **only one program at a time**; when a program finishes, it returns control to DOS via system call **INT 21h/4Ch**.
- Memory/resources used are then **marked free** — makes it impossible to "restart parts" of a program without reloading everything.
- If a program instead ends via **INT 27h** ("**terminate but stay resident**" → hence **"TSR"**), the OS does **not** reuse a chosen chunk of its memory — up to **64 KB** can stay resident.

---

### 0xA0000–FFFFF: System Area

The system area contains programs from: a **read-only memory (ROM)** or **flash memory (EEPROM)**, and areas of **read/write (RAM)** memory for data storage.

| Region | Size | Description |
|---|---|---|
| **BIOS System ROM** | 64 KB | Contains programs that set up the computer; procedures that control basic I/O. Booting Program + I/O Control Program live here. |
| **BASIC Language ROM** | 64 KB | Only on early PCs. IBM Cassette BASIC — a version of Microsoft BASIC included in original IBM PC's BIOS ROM. Provided the default UI if no bootable floppy disk was found ("Cassette" = used cassette tapes, not floppies, to store data). |
| **Free area / Hard disk & LAN controller ROM** | 96 KB total | Free area used for **expanded memory** on a PC, or the **upper memory** system on an AT system. Hard Disk Controller ROM = memory for attached HD (HD ROM/BIOS). |
| **Video BIOS ROM** | 32 KB | Located on ROM/flash; contains programs that control the DOS video display. |
| **Video RAM (text area)** | 64 KB | Part of the first 128 KB Video RAM area (depends on video adapter). |
| **Video RAM (graphics area)** | 64 KB | Bit-mapped graphical data. |

![[attachments/L2_system-area-diagram.png]]
![[attachments/L2_system-area-detail-labeled.png]]

---

## 10. Memory Map used by Windows XP

- **TPA is the first 2 GB of memory**, starting from `00000000H` — called the **Windows Transient Program Area**.
- Every program written for Windows can use **only up to 2 GB of memory** (even in a 64-bit system) — the upper half (`80000000H`–`FFFFFFFFH`) is the **Windows Systems Area**.

![[attachments/L2_memory-map-windows-xp.png]]

---
← [[Basics of Computer Systems|← Lecture 1]] | [[Home]] | Next: [[8086 Architecture|Lecture 3 — 8086 Architecture →]]
