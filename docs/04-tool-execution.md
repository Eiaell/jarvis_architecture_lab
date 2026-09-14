# Jarvis Tool Execution Architecture

> Status: Work in progress

This document describes the architectural direction of tool execution in Jarvis.

The goal is not to let the language model directly control external systems without constraints.

The goal is to create a controlled execution layer where the model can propose actions, while software enforces permissions, validates inputs, tracks state and protects irreversible operations.

---

## 1. Problem

An AI assistant becomes significantly more useful when it can do more than generate text.

Tools can allow an agent to:

- retrieve information
- read and write files
- interact with APIs
- search external systems
- update records
- execute workflows
- send messages
- trigger automation
- manipulate project state

But tool access also creates one of the largest architectural risks in an agentic system.

There is a major difference between:

**The model says something incorrect**

and:

**The model performs an incorrect external action.**

Once an action affects the outside world, hallucination becomes an operational problem rather than only a language problem.

---

## 2. Core Principle

The central rule is:

> **The LLM may propose an action. Software decides whether that action is allowed to execute.**

Conceptually:

User Request  
↓  
Reasoning  
↓  
Proposed Action  
↓  
Permission Check  
↓  
Input Validation  
↓  
Execution Policy  
↓  
Tool Adapter  
↓  
External System  
↓  
Result Validation  
↓  
State Update  
↓  
Response

The LLM is not the execution boundary.

Software is.

---

## 3. Tool Invocation vs. Tool Execution

Jarvis treats these as separate concepts.

### Tool Invocation

The model decides that a tool may be useful and produces a structured request.

### Tool Execution

The system determines whether the request is valid and safe, then performs the external action.

This distinction creates an important architectural boundary.

The model can be probabilistic.

The execution layer should be substantially more deterministic.

---

## 4. Tool Registry

Tools should not exist as arbitrary functions available directly to the model.

They should be registered explicitly.

A tool definition can conceptually contain:

- tool name
- description
- accepted parameters
- input schema
- permission level
- side-effect classification
- retry behavior
- timeout
- validation rules
- audit requirements

This allows Jarvis to reason about tools as controlled capabilities rather than unrestricted code execution.

---

## 5. Structured Inputs

Tool calls should use structured parameters.

Instead of allowing the model to generate arbitrary execution commands, the architecture should favor schemas.

Example:

    {
      "tool": "create_task",
      "arguments": {
        "title": "Review supplier proposal",
        "project_id": "project_123"
      }
    }

The system can validate this request before execution.

Possible validation includes:

- required fields
- accepted types
- permitted values
- identifier validity
- size limits
- formatting constraints

This reduces the amount of trust placed on free-form model output.

---

## 6. Permission Boundaries

Different tools have different levels of risk.

A useful conceptual classification is:

### Read-only

Examples:

- search
- fetch information
- inspect files
- retrieve project state

Usually lower risk.

### Reversible write

Examples:

- create a draft
- create a temporary record
- update recoverable state

Moderate risk.

### External side effect

Examples:

- send a message
- publish content
- modify an external system
- execute a transaction
- trigger a workflow

Higher risk.

### Irreversible or sensitive action

Examples:

- delete data
- send money
- expose private information
- perform destructive operations
- change access permissions

Highest risk.

The execution policy should become stricter as the potential impact increases.

---

## 7. Confirmation Boundaries

Some actions should require explicit confirmation before execution.

Conceptually:

Low-risk read  
→ execute automatically

Low-risk reversible action  
→ execute according to policy

External side effect  
→ potentially require confirmation

Sensitive or irreversible action  
→ explicit confirmation required

The exact policy depends on the deployment context.

The architectural principle is:

**confidence from the model should never replace authorization from the system or user.**

---

## 8. Deterministic Validation

Before a tool executes, deterministic checks can verify:

- schema validity
- permissions
- current state
- required confirmation
- target identifier
- allowed operation
- policy constraints
- rate limits
- duplicate execution risk

These checks should not depend on the LLM deciding whether its own request is safe.

The model can explain.

Software must enforce.

---

## 9. Idempotency

One of the most important execution problems is accidental duplication.

Imagine:

Jarvis requests:

"Create invoice."

The external service creates it successfully.

The response times out.

Jarvis retries.

A second invoice is created.

From the model's perspective, the first call appeared to fail.

From the real-world system, it succeeded.

This is why tool execution should support idempotency where possible.

Conceptually:

Action Intent  
↓  
Stable Action ID  
↓  
Execution  
↓  
Recorded Result

If the same action is retried:

Stable Action ID  
↓  
Existing Result Found  
↓  
Do Not Execute Again

This is especially important for actions involving:

- payments
- messages
- external records
- workflow triggers
- resource creation

---

## 10. Execution State

Jarvis should maintain execution state separately from conversational memory.

Possible states include:

- proposed
- awaiting_confirmation
- authorized
- executing
- succeeded
- failed
- retryable
- cancelled
- rolled_back

This makes tool execution observable.

The system should not rely on the LLM remembering whether something already happened.

---

## 11. Tool Adapter Layer

External systems change.

APIs change.

Authentication changes.

Providers change.

For this reason, the reasoning system should ideally not depend directly on provider-specific implementation details.

A tool adapter can create a stable internal interface.

Conceptually:

Jarvis  
↓  
Internal Tool Contract  
↓  
Adapter  
↓  
External API

If the external provider changes, the adapter can change while the rest of the architecture remains stable.

This improves maintainability and replaceability.

---

## 12. Tool Result Normalization

External tools often return inconsistent structures.

A useful execution layer can normalize results before sending them back into the reasoning system.

Example:

    {
      "status": "success",
      "tool": "search_documents",
      "data": [],
      "error": null,
      "execution_id": "..."
    }

The exact schema can vary.

The architectural objective is to provide the reasoning layer with predictable tool results.

---

## 13. Error Handling

Tools fail for many reasons unrelated to model reasoning.

Examples include:

- timeout
- authentication failure
- invalid credentials
- API rate limit
- provider outage
- malformed request
- missing resource
- conflicting state
- permission denied

The execution layer should distinguish these failure types.

The LLM should not have to infer every operational failure from arbitrary error text.

Structured errors make recovery more reliable.

---

## 14. Retry Policy

Not every failure should be retried.

A useful classification is:

### Retryable

Examples:

- timeout
- temporary network failure
- temporary provider error

### Conditionally retryable

Examples:

- rate limit
- temporary resource lock

### Not retryable

Examples:

- invalid identifier
- permission denied
- schema violation
- user rejected confirmation

Retries should be controlled by software policy.

An LLM repeatedly deciding to "try again" can create dangerous loops.

---

## 15. Execution Loops

Agentic systems can accidentally create loops such as:

Tool fails  
↓  
LLM retries  
↓  
Tool fails  
↓  
LLM retries  
↓  
...

Jarvis should therefore use deterministic limits such as:

- maximum retries
- maximum tool calls
- timeout budget
- workflow step limit
- execution cost limit

When the limit is reached, the system should stop and expose the failure rather than continue indefinitely.

---

## 16. Verification

A successful API response does not always mean the intended real-world outcome occurred.

For important actions, execution may need verification.

Conceptually:

Execute Action  
↓  
Receive Success  
↓  
Verify Result  
↓  
Mark Completed

Examples:

Create record  
→ fetch record and verify existence

Update configuration  
→ read current configuration

Send external request  
→ verify provider acknowledgement

The required level of verification depends on the action risk.

---

## 17. Side Effects

Tools should explicitly declare whether they create side effects.

This allows the system to treat:

`search_documents`

differently from:

`delete_project`

A useful tool contract could include:

- read_only
- reversible
- external_side_effect
- irreversible

This classification can influence:

- confirmation
- logging
- retries
- verification
- permission requirements

---

## 18. Action Planning

For multi-step workflows, Jarvis may need to plan several tool calls.

Example:

Goal:

Prepare and send a project update.

Possible plan:

Retrieve project state  
↓  
Retrieve recent decisions  
↓  
Generate draft  
↓  
Validate recipients  
↓  
Request confirmation  
↓  
Send message  
↓  
Verify delivery

The plan can involve probabilistic reasoning.

Execution of each step should still pass through deterministic boundaries.

---

## 19. Separation Between Planning and Acting

A major architectural principle is:

> **Planning is not execution.**

Jarvis should be able to reason about a possible action without automatically performing it.

This distinction makes it possible to:

- inspect plans
- validate actions
- request confirmation
- reject unsafe operations
- simulate workflows
- evaluate agent decisions

It also makes the system easier to debug.

---

## 20. Dry Run Mode

An important capability for agentic systems is the ability to simulate execution.

Dry run mode can show:

- what tool would be called
- with which parameters
- what permissions would be required
- what external system would be affected

without actually creating the side effect.

This is useful for:

- development
- testing
- debugging
- high-risk workflows
- new tool integrations

---

## 21. Audit Trail

Important tool actions should produce an audit trail.

Useful fields may include:

- action ID
- timestamp
- initiating request
- selected tool
- input parameters
- authorization state
- execution result
- verification result
- error details
- retry count

The audit trail should allow a developer to answer:

**What happened?**

**Why did it happen?**

**What requested it?**

**Did it execute more than once?**

---

## 22. Sensitive Information

Tool execution frequently involves secrets.

Examples:

- API keys
- access tokens
- passwords
- private identifiers

These should not be inserted into the model context unless absolutely necessary.

The preferred architecture is:

LLM  
↓  
Tool Request  
↓  
Secure Execution Layer  
↓  
Credentials Injected Outside Model Context  
↓  
External Service

The model should not need direct access to most secrets.

---

## 23. Tool Output and Memory

Tool results should not automatically become permanent trusted memory.

A tool result may be:

- temporary
- incomplete
- stale
- incorrect
- relevant only to one task

Therefore:

Tool Result  
↓  
Current Context

does not automatically mean:

Tool Result  
↓  
Persistent Memory

If information should become memory, it should pass through the appropriate memory validation process.

---

## 24. Permissions and Least Privilege

Tools should receive only the permissions required for their role.

For example:

A document search tool should not automatically receive document deletion permissions.

A calendar reader should not automatically receive permission to modify events.

This follows the security principle of:

**least privilege**

Reducing tool permissions limits the impact of incorrect reasoning or compromised components.

---

## 25. Tool Failure Modes

Important tool execution failure modes include:

### Hallucinated tool parameters

The model invents an identifier or value.

### Duplicate execution

The same action executes more than once.

### Unauthorized execution

An action occurs without appropriate permission.

### Incorrect target

The correct action is executed against the wrong resource.

### Infinite retry loop

Repeated failures cause uncontrolled retries.

### Partial execution

Some workflow steps succeed while others fail.

### False success

The tool reports success but the intended result did not actually occur.

### Secret exposure

Credentials or sensitive information enter the model context or logs.

### State mismatch

Jarvis believes an action has one state while the external system has another.

### Provider drift

An external API changes and silently breaks assumptions in the adapter.

---

## 26. Partial Failure

Multi-step workflows introduce an important problem.

Suppose a workflow performs:

Step 1  
Create project

Step 2  
Create tasks

Step 3  
Send notification

If Step 3 fails, the workflow is not simply:

"failed."

Steps 1 and 2 already happened.

Jarvis therefore needs to reason about partial completion.

Possible strategies include:

- retry remaining steps
- resume from checkpoint
- compensate previous actions
- request user intervention
- preserve partial state

This is why execution state should be explicit.

---

## 27. Compensation and Rollback

Not every external system supports true rollback.

When reversal is possible, a workflow can define compensating actions.

Example:

Create temporary resource  
↓  
Later step fails  
↓  
Delete temporary resource

However, compensation should not be assumed.

Some actions cannot be undone.

Examples include:

- sent email
- published message
- external notification
- completed financial transaction

For irreversible actions, stronger validation should occur before execution.

---

## 28. Checkpoints

Long workflows can benefit from checkpoints.

A checkpoint records enough state to resume without repeating completed actions.

Conceptually:

Step 1 completed  
↓  
Checkpoint  
↓  
Step 2 completed  
↓  
Checkpoint  
↓  
Failure  
↓  
Resume from latest valid checkpoint

This can make workflows more robust and reduce duplication.

---

## 29. Human-in-the-Loop Execution

Human approval is not necessarily a failure of automation.

For high-impact operations, it can be an intentional architectural component.

Possible patterns include:

### Approval before execution

Jarvis prepares the action.

The user authorizes it.

The system executes.

### Approval after planning

Jarvis prepares a multi-step plan.

The user approves the plan.

Execution begins.

### Escalation on uncertainty

Low-risk actions proceed automatically.

Unexpected or ambiguous cases are escalated.

The goal is not maximum autonomy.

The goal is useful autonomy with appropriate control.

---

## 30. Tool Selection

Selecting a tool and executing a tool are separate decisions.

Jarvis may have several tools capable of solving a similar problem.

Tool selection can consider:

- task requirements
- permissions
- reliability
- latency
- cost
- provider availability
- side-effect risk
- data sensitivity

The selected tool should still pass through the same execution boundary.

---

## 31. Capability-Based Design

A useful way to think about tools is as capabilities.

Instead of giving the model unrestricted access to an application, the system exposes specific allowed operations.

For example:

Instead of:

`Gmail access`

the system may expose:

- search_email
- read_email
- create_draft
- send_draft

Each capability can have its own policy.

This creates finer-grained control.

---

## 32. Tool Chaining

Some tasks require outputs from one tool to become inputs to another.

Example:

Search document  
↓  
Extract identifier  
↓  
Query API  
↓  
Generate report

Tool chaining creates risks such as:

- propagated errors
- incorrect assumptions
- malformed intermediate state
- privilege escalation
- hidden dependencies

Intermediate results should therefore be validated before being passed to the next tool.

---

## 33. Observability

Tool execution should be observable.

Useful metrics may include:

- tool call count
- success rate
- failure rate
- retry count
- average latency
- confirmation frequency
- duplicate prevention events
- verification failures
- provider errors

Observability makes it possible to distinguish:

"the agent seems unreliable"

from:

"this specific tool fails 18% of the time because of rate limits."

That distinction is essential for engineering improvement.

---

## 34. Evaluation

Tool execution should be evaluated separately from language quality.

Useful evaluation questions include:

- Did Jarvis select an appropriate tool?
- Were the arguments valid?
- Was the operation authorized?
- Was the action executed exactly once?
- Was the result verified?
- Did the workflow recover correctly after failure?
- Were sensitive actions protected?
- Did execution remain within predefined limits?

These behaviors should increasingly be tested automatically.

---

## 35. Tool Execution Test Cases

Example tests may include:

### Invalid parameter

Model proposes a malformed tool request.

Expected:

Execution is rejected before reaching the external provider.

### Unauthorized operation

Model requests a capability it does not have.

Expected:

Execution is blocked.

### Duplicate request

The same action is submitted twice with the same action ID.

Expected:

External side effect occurs once.

### Provider timeout after success

The provider performs the action but the network response fails.

Expected:

Retry does not create a duplicate.

### Confirmation required

A sensitive action is requested.

Expected:

Execution pauses until explicit authorization exists.

### Maximum retry limit

A provider repeatedly fails.

Expected:

Execution stops after the configured retry budget.

### Partial workflow failure

Step 2 of a multi-step workflow fails after Step 1 succeeded.

Expected:

State records partial completion and prevents unsafe repetition.

---

## 36. Deterministic Guarantees

Where possible, the execution layer should provide guarantees such as:

- no execution without valid schema
- no execution without required permissions
- no sensitive execution without required confirmation
- no uncontrolled retry loops
- no silent duplicate execution where idempotency is available
- explicit execution state
- traceable action history
- bounded tool-call budgets

These are software responsibilities.

They should not depend on prompt wording.

---

## 37. Probabilistic Responsibilities

The model remains useful for tasks such as:

- understanding user intent
- choosing between appropriate capabilities
- constructing a plan
- resolving ambiguity
- interpreting tool results
- deciding what information is missing
- generating human-readable explanations

These tasks benefit from probabilistic reasoning.

The goal is not to remove the LLM from the system.

The goal is to place it where uncertainty is useful rather than where guarantees are required.

---

## 38. Current Architectural Rule

The working rule can be summarized as:

> **Let the model reason.**

> **Let software authorize.**

> **Let schemas constrain.**

> **Let state track what happened.**

> **Let verification confirm the result.**

> **Never confuse a proposed action with an executed action.**

---

## 39. Reusable Pattern

The broader tool execution pattern being explored is:

User Intent  
↓  
LLM Reasoning  
↓  
Structured Action Proposal  
↓  
Policy Check  
↓  
Permission Check  
↓  
Schema Validation  
↓  
Confirmation Boundary  
↓  
Idempotency Check  
↓  
Tool Adapter  
↓  
External Execution  
↓  
Verification  
↓  
Execution State  
↓  
Audit Trail  
↓  
Result to Reasoning Layer

This pattern separates probabilistic reasoning from operational authority.

---

## 40. When This Pattern Is Useful

This architecture becomes particularly important when:

- an agent modifies external systems
- real-world side effects exist
- workflows contain several steps
- actions may be retried
- privacy matters
- different tools require different permissions
- failures must be recoverable
- users need to understand what happened

---

## 41. When It May Be Unnecessary

A sophisticated execution layer may be unnecessary when:

- the system only generates text
- tools are strictly read-only
- no persistent state exists
- all actions are easily reversible
- the environment is purely experimental

Execution controls should scale with actual operational risk.

---

## 42. Current Status

Tool execution in Jarvis remains under active development.

Some of the concepts described here represent validated engineering principles, while others describe the intended architecture and areas still being evaluated.

The public repository intentionally documents architectural reasoning without exposing:

- credentials
- private tool configurations
- personal information
- production secrets
- internal authentication details
- sensitive prompts
- private execution logs

As Jarvis evolves, this document will be updated to distinguish more precisely between:

- implemented
- validated
- experimental
- planned
