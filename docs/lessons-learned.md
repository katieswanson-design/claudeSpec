# Lessons Learned

Notes from real-world testing of claudeSpec — the gotchas, fixes, and patterns that emerged from generating specs with Claude.

## Preview frame placement

**Problem:** Component instances were placed as children of the `#preview` frame, but appeared outside the visible placeholder areas.

**Root cause:** Preview frames contain nested child frames (e.g., "Light theme preview placeholder" and "Dark theme preview placeholder"). The instance needs to be appended inside the specific placeholder frame, not the parent `#preview` wrapper.

**Fix:** Always inspect the preview's child structure first. Use `findOne()` to locate placeholder frames by name, then `appendChild()` into those — not into `#preview` directly. Set `primaryAxisAlignItems` and `counterAxisAlignItems` to `'CENTER'` on the placeholder to center the instance.

```javascript
const lightPlaceholder = preview.children.find(c => c.name.toLowerCase().includes('light'));
lightPlaceholder.appendChild(instance);
lightPlaceholder.primaryAxisAlignItems = 'CENTER';
lightPlaceholder.counterAxisAlignItems = 'CENTER';
```

## Template placeholder text targeting

**Problem:** Placeholder text like `{Table title}`, `{announcementExample}`, and `{value}` remained unfilled after rendering.

**Root cause:** The script targeted nodes by their frame name (e.g., `#table-title`) but the actual text node inside has a different name (e.g., `{Table title}`). The `findOne(n => n.type === 'TEXT')` pattern works, but you need to target the right parent frame first.

**Fix:** After cloning a template section, always traverse the full path: find the named frame, then find its text child. Validate by checking for any remaining `{` characters in the frame's text nodes after rendering.

## Font loading is mandatory

**Problem:** Text modifications silently fail without errors.

**Root cause:** Figma requires fonts to be loaded before text can be modified. If you skip `figma.loadFontAsync()`, text changes are silently dropped.

**Fix:** Always load fonts before any text manipulation. The safest pattern is to find all text nodes in the target frame, collect their font families and styles, then load them all:

```javascript
const textNodes = frame.findAll(n => n.type === 'TEXT');
const fontSet = new Set();
const fontsToLoad = [];
for (const tn of textNodes) {
  if (tn.characters.length > 0) {
    const fonts = tn.getRangeAllFontNames(0, tn.characters.length);
    for (const f of fonts) {
      const key = f.family + '|' + f.style;
      if (!fontSet.has(key)) { fontSet.add(key); fontsToLoad.push(f); }
    }
  }
}
await Promise.all(fontsToLoad.map(f => figma.loadFontAsync(f)));
```

## Multi-file navigation

**Problem:** The active file context can silently switch when the Desktop Bridge is running in multiple files.

**Root cause:** If you interact with the template library file in Figma (even just clicking on it), the Desktop Bridge may switch the active connection. Subsequent `figma_execute` calls then target the wrong file.

**Fix:** Always verify which file is active before executing scripts. Use `figma_list_open_files` to check, and `figma_navigate` with the target file URL to switch back. After switching, confirm with `figma_get_status`.

## Token extraction across variants

**Problem:** Extracting color tokens from a component set with many variants (4 sizes × 5 hierarchies × 5 states × 2 icon modes = 200 variants) can timeout.

**Fix:** Skip axes that don't affect color. Size and Icon Only typically don't change color tokens — only Hierarchy and State do. Pass a `SKIP_AXES` parameter to fix non-color axes to their default values:

```javascript
const SKIP_AXES = { "Size": "sm", "Icon only": "False" };
```

This reduces 200 variants to 25 (5 hierarchies × 5 states), well within timeout limits.

## Template section cloning pattern

All uSpec templates follow the same pattern: a named template section (e.g., `#variant-template`, `#anatomy-section`) that you clone for each variant/state, fill with data, then hide the original.

```javascript
// 1. Find the template
const template = frame.findOne(n => n.name === '#variant-template');

// 2. Clone for each variant
const section = template.clone();
template.parent.appendChild(section);
section.name = 'Primary';
section.visible = true;

// 3. Fill content...

// 4. Hide the original template
template.visible = false;
```

Always hide the original **after** all clones are created, not before — some templates reference sibling nodes that may be affected by visibility changes.

## Row template pattern

Tables use a similar pattern — clone the last row as a template, fill it, then remove the original:

```javascript
const rowTemplate = table.findOne(n => n.name === '#row-template');
for (const item of data) {
  const row = rowTemplate.clone();
  table.appendChild(row);
  // fill cells...
}
rowTemplate.remove(); // Remove the template, not hide
```

Note: rows are `remove()`d, not hidden — unlike section templates which are hidden. This is because hidden rows still take up space in auto-layout tables.

## Screen Reader template structure

The Screen Reader template has a unique nested structure: State → Platform → Table → Rows. Each state section contains a `#section` child for each platform (iOS, Android, Web), and each `#section` contains a `#state-table` with header and data rows.

The header row (`#header-row`) contains `#focus-order` and `#announcement` — the announcement cell holds the example announcement string (dynamic, per-state), not a column label.

Data rows use `#prop-name`, `#prop-value`, and `#prop-notes` for the accessibility properties.

When cloning platform sections for multiple platforms within a state, clone from the first `#section`, not from the state template — this avoids duplicating the entire state.

## Context window management

Each spec type consumes significant context. Best practices:

- **One spec per conversation** — start fresh for each spec type
- **Provide context upfront** — include component states, variants, and behaviors in your initial prompt
- **Template keys in the prompt** — include them rather than relying on Claude to search each time
- **Be specific about scope** — "Primary, Secondary, Tertiary hierarchies in Default and Disabled states" is better than "all variants"
