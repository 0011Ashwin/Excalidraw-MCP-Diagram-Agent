# 01 — Excalidraw MCP Diagram Agent

Describe any system, workflow, or architecture in plain text and get a beautiful, editable diagram in Excalidraw automatically.

This project is a Claude Code workflow that uses the Excalidraw MCP (Model Context Protocol) server to turn natural-language descriptions into diagrams. You stay in the conversation, Claude draws the diagram, and you can open the result in Excalidraw to edit boxes, arrows, and labels by hand.

---

## Table of contents

- [What it does](#what-it-does)
- [Why this exists](#why-this-exists)
- [Architecture](#architecture)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Setup](#setup)
- [Usage](#usage)
- [Starter prompts](#starter-prompts)
- [Working with diagrams](#working-with-diagrams)
- [Sample diagram in this repo](#sample-diagram-in-this-repo)
- [Going deeper](#going-deeper)
- [Troubleshooting](#troubleshooting)
- [What you will learn](#what-you-will-learn)
- [Project files](#project-files)
- [Contributing](#contributing)
- [License](#license)

---

## What it does

Every builder needs to visualize systems. Instead of manually dragging boxes in Excalidraw, you describe what you want and Claude creates it for you through the Excalidraw MCP server.

Typical uses:

- Content and publishing pipelines
- System and service architecture maps
- RAG and data-flow diagrams
- Multi-agent workflows
- Product or onboarding overviews

The generated diagram is a real Excalidraw scene. You can keep iterating in chat or open the file and tweak layout, colors, and labels yourself.

---

## Why this exists

Hand-drawing architecture diagrams is slow and easy to abandon. This workflow is meant to:

1. Get a first useful diagram from a plain-language description.
2. Keep the output fully editable (not a locked image).
3. Make diagram style reusable across projects via `CLAUDE.md` or a skill file.
4. Teach how MCP servers plug into Claude Code for tools beyond text.

---

## Architecture

The repo includes `diagram.excalidraw`, a starter Excalidraw scene whose root element is labeled **MCP Diagram Agent**. That file is both a sample output and a picture of the agent itself.

End-to-end flow:

```
You (plain-language request)
        |
        v
+----------------------+
|     Claude Code      |
|  (conversation +     |
|   tool calling)      |
+----------------------+
        |
        |  MCP tools
        v
+----------------------+
| Excalidraw MCP       |
| server               |
| create / update      |
| elements, export     |
+----------------------+
        |
        v
+----------------------+
| Excalidraw scene     |
| (.excalidraw file)   |
| boxes, arrows, text  |
+----------------------+
        |
        v
You open and edit in Excalidraw
```

How the pieces connect:

| Piece | Role |
| --- | --- |
| You | Describe the system, workflow, or architecture in chat. |
| Claude Code | Interprets the request, plans the layout, and calls MCP tools. |
| Excalidraw MCP server | Creates and updates rectangles, text, arrows, and groups. |
| `.excalidraw` file | Portable scene you can open at [excalidraw.com](https://excalidraw.com) or in a local Excalidraw app. |

`diagram.excalidraw` in this repository is a minimal valid scene: a single rectangle containing the text **MCP Diagram Agent**. Use it to confirm that files from this workflow open correctly, or as a seed scene to extend.

---

## Features

- Natural-language to Excalidraw diagrams
- Editable output (not a static screenshot)
- Works with workflows, architectures, and multi-agent systems
- Color coding, grouping, and labeled arrows from the prompt
- Reusable style via `CLAUDE.md` or a Claude Code skill
- Local MCP setup — no custom backend to host

---

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed and authenticated
- [Node.js](https://nodejs.org/) 18 or newer
- npm (comes with Node.js)
- Network access so Claude Code can reach the Excalidraw MCP server package
- Optional: a browser or the Excalidraw desktop/app to open `.excalidraw` files

Confirm Node.js:

```bash
node -v
```

You should see `v18` or higher.

---

## Setup

About five minutes the first time.

### 1. Install the Excalidraw MCP server

```bash
npm install -g @anthropic-ai/mcp-server-excalidraw
```

If you prefer not to install globally, you can skip this step and let `npx` fetch the package when Claude Code starts the server (see the next command).

### 2. Register the server with Claude Code

```bash
claude mcp add excalidraw -- npx @anthropic-ai/mcp-server-excalidraw
```

This adds an MCP server named `excalidraw` to your Claude Code configuration.

### 3. Verify the connection

Open Claude Code and run:

```
/mcp
```

You should see `excalidraw` listed as a connected server. If it is missing or shows an error, see [Troubleshooting](#troubleshooting).

### 4. (Optional) Clone this repo

```bash
git clone <this-repository-url>
cd <repository-directory>
```

You do not need the repo to use the MCP server. Cloning it gives you this README, the sample `diagram.excalidraw`, and a place to keep project-specific `CLAUDE.md` or skill files.

---

## Usage

1. Open Claude Code in a project directory (this repo or any other).
2. Confirm the Excalidraw MCP server is connected (`/mcp`).
3. Paste one of the [starter prompts](#starter-prompts) or describe your own system.
4. Ask Claude to create or update the diagram. Be specific about:
   - Steps or components
   - Arrows and data flow
   - Grouping
   - Colors (for example research = blue, creation = green)
5. Open the generated `.excalidraw` file in Excalidraw and adjust layout if needed.
6. Iterate in chat: “move distribution to the right”, “add a cache between embedder and vector DB”, and so on.

Prompt tips that produce better diagrams:

- List components as bullets, not a long paragraph.
- Say how things connect (“arrow from A to B labeled embeddings”).
- Ask for grouping of related boxes.
- Name a color for each stage or layer.
- Request a left-to-right or top-to-bottom layout.
- After the first draft, ask for spacing and alignment fixes instead of starting over.

---

## Starter prompts

Open Claude Code and paste any of these.

### Starter prompt 1: Simple workflow

```
Create an Excalidraw diagram showing a content creation pipeline:
1. Idea capture (from phone, Twitter, conversations)
2. Research phase (Grok scans X, Claude analyzes)
3. Script writing (Claude drafts, I refine)
4. Recording and editing
5. Distribution across 6 platforms

Use arrows between each step. Color code: research in blue, creation in green, distribution in orange.
```

### Starter prompt 2: System architecture

```
Create an Excalidraw diagram showing a RAG (Retrieval Augmented Generation) system:
- User uploads PDFs and notes
- Documents get chunked and embedded
- Embeddings stored in a vector database
- User asks a question
- System retrieves relevant chunks
- LLM generates answer using retrieved context

Show the data flow with arrows. Group related components together.
```

### Starter prompt 3: Agent workflow

```
Create an Excalidraw diagram showing a multi-agent research system:
- Orchestrator agent receives a research question
- Spawns 3 sub-agents: web researcher, academic paper finder, social media scanner
- Each sub-agent returns findings
- Synthesizer agent combines all findings into a report
- Report goes to the user

Show the agents as separate boxes with arrows showing communication flow.
```

### Starter prompt 4: Extend the sample in this repo

```
Open diagram.excalidraw in this repository. It currently has a single box
labeled "MCP Diagram Agent". Expand it into an architecture diagram with:

- User
- Claude Code
- Excalidraw MCP server
- .excalidraw scene file

Connect them with arrows that match the flow in the README architecture
section. Keep the existing "MCP Diagram Agent" box as the title or hub.
```

---

## Working with diagrams

### File format

Excalidraw scenes are JSON documents with a `.excalidraw` extension. They store:

- `type` / `version` / `source` metadata
- `elements` — rectangles, text, arrows, diamonds, and other shapes
- `appState` — theme, grid, default stroke and fill
- `files` — embedded images, if any

You can commit these files to git, open them at [excalidraw.com](https://excalidraw.com) (Load → Open), or keep generating updates through Claude.

### Suggested iteration loop

1. Generate a rough diagram from a structured prompt.
2. Ask Claude to fix overlap, alignment, and label length.
3. Open the file in Excalidraw for pixel-level tweaks.
4. Save; next chat turn can keep editing the same scene if the MCP server supports updates.

### Style conventions that work well

- One idea per box; short labels (two to five words).
- Consistent shape meaning (rectangles = services, rounded = users, diamonds = decisions).
- A small, repeated color palette rather than a new color per box.
- Arrows labeled only when the relationship is not obvious.
- Groups or containers around layers (ingest, retrieve, generate).

---

## Sample diagram in this repo

`diagram.excalidraw` is a valid Excalidraw v2 scene checked in as a reference.

What it contains today:

- One rectangle (`id: 1`) at position `(100, 100)`, size `200×100`
- One centered text element (`id: 2`) with the label **MCP Diagram Agent**
- Light theme, white background, black stroke, solid fill

How to open it:

1. Go to [excalidraw.com](https://excalidraw.com).
2. Use the menu to open or drop `diagram.excalidraw`.
3. You should see a single box titled MCP Diagram Agent.

That scene is intentionally small so you can verify the format and then grow it into the architecture described above.

---

## Going deeper

Once the basics work:

- **Project style guide** — add a `CLAUDE.md` in the repo with your preferred colors, layout direction, fonts, and naming rules so every new diagram matches.
- **Skill file** — build a Claude Code skill that always generates diagrams in your visual language (same palette, same shape meanings).
- **Architecture-first projects** — start each new product or service by describing the system in chat and committing the `.excalidraw` file next to the code.
- **Prompt library** — keep domain-specific prompts (data pipelines, auth, billing, agents) in this README or a `prompts/` folder.

Example `CLAUDE.md` snippet you can adapt:

```
# Diagram style

- Layout: left to right for pipelines, top to bottom for stacks.
- Colors: ingest #a5d8ff, compute #b2f2bb, storage #ffd8a8, user #eebefa.
- Shapes: rectangle = service, rounded rectangle = actor, diamond = decision.
- Text: short labels, title case, no sentences inside boxes.
- Always group related components and label the group.
```

---

## Troubleshooting

**`excalidraw` does not appear in `/mcp`**

- Re-run `claude mcp add excalidraw -- npx @anthropic-ai/mcp-server-excalidraw`.
- Restart Claude Code after changing MCP config.
- Confirm Node.js 18+ is on your `PATH` (`node -v`).

**MCP server starts then exits**

- Run the server yourself to read the error: `npx @anthropic-ai/mcp-server-excalidraw`.
- Check that npm can download the package (proxy, registry, or network issues).

**Claude talks about the diagram but never calls tools**

- Ask explicitly: “Use the Excalidraw MCP tools to create this diagram.”
- Confirm the server status is connected, not disabled.

**Generated layout is cramped or overlapping**

- Ask for more spacing, a wider canvas, or a single row/column layout.
- Open the file in Excalidraw and use align/distribute, then continue in chat.

**`.excalidraw` file will not open**

- Confirm it is JSON with `"type": "excalidraw"` at the top level.
- Compare with `diagram.excalidraw` in this repo, which is a known-good minimal scene.

---

## What you will learn

- How MCP servers attach to Claude Code and expose tools
- How to use AI for visual thinking, not only text
- How to set up reusable diagram workflows
- How Excalidraw scenes are structured and versioned in git
- How to encode visual style so diagrams stay consistent across a team

---

## Project files

| File | Purpose |
| --- | --- |
| `README.md` | Project overview, setup, usage, and architecture notes |
| `diagram.excalidraw` | Sample Excalidraw scene (MCP Diagram Agent box) |

This repository is documentation and a sample scene around the Excalidraw MCP + Claude Code workflow. Runtime behavior lives in Claude Code and the published MCP server, not in application source inside this repo.

---

## Contributing

Improvements to the docs, starter prompts, and sample scene are welcome.

1. Keep setup steps accurate for current Claude Code and the Excalidraw MCP server.
2. Prefer concrete prompts that someone can paste unchanged.
3. If you change `diagram.excalidraw`, make sure it still opens on [excalidraw.com](https://excalidraw.com).
4. Do not add secrets or machine-specific MCP config to the repo.

---

## License

Use and adapt this workflow and documentation in your own projects unless a separate license file in the repository says otherwise.