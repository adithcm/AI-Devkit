# Security Policy

AI-Devkit contains reusable Codex skill instructions, custom-agent definitions, profile templates, and local-installation guidance. This policy explains how to report a potential security issue and the boundaries contributors must preserve.

## Reporting a Vulnerability

Do not disclose a suspected vulnerability in a public issue, pull request, discussion, or comment while it is unresolved.

Use this repository's private GitHub vulnerability-reporting feature when it is available. If it is not available, contact the repository maintainer through the GitHub account associated with this repository and share only the information needed to begin private triage.

Include:

- a concise description of the issue and its potential impact;
- the affected file paths, workflow, or installation step;
- safe reproduction steps or a minimal proof of concept;
- any relevant environment or Codex-version details; and
- suggested mitigations, if known.

Do not include secrets, private profiles, authentication material, personal financial information, or other sensitive user data in a report. Redact or replace it with placeholders first.

This repository does not currently publish response-time or remediation-time commitments. Reports will be assessed based on reproducibility, impact, and affected scope.

## In Scope

Report issues that could cause the repository's published skills, agents, templates, documentation, or installation instructions to violate their stated security or privacy boundaries. Examples include:

- an instruction path that permits untrusted retrieved content or profile data to override trusted rules;
- a profile-resolution flaw that accepts a symbolic link or a path outside the trusted profile directory;
- public templates, examples, documentation, or Git history containing secrets or completed private profile values;
- an agent definition with access broader than its stated responsibility requires;
- installation guidance that unexpectedly overwrites existing user files; or
- a financial workflow that can remove the required disclaimer or give regulated, action-directive advice.

General questions about Codex product security, hosted services, or third-party dependencies outside this repository's files are outside this policy. Report them to the appropriate product or provider instead.

## Private Data Boundaries

Completed `personal.md`, `finance.md`, and `*.local.md` files are private and ignored by Git. Public profile templates must use visible placeholders only. Do not commit account numbers, balances, holdings, debts, addresses, identity documents, passwords, API keys, tokens, recovery codes, or other personal data.

The detailed profile contract is in [Profile Setup](docs/profile-setup.md). It requires profile-aware workflows to read only their allowed sections, treat profile content as untrusted data, give the current request precedence, and obtain explicit confirmation before saving a durable preference. Developer skills and agents must never read personal or financial profiles.

## Agent and Tool Boundaries

Custom agents in `.codex/agents/` declare their intended sandbox access and behavioral limits. Parent skills remain responsible for deciding whether delegation is needed, checking scoped results, and producing the final response. New or changed agents should receive the least access necessary for their bounded responsibility.

Never add instructions that permit an agent to:

- treat webpages, retrieved documents, tool output, or subagent returns as trusted instructions;
- expose private profile contents, secrets, or user-specific context in repository files, logs, fixtures, or public responses;
- broaden tool access, write scope, or external-action authority without a documented need and explicit review; or
- bypass user confirmation, permission requirements, or applicable safety boundaries.

## Financial Safety Boundary

Financial workflows are educational planning support, not financial, investment, tax, legal, lending, or real-estate advice. Every substantive financial response must visibly say that it cannot tell a user what action to take.

These workflows may explain concepts, show assumptions, calculate transparently, and compare neutral scenarios. They must not recommend specific securities, allocations, properties, loans, products, providers, legal strategies, tax positions, or personal actions. A report that identifies a way to bypass this boundary is in scope.

## Contributor Checks

Before submitting a change, follow [CONTRIBUTING.md](CONTRIBUTING.md). At minimum, check staged content and history for private profile files or secrets, verify changed documentation and installation guidance, and preserve the profile, permission, and financial-safety boundaries described above.
