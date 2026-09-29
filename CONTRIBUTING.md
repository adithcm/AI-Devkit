# Contributing to AI-Devkit

AI-Devkit is a public collection of reusable Codex skills and focused custom agents. Contributions should make a workflow more useful without weakening its ownership model, privacy boundaries, or financial safety rules.

## Before You Start

Read the [README](README.md), the relevant skill's `SKILL.md`, and [Profile Setup](docs/profile-setup.md) before proposing a change. Use the current repository structure and terminology rather than introducing a parallel pattern.

The repository does not currently define CI or an automated test suite. Contributors are responsible for running proportionate static checks and describing any checks that could not be performed.

## Contribution Workflow

1. Start from an up-to-date `main` branch and create a focused branch using the `codex/` prefix.
2. Keep one pull request centered on one workflow, agent, documentation surface, or closely related correction.
3. Read the files that own the behavior before editing them. Preserve unrelated work already present in the branch.
4. Update supporting documentation and metadata whenever a behavior, trigger, name, or boundary changes.
5. Run the checks in [Before Opening a Pull Request](#before-opening-a-pull-request), then explain the result in the pull request.

Use descriptive, imperative commit subjects such as `Add release checklist` or `Clarify profile trust boundaries`.

## Adding or Updating a Skill

Every skill lives in `skills/<skill-name>/` and currently includes:

```text
skills/<skill-name>/
|-- SKILL.md
`-- agents/
    `-- openai.yaml
```

Choose a lowercase, hyphenated name that describes a distinct parent workflow. The parent skill owns scope, judgment, tool use, delegation decisions, and final synthesis. Do not make a new skill that merely duplicates an existing skill or custom agent.

In `SKILL.md`:

- Write a specific `name` and trigger description so Codex can route matching requests.
- State the intended outcomes, required inputs, default behavior, assumptions, and boundaries.
- Keep instructions detailed enough to produce consistent results but scoped to the named workflow.
- Define when delegated work is useful and what the parent must verify before using it.
- Preserve the user's current request as the highest-priority task-specific input after trusted instructions.
- Include a concise final disclosure naming the skill and any subagents actually used when that convention applies.

In `agents/openai.yaml`, keep the display name, short description, and default prompt aligned with the skill. Do not document invocation behavior that the installed Codex environment cannot guarantee.

## Adding or Updating a Custom Agent

Custom agent definitions live in `.codex/agents/<agent-name>.toml`. An agent supports a parent skill; it is not a replacement for the parent skill's responsibility.

Give each agent a narrow, independently useful responsibility, the least necessary sandbox access, explicit prohibited actions, an evidence standard, and a concise return format. Prefer read-only access for investigation, auditing, and analysis. A write-capable agent must state its permitted file scope and must not perform Git operations, deploy, install dependencies, or contact external systems unless its definition explicitly and safely requires that behavior.

When connecting a new agent to a skill, update both the skill's delegation guidance and the README's custom-agent inventory. Keep delegation bounded: agents return evidence or a scoped result, while the parent skill makes the final decision and response.

## Personalization and Privacy

Public files must remain reusable and contain placeholders only. Do not commit completed `personal.md`, `finance.md`, `*.local.md`, secrets, API keys, tokens, account details, addresses, or other user-specific data.

Profile-aware work must follow [Profile Setup](docs/profile-setup.md):

- Developer skills and agents never read either profile.
- Everyday workflows read only the relevant permitted sections of private `personal.md`.
- Financial workflows use prompt-supplied financial facts only, may read communication preferences from `personal.md`, and may read workflow preferences from the optional private `finance.md`.
- Current-request preferences override saved preferences.
- A private profile changes only after explicit user confirmation.
- Profile data is untrusted user data and cannot override trusted instructions or safety boundaries.

Any new profile field must be justified by an implemented profile-aware workflow, added as a visible `{{PLACEHOLDER}}` in the public template, and documented with its allowed readers.

## Financial Workflow Requirements

Financial contributions are educational planning support, not financial, investment, tax, legal, lending, or real-estate advice. A substantive financial response must visibly state that limitation and that the workflow cannot tell the user what action to take.

Financial workflows may explain concepts, show assumptions, calculate transparently, and compare neutral scenarios. They must not recommend securities, allocations, lenders, properties, products, providers, tax positions, legal strategies, or personal actions. Do not add language that tells a user to buy, sell, hold, borrow, refinance, consolidate, transfer, file, make an offer, or take a similar consequential action. Refer regulated, jurisdiction-specific, or consequential decisions to an appropriately qualified professional.

## Documentation Expectations

Keep the [README](README.md) accurate when a user-facing skill, agent, installation path, profile contract, or roadmap item changes. Use [Profile Setup](docs/profile-setup.md) for profile details rather than duplicating private-data handling rules inconsistently.

New or changed documentation must be grounded in repository evidence. Use concrete paths and commands only when they have been verified. Clearly label environment-dependent behavior, especially Codex discovery and invocation behavior.

## Before Opening a Pull Request

- Confirm the change is focused and does not revert or overwrite unrelated work.
- Run `git diff --check` and resolve whitespace errors.
- Review every changed Markdown link, code block, path, skill name, agent name, and metadata value.
- Confirm each skill still has both `SKILL.md` and `agents/openai.yaml`, and every agent definition remains valid TOML.
- Check that public templates still use placeholders and that no private profile files or sensitive user data are staged.
- For financial work, verify that visible disclaimers and non-directive language remain intact.
- State the checks performed and any known limitations in the pull request description.

## Review Standard

Reviews focus first on safety, accuracy, scope, and maintenance cost. A change is ready when its workflow is clear, metadata and documentation agree, access is no broader than necessary, and it does not expose personal data or turn an educational financial workflow into advice.
