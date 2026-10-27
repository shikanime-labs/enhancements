# SEP-0007 (tracking issue #7): Label taxonomy and reset automation

## Summary

One label convention for every non-fork repository in both orgs, and a
workflow that keeps repositories converged to it. The convention is two
layers — an org-wide standard set and repo-scoped families using the
`prefix:value` colon form — plus explicit exceptions for labels owned by
automation.

The reset automation re-applies the standard set with canonical colors
and descriptions, reports deviations, and never touches automation-owned
labels.

## Motivation

The org accumulated three failure modes, all observed during the 2026-10
label audit:

- **Drift** — the triage quartet shipped org-wide with one canonical
  description; three repositories had reworded versions. Same name,
  four meanings.
- **Debris** — a malformed duplicate of a reviewer label
  (`Review effort [1-5]: 2` alongside `Review effort 2/5`) sat in
  `machines` unused.
- **Dead sets** — `auto`, `ghstack`, `stale`, and language labels
  survived on repositories long after the integrations that used them
  ended, indistinguishable from live labels without an audit.

Each was fixed by a one-off pass. This SEP makes the converged state a
maintained invariant instead of a memory.

### Goals

- A written convention: what exists, who owns it, what it may be named.
- A converging workflow: run anywhere, drift is repaired or reported.
- Automation-owned labels (`dependencies` from Renovate, reviewer
  labels) are recognized and skipped, never "fixed".

### Non-Goals

- Governing GitHub Projects fields (`Priority`, `Size`, `Status`) —
  boards own their field configuration; that is a separate concern.
- Migrating issue/PR label usage during a reset; the reset reconciles
  definitions, and renames migrate usage explicitly as one-off PRs.
- Governing fork repositories; they track upstream.

## Proposal

### The taxonomy

**Standard set** — present in every non-fork repo, canonical name, color,
description:

- GitHub defaults: `bug`, `documentation`, `duplicate`, `enhancement`,
  `good first issue`, `help wanted`, `invalid`, `question`, `wontfix`.
- Triage quartet: `needs-triage` (Awaiting triage), `needs-info` (More
  information required from reporter), `ready-for-agent` (Triage
  complete; safe to delegate to an agent), `ready-for-human` (Triage
  complete; needs human decision or review).

**Scoped families** — repo-specific vocabularies, `prefix:value` colon
form, lowercase values:

- `stage:` — SEP lifecycle in the enhancements repository
  (`provisional`, `implementable`, `implemented`, `rejected`,
  `withdrawn`). The only ordered family; one value at a time.
- `reproduce:` — defect reproduction frequency, ordered buckets,
  one value per defect: `always` (100% reproducible), `often`
  (50–80%), `rare` (<50%), `none` (unable to reproduce by QA/Eng).
  Applied by triage to `bug`-typed issues; drives prioritization
  alongside `Priority` (an `always` P2 outranks a `none` P1 in
  practice).
- `backport:` — the release needing an already-landed change ported
  back; the value is the target version tag (`backport:1.2`). Multi
  value: a fix may need two branches; one label per target.
- `candidate:` — the upcoming release this item is considered for;
  value is the version (`candidate:1.3`). Removed when the item is
  included or cut; `backport:` takes over once shipped and porting
  remains.

Version values carry digits and dots — the "single word" rule means no
spaces, not no punctuation.

A family earns a prefix when it has two or more related values
answering one question. A single value takes the default form with no
prefix (`project`, `photoshoot`).

**Automation-owned** — present where the integration runs, excluded from
resets: `dependencies` (Renovate), reviewer effort labels
(`Review effort N/5`, `Possible security concern`).

Naming: the standard set keeps GitHub default syntax (lowercase words,
spaces or hyphens as already established); scoped families use the
colon.

### The reset workflow

A workflow in each org's `.github` repository, dispatched manually or on
schedule, iterates the org's non-fork repositories and per repository:

1. Reads current label definitions.
2. Applies the standard set: creates missing labels, rewrites drifted
   colors and descriptions to canonical values.
3. Verifies every scoped family member referenced by an issue form
   (SEP-0006 (tracking issue #5)) exists.
4. Emits a report: repos converged, repos deviating with the exact
   deviation, automation-owned labels skipped.

The workflow reports; it does not delete unknown labels. Deletion of a
custom label is a human decision because an unused-looking label may be
awaiting its first use.

The implementation is a script executed by the workflow, not workflow
YAML gymnastics; the script is the single source of the canonical table
above.

### Risks and Mitigations

- **Reset fights an intentional local variant** — a repo deliberately
  rewords a description. Mitigation: the canonical table is code and
  changes by PR; a local variant that should win becomes a table change
  reviewed like any other.
- **Scheduled resets mask a broken rename** — a typo in the canonical
  table would faithfully propagate. Mitigation: the workflow posts its
  diff as a report before applying, on the scheduled path; apply-only-
  after-report for changes beyond create-missing.

## Design Details

### Test Plan

- The script runs in check mode (report only) and apply mode; CI runs
  check mode against both orgs so drift PRs fail visibly.
- A fixture repository with known drift (wrong description, missing
  label, automation-owned label) validates both modes.

### Graduation Criteria

- alpha: script exists, canonical table matches the audited state,
  check mode green.
- beta: scheduled run in both orgs, apply path used for one real drift
  event.
- stable: one quarter of scheduled runs with no unexplained deviation.

## Rollout / Upgrade Strategy

Ship check mode first — reports only, no writes — for two weeks. Apply
mode is enabled per org after the reports stabilize, since the audited
state is already converged today.

Rollback is disabling the workflow; labels are API state and any bad
apply is revertible by re-running with a corrected table.

## Drawbacks

A moving part that can annoy: a reset that reverts a deliberate local
change reads as the workflow misbehaving. The report-before-apply path
and PR-reviewed canonical table are the containment; the residual cost
is accepted.

## Alternatives

- **Per-repo label yml files (e.g. labeler-style configs) in every
  repo** — rejected: the convention lives in N places instead of one,
  and nothing checks the copies agree.
- **Org-wide default labels only, no scoped families** — rejected:
  product-specific vocabularies (SEP lifecycle, strategy areas) are
  real; forbidding them moves them to unlabeled text.

## Infrastructure Needed

None beyond GitHub Actions minutes in the two org `.github` repos.

## Implementation History

- 2026-10-27: SEP drafted and filed with tracking issue #7. The audited
  state it codifies was reached by the 2026-10 audit passes (quartet
  rollout, legacy label removal, colon-prefix restore).
