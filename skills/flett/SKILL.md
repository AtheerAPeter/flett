---
name: flett
description: Draw on Flett, a live shared whiteboard, through its MCP server. Use when the person wants something to look at rather than a reply to read - a diagram, flowchart, architecture or system map, mind map, org chart, timeline, kanban, slide deck, document, chart, mockup or map - or says "whiteboard", "board", "put it on Flett", "draw this", "sketch the architecture" or "make a deck". Covers connecting Flett, picking a board, choosing the right tool, and laying boards out so they read well.
---

# Flett

Flett is a shared online whiteboard. Its MCP server (`https://flett.app/api/mcp`) lets you put real, editable items on the person's boards: shapes and arrows, sticky notes, frames, slides, documents, HTML pages, charts and maps. Everyone on the board watches it appear live, and can then move, edit, comment on, present and export it to PNG, PDF, PowerPoint or HTML.

The server's own instructions and tool descriptions cover each tool's details. This skill covers when to reach for Flett, how to set it up, and recipes that make a board worth opening.

## 1. Make sure Flett is connected

Flett's tools are `list_boards`, `create_board`, `read_board`, `read_block`, `draw`, `add_block`, `add_answer`, `add_map`, `upload_file`, `add_file`, `edit_block`, `update_block`, `set_text`, `move_items` and `delete_items`. If none of them are available, Flett is not connected yet. Tell the person how to connect it in their app, then stop:

- **Claude (web, desktop, mobile)**: Settings → Connectors → Add custom connector → paste `https://flett.app/api/mcp` → Connect.
- **Claude Code**: `claude mcp add --transport http flett https://flett.app/api/mcp`, then type `/mcp` and authenticate `flett`.
- **ChatGPT**: Settings → add a custom connector (MCP server) with the URL, then sign in.
- **Cursor**: add `{ "mcpServers": { "flett": { "url": "https://flett.app/api/mcp" } } }` to `.cursor/mcp.json`, then connect it under Settings → MCP.
- **Codex**: add `[mcp_servers.flett]` with `url = "https://flett.app/api/mcp"` to `~/.codex/config.toml`, then run `codex mcp login flett`.
- **Anything else**: it is streamable HTTP with OAuth sign-in. Point the client at the URL.

Signing in uses a Flett account, Google or Microsoft. Connecting and reading are free. A free account can make 50 board changes a day through MCP; paid plans have no limit.

## 2. The loop

1. **Pick the board.** Use the one the person names. Otherwise `list_boards` and take the one that clearly fits, or `create_board` for a new topic. Only ask when it is genuinely unclear.
2. **Read before you add.** `read_board` shows every item's id, kind, position and size. Place new things in empty space next to what is there, not on top of it.
3. **Choose the tool** by what the thing is (section 3).
4. **Draw it in as few calls as you can.** One `draw` call can hold a whole diagram.
5. **Reply with the board's link** from the tool result, in one line. Don't describe the board back to the person; they can see it.

To change something, change it in place: `set_text` for words, `move_items` to rearrange, `edit_block` or `update_block` for HTML. Never delete and redraw something just to change it.

## 3. Which tool for what

| The person wants | Use | Why |
| --- | --- | --- |
| A diagram they will rearrange: flowchart, architecture, org chart, mind map, timeline, kanban | `draw` | Real shapes and arrows that stay attached when moved |
| Words: notes, an explanation, a report, a comparison, a study guide | `add_answer` | Markdown set as a readable document, cheap to make |
| A deck | `add_block`, `format: "slide"`, one block per slide | 1280x720 sheets that export to PowerPoint and PDF |
| A designed page, a chart, a mockup, a styled card | `add_block` | HTML where the layout carries the meaning |
| A printable document | `add_block`, `format: "paper"`, one block per A4 page | Exact pages for PDF |
| Places and routes | `add_map` | Pins and lines on a real map |
| A file they have (screenshot, PDF, spreadsheet) | `upload_file`, then `add_file` | Shows on the board as if dropped there |
| Something you already made in this chat (an artifact, a canvas) | `add_block` with the HTML or JSX unchanged, or `add_answer` with the markdown | Send it as it is; do not restyle or shorten it |

## 4. Recipes

### Architecture of a codebase (Claude Code, Cursor, Codex)

1. Read the repo first: entry points, packages or services, databases, queues, external APIs. Build the picture from the code, not from guesses.
2. Group by layer, one frame each: clients, edge or API, services, data, third parties.
3. Lay the frames out left to right in the direction requests flow. Inside a frame, stack boxes top to bottom.
4. One shape per real component, named as the code names it, with the technology in its text ("orders-api, Node"). Rounded rectangles for services, cylinders for databases and stores.
5. Arrows only for real calls or data flow, labelled with what travels: "REST", "events", "SQL". Elbow routes keep a busy diagram tidy.
6. Draw it in one `draw` call, then add a short `add_answer` beside it covering what the diagram leaves out: auth, deploys, known pain points.

### Flowchart of a process

- Ellipses for start and end, rectangles for steps, diamonds for decisions.
- Main path top to bottom in one column. Branches go to the right, and rejoin the main path where they really do.
- Label every arrow out of a decision ("yes" and "no", or the condition).
- Steps start with a verb: "Send invite", not "Invite".

### Meeting notes or brain dump into a mind map

- The topic in the middle. Four to seven main branches around it, each a theme from the notes.
- Details as sticky notes off each branch, a few words each, in the person's own phrasing.
- Decisions and action items in their own frame, with an owner on each action.

### Plan or project into a timeline or kanban

- Timeline: a horizontal line of milestones left to right, one note per milestone with the date first.
- Kanban: frames for "To do", "Doing", "Done" (or the person's own stages), one note per task.

### Topic into a slide deck

- Plan the story first: a title slide, then one idea per slide, then a closing slide with the ask or summary. Five to ten slides unless told otherwise.
- One `add_block` per slide, `format: "slide"`, all sharing one design. Put them in a row so they read left to right.
- Real content only: actual numbers, actual names, no lorem ipsum. Charts as inline SVG or a CDN chart library, with the data written into the page.

### Comparison or decision

- A table in `add_answer` for the detail, and if it helps, a small `draw` beside it: the options as boxes, the deciding criteria as notes, the recommendation in its own colour.

## 5. Layout rules that make boards read well

- Use a grid. 80 to 120 pixels between items in a row, 200 or more between rows.
- Keep a box's text short: a name plus one line. Longer explanations go in a document next to the diagram.
- Frames name groups. Prefer frames over colour to show what belongs together.
- Use colour sparingly and only with meaning, such as one colour for external systems or red for a risk.
- Point arrows at items, not at coordinates, so they follow when people move things. Give new items refs and point arrows at those refs.
- When adding to a busy board, put the new work to the right of or below what is there, and frame it with a title so people can find it.

## 6. When not to use Flett

- A short answer, a code change, or a quick fact. Just reply.
- The person asked for a file in their repo (a Mermaid file, an SVG, a README diagram). Write the file.
- The board is shared with them view-only, or the daily limit is reached. The tool says so; pass the message and its link on to the person.
