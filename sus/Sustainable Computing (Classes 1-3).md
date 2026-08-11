---
tags: [sustainability, computing, HUM3131, CSE, AI-carbon, LCA, e-waste, formulas]
course: "HUM 3131 – Environment and Sustainability"
source: "Class 1–3 — Introduction to Sustainable Computing, Carbon Footprint of AI/HPC/Cloud, Lifecycle Assessment & Embodied Emissions"
aliases: [Sustainable Computing Notes, Carbon Footprint of AI, LCA and Embodied Emissions]
---

# Sustainable Computing (Classes 1–3)

> [[Ethics of Technology & Sustainability|◀ Ethics of Technology & Sustainability]] | Sustainable Computing Series

**Contains 3 linked lectures:**
- [[#Class 1 — Introduction to Sustainable Computing & Environmental Footprint]]
- [[#Class 2 — Carbon Footprint of AI, HPC & Cloud Computing]]
- [[#Class 3 — Lifecycle Assessment (LCA) & Embodied Emissions]]

---

## Class 1 — Introduction to Sustainable Computing & Environmental Footprint

### Core Idea: "Digital ≠ Green, Invisible ≠ Impactless"
| Layer | Old Pollution (Visible) | Modern Pollution (Hidden) | The Illusion |
|---|---|---|---|
| What we see | Smoke, factories, oil spills | Data centers, chip factories, power plants | Email/streaming *looks* clean |
| Reality | Directly visible | Cost is hidden in the supply chain | It isn't clean — cost is displaced, not removed |

### Definition — Sustainable Computing
> The design, use, and disposal of computing systems in ways that minimize environmental harm and maximize social/economic benefit **across the full lifecycle** of technology.

**The 3 Pillars (E-R-C):**
- **E — Energy Efficiency**: reduce power consumption of hardware, software, and data centers
- **R — Resource Efficiency**: extend device lifetimes, reduce e-waste, use sustainable materials
- **C — Carbon Accountability**: measure, report, and reduce direct and indirect carbon emissions

### ICT's Environmental Impact — Key Numbers
- ICT produces **3–4% of global CO₂** — already **greater than aviation** emissions.
- If ICT were a country, it would be the **#5 energy consumer** in the world.
- Projected to reach **8–10% of global emissions by 2030**.
  *(Source: Freitag et al., 2021, "The real climate and transformative impact of ICT")*

### Global ICT Energy Footprint (breakdown)
| Component | Share | Notes |
|---|---|---|
| End-user devices | **~55%** | Smartphones, laptops, tablets, TVs, IoT |
| Data centers | **~30%** | Servers, cooling, storage, networking (24/7) |
| Networks | **~15%** | Mobile towers, fiber, routers — **5G uses 2–3× the energy per tower vs 4G** |

### Carbon Footprint of AI, Cloud & Data Centers
- Training **GPT-3** ≈ CO₂ equivalent of **~700,000 km of driving**.
- Training one large LLM ≈ **284 tonnes CO₂** (Strubell et al., 2019).
- **Daily AI inference** energy is now **bigger than training energy** (millions of queries = continuous drain).
- One **hyperscale data center** ≈ electricity use of **1 million homes**.
- **Ireland (2024):** data centers consume **18% of national electricity**.
- **USA (2030 projection):** data centers → **10% of US electricity**.
- **Cooling overhead:** **40–50%** of data center energy is used just for cooling servers.

### Embodied vs. Operational Emissions
Every device emits carbon at **two stages**:

**1. Embodied Emissions (BEFORE use)**
- Mining: lithium, cobalt, rare earth metals
- Chip fabrication: 1 TSMC wafer ≈ **800 kg CO₂**
- Assembly & shipping
- **Result: a smartphone = 70–90 kg CO₂ before it's ever switched on**

**2. Operational Emissions (DURING use)**

| Activity | CO₂ per use |
|---|---|
| 1 hr Netflix | 36–100 g CO₂ |
| 1 hr Zoom call | 150–1,000 g CO₂ |
| Email with attachment | ~10 g CO₂ |
| 1 Google search | 0.2–0.7 g CO₂ |

### Where Emissions Actually Come From (Key Takeaways)
| Device | Manufacturing share | Implication |
|---|---|---|
| Smartphones | ~80% | Keep using it longer, don't replace early |
| Laptops | ~75% | Use a laptop longer, not a new one |
| Servers | ~50/50 | Operational energy matters more here |
| Data centers | Energy-use dominated | Efficiency improvements matter most |

### GHG Protocol: Scope 1, 2 & 3 Emissions
| Scope | Category | Examples | Notes |
|---|---|---|---|
| **Scope 1** | Direct emissions | Diesel generators, company vehicles, on-site fuel combustion | Easiest to measure/control |
| **Scope 2** | Purchased energy | Electricity to power servers, cooling, office electricity | Reduced by buying renewable energy (RECs) |
| **Scope 3** | Value chain ("THE BIG ONE") | Device manufacturing (Foxconn, TSMC), supplier transport, user charging, e-waste | **70–90% of big tech's emissions are Scope 3** |

*Reference: GHG Protocol Corporate Standard (2004, updated 2011)*

### Lifecycle Assessment (LCA) — Preview (expanded in Class 3)
5 stages: **Mine → Manufacture → Ship → Use → End-of-Life (EoL)**
- Design decisions at Stage 1–2 (raw materials + manufacturing) **lock in 80% of a product's total environmental impact**.
- Engineers must consider sustainability **at the design stage**, not as an afterthought.
- *Reference: ISO 14040/14044*

### Sustainable Computing as a Professional (Engineering) Obligation
1. **Design Efficiently** — write algorithms that use fewer CPU cycles
2. **Choose Green Infrastructure** — deploy on carbon-aware cloud regions; schedule heavy jobs when grids are cleaner
3. **Measure Impact** — use LCA and carbon tools to quantify emissions
4. **Design for Longevity** — software that keeps hardware useful longer reduces embodied emissions
5. **Ethics & Accountability** — disclose environmental costs; push back on greenwashing
6. **Align with SDGs** — SDG 7 (Clean Energy), SDG 9 (Industry), SDG 13 (Climate Action)

---

## Class 2 — Carbon Footprint of AI, HPC & Cloud Computing

### Framing: "One AI query = 10× the energy of a Google search"
Chain: **Software → Hardware → Electricity → Emissions**; better code = lower emissions.

As future professionals:
- **System architect** → chooses hardware & infrastructure
- **ML engineer** → chooses model size & training strategy
- **Researcher** → defines what "progress" means
- **Tech leader** → balances innovation with responsibility

### Where AI Carbon Comes From (3 factors)
1. **Computation Intensity** — FLOPs, model size/depth/sequence length, training iterations/epochs
2. **Electricity Consumption** — GPU/TPU power draw (300–700 W/unit), memory/storage/networking, cooling & power losses
3. **Carbon Intensity of the Grid** — coal-heavy grid = high emissions; renewable grid = low emissions; **same compute can produce up to 39× different carbon**

> **Conceptual formula:** `Carbon Emissions = Compute × Energy Efficiency × Carbon Intensity`

- Efficient algorithm on a **dirty grid** = still high carbon.
- Inefficient algorithm on **clean energy** = still wastes resources.
- Discussion prompt: *Should AI research papers publish energy/carbon cost alongside accuracy metrics?*

### AI Training Loop (why it's energy-intensive)
| Step | What Happens |
|---|---|
| Dataset | Load training data into GPU memory |
| Forward Pass | Compute predictions via matrix multiply |
| Loss Function | Measure error between prediction & truth |
| Backward Pass | Compute gradients for every weight |
| Gradient Update | Adjust weights to reduce error |
| Repeat × Millions | Until loss converges — days to weeks |

**Why it costs so much energy:**
- **Matrix multiplication**: every transformer forward pass multiplies billions of floating-point numbers
- **GPU power draw**: A100/H100 GPUs run at 300–700 W per unit, often thousands running 24/7 for weeks
- **Inter-GPU communication**: gradient sync (NVLink/InfiniBand) uses significant power beyond compute
- **High memory bandwidth**: moving data GPU memory ↔ compute cores is often the bottleneck
- **Continuous cooling**: cooling runs at full load for entire training duration — can equal compute in energy

### AI Inference — "The Silent Multiplier"
**Inference** = executing a trained model to produce outputs from new inputs (chatbots, image classification, voice assistants, recommendations, fraud detection, medical diagnosis).

| Dimension | Training | Inference |
|---|---|---|
| Compute | Extremely high | Low–moderate |
| Duration | Days to weeks | Milliseconds |
| Frequency | Rare (once per model) | Massive (billions/day) |
| Energy per task | Very high | Small |
| Lifetime emissions | Large, concentrated | **Potentially larger** (cumulative) |
| Your control | Architecture, dataset | Model size, batching |

### The Training–Inference Paradox
- **Training dominates initial emissions** (e.g., 500 tonnes CO₂ once).
- **Inference can dominate lifetime emissions** (e.g., 0.1 g/query × 10 billion queries = 1,000 tonnes CO₂ total).
- ⇒ Model compression, edge inference, and efficient serving are **as important** as training optimization.

### Bigger Models ≠ Always Better
More parameters = more expressiveness, but also more energy.

| Why More Parameters Help | Why More Parameters Hurt Sustainability |
|---|---|
| Capture more complex patterns | Larger memory footprint (more DRAM) |
| Improve generalization | More compute per token |
| Enable emergent reasoning | Higher training/inference energy |
| Learn from larger datasets | Harder to deploy on edge/mobile |

> **Scaling Law:** Compute grows **super-linearly** with parameters; accuracy improves **sub-linearly** → diminishing returns + exploding energy cost.
> Discussion: *Should AI progress be measured by accuracy alone — or accuracy per watt?*

### FLOPs ≠ Energy ≠ Carbon (The Three-Step Disconnect)
`FLOPs → Energy (kWh) → Carbon (kgCO₂)`

- **FLOPs**: hardware-agnostic; useful for comparing algorithm complexity (standard metric in papers).
- **Energy (kWh)**: actual power drawn during execution — what you pay for and what drives emissions.
- **Carbon (kgCO₂)**: energy × carbon intensity of the grid at that location/time.

**Gap 1 — FLOPs don't capture:**
- DRAM access (memory reads/writes — very energy-hungry)
- Cache misses (forces expensive off-chip memory traffic)
- Data movement between CPU, GPU, storage
- Network synchronization in distributed training

**Gap 2 — Energy ≠ Carbon:**
- Same GPU job in Norway ≈ 18 gCO₂/kWh (hydro) vs. Bangladesh ≈ 620 gCO₂/kWh (gas)
- **34× difference in carbon — zero difference in FLOPs**
- Location and time of day both affect carbon intensity

### Carbon Intensity (CI) by Country (grid mix dependent)
| Country | CI (gCO₂/kWh) | Grid Type |
|---|---|---|
| Norway | 18 | Hydropower dominant |
| France | 58–60 | Nuclear dominant |
| California | 210 | Mixed renewables + gas |
| US Average | 386 | Gas, coal, nuclear mix |
| EU Average | 255 | Mixed + renewables |
| Bangladesh | 620 | Natural gas dominant |
| India | 710 | Coal dominant |
| Poland | 700 | Coal dominant |
| Iceland | 18 | 100% geothermal/hydro |

> **Carbon-aware scheduling**: same workload run in Norway vs. Bangladesh → **34× less CO₂**, with **zero code change**.
> *Source: Electricity Maps (electricitymaps.com)*

### Sustainable AI Strategies & Their CSE Roots
| Technique | What It Does | Carbon Impact | CSE Subject |
|---|---|---|---|
| Pruning | Remove redundant neurons | 5× lower compute | Machine Learning |
| Quantization | Reduce precision (FP32 → INT8/INT4) | 4–8× smaller model | Computer Architecture |
| Knowledge Distillation | Train small model to mimic large one | 60–80% size, ~97% accuracy | Artificial Intelligence |
| Carbon-Aware Scheduling | Run jobs when/where grid is greenest | Up to 34× less carbon | Operating Systems |
| Fine-Tuning vs Training | Adapt existing model instead of training fresh | 100× less energy | ML / AI |
| Edge Inference | Run model on-device, no cloud round-trip | Eliminates network energy | IoT / Distributed Systems |
| Efficient Cooling (PUE) | Reduce overhead energy fraction below 1.5 | 30–40% facility savings | Computer Architecture |

> **Key insight:** Sustainable AI is a **design choice starting at the algorithm level**, not a policy bolted on afterward.

### Ethical & Professional Responsibility
- **Disclosure**: should AI systems disclose emissions like a "nutrition label"? (Currently almost none do.)
- **Accountability**: developer, company, or user — who is responsible? Regulation is still forming, but the **engineer shapes what is possible**.
- **Grading efficiency**: today we optimize for accuracy, speed, cost — energy/carbon are rarely in the SLA; future regulation may make this a first-class design constraint.

---

### 🧮 FORMULA — AI Carbon Footprint (Class 2 version)

$$
CO_2 = Energy\ (kWh) \times PUE \times Carbon\ Intensity\ (kgCO_2/kWh)
$$

| Variable | Meaning | Unit |
|---|---|---|
| Energy | Electricity consumed by GPU/TPU during workload (Power × Time) | kWh |
| **PUE** | Power Usage Effectiveness = Total Facility Power ÷ IT Equipment Power | ratio (world-class ≈1.1, average ≈1.5, poor ≈2.0+) |
| CI | Carbon Intensity of the electricity grid | kgCO₂/kWh |
| CO₂ | Total carbon emitted | kg |

**Three controllable levers:**
1. Efficient algorithm → less energy
2. Better data center → lower PUE
3. Greener grid → lower CI

### 📐 Worked Example — AUST ML Training Job (Bangladesh vs. Norway)

**Scenario:** A CSE student trains an image classification CNN for **8 hours** on **1× NVIDIA RTX 3090 GPU** (350 W peak, avg 70% utilization = **245 W**).

| Step | Calculation | Result |
|---|---|---|
| 1. Energy consumed | Power × Time = 0.245 kW × 8 hrs | **1.96 kWh** |
| 2. Add PUE overhead | Energy × PUE = 1.96 × 1.6 (AUST DC estimate) | **3.14 kWh** |
| 3a. Carbon in Bangladesh | Energy × CI = 3.14 × 0.620 | **1.95 kgCO₂** |
| 3b. Carbon in Norway | Energy × CI = 3.14 × 0.018 | **0.057 kgCO₂** |
| Difference | 1.95 ÷ 0.057 | **≈34× more CO₂ in Bangladesh** |

> Running the SAME code on a Norwegian server cuts CO₂ from 1.95 kg to 0.057 kg — a 34× reduction, with **zero algorithmic change**.

### 📝 Student Exercise (with Worked Solution)

**Problem:** A research team trains a deep learning model for **72 hours** on **4× A100 GPUs** (400 W each, **80% utilization**). Data center **PUE = 1.4**. Calculate total CO₂ if run in (a) Bangladesh (CI = 0.620) and (b) France (CI = 0.058). How much CO₂ is saved by choosing France?

*Hint given: Energy = 4 × 0.400 × 0.80 × 72 hrs × PUE, then CO₂ = Energy × CI.*

**Solution:**
1. **Energy** = 4 GPUs × 0.400 kW × 0.80 (utilization) × 72 hrs × 1.4 (PUE)
   = 1.6 kW × 0.80 = 1.28 kW → × 72 hrs = 92.16 kWh → × 1.4 = **129.02 kWh**
2. **CO₂ in Bangladesh** = 129.02 kWh × 0.620 kgCO₂/kWh = **≈80.0 kg CO₂**
3. **CO₂ in France** = 129.02 kWh × 0.058 kgCO₂/kWh = **≈7.48 kg CO₂**
4. **CO₂ saved by choosing France** = 80.0 − 7.48 = **≈72.5 kg CO₂ saved** (~10.7× less carbon, ~90.6% reduction)

---

## Class 3 — Lifecycle Assessment (LCA) & Embodied Emissions

### Opening Hook
> "Your phone was already polluting before you switched it on."
- **60–80%** of a smartphone's lifetime carbon footprint is produced **before first use** (mining, smelting, chip fabrication, assembly, shipping).
- This is called **Embodied Carbon** — invisible to every environmental label you've seen.

### What is LCA?
A systematic method for evaluating a product's environmental impact across its **entire life**, from raw material extraction to end-of-life disposal. Standardized by **ISO 14040** (principles) and **ISO 14044** (requirements).

**4 Phases of LCA:**
1. **Goal & Scope** — define system boundary (what's included/excluded); define functional unit (e.g., "1 smartphone used for 3 years")
2. **Life Cycle Inventory (LCI)** — quantify all inputs (energy, materials, water) and outputs (emissions, waste) per lifecycle stage
3. **Life Cycle Impact Assessment (LCIA)** — convert inventory data into impact categories: climate change, resource depletion, toxicity, water use, land use
4. **Interpretation** — analyze results, identify hotspots, compare scenarios, recommend improvements

### Five Stages of a Digital Product's Lifecycle
| Stage | Details |
|---|---|
| **01 Raw Material Extraction** | Lithium, cobalt, tin, tantalum, rare earths — 70+ elements in a smartphone. Congo = 70% of global cobalt supply (high environmental & human cost). |
| **02 Manufacturing & Fabrication** | Semiconductor fab uses 2,000+ chemicals. One chip wafer ≈ 800 kg CO₂. 14 nm chips require ultra-pure water & vacuum. Major fabs: TSMC, Samsung, Intel. |
| **03 Distribution & Packaging** | Air freight = **50× more CO₂** than sea freight (Apple ships iPhones by air at launch). Packaging adds 2–5% embodied carbon. Global supply chains = complex Scope 3. |
| **04 Use Phase & Maintenance** | Electricity consumption over device lifetime. Software updates extend useful life. Repair vs. replace decision is key. Charging ≈ 0.01 kWh/full charge. |
| **05 End of Life & Disposal** | 53.6 million tonnes e-waste in 2019 (UN). Only 17% formally recycled. Informal recycling = toxic exposure. Rare earth recovery < 1% globally. |

### Critical Materials in Devices
| Material | Role | Key Facts |
|---|---|---|
| **Cobalt** | Battery cathodes (no viable substitute yet) | ~70% of global supply mined in DR Congo under hazardous conditions |
| **Silicon** | Processor base material | Refined from quartz sand to 99.9999999% purity — foundation of every logic chip |
| **Rare Earth Elements (REE)** | Screen phosphors (color), magnets in HDD motors/speakers | 17 elements dispersed across every component; <1% recovered from e-waste globally |
| **Lithium** | Li-ion battery anode/cathode | Water-intensive brine extraction (Chile, Argentina "Lithium Triangle"); demand growing 30% YoY with EVs |

### Why Material Recovery Is So Hard
- **Dispersed in tiny quantities** — REEs spread across dozens of components at milligram scale; no single component justifies targeted extraction.
- **No automated disassembly** — devices designed for performance/thinness, not recyclability; robotic disassembly not economically viable yet.
- **Smelting loses rare elements** — high-temp smelting recovers bulk metals (copper, gold, silver) efficiently but destroys/disperses REEs entirely.
- **Design for Disassembly (DfD)** — engineering discipline using standard modular interfaces, fewer adhesives, documented repair paths. Framework laptop (10/10 iFixit) is the benchmark example.

### Manufacturing vs. Use Phase — What This Means for CSE
| Device | Manufacturing % | Use Phase % | CSE Engineer's Action |
|---|---|---|---|
| Smartphone | 80% | 20% | Extend device life; write software that runs on older hardware |
| Laptop | 75% | 25% | Extend hardware life; energy-efficient code matters less here |
| Desktop PC | 60% | 40% | Both matter; efficient software has real operational impact |
| Server | 50% | 50% | Both equal; algorithm efficiency directly reduces data center energy |
| Large Data Center | 35% | 65% | Operational dominates; PUE, cooling efficiency, scheduling matter most |

### E-Waste: The Engineering Problem
**Scale:**
- **62 million tonnes** of e-waste generated annually — fastest-growing solid waste stream on Earth.
- Every tonne contains ~70 elements.
- Only **17%** is formally recycled; the rest enters informal processing or landfill, leaching **lead, cadmium, mercury** into soil/groundwater.

**Why repair is technically hard:**
- Adhesive bonding replaced screws (thinner, sealed designs)
- Proprietary connectors lock out third-party parts
- Key components soldered directly to motherboards (RAM, storage)
- No published repair manuals
- Software-enforced hardware locks (activation pairing) disable replaced parts

**Right to Repair — the engineering response:**
- Modular design (Framework laptop: 10/10 iFixit, standard Torx screws, hot-swappable expansion cards)
- EU mandated USB-C standardization (2024); manufacturers must publish spare parts availability in the EU
- Software support for older hardware reduces replacement pressure

**CSE Student Action:**
- Write software that runs on older hardware (backward compatibility = a sustainability engineering choice)
- Avoid dependencies with large runtime footprints on resource-constrained devices
- Publish security updates for older device OS versions
- Advocate within organizations for device longevity policies

### Circular Economy Principles (R0–R7 Ladder)
| Code | Principle | Description |
|---|---|---|
| R0 | **Refuse** | Don't make it at all — question whether the product needs to exist |
| R1 | **Rethink** | Redesign to use fewer materials or enable dematerialization |
| R2 | **Reduce** | Minimize material/energy use in manufacturing; thin, light designs |
| R3 | **Reuse** | Design for second life — trade-in programs, refurbished device markets |
| R4 | **Repair** | Replaceable batteries, standardized screws, parts availability (iFixit score) |
| R5 | **Refurbish** | Industrial restoration to as-new condition (e.g., Apple Certified Refurbished) |
| R6 | **Remanufacture** | Disassemble, replace worn parts, reassemble — common for servers/medical devices |
| R7 | **Recycle** | Material recovery at end of life — **last resort**, loses embodied energy of fabrication |

---

### 🧮 FORMULA — The AI Carbon Equation (Class 3, Two-Step Version)

**Step 1 — Energy:**
$$
E = \frac{H \times P \times PUE}{1000}
$$
| Variable | Meaning |
|---|---|
| H | Hours — duration of training/inference run |
| P | GPU peak power draw in Watts (e.g., 400 W for A100) |
| PUE | Power Usage Effectiveness — DC overhead (hyperscale typical ≈1.2, poor facility ≈2.0) |
| /1000 | Converts Watt-hours to kilowatt-hours (kWh) |

**Step 2 — Carbon:**
$$
C = E \times CI
$$
where **CI** = Carbon Intensity of the grid (kgCO₂/kWh).

**Carbon Intensity reference table (Class 3):**
| Location | CI (kg/kWh) | Grid |
|---|---|---|
| Bangladesh | 0.620 | Coal-heavy — **highest** |
| India | 0.710 | Coal dominant — **high** |
| EU Average | 0.255 | Mixed + renewables — **medium** |
| France | 0.060 | 80% nuclear — **low** |
| Iceland | 0.018 | 100% geothermal/hydro — **lowest** |

### 📐 Worked Example — Estimating GPT-Scale Training Carbon

**Parameters:** 1,024 × A100 GPUs, 400 W each, PUE = 1.2, training duration = 34 days.

| Step | Calculation | Result |
|---|---|---|
| Convert to hours | 34 days × 24 hrs/day | **816 hours** |
| Step 1: Energy (E) | E = 816 × (1,024 × 400 W) × 1.2 / 1,000 = 816 × 409,600 W × 1.2 / 1,000 | **≈401,000 kWh** |
| Step 2a: Carbon in Bangladesh | 401,000 × 0.620 | **249 tonnes CO₂** |
| Step 2b: Carbon in EU Average | 401,000 × 0.255 | **102 tonnes CO₂** |
| Step 2c: Carbon in France | 401,000 × 0.060 | **24 tonnes CO₂** |
| Step 2d: Carbon in Iceland | 401,000 × 0.018 | **7.2 tonnes CO₂** |

> **Key engineering insight:** the most impactful decision an ML infrastructure engineer makes is not model architecture or hardware — it is **WHERE and WHEN** to run the job. Bangladesh emits **34× more** than Iceland for the identical training run.

**Discussion prompt:** *If Bangladesh's grid reaches 40% renewables by 2041 (national target), how would CI (and therefore training carbon cost) change?*

---

## 🔑 Quick Recall Summary
- **ICT = 3–4% of global CO₂**, projected 8–10% by 2030; bigger than aviation.
- **Device footprint split**: end-user devices ~55%, data centers ~30%, networks ~15%.
- **Embodied vs. operational**: smartphones/laptops are manufacturing-dominated (75–80%); servers/data centers are operations-dominated.
- **Scope 1/2/3**: Scope 3 (value chain) = **70–90%** of big tech's emissions.
- **AI carbon = Compute × Energy Efficiency × Carbon Intensity.**
- **FLOPs ≠ Energy ≠ Carbon** — same FLOPs can yield up to 34× different carbon depending on grid location/time.
- **Core formula:** `CO₂ = Energy (kWh) × PUE × Carbon Intensity (kgCO₂/kWh)`, or two-step: `E = H×P×PUE/1000`, then `C = E×CI`.
- **PUE** = Total Facility Power ÷ IT Equipment Power (lower is better; 1.1 = world-class, 2.0+ = poor).
- **Location/scheduling** (carbon-aware computing) is the single biggest lever an engineer controls — up to 34× reduction with zero code change.
- **LCA (ISO 14040/44)**: Goal & Scope → LCI → LCIA → Interpretation, across 5 stages (Mine → Mfg → Ship → Use → EoL).
- **E-waste**: 62 million tonnes/year, only 17% formally recycled.
- **Circular economy ladder (R0–R7)**: Refuse > Rethink > Reduce > Reuse > Repair > Refurbish > Remanufacture > Recycle (recycle = last resort).

---

## ❓ Probable Exam Questions & Answers

### Conceptual Questions

**Q1. Define Sustainable Computing and name its three pillars.**
> A: Sustainable Computing is the design, use, and disposal of computing systems in ways that minimize environmental harm and maximize social and economic benefit across the full lifecycle of technology. Its three pillars are: Energy Efficiency (reducing power consumption of hardware/software/data centers), Resource Efficiency (extending device lifetimes, reducing e-waste, using sustainable materials), and Carbon Accountability (measuring, reporting, and reducing direct and indirect emissions).

**Q2. Explain the difference between embodied and operational emissions, with examples.**
> A: Embodied emissions are produced before a device is ever used — during mining, chip fabrication, assembly, and shipping (e.g., a smartphone carries 70–90 kg CO₂ before its first use; one chip wafer ≈ 800 kg CO₂). Operational emissions occur during use, from the electricity consumed while running the device (e.g., 1 hour of Netflix = 36–100 g CO₂; a Google search = 0.2–0.7 g CO₂). For small consumer devices like phones and laptops, embodied emissions dominate (75–80%); for data centers, operational emissions dominate.

**Q3. Explain Scope 1, 2, and 3 emissions under the GHG Protocol. Why is Scope 3 significant for tech companies?**
> A: Scope 1 covers direct emissions a company generates itself (diesel generators, company vehicles, on-site fuel combustion). Scope 2 covers emissions from purchased energy (electricity for servers, cooling, office buildings). Scope 3 covers the entire value chain — device manufacturing, supplier transport, user electricity for charging, and e-waste at end of life. Scope 3 is significant because it represents 70–90% of big tech companies' total emissions, meaning most of their environmental impact lies outside their direct operational control, in their supply chains and users' hands.

**Q4. What is the "Training–Inference Paradox" in AI carbon emissions?**
> A: Training a model produces a large, concentrated one-time carbon cost (e.g., 500 tonnes CO₂ for one training run). However, because a trained model may be queried billions of times over its lifetime, even a tiny per-query energy cost (e.g., 0.1 g CO₂) can accumulate to exceed the training emissions (e.g., 1,000 tonnes CO₂ from inference). This means inference — not training — can dominate a model's lifetime carbon footprint, making techniques like model compression and edge inference just as critical as optimizing training.

**Q5. Explain why "FLOPs ≠ Energy ≠ Carbon." Give the two key gaps.**
> A: FLOPs (floating-point operations) measure algorithmic complexity but are hardware-agnostic — they don't capture real-world energy use. Gap 1: FLOPs don't account for DRAM access, cache misses, data movement between CPU/GPU/storage, or network synchronization, all of which consume real energy beyond raw computation. Gap 2: even identical energy consumption (kWh) does not equal identical carbon emissions, because carbon output depends on the carbon intensity of the local electricity grid — e.g., the same GPU job produces 18 gCO₂/kWh in Norway but 620 gCO₂/kWh in Bangladesh, a 34× difference with zero difference in FLOPs.

**Q6. What is Power Usage Effectiveness (PUE) and why does it matter?**
> A: PUE (Power Usage Effectiveness) is the ratio of Total Facility Power to IT Equipment Power — it measures how much extra energy (mainly for cooling and infrastructure overhead) a data center uses beyond the energy delivered directly to computing equipment. A PUE of 1.1 is world-class efficiency, 1.5 is average, and 2.0+ is poor. It matters because cooling overhead can account for 40–50% of a data center's total energy use, and reducing PUE directly reduces total carbon emissions for the same computational workload.

**Q7. Why does "carbon-aware scheduling" matter more than algorithmic efficiency in some cases?**
> A: Because the carbon intensity (CI) of electricity grids varies dramatically by location and time — from as low as 18 gCO₂/kWh in Norway/Iceland (hydro/geothermal) to over 700 gCO₂/kWh in Poland (coal-heavy). Running the exact same code, with zero algorithmic changes, in a cleaner-grid location can cut emissions by up to 34×. This means WHERE and WHEN a computation runs can matter more for carbon footprint than how efficiently the algorithm itself is written.

**Q8. What is Lifecycle Assessment (LCA)? Name its four phases and five product-lifecycle stages.**
> A: LCA is a systematic method (standardized by ISO 14040/14044) for evaluating the environmental impact of a product across its entire life, from raw material extraction to disposal. Its four phases are: (1) Goal & Scope Definition, (2) Life Cycle Inventory (LCI) — quantifying inputs/outputs, (3) Life Cycle Impact Assessment (LCIA) — converting data into impact categories, and (4) Interpretation — analyzing results and recommending improvements. The five stages of a digital product's lifecycle are: Raw Material Extraction, Manufacturing & Fabrication, Distribution & Packaging, Use Phase & Maintenance, and End of Life & Disposal.

**Q9. Why is material recovery from e-waste so technically difficult?**
> A: Rare earth elements and other critical materials are dispersed in tiny (often milligram-scale) quantities across dozens of components, so no single component concentrates enough material to justify targeted extraction. Devices are also designed for performance and thinness rather than recyclability, and robotic/automated disassembly is not economically viable with current technology. Additionally, high-temperature smelting — while effective at recovering bulk metals like copper, gold, and silver — destroys or disperses rare earth elements entirely, meaning fewer than 1% of REEs are recovered globally.

**Q10. Explain the R0–R7 circular economy ladder and why "Recycle" is considered a last resort.**
> A: The ladder ranks circular economy strategies from most to least preferable: Refuse (don't make it), Rethink (redesign to use fewer materials), Reduce (minimize material/energy in manufacturing), Reuse (design for second life/trade-in), Repair (replaceable parts, standardized screws), Refurbish (industrial restoration to as-new), Remanufacture (disassemble and rebuild), and Recycle (material recovery at end of life). Recycling is the last resort because it loses the embodied energy already invested in fabrication — melting down a chip to recover raw metal wastes all the energy that went into refining and manufacturing it, whereas repairing or reusing the product preserves that embodied value.

**Q11. Why does "manufacturing vs. use-phase" emissions split matter differently for a smartphone vs. a data center?**
> A: For a smartphone, manufacturing accounts for about 80% of lifetime emissions, so the most effective sustainability action is extending device life (using it longer, writing software compatible with older hardware) rather than focusing on operational energy efficiency. For a large data center, manufacturing is only about 35% while operations account for 65%, so the most effective actions are reducing PUE, improving cooling efficiency, and carbon-aware scheduling — because ongoing operational energy dominates the lifetime footprint.

**Q12. What ethical questions does the lecture raise about AI transparency and accountability?**
> A: It raises three key questions: (1) Should AI systems disclose their emissions, similar to a nutrition label, so users and policymakers can make informed choices — currently almost no AI product does this. (2) Who is accountable for AI's carbon footprint — the developer who built the model, the company running the servers, or the user sending queries? Regulation is still forming. (3) Should carbon efficiency be graded like latency or accuracy — i.e., should energy/carbon become a first-class design constraint (like an SLA metric) rather than an afterthought?

### Mathematical / Numerical Problems

**Q13. A student runs an inference workload for 5 hours on a single GPU drawing 300 W, in a data center with PUE = 1.3. If the workload runs in India (CI = 0.710 kg/kWh), calculate the total CO₂ emitted.**
> Solution:
> Energy = Power × Time × PUE / 1000... using E = H × P × PUE / 1000 (with P in Watts):
> E = 5 hrs × 300 W × 1.3 / 1000 = 1,950 / 1000 = **1.95 kWh**
> CO₂ = E × CI = 1.95 × 0.710 = **≈1.38 kg CO₂**

**Q14. Using the formula CO₂ = Energy × PUE × CI, calculate emissions for a workload consuming 50 kWh of raw compute energy, in a data center with PUE = 1.5, located on the US Average grid (CI = 0.386 kg/kWh).**
> Solution:
> Effective Energy = 50 kWh × 1.5 (PUE) = **75 kWh**
> CO₂ = 75 × 0.386 = **≈28.95 kg CO₂**

**Q15. A research lab trains a model for 10 days on 8× H100 GPUs (700 W each), with a data center PUE of 1.25. Calculate the energy consumed in kWh.**
> Solution:
> Hours = 10 days × 24 hrs/day = 240 hours
> E = H × P × PUE / 1000 = 240 × (8 × 700 W) × 1.25 / 1000
> = 240 × 5,600 W × 1.25 / 1000 = 1,680,000 / 1000 = **1,680 kWh**

**Q16. Using the result from Q15, calculate the carbon emitted if the job runs (a) in Poland (CI = 0.700) and (b) in Iceland (CI = 0.018). What is the reduction factor?**
> Solution:
> (a) Poland: 1,680 kWh × 0.700 = **1,176 kg CO₂ (1.176 tonnes)**
> (b) Iceland: 1,680 kWh × 0.018 = **30.24 kg CO₂**
> Reduction factor = 1,176 / 30.24 ≈ **38.9× less carbon in Iceland**

**Q17. (Repeat of in-slide student exercise) A team trains a deep learning model for 72 hours on 4× A100 GPUs (400 W each, 80% utilization), PUE = 1.4. Calculate total CO₂ for (a) Bangladesh (CI = 0.620) and (b) France (CI = 0.058), and the CO₂ saved by choosing France.**
> Solution:
> Energy = 4 × 0.400 kW × 0.80 × 72 hrs × 1.4 = 1.6 × 0.80 = 1.28 kW → × 72 = 92.16 kWh → × 1.4 = **129.02 kWh**
> (a) Bangladesh: 129.02 × 0.620 = **≈80.0 kg CO₂**
> (b) France: 129.02 × 0.058 = **≈7.48 kg CO₂**
> CO₂ saved = 80.0 − 7.48 = **≈72.5 kg CO₂ saved** (about 10.7× less, ~90.6% reduction) by running the identical job in France instead of Bangladesh.

**Q18. If a data center's Total Facility Power is 220 kW and its IT Equipment Power is 160 kW, calculate the PUE. Is this world-class, average, or poor?**
> Solution:
> PUE = Total Facility Power ÷ IT Equipment Power = 220 / 160 = **1.375**
> This falls between world-class (1.1) and average (1.5) — closer to average, indicating moderate but not excellent cooling/infrastructure efficiency.

**Q19. A smartphone requires 0.01 kWh per full charge and is charged once per day. Over 3 years (1,095 days) on the EU Average grid (CI = 0.255 kg/kWh), what is the total operational carbon from charging alone? (Ignore PUE for personal charging.)**
> Solution:
> Total energy = 0.01 kWh × 1,095 days = **10.95 kWh**
> CO₂ = 10.95 × 0.255 = **≈2.79 kg CO₂** over 3 years — illustrating why this is negligible compared to the 70–90 kg CO₂ embodied in manufacturing the phone.

**Q20. If Bangladesh's grid carbon intensity drops from 0.620 to a hypothetical 0.350 kg/kWh (as renewables increase), recalculate the CO₂ for the Class 3 GPT-scale worked example (Energy = 401,000 kWh) and state the % reduction from the original Bangladesh figure (249 tonnes).**
> Solution:
> New CO₂ = 401,000 kWh × 0.350 = 140,350 kg = **≈140.35 tonnes CO₂**
> % reduction = (249 − 140.35) / 249 × 100 ≈ **43.6% reduction** in carbon emissions for the identical training run, purely from grid decarbonization.
