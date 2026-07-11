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

---

## 3. Interactive Execution Workflow

Follow this step-by-step procedure to define the agent:

### Step 1: Collect Metadata Interactively
Ask the user for the following parameters (use `ask_question` where choices apply):
1. **Agent Name**: Lowercase, alphanumeric, and hyphens (e.g. `backend-expert`).
2. **Scope**: Workspace-local vs. Global.
3. **Description**: Concise 1-sentence description of the agent's specialty.
4. **Tools Access**: Which tools the agent requires. Recommended default set:
   - `send_message`
   - `grep_search`
   - `view_file`
   - `list_dir`
   - `search_web`
   - `read_url_content`

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
- NEVER write vague descriptions ("helps with tasks", "general purpose agent") — the description determines when the agent activates; if it's vague, the agent is invisible
- NEVER copy the system prompt verbatim from another agent without adapting it to the new specialty — a research agent's prompt makes a terrible code reviewer
- NEVER skip `system_prompt_config` — it fails silently, not loudly; the agent will appear to work but lack critical context
- NEVER mix unrelated specialties in one agent (e.g. "code review + PDF processing") — narrow agents outperform broad ones

### Step 3: Verification
After writing the file, verify the agent registers successfully with the CLI:
1. Propose and execute the command:
   ```bash
   agy agent
   ```
2. Verify the output displays your new agent name.
3. If not shown, check `~/.gemini/antigravity-cli/scratch/cli.log` for parsing validation errors.

---

## 5. References & Resources
* **Legacy Example:** For a complete reference of a broken legacy configuration file, see [references/legacy-agent.md](references/legacy-agent.md).
* **Valid Example:** For a complete reference of a valid v1.0.16+ configuration file, see [references/valid-agent.md](references/valid-agent.md).

