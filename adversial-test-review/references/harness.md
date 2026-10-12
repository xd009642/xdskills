# Prepare an ad hoc consumer harness

Choose the smallest harness that faithfully exposes the relevant public contract.
The coordinator may inspect the repository to build it; the independent author
receives its usage contract, not implementation details.

## Choose the interface

- Library: prefer a temporary consumer project importing the library through its
  public API. Respect its supported language/toolchain version. Test files should
  not gain access to private modules merely by being placed inside the library.
- Service: expose the documented endpoint through a local test deployment and
  describe request construction, authentication fixtures, and observable responses.
- CLI: expose executable invocation, arguments, stdin, exit status, stdout/stderr,
  and relevant filesystem effects.
- Structured tool or script: useful when it avoids repeated setup, provided it
  preserves the semantics needed by the tests.

Do not build a universal adapter in advance. Generate project-specific glue when
it materially simplifies access. If bridging languages loses a relevant
distinction, use native consumer tests for that behavior.

## Preserve observables

An adapter must not silently sort output, deduplicate values, retry failed
operations, coerce types, collapse errors, or replace exceptions with success.
Preserve relevant distinctions such as absent versus null, integer precision,
ordering, identity, error category, and state after failure. Expose concurrency
or callback behavior directly when the contract depends on it; a sequential
JSON wrapper may be unsuitable.

Keep expectations out of the adapter. It may transport calls and observations;
it must not decide what the correct domain result should be.

## Write the author-facing instructions

Document only what is needed to construct reproducible consumer tests:

- Public operations or import signatures and the relevant contract packet paths.
- Language/toolchain, test framework, dependencies, and permitted output paths.
- Invocation syntax, input/output representation, and error representation.
- Required configuration, fixtures, resource limits, and setup/cleanup commands.
- Whether calls share state and how to obtain a fresh instance or reset fixtures.
- How to compile or validate syntax without running behavioral tests.
- How to return tests to the coordinator, who performs behavioral execution.

For example, a CLI harness could specify the executable placeholder, argv
construction, temporary working directory, and captured process result. A Rust
library harness could provide a prepared manifest, import signatures, and a
compile-only command such as `cargo test --no-run`. Neither needs sample results
from the implementation under review.

Use synthetic examples to explain encoding if necessary, clearly marked as
illustrations rather than evidence of target behavior. Keep implementation
stack traces, source links, and smoke-test results out of the instructions.

## Validate and record

Check that the harness reaches the intended revision, exposes the real public
API, and can initialize and clean up its fixtures. Record build/startup evidence
for the coordinator. A smoke test confirms mechanics, not the intended behavior.

If a harness cannot faithfully expose a required behavior, record the gap and
choose a different consumer interface rather than adding compensating behavior.
Record any harness changes between base and target so adapter differences are
not mistaken for product differences.
