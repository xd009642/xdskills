---
name: testgen-llm
description: Extend existing unit tests with measured coverage gains using the TestGen-LLM generate-and-filter approach. Use for targeted regression test improvement or coverage gaps in an existing suite, rather than independent behavioral review or generating a suite from scratch.
---

# TestGen-LLM test improvement

Produce a small, reviewable test patch with executable evidence of improvement.
Use the project's language, test framework, and coverage tooling.

## Basis and limits

Adapted from Nadia Alshahwan et al., [Automated Unit Test Improvement using Large
Language Models at Meta](https://arxiv.org/html/2402.09171v1), arXiv:2402.09171v1,
14 February 2024.

The paper extends human-written tests, filtering candidates by build success,
passing all five executions, and increased line coverage. Its later workflow
measures each test's contribution against the relevant build target's tests,
including previously accepted candidates. Different generation strategies can
contribute complementary tests. Developer feedback rejected assertion-free,
trivial, or unfocused cases. Line coverage is an improvement proxy; mutation
coverage is proposed as future work.

The operational choices below adapt that method to a coding agent. Passing tests
do not prove correct behavior, and five successful runs do not prove absence of
flakiness. This workflow is for regression test improvement, not an independent
assessment of correctness.

## Establish the target and baseline

Read project instructions and inspect the requested component, existing tests,
and relevant contracts. Record the revision or working-tree state, build/test
commands, coverage command, configuration, and measurement scope. Preserve user
changes and existing tests. Keep product-code fixes outside this test-only task
unless already authorized.

Run the existing tests and collect fresh baseline coverage before adding
candidates. Measure the containing test target or relevant suite, not just one
test file. Use the same source revision, instrumentation, configuration, and
scope for all comparisons. Clear or separate coverage output between runs so
stale execution data cannot create apparent gains.

If the baseline fails, distinguish a test failure from an environment problem
and report the blocker. Do not attribute pre-existing failures to generated
tests. If coverage cannot be collected, drafts may still be useful, but describe
them as unverified and identify the missing check. Do not claim measured gains
from source inspection.

## Generate focused candidates

Use uncovered behavior to select plausible, contract-valid cases. For each
candidate, identify the input or operation sequence, expected observable result,
and justification for that expectation. Consult implementation to understand
reachability; do not mechanically copy observed output into assertions.

Reuse suitable fixtures and assertion conventions without copying deprecated
practices. Prefer unit tests where practical. Exercise realistic boundaries,
state changes, and specified error paths. Avoid invalid API use, tautological
assertions, placeholder expectations, and testing only implementation details.

Start with a small batch. If candidates are redundant, change the targeted
behavior or generation angle instead of repeatedly renaming equivalent tests.
Further batches should respond to specific remaining gaps and fit the task's
time budget. Multiple models or delegated agents are optional and require
availability and authorization; they are not prerequisites for this skill.

## Validate candidates

Apply these gates in order, evaluating candidates separately:

1. Build using the project's normal test configuration.
2. Run each candidate five times; require every execution to pass.
3. Measure additional production lines covered over the baseline plus accepted
   candidates. Retain only candidates with a positive marginal contribution.

For dynamic languages, use the framework's normal collection and loading checks
as the build gate. Repeated runs should actually execute the test rather than
reuse a cached result. Use fresh fixture state according to the framework's
normal isolation rules.

A compiler error may warrant repairing a candidate's API usage. An assertion
failure requires investigating its expectation against the contract. Keep a
credible fault-revealing reproducer as a separate finding; do not weaken it or
change product behavior merely to qualify it as a passing regression test.
Distinguish incorrect expectations, environment failures, and intermittent
results. Remove rejected generated additions from the proposed patch while
preserving useful failure evidence in an appropriate temporary artifact.

Compare covered source locations, not only percentages or total hit counts.
Exclude test and generated-code execution from the improvement claim. Attribute
gains outside the requested component explicitly and assess their relevance.
Two candidates can cover identical new lines individually; after accepting one,
re-measure the other's marginal gain. Check that baseline-covered locations
remain covered and investigate any loss rather than relying on a positive net
count.

Branch coverage or mutation results may supply additional evidence when already
available or requested. If the user chooses another acceptance metric, label
that deviation and measure it consistently rather than calling it the paper's
line-coverage filter.

Finally run the combined affected suite and applicable formatting or lint
checks. Investigate interactions between individually passing candidates.

## Deliver the patch and evidence

For each retained test, report the behavior protected, expectation source,
execution results, and newly covered production locations. Include baseline and
final coverage with the measurement scope, exact commands, and relevant artifact
paths. Report limitations and possible defects separately from accepted tests.

If no candidate qualifies, report that outcome and the remaining gaps instead
of adding low-value tests to force a result. Keep the patch ready for normal
review; test generation alone does not authorize publishing or merging it.
