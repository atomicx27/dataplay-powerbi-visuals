# Data Play & Carbon Suite — Power BI Custom Visuals

A comprehensive repository of **23 production-ready, interactive custom visuals for Power BI**, organized into two dedicated suites:
1. **Data Play & Executive Plus Suite (`visuals/`)**: 16 interactive visualizers and executive analytics charts.
2. **Carbon Emission Suite (`carbon-visuals/`)**: 7 domain-specific visualizers designed for Telecom Circuit Analytics, Carrier Gravity, and Carbon Footprint (CO2e) Tracking.

All visuals are pre-compiled and packaged as standalone `.pbiviz` files ready to import directly into Power BI Desktop or the Power BI Service. Zero external network requests or dependencies required.

---

## 📦 1. Data Play & Executive Plus Visuals (`visuals/`)

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

## 🌿 2. Carbon Emission & Telecom Suite (`carbon-visuals/`)

A specialized suite of 7 visuals designed for **Telecom Circuit Analytics and Carbon Emission Tracking**:

| Visual Name | File | Description & Capabilities |
|---|---|---|
| 🗺️ **Carbon Atlas** | [`carbon-visuals/carbonAtlas.pbiviz`](carbon-visuals/carbonAtlas.pbiviz) | Interactive 2D Mercator world map with country emission plumes, heat color scaling, graticule lines, zoom/pan, and year timeline. |
| 🛰️ **Carrier Constellation** | [`carbon-visuals/carrierConstellation.pbiviz`](carbon-visuals/carrierConstellation.pbiviz) | D3 force-directed network diagram exposing shared telecom carriers operating across countries, weighted by carbon gravity. |
| 🌸 **Circuit Flowers** | [`carbon-visuals/circuitFlowers.pbiviz`](carbon-visuals/circuitFlowers.pbiviz) | Multi-dimensional glyph matrix displaying circuit type compositions (petals) and carbon intensity efficiency per country/carrier. |
| 🪐 **Efficiency Orbit** | [`carbon-visuals/efficiencyOrbit.pbiviz`](carbon-visuals/efficiencyOrbit.pbiviz) | Animated quadrant scatter orbit plotting Circuit Count (X) against Carbon (Y) with historical motion trails over time. |
| 🎀 **Emission Ribbons** | [`carbon-visuals/emissionRibbons.pbiviz`](carbon-visuals/emissionRibbons.pbiviz) | Year-over-year bump ribbon chart tracking how country carbon emission rankings climb or fall over historical periods. |
| 🌊 **Carbon Flow** | [`carbon-visuals/carbonFlow.pbiviz`](carbon-visuals/carbonFlow.pbiviz) | Alluvial Sankey flow diagram breaking down carbon footprints through Region → Country → Telecom Carrier. |
| 🌍 **Carbon Globe 3D** | [`carbon-visuals/carbonGlobe3d.pbiviz`](carbon-visuals/carbonGlobe3d.pbiviz) | Holographic 3D WebGL Earth globe with extruded carbon pillars, auto-rotation, and inter-country carrier mesh connection arcs. |

---

## ⬇️ Download

- **Single visual:** Open any `.pbiviz` file in [`visuals/`](visuals/) or [`carbon-visuals/`](carbon-visuals/) and click **Download raw file**.
- **Clone full repository:**
  ```bash
  git clone https://github.com/atomicx27/dataplay-powerbi-visuals.git
  ```
- **Download via GitHub CLI:**
  ```bash
  gh release download --repo atomicx27/dataplay-powerbi-visuals --pattern "*.pbiviz"
  ```

---

## 📥 Import into Power BI

### Power BI Desktop
1. In the **Visualizations** pane, click the **`…`** (Get more visuals) icon.
2. Select **Import a visual from a file**.
3. Pick your desired `.pbiviz` file (from `visuals/` or `carbon-visuals/`) and accept the prompt.
4. The visual icon will appear in the Visualizations pane. Click it to add it to your report canvas.

### Power BI Service (app.powerbi.com)
1. Open a report in **Edit** mode.
2. Under the **Visualizations** pane, click **`…` > Import a visual from a file**.
3. Upload the `.pbiviz` file.

> [!NOTE]
> If your tenant restricts third-party visuals, an administrator can allow them under **Admin portal → Tenant settings → Allow visuals created using the Power BI SDK**, or deploy them to your **Organizational visuals** repository.

---

## 🧩 Field Wells & Usage Highlights

### 🌿 Carbon Suite Standard Data Role Mappings
| Field Well | Data Type | Purpose |
|---|---|---|
| **Country** | Text (Grouping) | Matches world coordinates and country boundaries automatically. |
| **Region** | Text (Grouping) | Global region (EMEA, Americas, APAC, MEA). |
| **Carrier** | Text (Grouping) | Telecom carrier / supplier name. |
| **Year** | Number / Date | Historical reporting year. Triggers the interactive **Play / Scrub Timeline**. |
| **Carbon Emission** | Measure | Total carbon footprint metric (e.g. CO2e kg/tonnes). |
| **Circuit Types** | Measures | Multiple columns (`Ethernet`, `MPLS`, `DIA`, `Broadband`). |

### 🫧 Bubble Physics
| Field Well | Type | Purpose |
|---|---|---|
| **Color group** | Category | Assigns cluster color and grouping forces. |
| **Details** | Category (up to 3) | Generates unique individual bubbles. |
| **Bubble size** | Measure | Sets bubble radius (square-root scaled). |
| **Tooltips** | Measures | Metrics displayed on hover. |

### 🏙️ Data City (3D)
| Field Well | Type | Purpose |
|---|---|---|
| **District (Zone)** | Category | Groups buildings into distinct district grids. |
| **Building height** | Measure | Normalised skyscraper elevation. |
| **Building width** | Measure | Footprint width of the building. |
| **Tooltips** | Measures | Hover detail cards. |

### 🕸️ Network Web
| Field Well | Type | Purpose |
|---|---|---|
| **Source node** | Category | Origin node of the connection. |
| **Target node** | Category | Destination node of the connection. |
| **Link weight** | Measure | Link thickness and connection strength. |
| **Tooltips** | Measures | Metrics on link or node hover. |

---

## 💡 Interaction Cheatsheet

| Action | Result |
|---|---|
| **Left Click** | Selects data point and cross-filters all other visuals on page. |
| **Ctrl / Shift + Click** | Multi-selects items. |
| **Legend Click** | Selects all data points in the selected category. |
| **Timeline Play/Scrub** | Steps through historical years (Carbon Suite visuals). |
| **Canvas Empty Click** | Clears active selection. |
| **Right Click** | Opens native Power BI context menu (drill-through, include/exclude). |
| **Mouse Drag** | Moves bubbles/nodes or orbits/pans 3D WebGL scenes. |

---

## 🛠️ Engineering Playbook & Skill

The [`skill/SKILL.md`](skill/SKILL.md) file contains the full engineering playbook for Power BI custom visual development:
- Complete toolchain and dependency matrix (`powerbi-visuals-api 5.11.1`, TypeScript, D3, Three.js).
- Monorepo architectural patterns with shared capability schemas, palettes, and data parsers.
- WebGL lifecycle, context disposal, and sandboxed memory management.
- Complete catalog of sandbox certification rules and debugging fixes.
- Domain playbooks for Telecom & Carbon analytics.

To use the skill with an AI coding assistant, copy `skill/SKILL.md` into your agent skills path (e.g. `~/.gemini/config/skills/powerbi-custom-visuals/`).

---

## 📄 Technical Specifications

- **Power BI Visuals API:** `5.11.1`
- **Packaging CLI:** `powerbi-visuals-tools` (`pbiviz`) `7.2.1`
- **Engines:** D3 v7, Three.js r170, Matter.js
- **Privacy & Security:** 100% offline runtime sandbox (`"privileges": []`), zero external CDN/network calls, certified deterministic PRNG (`createRng`).

Author: [@atomicx27](https://github.com/atomicx27) · License: MIT
