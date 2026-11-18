# SEP-0015 (tracking issue #15): In-repo docs as the agent workflow knowledge base

## Summary

In-repo `docs/` becomes canonical for agent-facing workflow knowledge in the
skills catalog. The GitHub wiki stays a rendered human index that links outward
to those files. Agents read the repository, not the `.wiki.git` remote, so any
workflow fact that lives only in the wiki is invisible at runtime. Detail loads
in three steps: name and description at startup, `SKILL.md` when the skill
triggers, `references/` only when a stated condition fires. That chain works on
repository files only.

The split is already broken. Wiki `Home.md` and `Operations.md` point authoring
rules at `skills/sks-skill-authoring/SKILL.md`, which does not exist on `main`.
The real guidance is `docs/authoring-practices.md`. A reader following the wiki
lands on a dead path, and the drift goes unnoticed because wiki pushes bypass
review.

## Motivation

The catalog keeps both surfaces today and they are not reconciled. The wiki
carries `Home`, `Architecture`, `Operations`, `Process`, and `_Sidebar`, all
human-facing and pushed without review. `docs/authoring-practices.md` carries
the loading model and authoring rules, versioned and PR-reviewed.

The underlying distinction is version control versus accessibility. A wiki is
browser-editable, separated from the code, and drifts when code moves. `docs/`
is versioned with the code and reviewed before landing. For this catalog the
accessibility that matters is agent accessibility, which the wiki structurally
cannot provide.

### Goals

- One source of truth per fact: agent-facing knowledge in `docs/`, human
  overview in the wiki, links between them, never two copies.
- An agent can find the workflow for any of the 48 `sks-*` skills by following
  pointers from the skill it already loaded, without loading the whole family.
- No wiki page points at a path that is absent from `main`.

### Non-Goals

- Migrating, restructuring, or deleting the existing wiki pages.
- A separate release doc, an eval harness, or a docs build pipeline.
- Publishing agent-facing detail to the wiki.
- Changing the authoring rules themselves; this SEP changes where they live and
  how they are found.

## Proposal

Four units, in dependency order. Unit 1 owns the decision, unit 2 writes the
content, units 3 and 4 fan out after unit 2 because they touch different
surfaces.

1. Encode the split in `sks-wiki` doctrine: agent-facing knowledge lives in-repo
   under `docs/`; the wiki is a rendered human index; link, never fork; agents
   read `docs/`, not the wiki.
2. Add `docs/agent-workflow.md`: the three loading levels, a family map of the
   48 `sks-*` skills by concern, the coordination ladder, and explicit
   load-condition pointers into `SKILL.md` and `references/`. No duplication of
   the wiki's human overview.
3. Add load-conditioned pointers to that doc in `sks-context`, `sks-plan`,
   `sks-curate`, and `sks-wiki`, and cross-link `docs/authoring-practices.md`.
4. Repoint wiki `Home` and `Operations` to `docs/authoring-practices.md`, add a
   quick pointer to the new doc, and confirm no agent-facing detail exists only
   in the wiki.

All four target the open `26.11` milestone in `shikanime-labs/skills`. The repo
is on the semver train at `2.0.0`, so no goal-titled milestone is minted for the
work.

### User Stories

- As an agent assigned a task in the catalog, I load one skill and follow its
  pointers to the family workflow, so I do not read 48 skill bodies to find the
  right next step.
- As a maintainer editing authoring rules, I edit one PR-reviewed file in
  `docs/`, so the rule cannot silently diverge from what agents read.
- As a human reading the wiki, I see an accurate index that sends me to the
  canonical in-repo detail, so I never follow a dead path.

### Risks and Mitigations

- Wiki pushes bypass review, so a future edit can reintroduce duplication. The
  `sks-wiki` doctrine names the rule and the `Home` index carries the outward
  links; unit 4 verifies the current state.
- The family map can go stale as skills are added. Unit 3 wires pointers from
  the skills themselves, so the map is a convenience rather than a single point
  of failure.
- A reader may expect the wiki to be self-contained. `Home` states that in-repo
  `docs/` is canonical and links directly.

## Design Details

### Test Plan

Each implementation unit carries a command-decidable acceptance ledger: the
doctrine text exists in `sks-wiki`, `docs/agent-workflow.md` maps every `sks-*`
skill with a load condition, the four named skills link to it, wiki `Home` and
`Operations` resolve against `main`, and no agent-facing workflow fact exists
only in the wiki. Verification is a repository grep plus a rendered-page check,
not a subjective review.

### Graduation Criteria

- `alpha` — SEP merged as provisional and the four units boarded onto `26.11`.
- `beta` — units 1 through 3 landed; agents can reach the workflow from any of
  the four entry skills.
- `stable` — unit 4 landed, wiki pointers resolve, and a sweep finds no
  agent-facing detail isolated in the wiki.

## Rollout / Upgrade Strategy

Each unit lands through the normal dev loop in `shikanime-labs/skills`: jj
workspace off `main@origin`, one commit per unit, PR per unit, squash merge in
dependency order. Unit 1 lands before any content moves. The wiki change is a
plain push after the in-repo doc exists, so no reader sees a link to a missing
file. Rollback is per unit: revert the commit, then repoint the wiki if unit 4
has landed.

## Drawbacks

Two surfaces remain, so contributors must know which one owns a given fact. The
rule is short and lives in `sks-wiki`, but it is a convention that has to be
taught. The wiki also stays unreviewed by design; this SEP limits the blast
radius rather than removing it.

## Alternatives

Keep the wiki as the sole knowledge base. Rejected: agents never clone the wiki
remote, so the workflow becomes undiscoverable at runtime.

Keep `docs/` only and disable the wiki. Rejected: it removes the rendered human
index that already exists and works, and it discards the architecture,
operations, and process pages without a replacement.

Duplicate the workflow into both surfaces. Rejected: two copies drift, and the
drift is invisible on the wiki side because pushes bypass review.

## Infrastructure Needed

None. The work uses the existing wiki remote, the existing `docs/` directory,
and the existing PR pipeline in `shikanime-labs/skills`.

## Implementation History

- 2026-11-18 — tracking issue #15 opened; socialized from
  `shikanime-labs/skills#636`.
