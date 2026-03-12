---
name: setup-library
description: Configure claudeSpec with your Figma template library. Use when the user wants to set up template keys, configure templates, or link their template library for the first time.
adapted_from: uSpec by Ian Guisard (https://github.com/redongreen/uSpec)
---

# Setup Library

Configure claudeSpec to use your Figma template library. This skill extracts component keys from your template file and provides the configuration for future spec generation.

## Inputs Expected

- **Figma link**: URL to the Figma file containing your documentation templates (required)

The file must contain components with these exact names:
- Screen reader
- Color Annotation
- Overview (or Anatomy)
- API
- Properties (or Property)
- Structure
- Changelog
- Motion

## Workflow

Track progress through these steps:

```
Task Progress:
- [ ] Step 1: Verify MCP connection
- [ ] Step 2: Navigate to the library file
- [ ] Step 3: Search for template components
- [ ] Step 4: Extract component keys
- [ ] Step 4b: Detect font family from template
- [ ] Step 5: Present configuration to user
- [ ] Step 6: Offer to save to memory
```

### Step 1: Verify MCP Connection

Check that Figma Console MCP is connected:
- `figma_get_status` — Confirm Desktop Bridge plugin is active

If connection fails, guide user:
> Please open Figma Desktop and run the Desktop Bridge plugin. Then try again.

### Step 2: Navigate to the Library File

Use the Figma link provided by the user:
- `figma_navigate` — Open the template library URL

**Important:** If the user also has their component file open with the Desktop Bridge, this will switch the active file context. Remember to switch back before generating specs.

### Step 3: Search for Template Components

Search for all template components at once:
- `figma_search_components` with no query to get all components in the file

Or search individually for each of the 8 template components by name.

### Step 4: Extract Component Keys

For each found component, extract its component key from the `key` field in the search results.

Build a mapping of template type to key:
- screenReader: key from "Screen reader" component
- colorAnnotation: key from "Color Annotation" component
- anatomyOverview: key from "Overview" or "Anatomy" component
- apiOverview: key from "API" component
- propertyOverview: key from "Properties" or "Property" component
- structureSpec: key from "Structure" component
- changelog: key from "Changelog" component
- motionSpec: key from "Motion" component

If any template is not found, report which ones are missing.

### Step 4b: Detect Font Family from Template

Using the node ID of one of the found template components:
- Use `figma_execute` to find the first TEXT node and read its font family

```javascript
const node = await figma.getNodeByIdAsync('NODE_ID_FROM_STEP_3');
const textNode = node.findOne(n => n.type === 'TEXT');
if (textNode) {
  return textNode.fontName.family;
} else {
  return 'Inter';
}
```

Default to `Inter` if detection fails.

### Step 5: Present Configuration to User

Present the configuration clearly. In Claude Desktop there is no local config file — the configuration lives in the conversation context or Claude's memory.

```json
{
  "fontFamily": "DETECTED_FONT_FAMILY",
  "templateKeys": {
    "screenReader": "...",
    "colorAnnotation": "...",
    "anatomyOverview": "...",
    "apiOverview": "...",
    "propertyOverview": "...",
    "structureSpec": "...",
    "changelog": "...",
    "motionSpec": "..."
  }
}
```

### Step 6: Offer to Save to Memory

Ask the user if they'd like Claude to remember these template keys for future sessions.

> **Setup complete!** Your template library is configured. You can now generate specs by providing a Figma component link and asking for any spec type.
>
> Would you like me to remember these template keys for future conversations?

## Key Differences from uSpec

- **No local config file**: Configuration lives in Claude's memory or conversation context, not `uspecs.config.json`
- **Template name flexibility**: Accepts both "Overview"/"Anatomy" and "Property"/"Properties"
- **Multi-file awareness**: Explicitly manages active file context when switching between template library and component files
