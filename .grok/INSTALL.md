# Rust Skills for Grok

> Grok integration for `rust-skills`. Harness-agnostic: works with grok-cli and any
> Grok-based agent that loads a `GROK.md` / system-prompt instruction file.

## What's included

| File | Purpose |
|------|---------|
| `.grok/GROK.md` | The full instruction bundle — routing, meta-cognition, error reference, coding guidelines, MCP tooling. |
| `.grok/mcp.json` | actionbook MCP server config (enables live crate/version/news research). |
| `.grok/INSTALL.md` | This file. |

The instructions reference the skill files under `skills/<name>/SKILL.md` in this repo.
Keep the repo on disk so the agent can open the full skill content on demand.

## Installation

### Step 1 — Clone the repository

```bash
git clone https://github.com/actionbook/rust-skills.git ~/rust-skills
```

### Step 2 — Load GROK.md into your Grok agent

Pick whichever your harness supports:

**A. grok-cli — project instructions**

Copy the instructions to your project root (grok-cli auto-loads `GROK.md`):

```bash
cp ~/rust-skills/.grok/GROK.md /path/to/your/rust-project/GROK.md
```

Or reference it from your existing `GROK.md`:

```markdown
See `~/rust-skills/.grok/GROK.md` for Rust development guidelines.
```

**B. grok-cli — global config**

Add the file to your grok-cli settings (e.g. `~/.grok/settings.json`):

```json
{
  "instructions": ["~/rust-skills/.grok/GROK.md"]
}
```

> grok-cli config keys vary by version. If `instructions` is unsupported, fall back to
> copying `GROK.md` into the project root (method A).

**C. Any other Grok agent (API/SDK or custom harness)**

Concatenate `GROK.md` into your system prompt, or load it as a context/instruction file
via whatever mechanism your harness provides.

### Step 3 (optional, recommended) — Enable live research via MCP

Several skills (`rust-learner`, `rust-daily`, the `*-researcher` workflows) fetch live
data. To enable them, register the actionbook MCP server.

**grok-cli** — merge `.grok/mcp.json` into your MCP config:

```bash
cat ~/rust-skills/.grok/mcp.json
```

```json
{
  "mcpServers": {
    "actionbook": {
      "command": "npx",
      "args": ["-y", "@actionbookdev/mcp@latest"]
    }
  }
}
```

Add the `actionbook` entry to your harness's MCP server list (grok-cli reads MCP servers
from its settings file; consult your version's docs for the exact path). Requires Node.js
(`npx`).

Without MCP, the research skills degrade gracefully to instruction-only guidance.

## Verification

After installation, ask your Grok agent:

```
How do I fix E0382 in Rust?
```

Expected: it consults ownership concepts (m01-ownership), traces the cognitive layers,
and asks "who should own this data?" rather than just saying "clone it".

Then, if MCP is configured:

```
What's the latest version of tokio?
```

Expected: a live answer via the actionbook MCP server.

## Limitations

Grok integration is instruction-based. Features that depend on Claude Code's harness are
not available:

| Feature | Grok | Grok + MCP | Claude Code |
|---------|:----:|:----------:|:-----------:|
| Routing, error explanations, coding guidelines | ✅ | ✅ | ✅ |
| Live crate / version / news research | ❌ | ✅ | ✅ |
| Auto-triggering hooks | ❌ | ❌ | ✅ |
| Background agents / dynamic skill loading | ❌ | ❌ | ✅ |

Grok has no hook mechanism, so the auto-routing Claude Code does is reproduced in
`GROK.md` §3 as explicit rules the agent applies to each message.

## Links

- [Rust Skills GitHub](https://github.com/actionbook/rust-skills)
- [actionbook MCP](https://www.npmjs.com/package/@actionbookdev/mcp)
