---
name: adversarial-test-review
description: Review a PR, issue, or proposed change by generating adversarial tests from public API contracts in a fresh context, then running them against the change. Use for independent behavioral review of libraries, services, and CLIs, including review that must retain failing tests as possible defects.
---

# Adversarial test review

Find observable violations of intended behavior without deriving test expectations
from the implementation under review. Produce reproducible tests and an evidence
report; a passing suite does not establish correctness.

## Context boundary

Act as the coordinator. Prepare the public contract and a project-specific harness,
then delegate test design and implementation to a fresh agent with no inherited
conversation. The author receives only the contract packet, harness instructions,
and the author handoff in [references/author-handoff.md](references/author-handoff.md).
Do not send implementation details, suspected implementation bugs, proposed fixes,
existing test assertions, or execution results before expectations are recorded.

An instruction to ignore previously seen implementation is insufficient. If fresh
agent delegation is unavailable, prepare the packet and harness for a separate
session and report that independent authoring remains pending. Do not silently
substitute the coordinator for the independent author.

Where supported, give the author a workspace containing only approved artifacts
and consumer test files, with no source checkout, repository history, or source
browsing access. A workspace directory alone does not restrict filesystem access.
If access cannot be restricted, use an explicit artifact allowlist and disclose
that separation was instruction-based. If the author reads forbidden material,
retain the artifacts for audit and restart authoring in a fresh context.

## Prepare the review

Read applicable project guidance. Identify the issue/PR, exact target revision,
base revision when useful, intended behavior, and affected public interfaces.
Use isolated checkouts or temporary artifacts without disturbing user changes.
Do not modify product code as part of this review.

Build a focused contract packet from:

- Relevant issue/PR requirements, acceptance criteria, and public specifications.
- Public declarations, types, documented preconditions, errors, and API examples.
- Applicable version, configuration, and consumer build constraints.

Include the exact source and location for each behavioral claim. Distinguish
explicit requirements, justified deductions, and unresolved assumptions. Preserve
conflicting requirements as questions; observed behavior cannot resolve them.
Descriptions and documentation can be incomplete or mistaken.

Exclude implementation bodies, diffs containing implementation, private symbols,
source-linked documentation, developer regression tests, and assertions copied
from the existing suite. If extraction requires reading source, the coordinator
may do so, but must export declarations and contract text only. Do not summarize
control flow or leak conclusions into the packet. Include new public declarations
from the target revision when the base does not expose the changed API.

Choose an ad hoc harness for the actual public interface. There is no fixed
implementation or test language. For libraries, a native consumer project can
preserve types and API ergonomics; for services and CLIs, requests or subprocess
invocations may suffice. Use [references/harness.md](references/harness.md) when
building and documenting the harness.

Validate build, startup, invocation, and reset mechanics separately from the
behavior under review. Keep runtime outputs from these checks out of the author's
initial context. Never install observed output as an expected value.

## Author tests independently

Delegate with no inherited history and supply explicit paths to the approved
packet, harness instructions, and author handoff. Do not simply ask the agent to
review the checkout. The author must return a test plan and executable consumer
tests before receiving behavioral execution feedback.

For each test, record its requirement source, input or operation sequence,
expected observable outcome, and reasoning. Ambiguous expectations belong in
questions or explicitly exploratory probes, not confirmed defect assertions.

Prioritize plausible ways to violate the relevant contract: boundaries,
interactions between options, state transitions, failure effects on state, and
documented algebraic properties. Exercise valid inputs and specified error cases.
Do not treat behavior outside documented preconditions as a defect.

Property-based, metamorphic, and differential tests are useful when their
relationships or reference behavior have an independent justification. Do not
assume every serializer round-trips exactly, every operation is idempotent, or
another implementation is authoritative. Expected results must not be computed
by calling the same behavior being checked.

## Execute without changing the oracle to fit the result

Save the initial plan and tests as a versioned artifact or snapshot before the
first behavioral run. Record target revision, commands, configuration, seeds, and
outputs. The coordinator executes tests so compiler diagnostics, stack traces,
coverage, and source paths do not enter the author's initial context.

Correct harness, compilation, and API-usage errors when needed. Send the author
only the minimal public-interface diagnostic needed for repair, without source
snippets or implementation reasoning. Preserve each revision. If an assertion
changes, record the contract evidence that justifies the change. A test failure
alone never justifies weakening an assertion, accepting the observed value,
skipping the test, or narrowing generated inputs to make it pass.

Run against the base as well as the target when useful and feasible. Interpret
results against the contract:

| Base | Target | Interpretation to investigate |
| --- | --- | --- |
| Pass | Fail | Regression candidate |
| Fail | Pass | Evidence that this case is fixed |
| Fail | Fail | Existing defect, incomplete fix, or invalid expectation |
| Pass | Pass | Checked behavior; no defect exposed by this test |

A missing API on the base is not a behavioral failure. Intentional contract
changes need version-specific expectations. Neither the base nor the target is
an oracle merely because it runs successfully.

Reproduce and minimize relevant failures while preserving their contract and
preconditions. For nondeterministic cases, retain seeds or operation traces and
report variability. Use repetition when it answers a real uncertainty.

Only after expectations are saved and execution evidence is collected may the
coordinator inspect internals to explain failures. Keep this diagnosis separate
from independent authoring. Further tests influenced by that diagnosis must be
labeled accordingly, or authored in another fresh context from public evidence.

## Report the review

Provide executable tests and a concise report containing:

- Scope, target/base revisions, commands, and artifact locations.
- Author context sources, how access was restricted, and any contamination.
- For each finding: the requirement source, reproducer, expected and actual
  behavior, repeatability, and base result when available.
- Classification: reproducible contract violation, ambiguous requirement,
  harness/environment failure, exploratory observation, or passing check.
- Changes made after the first run, including reasons for expectation revisions.
- Remaining coverage gaps and unresolved questions.

A failing generated test is a possible code fault, not automatically a valid
finding. A confirmed contract violation needs a justified expectation and valid
reproduction. Retain failures and unresolved candidates as review artifacts;
do not require tests to pass before presenting them. Do not auto-merge generated
tests, fix product code, or post external review comments unless requested.

## Rationale

The separation protects against both implementation-derived expectations and
selection that discards fault-revealing tests because they fail. The motivation
is [On the Risks of using LLM-Generated Tests for Regression Testing](https://arxiv.org/abs/2610.11835v1).
That study motivates this workflow; it does not validate this skill's effectiveness.
