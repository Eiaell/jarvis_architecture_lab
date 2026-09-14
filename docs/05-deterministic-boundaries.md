# Jarvis Deterministic Boundaries

> Status: Work in progress

This document describes one of the central architectural ideas behind Jarvis:

**not every responsibility in an agentic system should be delegated to an LLM.**

Large language models are useful because they can interpret ambiguous requests, reason across incomplete information and generate flexible responses.

Those same characteristics make them unsuitable for enforcing guarantees that must always hold.

Jarvis therefore explores an architecture where probabilistic reasoning is surrounded by deterministic software boundaries.

---

## 1. Problem

An LLM can produce different outputs for the same input.

That is often useful.

It is also dangerous when the system must guarantee things such as:

- permissions
- data integrity
- execution limits
- valid schemas
- action state
- retry behavior
- irreversible action protection
- identity consistency
- auditability

Prompting the model to "always follow the rules" is not equivalent to enforcing those rules.

A reliable agentic system therefore needs to decide:

**What can be probabilistic?**

and:

**What must be guaranteed by software?**

---

## 2. Core Principle

The working Jarvis principle is:

> **Use the LLM for ambiguity. Use software for invariants.**

Conceptually:

Uncertain / semantic problem  
→ LLM reasoning

Hard system rule  
→ deterministic software

This does not mean that every component must be deterministic.

It means that important guarantees should not depend only on model behavior.

---

## 3. Probabilistic Responsibilities

LLMs are useful for tasks where there may be several valid answers.

Examples include:

- interpreting user intent
- resolving linguistic ambiguity
- summarizing information
- generating plans
- estimating relevance
- extracting meaning
- choosing among reasonable alternatives
- generating explanations
- understanding relationships between concepts
- deciding which information may be useful

These responsibilities benefit from flexibility.

---

## 4. Deterministic Responsibilities

Software should usually control responsibilities where correctness requires explicit guarantees.

Examples include:

- schema validation
- authorization
- permission checks
- identity constraints
- state transitions
- execution limits
- retry limits
- idempotency
- data validation
- context budgets
- irreversible action boundaries
- secret handling
- audit logging

These are not questions of interpretation.

They are system invariants.

---

## 5. What Is an Invariant?

An invariant is a condition that should remain true regardless of what the model generates.

Examples:

A user without permission cannot delete a resource.

An action requiring confirmation cannot execute without confirmation.

A workflow cannot exceed its configured retry limit.

The same idempotent action should not execute twice.

A malformed tool request cannot reach the external provider.

These conditions should be enforced outside the LLM.

---

## 6. Why Prompt Rules Are Not Enough

Consider the instruction:

> Never delete anything without asking the user first.

This may work most of the time.

But the guarantee still depends on model behavior.

A stronger architecture is:

LLM requests deletion  
↓  
Execution layer detects destructive action  
↓  
Confirmation policy checks authorization  
↓  
No confirmation exists  
↓  
Execution blocked

In this architecture, even if the LLM ignores the instruction, the system remains protected.

This is the difference between:

**behavioral guidance**

and:

**architectural enforcement**

---

## 7. Layered Protection

A useful architecture does not rely on a single safety mechanism.

For example:

LLM reasoning  
↓  
Structured output schema  
↓  
Policy validation  
↓  
Permission validation  
↓  
Execution boundary  
↓  
External system

Each layer reduces a different type of failure.

This is similar to defense-in-depth principles used in other software systems.

---

## 8. Schema Boundary

Structured outputs provide one deterministic boundary.

Instead of allowing arbitrary text such as:

"Send the project report to everyone."

the reasoning layer can produce a structured request:

    action: send_message
    recipients:
      - user_123
    document_id: report_42

Software can then verify:

- action exists
- recipient exists
- document exists
- requester has permission
- required fields are present

Natural-language reasoning remains flexible.

Execution input becomes constrained.

---

## 9. Permission Boundary

Permissions should not be inferred only from natural language.

For example:

The model should not decide:

"The user probably has access."

The system should verify actual permission state.

Conceptually:

Requested Action  
↓  
Actor Identity  
↓  
Required Capability  
↓  
Permission Check  
↓  
ALLOW / DENY

The result should be deterministic.

---

## 10. State Boundary

LLMs should not be the authoritative source of operational state.

For example:

The model may say:

"The email was already sent."

That statement does not prove the action occurred.

Operational state should come from software records or external verification.

Examples of deterministic state include:

- action status
- workflow step
- confirmation status
- retry count
- external resource ID
- timestamps
- execution result

---

## 11. Identity Boundary

Persistent systems need stable identifiers.

Names alone are often ambiguous.

For example:

"Alex"

could refer to several people.

Jarvis should increasingly distinguish:

Human-readable label

from:

Canonical identifier

Conceptually:

"Alex"  
↓  
Entity Resolution  
↓  
entity_93821

The model can help interpret identity.

Software should preserve canonical identifiers.

---

## 12. Context Boundary

Context retrieval can use probabilistic relevance.

But the amount of context provided to the model should remain bounded.

For example:

Retrieval candidates  
↓  
Ranking  
↓  
Filtering  
↓  
Context budget  
↓  
Final context pack

The model may help rank information.

Software can enforce:

- maximum context size
- project boundaries
- permission boundaries
- excluded data classes

---

## 13. Memory Write Boundary

One of the most important boundaries in Jarvis is persistent memory.

Generated text should not automatically become trusted memory.

Conceptually:

Model Output  
↓  
Candidate Memory  
↓  
Validation  
↓  
Classification  
↓  
Provenance  
↓  
Persistent Write

This reduces the risk of:

hallucination  
↓  
persistent memory  
↓  
future retrieval  
↓  
reinforced hallucination

Persistent state should have a stricter write boundary than conversational output.

---

## 14. Execution Boundary

The execution boundary separates:

**thinking**

from:

**acting**

Conceptually:

Reasoning  
↓  
Proposed action  
↓  
Execution boundary  
↓  
External action

The execution boundary can enforce:

- permission
- confirmation
- schema
- idempotency
- retry policy
- action limits
- target validation

This allows Jarvis to reason freely without giving the reasoning model unrestricted authority.

---

## 15. Irreversible Action Boundary

Some actions require stronger guarantees than others.

Examples include:

- deleting data
- sending money
- publishing information
- changing permissions
- sending external communications

For these operations, the system should require stronger checks.

A conceptual policy might be:

Read operation  
→ low friction

Reversible write  
→ validated execution

External side effect  
→ additional policy checks

Irreversible / sensitive action  
→ explicit confirmation + validation

The exact policy may vary.

The architectural principle remains stable.

---

## 16. Retry Boundary

Retries should not be controlled exclusively by model reasoning.

Without limits, an agent can create loops.

For example:

Action fails  
↓  
Model retries  
↓  
Action fails  
↓  
Model retries  
↓  
...

Software should enforce boundaries such as:

- maximum retries
- maximum workflow steps
- timeout
- cost budget
- tool-call budget

Once the boundary is reached, execution stops.

---

## 17. Idempotency Boundary

Retries introduce the possibility of duplicate external actions.

For actions that support idempotency:

Action intent  
↓  
Stable action identifier  
↓  
Execution

A repeated request with the same identifier should return the existing result rather than create a second side effect.

This property cannot reliably be implemented through prompt instructions alone.

---

## 18. Validation Boundary

A model can produce plausible but invalid information.

Validation should therefore happen after generation when correctness can be checked deterministically.

Examples include:

- JSON schema validation
- data type validation
- identifier verification
- required field checks
- file existence
- numerical ranges
- allowed enum values
- state transition rules

The LLM generates candidates.

Software validates candidates.

---

## 19. Verification Boundary

Execution and success are not always the same thing.

An external API may report success even when the intended outcome is incomplete.

For important operations:

Action  
↓  
Execution  
↓  
Verification  
↓  
State transition to completed

Examples:

Create resource  
→ fetch it and confirm existence

Update record  
→ read updated value

Upload file  
→ verify final object

Verification creates a stronger guarantee than trusting a single response.

---

## 20. Secrets Boundary

Credentials should normally remain outside the model context.

Preferred architecture:

LLM  
↓  
Structured tool request  
↓  
Secure execution layer  
↓  
Credential injection  
↓  
External provider

The reasoning model does not need to know the secret.

It only needs to know that the capability exists.

---

## 21. Audit Boundary

Important operations should create records that do not depend on model recollection.

An audit event may record:

- action identifier
- timestamp
- actor
- tool
- parameters
- authorization result
- execution result
- verification result
- errors

This allows the system to reconstruct what happened later.

---

## 22. Model Confidence Is Not Authorization

A recurring design mistake in agentic systems is treating model confidence as operational authority.

For example:

"I am highly confident this is what the user wants."

does not equal:

"The user authorized this action."

Confidence is probabilistic.

Authorization is state.

Jarvis keeps these concepts separate.

---

## 23. Deterministic Wrapper

One way to think about Jarvis is:

Deterministic System  
┌─────────────────────────────┐
│                             │
│      Probabilistic LLM      │
│                             │
└─────────────────────────────┘

The model operates inside boundaries defined by software.

The objective is not to make the LLM deterministic.

The objective is to prevent probabilistic behavior from violating critical system guarantees.

---

## 24. Boundary Placement

The difficult architectural question is not:

"Should everything be deterministic?"

It is:

**Where should deterministic boundaries be placed?**

Too few boundaries:

- unsafe execution
- unreliable state
- difficult debugging
- hidden failures

Too many boundaries:

- excessive complexity
- slow development
- reduced flexibility
- unnecessary infrastructure

The architecture should place guarantees where failure has meaningful consequences.

---

## 25. Risk-Based Boundary Design

The stronger the consequence of failure, the stronger the deterministic boundary should be.

Conceptually:

Low impact  
→ model flexibility acceptable

Moderate impact  
→ structured validation

High impact  
→ deterministic policy

Irreversible impact  
→ deterministic policy + explicit authorization + verification

This creates a risk-based approach rather than applying identical controls everywhere.

---

## 26. Example: Reading Information

User asks:

"Find the latest project notes."

Possible architecture:

User intent  
↓  
LLM interprets query  
↓  
Retrieval system searches  
↓  
Project boundary filters results  
↓  
Context budget applied  
↓  
Results returned

The LLM helps interpret meaning.

Software enforces access and scope.

---

## 27. Example: Sending a Message

User asks:

"Send the update to the team."

Possible architecture:

LLM interprets request  
↓  
Resolve "team"  
↓  
Generate message  
↓  
Structured send request  
↓  
Validate recipients  
↓  
Check permission  
↓  
Check confirmation policy  
↓  
Execute once  
↓  
Verify provider response  
↓  
Record action

The language generation is probabilistic.

The delivery boundary is deterministic.

---

## 28. Example: Memory Correction

Suppose Jarvis knows:

Supplier delivery: Monday.

Later the user says:

"It changed to Wednesday."

The LLM can interpret that this is a correction.

But software should manage:

- previous value
- new value
- timestamp
- supersession relationship
- provenance
- canonical state

The model interprets the change.

The memory system preserves consistency.

---

## 29. Example: Workflow

A workflow contains:

1. Retrieve project data
2. Generate report
3. Validate report
4. Send report

The model may help perform steps 1 and 2.

Software can guarantee:

- step ordering
- schema checks
- validation requirement
- confirmation before sending
- execution state
- retry limits

This creates a hybrid workflow.

---

## 30. Failure Modes

Important failures include:

### Prompt-only enforcement

Important rules exist only in instructions to the model.

### State hallucination

The model incorrectly believes an external action occurred.

### Permission inference

The model assumes access without deterministic verification.

### Duplicate execution

Retry logic creates repeated side effects.

### Context leakage

Information crosses project or permission boundaries.

### Memory pollution

Generated information becomes persistent without validation.

### Unbounded loops

The model repeatedly calls tools.

### False completion

The model reports success before verification.

### Secret exposure

Credentials enter model context unnecessarily.

---

## 31. Testing Deterministic Boundaries

Boundaries should be tested directly.

Example tests include:

### Invalid schema

Provide malformed tool arguments.

Expected:

Rejected.

### Missing permission

Request an unauthorized action.

Expected:

Blocked.

### Missing confirmation

Attempt sensitive action without approval.

Expected:

Blocked.

### Retry overflow

Force repeated provider failure.

Expected:

Stops after configured limit.

### Duplicate action

Submit the same idempotent action twice.

Expected:

One external side effect.

### Context isolation

Request information from Project A while Project B contains similar content.

Expected:

Project B data does not enter context.

### Invalid state transition

Attempt to move workflow directly from `proposed` to `completed`.

Expected:

Rejected.

---

## 32. Acceptance Criteria

A deterministic boundary is useful only if its behavior can be verified.

Examples of acceptance criteria:

- unauthorized actions cannot execute
- malformed tool requests cannot reach providers
- sensitive operations cannot bypass confirmation
- workflow retries remain bounded
- duplicate idempotent requests do not duplicate side effects
- persistent memory writes retain provenance
- project boundaries are respected
- execution state remains externally inspectable

These criteria should increasingly become automated tests.

---

## 33. Observability

Deterministic systems are easier to debug when their decisions are observable.

Useful events include:

- policy accepted
- policy rejected
- schema rejected
- permission denied
- confirmation required
- execution started
- retry attempted
- retry exhausted
- verification failed
- duplicate prevented

The goal is to understand not only what happened but:

**which boundary made the decision.**

---

## 34. Boundary Ownership

Each critical guarantee should have a clear owner.

For example:

Permission check  
→ authorization layer

Schema validation  
→ execution layer

Memory provenance  
→ memory layer

Retry limits  
→ workflow engine

Context budget  
→ retrieval layer

Secret injection  
→ secure tool adapter

This prevents important guarantees from existing vaguely across prompts and application logic.

---

## 35. Avoiding Duplicate Responsibility

The same rule should not be independently implemented in many places without a clear source of truth.

For example:

Retry limit = 3

should ideally belong to a defined execution policy.

Not:

- prompt says 3
- workflow says 5
- tool adapter says 10
- developer assumes 2

Duplicated rules create inconsistent behavior.

---

## 36. Architecture as Contracts

Jarvis increasingly treats boundaries as contracts between components.

For example:

Reasoning layer promises:

"I will produce an action proposal matching this schema."

Execution layer promises:

"I will never execute an invalid or unauthorized proposal."

Memory layer promises:

"I will preserve provenance for accepted persistent information."

Retrieval layer promises:

"I will respect scope and context limits."

Contracts make component responsibilities explicit.

---

## 37. LLM Replacement

A useful architectural test is:

**If the LLM provider changes tomorrow, which guarantees disappear?**

Ideally:

- permissions remain
- memory integrity remains
- execution limits remain
- audit history remains
- identity remains
- workflow state remains

The quality of reasoning may change.

Critical system guarantees should not.

This reduces coupling between the reliability of the architecture and the behavior of one particular model.

---

## 38. Provider Independence

Jarvis should avoid making system guarantees dependent on provider-specific prompt behavior.

Different models may:

- interpret instructions differently
- call tools differently
- produce different plans
- have different context limits

A deterministic architecture can allow model experimentation while preserving critical behavior.

---

## 39. Reusable Pattern

The general pattern is:

User Request  
↓  
Probabilistic Interpretation  
↓  
Structured Proposal  
↓  
Deterministic Boundary  
↓  
Validated State Transition / Action  
↓  
Verification  
↓  
Observable Result

This architecture can be applied beyond Jarvis.

It is useful whenever an AI system moves from generating text toward operating software or managing persistent state.

---

## 40. When This Pattern Is Useful

Deterministic boundaries become increasingly important when:

- tools create external side effects
- state persists across sessions
- multiple users or projects exist
- privacy matters
- actions may be irreversible
- workflows run for long periods
- failures need to be reproducible
- auditability matters

---

## 41. When Simpler Architecture Is Enough

Not every LLM application requires extensive deterministic infrastructure.

A simpler architecture may be sufficient when:

- the system only generates disposable text
- no external actions occur
- there is no persistent state
- no sensitive information is involved
- failures have minimal consequences

Complexity should be proportional to risk.

---

## 42. Design Heuristic

A useful question for each architectural decision is:

**What happens if the LLM gets this wrong?**

If the answer is:

"the wording may be slightly worse"

the responsibility may remain probabilistic.

If the answer is:

"data can be deleted"

"an external action can occur twice"

"private information can leak"

"state can become corrupted"

then a deterministic boundary is probably required.

---

## 43. Current Architectural Rule

The current Jarvis rule can be summarized as:

> Let the model interpret ambiguity.

> Let the model reason.

> Let the model propose.

> Let software validate.

> Let software authorize.

> Let software preserve state.

> Let software enforce limits.

> Let verification determine whether an important action actually succeeded.

---

## 44. Current Status

The deterministic-boundary architecture remains under active development.

Some boundaries already exist in the Jarvis implementation, while others represent architectural direction, planned hardening or areas still under evaluation.

This document intentionally describes the design principles without claiming that every boundary is already implemented.

As Jarvis evolves, architectural documentation should increasingly distinguish between:

- implemented
- validated
- experimental
- planned

The long-term objective is not to make Jarvis perfectly deterministic.

It is to make the **right parts deterministic**.
