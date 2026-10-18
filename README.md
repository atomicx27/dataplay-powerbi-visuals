# Data Play & Carbon Suite — Power BI Custom Visuals (`25 Visuals`)

A comprehensive repository of **25 production-ready, interactive custom visuals for Power BI**, organized into two dedicated suites:
1. **Data Play & Executive Plus Suite ([`visuals/`](visuals/))**: 16 interactive visualizers and executive analytics charts.
2. **Carbon Emission & Telecom Circuit Attribution Suite ([`carbon-visuals/`](carbon-visuals/))**: 9 domain-specific visualizers designed for **Country + Multi-Carrier Carbon Footprint Tracking (`CO2e`)**, **Dynamic Per-Circuit-Type Carbon Attribution (`Ethernet`, `Internet Access`, `MPLS`, `DIA`, etc.)**, **Regional Rollups**, and **Historical Year Timeline Playback** (see the detailed [**Carbon Suite Documentation (`carbon-visuals/README.md`)**](carbon-visuals/README.md)).

All visuals are pre-compiled and packaged as standalone `.pbiviz` files ready to import directly into Power BI Desktop or the Power BI Service. Zero external network requests or dependencies required.

---

## 🌿 1. Carbon Emission & Telecom Circuit Attribution Suite ([`carbon-visuals/`](carbon-visuals/))

> 📖 **Full Deep-Dive Guide:** See [**`carbon-visuals/README.md`**](carbon-visuals/README.md) for the complete mathematical attribution formulas, multi-carrier per country data schema, and visual-by-visual configuration reference.

Designed around the real-world telecom carbon schema where:
- Each row represents a **`Country` + `Carrier` (+ `Year` + `Region`)** record.
- A single **Country** can have **multiple Carriers**, each with its own **Total Carbon Emission (`CO2e`)**.
- **Dynamic Circuit Type Columns** (`Ethernet`, `Internet Access`, `MPLS`, `DIA`, `Dark Fiber`, etc. — up to 100 columns) hold the **active connection count** for each circuit type on that row, and the engine automatically calculates **how much carbon emission each circuit type causes**:
  $$E_{\text{row}, t} = \text{Carbon}_{\text{row}} \times \frac{\text{Connections}_{\text{row}, t}}{\sum_{k} \text{Connections}_{\text{row}, k}}$$

| Visual Name | File | Description & Capabilities |
|---|---|---|
| ⚡ **Carbon: Circuit Attribution Matrix** *(NEW)* | [`carbon-visuals/circuitAttribution.pbiviz`](carbon-visuals/circuitAttribution.pbiviz) | Dedicated per-circuit-type carbon attribution visual with 3 interactive views: **Stacked Attribution Bars**, **Country $\times$ Circuit Heatmap Matrix**, and **Per-Circuit Deep-Dive Cards** showing connection counts and attributed $\text{CO}_2\text{e}$ for every line type. |
| 🏆 **Carbon: Country & Carrier Scorecard** *(NEW)* | [`carbon-visuals/carrierScorecard.pbiviz`](carbon-visuals/carrierScorecard.pbiviz) | Multi-carrier per country benchmark scorecard comparing all carriers operating inside each country with `A+` to `F` carbon efficiency grades, circuit type mix bars, and carrier share percentages. |
| 🗺️ **Carbon: Global Emission Atlas** | [`carbon-visuals/carbonAtlas.pbiviz`](carbon-visuals/carbonAtlas.pbiviz) | Interactive 2D Mercator world map with country emission plumes, **segmented outer Donut Rings** (toggleable between **Circuit Type Attribution** and **Carrier Share**), inter-country shared carrier arcs, HUD legend, and year timeline. |
| 🛰️ **Carbon: Carrier Constellation** | [`carbon-visuals/carrierConstellation.pbiviz`](carbon-visuals/carrierConstellation.pbiviz) | D3 force-directed network with interactive node dragging and two modes: **Bipartite (`Country ↔ Carrier`)** with circuit-ring hubs and **Tripartite (`Country ↔ Carrier ↔ Circuit Type`)**. |
| 🌸 **Carbon: Circuit Flowers** | [`carbon-visuals/circuitFlowers.pbiviz`](carbon-visuals/circuitFlowers.pbiviz) | Botanical glyph matrix grouped by **Country**, **Carrier**, or **Country + Carrier**, where curved leaf petals represent each **Circuit Type** (sized by Attributed $\text{CO}_2\text{e}$ or Connection Count) and the center core encodes carbon intensity. |
| 🪐 **Carbon: Efficiency Orbit** | [`carbon-visuals/efficiencyOrbit.pbiviz`](carbon-visuals/efficiencyOrbit.pbiviz) | Animated quadrant scatter plotting **Active Connections (X)** against **Total Carbon (Y)** by Country, Carrier, Country+Carrier, or Circuit Type, with outer circuit-mix rings, **Global Average Intensity Benchmark Curve**, and historical motion trails. |
| 🎀 **Carbon: Emission Ribbons** | [`carbon-visuals/emissionRibbons.pbiviz`](carbon-visuals/emissionRibbons.pbiviz) | Ranking bump & slope ribbon chart tracking how **Countries**, **Carriers**, **Country+Carrier pairs**, or **Circuit Types** climb or fall in Total Carbon or Carbon Intensity across years (with automatic 3-stage Slopegraph in single-year views). |
| 🌊 **Carbon: Regional Sankey Flow** | [`carbon-visuals/carbonFlow.pbiviz`](carbon-visuals/carbonFlow.pbiviz) | **4-Stage Alluvial Sankey diagram** tracing carbon footprint and connection volume across **`Region → Country → Carrier → Circuit Type`**. |
| 🌍 **Carbon: 3D Emission Globe** | [`carbon-visuals/carbonGlobe3d.pbiviz`](carbon-visuals/carbonGlobe3d.pbiviz) | Holographic 3D WebGL Earth globe with extruded 3D country carbon pillars, color mode switcher (`Carbon/Conn`, `Dominant Circuit`, `Total Carbon`), inter-country carrier flight arcs, and HUD attribution panel. |

---

## 📦 2. Data Play & Executive Plus Visuals ([`visuals/`](visuals/))

| Visual | File | Description & Capabilities |
|---|---|---|
| 📊 **Bar Chart Plus** | [`visuals/barChartPlus.pbiviz`](visuals/barChartPlus.pbiviz) | Multi-mode executive bar chart (Vertical Columns, Horizontal Bars, Lollipop, 80/20 Pareto Curve) with target markers, average lines, dynamic sorting, and full cross-filtering. |
| 📈 **Line & Area Chart Plus** | [`visuals/lineChartPlus.pbiviz`](visuals/lineChartPlus.pbiviz) | Interactive trend visual (Gradient Area, Smooth Spline, Linear, Stepped) with linear regression forecast trendline, target comparison line, and peak callouts. |
| 🍩 **Donut & Pie Chart Plus** | [`visuals/donutChartPlus.pbiviz`](visuals/donutChartPlus.pbiviz) | Proportional composition chart (Donut Ring, Classic Pie, Nightingale Rose, Half-Gauge Arc) with dynamic center KPI callout, slice pull-out, and interactive share legend. |
| 📋 **KPI Scorecard & Matrix Plus** | [`visuals/kpiMatrixPlus.pbiviz`](visuals/kpiMatrixPlus.pbiviz) | Executive KPI summary cards paired with an interactive sortable data matrix featuring inline sparkbars and target/average variance badges. |
| 🫧 **Bubble Physics** | [`visuals/bubblePhysics.pbiviz`](visuals/bubblePhysics.pbiviz) | Force-directed collision physics where bubbles cluster by category. Supports dragging, spring burst animations, and cross-filtering. |
| 🐠 **Data Aquarium (3D)** | [`visuals/dataAquarium.pbiviz`](visuals/dataAquarium.pbiviz) | 3D WebGL boids flocking simulation where data rows act as wandering oceanic creatures reacting to metric currents. |
| 🏙️ **Data City (3D)** | [`visuals/dataCity.pbiviz`](visuals/dataCity.pbiviz) | 3D WebGL metropolis. Categories form districts and data rows become skyscrapers with height/footprint driven by measures. Supports full camera orbit/pan. |
| 🎧 **Data DJ** | [`visuals/dataDJ.pbiviz`](visuals/dataDJ.pbiviz) | Interactive audio-reactive DJ deck translating data rows into synthesized beat patterns, tempos, and frequencies. |
| 🎹 **Data Sonifier** | [`visuals/dataSonifier.pbiviz`](visuals/dataSonifier.pbiviz) | Musical audio synthesizer converting data metrics into melodic sequences, chords, and rhythmic pulses. |
| 🌊 **Fluid Particles** | [`visuals/fluidParticles.pbiviz`](visuals/fluidParticles.pbiviz) | High-speed particle fluid dynamics simulation driven by data density, mass, and velocity turbulence. |
| 🎨 **Generative Art** | [`visuals/generativeArt.pbiviz`](visuals/generativeArt.pbiviz) | Algorithmic generative canvas transforming dataset measures into procedural flow fields, mandalas, and abstract art. |
| 🌐 **Hologram Globe (3D)** | [`visuals/hologramGlobe.pbiviz`](visuals/hologramGlobe.pbiviz) | 3D holographic Earth globe rendering geographical coordinate data pillars, connection arcs, and atmospheric glow. |
| 🕸️ **Network Web** | [`visuals/networkWeb.pbiviz`](visuals/networkWeb.pbiviz) | Force-directed node-link graph built from Source → Target pairs with weighted link widths, node scaling, zoom, pan, and dragging. |
| 🧱 **Physics Blocks** | [`visuals/physicsBlocks.pbiviz`](visuals/physicsBlocks.pbiviz) | 2D Matter.js physics engine dropping data rows as interactive falling blocks with gravity, restitution, and collision. |
| 📉 **Dual-Month Savings Web** | [`visuals/savingsDelta.pbiviz`](visuals/savingsDelta.pbiviz) | Waterfall delta and radial comparison visual comparing last month vs this month savings across category tiers. |
| 🪐 **Solar System (3D)** | [`visuals/solarSystem.pbiviz`](visuals/solarSystem.pbiviz) | 3D orbital gravity model with categories as central suns/planets and individual data points as orbiting satellites. |

---

## ⬇️ Download & Import into Power BI

- **Single visual:** Open any `.pbiviz` file in [`carbon-visuals/`](carbon-visuals/) or [`visuals/`](visuals/) and click **Download raw file**.
- **Clone full repository:**
  ```bash
  git clone https://github.com/atomicx27/dataplay-powerbi-visuals.git
  ```
- **Import into Power BI Desktop / Service:**
  1. In the **Visualizations** pane, click **`…`** (Get more visuals) $\rightarrow$ **Import a visual from a file**.
  2. Select your desired `.pbiviz` file from `carbon-visuals/` or `visuals/`.

---

## 🧩 Carbon Suite Unified Field Mapping

All 9 visuals in [`carbon-visuals/`](carbon-visuals/) share the exact same 6 field wells so you can switch visuals in Power BI without re-binding columns:

| Field Well | Data Type | Purpose |
|---|---|---|
| **Country** | Text (Grouping) | Country name (supports multiple carriers per country; matches 100+ coordinates automatically). |
| **Region** | Text (Grouping) | *(Optional)* Global macro-region (`EMEA`, `Americas`, `APAC`, `MEA` — auto-inferred from Country if omitted). |
| **Carrier** | Text (Grouping) | Telecom carrier / provider name operating in that Country. |
| **Year** | Number / Text | *(Optional)* Historical reporting year — activates the interactive **Play / Scrub Year Timeline**. |
| **Carbon Emission** | Measure | Total carbon footprint ($\text{CO}_2\text{e}$) for that `Country + Carrier` row. |
| **Circuit Types (Multiple Columns)** | Measures (`max: 100`) | Drag **all dynamic circuit connection count columns** (`Ethernet`, `Internet Access`, `MPLS`, `DIA`, `Broadband`, `Dark Fiber`, etc.) into this well. |

---

## 🛠️ Engineering Playbook & Skill

The [`skill/SKILL.md`](skill/SKILL.md) file contains the full engineering playbook for Power BI custom visual development, including the **Carbon Emission & Per-Circuit-Type Attribution Architecture**.

Author: [@atomicx27](https://github.com/atomicx27) · License: MIT
