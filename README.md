# claudeSpec

Generate design system documentation directly in Figma — powered by Claude and the Figma Console MCP.

claudeSpec adapts the [uSpec](https://github.com/redongreen/uSpec) pipeline (by [Ian Guisard](https://www.linkedin.com/in/iguisard/)) for Claude users. Instead of Cursor's `@skill` triggers, you work conversationally with Claude while it reads component data, imports templates, and renders structured specs right into your Figma file.

## What you can generate

| Spec type | What you get |
|-----------|-------------|
| **Anatomy** | Numbered markers on a component with an attribute table |
| **API** | Property tables with values, defaults, and configuration examples |
| **Color Annotation** | Design token mapping for every element and state |
| **Structure** | Dimensions, spacing, and padding across size variants |
| **Screen Reader** | VoiceOver, TalkBack, and ARIA accessibility specs |

> **Motion** and **Changelog** specs from uSpec are also supported but require additional inputs (After Effects data and version history respectively).

## How it works

```
You ──prompt──▶ Claude ──MCP──▶ Figma Desktop
                  │                    │
                  │  reads skill       │  extracts component
                  │  instructions      │  data, tokens, styles
                  │                    │
                  │  reasons about     │  imports template,
                  │  classification    │  renders spec
                  │                    │
                  ◀──screenshot────────┘  validates output
```

Claude connects to your Figma file through the [Figma Console MCP](https://github.com/southleft/figma-console-mcp), which provides 56+ tools for reading component structure, design tokens, variables, and styles — and for creating and modifying content directly in your file.

## Quick start

See the full **[Setup Guide](docs/setup-guide.md)** for detailed instructions. The short version:

### 1. Install the Figma Console MCP

```bash
npx figma-console-mcp@latest
```

You'll need a [Figma personal access token](https://www.figma.com/developers/api#access-tokens).

### 2. Configure Claude Desktop

Add to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "figma-console": {
      "command": "npx",
      "args": ["-y", "figma-console-mcp@latest"],
      "env": {
        "FIGMA_ACCESS_TOKEN": "figd_YOUR_TOKEN_HERE"
      }
    }
  }
}
```

### 3. Run the Desktop Bridge plugin in Figma

Open Figma Desktop → Plugins → Development → Figma Desktop Bridge

### 4. Get the template library

Duplicate the [claudeSpec Template](https://www.figma.com/community/file/1603925462078533207/uspec-template) from Figma Community into your team project and publish it as a library.

### 5. Start generating specs

Open a conversation with Claude and say:

> "I want to generate an anatomy spec for my button component. Here's the Figma link: [paste URL]. The template library is called 'claudeSpec Template' and it's enabled across my files."

Claude will handle the rest — connecting to Figma, extracting data, importing the template, rendering the spec, and validating with a screenshot.

## Example prompts

### First time setup
```
I need to set up claudeSpec. My template library file is here:
https://www.figma.com/design/[your-file-key]/claudeSpec-Template

Please search for the template components and save the configuration.
```

### Anatomy spec
```
Create an anatomy spec for this component:
https://www.figma.com/design/[file-key]/Components?node-id=123-456

It has enabled, hover, pressed, and disabled states with an optional leading icon.
```

### API spec
```
Generate an API spec for this component:
https://www.figma.com/design/[file-key]/Components?node-id=123-456

Document all configurable properties including size variants,
hierarchy levels, and boolean toggles.
```

### Color Annotation
```
Create a color annotation for this component:
https://www.figma.com/design/[file-key]/Components?node-id=123-456

Map tokens for Primary, Secondary, and Tertiary hierarchy variants
across Default and Disabled states.
```

### Structure spec
```
Generate a structure spec for this component:
https://www.figma.com/design/[file-key]/Components?node-id=123-456

Show spacing, padding, and sizing across all size variants (sm, md, lg, xl).
```

### Screen Reader spec
```
Create a screen reader spec for this component:
https://www.figma.com/design/[file-key]/Components?node-id=123-456

Cover Default, Disabled, and Loading states with VoiceOver,
TalkBack, and ARIA annotations.
```

## Skill files

The `skills/` directory contains the instruction files that tell Claude how to generate each spec type. These are adapted from uSpec's `.cursor/skills/` files with Claude-specific language.

Each skill file is a standalone markdown document. You can:

- **Paste it into a conversation** — copy the contents and include it in your prompt
- **Add it to a Claude Project** — attach as project knowledge for persistent access
- **Reference it from Claude Code** — load via the filesystem

The reference instruction files in the root directories (`anatomy/`, `api/`, `color/`, etc.) contain the detailed classification rules, note-writing guidelines, and validation checklists that the skills reference.

## How this differs from uSpec

| | uSpec | claudeSpec |
|---|---|---|
| **Agent** | Cursor (any model) | Claude (Opus recommended) |
| **Skill trigger** | `@create-anatomy` autocomplete | Conversational prompt |
| **MCP config** | Cursor MCP settings | Claude Desktop config or claude.ai connector |
| **Config storage** | `uspecs.config.json` file | Claude's memory or conversation context |
| **Multi-file** | One agent per file | Claude switches between files via Desktop Bridge |

The underlying pipeline is identical — same MCP tools, same template library, same extraction scripts, same rendering logic.

## Attribution

claudeSpec would not exist without [uSpec](https://github.com/redongreen/uSpec) by **[Ian Guisard](https://www.linkedin.com/in/iguisard/)** — a genuinely brilliant piece of work that pioneered the agent-to-Figma spec generation pipeline. Ian built the skill architecture, the template system, the extraction scripts, and the rendering logic while on the Fluent Core team at Microsoft. The entire concept of using AI agent skills to automate design system documentation — from component analysis to rendered specs — is his innovation. claudeSpec simply adapts his pipeline for Claude users. If you find this useful, go star [uSpec](https://github.com/redongreen/uSpec) and connect with Ian — he's doing some of the most forward-thinking work at the intersection of design systems and AI tooling.

The [Figma Console MCP](https://github.com/southleft/figma-console-mcp) by [Southleft](https://github.com/southleft) is the critical bridge that makes all of this possible — 56+ tools for reading and writing Figma data in real-time via WebSocket.

## License

MIT — see [LICENSE](LICENSE) for details.

---

*Created by [Katie Swanson](https://katieswanson.design) and Claude.*
