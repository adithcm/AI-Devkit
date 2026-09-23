---
name: plugin-packager
description: Plan, create, assemble, validate, install, test, update, review, or prepare Codex and Agent Plugin packages for release. Use when the user asks to bundle skills, MCP servers, apps, hooks, scripts, or assets into a plugin; create or repair plugin manifests and marketplace entries; install or refresh a local plugin; audit plugin structure and metadata; test plugin activation and combined workflows; or prepare a plugin for team distribution or public submission.
---

# Plugin Packager

Build plugins as coherent installable products, not loose folders. Choose the smallest package shape that serves the intended workflows, assemble only working components, keep manifests and marketplace metadata accurate, validate every path and contract, install through the appropriate local source, and test the complete experience in a fresh task.

Treat this skill as the parent workflow. Use available plugin-scaffolding helpers for deterministic folder and manifest generation, skill-authoring guidance for bundled skills, and MCP guidance for bundled or registered servers. Keep final package ownership, integration decisions, validation, and release readiness here.

## Operating Modes

Choose the smallest mode that satisfies the request:

- **Plan:** define plugin purpose, users, workflows, components, distribution, and release criteria.
- **Create:** scaffold and populate a new plugin.
- **Package:** assemble existing skills, MCP servers, apps, hooks, scripts, and assets.
- **Install:** add the plugin to a personal or explicitly requested team marketplace and verify discovery.
- **Update:** change an installed local plugin and run the supported refresh or reinstall flow.
- **Review:** audit structure, manifests, metadata, permissions, policy, and user experience.
- **Release:** prepare versioning, documentation, assets, evaluations, and submission evidence.
- **Debug:** trace discovery, installation, component loading, authentication, activation, or cache problems.

Do not create a plugin when one reusable skill is still the appropriate scope. Package a plugin when the user wants distribution, related capabilities under one identity, external tools or data, installable configuration, lifecycle behavior, or a stable shared experience.

## Resolve Current Contracts First

Plugin formats, accepted manifest fields, marketplace behavior, product surfaces, submission requirements, and policy can change. Before editing:

1. Identify the target surfaces: local Codex, ChatGPT, team distribution, public directory, or a portable cross-host package.
2. Inspect existing manifests, marketplace entries, installed plugin state, and repository conventions.
3. Check current official plugin documentation and available schema or validation tools for exact fields and paths.
4. Preserve an explicitly requested format or compatibility target unless the user asks to migrate.
5. Separate packaging changes from skill rewrites, MCP migrations, UI redesigns, and policy remediation when those are independently testable.

Do not invent manifest keys or assume that a field accepted by one format belongs in another.

## Intake

Establish the package contract:

- Define the plugin’s literal purpose and target users.
- List three to seven representative user requests it must complete.
- List requests that should not activate or use the plugin.
- Identify existing skills, MCP servers, apps, hooks, scripts, and assets to include.
- Identify whether capabilities require authentication, private data, external writes, local executables, or remote infrastructure.
- Choose personal, repository/team, or public distribution.
- Identify publisher metadata, license, repository, website, privacy policy, terms, and support contact required by the destination.
- Define installation, authentication timing, product availability, versioning, and rollback expectations.

Ask a concise question only when the target distribution, plugin identity, ownership, or required capability is ambiguous enough to change the package. Otherwise, state assumptions and proceed.

## Choose The Plugin Shape

Use the smallest complete shape:

| Shape | Choose it when |
| --- | --- |
| Skills only | Existing model tools and instructions can complete the workflows. |
| MCP only | Server-backed tools or data are sufficient without added workflow guidance. |
| Skills plus MCP | Skills must guide repeatable workflows that call plugin tools. |
| MCP plus UI | Inspecting, comparing, editing, confirming, or navigating structured data materially benefits from UI. |
| Hooks plus other components | Trusted lifecycle automation is required on supported execution surfaces. |

Avoid adding UI for simple lookups or background actions. Avoid adding an MCP server when instructions and existing tools are enough. Avoid hooks for behavior that can remain an explicit workflow step.

## Select A Manifest Strategy

Choose one primary package model based on current documentation and the requested destination:

- Use the current portable root manifest format for new portable Agent Plugin packages when supported by the target.
- Use the Codex compatibility scaffold when the local plugin-creation workflow or existing package is based on `.codex-plugin/plugin.json`.
- Preserve a valid legacy package unless migration provides a requested benefit.
- Do not casually combine portable and compatibility metadata. Understand which source is canonical and whether an inline OpenAI extension replaces or supplements a compatibility overlay.

For a portable package, keep fixed portable components at the plugin root according to the active schema, commonly including:

```text
plugin-name/
├── plugin.json
├── skills/
├── mcp.json
├── hooks/
└── assets/
```

For the supported Codex compatibility scaffold, keep the required manifest and add only needed companions:

```text
plugin-name/
├── .codex-plugin/
│   └── plugin.json
├── skills/
├── .mcp.json
├── .app.json
├── hooks/
├── scripts/
└── assets/
```

Treat the displayed trees as conceptual layouts. Resolve exact filenames, schema declarations, and supported fields from current documentation and validators before writing them.

## Scaffold Deterministically

When an official or installed plugin-creation helper supports the target format:

1. Read its instructions and referenced manifest specifications.
2. Use its scaffold command rather than hand-building boilerplate.
3. Pass only the optional component flags the plugin actually needs.
4. Generate marketplace metadata through the helper when supported.
5. Edit generated files with real metadata and components.
6. Run the helper’s plugin validator before installation.

Do not overwrite an existing plugin without confirming the target and preserving unrelated user changes. Use force or replacement options only when intentional.

## Component Assembly

### Skills

- Give every skill its own correctly named directory and `SKILL.md`.
- Make the frontmatter description specific enough to trigger for intended requests and remain inactive for unrelated requests.
- Keep workflows detailed enough to be reliable but small enough for context-efficient loading.
- Include only supporting references, scripts, templates, and assets the skill uses.
- Resolve relative paths inside the installed package, not only in the source repository.
- Validate each skill independently before packaging.
- Test overlapping skill descriptions for ambiguous activation.

Do not copy a skill into a plugin and leave another unmanaged divergent version without deciding which copy is authoritative.

### MCP Servers

- Build and test each server before treating packaging as complete.
- Use current supported server configuration and transport fields.
- Keep server names stable and unique inside the plugin.
- Point to real commands or endpoints and use supported environment-variable or authentication mechanisms for secrets.
- Ensure tool descriptions, schemas, annotations, authorization, and results match behavior.
- Keep local executable requirements explicit; web installation does not deploy local scripts or services.
- Test skill-to-tool workflows end to end when both are bundled.

For public distribution, verify current remote endpoint and review requirements. A local development connection or tunnel is not automatically a production distribution strategy.

### Apps And UI

- Add registered app or server mappings only when the companion file exists and has real entries.
- Keep UI optional unless it materially improves a workflow.
- Ensure headless model-readable results remain useful when the component is unavailable.
- Validate resource paths, security policy, state restoration, and supported host behavior.
- Include screenshots only when UI exists and the distribution flow accepts them.

### Hooks And Scripts

- Add hooks only for supported lifecycle events and execution environments.
- Keep command paths inside the plugin and resolve them from documented plugin root variables.
- Treat bundled hooks as executable code requiring user trust and review.
- Ensure scripts exist, have the required runtime, and are tested from an installed package path.
- Write runtime state only to the documented writable data location, not the installed plugin directory.
- Never assume installing a plugin through a web surface deploys local hook executables.

### Assets

- Keep icons, logos, screenshots, templates, and other files inside the plugin archive.
- Use relative paths that stay within the plugin root.
- Verify every declared asset exists with the required format and dimensions.
- Provide dark-mode variants only when supported and visually checked.
- Do not include unused, copyrighted, private, generated-debug, or source-only assets.

## Manifest Quality

Keep metadata accurate and user-centered:

- Use one stable normalized plugin identifier everywhere.
- Use a valid semantic version when required.
- Write a literal description of what the plugin enables.
- Keep display name, developer identity, category, capabilities, links, and starter prompts consistent with actual behavior.
- Use absolute secure URLs where required.
- Keep component paths relative to the plugin root and inside the package.
- Declare only components that exist.
- Keep publisher, repository, license, privacy, terms, and support information current.
- Avoid promotional superlatives, claims of endorsement, competitor disparagement, or metadata designed to manipulate model selection.

Starter prompts must represent complete workflows the installed plugin can actually perform. Keep them short, distinct, and useful; do not use them as marketing slogans.

## Marketplace And Distribution

### Personal Marketplace

Default to the standard personal marketplace for an individual local plugin unless the user requests another destination. Use the supported scaffold or marketplace helper so entries retain the expected structure and order.

- Preserve existing marketplace identity and presentation metadata.
- Append new entries unless the user asks to reorder.
- Include required installation, authentication, and category policy fields.
- Add product gating only when explicitly requested.
- Keep the source path relative to the marketplace root according to the active format.
- Do not instruct the user to register a marketplace that the current Codex personal flow discovers automatically.

### Repository Or Team Marketplace

Use a repository or team marketplace only when explicitly requested. Confirm the plugin source path resolves from that marketplace and install the non-default marketplace through the supported command or interface before reinstalling from it.

### Public Distribution

Before public submission:

- verify publisher identity and domain requirements;
- provide accurate privacy, terms, and support URLs;
- confirm every skill, MCP tool, UI resource, and workflow is production-ready;
- review current plugin, safety, data, commerce, intellectual-property, and third-party integration policies;
- prepare review credentials and representative data when authentication review requires them;
- verify the remote server’s production availability, authentication, logging, and limits;
- remove internal, test-only, and unsupported capabilities;
- preserve submission evidence and evaluation results.

Do not promise approval. Report unresolved policy, infrastructure, or verification requirements explicitly.

## Installation And Update Workflow

### First Installation

1. Validate the plugin source.
2. Validate or generate the marketplace entry.
3. Confirm any non-default marketplace is configured.
4. Install the plugin using the current supported command or interface.
5. Confirm the installed plugin identity and enabled state.
6. Complete authentication only through the supported connection flow.
7. Start a fresh task so newly installed skills and tools load cleanly.
8. Run the plugin evaluation set.

### Updating A Local Plugin

Use the installed plugin-creation workflow’s documented cache refresh or version-bump helper when available. Do not repeatedly append cache markers or hand-edit installation state.

1. Update the source-of-truth files.
2. Validate the package and each changed component.
3. Apply one supported cachebuster or version update.
4. Resolve the marketplace name from the actual local marketplace source.
5. Reinstall or refresh through the supported plugin command.
6. Start a new task.
7. Rerun affected evaluation and regression cases.

If the installed plugin points to a different or non-local source, stop and resolve that mismatch instead of pretending the local update succeeded.

## Validation

Validate at five layers:

### Structure

- required manifest exists at the selected location;
- plugin folder and manifest identity agree;
- declared component paths stay inside the plugin;
- no placeholders, temporary files, broken symlinks, or secret files remain;
- portable and compatibility files do not conflict.

### Schema

- manifest and companion files parse;
- current schemas and supported field sets pass;
- versions, URLs, colors, enums, and paths use accepted formats;
- every declared file exists;
- unsupported keys are removed or moved to the correct extension block.

### Components

- every bundled skill validates independently;
- MCP servers start or respond and expose expected capabilities;
- hooks and scripts execute in the intended environment;
- app mappings and UI resources resolve;
- assets render and match metadata.

### Installation

- marketplace entry resolves to the source package;
- plugin installs or refreshes without errors;
- expected skills, tools, and hooks are discoverable;
- authentication and permissions appear at the intended time;
- uninstall or rollback path is understood.

### Behavior

- direct and indirect requests activate the intended skill or tool;
- negative and out-of-scope requests do not activate unnecessarily;
- follow-up requests preserve identifiers and workflow context;
- write actions expose effects and receive expected approval behavior;
- combined skill-and-tool workflows complete end to end;
- starter prompts succeed;
- results remain useful when optional UI is unavailable.

Record the request, selected component, arguments, result, errors, and approval behavior for failures. Rerun the same cases after changes.

## Security And Privacy Review

- Keep credentials, tokens, test accounts, private keys, and local secrets out of the package.
- Request the minimum permissions and user data needed for each workflow.
- Ensure metadata and tool descriptions disclose external writes or data transmission.
- Keep authentication and authorization enforcement in server code.
- Review hooks and scripts as executable supply-chain surfaces.
- Pin or constrain dependencies according to the repository’s security policy.
- Avoid unofficial third-party connectors that violate service terms.
- Do not collect broad conversation history, hidden context, or unrelated personal data.
- Include only telemetry that is necessary, disclosed, and appropriately controlled.

For public release, verify the current official policy rather than relying on a static checklist in this skill.

## Debugging Order

Trace plugin failures in this order:

1. **Source identity:** correct plugin folder, manifest, name, version, and marketplace source.
2. **Manifest:** syntax, schema, supported fields, canonical metadata source, and component declarations.
3. **Paths:** component files, assets, relative roots, archive boundaries, and installed paths.
4. **Marketplace:** marketplace identity, source path, entry policy, configuration, and ordering.
5. **Installation cache:** installed version, cachebuster, refresh, reinstall, and fresh-task boundary.
6. **Component loading:** skill discovery, MCP configuration, app mapping, hook trust, and executable availability.
7. **Authentication and policy:** account connection, scopes, workspace controls, product availability, and permissions.
8. **Behavior:** triggering, tool selection, schemas, approval flow, UI, and workflow completion.

Use current CLI or app diagnostics when available. Distinguish source files from installed cached copies before assuming an edit was ignored.

## Output Format

For implementation work, return:

```text
Plugin:
- Name, purpose, target users, surfaces, and distribution.

Package shape:
- Manifest strategy and bundled components.

Files changed:
- Manifests, skills, MCP config, hooks, scripts, apps, and assets.

Validation:
- Structure, schema, component, installation, and behavior checks.

Installation:
- Marketplace and plugin state, with secrets omitted.

Evaluation:
- Requests tested, activation behavior, results, and regressions.

Open items:
- Authentication, deployment, policy, submission, or unverified surfaces.
```

For review work, lead with findings ordered by severity and cite the affected file or contract. Prioritize secret exposure, unsafe executable behavior, broken installation, unauthorized actions, manifest incompatibility, misleading metadata, and missing end-to-end tests.

## Subagent Orchestration

Use available custom agents for bounded package work and independent checks while keeping package shape, source-of-truth, distribution, and release-readiness decisions in this parent skill:

- `repo_explorer`: inventory existing manifests, skills, MCP servers, apps, hooks, scripts, assets, marketplace entries, and installation paths.
- `implementation_worker`: implement an approved, file-bounded manifest, component, configuration, metadata, or packaging change.
- `test_and_verification`: validate schemas and paths, run component checks, install or refresh in an isolated target, and verify discovery in a fresh task when authorized.
- `security_reviewer`: inspect executable behavior, permissions, authentication, secrets, data handling, external writes, and cross-component trust boundaries.
- `docs_auditor`: compare package, installation, configuration, privacy, and usage documentation with the bundled and installed plugin.
- `eval_runner`: execute frozen direct, indirect, negative, boundary, follow-up, and starter-prompt cases and report activation regressions.

Give every delegated request the plugin purpose, target surfaces, distribution path, package source of truth, bounded component scope, authorization limits, and required return format. Apply these rules:

- Resolve portable versus compatibility format, component identities, and marketplace ownership in the parent before assigning implementation.
- Keep cache changes, installation, authentication, network access, publishing, submission, and overwrite actions prohibited unless explicitly authorized.
- Run component validation before installation, then use `test_and_verification` for an isolated installed-state check.
- Run `security_reviewer` independently for permission and data-flow risk; use `eval_runner` only after the installed plugin is discoverable.
- Use `docs_auditor` after package behavior and installation steps settle.
- Wait for requested results, verify decisive artifacts, reconcile component findings, and decide release readiness in this parent skill.
- If custom-agent delegation is unavailable, perform the same scoped work directly.

## Completion Checklist

Before finishing, confirm:

- The plugin has a clear purpose and coherent set of workflows.
- A plugin is the correct scope instead of one standalone skill.
- The target surfaces and distribution path are explicit.
- The selected manifest format is current and intentional.
- Every declared component exists and independently validates.
- Skills, MCP servers, apps, hooks, scripts, and assets work together.
- Metadata accurately describes behavior and ownership.
- Paths resolve from the packaged and installed plugin.
- Marketplace state and installation source are correct.
- The installed plugin was tested in a fresh task.
- Positive, negative, boundary, follow-up, and regression cases pass.
- Authentication, permissions, writes, data handling, and executable code were reviewed.
- Release or submission requirements are either satisfied or listed as open items.

## Guardrails

- Do not declare a plugin complete because the manifest merely parses.
- Do not invent manifest fields, marketplace policy values, product behavior, or submission requirements.
- Do not package placeholders, sample credentials, internal URLs, or untested components.
- Do not add manifest references for companion files that do not exist.
- Do not silently migrate between portable and compatibility formats.
- Do not hand-edit installed caches or marketplace configuration when a supported helper or command owns the update flow.
- Do not use force, overwrite, publish, or submit actions without clear user intent and validated targets.
- Do not claim local testing proves public-review acceptance.
- Do not allow plugin metadata, tool descriptions, or starter prompts to misrepresent capabilities or manipulate selection.
