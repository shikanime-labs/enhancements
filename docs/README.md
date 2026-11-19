<!-- owner: Automata | zone: technical | purpose: Canonical technical index for the cross-organization enhancements and SEP process repository -->
# Enhancements

Cross-organization enhancement tracking for Shikanime Labs and Shikanime
Studio. This repo holds one umbrella issue per enhancement and the
[SEP](seps/README.md) (Shikanime Enhancement Proposal) that is the design and
coordination document behind it. A SEP is required for anything non-trivial,
and an enhancement may span both organizations.

## Canonical sources

- [Repository README](https://github.com/shikanime-labs/enhancements#readme)
- [Technical wiki](https://github.com/shikanime-labs/enhancements/wiki)

## What qualifies

An enhancement introduces a new project, service or platform capability;
needs multiple teams or both orgs; will be announced or needs retraining;
changes shared interfaces, schemas or fleet conventions; or takes roughly
10+ person-days. Bug fixes, flaky tests, routine dependency maintenance and
single-repo changes with no cross-cutting impact are not enhancements. When
unsure, file an issue and let triage decide.

## Layout

- `README.md` — the process: socialize → file tracking issue → write SEP →
  iterate → track stages, plus the enhancement criteria and labels.
- `seps/README.md` — SEP guide: where a SEP lives, the status flow and the
  template rule. SEPs live under `seps/<owning-org>/NNNN-short-title/`;
  cross-cutting SEPs belong to the org carrying most of the work, with the
  other listed in `participating-orgs`.
- `seps/NNNN-sep-template/` — `sep.yaml` and `README.md` body, copied when a
  tracking issue is filed. The issue number becomes the SEP number.
- `seps/shikanime-labs/` — current proposals, each a directory with
  `sep.yaml` plus a `README.md` body; e.g. `7-label-taxonomy` and
  `15-docs-as-agent-knowledge-base`.
- `.github/ISSUE_TEMPLATE/enhancement.md` — the tracking-issue form.

## Process and stages

Copy the template into `seps/<owning-org>/NNNN-short-title`, fill `sep.yaml`
and the body, and open a PR linking the issue. Merge early as `provisional`,
then refine in follow-up PRs; implementation PRs in product repos reference
the SEP. The canonical `status` field in `sep.yaml` moves
`provisional` → `implementable` → `implemented`, with `deferred`, `rejected`,
`withdrawn` and `replaced` as terminal states. Issue stage labels mirror the
SEP status for board views.

## Issues close deliberately

Auto-close on merged linked PRs is disabled. Issues close only after their
acceptance conditions are verified — a merged PR is not the end of an
enhancement.

## Labels

`stage:provisional`, `stage:implementable`, `stage:implemented`,
`stage:rejected` and `stage:withdrawn` mirror SEP status; `project` marks a
new project proposal and `kind/enhancement` an enhancement to existing
systems.

## Documentation rule

In-repo `docs/` is canonical for technical detail. The wiki is a rendered
human index that links here.
