# Data Play — Power BI Custom Visuals

A comprehensive collection of **13 interactive custom visuals for Power BI**, ported from the **Data Play** web app.
All visuals are pre-packaged as `.pbiviz` files ready to import directly into Power BI Desktop or the Power BI Service without compiling or external dependencies.

---

## 📦 Packaged Custom Visuals (`visuals/`)

| Visual | File | Description & Capabilities |
|---|---|---|
| 📊 **Bar Chart Plus** | [`visuals/barChartPlus.pbiviz`](visuals/barChartPlus.pbiviz) | Clustered bar chart with enhanced category analytics, dynamic thresholds, and rich interactive filtering. |
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

## ⬇️ Download

- **Single visual:** Open any `.pbiviz` file in the [`visuals/`](visuals/) folder and click **Download raw file**.
- **All visuals:** Clone this repository or use GitHub Releases:
  ```bash
  git clone https://github.com/atomicx27/dataplay-powerbi-visuals.git
  ```
  or download via GitHub CLI:
  ```bash
  gh release download --repo atomicx27/dataplay-powerbi-visuals --pattern "*.pbiviz"
  ```

---

## 📥 Import into Power BI

### Power BI Desktop
1. In the **Visualizations** pane, click the **`…`** (Get more visuals) icon.
2. Select **Import a visual from a file**.
3. Pick your desired `.pbiviz` file from the `visuals/` directory and accept the prompt.
4. The custom visual icon will appear in the Visualizations pane. Click it to add it to your report canvas.

### Power BI Service (app.powerbi.com)
1. Open a report in **Edit** mode.
2. Under the **Visualizations** pane, click **`…` > Import a visual from a file**.
3. Upload the `.pbiviz` file.

> [!NOTE]
> If your tenant restricts third-party visuals, a Power BI administrator can enable them in **Admin portal → Tenant settings → Allow visuals created using the Power BI SDK**, or deploy them directly to your **Organizational visuals** library.

---

## 🧩 Field Wells & Usage Highlights

### 🫧 Bubble Physics
| Field Well | Type | Purpose |
|---|---|---|
| **Color group** | Category (Grouping) | Assigns cluster color and grouping forces. |
| **Details** | Category (Grouping, up to 3) | Generates unique individual bubbles. |
| **Bubble size** | Measure | Sets bubble radius (square-root scaled). |
| **Tooltips** | Measures (up to 10) | Metrics displayed on hover. |

*Format Pane: Max bubbles, Min/Max radius, Center gravity, Category labels, Burst animation.*

### 🏙️ Data City (3D)
| Field Well | Type | Purpose |
|---|---|---|
| **District (Zone)** | Category | Groups buildings into distinct district grids. |
| **Building height** | Measure | Normalised skyscraper elevation. |
| **Building width** | Measure | Footprint width of the building. |
| **Tooltips** | Measures | Hover detail cards. |

*Controls: Left-drag to orbit, right-drag to pan, wheel to zoom, click to cross-filter.*

### 🕸️ Network Web
| Field Well | Type | Purpose |
|---|---|---|
| **Source node** | Category | Origin node of the connection. |
| **Target node** | Category | Destination node of the connection. |
| **Link weight** | Measure | Link thickness and connection strength. |
| **Tooltips** | Measures | Metrics on link or node hover. |

*Format Pane: Max connections, Link distance, Repulsion force, Node labels.*

### 📉 Dual-Month Savings Web (`savingsDelta`)
| Field Well | Type | Purpose |
|---|---|---|
| **Category** | Category | Item, supplier, or account category. |
| **Last Month** | Measure | Previous reporting period amount. |
| **This Month** | Measure | Current reporting period amount. |
| **Tooltips** | Measures | Additional variance breakdown fields. |

### 🌐 Hologram Globe 3D (`hologramGlobe`)
| Field Well | Type | Purpose |
|---|---|---|
| **Location / City** | Category | Geo name or coordinate pair. |
| **Metric Value** | Measure | Extruded pillar height on the globe. |
| **Category** | Category | Color coding for pillars and arcs. |

### 🎨 Shared Appearance Card (All Visuals)
- **Palette Modes:** Data Play Neon, Report Theme, or High-Contrast Rainbow.
- **Dark Mode Background:** Toggles dark glassmorphism background.
- **Selection & Cross-filtering:** Fully synchronized with native Power BI slicers.
- **High-contrast Accessibility:** Inherits Windows/Power BI high-contrast themes automatically.

---

## 💡 Interaction Cheatsheet

| Action | Result |
|---|---|
| **Left Click** | Selects data point and cross-filters all other visuals on page. |
| **Ctrl / Shift + Click** | Multi-selects items. |
| **Legend Click** | Selects all data points in the selected category. |
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

To use the skill with an AI coding assistant, copy `skill/SKILL.md` into your agent skills path (e.g. `~/.gemini/config/skills/powerbi-custom-visuals/`).

---

## 📄 Technical Specifications

- **Power BI Visuals API:** `5.11.1`
- **Packaging CLI:** `powerbi-visuals-tools` (`pbiviz`) `7.2.1`
- **Engines:** D3 v7, Three.js r170, Matter.js
- **Privacy & Security:** 100% offline runtime sandbox (`"privileges": []`), zero external CDN/network calls, certified deterministic PRNG (`createRng`).

Author: [@atomicx27](https://github.com/atomicx27) · License: MIT
