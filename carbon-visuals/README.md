# Carbon Emission & Telecom Circuit Attribution Visuals for Power BI (`v2.0`)

A suite of **9 enterprise Power BI custom visuals (`.pbiviz`)** purpose-built for **Telecom Network Carbon Footprint Analysis**, **Multi-Carrier per Country Benchmarking**, and **Dynamic Per-Circuit-Type Carbon Emission Attribution**.

---

## 1. Domain & Data Model Architecture

### How Your Telecom Carbon Dataset Works

In enterprise telecom sustainability reporting, your dataset is structured at the **`Country` + `Carrier` (+ `Year` + `Region`)** level:

1. **Country (`country`)**: The country where network connections terminate or operate (e.g., `Germany`, `United States`, `United Kingdom`, `Japan`, `Singapore`).
2. **Multiple Carriers per Country (`carrier`)**: Within a single country, **multiple telecom carriers** can operate simultaneously (e.g., inside `Germany`, you may have rows for `Deutsche Telekom`, `Vodafone`, `Colt`, and `Orange`). Each `Country + Carrier` combination has its own **Total Carbon Emission** value.
3. **Total Carbon Emission (`carbon`)**: The total reported carbon footprint ($\text{CO}_2\text{e}$) for that `Country + Carrier` in a given `Year`.
4. **Dynamic Circuit Type Connection Count Columns (`circuitTypes`)**:
   - Your table contains **wide-format columns representing each type of telecom line / circuit in use** (for example: `Ethernet`, `Internet Access`, `MPLS`, `Dedicated Internet Access (DIA)`, `Dark Fiber`, `Wavelength`, `Broadband`, `SD-WAN`, `LTE/5G`, etc.).
   - The numeric value in each circuit column is the **number of active connections** of that circuit type for that `Country + Carrier` row.
   - *Example*: If a row for `Germany · Deutsche Telekom` has `Ethernet = 3` and `Internet Access = 2`, there are **$3 + 2 = 5$ total connections** in that `Country + Carrier`, and the `Carbon Emission` column holds the **total emission across all 5 connections**.
   - **Unlimited / Dynamic Circuit Columns**: You do not need a fixed number or fixed names of circuit columns. You can drag **any number of circuit type columns (up to 100 columns)** into the **`Circuit Types (Multiple Columns)`** field well, and every visual automatically discovers their names, strips Power BI aggregation prefixes (`"Sum of Ethernet"` $\rightarrow$ `"Ethernet"`), and computes per-circuit-type attributed emissions.
5. **Region (`region`)**: Geographic macro-region (e.g., `EMEA`, `Americas`, `APAC`, `MEA`). If you do not map a `Region` column (or if any row is blank), the shared engine **automatically infers the Region** from its built-in 100+ country geographic dictionary.
6. **Year (`year`)**: Reporting year (e.g., `2022`, `2023`, `2024`, `2025`). When multiple years are present, visuals automatically display an interactive **Timeline Playback Bar** (`Play / Pause` + year pills) at the bottom of the canvas.

---

### Mathematical Model: Per-Circuit-Type Carbon Attribution

Because the dataset provides **Total Carbon Emission ($\text{Carbon}_i$)** per `Country + Carrier` row $i$ alongside the **Connection Counts ($c_{i, t}$)** for each dynamic circuit type $t \in \mathcal{T}$, the shared data engine (`shared/src/data.ts`) performs exact row-level and aggregate attribution:

1. **Row Total Active Connections ($N_i$)**:
   $$N_i = \sum_{t \in \mathcal{T}} c_{i, t}$$

2. **Row Carbon Intensity per Connection ($I_i$)**:
   $$I_i = \begin{cases} \dfrac{\text{Carbon}_i}{N_i} & \text{if } N_i > 0 \\[6pt] 0 & \text{if } N_i = 0 \end{cases}$$

3. **Attributed Carbon Emission for Circuit Type $t$ on Row $i$ ($E_{i, t}$)**:
   $$E_{i, t} = \text{Carbon}_i \times \frac{c_{i, t}}{N_i}$$
   *Example*: On a row with $\text{Carbon}_i = 500\text{ CO}_2\text{e}$, `Ethernet` $= 3$, and `Internet Access` $= 2$:
   - Total connections $N_i = 3 + 2 = 5$
   - Carbon per connection $I_i = 500 / 5 = 100\text{ CO}_2\text{e / conn}$
   - **Ethernet Attributed Emission**: $500 \times (3 / 5) = \mathbf{300\text{ CO}_2\text{e}}$ ($60\%$)
   - **Internet Access Attributed Emission**: $500 \times (2 / 5) = \mathbf{200\text{ CO}_2\text{e}}$ ($40\%$)

4. **Aggregate Circuit Type Footprint & Intensity Across Any Scope $S$ (Country, Carrier, Region, or Global)**:
   $$C_{S, t} = \sum_{i \in S} c_{i, t}, \qquad E_{S, t} = \sum_{i \in S} E_{i, t}, \qquad \bar{I}_{S, t} = \frac{E_{S, t}}{C_{S, t}}$$

---

## 2. Complete Catalog of the 9 Carbon Visuals

| # | Package File (`.pbiviz`) | Visual Name in Power BI | Primary Analytical Use Case |
| :- | :--- | :--- | :--- |
| 1 | [`circuitAttribution.pbiviz`](./circuitAttribution.pbiviz) | **Carbon: Circuit Attribution Matrix** | **(NEW)** Dedicated circuit attribution engine with 3 views: **Stacked Attribution Bars**, **Country $\times$ Circuit Type Heatmap Matrix**, and **Per-Circuit Deep-Dive Cards** showing exact connection counts and attributed $\text{CO}_2\text{e}$ per line type. |
| 2 | [`carrierScorecard.pbiviz`](./carrierScorecard.pbiviz) | **Carbon: Country & Carrier Scorecard** | **(NEW)** Hierarchical **Country $\rightarrow$ Multi-Carrier Scorecard** benchmarking multiple carriers inside each country with `A+` to `F` efficiency grades, circuit mix bars, and carrier share comparison. |
| 3 | [`carbonAtlas.pbiviz`](./carbonAtlas.pbiviz) | **Carbon: Global Emission Atlas** | Interactive 2D world map with heat-colored country emission plumes, **segmented outer Donut Rings** (toggleable between **Circuit Type Attribution** and **Carrier Share**), inter-country shared carrier arcs, and HUD legend. |
| 4 | [`carrierConstellation.pbiviz`](./carrierConstellation.pbiviz) | **Carbon: Carrier Constellation** | Force-directed network with interactive node dragging and two modes: **Bipartite (`Country ↔ Carrier`)** with circuit-ring hubs and **Tripartite (`Country ↔ Carrier ↔ Circuit Type`)**. |
| 5 | [`circuitFlowers.pbiviz`](./circuitFlowers.pbiviz) | **Carbon: Circuit Flowers** | Organic botanical glyph matrix where each flower represents a **Country**, **Carrier**, or **Country + Carrier** pair, curved leaf petals encode each **Circuit Type** (sized by Attributed $\text{CO}_2\text{e}$ or Connection Count), and the core encodes carbon intensity. |
| 6 | [`efficiencyOrbit.pbiviz`](./efficiencyOrbit.pbiviz) | **Carbon: Efficiency Orbit** | Gapminder-style quadrant scatter chart plotting **Active Connections (X)** vs. **Total Carbon (Y)** by Country, Carrier, Country+Carrier, or Circuit Type, complete with outer circuit-mix rings, a **Global Average Intensity Benchmark Curve**, and historical motion trails. |
| 7 | [`emissionRibbons.pbiviz`](./emissionRibbons.pbiviz) | **Carbon: Emission Ribbons** | Ranking bump & slope chart tracking how **Countries**, **Carriers**, **Country+Carrier pairs**, or **Circuit Types** climb or fall in Total Carbon or Carbon Intensity across years (with automatic 3-stage Slopegraph fallback when viewing a single year). |
| 8 | [`carbonFlow.pbiviz`](./carbonFlow.pbiviz) | **Carbon: Regional Sankey Flow** | **4-Stage Alluvial Sankey Diagram** tracing carbon emissions and connection counts from **`Region → Country → Carrier → Circuit Type`** with properly stacked non-overlapping ribbons. |
| 9 | [`carbonGlobe3d.pbiviz`](./carbonGlobe3d.pbiviz) | **Carbon: 3D Emission Globe** | Hardware-accelerated **Three.js 3D Holographic Earth** with extruded 3D country carbon pillars (scaled by carrier density), color mode switching (`Carbon/Conn`, `Dominant Circuit`, `Total Carbon`), 3D bezier carrier flight arcs, and HUD attribution panel. |

---

## 3. Detailed Guide to Each Visual

### 3.1 `circuitAttribution.pbiviz` — Carbon: Circuit Attribution Matrix *(NEW)*
Specifically designed to answer: *"How much carbon emission is caused by each circuit type (e.g., 3 Ethernet connections vs. 2 Internet Access connections) across Countries, Carriers, and Regions?"*
- **Top Global Circuit Summary Ribbon**: Displays a KPI card for every dynamic Circuit Type in your dataset showing its color swatch, **Share of Total Carbon (`%`)**, **Total Attributed Carbon (`CO2e`)**, **Total Active Connections (`N connections`)**, and **Average Carbon per Connection (`CO2e/conn`)**.
- **Interactive View Modes** (switchable in the header bar):
  1. **`Stacked Attribution`**: Horizontal stacked bars where each bar's length is the entity's Total Carbon and its color-coded segments show the exact carbon attributed to each Circuit Type (`Ethernet (3)`, `Internet Access (2)`, etc.).
  2. **`Circuit Heatmap Matrix`**: A full 2D tabular matrix where rows are `Country · Carrier` (or `Country`, `Carrier`, `Region`) and columns are every dynamic `Circuit Type`, displaying both the **Attributed Carbon (`CO2e`)** and **Connection Count (`N conn`)** inside every heat-shaded cell.
  3. **`Per-Circuit Deep Dive`**: Dedicated analytical cards for each Circuit Type listing its top 5 `Country · Carrier` contributors with inline progress bars.
- **Interactive Grouping Modes**: Switch dynamically between `Country + Carrier`, `By Country`, `By Carrier`, and `By Region`.

### 3.2 `carrierScorecard.pbiviz` — Carbon: Country & Carrier Scorecard *(NEW)*
Specifically designed for datasets where **each Country has multiple Carriers**, allowing side-by-side carrier benchmarking within the same country.
- **Efficiency Grading (`A+` to `F`)**: Compares each Country's and each Carrier's carbon per connection (`CO2e/conn`) against the global dataset average:
  - `A+` ($\le 0.65\times$ global avg), `A` ($\le 0.90\times$), `B` ($\le 1.15\times$), `C` ($\le 1.45\times$), `D` ($\le 1.85\times$), `F` ($> 1.85\times$).
- **Interactive View Modes**:
  1. **`Country → Multi-Carrier`**: Renders a card for each Country showing its Region badge, Total Carbon, Total Connections, Efficiency Grade, Circuit Type Mix Bar, and a nested list of **all Carriers operating inside that Country** with their individual connection counts, top circuit types, share of country carbon (`%`), and per-connection intensity.
  2. **`Carrier Leaderboard`**: Flips the hierarchy to rank **Carriers globally** and list the **Countries** served under each Carrier.
  3. **`Regional Rollup`**: Aggregates by **Region** (`EMEA`, `Americas`, `APAC`, `MEA`) and lists the top `Country · Carrier` pairs inside each region.

### 3.3 `carbonAtlas.pbiviz` — Carbon: Global Emission Atlas
- **Donut Attribution Rings around Country Plumes**: Each country on the world map features a central heat plume surrounded by a segmented D3 donut ring. Use the header pills to switch the ring between:
  - **`Ring: Circuit Types`**: Shows the proportion of carbon in that country caused by each circuit type (`Ethernet`, `Internet Access`, etc.).
  - **`Ring: Carriers`**: Shows the proportion of carbon in that country generated by each telecom carrier operating there.
  - **`Plume Only`**: Clean heat-plume circles.
- **Multi-Carrier Core Badge**: Countries with multiple carriers display a `NC` badge (e.g., `4C` for 4 carriers) at the center of the node and draw dashed cyan bezier arcs to other countries sharing the same carrier.

### 3.4 `carrierConstellation.pbiviz` — Carbon: Carrier Constellation
- **Bipartite Mode (`Country ↔ Carrier`)**: Carrier star hubs wear a segmented Circuit Type attribution ring, and links to Country nodes are colored by the dominant Circuit Type on that route.
- **Tripartite Mode (`Country ↔ Carrier ↔ Circuit Type`)**: Adds explicit `⚡ Circuit Type` hub nodes into the force simulation so you can visually trace which carriers rely on which circuit types and cross-filter the report by clicking any Circuit Type hub.
- **Interactive Node Dragging**: Click and drag any Country, Carrier, or Circuit node to rearrange the layout.

### 3.5 `circuitFlowers.pbiviz` — Carbon: Circuit Flowers
- **3 Grouping Modes**: Group flowers by **`Country`**, **`Carrier`**, or **`Country + Carrier`** (ideal when comparing multiple carriers inside the same country).
- **2 Petal Sizing Modes**: Toggle petals between **`Petals: CO2e`** (petal length scaled by attributed carbon emission) and **`Petals: Connections`** (petal length scaled by connection count).
- **Organic Leaf Geometry & Core Intensity**: Active circuit types render as curved botanical petals with glowing tips, while the flower's center core is heat-colored from Emerald (`low CO2e/conn`) to Crimson (`high CO2e/conn`) and displays the total connection count inside the core.

### 3.6 `efficiencyOrbit.pbiviz` — Carbon: Efficiency Orbit
- **4 Entity Modes**: Switch the scatter plot bubbles between **`By Country`**, **`By Carrier`**, **`Country + Carrier`**, and **`By Circuit Type`**.
- **Global Average Intensity Benchmark Curve**: Plots a dashed cyan curve representing $Y = \bar{I}_{\text{global}} \times X$. Bubbles **above** the curve emit more carbon per connection than the network average; bubbles **below** the curve operate more efficiently than average.
- **Outer Circuit-Mix Rings & Historical Trails**: Each bubble is wrapped in a segmented ring showing its circuit type mix and leaves a motion trail across years when scrubbed via the timeline.

### 3.7 `emissionRibbons.pbiviz` — Carbon: Emission Ribbons
- **4 Ranking Modes**: Track rankings for **`Countries`**, **`Carriers`**, **`Country + Carrier`**, or **`Circuit Types`**, sorted either by **`Total Carbon`** or **`Carbon/Conn`**.
- **Multi-Year Bump Chart + Single-Year Slopegraph**: When multiple years are present, columns represent reporting years. When only a single year is selected (or no Year column is bound), the visual automatically renders a **3-Stage Slopegraph (`Connections Rank → Total Carbon Rank → Carbon/Conn Rank`)** so ribbons are always visible and immediately reveal which entities jump in rank from connection volume to carbon intensity.

### 3.8 `carbonFlow.pbiviz` — Carbon: Regional Sankey Flow
- **4-Stage Alluvial Sankey (`Region → Country → Carrier → Circuit Type`)**: Traces how regional carbon footprints divide into countries, split across multiple carriers per country, and terminate in specific **Circuit Types** (`Ethernet`, `Internet Access`, `MPLS`, etc.) using exact per-circuit attributed carbon.
- **Flow Metric Switcher**: Toggle ribbon thickness between **`Flow: Carbon (CO2e)`** and **`Flow: Connections`**.

### 3.9 `carbonGlobe3d.pbiviz` — Carbon: 3D Emission Globe
- **3D WebGL Holographic Earth**: Extrudes 3D cylinders from every country's geographic coordinates (`OrbitControls` for 360° rotation, zoom, and pan).
- **Color Mode Switcher**: Color 3D pillars by **`Carbon/Conn Intensity`**, **`Dominant Circuit Type`**, or **`Total Carbon`**.
- **Guaranteed Country Coverage**: Built-in 100+ country coordinate dictionary with deterministic regional fallback coordinates ensures 100% of rows in your Power BI table render on the 3D globe and 2D atlas without external network calls.

---

## 4. Field Mapping Reference in Power BI

Every visual in the Carbon Suite shares the same unified 6-well field mapping so you can switch between any of the 9 visuals in Power BI Desktop with a single click without re-dragging fields:

| Field Well Name | Kind | Max Columns | What to Drag Here |
| :--- | :--- | :-: | :--- |
| **Country** | Grouping | `1` | Country name column (e.g., `Country`) |
| **Region** | Grouping | `1` | *(Optional)* Macro-region column (e.g., `Region` — auto-inferred from Country if omitted) |
| **Carrier** | Grouping | `1` | Telecom carrier / provider column (e.g., `Carrier`, `Provider`) |
| **Year** | Grouping or Measure | `1` | *(Optional)* Reporting year column (e.g., `Year` — enables timeline playback) |
| **Carbon Emission** | Measure | `1` | Total Carbon Emission column ($\text{CO}_2\text{e}$ for that Country + Carrier row) |
| **Circuit Types (Multiple Columns)** | Measure | `100` | **Drag ALL circuit connection count columns here** (`Ethernet`, `Internet Access`, `MPLS`, `DIA`, `Dark Fiber`, `Broadband`, `SD-WAN`, etc.) |

> **Important Power BI Tip**: Make sure **`Country`**, **`Carrier`**, **`Region`**, and **`Year`** are set to *"Don't Summarize"* in the field well, and your **`Carbon Emission`** and **`Circuit Types`** columns are numeric measures (or numeric columns set to *Sum*).

---

## 5. Building from Source

```bash
cd carbon-visuals
npm install
node scripts/generate-configs.mjs
npm run build:all
```

All 9 compiled `.pbiviz` packages are output to `carbon-visuals/dist/` and copied to `dataplay-powerbi-visuals/carbon-visuals/`.
