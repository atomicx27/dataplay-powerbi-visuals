# Data Play — Power BI Custom Visuals

Three interactive custom visuals for Power BI, ported from the **Data Play** web app.
They are packaged as `.pbiviz` files, so you can download one and import it straight into Power BI Desktop or the Power BI Service. Nothing needs to be built first.

| Visual | File | What it does |
|---|---|---|
| 🫧 **Bubble Physics** | [`visuals/bubblePhysics.pbiviz`](visuals/bubblePhysics.pbiviz) | Force-directed bubbles that collide and cluster by category. You can drag bubbles, and clicking one cross-filters the report. |
| 🏙️ **Data City (3D)** | [`visuals/dataCity.pbiviz`](visuals/dataCity.pbiviz) | A 3D WebGL city. Each category becomes a district and each row becomes a skyscraper, with height and width driven by your measures. You can fly the camera around. |
| 🕸️ **Network Web** | [`visuals/networkWeb.pbiviz`](visuals/networkWeb.pbiviz) | A force-directed node-link graph built from Source → Target pairs. It supports zoom, pan, dragging nodes and cross-filtering. |

---

## ⬇️ Download

- **Single file:** open a `.pbiviz` file in [`visuals/`](visuals/) and click **Download raw file**.
- **All three:** grab them from the [Releases](../../releases) page, or use **Code → Download ZIP**.
- **CLI:**
  ```bash
  gh release download --repo atomicx27/dataplay-powerbi-visuals --pattern "*.pbiviz"
  ```

## 📥 Import into Power BI

**Power BI Desktop**
1. In the **Visualizations** pane, click the **`…`** (Get more visuals) button.
2. Choose **Import a visual from a file**.
3. Pick the `.pbiviz` file and accept the warning.
4. A new icon appears in the Visualizations pane. Click it to add the visual to your page.

**Power BI Service (app.powerbi.com)**
Edit a report, then go to **Visualizations → … → Import a visual from a file**.

> If your organisation blocks uncertified visuals, an admin must allow them under **Admin portal → Tenant settings → Allow visuals created using the Power BI SDK**, or add them to the **Organizational visuals** store.

---

## 🧩 Field wells

### 🫧 Bubble Physics
| Field well | Type | Purpose |
|---|---|---|
| **Color group** | Category (Grouping) | Sets the color of each bubble and clusters bubbles by this field. |
| **Details** | Category (Grouping), up to 3 fields | Gives one bubble per unique value, e.g. an ID or a name. |
| **Bubble size** | Measure | Sets the bubble radius (square-root scaled). |
| **Tooltips** | Measures (up to 10) | Extra fields shown on hover. |

**Format pane → Bubbles:** Max bubbles, Min/Max radius, Center gravity, Category labels, Burst animation on click.

### 🏙️ Data City (3D)
| Field well | Type | Purpose |
|---|---|---|
| **District (Zone)** | Category | Each unique value becomes a district block. |
| **Building height** | Measure | Sets the skyscraper height (normalised). |
| **Building width** | Measure | Sets the building footprint width. |
| **Tooltips** | Measures | Extra fields shown on hover. |

**Format pane → Data City:** Max buildings, Max height scale, District spacing.
**Controls:** drag to orbit, scroll to zoom, right-drag to pan, click a building to cross-filter.

### 🕸️ Network Web
| Field well | Type | Purpose |
|---|---|---|
| **Source node** | Category | The node where a link starts. |
| **Target node** | Category | The node where a link ends. |
| **Link weight** | Measure | Sets the link thickness (√ scaled). |
| **Tooltips** | Measures | Extra fields shown on hover. |

Node size grows with the number of connections.
**Format pane → Network:** Max connections, Link distance, Repulsion strength, Node labels.

### 🎨 Shared “Appearance” card (all visuals)
- **Color palette:** Data Play neon, Report theme, or Rainbow.
- **Dark background**, **Tooltips**, **Detail card on click**, **Interaction hints**.
- High-contrast mode is respected automatically.

---

## 💡 Interaction cheatsheet

| Action | Result |
|---|---|
| Click | Selects the item and cross-filters the other visuals. |
| Ctrl / Shift + click | Adds to the selection (multi-select). |
| Click a legend item | Selects every item in that category. |
| Click empty space | Clears the selection. |
| Right-click | Opens the Power BI context menu (drill-through, include/exclude). |
| Drag | Moves bubbles or nodes, or orbits the 3D camera in Data City. |

## 📊 Sample data shapes

```text
Bubble Physics : Region | Customer | Revenue
Data City      : Department | Headcount | Budget
Network Web    : FromSite | ToSite | TrafficGbps
```

Each visual reads up to 30,000 rows. Use the **Max …** slider in the format pane to cap how many rows are drawn, which keeps big tables fast.

---

## 🛠️ Build your own visuals

The [`skill/SKILL.md`](skill/SKILL.md) file is an AI-agent skill. It records the whole playbook used to build these visuals: architecture, toolchain versions, the capabilities/settings/visual templates, Power BI sandbox rules, and every bug that was hit along the way together with its fix.

To use it, copy the `skill/` folder into your agent's skills directory, e.g. `~/.gemini/config/skills/powerbi-custom-visuals/`. Then ask the agent: *“Create a new Data Play Power BI visual that …”*.

## 📄 Technical info

- Power BI Visuals API `5.11.1` and `powerbi-visuals-tools` (pbiviz CLI) `7.2.1`.
- Bubble Physics and Network Web use **D3 v7**. Data City uses **three.js r170** with OrbitControls.
- No external network calls (`privileges: []`), so the visuals run fully inside the Power BI sandbox.

Author: [@atomicx27](https://github.com/atomicx27) · License: MIT
