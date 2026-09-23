---
name: mcp-server-builder
description: Design, implement, review, debug, test, secure, deploy, or connect Model Context Protocol (MCP) servers. Use when the user asks to expose tools, resources, or prompts through MCP; build a local stdio or remote HTTP server; integrate an API, database, filesystem, or service with an AI host; configure authentication and authorization; connect an MCP server to Codex or another client; or troubleshoot MCP schemas, transport, lifecycle, discovery, and tool-call failures.
---

# MCP Server Builder

Build MCP servers that expose focused, reliable capabilities to AI hosts. Treat the server as a security boundary and a versioned public contract: design from user goals, use the current protocol and official SDK, validate every input, enforce authorization in code, and verify behavior through a real MCP client or inspector.

## Operating Modes

Choose the smallest mode that satisfies the request:

- **Design:** define capabilities, contracts, transport, security, and deployment without implementation.
- **Build:** implement a new MCP server or add a capability to an existing server.
- **Connect:** configure a host such as Codex to launch or reach the server.
- **Review:** audit protocol compliance, tool design, security, reliability, and tests.
- **Debug:** trace discovery, lifecycle, transport, schema, authorization, handler, or result failures.
- **Deploy:** prepare a remote endpoint, authentication, observability, and operational controls.

Preserve the project’s language, framework, package manager, validation library, test style, and deployment conventions unless there is a concrete reason to change them.

## Resolve Current Contracts First

MCP specifications, SDK packages, transports, authorization requirements, and host capabilities evolve. Before implementation:

1. Identify the target host or hosts.
2. Identify the protocol revision and SDK version already used by the project.
3. Check the current official MCP specification and official SDK documentation when adding dependencies or relying on exact behavior.
4. Check current official host documentation for supported transports, authentication, configuration, and optional extensions.
5. Preserve an explicitly requested version unless the user asks to upgrade it.
6. Keep experimental extensions isolated and label them clearly.

Do not combine a protocol or SDK migration with an unrelated feature unless required. If current documentation conflicts with existing code, explain the compatibility decision before changing behavior.

## Intake

Establish the server contract:

- Define the user goals the server must support.
- Identify the backing systems, data, actions, and credentials.
- Identify whether each operation is read-only, mutating, destructive, externally visible, expensive, or long-running.
- Identify the intended clients and where the server runs.
- Determine whether the server needs tools, resources, prompts, optional extensions, or only a subset.
- Define expected scale, latency, availability, data sensitivity, tenancy, and compliance constraints.
- Identify existing API clients, service modules, schemas, auth middleware, and logging conventions to reuse.

Ask a concise question only when the backing system, required action, identity model, or deployment target is ambiguous enough to change the architecture. Otherwise, state assumptions and proceed.

## Capability Selection

Select the primitive by who should control it and what it does:

- **Tools:** expose model-invokable retrieval or actions with explicit input and output contracts.
- **Resources:** expose addressable context that clients can discover and read.
- **Prompts:** expose reusable, user-selected prompt templates when the target host supports them.
- **Extensions:** add non-core behavior only when both the server and intended clients support the same documented extension.

Do not expose the same operation through multiple primitives without a clear client need.

### Tool Design

Design one tool around one recognizable user goal. Prefer focused names such as `list_projects`, `get_project`, and `update_project` over a generic `manage_project` tool with unrelated modes.

For every tool, define:

- a stable action-oriented name;
- a human-readable title when supported;
- a description that says when to use the tool and what it returns;
- an explicit input schema with types, constraints, defaults, and required fields;
- an output schema when structured results are supported;
- accurate behavior or safety annotations;
- authentication and authorization requirements;
- timeout, pagination, idempotency, and retry behavior when relevant;
- predictable success, partial-success, and error results.

Use semantic arguments rather than asking the model to construct raw URLs, SQL, shell commands, or provider payloads. Keep provider-specific details behind the handler boundary.

Return stable identifiers needed by later calls. Keep structured data concise and include a brief model-readable summary when the SDK supports both structured and textual content.

Treat annotations as descriptive metadata, not enforcement. A read-only or non-destructive hint does not replace server-side validation, permissions, or confirmation.

### Resource Design

- Use stable, documented URI schemes and templates.
- Define media types and encoding accurately.
- Authorize every read; never infer access from possession of a URI alone.
- Bound resource sizes or paginate large collections.
- Avoid embedding secrets or unnecessary personal data.
- Define cache behavior and update notifications only when supported by the selected protocol revision and client.
- Prevent path traversal, unsafe normalization, and cross-tenant resource access.

### Prompt Design

- Use prompts for reusable interaction templates, not hidden policy enforcement.
- Validate prompt arguments and clearly delimit inserted external content.
- Keep security and authorization in server code.
- Avoid prompts that depend on unsupported client behavior.

### Server Instructions

Use server-wide instructions only for cross-tool guidance such as required lookup-before-update sequences, shared limits, or common identifiers. Keep the opening concise and self-contained because hosts may prioritize or truncate it. Do not duplicate every tool description or attempt to override the host’s personality or safety policy.

## Architecture

Separate protocol concerns from business logic:

```text
MCP transport
  -> protocol and SDK adapter
  -> authentication and request identity
  -> schema validation
  -> authorization and policy checks
  -> application service
  -> external API, database, filesystem, or queue
  -> normalized result and error mapping
```

Keep handlers thin. Put reusable business behavior in service modules that can be tested without MCP transport. Reuse existing clients and repositories rather than duplicating integration logic inside tool handlers.

For multi-tenant systems, derive tenant and user scope from verified credentials or trusted deployment configuration. Never accept an unrestricted tenant identifier as authority from a model-provided argument.

## Transport Selection

Choose transport from the deployment boundary and current client support:

- Use **stdio** for local servers launched as child processes by a trusted host.
- Use the current standard **HTTP transport** for remote, shared, or independently deployed servers.
- Use a custom or legacy transport only for a documented compatibility requirement.

For stdio:

- Reserve stdout for protocol messages.
- Send diagnostics to stderr or the SDK’s supported logging path.
- Resolve executable paths, working directories, and environment variables deterministically.
- Handle startup, shutdown, cancellation, and broken pipes cleanly.
- Never print banners or debug output to stdout.

For HTTP:

- Use HTTPS outside local development.
- Follow the active MCP transport contract exactly, including required headers and lifecycle behavior.
- Apply request size limits, timeouts, concurrency limits, and rate limits.
- Configure CORS or origin validation only where the client model requires it; do not use CORS as authentication.
- Avoid hidden in-memory session dependence when the selected protocol revision or deployment architecture expects stateless requests.
- Design horizontal scaling and event delivery around documented SDK mechanisms.

## Authentication And Authorization

Apply authentication whenever the server exposes private data or user-specific actions. For remote servers, follow the current MCP authorization specification and target-client requirements; do not invent an OAuth flow from memory.

On every protected request:

1. Validate token signature or introspection response.
2. Validate issuer, audience or resource, expiration, and required scopes.
3. Derive the user and tenant from validated identity data.
4. Authorize the exact object and action.
5. Reject missing or insufficient authority before calling the backing service.

Use least-privilege scopes. Separate read and write permissions where practical. Keep service credentials in the existing secret-management system and never return them through MCP results, metadata, errors, or logs.

For consequential actions:

- make effects explicit in the tool description and result;
- validate ownership and current state immediately before mutation;
- support idempotency where retries could duplicate effects;
- require confirmation through the host’s supported flow when appropriate;
- return what changed, what did not, and how to recover when recovery exists.

Do not accept secrets through elicitation or ordinary tool arguments when a standard authentication flow should provide them.

## Input, Output, And Error Contracts

### Inputs

- Reject unknown or malformed values when accepting them would be unsafe.
- Apply length, range, enum, pattern, collection-size, and nesting limits.
- Normalize identifiers only according to the backing system’s rules.
- Treat every string as untrusted, including URLs, paths, queries, and instructions embedded in content.
- Parameterize database queries and avoid direct shell construction.

### Outputs

- Return only information needed to complete the user goal.
- Keep machine-useful fields in structured content when supported.
- Use stable IDs and absolute user-openable URLs when the client must cite or navigate to a source.
- Redact tokens, credentials, internal stack traces, and unnecessary personal data.
- Bound large payloads with pagination, summaries, resource links, or explicit truncation metadata.
- Match declared output schemas exactly.

### Errors

Map internal failures into actionable, non-sensitive categories:

- invalid input;
- unauthenticated;
- unauthorized;
- not found;
- conflict or stale state;
- rate limited;
- upstream unavailable;
- timeout or cancellation;
- internal failure.

Tell the model whether retrying, changing arguments, reauthenticating, or asking the user can resolve the error. Keep internal diagnostic details in protected logs with a correlation ID.

## Implementation Workflow

1. Inspect the repository and current MCP dependencies.
2. Resolve the active spec, SDK, and host requirements.
3. Write a capability inventory tied to concrete user goals.
4. Design schemas, permissions, side effects, errors, and result shapes.
5. Choose the transport and deployment boundary.
6. Implement the smallest end-to-end capability first.
7. Add authentication, authorization, limits, and observability at the boundary.
8. Add focused unit, protocol, integration, and security tests.
9. Connect through the target host and run realistic evaluation prompts.
10. Document setup, required environment variables, connection configuration, and verification commands.

Do not add a broad framework when the request needs one focused server or capability.

## Testing And Verification

Test at four layers:

### Handler Tests

- valid inputs and expected structured output;
- schema boundary values and malformed inputs;
- backing-service errors and timeouts;
- pagination, idempotency, and partial failure;
- output redaction and size limits.

### Security Tests

- missing, invalid, expired, wrong-audience, and under-scoped credentials;
- cross-user and cross-tenant access attempts;
- prompt-injection text inside tool inputs and upstream content;
- path traversal, query injection, SSRF, and unsafe redirects when relevant;
- duplicate write requests and stale-state mutations.

### Protocol Tests

- server startup or endpoint reachability;
- capability discovery and server instructions;
- tool, resource, and prompt listing;
- representative calls and declared output-schema validity;
- protocol error shape, cancellation, and shutdown;
- compatibility with the selected protocol revision.

### Host Tests

- connect with the official MCP Inspector or another compatible protocol client;
- connect from the actual target host;
- run direct, indirect, ambiguous, edge-case, and out-of-scope requests;
- verify the host selects the right tool and supplies valid arguments;
- verify consequential actions receive the intended approval behavior;
- verify final answers accurately represent completed, failed, and partial actions.

Prefer deterministic mocked tests for external systems. Keep live tests opt-in and prevent them from taking unintended write actions.

## Codex Connection

When the target is Codex:

- Verify current Codex documentation before writing configuration because supported fields can change.
- Use project configuration only for trusted-project, repository-specific servers.
- Use global configuration for personal servers intended across projects.
- Choose stdio command configuration for local processes and URL configuration for remote HTTP servers.
- Pass secrets through supported environment-variable references or authentication flows, not literal committed values.
- Configure enabled tools, approval behavior, startup timeout, tool timeout, and output limits when the current Codex configuration supports them.
- Restart or refresh the relevant Codex surface when required, then confirm the server and tools are visible.
- Use the Codex MCP management commands or interface for status, authentication, and troubleshooting when available.

Do not assume local Codex configuration automatically makes a remote server available in hosted ChatGPT. Use the appropriate hosted plugin or connector path for that surface.

## Deployment And Operations

For remote servers:

- deploy a stable HTTPS endpoint;
- use managed secrets and explicit environment configuration;
- enforce authentication, authorization, rate limits, and request limits;
- record latency, error category, tool name, and correlation identifiers;
- avoid logging credentials or sensitive arguments and results;
- add health checks that do not expose protected data;
- plan capacity, cold starts, upstream limits, retries, and backpressure;
- define versioning, backward compatibility, rollout, and rollback;
- verify the production endpoint through an MCP inspector and the target host.

Keep published tool names and schemas backward compatible when clients may cache or depend on them. Add optional fields and new tools carefully; use a deliberate version or migration path for breaking changes.

## Debugging Order

Trace failures in this order:

1. **Configuration:** server name, command or URL, arguments, environment, working directory, and enabled state.
2. **Startup or reachability:** executable errors, DNS, TLS, routing, endpoint path, and process exits.
3. **Transport and lifecycle:** protocol revision, headers, message framing, initialization or discovery behavior, and session assumptions.
4. **Authentication:** metadata discovery, callback, token acquisition, issuer, audience, expiry, and scopes.
5. **Capability discovery:** advertised tools, resources, prompts, instructions, extensions, and pagination.
6. **Schema validation:** argument names, types, required values, output shape, and SDK serialization.
7. **Authorization and handler:** identity scope, object permissions, backing-system request, timeout, and error mapping.
8. **Host behavior:** tool description clarity, tool selection, approval policy, output limits, and model interpretation.

Capture stderr, protected server logs, inspector traces, and host diagnostics. Do not expose tokens or sensitive payloads while debugging.

## Output Format

For implementation work, return:

```text
Server:
- User goals, target hosts, protocol/SDK basis, and transport.

Capabilities:
- Tools, resources, prompts, and their contracts.

Security:
- Authentication, authorization, validation, side effects, and secret handling.

Files changed:
- Implementation, tests, configuration, and documentation.

Verification:
- Tests, inspector checks, host checks, and results.

Connection:
- Exact local or remote setup with secrets omitted.

Open items:
- Live credentials, deployment, compatibility, or unverified behavior.
```

For design work, include the capability inventory, architecture, schemas, transport, identity model, threat boundaries, testing plan, deployment plan, and compatibility assumptions.

For reviews, lead with findings ordered by severity and cite the affected file or contract. Prioritize unauthorized access, unsafe writes, secret exposure, protocol incompatibility, schema mismatch, and missing negative tests.

## Subagent Orchestration

Use available custom agents for bounded implementation and independent review while keeping MCP capability, protocol, identity, and deployment decisions in this parent skill:

- `repo_explorer`: map the host application, service boundaries, SDK usage, existing transports, configuration, and tests.
- `implementation_worker`: implement an approved, file-bounded server, handler, schema, transport, configuration, or test change.
- `test_and_verification`: exercise parsing, discovery, tool and resource calls, errors, cancellation, transport behavior, builds, and target-host compatibility.
- `security_reviewer`: inspect authentication, scopes, object authorization, secret handling, injection, SSRF, path access, unsafe actions, and tool-permission boundaries.
- `docs_auditor`: verify installation, connection, capability, environment-variable, and usage documentation against server behavior.
- `eval_runner`: evaluate tool descriptions, tool selection, result usability, prompt behavior, and multi-step agent workflows against a frozen dataset and rubric.

Give every delegated request the user goal, approved MCP contract, target host and transport, bounded files or capabilities, authoritative evidence, prohibited actions, and expected return format. Apply these rules:

- Resolve names, descriptions, schemas, side effects, identity, authorization, and transport in the parent before assigning implementation.
- Keep credentials, dependency installation, live services, network access, deployments, and external writes prohibited unless explicitly authorized.
- Run `security_reviewer` and `test_and_verification` after implementation; neither agent owns final protocol or product decisions.
- Use `eval_runner` for model-facing behavioral quality, not as a substitute for protocol tests.
- Use `docs_auditor` after configuration and behavior settle.
- Wait for requested results, verify decisive evidence, reconcile conflicts, and deliver the final server decision in this parent skill.
- If custom-agent delegation is unavailable, perform the same scoped work directly.

## Completion Checklist

Before finishing, confirm:

- The active MCP spec, SDK, and host capabilities were verified.
- Every capability maps to a concrete user goal.
- Tool names, descriptions, schemas, annotations, and results are accurate.
- Protocol handlers are separated from business logic.
- Transport matches the deployment boundary and target clients.
- Every private operation authenticates and authorizes the exact request.
- Consequential actions are explicit, validated, and retry-safe where needed.
- Inputs, outputs, errors, and payload sizes are bounded.
- Secrets and unnecessary personal data stay out of results and logs.
- Negative, security, protocol, and host tests cover realistic failures.
- Connection instructions use supported configuration and omit secret values.
- Deployment includes observability, limits, compatibility, and rollback planning.

## Guardrails

- Do not invent MCP methods, schemas, transport behavior, SDK APIs, or host configuration fields.
- Do not implement raw protocol framing when an appropriate official SDK supports the target version.
- Do not place business authorization decisions in tool descriptions or model instructions.
- Do not trust tool annotations, client confirmations, resource URIs, or model-supplied identity as authorization.
- Do not expose a general shell, unrestricted SQL, arbitrary URL fetcher, or broad filesystem tool unless the user explicitly requires it and strong isolation is designed.
- Do not log protocol messages to stdout for stdio servers.
- Do not publish a remote server without HTTPS, authentication where needed, request limits, and production verification.
- Do not claim compatibility without testing through a real protocol client and the target host when available.
