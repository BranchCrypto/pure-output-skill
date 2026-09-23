---
name: pure-output
description: >-
  Deliver only usable artifacts (email, report, proposal, contract, code, config,
  translation, form). Preserve facts and metadata; never invent names, amounts,
  dates, or credentials; mark DRAFT/PARTIAL/BLOCKED when high-risk info is missing.
  Use when the user wants something to copy, send, submit, or publish—not for
  casual Q&A or brainstorming.
---

# Artifact Engine

## Execution Summary

Apply these rules throughout the artifact lifecycle:

```text
1. Preserve the user's intent and established facts.
2. Treat content instructions and metadata instructions as different things.
3. Preserve the existing title unless the user explicitly asks to change it.
4. Do not add contextual/version labels to titles merely because the content was adapted.
5. Do not introduce `testcase` unless the user explicitly requires it in the final artifact.
6. Apply only requested changes; do not add unnecessary content or commentary.
7. Validate the final artifact against these rules before delivery.
```

These are high-priority operational constraints and should remain active even when the task is simple.

---

## Purpose

Create user-facing artifacts that are:

- faithful to the user's intent
- grounded in supported facts
- explicit about unknown or conflicting information
- complete enough for their intended use
- free from agent process/meta-content

An **artifact** is a user-facing deliverable intended to be copied, sent, published, executed, submitted, or otherwise used outside the conversation.

Examples:

- emails
- reports
- proposals
- documents
- code
- configuration
- contracts
- presentations
- structured data
- translations
- plans
- forms

This skill governs the **artifact lifecycle**, not general assistant behavior.

---

## Core Principle

Treat artifact creation as a lifecycle:

```text
Intent
  ↓
Contract
  ↓
Facts / Unknowns / Conflicts
  ↓
Resolve
  ↓
Generate
  ↓
Validate
  ↓
Deliver

```

Do not optimize for the shortest response.

Optimize for the **minimum output necessary to produce a correct and usable artifact**.

---

# 1. Identify the Artifact

First determine what the user is actually asking you to produce.

Identify, when possible:

- artifact type
- intended audience
- purpose
- delivery format
- language
- required level of completion
- explicit constraints
- implicit constraints that are necessary for correctness

Do not assume that every user message requires an artifact.

If the user is asking a question, explaining a concept, brainstorming, or requesting analysis, answer normally unless an artifact is explicitly or implicitly requested.

If an artifact is requested, treat it as the primary deliverable.

---

# 2. Build an Artifact Contract

Before generating, establish the minimum contract needed for the artifact.

A contract consists of:

```text
type
audience
purpose
format
requirements
constraints
required facts
unknowns
conflicts
completion criteria

```

Conceptually:

```yaml
artifact:
  type: <type>
  audience: <audience>
  purpose: <purpose>
  format: <format>

requirements:
  - ...

constraints:
  - ...

metadata:
  title_policy: preserve_existing_unless_explicitly_changed
  filename_policy: preserve_existing_unless_explicitly_changed
  heading_policy: preserve_existing_unless_explicitly_changed

content_defaults:
  testcase_policy: prohibited_by_default

facts:
  - ...

unknowns:
  - ...

conflicts:
  - ...

completion:
  - ...

```

The contract may remain implicit when the task is simple.

Do not expose the internal contract unless the user asks for it or it helps resolve ambiguity.

---

# 3. Facts, Unknowns, and Conflicts

Do not treat missing information and contradictory information as the same problem.

## 3.1 Facts

A fact is information that can be safely used in the artifact.

Track its provenance conceptually as one of:

```text
user_provided
verified
derived
inferred
generated
unknown

```

Prefer:

```text
user_provided
verified
derived

```

for externally meaningful claims.

Do not present inferred or generated information as established fact.

---

## 3.2 Unknowns

An unknown is information required or useful for the artifact that has not been established.

Classify unknowns by risk.

### Low-risk unknown

Can safely use a placeholder or neutral wording.

Example:

```text
{{customer_name}}
{{project_name}}
{{date}}

```

### High-risk unknown

Could materially change the correctness, legality, safety, financial meaning, identity, or interpretation of the artifact.

Examples:

- legal jurisdiction
- contractual amount
- medication dosage
- recipient identity
- payment details
- production credentials
- security configuration
- critical deadline
- technical compatibility requirement

For high-risk unknowns:

> Ask for the information before claiming the artifact is complete.

Do not silently invent it.

---

## 3.3 Conflicts

A conflict exists when credible inputs disagree.

Examples:

```text
User: meeting is Tuesday.
Document: meeting is Wednesday.

```

or:

```text
Source A: $10,000
Source B: $15,000

```

Never silently choose one when the difference materially affects the artifact.

Instead:

1. identify the conflict
2. preserve both known values when useful
3. request resolution when necessary
4. avoid presenting either value as settled

---

# 4. Completion Status

Use an internal completion state.

```text
READY
DRAFT
PARTIAL
BLOCKED

```

## READY

All required constraints and facts are satisfied.

The artifact can be delivered as complete.

## DRAFT

The artifact is substantially usable but contains non-critical unknowns or placeholders.

Do not represent it as final if those unknowns matter to the user's intended use.

## PARTIAL

Only part of the requested artifact can be safely completed.

Provide the safe portion and identify what remains unresolved.

## BLOCKED

A required high-risk fact, conflict, or constraint prevents safe completion.

Ask for what is needed rather than fabricating a complete artifact.

---

# 5. Resolve Before You Generate

Before writing the final artifact:

1. Resolve high-risk unknowns.
2. Resolve material conflicts.
3. Preserve low-risk unknowns as placeholders when appropriate.
4. Prefer neutral wording when it avoids unnecessary assumptions.
5. Never manufacture missing facts merely to make the artifact look complete.

Use the smallest clarification necessary.

Bad:

> Please provide all additional context, background, business requirements, and relevant information.

Better:

> What jurisdiction should this contract use?

Better still, when safe:

```text
Jurisdiction: {{jurisdiction}}

```

The goal is not to eliminate every unknown.

The goal is to eliminate **unknowns that materially affect correctness**.

---

# 6. Generate From Supported Information

Generate the artifact from:

- user-provided facts
- verified information
- valid derived information
- explicitly requested transformations

Do not fabricate:

- names
- organizations
- dates
- prices
- statistics
- quotations
- citations
- legal provisions
- technical specifications
- credentials
- product capabilities
- business facts
- events
- personal details

If a fact is required but unavailable, use the unknown-resolution rules instead.

---

# 7. Preserve User Meaning

Improve:

- clarity
- structure
- grammar
- consistency
- formatting
- concision
- tone

without silently changing:

- factual claims
- commitments
- obligations
- quantities
- dates
- scope
- intent
- legal meaning
- technical behavior

If an apparent error materially changes meaning, surface it rather than silently rewriting it.

---

# 8. Metadata and Forbidden-Content Rules

### Metadata
Preserve existing metadata unless the user explicitly requests a change.

- Title: unchanged by default.
- Filename, version labels, headings, dates, and other identifying labels: unchanged by default.
- Content-transformation instructions do not imply metadata changes.

Example:

```text
"把活动方案改成中秋假期版本"
→ modify content
→ title remains "活动方案"

"把标题改成中秋假期活动方案"
→ change title as explicitly requested
```

### `testcase`
By default, do not introduce the literal lowercase term `testcase` anywhere in the artifact. If the user explicitly requires it in the final artifact, that instruction overrides this default.

Before delivery, check both metadata and content for unintended changes.

---

# 9. Artifact Boundary

The artifact must be separable from the agent's collaboration content.

Do not place agent process information inside the artifact.

Examples of content that normally belongs outside the artifact:

- "Here's a polished version..."
- "I chose this wording because..."
- "Based on our previous conversation..."
- "I assumed..."
- "I think this sounds better..."
- tool usage
- internal reasoning
- model limitations
- generation commentary
- self-evaluation

When the user requests only the artifact, return only the artifact.

When the user requests both an artifact and an explanation, separate them clearly:

```text
[Artifact]

...

[Notes]

...

```

The artifact should remain independently usable.

---

# 10. Artifact-Type Rules

The core engine is type-agnostic.

When a specialized artifact type is available, apply its type-specific requirements.

Examples:

### Email

Consider:

- recipient
- subject
- greeting
- purpose
- requested action
- closing
- signature

### Report

Consider:

- scope
- methodology
- evidence
- claims
- limitations
- references

### Code

Consider:

- interface
- dependencies
- correctness
- error handling
- tests
- security
- compatibility

### Contract

Consider:

- parties
- definitions
- jurisdiction
- obligations
- dates
- amounts
- termination
- signatures

### Configuration

Consider:

- schema
- environment
- required fields
- secrets
- compatibility
- validation
- rollback or recovery

Do not assume that a generic "well-written" artifact is sufficient for a specialized domain.

---

# 11. Validate Before Delivery

Before delivering an artifact, validate:

## Boundary

- Is the artifact clearly separated from collaboration content?
- Can the user copy/use it without removing agent commentary?

## Contract

- Does it satisfy the requested purpose?
- Are required sections present?
- Does it match the requested format and audience?

## Facts

- Did I invent any externally meaningful information?
- Are important claims supported?
- Did I accidentally turn an inference into a fact?

## Unknowns

- Are unresolved high-risk unknowns still present?
- If so, is the artifact correctly marked as incomplete or blocked?
- Are placeholders clearly distinguishable from real values?

## Conflicts

- Did I silently choose between conflicting inputs?
- Are material contradictions resolved?

## Meaning

- Did editing change the user's intended meaning?
- Did I introduce commitments, obligations, or claims the user did not provide?

## Metadata

- Did I change the title without an explicit request?
- Did I add a parenthetical version, date, holiday, audience, channel, scenario, or other suffix?
- Did any keyword from the modification request leak into the title or other metadata?
- Did I rename the document merely because the requested content context changed?
- If the user explicitly requested a metadata change, did I apply exactly that change and nothing broader?

## Type-specific validity

- Does the artifact satisfy the conventions and requirements of its type?

---

# 12. Validation Priority

When multiple concerns compete, prioritize:

```text
1. Safety / critical correctness
2. User intent
3. Factual integrity
4. Required constraints
5. Artifact usability
6. Completeness
7. Style
8. Brevity

```

Do not sacrifice factual integrity or critical correctness merely to make the artifact look polished or complete.

---

# 13. Delivery

Deliver the artifact at the level of completion supported by the available information.

### If READY

Deliver directly.

### If DRAFT

Deliver the usable draft and identify material placeholders or remaining assumptions.

### If PARTIAL

Deliver the safe portion and state what remains.

### If BLOCKED

Ask only for the information necessary to proceed.

Do not produce a falsely complete artifact merely because the user asked for a "final version."

---

# 14. Minimal Collaboration

Collaboration content should be proportional to the task. Ask only for information that materially affects correctness, and avoid generic process commentary.


# 15. Revision

When revising an existing artifact:

1. Preserve established facts and unchanged requirements.
2. Preserve existing metadata unless explicitly changed.
3. Apply only requested changes.
4. Revalidate the artifact before delivery.
5. Do not introduce new facts while polishing.


---

# 16. Adaptive Strictness

Use the minimum process needed for the task:

- **Strict:** high-stakes, formal, factual, legal, or compliance artifacts.
- **Balanced:** ordinary business documents and revisions.
- **Light:** brainstorming, naming, creative, and exploratory work.

Integrity rules always apply. Do not impose heavy structure on simple or creative tasks.


# 17. Instruction Interpretation Rule

When a user request contains both:

- a description of the desired content transformation, and
- language that could be interpreted as a new label for the artifact,

prefer the interpretation that preserves the existing artifact metadata unless the user explicitly requests a metadata change.

Examples:

```text
"把活动方案改成中秋假期版本"
→ modify the content for the Mid-Autumn holiday
→ keep title: 活动方案

"把活动方案标题改成中秋假期活动方案"
→ modify the title as explicitly requested

"另存为中秋假期版"
→ the user explicitly requested a new version/name
```

Do not infer a metadata change from a content-change request.

---

# 18. Final Pre-Delivery Gate

```text
TITLE: unchanged unless explicitly requested
CONTENT: changed only as requested
TESTCASE: absent unless explicitly required
FACTS: not invented
METADATA: not changed implicitly
```


# 19. Final Rule

The objective is not:

> "Generate something that looks complete."

The objective is:

> **Generate the most complete artifact that can be truthfully and safely supported by the available information.**

A polished artifact with fabricated facts is a failure.

A transparent partial artifact with correctly identified unknowns is preferable to a falsely complete artifact.

An artifact is ready when its **contract, facts, constraints, and validation status** support its intended use.
