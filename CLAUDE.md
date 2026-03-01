# N8N Workflow Automation Project

This project builds and manages n8n workflow automations through AI-assisted prompting. Workflows are created, validated, and deployed using two complementary tools: **n8n-skills** for domain knowledge and **n8n-mcp** for live n8n instance interaction.

## Project Structure

```
ProjectRoot/
  ModulN/               # Organizational folders (Modul1/, Modul2/, etc.)
    WorkflowName.json   # n8n workflow export files
  CLAUDE.md
  README.md
```

- Each `ModulN/` folder contains one or more `.json` workflow files
- Workflow files use descriptive PascalCase names (e.g., `WikiSearch.json`, `GameMaster.json`)
- JSON files follow n8n's standard export format (`name`, `nodes`, `connections`, `settings`, `meta`)
- Credentials are referenced by ID only — never store API keys or secrets in workflow JSON

## Available Tools

### n8n Skills — Knowledge Layer

Seven Claude Code skills auto-activate based on query context. Do not call them directly — they provide expert guidance automatically when relevant topics arise:

1. **Expression Syntax** — Correct `{{ }}` patterns, variables, 15 common mistakes
2. **MCP Tools Expert** — Effective usage of all 24 MCP tools
3. **Workflow Patterns** — Webhook processing, HTTP APIs, databases, AI agents, scheduled tasks
4. **Validation Expert** — Error types, validation profiles, iterative fixing
5. **Node Configuration** — Progressive discovery, property dependencies, top 20 nodes
6. **Code JavaScript** — Data access patterns, execution modes, 10 production patterns
7. **Code Python** — Standard library only, prefer JavaScript for 95% of cases

### n8n MCP Server — Action Layer

24 MCP tools providing live interaction with the n8n instance:

- **Discovery:** `search_nodes` (1000+ nodes), `get_node` (schemas, docs, versions)
- **Validation:** `validate_node`, `validate_workflow`
- **Templates:** `search_templates` (2700+), `get_template`, `n8n_deploy_template`
- **Workflow Management:** `n8n_create_workflow`, `n8n_get_workflow`, `n8n_update_partial_workflow`, `n8n_update_full_workflow`, `n8n_list_workflows`, `n8n_delete_workflow`
- **Testing & Ops:** `n8n_test_workflow`, `n8n_executions`, `n8n_autofix_workflow`, `n8n_validate_workflow`, `n8n_workflow_versions`
- **System:** `n8n_health_check`, `n8n_diagnostic`, `n8n_list_available_tools`, `tools_documentation`

**The principle:** Skills teach you HOW to build correctly. MCP tools let you DO it.

## Standard Workflow Process

### Building a New Workflow

1. **Discover** — Use `search_nodes` to find needed nodes by keyword
2. **Inspect** — Use `get_node` with `detail="standard"` to get node schemas (covers 95% of needs)
3. **Configure** — Build node configurations following the schema; use expression syntax skill for `{{ }}` patterns
4. **Validate nodes** — Use `validate_node` to check individual node configs before assembly
5. **Create** — Use `n8n_create_workflow` with nodes array and connections object
6. **Validate workflow** — Use `n8n_validate_workflow` immediately after creation
7. **Fix** — If validation fails, use `n8n_update_partial_workflow` to fix issues, then validate again
8. **Export** — Save the workflow JSON to the appropriate `ModulN/` folder in this repo

Build iteratively — do not attempt to construct an entire complex workflow in a single pass. The edit → validate → fix cycle is the standard loop.

### Modifying an Existing Workflow

1. Load the workflow from the repo or use `n8n_get_workflow` if it exists on the instance
2. Use `n8n_update_partial_workflow` for incremental changes (preferred — 17 operation types, 99% success rate)
3. Always validate after changes with `n8n_validate_workflow`
4. Update the JSON file in the repo

## Critical Rules

### nodeType Prefix Convention

- `search_nodes` uses the **short** prefix: `nodes-base.slack`, `nodes-langchain.agent`
- Workflow JSON uses the **full** prefix: `n8n-nodes-base.slack`, `@n8n/n8n-nodes-langchain.agent`
- Mixing these up causes "node not found" errors — always use the correct format for the context

### Expression Syntax

- Wrap expressions in `{{ }}` in node parameter fields: `{{ $json.email }}`
- Webhook data lives under `$json.body.*`, NOT `$json.*` directly
- Do NOT use `{{ }}` inside Code nodes — access data directly: `$json.email`
- Use bracket notation for field names with spaces: `$json['first name']`
- Cross-node references always need `.json`: `$node["NodeName"].json.fieldName`
- Expression fields start with `=` prefix: `={{ $json.body.email }}`

### AI Workflow Connections

- 8 AI connection types: `ai_languageModel`, `ai_tool`, `ai_memory`, `ai_outputParser`, `ai_embedding`, `ai_vectorStore`, `ai_document`, `ai_textSplitter`
- AI connections flow **TO the consumer** (reversed from visual expectation) — e.g., from "OpenAI Chat Model" to "AI Agent" via `ai_languageModel`
- Use AI connection types exclusively — NOT `main` connections — for AI sub-nodes
- Connect the language model BEFORE adding the AI Agent node
- Always add memory (`ai_memory`) for conversational workflows

### Validation

- Always validate after creating or updating workflows — no exceptions
- Use `profile: "ai-friendly"` to reduce false positives from AI-generated configs
- Auto-sanitization runs on every workflow update (fixes operator structures automatically)
- Use `n8n_autofix_workflow` with `applyFixes: false` first to preview fixes before applying

### Partial Updates (`n8n_update_partial_workflow`)

- Always include an `intent` parameter describing what you are changing
- Use smart semantic parameters for multi-output nodes:
  - IF nodes: `branch: "true"` or `branch: "false"`
  - Switch nodes: `case: 0`, `case: 1`, `case: N`
- Set properties to `undefined` (not `null`) to remove them
- Use `validateOnly: true` to preview changes before applying

### Code Nodes

- JavaScript: always return `[{json: {...}}]` format
- Data access: `$input.all()` (batch), `$input.first()` (single), `$input.item` (per-item mode)
- Python: standard library only — no `requests`, `pandas`, `numpy`, or any pip packages
- Prefer JavaScript for 95% of use cases

## Key MCP Tools Quick Reference

| Tool | Purpose | When to Use |
|------|---------|-------------|
| `search_nodes` | Find nodes by keyword | Starting any workflow build |
| `get_node` | Get node schema/docs | Configuring a node |
| `validate_node` | Check a node config | Before adding to workflow |
| `validate_workflow` | Check entire workflow | After every create/update |
| `n8n_create_workflow` | Create new workflow | New workflow from scratch |
| `n8n_update_partial_workflow` | Surgical edits | Any modification (preferred) |
| `n8n_autofix_workflow` | Auto-fix common issues | When validation finds errors |
| `search_templates` | Find community templates | Looking for starting points |
| `n8n_deploy_template` | Deploy template to instance | Fast-start from templates |
| `n8n_test_workflow` | Trigger workflow execution | Testing after build/deploy |

## Anti-Patterns

- **Don't** build entire workflows in one shot — iterate with the edit → validate → fix cycle
- **Don't** use `detail="full"` on `get_node` by default — `"standard"` covers 95% of needs
- **Don't** skip validation before deploying or activating a workflow
- **Don't** mix nodeType prefix formats between search tools and workflow JSON
- **Don't** use `{{ }}` expressions inside Code nodes — use direct variable access
- **Don't** give AI agents write access to databases in workflow designs
- **Don't** use `n8n_update_full_workflow` for small changes — use partial updates instead

## Templates as Starting Points

- Use `search_templates` to browse 2,700+ community workflow templates
- Deploy with `n8n_deploy_template` for fast starts — auto-upgrades node versions by default
- Templates deploy **inactive** — always configure credentials in the n8n UI before activation
- Review `autoFixStatus` and `fixesApplied` fields after deployment
