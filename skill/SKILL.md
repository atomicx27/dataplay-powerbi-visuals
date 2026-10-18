---
name: powerbi-custom-visuals
description: End-to-end playbook, architecture, and troubleshooting guide for building, debugging, and packaging custom Power BI visuals (.pbiviz) with D3, Three.js, Canvas, and TypeScript.
---

# Power BI Custom Visuals Engineering Playbook

This skill captures the complete engineering architecture, development workflow, runtime sandbox constraints, and troubleshooting solutions discovered while porting interactive WebGL/D3 visualizers into high-performance, certified Power BI Custom Visuals (`.pbiviz`).

---

## 1. Architecture & Toolchain

### 1.1 Core Stack & Version Matrix
Power BI custom visuals execute inside an isolated sandbox iframe. Ensure compatibility with the following tested dependency matrix:

| Package | Version | Purpose |
|---|---|---|
| `powerbi-visuals-api` | `5.11.1` | Visual lifecycle (`IVisual`, `VisualUpdateOptions`, `IVisualHost`) |
| `powerbi-visuals-tools` (`pbiviz`) | `7.2.1` | Compiler, local dev server, and packaging CLI |
| `powerbi-visuals-utils-formattingmodel` | `6.0.4` | Modern Format Pane (Formatting Model v6) |
| `d3` / `@types/d3` | `7.9.0` / `7.4.3` | 2D layouts, force simulations, zoom/pan, scales |
| `three` / `@types/three` | `0.170.0` | 3D WebGL scenes, meshes, lights, materials |
| `three/examples/jsm/controls/OrbitControls.js` | `0.170.0` | 3D camera mouse/touch navigation |
| `typescript` | `5.5.4` | ES2022 target, strict typing |
| `eslint-plugin-powerbi-visuals` | `^1.0.0` | Security & sandbox certification validation |

### 1.2 Repository Structure
A scalable monorepo pattern separates shared abstractions from visual implementations:

```text
powerbi-visuals/
├── shared/
│   ├── capabilities.appearance.json    # Standard appearance settings schema
│   ├── assets/icon.png                 # 20x20 transparent visual icon
│   ├── src/
│   │   ├── color.ts                    # Neon/Theme/Rainbow palettes & deterministic PRNG
│   │   ├── data.ts                     # Robust table/categorical parser with role fallbacks
│   │   ├── settings.ts                 # Shared Appearance formatting cards & helpers
│   │   ├── components.ts / .tsx        # Landing page & UI error boundaries
│   │   ├── visualBase.ts               # Base visual class & service factories
│   │   └── index.ts                    # Barrel export
│   └── style/
│       └── dataplay.less               # Shared theme CSS, cards, legends, hints
├── visuals/
│   ├── bubblePhysics/                  # D3 force-directed collision bubbles
│   ├── dataCity/                       # 3D procedural WebGL metropolis (Three.js)
│   ├── networkWeb/                     # D3 force-directed node-link graph
│   └── ...
├── scripts/
│   ├── new-visual.mjs                  # Scaffolding CLI for new visual packages
│   └── build-all.mjs                   # Master compilation & packaging script
└── dist/                               # Output directory for all compiled .pbiviz files
```

---

## 2. The Pure TypeScript vs. React Paradigm

> [!IMPORTANT]
> **Why Pure TypeScript DOM Rendering is Preferred:**
> When embedding complex animation libraries (D3 simulations, Three.js WebGL contexts) inside Power BI's sandboxed iframe, wrapping them in React components frequently introduces lifecycle race conditions (`Cannot read properties of null (reading 'useState')`), unmount memory leaks, and double-mount glitches.
> 
> Implementing the visual as a **pure TypeScript class implementing `IVisual`** that directly manages its DOM container (`this.element`) provides:
> 1. Zero overhead and immediate lifecycle response on resize and filter events.
> 2. Direct control over animation frame loops (`requestAnimationFrame`).
> 3. Clean teardown and WebGL context disposal in `destroy()` and `cleanup()`.

---

## 3. Creating a New Visual: Step-by-Step

### Step 1: Scaffold using `scripts/new-visual.mjs`
Run the generator script to create the visual package with hoisted dependencies and a unique GUID:
```bash
node scripts/new-visual.mjs myVisual "My Visual Name" "Description of what it does"
```

### Step 2: Define `capabilities.json`
Configure data roles, data view mappings, modern formatting objects, and sandbox flags:

```json
{
    "dataRoles": [
        { "displayName": "Category", "name": "category", "kind": "Grouping", "description": "Grouping/Color field" },
        { "displayName": "Value", "name": "value", "kind": "Measure", "description": "Metric value" },
        { "displayName": "Tooltips", "name": "tooltips", "kind": "Measure" }
    ],
    "dataViewMappings": [
        {
            "conditions": [
                { "category": { "min": 0, "max": 1 }, "value": { "min": 0, "max": 1 }, "tooltips": { "min": 0, "max": 10 } }
            ],
            "table": {
                "rows": {
                    "select": [
                        { "for": { "in": "category" } },
                        { "for": { "in": "value" } },
                        { "for": { "in": "tooltips" } }
                    ],
                    "dataReductionAlgorithm": { "top": { "count": 30000 } }
                }
            }
        }
    ],
    "objects": {
        "visualSettings": {
            "properties": {
                "itemScale": { "type": { "numeric": true } },
                "showLabels": { "type": { "bool": true } }
            }
        },
        "appearance": {
            "properties": {
                "palette": {
                    "type": {
                        "enumeration": [
                            { "value": "neon", "displayName": "Data Play neon" },
                            { "value": "theme", "displayName": "Report theme" },
                            { "value": "rainbow", "displayName": "Rainbow" }
                        ]
                    }
                },
                "darkBackground": { "type": { "bool": true } },
                "showTooltips": { "type": { "bool": true } },
                "showDetailCard": { "type": { "bool": true } },
                "showHints": { "type": { "bool": true } }
            }
        }
    },
    "tooltips": { "supportedTypes": { "default": true, "canvas": true }, "roles": [ "tooltips" ] },
    "supportsLandingPage": true,
    "supportsEmptyDataView": true,
    "supportsMultiVisualSelection": true,
    "privileges": []
}
```

### Step 3: Define Format Settings (`src/settings.ts`)
Implement the Formatting Model v6 cards using `powerbi-visuals-utils-formattingmodel`:

```typescript
"use strict";
import { formattingSettings } from "powerbi-visuals-utils-formattingmodel";
import { AppearanceCard, slider, toggle } from "../../../shared/src";

class VisualConfigCard extends formattingSettings.SimpleCard {
    itemScale = slider("itemScale", "Item scale", 20, 5, 100);
    showLabels = toggle("showLabels", "Show labels", true);

    name = "visualSettings";
    displayName = "Visual Settings";
    slices: formattingSettings.Slice[] = [this.itemScale, this.showLabels];
}

export class Settings extends formattingSettings.Model {
    visualSettings = new VisualConfigCard();
    appearance = new AppearanceCard();
    cards: formattingSettings.Cards[] = [this.visualSettings, this.appearance];
}
```

### Step 4: Implement Visual Logic (`src/visual.ts`)
Follow this resilient lifecycle template:

```typescript
"use strict";
import powerbi from "powerbi-visuals-api";
import { FormattingSettingsService } from "powerbi-visuals-utils-formattingmodel";
import { ParsedTable, parseTable, str, num, createColorScale, formatValue } from "../../../shared/src";
import { Settings } from "./settings";
import "./../style/visual.less";

import VisualConstructorOptions = powerbi.extensibility.visual.VisualConstructorOptions;
import VisualUpdateOptions = powerbi.extensibility.visual.VisualUpdateOptions;
import IVisual = powerbi.extensibility.visual.IVisual;
import IVisualHost = powerbi.extensibility.visual.IVisualHost;
import ISelectionManager = powerbi.extensibility.ISelectionManager;
import ISelectionId = powerbi.visuals.ISelectionId;

export class Visual implements IVisual {
    private host: IVisualHost;
    private element: HTMLElement;
    private selectionManager: ISelectionManager;
    private formattingSettingsService: FormattingSettingsService;
    private settings: Settings;
    private stageContainer: HTMLDivElement;
    private selectedKeys = new Set<string>();

    constructor(options: VisualConstructorOptions) {
        this.host = options.host;
        this.element = options.element;
        this.selectionManager = this.host.createSelectionManager();
        this.formattingSettingsService = new FormattingSettingsService();
        this.settings = new Settings();

        this.element.classList.add("dataplay-root");
        this.stageContainer = document.createElement("div");
        this.stageContainer.className = "dp-stage";
        this.element.appendChild(this.stageContainer);

        // Context menu listener
        this.element.addEventListener("contextmenu", (e: MouseEvent) => {
            e.preventDefault();
            this.selectionManager.showContextMenu(
                this.host.createSelectionIdBuilder().createSelectionId(),
                { x: e.clientX, y: e.clientY }
            );
        });

        // External selection sync callback
        this.selectionManager.registerOnSelectCallback((ids: ISelectionId[]) => {
            this.selectedKeys = new Set((ids || []).map(id => (id as any).getKey ? (id as any).getKey() : String(id)));
            this.updateSelectionStyles();
        });
    }

    public update(options: VisualUpdateOptions): void {
        const events = this.host.eventService;
        events.renderingStarted(options);

        try {
            const dv = options.dataViews?.[0];
            const width = options.viewport.width || 400;
            const height = options.viewport.height || 300;

            // 1. Populate settings safely
            try {
                this.settings = this.formattingSettingsService.populateFormattingSettingsModel(Settings, dv);
            } catch {
                this.settings = new Settings();
            }

            // 2. Parse data safely
            const data: ParsedTable = parseTable(dv, this.host);

            // 3. Apply theme classes
            const isDark = !!this.settings.appearance?.darkBackground?.value && !this.host.colorPalette.isHighContrast;
            this.element.classList.toggle("dataplay-dark", isDark);
            this.element.classList.toggle("dataplay-hc", this.host.colorPalette.isHighContrast);

            // 4. CRITICAL: Clean up previous render artifacts
            this.cleanup();

            // 5. Render Landing Page if data is empty
            if (!data.rows.length) {
                this.renderLandingPage(width, height);
                events.renderingFinished(options);
                return;
            }

            // 6. Execute main visual renderer
            this.renderVisual(data, width, height);
            events.renderingFinished(options);
        } catch (err) {
            events.renderingFailed(options, String(err));
        }
    }

    public getFormattingModel(): powerbi.visuals.FormattingModel {
        try {
            return this.formattingSettingsService.buildFormattingModel(this.settings);
        } catch {
            return this.formattingSettingsService.buildFormattingModel(new Settings());
        }
    }

    public destroy(): void {
        this.cleanup();
    }

    private cleanup(): void {
        // Remove landing cards, cancel animations, dispose WebGL/D3 contexts
        const landings = this.stageContainer.querySelectorAll(".dp-landing");
        landings.forEach(el => el.remove());
    }

    private renderLandingPage(width: number, height: number): void {
        const landing = document.createElement("div");
        landing.className = "dp-landing";
        landing.style.width = `${width}px`;
        landing.style.height = `${height}px`;
        landing.innerHTML = `
            <div class="dp-landing-inner">
                <div class="dp-landing-title">Visual Title</div>
                <div class="dp-landing-desc">Add fields to the wells to display data.</div>
            </div>
        `;
        this.stageContainer.appendChild(landing);
    }

    private renderVisual(data: ParsedTable, width: number, height: number): void {
        // Implement custom D3/Three.js render logic here
    }

    private updateSelectionStyles(): void {
        // Update opacity/highlights based on this.selectedKeys
    }
}
```

---

## 4. Visual Architectures Deep Dive

### 4.1 Bubble Physics (D3 Force Collision)
- **Concept:** Circles representing data rows with radii proportional to metric values, clustered by category and bounded inside the viewport.
- **Physics Implementation:**
  ```typescript
  const sim = d3.forceSimulation<BubbleNode>(nodes)
      .force("charge", d3.forceManyBody<BubbleNode>().strength(5))
      .force("center", d3.forceCenter(width / 2, height / 2).strength(0.05))
      .force("collision", d3.forceCollide<BubbleNode>().radius(d => d.radius + 2).iterations(4))
      .force("x", d3.forceX<BubbleNode>(width / 2).strength(gravity))
      .force("y", d3.forceY<BubbleNode>(height / 2).strength(gravity))
      .on("tick", () => {
          node.attr("transform", d => {
              const r = d.radius || minR;
              d.x = Math.max(r, Math.min(width - r, d.x ?? width / 2));
              d.y = Math.max(r, Math.min(height - r, d.y ?? height / 2));
              return `translate(${d.x},${d.y})`;
          });
      });
  ```
- **Interactive Burst:** On node click, animate radius expansion and spring bounce with `d3.easeBounceOut` before selection propagation.

### 4.2 Data City 3D (Three.js WebGL Metropolis)
- **Concept:** Groups data into urban districts (`zone`), mapping rows to 3D skyscraper boxes with height and width driven by metrics.
- **Three.js Scene Setup & Memory Management:**
  - Create `PerspectiveCamera`, `Scene`, `DirectionalLight`, and `AmbientLight`.
  - Always call `renderer.dispose()`, `controls.dispose()`, and `cancelAnimationFrame(this.animId)` in `cleanupThree()`.
- **District Grid Arrangement:**
  ```typescript
  const districtList = Array.from(districtGroups.entries());
  const cols = Math.ceil(Math.sqrt(districtList.length));
  const spacing = citySettings.districtSpacing.value;
  const offsetX = (cols * spacing) / 2 - (spacing / 2);
  const offsetZ = (Math.ceil(districtList.length / cols) * spacing) / 2 - (spacing / 2);
  ```
- **Raycasting for 3D Selection & Tooltips:**
  Convert mouse coordinates to Normalized Device Coordinates (`[-1, 1]`):
  ```typescript
  const rect = this.renderer.domElement.getBoundingClientRect();
  this.mouse.x = ((event.clientX - rect.left) / rect.width) * 2 - 1;
  this.mouse.y = -((event.clientY - rect.top) / rect.height) * 2 + 1;
  this.raycaster.setFromCamera(this.mouse, this.camera);
  const intersects = this.raycaster.intersectObjects(this.buildingMeshes);
  ```

### 4.3 Network Web (D3 Force Graph)
- **Concept:** Node-link network displaying connectivity between `source` and `target` entities with weighted link widths.
- **Data Aggregation:** Aggregate distinct nodes into a `Map<string, NetNode>` and track connection counts (`connCount`) to scale node radius dynamically:
  ```typescript
  nodeRadius = Math.min(Math.max(8, connCount * 3 + 6), 36);
  ```
- **Interactive Zoom & Pan:**
  ```typescript
  const zoom = d3.zoom<SVGSVGElement, unknown>()
      .scaleExtent([0.1, 4])
      .on("zoom", (event) => g.attr("transform", event.transform));
  svgSelection.call(zoom);
  ```

### 4.4 Dual-Month Savings Web (`savingsDelta`)
- **Concept:** Analyzes recurring financial deltas (e.g., last month vs this month savings) grouped by categories with directional color grading (positive vs negative variances).
- **Variance Tracking:** Compute baseline delta metrics:
  ```typescript
  const delta = thisMonth - lastMonth;
  const pctChange = lastMonth !== 0 ? (delta / lastMonth) * 100 : 0;
  const isPositive = delta >= 0;
  ```

### 4.5 Hologram Globe 3D (`hologramGlobe`) & Solar System (`solarSystem`)
- **Concept:** Three.js orbital models using procedural geometries and latitude/longitude spherical coordinates:
  ```typescript
  // Convert spherical coordinates (lat, lon, radius) to Cartesian (x, y, z)
  const phi = (90 - lat) * (Math.PI / 180);
  const theta = (lon + 180) * (Math.PI / 180);
  const x = -(radius * Math.sin(phi) * Math.cos(theta));
  const z = radius * Math.sin(phi) * Math.sin(theta);
  const y = radius * Math.cos(phi);
  ```
- **Disposal Guarantee:** Always traverse meshes, dispose geometries and materials on visual destruction or re-render.

### 4.6 Physics Blocks (`physicsBlocks`)
- **Concept:** 2D rigid-body simulation using Matter.js engine. Rows fall under gravity and stack interactively inside bounded canvas containers.

---

## 5. Comprehensive Bug & Error Catalog

| Error / Symptom | Root Cause | Solution |
|---|---|---|
| **Blank screen on data drop** (`bubblePhysics`, `dataCity`) | Numeric scale calculation received `null`, `undefined`, or `NaN` from un-aggregated data, setting SVG `<circle r="NaN">` or WebGL coordinates to `NaN`. | Use `safeSqrtScale(v, min, max, r0, r1)` and `num(val, fallback)` to guarantee finite numbers and fallbacks. |
| **Stacked landing page pushes WebGL offscreen** | When data was added, `update()` rendered the 3D canvas without deleting previous `.dp-landing` DOM elements, pushing the canvas 600px below view. | Query and remove all `.dp-landing` elements at the start of `update()` / `cleanup()`. |
| **React hook crash: `Cannot read properties of null (reading 'useState')`** | Sandboxed iframe context isolation conflicted with React 19 dispatcher mounting. | Refactor visual to a pure TypeScript class implementing `IVisual` directly on the DOM. |
| **Only 1,000 rows loaded in large datasets** | Power BI default data reduction truncates table views to 1,000 rows if unspecified. | Add `"dataReductionAlgorithm": { "top": { "count": 30000 } }` to `capabilities.json`. |
| **Format Pane sliders missing / not appearing** | Format properties defined in `capabilities.json` `objects` did not match card property names in `Settings.ts` or used legacy `enumerateObjectInstances`. | Use Formatting Model v6 with `FormattingSettingsService.populateFormattingSettingsModel()` and `buildFormattingModel()`. |
| **WebGL Context Lost (`WARNING: Too many active WebGL contexts`)** | Visual resizing or cross-filtering spawned new `THREE.WebGLRenderer` instances without disposing old contexts. | Always call `renderer.dispose()`, remove the canvas DOM child, and cancel animation frame IDs before re-creating the scene. |
| **`pbiviz package` fails with `insecure-random` lint error** | Power BI certification forbids native `Math.random()`. | Use a deterministic PRNG such as Mulberry32 (`createRng(seed)`). |
| **Tooltip crash when user adds optional tooltip fields** | `capabilities.json` lacked tooltips mapping conditions. | Add `"tooltips": { "min": 0, "max": 10 }` in conditions and `"tooltips": { "roles": ["tooltips"] }`. |
| **Cross-filtering fails for duplicate row values** | Using simple category strings for `SelectionId` caused key collisions across rows. | Build Selection IDs with `host.createSelectionIdBuilder().withTable(table, rowIndex).createSelectionId()`. |

---

## 6. Power BI Sandbox Rules & Certification Checklist

1. **No External Network Calls:**
   - Visuals must declare `"privileges": []` in `capabilities.json`.
   - Never use `fetch()`, `XMLHttpRequest`, `<script src="...">`, or CDN-hosted fonts/textures. All assets (icons, stylesheets, textures) must be compiled locally.
2. **Deterministic Layouts:**
   - Replace any `Math.random()` calls with `createRng(seed)` to pass ESLint security rules and maintain layout stability across slicer updates.
3. **High Contrast Compliance:**
   - Check `host.colorPalette.isHighContrast`.
   - When true, render high contrast foreground (`host.colorPalette.foreground.value`) and background colors (`--dp-hc-fg`, `--dp-hc-bg`).
4. **Landing Page Requirement:**
   - When `dataViews` is empty or missing required roles, render an informative landing card explaining which fields to drag into the wells.
5. **Selection & Context Menu:**
   - Bind selection events to `selectionManager.select(id, multiSelect)`.
   - Implement `contextmenu` listener to support drill-through and standard Power BI right-click actions.

---

## 8. Domain Playbook: Telecom / Last-Mile Access Cost Analytics (OINIS)

In enterprise data analytics scenarios (e.g., Orange Business OINIS last-mile off-net telecom circuits), the visualizers map directly to specific financial and infrastructure questions:

| Visual Template | Telecom & Access Cost Business Case | Field Mappings |
|---|---|---|
| **Savings Waterfall (`savingsDelta`)** | Demonstrates net savings from renegotiations, disconnects, migrations, and MRC price reductions over time. | Category: `Saving_Type` / `Month`<br>Measure: `Monthly_Savings_EUR` |
| **Carrier City 3D (`dataCity`)** | Compares supplier spend across local telco carriers. Districts = Countries/Regions, Buildings = Carriers, Height = MRC Spend, Dark Windows = Inactive/Unmatched circuits still being billed. | Zone: `Country` or `Carrier_Tier`<br>Height: `MRC`<br>Width: `Circuit_Count` |
| **Circuit Network Web (`networkWeb`)** | Visualizes mesh connectivity between Customer Sites, Orange PoPs, and Carrier Interconnects with link thickness reflecting bandwidth or MRC. | Source: `PoP_Location`<br>Target: `Customer_Site`<br>Weight: `Bandwidth_Mbps` |
| **Contract Orbit Radar (`solarSystem`)** | Displays contracts orbiting carrier hubs; distance and color shift toward red as auto-renewal / contract expiry dates approach. | Planet: `Carrier`<br>Moon: `Circuit_ID`<br>Radius: `MRC` |
| **Last-Mile Globe 3D (`hologramGlobe`)** | Visualizes geographic spread of off-net circuits with arcs connecting PoPs to customer endpoints colored by circuit status (Active, Pending, Upgrade). | Source Lat/Long: PoP<br>Dest Lat/Long: Site |

### 8.1 Carbon Footprint & Per-Circuit-Type Attribution Suite (`carbon-visuals/`)

Dedicated domain visualizers for telecom circuit emissions, multi-carrier per country benchmarking, and dynamic per-circuit-type carbon attribution:

#### Data Schema & Attribution Formula
- **Granularity:** Each row represents `Country` + `Carrier` (+ `Year` + `Region`). Multiple carriers can operate inside the same country.
- **Total Carbon (`carbon`):** Total carbon emission ($\text{CO}_2\text{e}$) for that `Country + Carrier` row.
- **Dynamic Circuit Type Columns (`circuitTypes`, `max: 100`):** Wide-format connection count columns (e.g., `Ethernet = 3`, `Internet Access = 2`, `MPLS = 1`).
- **Row-Level Per-Circuit-Type Carbon Attribution (`shared/src/data.ts`):**
  $$N_{\text{row}} = \sum_{t} c_{\text{row}, t}, \qquad I_{\text{row}} = \frac{\text{Carbon}_{\text{row}}}{N_{\text{row}}}, \qquad E_{\text{row}, t} = \text{Carbon}_{\text{row}} \times \frac{c_{\text{row}, t}}{N_{\text{row}}}$$

| Visual | ESG & Telecom Analytics Focus | Key Mechanics & Mappings |
|---|---|---|
| **Circuit Attribution Matrix (`circuitAttribution`)** | Per-circuit-type carbon attribution & intensity across countries/carriers. | 3 interactive views: Stacked Attribution Bars, Country $\times$ Circuit Heatmap Matrix, and Per-Circuit Deep-Dive Cards (`Country`, `Carrier`, `Carbon Emission`, `Circuit Types`). |
| **Country & Carrier Scorecard (`carrierScorecard`)** | Multi-carrier per country benchmarking & `A+`–`F` efficiency grading. | Compares multiple carriers inside each country with circuit mix bars, intensity grades, and global/regional rollups. |
| **Carbon Atlas (`carbonAtlas`)** | Global geographic emission map with donut attribution rings. | Mercator map, country plumes wrapped in Circuit Type or Carrier Share donut rings, inter-country shared carrier arcs, HUD legend, and year timeline. |
| **Carrier Constellation (`carrierConstellation`)** | Bipartite (`Country ↔ Carrier`) & Tripartite (`+ Circuit Type`) network. | Force-directed graph with draggable nodes, circuit-ring carrier hubs, and dominant-circuit colored links. |
| **Circuit Flowers (`circuitFlowers`)** | Botanical glyph matrix of circuit mixes & carbon intensity. | Group by `Country`, `Carrier`, or `Country + Carrier`; organic leaf petals sized by Attributed $\text{CO}_2\text{e}$ or Connection Count; core colored by `CO2e/conn`. |
| **Efficiency Orbit (`efficiencyOrbit`)** | Quadrant scatter (`Connections` vs `Carbon`) with benchmark curve. | Plots Country, Carrier, Country+Carrier, or Circuit Type bubbles with outer circuit-mix rings, global average intensity line, and historical trails. |
| **Emission Ribbons (`emissionRibbons`)** | Multi-year ranking bump chart & single-year 3-stage slopegraph. | Tracks rank shifts of Countries, Carriers, Country+Carrier pairs, or Circuit Types across years or across `Connections Rank → Carbon Rank → Intensity Rank`. |
| **Carbon Flow (`carbonFlow`)** | 4-Stage Alluvial Sankey (`Region → Country → Carrier → Circuit Type`). | Traces carbon footprint and connection volume across all 4 hierarchy levels with stacked non-overlapping ribbons. |
| **Carbon Globe 3D (`carbonGlobe3d`)** | Spherical 3D WebGL Earth with extruded carbon pillars. | Three.js holographic globe with color mode switcher (`Carbon/Conn`, `Dominant Circuit`, `Total Carbon`), 3D carrier arcs, and HUD attribution panel. |

---

## 9. Build & Packaging Commands

```bash
# Package a single visual with certification fixes:
cd visuals/<visualName>
npx pbiviz package --certification-fix

# Master monorepo build (compiles all visuals to dist/):
npm run build:all
```
All packaged `.pbiviz` files are generated in `dist/` ready for direct import into Power BI Desktop or Service.


