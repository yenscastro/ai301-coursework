# Evidence guide: where proof lives in a plan package

## Environment and setup

**Where it lives:**
- In the repro-evidence block: the environment section (OS, runtime
  versions, repo commit). In the plan's own test plan section: any
  environment the plan says the test needs.
- In the repo-facts block: the repo's bug-report template asks (does
  it demand version confirmation?).

**What good looks like:**
The plan reuses the repro's environment or explicitly states an
equivalent one. It does not silently swap OS or runtime without
saying so. If the repo's template demands version confirmation, the
plan's test plan names the version it will run against.

## Diagnosis evidence

**Where it lives:**
- In the plan's diagnosis section.
- In the repro-evidence block: the stack trace, error text, or wrong
  value captured in the repro.
- In the issue body: the reporter's described cause, if any.

**What good looks like:**
The plan names the failing function or line, and that function or
line is where the repro evidence shows the failure. If the repro
shows `db.execute("SELECT 1")` raising ArgumentError, the plan's
diagnosis names that call. A diagnosis that names a different call,
or that contradicts the stack trace, fails.

## Scope evidence

**Where it lives:**
- In the plan's scope section: "what I'll change" and "what I won't."

**What good looks like:**
One bounded change: one function or module, plus its test. Explicit
exclusions are stated. A plan that names two unrelated files, or
proposes opportunistic refactors, fails.

## Approach evidence

**Where it lives:**
- In the plan's approach section.
- In the repro-evidence block: the code path the stack trace shows.

**What good looks like:**
The approach modifies the code path the diagnosis names. It says what
the change will look like at the call site (e.g. "wrap in text()").
A vague "handle the error better" without a concrete change fails.

## Files-to-touch evidence

**Where it lives:**
- In the plan's files section or the approach section's first
  paragraph.

**What good looks like:**
Exact file paths, named functions or lines. A stranger could open the
file and begin. "The health module" without a path fails.

## Test plan evidence

**Where it lives:**
- In the plan's test plan section.
- In the repro-evidence block: the repro steps.

**What good looks like:**
The test plan re-runs the repro steps, or a strict equivalent, and
states an observable expected outcome: "GET /health returns 200 with
postgres healthy" instead of "tests pass." "Verify it works" without
a named observable fails.

## Risks and unknowns evidence

**Where it lives:**
- In the plan's risks section.

**What good looks like:**
At least one thing named as uncertain, or an explicit "none." Either
is a pass. A plan that presents inferential steps as certain fails.

## Comment and convention evidence

**Where it lives:**
- In the plan comment.
- In the thread highlights and the repo-facts block's contribution
  policy line.

**What good looks like:**
- The comment names the specific bug by mechanism, not just number.
- It does not contradict a maintainer's earlier statement in the
  thread.
- If the repo requires AI disclosure, the comment discloses.
- Where the plan lists unknowns, the comment hedges appropriately
  rather than promising a complete fix.
  