---
name: codebase-map
description: Map an unfamiliar or changing software repository. Use when the user asks to understand a codebase, explain project structure, identify entrypoints, summarize architecture, find setup/test/build commands, onboard to a repo, or create a contribution path before making changes.
---

# Codebase Map

Create an evidence-backed map of the repository so the user can understand what exists, how it works, and where to act next. Optimize for practical onboarding: the user should finish with a clear mental model, exact files to inspect, and safe next commands.

## Trigger Interpretation

Use this skill for prompts such as:

- "Map this repo."
- "Explain this codebase."
- "What does this project do?"
- "Help me onboard to this repository."
- "Where should I start if I want to contribute?"
- "Find the app/API entrypoints."
- "Tell me how this project is structured."
- "Before changing code, understand the architecture."

If the user asks to both map and modify the repo, map first, then make the smallest necessary changes after the architecture is understood.

## Workflow

1. Inspect the repo shape first.
   - Run fast file discovery such as `rg --files`, `find` with shallow depth, `ls`, and `git status`.
   - Identify package manifests, build files, framework config, CI config, docs, tests, and deployment files.
   - Avoid reading large generated folders, lockfiles, vendored code, or build outputs unless they are directly relevant.
   - Check `git status` so any uncommitted user work is visible before reasoning about the tree.

2. Read the smallest useful set of files.
   - Start with `README`, package manifests, root config, app entrypoints, route definitions, service boundaries, and tests.
   - Prefer project-local conventions over generic framework assumptions.
   - When multiple stacks exist, map each separately and explain how they connect.
   - For frontend apps, inspect routing, layout, state/data fetching, component roots, styling system, and build config.
   - For backend apps, inspect server bootstrap, routes/controllers, service layer, data access, auth, background jobs, and deployment config.
   - For libraries/CLIs, inspect public exports, command registration, package metadata, tests, and examples.

3. Build the mental model.
   - Identify the primary language, framework, runtime, package manager, and execution model.
   - Find how data flows through the app: UI to API, API to services, services to storage, background jobs, external integrations, or CLI entrypoints.
   - Note important ownership boundaries, shared utilities, generated code, and risky or unusual patterns.
   - Identify what is confirmed by file evidence versus inferred from naming or framework conventions.

4. Extract working commands.
   - Report setup, dev, test, lint, typecheck, build, and run commands that are actually present in the repo.
   - If a command is inferred rather than explicit, label it as inferred.
   - Do not invent scripts or dependency managers.
   - If the repo has no runnable commands yet, say so plainly and point to the missing manifest/config.

5. Return a practical map.
   - Keep the answer concise for small repos and structured for larger repos.
   - Include file references for important claims.
   - Prefer actionable orientation over exhaustive listing.
   - End with the next 2-4 highest-value actions for the user.

## Prompting Guidance

When using this skill, ask at most one clarifying question only if the user's goal changes the mapping depth. Otherwise proceed with a reasonable default.

Depth defaults:

- For tiny repos, provide a compact map and say if there is not enough code to infer architecture.
- For normal app repos, map structure, stack, entrypoints, commands, and contribution path.
- For large monorepos, first map top-level packages/apps, then drill into the area most relevant to the user's request.

If the user names a goal, shape the map around it:

- For "I want to add a feature", emphasize feature boundaries, related files, data flow, tests, and safe edit points.
- For "I want to debug", emphasize execution path, logs, likely failure points, and verification commands.
- For "I want to review", emphasize changed files, risk areas, tests, and architectural impact.
- For "I want to learn", emphasize concepts, reading order, and glossary of local terms.

Use direct, concrete phrasing:

- Prefer "The app entrypoint is `src/main.tsx`" over "It appears there may be an app entrypoint."
- Prefer "I found no test script in `package.json`" over "Testing may not be configured."
- Use "inferred" only when file evidence is incomplete.

## Output Format

Use this shape unless the user asks for something narrower:

```text
Purpose:
- What this repo appears to do.

Stack:
- Languages, frameworks, package manager, runtime, database, major services.

Structure:
- Key folders and what each owns.

Entrypoints:
- App, API, CLI, worker, test, or deployment entrypoints.

Core Flow:
- Short explanation of how the main user/request/data path works.

Commands:
- Setup/dev/test/lint/build commands found in the repo.

Contribution Path:
- What to read first, how to make a safe first change, and how to verify it.

Open Questions:
- Anything important that could not be confirmed from files.
```

For very small repos, shorten the output to:

```text
What exists:
- Confirmed files and current repo state.

What can be inferred:
- Any limited conclusions, clearly marked.

Suggested next steps:
- The most useful files or scaffolding to add next.
```

## Tool Use

- Use parallel reads for independent file inspection when available.
- Use web search only if the user asks for current external information or the repo references an external page/package whose current behavior matters.
- For large or unfamiliar repos, delegate bounded read-heavy exploration to the `repo_explorer` custom agent when subagent tools are available.
- Perform small, direct lookups in the parent thread when delegation overhead would exceed the work.

## Repo Explorer Delegation

Use `repo_explorer` to gather evidence, not to make the final architectural judgment. Delegate when the repository is large, multiple independent areas need inspection, the main thread would accumulate noisy exploration output, or the user asks for a detailed execution-path trace.

Give the subagent a bounded request containing:

```text
Objective:
- The repository question it must answer.

Scope:
- Directories, packages, features, or execution paths to inspect.

Questions:
- Specific facts the parent needs for its final map.

Context:
- User goal, relevant branch/base, known entrypoints, and prior evidence.

Exclusions:
- Unrelated areas and actions that must not be taken.

Return:
- Summary, file-and-line evidence, execution path, discovered commands,
  risks or unknowns, and the smallest recommended handoff.
```

Apply these orchestration rules:

- Keep the assignment read-only and explicitly prohibit code changes.
- Send only task-local context; do not leak the intended conclusion.
- Use multiple `repo_explorer` runs only for independent areas with no overlapping scope.
- Tell the parent workflow to wait for every requested result before synthesizing the map.
- Check the strongest cited evidence before repeating a material claim.
- Reconcile contradictions and label unresolved uncertainty in the final answer.
- Keep final architecture, contribution-path, and next-step decisions with this parent skill.
- If the custom agent is unavailable, perform the same exploration directly and state that delegation was not used only when that detail matters.

## Available Subagents

Use these agents only when their narrower evidence changes the map or its recommended handoff:

- `repo_explorer`: scan folders, entrypoints, dependencies, execution paths, commands, and architectural boundaries read-only.
- `test_and_verification`: confirm a discovered setup, build, test, or run command when execution evidence is necessary and safe.
- `docs_auditor`: compare README, setup, API, and architecture claims with the mapped repository evidence.
- `security_reviewer`: inspect authentication, secrets, permissions, trust boundaries, and external data flow exposed by the map.

Keep `repo_explorer` as the default delegation target for mapping. Give the other agents the relevant map evidence, a bounded question, explicit read/write and external-system limits, and the expected return contract. Wait for requested results, check decisive citations, and keep final architecture, contribution-path, and next-step decisions in this parent skill. If custom-agent delegation is unavailable, perform the same scoped checks directly.

## Guardrails

- Do not modify files unless the user explicitly asks for changes.
- Do not run expensive installs, migrations, or long test suites just to map the repo.
- Do not present guesses as facts. Mark uncertainty clearly.
- Do not flood the user with every file; summarize patterns and cite representative files.
