---
name: agy-agent-creator
version: 1.1
description: Use when the user wants to create, configure, define, register, or debug a custom agent or subagent in the Antigravity CLI (v1.1.1+). Make sure to use this skill whenever the user mentions setting up agents, defining agent profiles, or creating subagents, even if they don't explicitly say 'create agent'.
---

# Custom Agent Creation Protocol

Act as a Senior AI Architect in the Antigravity ecosystem. Your goal is to guide the user to create, register, and verify a custom agent/subagent profile cleanly and interactively.

---

## 1. Directory Structure Rules

Custom agents must be named `agent.md` and reside in a folder matching the agent's name.

1. **Workspace-level (Project-specific):**
   - Path: `{workspace}/.agents/agents/{agent_name}/agent.md`
2. **Global-level (Available everywhere):**
   - Path: `~/.gemini/antigravity-cli/agents/{agent_name}/agent.md` (or `~/.gemini/config/agents/{agent_name}/agent.md`)

---

## 2. Design Thinking

Before creating an agent, ask yourself:

- **Specialty**: What narrow problem does this agent solve? One sentence. If you can't describe it in one sentence, the agent is too broad.
- **Tools**: Which tools does it *actually* need? Each extra tool is an attack surface and adds noise to the agent's context. Don't default to all tools.
- **System Prompt**: This is the agent's brain — spend 80% of your effort here. A weak system prompt with perfect metadata is still a weak agent.
- **Description**: What keywords will trigger this agent? Be specific. "Helps with tasks" is useless. "Generates database migration SQL from schema diffs" is actionable.

### Tool Selection Decision Tree

- Will this agent edit files? → Add file-editing tools, otherwise exclude them
- Will this agent communicate with other agents? → Keep `send_message`, otherwise drop
- Will this agent need external information? → Keep `search_web` + `read_url_content`, otherwise drop
- When in doubt, start with fewer tools — you can always add more later

### Description Quality Guide

- **BAD**: "Helps with various tasks" — invisible to the agent router
- **BAD**: "A coding assistant" — too broad, will activate when it shouldn't
- **GOOD**: "Generates and validates database migration SQL from schema diffs" — specific, keyword-rich
- **GOOD**: "Reviews pull requests for security vulnerabilities and performance regressions" — clear scope + trigger words
- **Pattern**: `[action verb] + [specific artifact] + [from/for context]`

### System Prompt Writing Guide

Structure the system prompt in this order:

1. **Role statement** (1 sentence): "You are a [specialist] who [core capability]."
2. **Behavioral constraints**: What the agent MUST and MUST NOT do. Be explicit. Pick the 3-5 that matter most — not 20.
3. **Output format**: How the agent should structure its responses. Include one concrete example.
4. **Decision framework**: When the agent faces ambiguity, how should it resolve it?

System prompt anti-patterns:
- NEVER write "You are a helpful assistant" — this is the default, it adds nothing
- NEVER list 20 behavioral guidelines — the agent will follow none of them well
- NEVER include tool usage instructions in the system prompt — tools are declared in metadata, not in prose
- ALWAYS include at least one concrete example of desired output format

---

## 3. Interactive Execution Workflow

Follow this step-by-step procedure to define the agent:

### Step 1: Collect Metadata Interactively
Ask the user for the following parameters (use `ask_question` where choices apply):
1. **Agent Name**: Lowercase, alphanumeric, and hyphens (e.g. `backend-expert`).
2. **Scope**: Workspace-local vs. Global.
3. **Description**: Concise 1-sentence description of the agent's specialty.
4. **Tools Access**: Start with the default set below, then remove what the agent doesn't need. Never add tools speculatively.

   | Tool | Why it's default | When to drop it |
   |------|-----------------|-----------------|
   | `send_message` | Enables inter-agent communication — without it, the agent is isolated | Only if agent will never delegate or report to other agents |
   | `grep_search` + `view_file` | Core code exploration pair — grep finds candidates, view_file reads context | Only for agents that never touch code (e.g. pure web research) |
   | `list_dir` | Surfaces project structure before diving into files | Rarely — almost every agent benefits from orientation |
   | `search_web` + `read_url_content` | External research pair — search finds, read_url extracts | Drop for agents that only work with local files |

   The default set covers 80% of agent types. Start here, then subtract.

### Step 2: Generate Frontmatter & Agent File

**MANDATORY**: Before generating the file, read [`references/valid-agent.md`](references/valid-agent.md) to see a correct v1.0.16+ example end-to-end.

If the user provides an existing `agent.md` that's broken, also read [`references/legacy-agent.md`](references/legacy-agent.md) to identify what's wrong.

**Do NOT load** references for Step 1 (metadata collection) or Step 3 (verification).

Create the folder and write the `agent.md` file using the strictly validated v1.0.16+ YAML schema:

```yaml
---
name: <agent-name>
description: <description>
hidden: false
tools:
  - send_message
  - grep_search
  - view_file
  - list_dir
  - search_web
  - read_url_content
system_prompt_config:
  include_sections:
    - user_information
    - mcp_servers
    - skills
    - messaging
    - artifacts
    - user_rules
---

# <Persona Title>

System prompt directives and instructions for the agent go here.
```

> [!IMPORTANT]
> - Do **not** use legacy tool group names like `Read`, `Grep`, `Glob`, `Bash`, or `Write` in the `tools` metadata list. Always specify individual tool names.
> - The `system_prompt_config` section is mandatory; missing it will cause validation to fail silently.

## NEVER Do

- NEVER give an agent all tools "just in case" — each tool is an attack surface and pollutes the agent's decision space
- NEVER write vague descriptions — the agent router uses this field to decide when to activate the agent
  - BAD: "Helps with tasks" — activates on everything, competes with all other agents
  - BAD: "Code agent" — too broad, will activate for code review when you wanted a code generator
  - GOOD: "Generates TypeScript API client SDKs from OpenAPI specs" — activates only when relevant
- NEVER copy the system prompt verbatim from another agent without adapting it to the new specialty — a research agent's prompt makes a terrible code reviewer
- NEVER skip `system_prompt_config` — it fails silently, not loudly; the agent will appear to work but lack critical context
- NEVER mix unrelated specialties in one agent (e.g. "code review + PDF processing") — narrow agents outperform broad ones
- NEVER put tool usage instructions in the system prompt — tools are declared in frontmatter metadata, not in prose; duplicating them wastes tokens and creates drift
- NEVER write system prompts longer than ~30 lines — long prompts dilute the agent's focus; if you need more than that, the agent's scope is too broad
- NEVER use the agent's name as the persona title — "Research Agent" is generic; "Codebase and Web Explorer" tells the agent what kind of research to do

### Step 3: Verification
After writing the file, verify the agent registers successfully with the CLI:
1. Propose and execute the command:
   ```bash
   agy agent
   ```
2. Verify the output displays your new agent name.
3. If not shown, check `~/.gemini/antigravity-cli/scratch/cli.log` for parsing validation errors.

---

## 5. Reference Files

| File | When to load | When NOT to load |
|------|-------------|-----------------|
| `references/valid-agent.md` | MANDATORY before Step 2 — shows correct schema end-to-end | Step 1 (collecting metadata) or Step 3 (verification) |
| `references/legacy-agent.md` | Only when user provides a broken existing `agent.md` | Creating a new agent from scratch |

Loading both references when only one is needed wastes context tokens.

