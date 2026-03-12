# Setup Guide

Get claudeSpec running with Claude Desktop and Figma in about 10 minutes.

## Prerequisites

- **Figma Desktop** app (not the web version)
- **Claude Desktop** app ([download](https://claude.ai/download))
- **Node.js** 18+ installed
- A **Figma account** with edit access to the file you want to spec

## Step 1: Get a Figma personal access token

1. Go to [figma.com/developers/api#access-tokens](https://www.figma.com/developers/api#access-tokens)
2. Click **"Get personal access token"**
3. Enter a description like `claudeSpec`
4. Copy the token — it starts with `figd_`

> Copy it immediately. You can't see it again after closing the dialog.

## Step 2: Configure the MCP server in Claude Desktop

Claude Desktop uses MCP (Model Context Protocol) servers to connect to external tools. You need to add the Figma Console MCP to your configuration.

### Find your config file

| Platform | Path |
|----------|------|
| macOS | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Windows | `%APPDATA%\Claude\claude_desktop_config.json` |

If the file doesn't exist, create it.

### Add the MCP server

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

Replace `figd_YOUR_TOKEN_HERE` with your actual token from Step 1.

### Restart Claude Desktop

Quit and reopen Claude Desktop. You should see a hammer icon (🔨) in the chat input area indicating MCP tools are available.

## Step 3: Install the Desktop Bridge plugin in Figma

The Desktop Bridge plugin gives Claude real-time access to your Figma file data via WebSocket.

1. Open a terminal and run:
   ```bash
   npx figma-console-mcp@latest
   ```
   This downloads the package. Note the path it prints — you'll need the plugin manifest.

2. In Figma Desktop, go to **Plugins → Development → Import plugin from manifest…**

3. Navigate to the manifest file. It's typically at:
   ```
   ~/.npm/_npx/[hash]/node_modules/figma-console-mcp/figma-desktop-bridge/manifest.json
   ```
   The exact path is shown in the MCP status output.

4. The plugin should now appear under **Plugins → Development → Figma Desktop Bridge**

## Step 4: Set up the template library

claudeSpec renders documentation using Figma component templates. You need a copy of the template library in your Figma account.

1. Go to the [uSpec Template](https://www.figma.com/community/file/1603925462078533207/uspec-template) on Figma Community
2. Click **Open in Figma** to duplicate it to your drafts
3. Rename it to something like "claudeSpec Template" (optional)
4. Move it to your team or organization project
5. Publish it as a library:
   - Open the file → Assets panel → Click the book icon → **Publish library**

This makes the templates available for claudeSpec to import into any file.

## Step 5: Configure template keys

The first time you use claudeSpec, you need to connect it to your template library. Open Claude Desktop and say:

```
I want to set up claudeSpec. My template library file is here:
https://www.figma.com/design/[your-file-key]/claudeSpec-Template

Please connect to Figma, navigate to the template library,
search for all 8 template components, and save the configuration.
```

Claude will:
1. Verify the MCP connection
2. Navigate to your template file
3. Search for each template component (Anatomy, API, Properties, Color Annotation, Structure, Screen Reader, Motion, Changelog)
4. Extract the component keys
5. Detect the font family

You can ask Claude to remember these keys for future sessions, or provide them at the start of each conversation.

### Template keys reference

After setup, Claude will have a configuration like this:

```json
{
  "fontFamily": "Inter",
  "templateKeys": {
    "screenReader": "your-key-here",
    "colorAnnotation": "your-key-here",
    "anatomyOverview": "your-key-here",
    "apiOverview": "your-key-here",
    "structureSpec": "your-key-here",
    "changelog": "your-key-here",
    "propertyOverview": "your-key-here",
    "motionSpec": "your-key-here"
  }
}
```

## Step 6: Generate your first spec

1. Open Figma Desktop with your component file
2. Run the **Desktop Bridge** plugin (Plugins → Development → Figma Desktop Bridge)
3. Open Claude Desktop and paste a prompt like:

```
Create an anatomy spec for this button component:
https://www.figma.com/design/[file-key]/Components?node-id=123-456

The template library keys are:
- anatomyOverview: [your key]
- Font family: Inter

It's a button with Size (sm/md/lg/xl), Hierarchy (Primary/Secondary/Tertiary),
and State (Default/Hover/Focused/Disabled/Loading) variants.
```

Claude will connect to Figma, extract the component data, import the template, render the spec, and take a screenshot to validate.

## Running multiple specs

Start a **fresh conversation** for each spec. Each skill is context-intensive, and accumulated history can degrade output quality. This mirrors the same best practice from uSpec.

## Multi-file workflow

The Desktop Bridge plugin supports multiple files simultaneously. Run the plugin in both your component file and your template library file. Claude can switch between them using `figma_navigate`.

## Troubleshooting

### Claude doesn't show MCP tools (no 🔨 icon)

- Verify `claude_desktop_config.json` is valid JSON (no trailing commas)
- Restart Claude Desktop completely (quit, not just close)
- Check that Node.js 18+ is installed: `node --version`

### Desktop Bridge not connecting

- Confirm you're using **Figma Desktop**, not the web app
- Close and reopen the Desktop Bridge plugin
- Make sure only one instance of Figma Desktop is running
- Check your Figma access token is valid and not expired

### Template import fails

- Verify the template library is published (not just saved)
- Ensure the library is enabled in the target file (Assets panel → library toggle)
- Confirm the template component keys are correct

### Claude times out during rendering

- Large components with many variants can hit the 30-second execution timeout
- Try specifying which variants to focus on in your prompt
- Start a fresh conversation to clear context

### Specs render but look wrong

- Check the Desktop Bridge is connected to the correct file
- Verify you're on the right page in Figma
- Don't interact with the Figma frame while Claude is rendering — it can break node references

## Alternative: Using claude.ai instead of Claude Desktop

If you use claude.ai (Pro, Team, or Enterprise), you can connect the Figma MCP directly through the web interface:

1. Go to claude.ai Settings → Connected Apps
2. Enable the **Figma** connector
3. Authorize with your Figma account

This skips all local MCP configuration. The Desktop Bridge plugin is still required in Figma Desktop for the real-time connection.

## Next steps

- Read the [skill files](../skills/) to understand what each spec type generates
- Check the [lessons learned](lessons-learned.md) for tips and gotchas from real-world usage
- Explore the reference instruction files (`anatomy/`, `api/`, etc.) for detailed classification rules
