---
name: excalidraw
description: Creates Excalidraw diagram files (.excalidraw) for flowcharts, architecture diagrams, entity-relationship diagrams, mind maps, and other visual diagrams. Use when the user wants to visualize something as an Excalidraw file they can open and edit. Triggered by phrases like "draw this in excalidraw", "create an excalidraw diagram", "visualize as excalidraw", or "make an excalidraw of".
---

When the user wants a diagram:

1. **Clarify** what to draw if components or relationships aren't clear.
2. **Plan the layout** on a mental grid (cells ~200×100px, 100–200px gaps between).
3. **Write the `.excalidraw` file** using the Write tool. Default filename: `diagram.excalidraw` in the current directory, or a descriptive name the user specifies.
4. **Tell the user** how to open it:
   - VS Code: open the file with the Excalidraw extension
   - Web: excalidraw.com → Open → select the file

---

## File structure

```json
{
  "type": "excalidraw",
  "version": 2,
  "source": "https://excalidraw.com",
  "elements": [],
  "appState": { "gridSize": null, "viewBackgroundColor": "#ffffff" },
  "files": {}
}
```

---

## Element reference

Generate unique 8-character alphanumeric IDs (e.g. `"aB3xY9mK"`). Use a random 6-digit integer for `seed` and `versionNonce`. Use `1700000000000` for `updated`.

### Shared base fields (all element types)

```json
{
  "id": "aB3xY9mK",
  "type": "<see below>",
  "x": 100, "y": 100,
  "width": 200, "height": 80,
  "angle": 0,
  "strokeColor": "#1e1e1e",
  "backgroundColor": "transparent",
  "fillStyle": "solid",
  "strokeWidth": 2,
  "strokeStyle": "solid",
  "roughness": 1,
  "opacity": 100,
  "groupIds": [],
  "frameId": null,
  "roundness": null,
  "seed": 123456,
  "version": 1,
  "versionNonce": 654321,
  "isDeleted": false,
  "boundElements": [],
  "updated": 1700000000000,
  "link": null,
  "locked": false
}
```

### Shape types
- `"rectangle"` — use `"roundness": {"type": 3}` for rounded corners
- `"ellipse"` — for ovals and circles
- `"diamond"` — for decision nodes in flowcharts

### Text element (extra fields)

```json
{
  "type": "text",
  "text": "Hello",
  "originalText": "Hello",
  "fontSize": 20,
  "fontFamily": 1,
  "textAlign": "center",
  "verticalAlign": "middle",
  "baseline": 18,
  "lineHeight": 1.25,
  "containerId": null
}
```

**To embed a label inside a shape**: set `containerId` to the shape's ID, set `textAlign: "center"`, `verticalAlign: "middle"`. Then add `{"id": "<textId>", "type": "text"}` to the shape's `boundElements`.

### Arrow element (extra fields)

```json
{
  "type": "arrow",
  "points": [[0, 0], [0, 150]],
  "lastCommittedPoint": null,
  "startArrowhead": null,
  "endArrowhead": "arrow",
  "startBinding": null,
  "endBinding": null
}
```

Use `"roundness": {"type": 2}` on arrows for curved elbows.

**Binding arrows to shapes** — this links arrow endpoints to shapes so they move together:

1. Set `startBinding` on the arrow:
   ```json
   "startBinding": { "elementId": "<shapeId>", "focus": 0, "gap": 1 }
   ```
2. Set `endBinding` similarly for the target shape.
3. Add `{"id": "<arrowId>", "type": "arrow"}` to each bound shape's `boundElements`.

**Arrow label**: create a text element with `"containerId": "<arrowId>"` and add `{"id": "<textId>", "type": "text"}` to the arrow's `boundElements`.

---

## Arrow positioning

Arrow `x, y` is the absolute position of the *first point*. `points` are relative offsets from `x, y`.

For a downward arrow from the bottom edge of a shape (x=300, y=100, w=200, h=80) to the top edge of a shape (x=300, y=260, w=200, h=80):
- Start point: center of bottom edge → `x = 300+100 = 400`, `y = 100+80 = 180`
- End point: center of top edge → `x = 400`, `y = 260`
- Arrow: `"x": 400, "y": 180, "points": [[0,0],[0,80]]`

For a horizontal arrow, adjust accordingly.

---

## Layout guidelines

**Flowchart (top-to-bottom)**
- Start at y=50. Row spacing: element height + 80px gap.
- Center all elements at the same x (e.g. x=300 for w=200 elements → left edge at 300).
- Decision diamonds branch: "Yes" goes down, "No" goes right (+300px).
- Terminator nodes: use ellipse.

**Architecture diagram**
- Group services spatially (frontend left, backend center, data right).
- Box size: 220×80 or 220×100. Gap: 150px horizontal, 120px vertical.
- Use dashed `strokeStyle` for optional or async connections.
- Use frames or large background rectangles with low opacity to group tiers.

**Mind map**
- Central node at (500, 350).
- First-level branches: 280px radius, evenly distributed by angle.
- Second-level: 180px further out.

**Entity-relationship diagram**
- Entities as rectangles (220×80). Attributes as smaller ellipses around them.
- Use label text on arrows for relationship names ("has", "belongs to").

---

## Color palette

| Purpose | backgroundColor | strokeColor |
|---------|----------------|-------------|
| Primary / action | `#dbe4ff` | `#364fc7` |
| Success / output | `#d3f9d8` | `#2b8a3e` |
| Warning / decision | `#fff3bf` | `#e67700` |
| Error / stop | `#ffe3e3` | `#c92a2a` |
| External / third-party | `#f3d9fa` | `#862e9c` |
| Neutral / secondary | `#f1f3f5` | `#495057` |
| Transparent (arrows, text) | `"transparent"` | `"#1e1e1e"` |

---

## Complete minimal example — 3-step flowchart

```json
{
  "type": "excalidraw",
  "version": 2,
  "source": "https://excalidraw.com",
  "elements": [
    {
      "id": "start01x", "type": "ellipse",
      "x": 300, "y": 50, "width": 200, "height": 60,
      "angle": 0, "strokeColor": "#2b8a3e", "backgroundColor": "#d3f9d8",
      "fillStyle": "solid", "strokeWidth": 2, "strokeStyle": "solid",
      "roughness": 1, "opacity": 100, "groupIds": [], "frameId": null,
      "roundness": {"type": 2}, "seed": 111111, "version": 1, "versionNonce": 222222,
      "isDeleted": false, "boundElements": [{"id": "txt001x", "type": "text"}, {"id": "arr001x", "type": "arrow"}],
      "updated": 1700000000000, "link": null, "locked": false
    },
    {
      "id": "txt001x", "type": "text",
      "x": 300, "y": 50, "width": 200, "height": 60,
      "angle": 0, "strokeColor": "#2b8a3e", "backgroundColor": "transparent",
      "fillStyle": "solid", "strokeWidth": 1, "strokeStyle": "solid",
      "roughness": 1, "opacity": 100, "groupIds": [], "frameId": null,
      "roundness": null, "seed": 333333, "version": 1, "versionNonce": 444444,
      "isDeleted": false, "boundElements": [], "updated": 1700000000000, "link": null, "locked": false,
      "text": "Start", "originalText": "Start",
      "fontSize": 20, "fontFamily": 1, "textAlign": "center", "verticalAlign": "middle",
      "baseline": 18, "lineHeight": 1.25, "containerId": "start01x"
    },
    {
      "id": "arr001x", "type": "arrow",
      "x": 400, "y": 110, "width": 0, "height": 80,
      "angle": 0, "strokeColor": "#1e1e1e", "backgroundColor": "transparent",
      "fillStyle": "solid", "strokeWidth": 2, "strokeStyle": "solid",
      "roughness": 1, "opacity": 100, "groupIds": [], "frameId": null,
      "roundness": {"type": 2}, "seed": 555555, "version": 1, "versionNonce": 666666,
      "isDeleted": false, "boundElements": [], "updated": 1700000000000, "link": null, "locked": false,
      "points": [[0, 0], [0, 80]],
      "lastCommittedPoint": null, "startArrowhead": null, "endArrowhead": "arrow",
      "startBinding": {"elementId": "start01x", "focus": 0, "gap": 1},
      "endBinding": {"elementId": "proc01x", "focus": 0, "gap": 1}
    },
    {
      "id": "proc01x", "type": "rectangle",
      "x": 300, "y": 190, "width": 200, "height": 60,
      "angle": 0, "strokeColor": "#364fc7", "backgroundColor": "#dbe4ff",
      "fillStyle": "solid", "strokeWidth": 2, "strokeStyle": "solid",
      "roughness": 1, "opacity": 100, "groupIds": [], "frameId": null,
      "roundness": {"type": 3}, "seed": 777777, "version": 1, "versionNonce": 888888,
      "isDeleted": false, "boundElements": [{"id": "txt002x", "type": "text"}, {"id": "arr001x", "type": "arrow"}, {"id": "arr002x", "type": "arrow"}],
      "updated": 1700000000000, "link": null, "locked": false
    },
    {
      "id": "txt002x", "type": "text",
      "x": 300, "y": 190, "width": 200, "height": 60,
      "angle": 0, "strokeColor": "#364fc7", "backgroundColor": "transparent",
      "fillStyle": "solid", "strokeWidth": 1, "strokeStyle": "solid",
      "roughness": 1, "opacity": 100, "groupIds": [], "frameId": null,
      "roundness": null, "seed": 101010, "version": 1, "versionNonce": 121212,
      "isDeleted": false, "boundElements": [], "updated": 1700000000000, "link": null, "locked": false,
      "text": "Process", "originalText": "Process",
      "fontSize": 20, "fontFamily": 1, "textAlign": "center", "verticalAlign": "middle",
      "baseline": 18, "lineHeight": 1.25, "containerId": "proc01x"
    },
    {
      "id": "arr002x", "type": "arrow",
      "x": 400, "y": 250, "width": 0, "height": 80,
      "angle": 0, "strokeColor": "#1e1e1e", "backgroundColor": "transparent",
      "fillStyle": "solid", "strokeWidth": 2, "strokeStyle": "solid",
      "roughness": 1, "opacity": 100, "groupIds": [], "frameId": null,
      "roundness": {"type": 2}, "seed": 131313, "version": 1, "versionNonce": 141414,
      "isDeleted": false, "boundElements": [], "updated": 1700000000000, "link": null, "locked": false,
      "points": [[0, 0], [0, 80]],
      "lastCommittedPoint": null, "startArrowhead": null, "endArrowhead": "arrow",
      "startBinding": {"elementId": "proc01x", "focus": 0, "gap": 1},
      "endBinding": {"elementId": "end01x", "focus": 0, "gap": 1}
    },
    {
      "id": "end01x", "type": "ellipse",
      "x": 300, "y": 330, "width": 200, "height": 60,
      "angle": 0, "strokeColor": "#c92a2a", "backgroundColor": "#ffe3e3",
      "fillStyle": "solid", "strokeWidth": 2, "strokeStyle": "solid",
      "roughness": 1, "opacity": 100, "groupIds": [], "frameId": null,
      "roundness": {"type": 2}, "seed": 151515, "version": 1, "versionNonce": 161616,
      "isDeleted": false, "boundElements": [{"id": "txt003x", "type": "text"}, {"id": "arr002x", "type": "arrow"}],
      "updated": 1700000000000, "link": null, "locked": false
    },
    {
      "id": "txt003x", "type": "text",
      "x": 300, "y": 330, "width": 200, "height": 60,
      "angle": 0, "strokeColor": "#c92a2a", "backgroundColor": "transparent",
      "fillStyle": "solid", "strokeWidth": 1, "strokeStyle": "solid",
      "roughness": 1, "opacity": 100, "groupIds": [], "frameId": null,
      "roundness": null, "seed": 171717, "version": 1, "versionNonce": 181818,
      "isDeleted": false, "boundElements": [], "updated": 1700000000000, "link": null, "locked": false,
      "text": "End", "originalText": "End",
      "fontSize": 20, "fontFamily": 1, "textAlign": "center", "verticalAlign": "middle",
      "baseline": 18, "lineHeight": 1.25, "containerId": "end01x"
    }
  ],
  "appState": { "gridSize": null, "viewBackgroundColor": "#ffffff" },
  "files": {}
}
```
