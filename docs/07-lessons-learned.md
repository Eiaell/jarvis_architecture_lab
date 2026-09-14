# Jarvis — Lessons Learned

> Status: Living document

This document captures architectural lessons emerging from the development and review of Jarvis.

It is intentionally a living document.

The objective is not to present these ideas as universal truths, but to record the principles that have become useful while building, testing and rethinking a persistent agentic AI system.

Some lessons come from successful design decisions.

Others come from limitations, failed approaches, architectural tension or questions that remain unresolved.

---

## 1. Building an Agent Is Different From Building a Chatbot

One of the first lessons is that a persistent agent introduces problems that do not exist in the same way in a conventional chatbot.

A chatbot can often rely mainly on:

- the current conversation
- a system prompt
- model reasoning

A persistent agent must additionally manage:

- memory
- state
- identity
- tools
- external actions
- permissions
- long-running workflows
- failures
- corrections
- provenance

The architecture changes once the system is expected to remember and act over time.

---

## 2. More Context Is Not Automatically Better

A natural early instinct is to provide the model with as much information as possible.

This does not scale well.

Large context can introduce:

- irrelevant information
- contradictions
- higher latency
- higher token usage
- weaker attention to important details
- unnecessary privacy exposure

The better objective is not:

**maximum context**

but:

**minimum sufficient context**

This makes retrieval and context construction architectural problems rather than prompt-size problems.

---

## 3. Memory Is Not a Database Dump

Persistent storage alone does not create useful memory.

A system can store everything and still fail to recall the right thing.

Useful memory requires decisions about:

- what deserves persistence
- what type of memory it is
- where it came from
- whether it is still valid
- how it relates to entities
- whether it has been corrected
- when it should be retrieved

This leads to an important distinction:

Storage  
≠ Memory  
≠ Active Context

---

## 4. Provenance Becomes More Important Over Time

When an AI assistant remembers something for months, a new question becomes important:

**Why does the system believe this?**

Without provenance, memory becomes difficult to trust and difficult to correct.

A useful memory architecture should increasingly preserve links between:

Evidence  
↓  
Interpretation  
↓  
Memory  
↓  
Retrieval  
↓  
Reasoning

This does not mean every user-facing response needs to expose the entire chain.

It means the system should preserve enough evidence internally to reconstruct it when necessary.

---

## 5. Corrections Are Harder Than Initial Memory Writes

Storing information is relatively easy.

Correcting information reliably is much harder.

If the system stores:

Delivery = Monday

and later learns:

Delivery = Wednesday

simply adding both values creates ambiguity.

A persistent system therefore needs concepts such as:

- supersession
- temporal validity
- correction history
- canonical current state

This is one of the strongest reasons not to treat memory as an append-only collection of model-generated statements.

---

## 6. Generated Text Should Not Automatically Become Trusted Memory

LLMs can produce plausible but incorrect conclusions.

If generated content automatically enters persistent memory, an error can become self-reinforcing:

Incorrect generation  
↓  
Persistent memory  
↓  
Future retrieval  
↓  
Model sees incorrect information as context  
↓  
Error becomes more convincing

Persistent memory therefore deserves a stronger write boundary than ordinary conversational text.

---

## 7. Retrieval Is an Architecture, Not Just Vector Search

Semantic similarity is useful.

It is not enough.

A persistent assistant may need to consider:

- semantic similarity
- exact names
- entities
- current project
- recency
- memory type
- provenance
- explicit references
- permissions
- workflow state

A result can be semantically similar and still be wrong for the current task.

Retrieval therefore benefits from hybrid signals and explicit scope.

---

## 8. Active Context Should Be Built Deliberately

The final context shown to the model should not simply be the raw result of a search.

A useful pipeline is closer to:

Candidate Retrieval  
↓  
Filtering  
↓  
Ranking  
↓  
Conflict Handling  
↓  
Prioritization  
↓  
Context Budget  
↓  
Bounded Context Pack

Context construction becomes a controlled stage of the architecture.

---

## 9. The LLM Should Not Own System Guarantees

One of the most important lessons in Jarvis is:

**prompt instructions are not guarantees.**

An instruction such as:

"Never perform this action without confirmation"

is useful guidance.

It is not equivalent to:

software blocks the operation unless confirmation state exists.

This distinction leads to the principle:

> Use the LLM for ambiguity.  
> Use software for invariants.

---

## 10. Planning and Acting Must Remain Separate

An agent should be able to consider an action without performing it.

This sounds obvious, but it becomes critical once external tools exist.

A useful separation is:

Reasoning  
↓  
Proposed Action  
↓  
Validation  
↓  
Authorization  
↓  
Execution

This allows:

- inspection
- user confirmation
- dry runs
- testing
- permission checks
- policy enforcement

Reasoning becomes safer when it is not automatically equivalent to authority.

---

## 11. A Tool Call Is an Operational Event

Once a model can interact with external systems, tool calls should no longer be treated as just another type of generated text.

Tool execution introduces concerns such as:

- permissions
- schemas
- retries
- timeouts
- duplicate execution
- partial failure
- audit history
- irreversible side effects

This means tool execution deserves its own architecture.

---

## 12. Idempotency Is More Important Than It First Appears

Distributed systems fail in ambiguous ways.

A tool may successfully perform an action while Jarvis receives a timeout.

If Jarvis retries blindly, the action may happen twice.

Examples include:

- sending messages
- creating records
- triggering workflows
- performing transactions

Stable action identifiers and idempotent execution can prevent an entire class of failures.

This is a software guarantee, not a reasoning problem.

---

## 13. State Should Not Live Only in Conversation

The model's conversation is not a reliable source of operational truth.

The model may believe:

"The action completed."

But external state may disagree.

Important state should therefore exist independently from the LLM.

Examples include:

- task state
- action state
- approval state
- retry count
- workflow step
- external resource identifier

Language can describe state.

Software should own state.

---

## 14. Long Workflows Need Recovery, Not Just Success Paths

It is easy to design:

Step 1  
↓  
Step 2  
↓  
Step 3  
↓  
Success

Real systems must also handle:

Step 1 succeeds  
↓  
Step 2 succeeds  
↓  
Step 3 fails

The architecture must answer:

- What already happened?
- What should be retried?
- What should not repeat?
- Can execution resume?
- Is rollback possible?
- Is human intervention needed?

Failure handling is part of workflow architecture, not an optional addition.

---

## 15. Human Approval Can Be a Feature

Maximum autonomy is not automatically the best design.

For high-impact actions, human approval can provide a deliberate architectural boundary.

The useful question is not:

"Can the agent do everything by itself?"

It is:

**Which actions should the agent perform autonomously, and where should authority remain with the user?**

This produces a more practical concept of autonomy.

---

## 16. Least Privilege Applies to AI Agents Too

Giving an agent access to an application does not mean it needs every capability of that application.

For example:

A document search agent may need:

- read

but not:

- delete
- change permissions
- permanently modify data

Capability-based access reduces the consequences of incorrect reasoning.

---

## 17. Secrets Should Stay Outside the Model Whenever Possible

The LLM does not need to know an API key in order to use an API.

The safer pattern is:

Model  
↓  
Structured Tool Request  
↓  
Secure Execution Layer  
↓  
Credential Injection  
↓  
Provider

Reducing secret exposure improves both security and architecture.

---

## 18. Replaceable Components Are Valuable

AI infrastructure changes quickly.

Models change.

Vector databases change.

Graph tools change.

Providers change.

Retrieval techniques change.

If one technology becomes the canonical representation of everything, replacing it later becomes difficult.

This motivates the distinction between:

Canonical State

and:

Derived Projections

A graph, vector index or generated wiki can be useful without becoming the only source of truth.

---

## 19. Experimental Infrastructure Should Remain Replaceable

New technologies are attractive in agentic systems.

But every experiment should answer:

- What problem does this solve?
- Is the problem already solved elsewhere?
- What happens if this technology is removed?
- Does it become authoritative?
- Can its data be rebuilt?

Architecture should not become permanently dependent on an experiment before its value is demonstrated.

---

## 20. Architecture Complexity Must Be Earned

It is easy to continuously add:

- databases
- agents
- routers
- graphs
- indexes
- queues
- planners
- evaluators

Each component adds:

- dependencies
- failure modes
- debugging cost
- maintenance
- migration requirements

A useful rule is:

> Add architecture when a demonstrated problem requires it.

Not:

> Add architecture because the technology is interesting.

---

## 21. Reliability Before Autonomy

A system that performs more actions is not necessarily more advanced.

Increasing autonomy before increasing reliability can amplify failures.

A useful development order is:

Reliable state  
↓  
Reliable retrieval  
↓  
Reliable tools  
↓  
Reliable execution  
↓  
Reliable evaluation  
↓  
More autonomy

Autonomy should be earned by demonstrated reliability.

---

## 22. Observability Changes Debugging

Without traces, many failures look like:

"The AI did something strange."

With observability, the same failure may become:

- wrong memory retrieved
- stale context selected
- invalid tool argument generated
- provider timed out
- permission policy rejected action
- retry limit triggered

This transforms agentic debugging from intuition into engineering.

---

## 23. Every Important Failure Should Become a Test

A useful engineering loop is:

Failure  
↓  
Understand Cause  
↓  
Create Reproducible Scenario  
↓  
Add Test  
↓  
Fix  
↓  
Keep Regression Test

A bug that is fixed without becoming a test can easily return.

Over time, the test suite becomes a record of what the system has learned.

---

## 24. Implementation Is Not Validation

A component existing in the codebase does not prove it works reliably.

This distinction is important:

**Implemented**

means:

The feature exists.

**Validated**

means:

The feature has demonstrated expected behavior against defined criteria.

Jarvis documentation should preserve this distinction.

---

## 25. Architecture Claims Should Be Evidence-Based

Statements such as:

"Jarvis has reliable memory"

are too broad without evidence.

A stronger claim would be:

"In the current evaluation suite, corrected facts supersede previous values while preserving provenance."

The second statement is narrower but more meaningful.

The objective is to gradually replace architectural claims with demonstrated behavior.

---

## 26. Small Reproducible Evaluations Are Valuable

Not every evaluation needs a huge benchmark.

A small test such as:

Old value = Monday  
New value = Wednesday  
Question = What is the current delivery date?

can reveal important properties about:

- memory
- correction
- retrieval
- temporal validity

Small controlled scenarios often expose architectural weaknesses faster than large demonstrations.

---

## 27. Critical Properties Should Prefer Binary Tests

Some properties should not be evaluated by "vibes."

For example:

Did an unauthorized action execute?

The answer should be:

PASS / FAIL

Other properties such as writing quality can remain graded or subjective.

The evaluation method should match the property being tested.

---

## 28. Model Quality and System Quality Are Different

A stronger model can improve:

- reasoning
- interpretation
- planning
- language quality

But it does not automatically fix:

- broken permissions
- duplicate execution
- poor state management
- missing provenance
- unsafe retries
- incorrect schemas

Better models can improve the system.

They cannot replace architecture.

---

## 29. Model Independence Is a Useful Test

A useful architectural question is:

**What breaks if the model provider changes?**

Reasoning quality may change.

But ideally:

- permission rules remain
- execution state remains
- audit history remains
- schemas remain
- memory integrity remains
- action limits remain

This reveals whether system guarantees are truly architectural or hidden inside prompt behavior.

---

## 30. Documentation Improves Architecture

Writing architecture down forces questions that can remain hidden during implementation.

For example:

- Who owns this state?
- What is canonical?
- What is derived?
- What is guaranteed?
- What is probabilistic?
- How does failure behave?
- How is this tested?
- When should this pattern not be used?

Documentation is therefore not only a presentation layer.

It can also be a design tool.

---

## 31. Public Documentation Should Not Pretend the System Is Finished

Jarvis is still under active development.

A public portfolio becomes less credible if every architectural idea is presented as already implemented and validated.

The documentation should therefore distinguish:

- planned
- experimental
- implemented
- validated
- deprecated

Being explicit about current limitations can make the project more technically credible.

---

## 32. Private Implementation and Public Architecture Should Stay Separate

The real Jarvis system may contain:

- private memory
- personal data
- prompts
- credentials
- internal traces
- configuration
- operational details

These should not be exposed simply to demonstrate technical ability.

A public architecture repository can still demonstrate:

- engineering decisions
- system design
- evaluation methodology
- failure analysis
- reusable patterns

without publishing the private implementation.

---

## 33. Reusable Patterns Require Context

A pattern should not simply say:

"Use a graph database."

A useful reusable pattern should describe:

- problem
- context
- alternatives
- decision
- guarantees
- limitations
- failure modes
- tests
- when to use
- when not to use

This prevents implementation choices from being mistaken for universal rules.

---

## 34. Not Every Project Needs Jarvis-Level Architecture

The architecture being explored here is useful because Jarvis aims to persist information, use tools and operate across time.

A simple chatbot may not need:

- persistent memory
- complex entity resolution
- execution state
- workflow recovery
- extensive audit logs

Architecture should reflect the problem being solved.

Complexity is not evidence of quality.

---

## 35. The Boundary Between LLM and Software Is a Core Design Decision

One of the most useful ways to analyze an agentic system is to ask:

**Which component owns this decision?**

If the answer is:

"the model decides"

then ask:

**What happens if the model is wrong?**

If the consequence is small, probabilistic behavior may be appropriate.

If the consequence is serious, software probably needs to enforce a boundary.

---

## 36. A Useful Risk Heuristic

For each capability, ask:

### If this fails, what happens?

If the consequence is:

Poor wording

→ model-level handling may be enough.

If the consequence is:

Wrong information

→ validation may be needed.

If the consequence is:

Corrupted state

→ deterministic state management is needed.

If the consequence is:

External side effect

→ execution controls are needed.

If the consequence is:

Irreversible damage or privacy violation

→ authorization, confirmation and strong deterministic boundaries are required.

---

## 37. Current Jarvis Design Heuristic

A compact version of the current thinking is:

> LLMs interpret.

> Retrieval selects.

> Memory preserves.

> Software validates.

> Policies authorize.

> Tools execute.

> Verification confirms.

> State records.

> Evaluations provide evidence.

Each component should have a clear responsibility.

---

## 38. Architecture Review Is Valuable

Another lesson from Jarvis is that architecture should periodically be challenged.

Existing decisions should not automatically remain correct because they already exist.

A review should ask:

- Is this component still necessary?
- Is there duplicated responsibility?
- Is there a simpler architecture?
- Is a probabilistic component enforcing something that should be deterministic?
- Is an experimental technology becoming too authoritative?
- Are we measuring whether this actually works?

Architecture should evolve through evidence rather than inertia.

---

## 39. The Goal Is Not a Perfect Agent

The goal of Jarvis is not to create a fictional perfectly autonomous assistant.

The more useful objective is to build a system that becomes progressively:

- more useful
- more reliable
- more inspectable
- more correctable
- safer to extend
- easier to evaluate

Progress can be incremental.

---

## 40. Jarvis as a Learning Laboratory

Jarvis is useful not only as a product concept.

It is also a laboratory for learning about:

- persistent AI memory
- retrieval
- tool use
- agent architecture
- deterministic guarantees
- evaluation
- system design

The project becomes more valuable when architectural decisions are documented rather than remaining implicit in code.

---

## 41. Turning Jarvis Into a Blueprint

The long-term objective is to identify which lessons are specific to Jarvis and which can generalize to other systems.

Potential reusable areas include:

- memory boundaries
- bounded context construction
- retrieval routing
- tool execution contracts
- permission boundaries
- workflow state
- evaluation patterns
- migration strategies

These patterns could eventually support different Jarvis-style systems for different users or domains.

---

## 42. Blueprint Requirement

A pattern should not become part of the reusable blueprint simply because Jarvis uses it.

Before promotion into a reusable pattern, it should ideally have:

1. a clearly defined problem
2. a documented solution
3. known alternatives
4. deterministic guarantees where relevant
5. known failure modes
6. tests
7. acceptance criteria
8. evidence of usefulness
9. defined conditions for reuse
10. defined conditions where it should not be used

This helps separate architecture from accidental implementation history.

---

## 43. What Still Needs to Be Learned

Several questions remain open.

Examples include:

- How much memory structure is enough?
- How should conflicting evidence be resolved?
- When should an LLM be involved in memory promotion?
- How should retrieval quality be measured at scale?
- What is the best boundary between semantic and procedural memory?
- How much orchestration should be deterministic?
- Which workflows benefit from autonomy?
- Which actions should always remain human-controlled?
- How should evaluation evolve as the system grows?

These unresolved questions are part of the project rather than weaknesses to hide.

---

## 44. Current Working Principles

The current architecture can be summarized by several working principles:

1. Preserve evidence.
2. Keep provenance.
3. Separate memory from context.
4. Retrieve selectively.
5. Treat corrections explicitly.
6. Keep canonical state separate from projections.
7. Use LLMs where ambiguity is useful.
8. Use software where guarantees are required.
9. Separate planning from execution.
10. Use least privilege for tools.
11. Make important actions observable.
12. Design for failure and recovery.
13. Turn failures into tests.
14. Distinguish implementation from validation.
15. Add complexity only when it solves a demonstrated problem.
16. Increase autonomy only when reliability supports it.
17. Keep public documentation honest about system maturity.

---

## 45. Current Status

These lessons represent the current understanding emerging from Jarvis.

They are expected to change.

Some ideas may later prove incorrect.

Some patterns may be simplified.

Some architectural experiments may be removed entirely.

That is intentional.

The purpose of this document is not to freeze the architecture.

It is to preserve the reasoning that allows the architecture to improve.

---

## 46. Final Principle

The most important lesson so far can be summarized as:

> **A useful AI agent is not just an LLM with more tools.**

> It is a system around the LLM that decides what the model should know, what it is allowed to do, what software must guarantee, how failures are handled and how we know whether the system actually works.
