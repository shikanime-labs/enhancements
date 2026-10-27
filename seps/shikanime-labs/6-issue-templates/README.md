# SEP-0006 (tracking issue #5): Standardized issue templates across org repositories

## Summary

Every non-fork repository in `shikanime-labs` and `shikanime-studio` ships
a small set of GitHub issue templates that ask the questions triage would
otherwise ask, and pre-assign the triage label so the queue is ordered
before a human looks at it.

Templates live in each repository's `.github/ISSUE_TEMPLATE/` and are
distributed by the shared `.github` repository of each org, so a repo gets
them by existing, not by copying files.

## Motivation

Issue quality varies by repository. Triage today starts from free-form
text: the reporter omits context, the triager asks for it, the reporter
answers, and only then does work begin. The `needs-triage` and
`needs-info` labels exist since the org-wide label rollout, but nothing
systematically applies them at creation time.

### Goals

- Every non-fork repo has a bug template and an enhancement template.
- Templates populate the description fields triage needs (what happened,
  what was expected, reproduction, environment) so the first human pass
  can be a decision, not a question.
- New issues land in the triage workflow automatically: bug and
  enhancement templates apply `needs-triage`; a separate support form
  applies `needs-info`.
- Blank issues stay enabled; templates guide without gating.

### Non-Goals

- Issue forms for internal-only repos with no external reporters.
- Auto-assignment of agents or humans at creation time.
- Per-repo bespoke template sets; deviation requires a documented reason.

## Proposal

Two YAML issue forms, plus one optional third:

1. **Bug report** — applies `needs-triage`. Fields: what happened, what
   was expected, reproduction steps, environment (host, version), logs.
2. **Enhancement request** — applies `needs-triage`. Fields: problem
   statement, proposed shape, affected consumers, cross-repo impact (a
   yes answer routes the reporter to the enhancements repository
   instead).
3. **Support / question** (optional, external-facing repos only) —
   applies `needs-info`, since the answer is usually information the
   reporter must supply.

Distribution is ownership, not duplication: each org's `.github`
repository holds the canonical forms; product repositories use
`paths:`-style inheritance where GitHub supports it, otherwise a
sync workflow copies them on change to `.github` default branch. The
mechanism follows whatever the org `.github` repos already use for
shared files.

Templates set labels via the YAML form's `labels:` field; the triage
quartet definitions are owned by SEP-0007 (tracking issue #6).

### Risks and Mitigations

- **Template staleness** — forms drift from what triage actually needs.
  Mitigation: the templates live in one place per org and change by PR,
  so drift is visible and reviewable.
- **Label renames break forms** — a form referencing a deleted label
  silently stops applying it. Mitigation: SEP-0007 (tracking issue #6)'s reset automation
  verifies the referenced labels exist in every repo and reports gaps.

## Design Details

### Test Plan

- Create a test issue from each form in a scratch repo; assert the
  template rendered and the expected label applied.
- Reset automation (SEP-0007 (tracking issue #6)) gains a check: every repo with issue
  forms has `needs-triage` and `needs-info` defined.

### Graduation Criteria

- alpha: forms exist in both org `.github` repos and one pilot repo.
- beta: forms rolled out to all non-fork repos; label application
  verified per repo.
- stable: forms unchanged for one quarter with no triage complaints.

## Rollout / Upgrade Strategy

Pilot in one high-traffic repo, then org-wide. Repos with existing
bespoke templates replace them; the old forms are deleted, not kept
alongside, to avoid a choose-a-path page that splits reporters.

Rollback is deleting the forms; GitHub falls back to the blank issue
page with no other change.

## Drawbacks

A form is friction for a reporter who knows the repo well. Keeping
blank issues enabled preserves the escape hatch at the cost of
occasional free-form issues that need manual triage.

## Alternatives

- **Do nothing and rely on triage labels** — rejected: the labels order
  the queue but arrive after the fact, when the reporter has moved on.
- **One issue template per repo, hand-maintained** — rejected: the
  drift this SEP exists to end.

## Infrastructure Needed

None. Templates are repository content; the reset automation from
SEP-0007 (tracking issue #6) verifies them.

## Implementation History

- 2026-10-27: SEP drafted and filed with tracking issue #6.
