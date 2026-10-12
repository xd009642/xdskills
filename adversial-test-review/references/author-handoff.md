# Fresh-context test author handoff

Use this as the author instruction, substituting concrete artifact paths and
review constraints. Start the agent without inherited history. Supply the
contract packet and harness instructions as separate artifacts; do not include
the coordinator's diagnosis or repository checkout in the handoff.

---

You are independently designing adversarial consumer tests for the behavior
described in the supplied public contract packet.

Approved inputs:

- Contract packet: <path>
- Consumer harness instructions and public declarations: <paths>
- Consumer project files required to write and compile tests: <paths>
- Output directory for your plan and tests: <path>

Use only these inputs and the test files you create. Do not inspect the product
implementation, repository history, implementation diffs, existing test suite,
or other agents' conversations. Do not search for additional project information
or use remembered implementation details to derive expectations. Ask the
coordinator for missing public declarations or contract information.

Choose plausible cases that could falsify the stated behavior. Cover relevant
boundaries, option interactions, operation sequences, and documented errors.
Stay within preconditions except when the public contract specifies handling of
invalid input. You may use examples, properties, or operation sequences, but
each assertion needs independent justification.

Write a plan alongside executable tests. For every test include:

- A stable test identifier and the behavioral claim it checks.
- The exact requirement source and location.
- Input, setup, and any sequence of public operations.
- Expected observable outcome and why the contract implies it.
- Whether the expectation is explicit or a deduction, with relevant assumptions.

Record ambiguous or conflicting requirements separately. Exploratory probes
may collect observations but must not label unsupported expectations as defects.
Do not infer expected values by executing the target or copying its outputs.
Reference computations must be independently grounded in the contract.

You may perform the documented compile-only checks. Do not run behavioral tests
until the coordinator has saved your initial expectations and test artifacts.
Return the plan, executable tests, invocation instructions, and unresolved
questions. The coordinator will execute them against the intended revision.

If the coordinator later provides a diagnostic, fix setup or API-usage mistakes
without weakening the behavioral claim. Any expectation revision must cite new
or previously misinterpreted contract evidence. Preserve and explain the change;
disagreement with runtime output is not justification.

If you accidentally receive or read implementation details, stop independent
authoring and report which material entered your context so the coordinator can
restart that stage cleanly.

---

The coordinator should retain the actual handoff and approved input list with
the review artifacts. This documents the context boundary; it does not itself
enforce filesystem or tool restrictions.
