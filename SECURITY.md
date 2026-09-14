# Security and Privacy

Jarvis is a personal agentic AI research and development project.

This public repository is intentionally limited to architectural documentation, sanitized examples and reusable engineering patterns.

The private Jarvis implementation is maintained separately.

## What Is Not Published Here

This repository must not contain:

- API keys
- access tokens
- passwords
- `.env` files
- private credentials
- personal memory data
- private conversations
- sensitive user information
- internal production configuration
- private prompts containing personal context
- authentication details
- sensitive execution logs
- private datasets
- unrestricted exports from the real Jarvis system

## Public vs. Private Architecture

The purpose of this repository is to document:

- architectural decisions
- engineering principles
- system boundaries
- evaluation methods
- sanitized examples
- reusable patterns
- lessons learned

It is not intended to expose the complete private implementation.

A design may therefore be documented publicly while its production configuration, credentials, private data and sensitive implementation details remain private.

## Secrets

Secrets should never be committed to this repository.

Examples include:

- API credentials
- database passwords
- OAuth tokens
- service-account credentials
- private keys
- authentication cookies

Secrets used by the private Jarvis implementation should be managed outside version-controlled public files.

## Personal Data

Jarvis may interact with personal or contextual information in its private environment.

Real personal information should not be copied into this repository for demonstrations.

Public examples should use:

- fictional identities
- sanitized data
- synthetic scenarios
- generic identifiers

For example:

`user_123`

is preferable to exposing a real person's private information.

## Memory Data

The public repository may describe the architecture of persistent memory.

It should not contain the actual private memory store used by Jarvis.

Documentation can explain:

- memory types
- retrieval strategies
- provenance
- correction mechanisms
- evaluation methods

without exposing the underlying personal data.

## Tool Integrations

Jarvis may experiment with external tools and services.

Public documentation may describe the architecture of those integrations.

It should not expose:

- credentials
- private endpoints
- sensitive identifiers
- authentication configuration
- private tool outputs

## Logs and Traces

Execution logs can unintentionally contain sensitive information.

Before publishing any trace, screenshot, evaluation result or example, it should be reviewed for:

- personal information
- credentials
- internal identifiers
- private URLs
- access tokens
- sensitive tool parameters

Public examples should be sanitized before publication.

## Architecture Principle

Jarvis follows the general principle of least privilege.

A component should receive only the permissions required to perform its responsibility.

The architecture also aims to keep secrets outside the LLM context whenever possible.

Conceptually:

LLM
↓
Structured Tool Request
↓
Controlled Execution Layer
↓
Credential Injection
↓
External Service

The model should normally receive access to a capability rather than direct access to the secret that enables it.

## Reporting a Security Issue

If a security issue is discovered in material published in this repository, please avoid posting sensitive details in a public GitHub issue.

Sensitive information should not be disclosed publicly while the issue is being reviewed.

## Repository Scope

This repository documents an experimental system under active development.

It should not be interpreted as a production security specification or as evidence that every architecture described here has already been implemented or formally audited.

Security controls, like the rest of the Jarvis architecture, are progressively implemented, tested and evaluated.
