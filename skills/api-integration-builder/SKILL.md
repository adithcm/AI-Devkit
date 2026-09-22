---
name: api-integration-builder
description: Design, implement, review, or debug integrations with external or internal APIs. Use when the user asks to connect a service, call REST/GraphQL/RPC endpoints, add SDK usage, handle authentication, model request/response types, implement retries/errors/rate limits/webhooks, or create reliable API integration code and documentation.
---

# API Integration Builder

Build API integrations that are reliable, maintainable, observable, and safe to change. Treat the integration as a contract between this codebase and another system: understand both sides, preserve existing project patterns, and make failure behavior explicit.

## Trigger Interpretation

Use this skill for prompts such as:

- "Integrate Stripe/Twilio/OpenAI/GitHub/etc."
- "Add an API client for this service."
- "Call this endpoint from the app."
- "Wire this webhook into the backend."
- "Review this API integration."
- "Fix this API request failing."
- "Create typed wrappers around this SDK."
- "Add retries, auth, or error handling to these API calls."
- "Convert this curl/API doc into code."

If the user provides API docs, examples, OpenAPI schemas, curl commands, SDK snippets, or credentials guidance, treat those as primary evidence. If docs are missing and current behavior matters, ask for docs or browse official/current documentation when allowed.

## Workflow

1. Define the integration goal.
   - Identify the external service, endpoint(s), operation(s), and user-facing outcome.
   - Determine whether the work is read-only, write/action-taking, webhook-based, streaming, batch, or background.
   - Ask one concise clarifying question only if the target service, operation, or auth method is ambiguous enough to block progress.

2. Inspect the existing project patterns.
   - Find existing API clients, SDK wrappers, service modules, environment variable handling, logging, error types, tests, and docs.
   - Reuse local conventions for file placement, naming, dependency injection, validation, and test style.
   - Check package manager and dependency policy before adding libraries.

3. Understand the API contract.
   - Capture method, URL, auth scheme, headers, query params, request body, response body, pagination, rate limits, idempotency, errors, timeouts, and versioning.
   - For GraphQL, capture operation names, variables, fragments, response shape, and error envelope.
   - For webhooks, capture signature verification, replay protection, event types, idempotency, and retry semantics.
   - For SDKs, identify what the SDK handles versus what this codebase must still validate or wrap.

4. Design the integration boundary.
   - Create a small client/service wrapper rather than scattering raw calls across the app.
   - Keep credentials and base URLs in configuration or environment variables.
   - Normalize provider-specific errors into project-appropriate errors.
   - Validate or type external responses at the boundary when the project has a validation pattern.
   - Plan for timeouts, retries, backoff, rate limits, cancellation, and partial failure.

5. Implement narrowly.
   - Add only the endpoints and operations needed for the user's goal.
   - Keep request construction, response parsing, and business logic separated when the codebase supports that shape.
   - Avoid logging secrets, tokens, personally identifiable information, or full raw payloads unless explicitly safe.
   - Prefer deterministic tests with mocked network responses over live API calls.

6. Verify and document.
   - Run targeted tests, typechecks, lint, or build commands that match the changed surface.
   - Add or update concise usage docs, environment variable notes, and example calls when useful.
   - If live credentials are required and unavailable, state exactly what could not be verified and what command/request should be run with credentials.

## Prompting Guidance

When using this skill, shape the response around the user's intent:

- For implementation: produce a short integration plan, make the code changes, add focused tests, and report verification.
- For design-only requests: provide an integration design with file placement, API contract, error strategy, config, tests, and rollout steps.
- For debugging: trace request creation, auth, network transport, response parsing, error mapping, retries, and logs in that order.
- For review: lead with correctness, security, reliability, rate-limit, idempotency, and test risks.
- For docs conversion: turn API docs/curl examples into project-native code, types, and examples.

Use concrete language:

- Say "store `SERVICE_API_KEY` in the existing env config module" instead of "configure credentials."
- Say "retry `429` and `5xx` with exponential backoff, but do not retry non-idempotent POSTs unless an idempotency key is present" instead of "add retries."
- Say "mock `fetch`/HTTP client in tests; do not call the live API in CI" instead of "test the API."

If the user asks for an integration but gives no provider docs, first inspect whether the repo already has docs or code for that provider. If not, request the docs or use official/current docs if browsing is allowed and the details may have changed.

## Output Format

For implementation work, return:

```text
Integration:
- Service, operation, and user-facing purpose.

Files changed:
- What was added or modified and why.

Contract:
- Auth, request shape, response shape, errors, rate limits, idempotency, and config.

Verification:
- Commands run and results.

Usage:
- How the project should call the integration.

Open items:
- Credentials, live verification, provider setup, or docs still needed.
```

For design-only work, return:

```text
Goal:
- What the integration should accomplish.

Recommended shape:
- Client/service boundary, files, config, and data flow.

API contract:
- Endpoint/operation details and assumptions.

Reliability:
- Timeouts, retries, rate limits, idempotency, and failure handling.

Security:
- Secret handling, webhook verification, logging, and permissions.

Testing:
- Unit/integration test plan and mock strategy.

Next steps:
- Ordered implementation steps.
```

## Checklist

Before finishing, confirm:

- The integration has a clear owner module or wrapper.
- Secrets are not hardcoded or logged.
- Request and response shapes are explicit.
- Errors are handled in a way the app can act on.
- Timeouts and retry behavior are intentional.
- Rate limits and pagination are considered when relevant.
- Webhooks verify signatures and handle duplicate events when relevant.
- Tests do not require live network access unless explicitly marked/manual.
- Documentation mentions required env vars or provider setup when relevant.

## Future Subagents

When custom agents exist, this skill can call:

- `api-contract-reviewer`: inspect endpoint shape, schema compatibility, auth, versioning, and breaking-change risk.
- `implementation-worker`: implement scoped client/service/test changes after the integration plan is clear.
- `test-and-verification`: run targeted tests, typechecks, build commands, and investigate failures.
- `security-reviewer`: inspect secret handling, webhook verification, permissions, data exposure, and unsafe logging.
- `docs-auditor`: compare integration docs and examples against actual code behavior.

## Guardrails

- Do not invent provider-specific parameters, response fields, auth behavior, prices, rate limits, or webhook semantics.
- Do not use unofficial docs when official docs are available for current API behavior.
- Do not add a new dependency when the repo already has a suitable HTTP client, SDK, or validation library.
- Do not run live write/action API calls unless the user explicitly approves the action and environment.
- Do not commit secrets, tokens, sample real credentials, or sensitive payloads.
- Do not make a broad integration framework when the user needs one focused operation.
