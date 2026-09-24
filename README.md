# AI-Devkit

AI-Devkit is a growing collection of reusable Codex skills and focused custom agents. It serves two purposes: a practical personal toolkit for recurring work and a public, inspectable example of how to design agent workflows with clear ownership, bounded delegation, and evidence-backed verification.

The current repository state establishes the developer foundation. It includes six parent skills and six supporting custom agents. Everyday, news, and personal-finance workflows are planned next, with private user profiles kept outside the repository.

## How It Works

Skills own complete workflows. A matching skill can handle a task directly or delegate a narrow investigation, implementation, review, or evaluation to a custom agent. The parent skill remains responsible for scope, decisions, synthesis, and the final response.

```mermaid
flowchart LR
  User["User request"] --> Match["Codex skill matching"]
  Match --> Parent["Parent skill"]
  Parent --> Work["Direct workflow"]
  Parent -->|"Bounded delegation when useful"| Agent["Custom agent"]
  Agent -->|"Evidence or scoped result"| Parent
  Work --> Result["Final response"]
  Parent --> Result
```

The diagram shows the intended ownership model. Custom-agent availability and tool access still depend on the active Codex environment.

## Developer Skills

| Skill | Purpose |
| --- | --- |
| [`codebase-map`](skills/codebase-map/SKILL.md) | Map repository structure, entrypoints, architecture, commands, and contribution paths. |
| [`api-integration-builder`](skills/api-integration-builder/SKILL.md) | Design, implement, review, and debug reliable REST, GraphQL, RPC, SDK, and webhook integrations. |
| [`docs-writer-dev`](skills/docs-writer-dev/SKILL.md) | Create and audit developer documentation, setup guides, API references, runbooks, and architecture diagrams. |
| [`prompt-engineer`](skills/prompt-engineer/SKILL.md) | Design, revise, debug, and evaluate prompts, tool policies, structured outputs, and agent instructions. |
| [`mcp-server-builder`](skills/mcp-server-builder/SKILL.md) | Design, build, secure, test, connect, and deploy Model Context Protocol servers. |
| [`plugin-packager`](skills/plugin-packager/SKILL.md) | Assemble, validate, install, test, and prepare Codex or Agent Plugin packages for release. |

Each skill includes `agents/openai.yaml` metadata for display and invocation in supported Codex surfaces. Exact discovery and invocation behavior depends on the installed Codex version.

## Custom Agents

| Agent | Access | Responsibility |
| --- | --- | --- |
| [`repo_explorer`](.codex/agents/repo_explorer.toml) | Read-only | Gather repository structure, execution paths, commands, and file-level evidence. |
| [`implementation_worker`](.codex/agents/implementation_worker.toml) | Workspace write | Apply an approved, bounded change while preserving existing work. |
| [`test_and_verification`](.codex/agents/test_and_verification.toml) | Workspace write | Run proportionate checks, classify failures, and report worktree impact. |
| [`security_reviewer`](.codex/agents/security_reviewer.toml) | Read-only | Trace trust boundaries and report evidence-backed security findings. |
| [`docs_auditor`](.codex/agents/docs_auditor.toml) | Read-only | Compare documentation, examples, and diagrams with repository behavior. |
| [`eval_runner`](.codex/agents/eval_runner.toml) | Workspace write | Execute frozen evaluation contracts and expose regressions, scores, cost, and uncertainty. |

`workspace-write` identifies the Codex sandbox available to an agent. Each agent definition adds narrower behavioral limits, including restrictions on source edits, dependencies, external systems, Git operations, and destructive actions.

## Repository Structure

```text
AI-Devkit/
|-- .codex/
|   `-- agents/             # Project-scoped custom agent definitions
|-- skills/
|   |-- api-integration-builder/
|   |-- codebase-map/
|   |-- docs-writer-dev/
|   |-- mcp-server-builder/
|   |-- plugin-packager/
|   `-- prompt-engineer/
`-- README.md
```

Every skill directory currently contains:

```text
skill-name/
|-- SKILL.md                # Trigger description and workflow instructions
`-- agents/
    `-- openai.yaml         # User-facing skill metadata
```

## Local Installation

This repository does not yet include an automated installer. Review the files before installing them into a personal Codex environment. Run the commands below from the cloned repository root.

The locations and invocation behavior in this section follow the current [Codex skill documentation](https://learn.chatgpt.com/docs/build-skills) and [Codex subagent documentation](https://learn.chatgpt.com/docs/agent-configuration/subagents). The copy commands have been tested against an isolated home directory; automatic discovery still depends on the user's Codex version and configuration.

### Install Skills

Current Codex documentation identifies `$HOME/.agents/skills` as the user-level skill location. Copy the skill directories you want to use:

```bash
mkdir -p "$HOME/.agents/skills"
cp -R skills/codebase-map "$HOME/.agents/skills/"
cp -R skills/api-integration-builder "$HOME/.agents/skills/"
cp -R skills/docs-writer-dev "$HOME/.agents/skills/"
cp -R skills/prompt-engineer "$HOME/.agents/skills/"
cp -R skills/mcp-server-builder "$HOME/.agents/skills/"
cp -R skills/plugin-packager "$HOME/.agents/skills/"
```

### Install Custom Agents

Current Codex documentation supports project-scoped definitions under `.codex/agents` and personal definitions under `$HOME/.codex/agents`. To make these definitions available from other projects, copy them to the personal directory:

```bash
mkdir -p "$HOME/.codex/agents"
cp .codex/agents/*.toml "$HOME/.codex/agents/"
```

Open a new Codex task after installation. Current Codex documentation states that skill changes are detected automatically and recommends restarting Codex when a new skill does not appear.

The agent definitions currently select `gpt-5.6-terra` with medium reasoning effort. Review those settings before installation if that model is unavailable in the target environment.

## Usage

Current Codex versions can select a skill implicitly when a request matches the skill's description:

```text
Map this repository and show me the safest contribution path.
```

Skills can also be invoked explicitly in Codex with `$skill-name`:

```text
Use $prompt-engineer to review this tool-using system prompt.
```

More examples:

```text
Connect this service to the GitHub REST API and handle retries and rate limits.

Write an architecture guide for this repository and include a readable Mermaid diagram.

Build a local stdio MCP server for these read-only project operations.

Package these skills and MCP components as one installable plugin.
```

When a skill delegates work, the custom agent returns a scoped result to the parent skill. The parent reconciles the evidence and produces the final answer.

## Design Principles

- **Parent ownership:** skills retain final scope, judgment, and synthesis.
- **Bounded delegation:** custom agents receive narrow objectives, explicit permissions, and return contracts.
- **Evidence before claims:** repository behavior, commands, documentation, and evaluations are checked rather than assumed.
- **Least necessary access:** read-only agents stay read-only; write-capable agents remain constrained by task scope.
- **Independent checks:** implementation, verification, security review, documentation audit, and evaluation remain distinct responsibilities.
- **Public-safe personalization:** future personal preferences will live outside Git, while planned repository templates will contain placeholders and generic workflows.

## Roadmap

| Phase | Status | Planned work |
| --- | --- | --- |
| Developer skills | Complete | Six reusable developer workflows. |
| Developer subagents | Complete | Six focused agents connected to the parent skills. |
| Everyday skills | Planned | Decision support, research briefs, writing, learning, and news. |
| Profile system | Planned | Private local profiles with public placeholder templates and opt-in updates. |
| Financial skills | Planned | Personal-finance review and budgeting with a separate private financial profile. |
| Distribution | Planned | Automated installation, plugin packaging, clean-user testing, and release documentation. |

The repository does not currently include a release package, automated installer, contribution guide, or license. Those surfaces will be added and validated before a public release.

## Privacy Direction

Developer skills and agents do not use personal profiles. Planned everyday and financial skills will follow a hybrid profile model:

- generic behavior and profile schemas remain public;
- completed personal profiles remain outside the repository;
- skills read only the profile sections relevant to the current task;
- current user instructions override saved preferences;
- profile changes require explicit confirmation;
- financial context remains separate from everyday preferences.

Until that profile system is implemented, no profile files or setup commands should be treated as available features.
