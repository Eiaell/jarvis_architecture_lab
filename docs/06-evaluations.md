# Jarvis Evaluation Architecture

> Status: Work in progress

This document describes the evaluation philosophy and architectural direction used to assess Jarvis.

The objective is not simply to determine whether Jarvis can produce impressive responses.

The objective is to determine whether the system behaves reliably across memory, retrieval, reasoning, tool execution and deterministic boundaries.

A system that works once is a demonstration.

A system that behaves correctly across repeated controlled scenarios begins to provide engineering evidence.

---

## 1. Problem

Agentic AI systems are difficult to evaluate because many components are probabilistic.

A response may look good while important failures remain hidden.

For example:

- the answer may be correct but based on the wrong memory
- the correct tool may be selected with invalid parameters
- an action may succeed twice because of a retry
- stale information may silently influence reasoning
- the system may violate a project boundary
- a workflow may work once and fail on repetition
- the LLM may report success even though the external action failed

Therefore:

**output quality alone is not enough to evaluate an agentic system.**

Jarvis needs evaluation at several architectural layers.

---

## 2. Core Principle

The working principle is:

> **Evaluate behavior, not only answers.**

Jarvis evaluation should increasingly ask:

- What information was retrieved?
- Why was it retrieved?
- What decision was made?
- Which tool was selected?
- Was execution authorized?
- Did the action occur exactly once?
- Was the result verified?
- Was persistent state updated correctly?
- Did the system respect boundaries?
- Can the behavior be reproduced?

The final response is only one part of the system.

---

## 3. Evaluation Layers

Jarvis can be evaluated at several levels:

### Layer 1 — Component Evaluation

Evaluate individual components in isolation.

Examples:

- retrieval
- memory write
- schema validation
- tool adapter
- permission check

### Layer 2 — Integration Evaluation

Evaluate whether components interact correctly.

Examples:

Memory  
→ Retrieval  
→ Context

Reasoning  
→ Tool Proposal  
→ Execution

### Layer 3 — Workflow Evaluation

Evaluate complete multi-step tasks.

Example:

User request  
→ Context retrieval  
→ Planning  
→ Tool execution  
→ Verification  
→ Final response

### Layer 4 — Regression Evaluation

Determine whether a new change breaks behavior that previously worked.

This becomes increasingly important as Jarvis grows.

---

## 4. Evaluation Categories

The main evaluation categories currently considered are:

- memory correctness
- retrieval quality
- context quality
- reasoning behavior
- tool selection
- tool execution
- deterministic boundary enforcement
- workflow completion
- failure recovery
- security and isolation
- observability
- regression resistance

Each category tests a different part of system reliability.

---

## 5. Memory Evaluation

Memory evaluation asks whether persistent information remains trustworthy.

Important questions include:

- Was the correct information stored?
- Was provenance preserved?
- Was a generated interpretation incorrectly stored as fact?
- Was a correction applied?
- Was superseded information handled correctly?
- Were duplicate memories created?
- Can memory be traced back to evidence?

Possible scenarios include:

### New Fact

Input:
A new verified fact is introduced.

Expected:
The fact becomes available according to the memory policy.

### Correction

Existing memory:
Delivery date = Monday

New evidence:
Delivery date changed to Wednesday

Expected:
Wednesday becomes current while the previous value remains traceable.

### Contradiction

Two sources provide incompatible claims.

Expected:
The system does not silently merge them into a false certainty.

### Derived Information

The model generates an inference.

Expected:
The inference is not silently promoted to the same authority as direct evidence.

---

## 6. Retrieval Evaluation

Retrieval evaluation measures whether the correct information reaches active context.

Important dimensions include:

### Recall

Did retrieval find the information required for the task?

### Precision

How much retrieved information was actually relevant?

### Freshness

Did current information outrank superseded information?

### Scope

Did retrieval remain inside the correct project or information boundary?

### Provenance

Can important retrieved information be traced back to evidence?

### Context Efficiency

How much unnecessary information was included?

The goal is not to maximize recall at any cost.

The goal is to retrieve enough relevant information without polluting the context.

---

## 7. Context Evaluation

Even good retrieval can produce a bad context pack.

Context evaluation asks:

- Was essential information included?
- Was irrelevant information excluded?
- Were contradictions surfaced?
- Were priorities clear?
- Did context remain within budget?
- Was the active state represented correctly?

A possible quality model is:

Required information present  
+  
Current information preferred  
+  
Relevant constraints included  
+  
Provenance available  
−  
Irrelevant information  
−  
Contradictions hidden  
−  
Context overflow

---

## 8. Reasoning Evaluation

Reasoning quality is more difficult to evaluate deterministically.

Possible questions include:

- Did the model identify the correct task?
- Did it notice missing information?
- Did it choose a reasonable next step?
- Did it distinguish evidence from inference?
- Did it avoid claiming certainty where evidence was incomplete?
- Did it choose an action consistent with the user's intent?

Reasoning evaluation may combine:

- deterministic checks
- scenario-specific expected properties
- model-based evaluation
- human review

No single method should automatically be treated as ground truth.

---

## 9. Tool Selection Evaluation

Tool selection can be evaluated independently from execution.

Example:

Task:
Find a document.

Expected:
Use a retrieval capability.

Incorrect:
Attempt to create or modify a document.

Possible evaluation criteria include:

- appropriate capability selected
- unnecessary tools avoided
- read-only capability preferred when sufficient
- high-risk capability not selected unnecessarily
- required information gathered before action

---

## 10. Tool Argument Evaluation

Selecting the right tool is not enough.

Arguments must also be correct.

Tests can verify:

- required fields exist
- identifiers are valid
- values use allowed formats
- target resource is correct
- no hallucinated parameters appear
- sensitive information is excluded where unnecessary

Where possible, these checks should be deterministic.

---

## 11. Tool Execution Evaluation

Execution evaluation asks whether the external action behaved correctly.

Important questions include:

- Was the action authorized?
- Was schema validation successful?
- Was confirmation required?
- Did execution happen exactly once?
- Was the result verified?
- Was execution state recorded?
- Were failures classified correctly?

A successful model response does not compensate for incorrect execution.

---

## 12. Deterministic Boundary Evaluation

Critical boundaries should have explicit tests.

Examples:

### Permission Test

Attempt unauthorized action.

Expected:
Blocked.

### Confirmation Test

Attempt sensitive action without approval.

Expected:
Execution paused or rejected.

### Schema Test

Submit malformed parameters.

Expected:
Rejected before external execution.

### Retry Test

Force repeated provider failure.

Expected:
Execution stops after configured limit.

### Idempotency Test

Submit the same action multiple times.

Expected:
One external side effect.

### Context Boundary Test

Request information outside authorized project scope.

Expected:
Information remains excluded.

These evaluations should ideally produce binary pass/fail outcomes.

---

## 13. Workflow Evaluation

A workflow evaluation tests the system as a whole.

Example:

Goal:
Prepare a project update and send it after approval.

Expected workflow:

Retrieve project information  
↓  
Retrieve current decisions  
↓  
Generate update  
↓  
Validate recipient  
↓  
Request confirmation  
↓  
Execute send  
↓  
Verify result  
↓  
Record execution state

Evaluation asks whether each required stage occurred correctly.

The final message alone is insufficient evidence.

---

## 14. Partial Failure Evaluation

Real workflows rarely fail cleanly.

Example:

Step 1 succeeds.

Step 2 succeeds.

Step 3 fails.

Expected behavior should be defined.

Possible acceptable outcomes include:

- retry Step 3
- resume later from Step 3
- request user intervention
- execute compensation
- preserve partial state

Unacceptable behavior may include:

- repeating Steps 1 and 2 unnecessarily
- claiming the whole workflow completed
- losing execution history

---

## 15. Failure Injection

Reliable systems should be tested under failure conditions.

Possible injected failures include:

- network timeout
- malformed provider response
- authentication failure
- rate limit
- missing file
- invalid identifier
- duplicate request
- unavailable external service
- corrupted context candidate
- stale memory
- contradictory memory

The purpose is to observe whether Jarvis fails safely.

---

## 16. Adversarial Evaluation

Some evaluations should intentionally try to break architectural boundaries.

Examples:

- instruct the model to ignore permission checks
- request access to another project's information
- provide contradictory user instructions
- attempt to trigger repeated tool execution
- inject malformed tool output
- ask the model to treat inference as verified fact
- attempt to bypass confirmation using conversational pressure

The expected result is not necessarily a perfect response.

The important question is whether critical guarantees remain intact.

---

## 17. Repeatability

Probabilistic systems can behave differently across runs.

Important scenarios should therefore be repeated.

For example:

Run the same task 10 times.

Measure:

- successful completion
- tool selection consistency
- retrieval consistency
- boundary violations
- execution failures
- unnecessary variation

A system that succeeds once out of ten is not reliable.

A system that succeeds nine times out of ten may still be unacceptable for some high-risk actions.

Reliability targets should depend on consequence.

---

## 18. Golden Test Cases

Jarvis should gradually accumulate a set of known scenarios with expected behavior.

These can become a regression suite.

A golden test may contain:

- user request
- initial state
- available memory
- available tools
- expected retrieved information
- expected forbidden information
- expected action
- expected state transition
- acceptance criteria

Example:

### Test: Corrected Supplier Date

Initial memory:

Supplier delivery = Monday

Correction:

Supplier delivery = Wednesday

Request:

"When will the supplier deliver?"

Expected:

- Wednesday is used
- Monday is not presented as current
- correction remains traceable

This creates reproducible evidence.

---

## 19. Regression Testing

New features can silently break old behavior.

Every important architectural change should therefore ask:

**What previously validated behavior could this change affect?**

Examples:

New retrieval ranking  
→ could break project isolation

New memory system  
→ could lose correction history

New tool adapter  
→ could break idempotency

New planning behavior  
→ could bypass confirmation flow

Important past failures should become future regression tests.

---

## 20. Failure-to-Test Pipeline

A useful development pattern is:

Failure observed  
↓  
Failure understood  
↓  
Minimal reproducible case created  
↓  
Test added  
↓  
Fix implemented  
↓  
Regression suite updated

This converts mistakes into permanent engineering knowledge.

A failure that is fixed but never turned into a test can easily return.

---

## 21. Evaluation Dataset

Jarvis should eventually maintain an evaluation dataset.

The dataset should not contain sensitive personal information in the public repository.

Public examples can use fictional or sanitized scenarios.

Possible categories include:

- memory
- retrieval
- corrections
- entity resolution
- tool selection
- tool execution
- permissions
- workflow recovery
- context isolation

Private evaluation data can test the real system.

Public evaluation data can demonstrate the methodology.

---

## 22. Metrics

Not every component needs the same metric.

Possible metrics include:

### Retrieval

- recall
- precision
- freshness
- context size
- irrelevant context ratio

### Tool Execution

- success rate
- duplicate execution rate
- retry rate
- verification failure rate
- unauthorized execution rate

### Workflow

- completion rate
- steps completed correctly
- recovery rate
- average number of interventions

### Memory

- correction accuracy
- duplicate rate
- provenance coverage
- contradiction detection rate

### Reliability

- successful runs / total runs
- regression count
- boundary violations

Metrics should support engineering decisions rather than exist only for dashboards.

---

## 23. Binary vs. Graded Evaluations

Some behaviors should be binary.

Example:

Was unauthorized execution blocked?

PASS / FAIL

Other behaviors may require graded evaluation.

Example:

How relevant was the retrieved context?

Possible score:

0 — unrelated  
1 — weakly relevant  
2 — partially sufficient  
3 — sufficient  
4 — strong  
5 — ideal

Critical guarantees should prefer deterministic pass/fail evaluation where possible.

---

## 24. Acceptance Criteria

Features should have acceptance criteria before being considered validated.

Example:

### Memory Correction Feature

Acceptance criteria:

- corrected value becomes current
- old value remains traceable
- retrieval returns current value
- duplicate current values are not created
- provenance remains available

### Tool Execution Feature

Acceptance criteria:

- invalid schema cannot execute
- unauthorized action cannot execute
- duplicate action does not duplicate side effect
- state is recorded
- result is verifiable

This creates a clearer definition of "working."

---

## 25. Implemented vs. Validated

An important distinction in Jarvis is:

**Implemented**

The feature exists.

**Validated**

The feature has demonstrated expected behavior against defined evaluation criteria.

These are not the same thing.

A feature can be implemented and still be unreliable.

The public architecture documentation should avoid presenting implementation alone as proof of reliability.

---

## 26. Experimental Status

Features under active investigation can be marked as:

- planned
- experimental
- implemented
- validated
- deprecated

This status model makes documentation more honest and useful.

It also allows Jarvis to evolve without pretending that every experiment is already production architecture.

---

## 27. Release Gates

As Jarvis matures, important changes can use evaluation gates.

Conceptually:

Implementation  
↓  
Unit / Component Tests  
↓  
Evaluation Suite  
↓  
Regression Tests  
↓  
Shadow Mode  
↓  
Canary  
↓  
Production

Not every change requires every stage.

Higher-risk changes should face stronger gates.

Memory migrations and external action systems deserve particularly careful evaluation.

---

## 28. Shadow Mode

Shadow mode allows a new architecture to run without becoming authoritative.

Example:

Current retrieval system produces production context.

New retrieval system runs in parallel.

Results are compared.

But only the current system affects user behavior.

Conceptually:

Request  
├── Current System → Production Result
└── New System → Comparison Only

This allows real-world evaluation with lower migration risk.

---

## 29. Canary Evaluation

After shadow evaluation, a new component may be enabled for a limited percentage of cases.

This allows the system to measure real behavior while limiting exposure.

Canary evaluation can help detect:

- unexpected regressions
- provider-specific problems
- latency increases
- retrieval changes
- new failure modes

Rollback should remain possible.

---

## 30. Human Evaluation

Not everything can be captured by automated metrics.

Human review remains useful for:

- reasoning quality
- usefulness
- clarity
- ambiguity handling
- unexpected behavior
- edge cases

Human evaluation should ideally use defined criteria rather than only intuition.

For example:

Poor  
Acceptable  
Good  
Excellent

with descriptions of what each level means.

---

## 31. LLM-as-Judge

A model can sometimes assist with evaluation.

Possible uses include:

- comparing two answers
- checking whether required information is present
- evaluating clarity
- detecting contradictions
- categorizing failure types

However:

**an LLM should not be the sole judge of guarantees that can be checked deterministically.**

For example:

Do not ask another model:

"Did the permission system work?"

when software can directly verify whether permission was granted.

---

## 32. Evaluation Hierarchy

A useful preference order is:

Deterministic test  
↓  
Structured metric  
↓  
Human evaluation  
↓  
LLM-assisted evaluation  
↓  
Subjective impression

Use the strongest available evaluation method for each property.

---

## 33. Observability as Evaluation Infrastructure

Evaluations require visibility into what the system actually did.

Useful traces may include:

- retrieved context
- retrieval scores
- selected tools
- tool parameters
- policy decisions
- execution state
- retries
- verification results
- memory writes
- model outputs
- latency

Without observability, failures can appear as mysterious model behavior.

With observability, failures can often be assigned to specific components.

---

## 34. Cost and Performance

Reliability is not the only evaluation dimension.

Agentic systems also have operational costs.

Useful measurements include:

- token usage
- number of model calls
- number of tool calls
- execution latency
- retrieval latency
- external API cost
- memory storage growth

A new architecture may improve answer quality while making the system unnecessarily expensive or slow.

Evaluation should therefore consider trade-offs.

---

## 35. Evaluation Example

Suppose Jarvis receives:

"Continue the supplier project and tell me what changed."

An evaluation could inspect:

### Retrieval

Did it retrieve the correct supplier project?

### Memory

Did it retrieve the newest supplier information?

### Isolation

Did unrelated supplier data remain excluded?

### Reasoning

Did it correctly identify the meaningful changes?

### Provenance

Can those changes be traced to evidence?

### Response

Was the explanation accurate and useful?

The same user-visible answer may hide very different internal quality.

---

## 36. Evaluation Example: External Action

Request:

"Send the approved report."

Evaluation:

### Intent

Was the correct report identified?

### Authorization

Was the sender authorized?

### State

Was the report actually marked approved?

### Recipient

Was the correct destination resolved?

### Execution

Was the send action executed once?

### Verification

Was provider acknowledgement recorded?

### Audit

Can the action later be reconstructed?

Only then should the workflow be considered successfully completed.

---

## 37. Failure Severity

Not every failure has equal importance.

A possible severity model is:

### Low

Formatting or stylistic issue.

### Medium

Incorrect but recoverable reasoning.

### High

Incorrect persistent state or failed workflow.

### Critical

Unauthorized external action, privacy violation, destructive action or unrecoverable corruption.

Evaluation priorities should reflect severity.

A system can tolerate more stylistic variability than authorization failures.

---

## 38. Evaluation Priority

The current priority order is approximately:

1. prevent critical boundary violations
2. preserve canonical state integrity
3. prevent incorrect external actions
4. retrieve correct and current context
5. recover safely from failures
6. improve reasoning quality
7. optimize latency and cost
8. improve presentation quality

This order reflects consequence rather than visibility.

---

## 39. Test Documentation

Important tests should document:

- purpose
- initial state
- input
- expected behavior
- observed behavior
- result
- failure severity
- related architectural component

Example:

Test ID: MEM-CORRECTION-001

Purpose:
Verify that corrected information supersedes previous information.

Initial State:
delivery_date = Monday

Input:
"Delivery was moved to Wednesday."

Expected:
Wednesday becomes current.

Result:
PASS / FAIL

This format can eventually support automated evaluation reports.

---

## 40. Evaluation Report

A future evaluation report could summarize:

Jarvis Evaluation Run

Date:
Build:
Model:
Configuration:

Memory:
18 / 20 passed

Retrieval:
23 / 25 passed

Tool Execution:
15 / 15 passed

Boundaries:
20 / 20 passed

Workflows:
12 / 15 passed

Critical failures:
0

Regressions:
2

The goal is not to produce impressive numbers.

The goal is to create evidence about where the architecture is reliable and where it is not.

---

## 41. Public vs. Private Evaluations

The public repository can contain:

- methodology
- sanitized scenarios
- architectural test patterns
- example evaluation structures
- non-sensitive results

The private Jarvis implementation may contain:

- real personal context
- private integration tests
- private memory datasets
- credentials
- internal traces
- production-specific evaluation data

These should remain separated.

---

## 42. Evaluation as Documentation

Evaluations also document architecture.

A test reveals what the system considers important enough to guarantee.

For example:

Test:
Unauthorized action must never execute.

This communicates more architectural meaning than simply stating:

"The system has permissions."

Tests therefore serve both engineering and documentation purposes.

---

## 43. Evaluation as Learning

Jarvis is also a learning project.

Evaluations help transform:

"I think this architecture works"

into:

"I can demonstrate where it works, where it fails and under which conditions."

This is one of the central objectives of the project.

The purpose is not only to build features.

It is to understand the engineering properties of agentic systems.

---

## 44. Current Evaluation Rule

The working rule can be summarized as:

> Do not trust a feature because it worked once.

> Define expected behavior.

> Create reproducible scenarios.

> Test important guarantees deterministically.

> Turn failures into regression tests.

> Separate implementation from validation.

> Measure the system at the component and workflow level.

---

## 45. Reusable Evaluation Pattern

The reusable pattern being explored is:

Requirement  
↓  
Expected Behavior  
↓  
Test Scenario  
↓  
Execution  
↓  
Observation  
↓  
Pass / Fail / Score  
↓  
Failure Analysis  
↓  
Regression Test  
↓  
Architecture Improvement

This pattern is intended to make agentic development progressively more evidence-driven.

---

## 46. Current Status

Jarvis evaluation infrastructure remains under active development.

Not every evaluation strategy described in this document is currently automated.

Some represent existing practices, some are experimental and others describe the intended direction of the evaluation architecture.

The public documentation intentionally avoids claiming reliability that has not been demonstrated.

As Jarvis evolves, this document should increasingly distinguish between:

- evaluation methodology
- implemented tests
- validated behavior
- known failures
- unresolved risks

The long-term objective is to make architectural claims supported by evidence rather than by demonstration alone.
