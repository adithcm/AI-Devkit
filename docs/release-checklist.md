# Release Checklist

Use this checklist before merging a public-release branch or creating a public version tag. It is a review aid, not an automated guarantee.

## Scope and Version

- [ ] The release has a clear purpose and the branch contains only intended changes.
- [ ] The changelog records the user-visible additions, changes, and limitations.
- [ ] The stated version is appropriate for the scope and does not imply guarantees the repository cannot support.
- [ ] The license decision has been made before claiming an open-source public release.

## Repository Integrity

- [ ] `git status --short` is empty after the final commit.
- [ ] `git diff --check` passes.
- [ ] Skill structure and all YAML/TOML metadata parse successfully according to the [Validation Guide](validation.md).
- [ ] The README inventory matches the current skills and custom agents.
- [ ] All Markdown links, code blocks, Mermaid diagrams, paths, and installation commands have been reviewed.

## Privacy and Safety

- [ ] No completed `personal.md`, `finance.md`, or `*.local.md` file is tracked.
- [ ] No secret, token, account detail, identity document, private URL, completed profile value, or task-specific financial circumstance is included in the release changes or history in scope.
- [ ] Public profile templates contain generic instructions and visible placeholders only.
- [ ] Profile-aware workflows preserve trusted-path validation, request precedence, permitted-reader scope, and explicit confirmation before saves.
- [ ] Financial workflows retain their visible educational-use disclaimer, neutral scenario limits, and prohibition on action directives or recommendations.

## Public Documentation

- [ ] The README accurately describes the current project, installation limitations, version baseline, and roadmap.
- [ ] [Contributing](../CONTRIBUTING.md), [Security Policy](../SECURITY.md), [Architecture](architecture.md), [Profile Setup](profile-setup.md), and [Validation Guide](validation.md) agree with each other and the repository.
- [ ] Security reporting instructions do not expose personal contact details or promise unavailable response times.
- [ ] No documentation claims that skill discovery, agent availability, model access, or sandbox enforcement is guaranteed outside the installed Codex environment.

## Installation and Distribution

- [ ] The isolated-home installation smoke test has passed if installation instructions or package contents changed.
- [ ] Existing destinations, including symbolic links, are skipped rather than overwritten by documented installation commands.
- [ ] No automated installer, plugin package, or release artifact is described as available unless it is present and validated.
- [ ] Any future release tag, GitHub release, or external publication is created only after this repository review is complete.

## Final Handoff

- [ ] Final commit identifiers and validation results are recorded in the merge or release review.
- [ ] The branch is pushed and synchronized with its remote.
- [ ] Any remaining limitations are explicit in the README, changelog, or release notes.
- [ ] The maintainer has made the final merge and publication decision.
