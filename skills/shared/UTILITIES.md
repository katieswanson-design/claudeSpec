[UTILITIES.md](https://github.com/user-attachments/files/25946496/UTILITIES.md)
# Shared Utilities

Scripts used across all claudeSpec skills for consistent template placement and layout.

## Smart Placement (use when importing a template)

Before placing a new template frame, scan the page for existing content and position the new frame to the right of everything, avoiding overlaps:

```javascript
// Smart placement — add this after detaching the template instance
const page = figma.currentPage;
const existing = page.children.filter(n => n.type === 'FRAME');
let rightEdge = 0;
let topY = Infinity;
for (const f of existing) {
  rightEdge = Math.max(rightEdge, f.x + f.width);
  topY = Math.min(topY, f.y);
}
if (topY === Infinity) topY = 0;
const GUTTER = 100;

// Position the new frame to the right of all existing content
frame.x = existing.length > 0 ? rightEdge + GUTTER : 0;
frame.y = existing.length > 0 ? topY : 0;
```

Replace the default viewport-center placement in each skill's template import step with this logic.

## Tidy-Up (use after all specs are generated)

After all spec frames have been generated, run this cleanup script to arrange them alphabetically, top-aligned with 100px gutters:

```javascript
const page = figma.currentPage;

// Identify spec frames by known suffixes
const SPEC_SUFFIXES = ['Anatomy', 'API', 'Color Annotation', 'Properties', 'Screen Reader', 'Structure'];
const specFrames = page.children.filter(n => 
  n.type === 'FRAME' && SPEC_SUFFIXES.some(s => n.name.includes(s))
);

if (specFrames.length === 0) return { error: 'No spec frames found' };

// Sort alphabetically by frame name
specFrames.sort((a, b) => a.name.localeCompare(b.name));

// Find a reasonable top-Y from existing content
const topY = Math.min(...specFrames.map(f => f.y));

// Determine starting X — to the right of any non-spec content
const nonSpecFrames = page.children.filter(n => 
  n.type === 'FRAME' && !SPEC_SUFFIXES.some(s => n.name.includes(s))
);
let startX = 0;
if (nonSpecFrames.length > 0) {
  const nonSpecRight = Math.max(...nonSpecFrames.map(f => f.x + f.width));
  startX = nonSpecRight + 200; // Extra gap from source content
}

const GUTTER = 100;
let currentX = startX;

const results = [];
for (const frame of specFrames) {
  frame.x = currentX;
  frame.y = topY;
  results.push({ name: frame.name, x: Math.round(currentX), width: Math.round(frame.width) });
  currentX += frame.width + GUTTER;
}

figma.viewport.scrollAndZoomIntoView(specFrames);
return { success: true, framesArranged: results.length, order: results.map(r => r.name) };
```

## Usage in Skills

### On template import (every skill)
Replace the viewport-center placement block:
```javascript
// OLD — places at viewport center, may overlap
const { x, y } = figma.viewport.center;
instance.x = x - instance.width / 2;
instance.y = y - instance.height / 2;
```

With the smart placement block:
```javascript
// NEW — places to the right of existing content
const existing = figma.currentPage.children.filter(n => n.type === 'FRAME');
let rightEdge = 0;
let topY = Infinity;
for (const f of existing) {
  rightEdge = Math.max(rightEdge, f.x + f.width);
  topY = Math.min(topY, f.y);
}
if (topY === Infinity) topY = 0;
frame.x = existing.length > 0 ? rightEdge + 100 : 0;
frame.y = existing.length > 0 ? topY : 0;
```

### After all specs are generated
Tell Claude: "Please tidy up the spec frames" or include it in a multi-spec prompt as a final step. Claude will run the alphabetical tidy-up script.
