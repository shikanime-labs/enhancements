# Shikanime Enhancement Proposals (SEPs)

A Shikanime Enhancement Proposal (SEP) is the design and coordination
document for a new project or significant enhancement across Shikanime Labs
and Shikanime Studio. SEPs are inspired by Kubernetes KEPs, IETF RFCs, and
Rust RFCs: one numbered document per enhancement, covering its whole
lifecycle.

## Quick start

1. **Socialize the idea** where the affected teams work. Confirm an owning
   org.
2. **File a tracking issue** in this repo using the enhancement template.
   The issue number becomes the SEP number.
3. **Copy the [template](NNNN-sep-template)** into
   `seps/<owning-org>/NNNN-short-title/`, fill in `sep.yaml` and the body,
   and open a PR linking the issue.
4. **Iterate.** Merge early as `provisional`; refine in follow-up PRs.

## Do I have to use the SEP process?

For anything non-trivial, yes:

- new projects or services
- cross-org or cross-team changes
- changes to shared interfaces, schemas, or fleet conventions
- anything controversial or requiring retraining of operators

Small scoped fixes do not need a SEP. When unsure, open the issue and let
triage decide.

## Where does a SEP live?

Under its owning org directory: `seps/shikanime-labs/` or
`seps/shikanime-studio/`. Cross-cutting SEPs belong to the org that will
carry most of the work; list the other org in `participating-orgs`.

## Status flow

`provisional` → `implementable` → `implemented`
(plus `deferred`, `rejected`, `withdrawn`, `replaced`)

The `status` field in `sep.yaml` is canonical; issue stage labels mirror it
for board views.
