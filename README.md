# AI-Devkit

AI-Devkit is a growing collection of reusable Codex skills and focused custom agents. It serves two purposes: a practical personal toolkit for recurring work and a public, inspectable example of how to design agent workflows with clear ownership, bounded delegation, and evidence-backed verification.

The repository includes completed developer and everyday foundations plus prompt-driven financial workflows. It currently provides twelve parent skills and twelve supporting custom agents, with one of four financial workflows implemented. Private user profiles remain outside the repository.

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

## Everyday Skills

| Skill | Purpose |
| --- | --- |
| [`decision-helper`](skills/decision-helper/SKILL.md) | Frame choices, compare tradeoffs, test uncertainty and reversibility, rank options, and identify a practical next step. |
| [`research-brief`](skills/research-brief/SKILL.md) | Investigate questions, evaluate sources, reconcile evidence, verify claims, and produce a cited brief. |
| [`writing-editor`](skills/writing-editor/SKILL.md) | Proofread, copyedit, restructure, critique, or rewrite text while preserving meaning, facts, citations, and voice. |
| [`learning-coach`](skills/learning-coach/SKILL.md) | Explain concepts, build study plans, guide practice, assess work, diagnose misconceptions, and adapt feedback to demonstrated understanding. |
| [`news-brief`](skills/news-brief/SKILL.md) | Produce current, sourced briefings that cluster events, rank material developments, explain context and uncertainty, and adapt to saved interests. |

Everyday skills read only their relevant section plus `Communication` from a private `personal.md`. The current request always takes precedence, and profile changes require explicit confirmation.

## Financial Skills

| Skill | Purpose |
| --- | --- |
| [`investing-education`](skills/investing-education/SKILL.md) | Explain investing concepts and compare user-supplied hypothetical scenarios with transparent assumptions, calculations, uncertainty, and risks, without recommending an action. |

Financial skills use the current request as the sole source of financial circumstances and numbers. They may read communication preferences from `personal.md` and workflow preferences from version 2 of an optional `finance.md`, but they never store prompt-supplied financial facts. Every financial response includes the visible disclaimer and remains educational and neutral.

## Custom Agents

| Agent | Access | Responsibility |
| --- | --- | --- |
| [`repo_explorer`](.codex/agents/repo_explorer.toml) | Read-only | Gather repository structure, execution paths, commands, and file-level evidence. |
| [`implementation_worker`](.codex/agents/implementation_worker.toml) | Workspace write | Apply an approved, bounded change while preserving existing work. |
| [`test_and_verification`](.codex/agents/test_and_verification.toml) | Workspace write | Run proportionate checks, classify failures, and report worktree impact. |
| [`security_reviewer`](.codex/agents/security_reviewer.toml) | Read-only | Trace trust boundaries and report evidence-backed security findings. |
| [`docs_auditor`](.codex/agents/docs_auditor.toml) | Read-only | Compare documentation, examples, and diagrams with repository behavior. |
| [`eval_runner`](.codex/agents/eval_runner.toml) | Workspace write | Execute frozen evaluation contracts and expose regressions, scores, cost, and uncertainty. |
| [`tradeoff_analyst`](.codex/agents/tradeoff_analyst.toml) | Read-only | Compare bounded options, criteria, evidence, uncertainty, reversibility, and opportunity costs. |
| [`research_scout`](.codex/agents/research_scout.toml) | Read-only | Discover authoritative sources, trace primary evidence, and map disagreements and gaps. |
| [`claim_verifier`](.codex/agents/claim_verifier.toml) | Read-only | Check material claims against proposed sources for support, scope, freshness, and qualifiers. |
| [`learning_assessor`](.codex/agents/learning_assessor.toml) | Read-only | Evaluate supplied learner work against explicit objectives and criteria, identify error patterns, and suggest targeted practice. |
| [`news_scout`](.codex/agents/news_scout.toml) | Read-only | Search bounded current-events scopes, cluster duplicate coverage, trace primary evidence, and report freshness, disagreement, and gaps. |
| [`investment_scenario_analyst`](.codex/agents/investment_scenario_analyst.toml) | Read-only | Calculate supplied hypothetical investment scenarios, verify formulas and assumptions, and return neutral results without recommendations. |

`workspace-write` identifies the Codex sandbox available to an agent. Each agent definition adds narrower behavioral limits, including restrictions on source edits, dependencies, external systems, Git operations, and destructive actions.

## Repository Structure

```text
AI-Devkit/
|-- .codex/
|   `-- agents/             # Project-scoped custom agent definitions
|-- docs/
|   `-- profile-setup.md    # Hybrid profile setup and privacy contract
|-- profiles/
|   |-- finance.example.md  # Placeholder-only financial profile
|   `-- personal.example.md # Placeholder-only everyday profile
|-- skills/
|   |-- api-integration-builder/
|   |-- codebase-map/
|   |-- decision-helper/
|   |-- docs-writer-dev/
|   |-- investing-education/
|   |-- learning-coach/
|   |-- mcp-server-builder/
|   |-- news-brief/
|   |-- plugin-packager/
|   |-- prompt-engineer/
|   |-- research-brief/
|   `-- writing-editor/
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
cp -R skills/decision-helper "$HOME/.agents/skills/"
cp -R skills/research-brief "$HOME/.agents/skills/"
cp -R skills/writing-editor "$HOME/.agents/skills/"
cp -R skills/learning-coach "$HOME/.agents/skills/"
cp -R skills/news-brief "$HOME/.agents/skills/"
cp -R skills/investing-education "$HOME/.agents/skills/"
```

### Install Custom Agents

Current Codex documentation supports project-scoped definitions under `.codex/agents` and personal definitions under `$HOME/.codex/agents`. To make these definitions available from other projects, copy them to the personal directory:

```bash
mkdir -p "$HOME/.codex/agents"
cp .codex/agents/*.toml "$HOME/.codex/agents/"
```

Open a new Codex task after installation. Current Codex documentation states that skill changes are detected automatically and recommends restarting Codex when a new skill does not appear.

The agent definitions currently select `gpt-5.6-terra` with medium reasoning effort. Review those settings before installation if that model is unavailable in the target environment.

### Set Up Optional Profiles

The repository includes placeholder-only personal and financial profile templates. Completed profiles belong outside Git and are optional until a profile-aware skill needs them.

See [Profile Setup](docs/profile-setup.md) for paths, precedence, privacy boundaries, setup commands, and the confirmation flow for saved preference changes.

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

Help me decide between these options, show the decisive tradeoffs, and recommend the next step.

Research this topic and give me a sourced brief that separates evidence, disagreement, and uncertainty.

Edit this draft for clarity and flow while preserving my meaning and voice.

Teach me this topic, check my understanding with guided practice, and adapt based on my mistakes.

Give me a current news brief based on my saved interests, with context, uncertainty, and direct source links.

Explain how fees and inflation affect these hypothetical investing scenarios without recommending an action.
```

When a skill delegates work, the custom agent returns a scoped result to the parent skill. The parent reconciles the evidence and produces the final answer.

## Design Principles

- **Parent ownership:** skills retain final scope, judgment, and synthesis.
- **Bounded delegation:** custom agents receive narrow objectives, explicit permissions, and return contracts.
- **Evidence before claims:** repository behavior, commands, documentation, and evaluations are checked rather than assumed.
- **Least necessary access:** read-only agents stay read-only; write-capable agents remain constrained by task scope.
- **Independent checks:** implementation, verification, security review, documentation audit, and evaluation remain distinct responsibilities.
- **Public-safe personalization:** personal values live outside Git, while repository templates contain placeholders and generic workflows.
- **Financial boundaries:** financial skills act as decision aids that provide education, transparent calculations, and neutral scenarios only; they do not prescribe financial, investment, tax, legal, lending, debt, or real-estate actions.

## Financial Use Disclaimer

Financial workflows are decision aids for general education and neutral planning support. They are not financial, investment, tax, legal, lending, or real-estate advice and are not substitutes for appropriately qualified professionals. Every substantive financial response must display a concise disclaimer stating both that limitation and that the workflow cannot tell the user what action to take. These workflows may organize prompt-supplied information, explain concepts, show assumptions, perform transparent calculations, and compare neutral scenarios. They will not recommend specific securities, allocations, properties, loans, products, providers, legal strategies, or tax positions; issue action directives; or make legal, tax, lending, suitability, or purchase determinations.

## Roadmap

| Phase | Status | Planned work |
| --- | --- | --- |
| Developer skills | Complete | Six reusable developer workflows. |
| Developer subagents | Complete | Six focused agents connected to the parent skills. |
| Everyday skills | Complete | Five profile-aware workflows implemented: decision support, research briefs, writing editing, learning coaching, and news briefings. |
| Profile foundation | Available | Public placeholder templates, private-path conventions, and opt-in update rules; all five everyday integrations are available. |
| Financial skills | In progress | Investing Education is implemented; Budget Planner, Real-Estate Buying Guide, and Debt And Loan Scenarios are planned. |
| Distribution | Planned | Automated installation, plugin packaging, clean-user testing, and release documentation. |

The repository does not currently include a release package, automated installer, contribution guide, or license. Those surfaces will be added and validated before a public release.

## Privacy Direction

Developer skills and agents do not use personal profiles. The profile foundation is documented in [Profile Setup](docs/profile-setup.md). Implemented and planned everyday and financial skills follow this hybrid model:

- generic behavior and profile schemas remain public;
- completed personal profiles remain outside the repository;
- skills read only the profile sections relevant to the current task;
- current user instructions override saved preferences;
- profile changes require explicit confirmation;
- financial workflow preferences remain separate from everyday preferences; and
- prompt-supplied financial facts remain task-local and are never saved automatically.

The templates and setup contract are available now. No developer skill reads either profile. Decision, research, writing, learning, and news skills read only their relevant sections of `personal.md`. `investing-education` uses the current request as the sole source of financial circumstances and numbers, may read communication preferences from `personal.md`, and may read only its relevant workflow preferences from a version 2 `finance.md`; future financial skills will follow the same contract.
