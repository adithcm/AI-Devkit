# Validation Guide

AI-Devkit is a configuration repository. Validation focuses on file structure, metadata syntax, documentation accuracy, installation behavior, privacy, and workflow boundaries rather than application builds or runtime test suites.

Run these checks from the repository root before opening a pull request or preparing a release. The repository does not currently define CI or an automated test suite.

## Baseline State

Confirm the working tree and whitespace are clean before interpreting later results:

```bash
git status --short
git diff --check
```

The first command should be empty when validating a finalized change. The second command must produce no output.

## Skill and Agent Structure

Every skill must include its workflow instructions and user-facing metadata:

```bash
for skill in skills/*; do
  test -f "$skill/SKILL.md"
  test -f "$skill/agents/openai.yaml"
done
```

Review the result with the inventory in the [README](../README.md). Additions and removals must be reflected there, and each custom agent in `.codex/agents/` must have a matching `.toml` definition.

## Parse Metadata

The commands below were verified with Python 3.11 and Ruby on the maintainer environment. They do not modify repository files.

Parse every skill metadata file as YAML:

```bash
ruby -e 'require "yaml"; ARGV.each { |path| YAML.safe_load(File.read(path), aliases: true) }' skills/*/agents/openai.yaml
```

Parse every custom-agent definition as TOML. This requires Python 3.11 or later for `tomllib`:

```bash
python3 -c 'import pathlib, tomllib; [tomllib.loads(path.read_text()) for path in pathlib.Path(".codex/agents").glob("*.toml")]'
```

Both commands should exit successfully. Syntax parsing is necessary but not sufficient: also check that each skill name, description, display label, agent name, sandbox mode, and stated responsibility agree with the surrounding documentation.

## Documentation Review

List Markdown links for a focused manual review:

```bash
rg -n '\]\([^)]*\)' --glob '*.md' .
```

Confirm that every local path resolves from the file that contains it and that Markdown anchors render correctly on the intended Git host. Read changed examples and commands as a new user would: a documented command should be safe, complete, and supported by a file or configuration in the repository.

For architecture diagrams, render the changed Markdown in a Mermaid-capable viewer. Confirm that node labels are legible and that the diagram still distinguishes repository-defined behavior from installed-Codex behavior.

## Privacy and Safety Review

Completed profiles must never be tracked. This command must produce no output:

```bash
git ls-files profiles/personal.md profiles/finance.md 'profiles/*.local.md'
```

Review all staged and committed additions for secrets, completed profile values, account details, financial circumstances, private URLs, or other user-specific data. Keyword searches can help find candidates, but they are not proof of a clean release; inspect the actual files and history in scope.

For profile-aware changes, verify the rules in [Profile Setup](profile-setup.md):

- public templates retain visible `{{PLACEHOLDER}}` values;
- developer skills and agents do not access profiles;
- everyday skills read only their relevant permitted `personal.md` sections;
- financial workflows use current-request facts and preference-only profile fields; and
- no profile is written without explicit user confirmation.

For financial changes, review each substantive response template or workflow instruction for a visible educational-use disclaimer and non-directive language. It must not recommend securities, products, providers, loans, properties, allocations, legal strategies, tax positions, or personal actions.

## Installation Smoke Test

Use an isolated temporary home directory to exercise the copy commands in [Local Installation](../README.md#local-installation). Do not test them against a real Codex configuration directory.

1. Open a new shell and set `HOME` to the path returned by `mktemp -d` for that shell only.
2. From the repository root, run the `Install Skills` and `Install Custom Agents` command blocks from the README.
3. Confirm that every skill directory and every `.toml` agent definition was copied into the temporary home.
4. Create a sentinel file at one destination, rerun the relevant installation block, and confirm that the sentinel remains unchanged.
5. Run the optional profile setup commands and confirm the private directory is mode `700` and created profile files are mode `600`.
6. Dispose of the temporary directory only after recording the result.

This test proves the documented copy behavior. Actual skill discovery and agent availability still depend on the installed Codex version and configuration.

## What to Record

In a pull request or release review, record:

- the commands run and whether they passed;
- the changed skills, agents, profiles, or documentation surfaces reviewed;
- the privacy and financial-safety review result when applicable;
- installation smoke-test outcome when installation instructions changed; and
- any environment-dependent behavior that could not be verified locally.

See [Release Checklist](release-checklist.md) for the final publication sequence.
