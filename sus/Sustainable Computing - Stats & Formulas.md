---
tags: [sustainability, computing, statistics, formulas, HUM3131, carbon-math, CSE]
course: "HUM 3131 – Environment and Sustainability"
source: "Class 1–3 — Sustainable Computing (statistical data & formula reference)"
aliases: [Sustainable Computing Stats, Carbon Formula Sheet, AI Carbon Math]
---

# Sustainable Computing — Statistical Data & Formula Reference

> Companion to [[Sustainable Computing (Classes 1-3)]] — this note isolates **every numeric data point** and **every formula** from the slides, with extra worked demo problems and solutions for practice.

---

## Part A — Statistical Data (All Numbers from the Slides)

### A1. ICT Sector — Global Scale
| Statistic | Value | Source/Context |
|---|---|---|
| ICT share of global CO₂ | **3–4%** | Freitag et al. (2021) |
| ICT vs. aviation emissions | ICT **> aviation** | ICT surpasses the airline industry |
| ICT global rank as energy consumer (if it were a country) | **#5** | — |
| Projected ICT share of global emissions by 2030 | **8–10%** | — |

### A2. Global ICT Energy Footprint (by component)
| Component | Share of ICT Energy | Detail |
|---|---|---|
| End-user devices | **~55%** | Smartphones, laptops, tablets, TVs, IoT |
| Data centers | **~30%** | Servers, cooling, storage, networking (24/7) |
| Networks | **~15%** | Mobile towers, fiber, routers |
| 5G vs 4G energy per tower | **2–3× more** | 5G towers use more energy than 4G |

### A3. AI / Cloud / Data Center Carbon Benchmarks
| Metric | Value |
|---|---|
| Training GPT-3 (CO₂-equivalent) | **≈700,000 km of driving** |
| Training one large LLM | **284 tonnes CO₂** (Strubell et al., 2019) |
| Daily AI inference energy vs. training energy | **Now bigger than training** |
| 1 hyperscale data center electricity use | **≈ electricity of 1 million homes** |
| Ireland (2024): data centers' share of national electricity | **18%** |
| USA (2030 projection): data centers' share of electricity | **10%** |
| Cooling overhead in data centers | **40–50%** of total data center energy |
| GPU power draw range (training-class GPUs) | **300–700 W/unit** |
| A100 GPU peak power (used in examples) | **400 W** |
| Big tech emissions that are Scope 3 | **70–90%** |

### A4. Embodied Emissions (Manufacturing/Before-Use)
| Item | Embodied CO₂ |
|---|---|
| Smartphone (total, mining→shipping) | **70–90 kg CO₂** |
| One TSMC chip wafer | **≈800 kg CO₂** |

### A5. Operational Emissions (Per Activity, During Use)
| Activity | CO₂ Emitted |
|---|---|
| 1 hour Netflix streaming | **36–100 g CO₂** |
| 1 hour Zoom call | **150–1,000 g CO₂** |
| 1 email with attachment | **~10 g CO₂** |
| 1 Google search | **0.2–0.7 g CO₂** |
| 1 AI query (vs. Google search) | **≈10× the energy of a Google search** |
| 1 full smartphone charge | **≈0.01 kWh** |

### A6. Manufacturing vs. Use-Phase Emissions Split (by device type)
| Device | Manufacturing % | Use Phase % |
|---|---|---|
| Smartphone | **80%** | 20% |
| Laptop | **75%** | 25% |
| Desktop PC | **60%** | 40% |
| Server | **50%** | 50% |
| Large Data Center | **35%** | **65%** |

### A7. Carbon Intensity (CI) of Electricity Grids — by Country/Region
| Location | CI (kgCO₂/kWh) | Grid Type | Class 2 value | Class 3 value |
|---|---|---|---|---|
| Iceland | 0.018 | 100% geothermal/hydro | — | 0.018 (lowest) |
| Norway | 0.018 | Hydropower dominant | 0.018 | — |
| France | 0.058–0.060 | 80% nuclear | 0.058–0.060 | 0.060 (low) |
| EU Average | 0.255 | Mixed + renewables | 0.255 | 0.255 (medium) |
| California | 0.210 | Mixed renewables + gas | 0.210 | — |
| US Average | 0.386 | Gas, coal, nuclear mix | 0.386 | — |
| Bangladesh | 0.620 | Natural gas dominant | 0.620 | 0.620 (highest) |
| India | 0.710 | Coal dominant | 0.710 | 0.710 (high) |
| Poland | 0.700 | Coal dominant | 0.700 | — |

> Note: minor value differences between Class 2 and Class 3 slides (e.g., France 0.058 vs 0.060) reflect slightly different source snapshots — use whichever the question specifies.

### A8. Sustainable AI Technique Impact
| Technique | Carbon/Efficiency Impact |
|---|---|
| Pruning | **5× lower compute** |
| Quantization (FP32→INT8/INT4) | **4–8× smaller model** |
| Knowledge Distillation | **60–80% size, ~97% accuracy retained** |
| Carbon-Aware Scheduling | **Up to 34× less carbon** |
| Fine-Tuning vs. Training from scratch | **100× less energy** |
| Edge Inference | **Eliminates network energy** entirely |
| Efficient Cooling (PUE optimization) | **30–40% facility energy savings** |

### A9. PUE (Power Usage Effectiveness) Benchmarks
| Rating | PUE Value |
|---|---|
| World-class | **1.1** |
| Average | **1.5** |
| Poor | **2.0+** |
| Typical hyperscale (Class 3) | **1.2** |

### A10. LCA & Materials — E-Waste Statistics
| Statistic | Value |
|---|---|
| Smartphone lifetime carbon produced before first use | **60–80%** |
| Global e-waste generated annually | **53.6 million tonnes (2019, UN)** / **62 million tonnes** (Class 3, current) |
| Formally recycled share of e-waste | **only 17%** |
| Rare earth element recovery rate from e-waste | **< 1% globally** |
| Elements found in a single tonne of e-waste | **~70 elements** |
| Elements found in a smartphone | **70+ elements** |
| Cobalt supply from DR Congo | **~70% of global supply** |
| Silicon purity required for semiconductors | **99.9999999%** |
| Air freight vs. sea freight CO₂ | **50× more CO₂** |
| Packaging's share of embodied carbon | **2–5%** |
| Design decisions (Stage 1–2) lock in what % of total impact | **80%** |
| Lithium demand growth (with EV growth) | **30% year-on-year** |
| Framework laptop iFixit repairability score | **10/10** |

---

## Part B — Mathematical Formulas (with Demo Examples & Solutions)

### Formula 1 — AI Carbon Footprint (Single-Step / Conceptual)

$$
CO_2 = Energy\ (kWh) \times PUE \times Carbon\ Intensity\ (kgCO_2/kWh)
$$

Where **Energy = Power (kW) × Time (hours)**.

**🔍 Symbol Legend**
| Symbol | What it literally is | Unit | Plain-English meaning |
|---|---|---|---|
| **CO₂** | Total carbon emitted | kg or tonnes | The final answer — the pollution caused by this computation |
| **Energy** | Electricity actually consumed | kWh | = Power × Time. A 1 kW device run for 5 hrs = 5 kWh |
| **PUE** | Power Usage Effectiveness | ratio, no unit | The "tax" the data center adds for cooling/lighting/overhead. PUE=1.5 means the building uses 50% more power than the servers alone need |
| **CI** (Carbon Intensity) | How dirty the electricity grid is | kgCO₂/kWh | Coal-heavy country = high number (~0.6–0.7); hydro/nuclear country = low number (~0.02–0.06) |

**Demo Example B1.1**
> A single GPU draws 245 W and runs for 8 hours in a data center with PUE = 1.6, on the Bangladesh grid (CI = 0.620 kg/kWh). Find total CO₂.

**Solution:**
1. Energy = 0.245 kW × 8 hrs = **1.96 kWh**
2. Effective energy = 1.96 × 1.6 (PUE) = **3.14 kWh**
3. CO₂ = 3.14 × 0.620 = **1.95 kg CO₂**

**Demo Example B1.2 (New)**
> A data center runs 100 servers at 250 W each for 10 hours, PUE = 1.5, on the US Average grid (CI = 0.386 kg/kWh). Find total CO₂.

**Solution:**
1. Total power = 100 × 250 W = 25,000 W = **25 kW**
2. Energy = 25 kW × 10 hrs = **250 kWh**
3. Effective energy = 250 × 1.5 = **375 kWh**
4. CO₂ = 375 × 0.386 = **≈144.75 kg CO₂**

---

### Formula 2 — AI Carbon Equation (Two-Step Version)

**Step 1 — Energy:**
$$
E = \frac{H \times P \times PUE}{1000}
$$
(H = hours, P = GPU power in Watts, result in kWh)

**Step 2 — Carbon:**
$$
C = E \times CI
$$

**🔍 Symbol Legend**
| Symbol | What it literally is | Unit | Plain-English meaning |
|---|---|---|---|
| **H** | Hours | hours | How long the job ran for |
| **P** | GPU power draw | Watts (W) | Power rating of ONE GPU (e.g. A100 = 400W). If using multiple GPUs, multiply P by GPU count first |
| **PUE** | Power Usage Effectiveness | ratio | Same overhead "tax" as Formula 1 |
| **/1000** | Unit conversion | — | Just converts Watt-hours into kilowatt-hours (kWh) — not a real physical quantity, just math housekeeping |
| **E** | Energy | kWh | Result of Step 1 — the actual electricity used, overhead included |
| **CI** | Carbon Intensity | kgCO₂/kWh | Same as Formula 1 — grid dirtiness |
| **C** | Carbon (final answer) | kg | Total CO₂ emitted by the job |

**Demo Example B2.1 (from slides — GPT-scale training)**
> 1,024 × A100 GPUs, 400 W each, PUE = 1.2, training duration = 34 days. Find energy and CO₂ on the Bangladesh, EU, France, and Iceland grids.

**Solution:**
1. Hours = 34 × 24 = **816 hours**
2. E = 816 × (1,024 × 400) × 1.2 / 1000 = 816 × 409,600 × 1.2 / 1000 ≈ **401,000 kWh**
3. Bangladesh: 401,000 × 0.620 = **≈248,620 kg = 249 tonnes CO₂**
4. EU Average: 401,000 × 0.255 = **≈102,255 kg = 102 tonnes CO₂**
5. France: 401,000 × 0.060 = **≈24,060 kg = 24 tonnes CO₂**
6. Iceland: 401,000 × 0.018 = **≈7,218 kg = 7.2 tonnes CO₂**

**Demo Example B2.2 (slide student exercise, solved)**
> 4 × A100 GPUs, 400 W each, 80% utilization, 72 hours, PUE = 1.4. Find CO₂ in Bangladesh (0.620) and France (0.058), and CO₂ saved.

**Solution:**
1. Effective power per GPU = 400 W × 0.80 = 320 W; total = 4 × 320 W = 1,280 W = 1.28 kW
2. E = 1.28 kW × 72 hrs × 1.4 (PUE) = **129.02 kWh**
3. Bangladesh: 129.02 × 0.620 = **≈80.0 kg CO₂**
4. France: 129.02 × 0.058 = **≈7.48 kg CO₂**
5. CO₂ saved = 80.0 − 7.48 = **≈72.5 kg CO₂** (**≈10.7× reduction**, ~90.6% less)

**Demo Example B2.3 (New)**
> A lab trains a model for 10 days on 8 × H100 GPUs (700 W each), PUE = 1.25. Compare CO₂ in Poland (0.700) vs. Iceland (0.018).

**Solution:**
1. Hours = 10 × 24 = **240 hours**
2. E = 240 × (8 × 700) × 1.25 / 1000 = 240 × 5,600 × 1.25 / 1000 = **1,680 kWh**
3. Poland: 1,680 × 0.700 = **1,176 kg CO₂ (1.176 tonnes)**
4. Iceland: 1,680 × 0.018 = **30.24 kg CO₂**
5. Reduction factor = 1,176 / 30.24 ≈ **38.9× less carbon in Iceland**

---

### Formula 3 — Power Usage Effectiveness (PUE)

$$
PUE = \frac{Total\ Facility\ Power}{IT\ Equipment\ Power}
$$

**🔍 Symbol Legend**
| Symbol | What it literally is | Plain-English meaning |
|---|---|---|
| **Total Facility Power** | ALL electricity the building draws | Servers + cooling + lighting + everything, combined |
| **IT Equipment Power** | Electricity used by computers only | Just the servers/GPUs doing the actual work |
| **PUE** | The ratio of the two | PUE = 1.0 would mean zero overhead (impossible in practice). PUE = 2.0 means the building wastes as much power on cooling/overhead as the computers themselves use |

**Demo Example B3.1**
> A data center's total facility power is 220 kW; IT equipment power is 160 kW. Find PUE and classify it.

**Solution:**
1. PUE = 220 / 160 = **1.375**
2. Classification: between world-class (1.1) and average (1.5) — moderately efficient, room for improvement.

**Demo Example B3.2 (New)**
> A poorly cooled facility has Total Facility Power = 300 kW and IT Equipment Power = 130 kW. Find PUE and classify it.

**Solution:**
1. PUE = 300 / 130 ≈ **2.31**
2. Classification: **poor** (above the 2.0+ "poor" threshold) — indicates highly inefficient cooling/overhead.

---

### Formula 4 — Reduction Factor / Comparative Carbon Savings

$$
\text{Reduction Factor} = \frac{CO_2\ \text{(high-carbon location)}}{CO_2\ \text{(low-carbon location)}}
$$
$$
\text{\% Reduction} = \frac{CO_2\ \text{(high)} - CO_2\ \text{(low)}}{CO_2\ \text{(high)}} \times 100
$$

**🔍 Symbol Legend**
| Symbol | What it literally is | Plain-English meaning |
|---|---|---|
| **CO₂ (high)** | Carbon from the dirty/worse scenario | e.g. running the job in Bangladesh (coal-heavy grid) |
| **CO₂ (low)** | Carbon from the clean/better scenario | e.g. running the same job in Iceland (hydro/geothermal) |
| **Reduction Factor** | "How many times more" | e.g. 34× means the dirty option pollutes 34 times as much |
| **% Reduction** | Same comparison, as a percentage | e.g. "switching locations cut emissions by 90%" |

**Demo Example B4.1**
> Using Example B2.1 results: Bangladesh = 249 tonnes, Iceland = 7.2 tonnes. Find reduction factor and % reduction.

**Solution:**
1. Reduction factor = 249 / 7.2 ≈ **34.6×**
2. % reduction = (249 − 7.2) / 249 × 100 ≈ **97.1%**

**Demo Example B4.2 (New — grid decarbonization scenario)**
> If Bangladesh's grid CI drops from 0.620 to a projected 0.350 kg/kWh (higher renewable share), recompute CO₂ for the GPT-scale run (E = 401,000 kWh) and find the % reduction from the original 249-tonne figure.

**Solution:**
1. New CO₂ = 401,000 × 0.350 = 140,350 kg ≈ **140.35 tonnes**
2. % reduction = (249 − 140.35) / 249 × 100 ≈ **43.6% reduction**

---

### Formula 5 — Applying Technique Multipliers (Pruning, Quantization, Distillation)

$$
E_{\text{optimized}} = \frac{E_{\text{original}}}{\text{Technique Multiplier}}
$$

**🔍 Symbol Legend**
| Symbol | What it literally is | Plain-English meaning |
|---|---|---|
| **E_original** | Energy used BEFORE optimization | The normal, unoptimized energy cost |
| **Technique Multiplier** | How much smaller the technique makes it | e.g. pruning = divide by 5; quantization = divide by 4–8 |
| **E_optimized** | Energy used AFTER optimization | What you actually pay for (in energy/carbon) once the trick is applied |

**Demo Example B5.1 (New — Pruning)**
> An original training run uses 8 GPUs at 500 W each, for 48 hours, PUE = 1.3, on the France grid (CI = 0.058). Pruning gives a 5× compute (and energy) reduction. Find CO₂ before and after pruning.

**Solution:**
1. Original E = 48 × (8 × 500) × 1.3 / 1000 = 48 × 4,000 × 1.3 / 1000 = **249.6 kWh**
2. Original CO₂ = 249.6 × 0.058 = **≈14.48 kg CO₂**
3. Pruned E = 249.6 / 5 = **49.92 kWh**
4. Pruned CO₂ = 49.92 × 0.058 = **≈2.90 kg CO₂**
5. CO₂ saved ≈ **11.58 kg (≈80% reduction)** — consistent with "5× lower compute."

**Demo Example B5.2 (New — Quantization on inference workload)**
> A deployed model serves 1,000,000 queries/day, each costing 0.0002 kWh (FP32). Quantizing to INT8 cuts per-query energy by 4×. Running on the India grid (CI = 0.710), find daily CO₂ before and after quantization.

**Solution:**
1. Original daily energy = 1,000,000 × 0.0002 = **200 kWh**
2. Original daily CO₂ = 200 × 0.710 = **142 kg CO₂/day**
3. Quantized daily energy = 200 / 4 = **50 kWh**
4. Quantized daily CO₂ = 50 × 0.710 = **35.5 kg CO₂/day**
5. Daily saving = 142 − 35.5 = **106.5 kg CO₂/day saved (75% reduction)**

---

### Formula 6 — Operational Carbon Over Device Lifetime (Charging Example)

$$
CO_2 = (\text{Energy per charge}) \times (\text{charges over period}) \times CI
$$

**🔍 Symbol Legend**
| Symbol | What it literally is | Plain-English meaning |
|---|---|---|
| **Energy per charge** | kWh used in ONE charge cycle | e.g. 0.01 kWh for a phone |
| **charges over period** | Total number of charges | days × charges-per-day |
| **CI** | Carbon Intensity of the grid | Same meaning as every other formula |
| **CO₂ (result)** | Total lifetime "use-phase" carbon | Compare this against the one-time manufacturing/embodied CO₂ to see which one actually dominates |

**Demo Example B6.1**
> A phone uses 0.01 kWh per charge, charged once daily, over 3 years (1,095 days), on the EU grid (CI = 0.255). Find total charging CO₂ and compare to embodied manufacturing CO₂ (70–90 kg).

**Solution:**
1. Total energy = 0.01 × 1,095 = **10.95 kWh**
2. CO₂ = 10.95 × 0.255 = **≈2.79 kg CO₂** over 3 years
3. Comparison: charging CO₂ (~2.79 kg) is roughly **25–32× smaller** than embodied manufacturing CO₂ (70–90 kg) — confirming manufacturing dominates smartphone lifetime emissions (matches the 80%/20% split in Table A6).

**Demo Example B6.2 (New)**
> Same phone, but charged **twice daily** (heavier use) over 2 years (730 days). Find total charging CO₂.

**Solution:**
1. Total energy = 0.01 × 2 × 730 = **14.6 kWh**
2. CO₂ = 14.6 × 0.255 = **≈3.72 kg CO₂** over 2 years — still far smaller than embodied emissions, reinforcing that even doubled usage frequency doesn't change the manufacturing-dominant conclusion.

---

## 🔑 Formula Cheat-Sheet (All-in-One)

| # | Formula | Use Case |
|---|---|---|
| 1 | `Energy (kWh) = Power (kW) × Time (hrs)` | Base energy from power draw |
| 2 | `E = H × P(W) × PUE / 1000` | Energy including data-center overhead, power in Watts |
| 3 | `CO₂ (kg) = Energy (kWh) × CI (kgCO₂/kWh)` | Carbon from energy + grid intensity |
| 4 | `CO₂ = Energy × PUE × CI` | Single-step combined formula |
| 5 | `PUE = Total Facility Power / IT Equipment Power` | Data center efficiency rating |
| 6 | `Reduction Factor = CO₂(high) / CO₂(low)` | Comparing two locations/scenarios |
| 7 | `% Reduction = (CO₂_high − CO₂_low)/CO₂_high × 100` | Percentage carbon savings |
| 8 | `E_optimized = E_original / technique multiplier` | Applying pruning/quantization/distillation gains |

**Remember the 3 controllable levers (any formula):**
1. **Algorithm efficiency** → lowers Energy/FLOPs
2. **Data center quality** → lowers PUE
3. **Grid choice (location/time)** → lowers Carbon Intensity (CI)
