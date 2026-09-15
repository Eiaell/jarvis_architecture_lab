# Jarvis — Personal Agentic AI System

[English](README.md) · [Español](README.es.md) · [Deutsch](README.de.md)

> **Read-only / portfolio repository.** This repository documents the architecture of Jarvis, a personal agentic AI assistant. It is not the real production system: it is a public, sanitized version meant to showcase design decisions, not to be run. The private implementation is maintained in a separate repository. See [SECURITY.md](SECURITY.md) for details on what is published and what is not.

## Table of Contents

- [What Jarvis Is](#what-jarvis-is)
- [What Problem It Solves](#what-problem-it-solves)
- [What I'm Exploring](#what-im-exploring)
- [High-Level Architecture](#high-level-architecture)
- [Key Architecture Decisions](#key-architecture-decisions)
- [Technical Documentation](#technical-documentation)
- [What This Repository Contains and Doesn't Contain](#what-this-repository-contains-and-doesnt-contain)
- [Documentation Philosophy](#documentation-philosophy)
- [Tools](#tools)
- [Current Status and Notice](#current-status-and-notice)

## What Jarvis Is

Jarvis is a personal agentic AI system I'm building as a hands-on environment for learning how to design reliable AI agents: ones capable of maintaining context, working with persistent memory, using external tools, and executing multi-step workflows without constant supervision.

The project started from a concrete question:

> **How can an AI assistant become more useful over time without relying solely on ever-larger prompts, nor on uncontrolled model behavior?**

## What Problem It Solves

Most LLM-based assistants lose context between sessions, don't distinguish between "remembering something" and "having it in the current context window," and delegate decisions to the model that should instead be deterministic (calculations, validations, permissions). Jarvis explores how to separate those responsibilities: what should be solved by the model through probabilistic reasoning, and what should be solved by deterministic code with explicit rules.

## What I'm Exploring

Jarvis works as an active learning and experimentation environment for:

- persistent memory
- structured context retrieval
- separation between memory and active context
- tool execution
- multi-step workflows
- deterministic vs. probabilistic behavior
- agent permissions and boundaries
- validation and evaluation
- reusable architectural patterns

## High-Level Architecture

```
User
  ↓
Interaction Layer
  ↓
Context Retrieval
  ↓
Memory System
  ↓
Reasoning / Decision Layer
  ↓
Tool Execution
  ↓
Validation
  ↓
Result
```

The exact architecture keeps evolving as the system is tested in practice. The full, layer-by-layer detail is in [`docs/architecture/system-overview.md`](docs/architecture/system-overview.md).

## Key Architecture Decisions

For anyone evaluating the technical depth of the project, these are the decisions that define Jarvis the most:

- **The model is not responsible for every guarantee in the system.** Arithmetic, business rules, and permission boundaries are executed by deterministic code; the LLM proposes, the code decides. See [`docs/05-deterministic-boundaries.md`](docs/05-deterministic-boundaries.md).
- **Memory separated from active context.** Persisting something doesn't mean the model "remembers" it on every turn: there's an explicit retrieval layer that decides what enters the prompt and why. See [`docs/02-memory-architecture.md`](docs/02-memory-architecture.md) and [`docs/03-context-retrieval.md`](docs/03-context-retrieval.md).
- **Secrets never reach the model.** The LLM receives access to a *capability* (a tool), not to the secret that tool needs to run. See the secrets section in [`docs/04-tool-execution.md`](docs/04-tool-execution.md) and in [SECURITY.md](SECURITY.md).
- **Everything that decides, predicts, or executes is validated before it's trusted.** Evaluation approaches and explicit acceptance criteria before any new capability is considered good. See [`docs/06-evaluations.md`](docs/06-evaluations.md).
- **Failures are documented, not hidden.** Every lesson learned — including what didn't work — is recorded as input for the next decision. See [`docs/07-lessons-learned.md`](docs/07-lessons-learned.md).

## Technical Documentation

| Document | Content |
|---|---|
| [`docs/01-overview.md`](docs/01-overview.md) | High-level architecture overview |
| [`docs/02-memory-architecture.md`](docs/02-memory-architecture.md) | Persistent memory architecture |
| [`docs/03-context-retrieval.md`](docs/03-context-retrieval.md) | Structured context retrieval |
| [`docs/04-tool-execution.md`](docs/04-tool-execution.md) | Tool execution and secret handling |
| [`docs/05-deterministic-boundaries.md`](docs/05-deterministic-boundaries.md) | Boundaries between deterministic and probabilistic |
| [`docs/06-evaluations.md`](docs/06-evaluations.md) | Evaluation methods and acceptance criteria |
| [`docs/07-lessons-learned.md`](docs/07-lessons-learned.md) | Lessons learned (living document) |
| [`docs/architecture/system-overview.md`](docs/architecture/system-overview.md) | Complete system architecture, layer by layer |

## What This Repository Contains and Doesn't Contain

This public repository contains architectural documentation, design decisions, sanitized examples, evaluation approaches, lessons learned, and reusable patterns.

Intentionally, it **does not** contain personal memories, private user data, credentials or API keys, production configuration, private prompts, or sensitive logs.

The full detail of what is published, what isn't, and why, is in [SECURITY.md](SECURITY.md).

## Documentation Philosophy

For every important component, I try to document:

1. The problem it solves
2. Why the component exists
3. Alternatives considered
4. The chosen approach
5. What must be deterministic
6. What can remain probabilistic
7. Failure modes
8. Tests and evaluations
9. Acceptance criteria
10. When the pattern should and shouldn't be reused

## Tools

The project is developed and explored using tools such as:

- Claude Code
- OpenAI Codex
- Git
- GitHub
- Python and other supporting tools when needed

## Current Status and Notice

Jarvis is under active development. The goal of this repository is not to present a finished product, but to document the architecture, decisions, experiments, mistakes, and lessons learned while building the system.

Jarvis is a personal, experimental project. In its current state it should not be considered a production-ready autonomous agent, nor should this repository be interpreted as a formal security audit (see [SECURITY.md](SECURITY.md)).

---

**Author:** [Engelbert Huber](https://github.com/Eiaell) — more projects and context on my [GitHub profile](https://github.com/Eiaell).
