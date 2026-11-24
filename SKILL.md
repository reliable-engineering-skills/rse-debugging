---
name: rse-debugging
description: "Fix bugs from evidence: crashes, errors, exceptions, wrong results, failing or flaky tests, and performance regressions. Use whenever the user reports something broken or asks to fix, debug, diagnose or investigate a failure, even when the cause looks obvious. Reproduce it first with the smallest failing check, fix the cause rather than the symptom, and leave a regression test that fails on the original code and passes after the fix, even when you cannot run it here (then say it did not run). A request to investigate or explain produces a diagnosis, not a change. Builds on the reliable-software-engineering skill."
metadata:
  version: "3.2.0"
---

# Debugging from a reproduction

> Companion of the `reliable-software-engineering` skill, which must be installed alongside. Its core rules apply here; links into `../reliable-software-engineering/` point at its references.

Use for any reported bug: something crashes, errors, returns a wrong result, fails or flakes. When the cause is obvious, sections 1 and 2 shrink to reading the trace and the code, but section 4 still applies: a fix without a regression test is unproven. A request to explain or investigate produces a diagnosis, not a change.

## 1. Reproduce before you explain

Read the exact error, stack trace, inputs, versions and environment first. Then build the smallest check that fails on this bug, and run it once to see it fail. Prefer, in order:

- a failing test at the public seam that reaches the bug;
- a script or command that feeds a captured input and compares the output;
- a request against a local or disposable instance;
- a replay of a captured, redacted payload;
- for intermittent failures, a loop that raises the failure rate (repetition, load, narrowed timing) until it is observable.

Make the check fast and deterministic where possible: pin time and randomness, isolate files and network.

Reproduce only against local data, fixtures or sanitized copies. Production, real accounts, real payments and shared services need explicit authorization. Redact secrets, tokens and personal data in everything you show.

If no failing check can be built, stop guessing. Report what you tried, what evidence is missing, and the smallest thing the user can provide: access, a log, a captured request, or exact steps. A diagnosis without a reproduction is labelled as a hypothesis.

## 2. Narrow the cause with evidence

- List hypotheses that explain every observed symptom, ranked by likelihood and by cost to test.
- Test one at a time against the reproduction: logs at boundaries, assertions, bisecting inputs, configuration or commits (`git bisect run` with the check). Change one variable per experiment.
- Keep what each experiment showed. Drop a hypothesis the evidence contradicts, however plausible it sounds.
- Read the responsible code at the version that actually runs, and check installed library versions against their official documentation before blaming a dependency.
- Remove temporary instrumentation before finishing.

## 3. Fix the cause, not the symptom

- Fix where the wrong behavior originates. When that is a shared function, check every caller before changing it; when the right owner is a boundary (a page, a handler), handle it there. Decide deliberately and say what the other callers now see.
- Do not make the symptom disappear by suppressing the error, widening a catch, adding a retry, raising a timeout or disabling a check. Retries and timeouts change contracts: read [move-sensitive contracts](../reliable-software-engineering/references/move-sensitive-contracts.md) first.
- Report unrelated failures you meet; do not fix them in passing.

## 4. Prove it

- The reproduction now passes. Keep it as a regression test that fails on the original code and passes after the fix ([verification](../reliable-software-engineering/references/verification.md#detect-superficial-tests)).
- When commands cannot run here, still write the regression test and the command that runs it, and say that neither ran.
- Run the relevant existing tests. For an intermittent bug, repeat the check enough times to make the claim meaningful, and state how many.
- Report the symptom, the root cause and its evidence, the fix, the regression test, what ran and where, and what remains unverified.

## Stop conditions

Stop and report rather than escalate effort when the next step needs production access, real credentials, a paid or destructive action, or a product decision about what the intended behavior is.
