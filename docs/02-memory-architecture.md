# Jarvis Memory Architecture

> Status: Work in progress  
> Scope: Public architectural documentation derived from the private Jarvis implementation.

This document describes the current memory architecture of Jarvis and, importantly, distinguishes between architectural direction, validated components and functionality that is not yet part of the production path.

The purpose of the memory system is not simply to store more information.

Its purpose is to provide Jarvis with **relevant, traceable and correctable context without turning the entire history of the system into one continuously growing prompt**.

---

## 1. Problem

Long-running AI assistants face a different problem from conventional chatbots.

A chatbot can often rely on the current conversation.

A persistent assistant cannot.

Over time it must deal with:

- thousands of pieces of evidence
- information that changes
- conflicting facts
- previous decisions
- unfinished tasks
- user preferences
- repeated entities
- past interactions
- external actions
- derived conclusions
- information that should no longer be trusted

Simply storing everything is not enough.

The system must answer a more difficult question:

**What information should be trusted and brought into the current context for this specific task?**

---

## 2. Core Principle

Jarvis separates three concepts that are often treated as the same thing:

**Storage**

Information exists somewhere in the system.

**Memory**

Information has been structured so that it can potentially influence future behavior.

**Active Context**

Information has been selected for the current interaction and is actually exposed to the model.

Therefore:

Stored information  
≠ Memory  
≠ Active context

A piece of information can exist in Jarvis without automatically becoming part of every future prompt.

---

## 3. High-Level Memory Architecture

The current architectural model is layered:

Raw Evidence  
↓  
Execution State  
↓  
Identity / Entity Layer  
↓  
Semantic Memory  
↓  
Episodic Memory  
↓  
Procedural Memory  
↓  
Derived Projections  
↓  
Retrieval Router  
↓  
Bounded Context Pack  
↓  
LLM

Each layer has a different responsibility.

The intention is to avoid treating "memory" as one undifferentiated database.

---

## 4. Raw Evidence

Raw Evidence is the lowest layer.

It represents information close to its original source before higher-level interpretation.

Examples may include:

- conversations
- documents
- files
- external responses
- observations
- imported information
- tool results

Raw evidence is important because higher-level memories can be wrong.

When possible, Jarvis should preserve enough provenance to trace a memory back toward the evidence from which it originated.

The principle is:

**Derived knowledge should not silently replace its source evidence.**

---

## 5. Execution State

Execution state is intentionally separated from semantic memory.

It answers operational questions such as:

- What task is currently running?
- What has already been executed?
- What remains pending?
- Has an external action already happened?
- Can an operation safely be retried?

This distinction is important because operational state often requires stronger guarantees than conversational memory.

An LLM may reason about what should happen next.

Software should determine whether an irreversible action has already happened.

This is part of a broader Jarvis principle:

**Claude handles ambiguity. Software enforces invariants.**

---

## 6. Identity and Entity Layer

Information becomes much more useful when the system understands what or whom it refers to.

The entity layer is intended to resolve relationships between information associated with:

- people
- organizations
- projects
- systems
- documents
- tasks
- concepts

Without identity resolution, the same real-world object can gradually become several disconnected memories.

Entity handling therefore sits between raw evidence and higher-level semantic interpretation.

This area is still evolving and should not currently be interpreted as a fully solved entity-resolution system.

---

## 7. Semantic Memory

Semantic memory represents relatively stable knowledge extracted from evidence.

Examples include:

- facts
- preferences
- project constraints
- definitions
- relationships
- known system state

Jarvis does not treat every generated sentence as a fact.

A working classification distinguishes at least:

**FACT**

Information supported directly by evidence.

**DECISION**

A choice that was explicitly made.

**DERIVED**

A conclusion produced from other information.

This distinction matters because a derived interpretation should not have the same authority as a directly supported fact.

---

## 8. Episodic Memory

Semantic memory answers:

**What is known?**

Episodic memory answers:

**What happened?**

Episodes preserve temporal structure around events and interactions.

An episode can represent something such as:

- a project event
- an operational case
- a sequence of actions
- a completed workflow
- a previous investigation
- a significant interaction

Temporal information is important because knowledge changes.

A statement that was correct six months ago may no longer describe the current state.

For that reason Jarvis increasingly treats time and provenance as first-class properties rather than optional metadata.

---

## 9. Procedural Memory

Procedural memory represents knowledge about **how a task should be performed**.

Examples include:

- validated workflows
- standard operating procedures
- reusable investigation patterns
- tool sequences
- decision processes

This is intentionally different from semantic knowledge.

Knowing that a process exists is not the same as knowing how to execute it reliably.

Procedural memory is especially important for Jarvis because repeated workflows should gradually require less reinvention.

---

## 10. Derived Projections

Jarvis may generate representations optimized for a particular use without treating those representations as the canonical source of truth.

Examples can include:

- summaries
- indexes
- graph relationships
- searchable views
- Markdown documentation
- vector representations

These are treated conceptually as **projections**.

A projection can be rebuilt.

The underlying evidence and canonical state should remain authoritative.

This distinction allows experimental technologies to be replaced without making them the foundation of the entire memory system.

---

## 11. Replaceable Retrieval Infrastructure

One architectural objective is to avoid coupling Jarvis permanently to a single retrieval technology.

Graph-based retrieval, vector retrieval and structured retrieval can each be useful, but none should automatically become the canonical memory itself.

For example, graph infrastructure such as Graphiti can be useful as a retrieval or relationship layer while remaining replaceable.

The architecture therefore aims toward:

Canonical information  
↓  
Multiple derived indexes / projections  
↓  
Retrieval

rather than:

Retrieval technology  
=  
Source of truth

---

## 12. Parallel Knowledge Views

Jarvis has also explored maintaining parallel representations of knowledge.

Conceptually these can include views such as:

Raw  
Graph  
Wiki  
Decisions  
Vector

These representations serve different purposes.

### Raw

Preserves source evidence.

### Graph

Represents relationships.

### Wiki

Produces human-readable structured knowledge.

### Decisions

Makes important architectural or project decisions explicitly visible.

### Vector

Supports similarity-based retrieval.

The goal is not to duplicate authority across five databases.

The goal is to create multiple useful projections from information whose provenance remains traceable.

---

## 13. Memory Gate

A critical architectural boundary is the **Memory Gate**.

Information should not become trusted long-term memory simply because an LLM generated it.

The Memory Gate is intended to control what information is promoted into persistent memory.

Conceptually:

Evidence  
↓  
Candidate extraction  
↓  
Validation  
↓  
Classification  
↓  
Provenance  
↓  
Memory write

This boundary helps reduce a dangerous failure mode:

LLM generates incorrect statement  
↓  
statement is stored as memory  
↓  
future LLM receives it as trusted context  
↓  
error reinforces itself

Memory therefore requires stricter controls than ordinary text generation.

---

## 14. Memory Correction

Persistent memory creates a new requirement:

**corrections must propagate.**

If information changes, Jarvis should not merely add a second contradictory memory and hope that retrieval selects the right one.

The architecture is moving toward explicit handling of:

- superseded information
- invalidation
- correction provenance
- temporal validity
- conflicting evidence

This is one of the highest-priority areas of the memory architecture.

The objective is to maintain:

**canonical memory + traceable correction history**

rather than an uncontrolled accumulation of statements.

---

## 15. Retrieval Router

The retrieval layer determines which available information should be considered for a specific task.

Retrieval should not simply mean:

"find the most similar text."

Different requests can require different forms of context.

The router can conceptually consider:

- semantic similarity
- entities
- recency
- project scope
- memory type
- provenance
- task state
- explicit user intent

The retrieval architecture is therefore designed as a hybrid system rather than a single similarity search.

---

## 16. Bounded Context Pack

The output of retrieval is not the full memory database.

It is a **bounded context pack**.

The context pack contains only the subset of information judged useful for the current task.

Conceptually:

Large persistent memory  
↓  
Retrieval  
↓  
Filtering  
↓  
Prioritization  
↓  
Bounded context  
↓  
LLM

This boundary matters for:

- token efficiency
- relevance
- latency
- reasoning quality
- privacy
- reduced contradiction

The objective is not maximum context.

The objective is **minimum sufficient context**.

---

## 17. Canonical State vs. Projections

A major architectural distinction in Jarvis is:

**Canonical state**

Information whose integrity matters.

versus

**Derived projections**

Representations that can be recreated.

This principle makes it possible to experiment with:

- graph databases
- embeddings
- search indexes
- summaries
- generated Markdown
- alternative retrieval systems

without allowing any experimental representation to silently become the system's source of truth.

---

## 18. Current Implementation Reality

The architecture described here represents the direction of Jarvis, but not every layer should be interpreted as equally mature.

One important rule of the project is:

**documentation must distinguish intended architecture from production reality.**

The current production path still contains legacy memory behavior in some areas.

A newer architecture has been evaluated, but migration is intentionally controlled.

The existing RBA lifecycle is treated as a protected and stable behavior boundary.

New memory infrastructure should not be introduced by breaking previously validated behavior.

---

## 19. Controlled Migration Strategy

Memory migrations are particularly dangerous because failures can silently corrupt future reasoning.

The current strategy therefore favors staged migration.

The intended sequence includes concepts such as:

Backfill  
↓  
Shadow mode  
↓  
Comparison  
↓  
Canary  
↓  
Production

Important requirements include:

- idempotent backfill
- feature flags
- rollback capability
- observable differences
- preservation of existing behavior
- no regressions in validated lifecycle logic

A new system should not become authoritative simply because it works once.

---

## 20. Why Idempotency Matters

Memory pipelines often need to be replayed.

For example:

- rebuilding indexes
- importing historical evidence
- recovering after failure
- testing a new extraction pipeline

Running the same operation twice should not silently create duplicated state.

For this reason, idempotency is treated as an architectural requirement for backfill and migration workflows.

---

## 21. Evaluation

Memory quality cannot be evaluated only by asking whether retrieval "looks good."

Important evaluation questions include:

- Was the correct evidence retrieved?
- Was irrelevant information excluded?
- Was newer information preferred when appropriate?
- Were corrections respected?
- Was provenance preserved?
- Did retrieval remain within context limits?
- Did the system invent memory that did not exist?
- Did migration change previously validated behavior?

Future evaluation should increasingly use reproducible datasets and acceptance criteria rather than manual inspection alone.

---

## 22. Failure Modes

Important memory failure modes currently considered include:

### Memory pollution

Incorrect generated information becomes persistent.

### Retrieval omission

Relevant information exists but is not retrieved.

### Context overload

Too much information reaches the model.

### Stale memory

Old information is retrieved after it has been superseded.

### Duplicate identity

The same entity is represented several times.

### Projection drift

A derived index stops matching canonical information.

### Hidden contradiction

Two memories conflict without the system recognizing the conflict.

### Migration regression

A new architecture changes behavior that was already validated.

### Provenance loss

The system knows something but can no longer explain where it came from.

---

## 23. Deterministic and Probabilistic Boundaries

The memory architecture deliberately separates responsibilities.

The LLM can help with:

- interpretation
- extraction
- summarization
- semantic relationships
- relevance estimation

Software should enforce:

- schemas
- identifiers
- permissions
- write boundaries
- state integrity
- idempotency
- versioning
- invalidation rules
- action state
- migration guarantees

This separation is fundamental to the architecture.

---

## 24. Current Priorities

The current memory priorities are approximately:

1. canonical memory integrity
2. correction propagation
3. retrieval quality
4. persistent task and action state
5. provenance
6. deterministic boundaries
7. safe migration
8. evaluation

Additional complexity should only be introduced when the previous layer has demonstrated sufficient reliability.

---

## 25. Architectural Rule

The working rule for Jarvis memory can be summarized as:

> Store evidence.  
> Structure memory.  
> Preserve provenance.  
> Retrieve selectively.  
> Correct explicitly.  
> Keep guarantees in software.

---

## 26. Reusable Pattern

The broader pattern being explored is:

Evidence  
↓  
Validated memory formation  
↓  
Canonical state  
↓  
Replaceable projections  
↓  
Hybrid retrieval  
↓  
Bounded context  
↓  
Reasoning

This pattern may be useful for systems that need persistent context across long periods while retaining auditability and the ability to correct previous knowledge.

---

## 27. When This Pattern Is Useful

This architecture is potentially useful when:

- the assistant operates across many sessions
- information changes over time
- provenance matters
- tasks span multiple interactions
- external tools are involved
- incorrect memory could influence future actions
- the system must remain inspectable

---

## 28. When It May Be Unnecessary

This architecture may be excessive when:

- conversations are short-lived
- no information needs to persist
- the assistant performs isolated tasks
- provenance has little value
- simple retrieval is sufficient

Complex memory should not be added merely because an application uses an LLM.

---

## 29. Current Status

The memory architecture remains under active development.

Some concepts described here are implemented, some are being validated and others represent the intended direction of the system.

This repository intentionally avoids presenting experimental architecture as completed production infrastructure.

As Jarvis evolves, this document will be updated to reflect what has actually been validated.
