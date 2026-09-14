# Jarvis — Personal Agentic AI System

[English](README.md) · [Español](README.es.md) · [Deutsch](README.de.md)

> Work in progress.

Jarvis is a personal agentic AI system I am building as a practical environment to learn how reliable AI agents can maintain context, work with memory, use tools and execute multi-step workflows.

The project started from a simple question:

**How can an AI assistant become useful over time without relying only on increasingly large prompts or uncontrolled LLM behavior?**

## What I am exploring

Jarvis currently serves as a learning and experimentation environment for:

- persistent memory
- structured context retrieval
- separation between memory and active context
- tool execution
- multi-step workflows
- deterministic vs. probabilistic behavior
- agent permissions and boundaries
- validation and evaluation
- reusable architectural patterns

## Current status

Jarvis is under active development.

The objective of this repository is not to present a finished product.

It documents the architecture, decisions, experiments, failures and lessons learned while building the system.

## Core idea

An LLM should not be responsible for every guarantee in an agentic system.

Some tasks are appropriate for probabilistic reasoning.

Others require deterministic software, explicit validation and controlled execution.

A major part of this project is learning where that boundary should be.

## High-level architecture

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

The exact architecture continues to evolve as the system is tested.

## What this repository contains

This public repository contains:

- architectural documentation
- design decisions
- sanitized examples
- evaluation approaches
- lessons learned
- reusable patterns

It intentionally does **not** contain:

- personal memories
- private user data
- credentials or API keys
- production configuration
- private prompts
- sensitive logs

## Why I am building Jarvis

I am transitioning deeper into AI, automation and digital systems.

Rather than learning only from courses, I use Jarvis as a practical project where I can encounter real architectural problems, test approaches and document what works and what does not.

My goal is eventually to turn the lessons from Jarvis into a reusable blueprint for building personal or domain-specific AI agents.

## Tools

The project is developed and explored using tools including:

- Claude Code
- OpenAI Codex
- Git
- GitHub
- Python and supporting tooling where appropriate

## Documentation philosophy

For each major component, I aim to document:

1. The problem it solves
2. Why the component exists
3. Alternatives considered
4. The chosen approach
5. What should be deterministic
6. What can remain probabilistic
7. Failure modes
8. Tests and evaluations
9. Acceptance criteria
10. When the pattern should and should not be reused

## Disclaimer

Jarvis is an experimental personal project and should not currently be considered a production-ready autonomous agent.
