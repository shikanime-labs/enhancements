# Enhancements

Cross-organization enhancement tracking for Shikanime Labs and Shikanime
Studio. This repo is where new projects and significant enhancements are
proposed, discussed, and tracked to completion before and while work begins.

It contains issues and [SEPs](seps/README.md) (Shikanime Enhancement
Proposals). Issues are umbrellas for an enhancement; the SEP is the design
document behind it. An enhancement can span both organizations, and a SEP is
required for anything non-trivial.

## Is my thing an enhancement?

An enhancement is anything that:

- introduces a new project, service, or platform capability
- requires multiple teams or both organizations to complete
- will be announced or written up when it ships
- changes how people build, deploy, or operate fleet systems in a way that
  needs coordination or retraining
- needs significant effort (roughly 10+ person-days) or changes shared
  interfaces, schemas, or conventions

It is unlikely an enhancement if it is:

- a bug fix, a flaky test, or routine dependency maintenance
- a small change scoped to a single repo with no cross-cutting impact
- performance work invisible to consumers

When unsure, file an issue here anyway — triage will sort it out.

## When to create an issue

Create an [issue](../../issues/new/choose) once you:

- have circulated the idea in the relevant team channel, meeting, or repo
- optionally have a prototype or proof of concept
- have identified who would review, approve, and work on it
- are ready to act as the coordinator for the enhancement

An enhancement may be filed as backlog before implementation work starts.

## The process

1. **Socialize.** Circulate the idea where the affected people are. Confirm
   at least one organization will own it.
2. **File a tracking issue.** Use the Enhancement tracking issue template.
   This issue number becomes the SEP number.
3. **Write the SEP.** Copy
   [`seps/NNNN-sep-template`](seps/NNNN-sep-template) to
   `seps/<owning-org>/NNNN-short-title`, fill in `sep.yaml` and the body,
   and open a PR. Link the SEP from the issue.
4. **Iterate.** Merge early as `provisional`, refine in follow-up PRs.
   Implementation PRs in product repos reference the SEP.
5. **Track stages.** Update `sep.yaml` as the enhancement moves through
   `provisional` → `implementable` → `implemented`. Move the issue through
   the stages using stage labels.

## Issues close deliberately

Auto-close on merged linked PRs is disabled for this repo. Issues are closed
only after their acceptance conditions are verified — a merged PR is not the
end of an enhancement.

## Labels

- `org/labs`, `org/studio` — the owning and participating organizations
- `stage/provisional`, `stage/implementable`, `stage/implemented`,
  `stage/rejected`, `stage/withdrawn` — SEP status mirror
- `kind/project` — new project proposal
- `kind/enhancement` — enhancement to existing systems
