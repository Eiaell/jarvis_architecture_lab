# Jarvis Context Retrieval Architecture

> Status: Work in progress

This document describes the architectural direction of context retrieval in Jarvis.

It should not be interpreted as a claim that every mechanism described here is already implemented in the production path.

The purpose of retrieval is not to give the LLM as much information as possible.

The purpose is to provide the **smallest useful set of trustworthy context required for the current task**.

---

## 1. Problem

Persistent AI systems accumulate information over time.

Eventually, the amount of available information becomes much larger than what should be placed inside a single model context.

A long-running assistant may have access to:

- previous conversations
- project information
- decisions
- preferences
- documents
- task history
- external evidence
- tool results
- corrections
- temporary operational state

Sending everything to the model would create several problems:

- context windows become unnecessarily large
- irrelevant information competes with relevant information
- old information can conflict with newer information
- token usage increases
- latency increases
- reasoning quality can decrease
- private information may be exposed unnecessarily
- provenance becomes harder to understand

Jarvis therefore treats retrieval as an architectural component rather than a simple search feature.

---

## 2. Core Principle

The central retrieval principle is:

> **Maximum available memory should not produce maximum active context.**

Instead:

Large memory space  
↓  
Task interpretation  
↓  
Candidate retrieval  
↓  
Filtering  
↓  
Ranking  
↓  
Conflict handling  
↓  
Context budget  
↓  
Bounded Context Pack  
↓  
LLM

The objective is:

**minimum sufficient context**

rather than:

**maximum possible context**

---

## 3. Retrieval Is Different From Memory

Memory determines what information can persist.

Retrieval determines what information becomes relevant now.

These are separate responsibilities.

A fact can exist in persistent memory without being retrieved.

A document can exist in the evidence layer without being placed into active context.

A previous decision may become relevant only when a related project or task appears again.

Therefore:

Stored  
≠ Retrieved  
≠ Active context

---

## 4. Retrieval Inputs

Retrieval begins with the current task.

Potential signals include:

- the user's request
- detected entities
- active project
- current workflow
- task type
- conversation context
- explicit references
- temporal constraints
- previously retrieved context
- operational state

Different tasks may require different retrieval strategies.

For example:

A question about a person may require entity-oriented retrieval.

A project continuation may require previous decisions and project state.

A factual investigation may require source evidence.

A workflow continuation may require execution state.

---

## 5. Retrieval Router

Jarvis is exploring the idea of a retrieval router.

The router is responsible for deciding which sources of information should be queried for the current request.

Conceptually:

Current Request
↓
Intent / Task Analysis
↓
Retrieval Router
↓
Relevant Memory Sources
↓
Candidate Context

Possible sources may include:

- semantic memory
- episodic memory
- procedural memory
- project state
- raw evidence
- previous decisions
- external information
- current conversation

The router should avoid querying every information source for every request.

---

## 6. Hybrid Retrieval

No single retrieval technique is expected to solve every context problem.

Jarvis therefore explores a hybrid retrieval approach.

Possible retrieval signals include:

### Semantic similarity

Useful when the wording of the current request differs from previously stored information.

### Keyword or lexical matching

Useful when exact terms, identifiers or names matter.

### Entity matching

Useful when information is associated with a specific person, project, organization or system.

### Temporal relevance

Useful when newer information should replace or outweigh older information.

### Project scope

Useful for avoiding information leakage between unrelated projects.

### Memory type

Useful when a task specifically needs facts, procedures, episodes or decisions.

### Explicit references

Useful when the user directly points to a previous task, document or conversation.

The retrieval architecture should be capable of combining several of these signals.

---

## 7. Candidate Retrieval

Retrieval should initially produce candidates rather than immediately injecting information into the model context.

Conceptually:

Query
↓
Candidate retrieval
↓
Candidate set
↓
Evaluation
↓
Selected context

This separation is important because similarity alone does not guarantee usefulness.

A candidate can be:

- highly similar but outdated
- relevant but untrusted
- correct but unnecessary
- related to the wrong entity
- superseded by newer information
- useful only as supporting evidence

Candidate retrieval is therefore only one stage of the pipeline.

---

## 8. Filtering

After candidate retrieval, information may need to be filtered.

Filtering criteria can include:

- project boundaries
- permissions
- information type
- temporal validity
- superseded state
- source quality
- provenance availability
- current task relevance

A retrieval result should not automatically become context merely because a search system returned it.

---

## 9. Ranking

Relevant candidates may then be ranked.

A conceptual relevance score could consider several dimensions:

Relevance  
+ Entity match  
+ Recency  
+ Source confidence  
+ Project alignment  
+ Task usefulness

The exact scoring mechanism remains subject to experimentation.

The architectural principle is more important than a specific formula:

**retrieval quality should depend on more than semantic similarity.**

---

## 10. Recency and Temporal Validity

Persistent systems must understand that information changes.

Consider:

January:
"Project X is using architecture A."

June:
"Architecture A has been replaced by architecture B."

A similarity-only retrieval system could return both statements without understanding that one supersedes the other.

Jarvis therefore treats temporal relevance as an important retrieval dimension.

Information may eventually require properties such as:

- created_at
- observed_at
- valid_from
- valid_until
- superseded_by
- corrected_at

The exact schema remains part of the evolving architecture.

---

## 11. Conflict Detection

Retrieval can expose contradictions.

For example:

Memory A:
Supplier delivery date is Monday.

Memory B:
Supplier delivery date was changed to Wednesday.

The correct behavior is not necessarily to send both statements to the LLM without explanation.

The retrieval system should eventually be capable of identifying possible conflicts and providing structured context such as:

Current value  
Previous value  
Reason for change  
Source  
Timestamp

Conflict handling is particularly important for long-lived memory.

---

## 12. Provenance

Retrieved information should retain a path toward its origin whenever possible.

Useful provenance may include:

- source
- timestamp
- document
- conversation
- tool result
- memory identifier
- evidence identifier

The principle is:

> **Context should be explainable.**

If Jarvis relies on a memory when reasoning, the system should increasingly be able to answer:

**Why does Jarvis believe this?**

---

## 13. Context Prioritization

Not all retrieved information deserves equal space.

A possible prioritization hierarchy is:

### Required

Information necessary to execute the task correctly.

### Important

Information that materially improves reasoning.

### Supporting

Useful evidence or background.

### Optional

Information that may help but should be removed first when context becomes constrained.

This creates a more deliberate context-building process.

---

## 14. Context Budget

Retrieval must operate under a context budget.

The system should not continue adding information indefinitely simply because relevant memories exist.

The budget can consider:

- model context limits
- task complexity
- expected response size
- tool requirements
- conversation history
- information priority

Conceptually:

Available context budget  
↓
Required information  
↓
Important information  
↓
Supporting information  
↓
Stop when budget is reached

This provides a deterministic constraint around a probabilistic reasoning system.

---

## 15. Bounded Context Pack

The final retrieval output is a bounded context pack.

A context pack can conceptually contain:

- current task
- essential entities
- relevant facts
- previous decisions
- active constraints
- supporting evidence
- provenance
- relevant workflow state

The context pack should be:

- relevant
- compact
- traceable
- current
- internally consistent where possible

It should not simply be a raw dump of search results.

---

## 16. Context Pack Example

A conceptual example:

Current task:
Continue planning Project X.

Relevant entities:
Project X
Organization Y

Current state:
Prototype phase.

Relevant decision:
Architecture B was selected.

Constraint:
Budget limit remains active.

Previous decision:
Architecture A was rejected due to operational complexity.

Source references:
Decision record 17
Project note 42

This is significantly more useful than injecting dozens of unrelated historical messages.

---

## 17. Retrieval vs. Reasoning

Retrieval and reasoning should remain conceptually separate.

Retrieval answers:

**What information may be useful?**

Reasoning answers:

**What should be concluded or done with that information?**

Mixing these responsibilities can create hidden assumptions.

Jarvis therefore aims toward a pipeline where retrieved evidence remains distinguishable from model-generated interpretation.

---

## 18. Deterministic and Probabilistic Responsibilities

The retrieval system combines both types of behavior.

The LLM may help with:

- intent interpretation
- relevance estimation
- semantic relationships
- query expansion
- ambiguity resolution

Software should enforce:

- permissions
- project boundaries
- context limits
- schemas
- identifiers
- filtering rules
- source tracking
- exclusion rules

A useful architectural rule is:

> Use the model to understand meaning.  
> Use software to enforce boundaries.

---

## 19. Privacy and Scope

Retrieval introduces an important privacy risk.

Just because information is stored does not mean every task should have access to it.

Context retrieval should eventually consider:

- project boundaries
- user boundaries
- information sensitivity
- tool permissions
- task relevance

This becomes increasingly important as Jarvis connects more tools and information sources.

---

## 20. Retrieval Failure Modes

Important failure modes include:

### Retrieval omission

Relevant information exists but is not returned.

### Retrieval pollution

Irrelevant information enters the context.

### Stale retrieval

Old information is selected instead of the current state.

### Entity confusion

Information about one entity is incorrectly associated with another.

### Context overflow

Too much information reaches the model.

### Similarity trap

Semantically similar information is retrieved even though it is not operationally relevant.

### Missing provenance

Information is retrieved without sufficient evidence about its origin.

### Hidden contradiction

Conflicting memories are retrieved without being recognized as conflicting.

### Cross-project leakage

Information from one project incorrectly influences another.

---

## 21. Evaluation

Retrieval should eventually be measured using reproducible evaluations.

Possible metrics include:

### Recall

Did the system retrieve the information required to answer correctly?

### Precision

How much retrieved information was actually useful?

### Freshness

Did the system prefer current information over superseded information?

### Context efficiency

How much of the context pack contributed meaningfully to the task?

### Provenance coverage

Can important retrieved information be traced to a source?

### Isolation

Did unrelated project or user information remain outside the context?

---

## 22. Retrieval Test Cases

Example evaluation scenarios may include:

### Known fact retrieval

Ask for information known to exist in memory.

Expected:
Correct fact is retrieved.

### Correction retrieval

Store an old value and a corrected value.

Expected:
Current value is preferred and correction history remains traceable.

### Project isolation

Store similar information across two projects.

Expected:
Only information from the active project is retrieved.

### Entity ambiguity

Store two entities with similar names.

Expected:
The correct entity is selected or ambiguity is explicitly surfaced.

### Context pressure

Provide more potentially relevant information than the context budget allows.

Expected:
Higher-priority information survives.

---

## 23. Retrieval Observability

A retrieval system is difficult to improve when its decisions are invisible.

Jarvis should increasingly make retrieval observable.

Useful diagnostic information may include:

- query generated
- sources queried
- candidates returned
- candidates rejected
- ranking scores
- filtering reasons
- final context size
- provenance references

This information does not necessarily belong in the user-facing response.

It is useful for development, debugging and evaluation.

---

## 24. Retrieval as Infrastructure

Retrieval should eventually be treated as infrastructure rather than prompt engineering.

Instead of manually adding more text to a system prompt when information is missing, the architecture should ask:

- Why was the information not retrieved?
- Was it stored correctly?
- Was the entity recognized?
- Was the retrieval strategy appropriate?
- Was it filtered incorrectly?
- Was the context budget too restrictive?

This turns context management into an engineering problem that can be tested and improved.

---

## 25. Current Design Rule

The working retrieval rule for Jarvis can be summarized as:

> Retrieve broadly enough to avoid missing critical information.

> Filter aggressively enough to avoid context pollution.

> Preserve provenance.

> Prefer current information.

> Respect boundaries.

> Build the smallest context that is sufficient for the task.

---

## 26. Reusable Pattern

The reusable architecture currently being explored is:

Task  
↓  
Intent and scope  
↓  
Retrieval routing  
↓  
Hybrid candidate retrieval  
↓  
Filtering  
↓  
Ranking  
↓  
Conflict and freshness handling  
↓  
Context budget  
↓  
Bounded Context Pack  
↓  
Reasoning

This pattern may be useful for long-running AI systems where persistent information grows substantially over time.

---

## 27. When This Pattern Is Useful

This architecture becomes particularly useful when:

- memory persists across sessions
- many projects coexist
- facts can change
- provenance matters
- information comes from multiple sources
- context size must remain controlled
- the assistant performs complex multi-step work

---

## 28. When It May Be Unnecessary

A sophisticated retrieval layer may be unnecessary when:

- conversations are temporary
- available information is small
- no persistent memory exists
- the full dataset easily fits into context
- information rarely changes
- simple search already solves the problem

Architecture should follow actual complexity rather than adding infrastructure for its own sake.

---

## 29. Current Status

Context retrieval in Jarvis remains under active development.

Some mechanisms are part of the current system, while others described here represent architectural direction or areas under evaluation.

This public documentation intentionally focuses on the design principles and engineering problems without exposing private memory, personal information, internal prompts or sensitive implementation details.

As implementation and evaluations mature, this document will be updated to distinguish more precisely between:

- implemented
- validated
- experimental
- planned
