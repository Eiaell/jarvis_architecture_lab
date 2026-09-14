# Jarvis Architecture Overview

> Status: Work in progress

This document describes the current architectural direction of Jarvis.

Jarvis is a personal agentic AI system used as a practical environment for exploring how an AI assistant can maintain useful context over time, work with persistent memory, interact with tools and execute multi-step workflows while keeping important guarantees outside the LLM.

The architecture is intentionally evolving as different approaches are implemented, tested and evaluated.

---

## 1. Problem

A conventional chatbot usually operates mainly on the information available inside the current conversation.

As interactions become longer and tasks become more complex, several problems appear:

- relevant information can disappear from the active context
- prompts can grow continuously
- important facts can be duplicated or become inconsistent
- an LLM may make decisions that should instead be enforced by software
- tool execution introduces additional failure modes
- long-running workflows require state beyond a single interaction

Jarvis is an attempt to explore these problems through implementation rather than only theory.

---

## 2. Design Goal

The goal is not to create an unrestricted autonomous agent.

The goal is to explore an architecture where:

- the LLM handles reasoning where probabilistic reasoning is useful
- software handles guarantees where deterministic behavior is required
- memory is separated from active context
- tools operate behind explicit boundaries
- important actions can be validated
- failures can be observed and evaluated
- architectural decisions can eventually become reusable patterns

---

## 3. High-Level Architecture

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

This representation is intentionally simplified.

Individual components may evolve as Jarvis is tested.

---

## 4. Main Architectural Areas

### Interaction Layer

Receives the user's request and prepares it for the rest of the system.

Questions currently being explored include:

- What information should enter the active context?
- What information should remain outside the LLM?
- How should user intent influence context retrieval?

---

### Context Retrieval

Responsible for determining which previously stored information is relevant to the current task.

The objective is to avoid relying on continuously growing prompts.

Important questions include:

- relevance
- recency
- conflicting information
- retrieval limits
- context prioritization

---

### Memory System

Stores information that may be useful beyond the current interaction.

Memory and active context are treated as different concepts.

Not every stored item should automatically be sent to the model.

The memory architecture is documented separately in:

`docs/02-memory-architecture.md`

---

### Reasoning / Decision Layer

Uses the active context to determine the next useful action.

This is one of the areas where probabilistic model behavior can be valuable.

However, decisions that require hard guarantees should not depend exclusively on the LLM.

---

### Tool Execution

Allows Jarvis to interact with external capabilities.

Examples may include:

- information retrieval
- file operations
- APIs
- workflows
- external services

Tool access should be constrained by explicit permissions and validation.

---

### Validation

Checks whether important conditions have been satisfied before considering a task complete.

Possible validation mechanisms include:

- schemas
- deterministic rules
- assertions
- tests
- permission checks
- output verification

---

## 5. Deterministic vs. Probabilistic Responsibilities

A central architectural question in Jarvis is:

**What should the LLM decide, and what should software guarantee?**

A useful working principle is:

LLM  
→ interpretation  
→ reasoning  
→ planning  
→ language generation

Software  
→ permissions  
→ state integrity  
→ validation  
→ schemas  
→ irreversible action boundaries  
→ deterministic guarantees

This boundary is still being tested and refined.

---

## 6. Current Development Philosophy

Jarvis is developed experimentally.

For each architectural component the process is approximately:

Problem  
↓  
Hypothesis  
↓  
Implementation  
↓  
Test  
↓  
Failure analysis  
↓  
Architectural decision  
↓  
Documentation

Failures are considered useful evidence.

The objective is not only to make Jarvis work, but to understand **why a particular architecture works and under which conditions it fails**.

---

## 7. Reusable Architecture

One long-term objective of the project is to determine which parts of Jarvis can become reusable patterns for other agentic systems.

Each reusable pattern should eventually document:

1. Problem
2. Context
3. Alternatives considered
4. Decision
5. Deterministic guarantees
6. Probabilistic components
7. Failure modes
8. Tests
9. Acceptance criteria
10. When to use
11. When not to use

The intention is to gradually transform lessons learned from Jarvis into a practical blueprint for future personal or domain-specific AI agents.

---

## 8. Current Limitations

Jarvis is not currently presented as a production-ready autonomous system.

The architecture is still evolving and several components remain experimental.

Documentation in this repository should therefore be interpreted as:

**architecture under active evaluation rather than a finished reference implementation.**
