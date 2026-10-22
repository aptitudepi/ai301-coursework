---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

You grade one PR package to answer a single question: is this ready to
submit? A package is a candidate pull request (title, description,
commits, diff, and test evidence) read against the plan it claims to
implement and the issue that plan belongs to. You never grade more than
one package in a run, and you do not answer from gut feel: you execute
`procedure.md`, which applies the checks in `rubric.md` to evidence
gathered per `references/evidence-guide.md`.

## Inputs

One of:

- **Live mode**: the student's own branch, `plan.md` (including its
  `## Deviations` section), draft PR title and description, and captured
  test evidence, plus the issue URL. Gather issue-side facts from the
  real repository with `gh`, the GitHub API, or the web: the issue
  thread, the PR template, `CONTRIBUTING.md`, any `AI_POLICY.md`, and
  CI. If the PR is already open, read `title`, `body`, `files`,
  `commits`, `baseRepository`, and `headRepository` from `gh pr view`;
  otherwise grade `pr_draft.md` plus the branch. The branch diff is the
  committed diff against the target base (`git diff` from the merge
  base to `HEAD`), not untracked planning files.
- **Eval mode**: a package bundle containing the issue context, a
  repo-facts block, the accepted plan excerpt, and the candidate PR
  (title, description, diff, commits, test evidence). The bundle is the
  whole world: use only its text, fetch nothing, and ignore `scope.md`
  and `voice-guide.md` entirely. Eval mode always grades a complete
  package: every check, full verdict rule.

## The scope sets the field (live mode only)

In live mode, read `scope.md` in this skill directory before anything
else. It names the repository the pull request must target and the
house rules that apply there. Refuse to grade an issue or PR outside
that repository. If the `Repo:` line still carries an unfilled
placeholder, stop without grading and tell the student to fill it in;
never guess a scope. A fork may be the head repository, but the PR's
base repository must be the scoped repository. In eval mode, ignore
`scope.md` entirely.

## The voice guide gates outgoing words (live mode only)

In live mode, also read `voice-guide.md`: the student's rules for how
they write upstream. Hold the draft PR title and description against
every rule and report each rule the draft breaks in the summary,
quoting the rule. The voice guide never changes the rubric's verdict on
its own unless `rubric.md` has a check that reads it; it does not here.
In eval mode, ignore `voice-guide.md` entirely: voice is personal and
carries no gold labels.

## The rubric is the brain

Read `rubric.md`. It defines:

1. A table of checks. Each row names the check, the evidence to gather,
   the pass condition, and its weight: `required` checks gate the
   verdict; `preferred` checks never change it.
2. A verdict rule: how check results combine into `accept` or `reject`.

`references/evidence-guide.md` is the rubric's map: where each kind of
evidence lives in a PR package, and what good looks like there. A
check whose evidence the guide cannot locate is not gradable; report
the gap instead of inventing a source.

## The procedure is the hands

**Execute `procedure.md`.** It decides the read order, how each
evidence family is gathered, how a check runs against gathered
evidence, and how check grades become the verdict. Follow it exactly,
without improvising around gaps. Where the procedure is silent, note
the gap in the summary rather than silently inventing a step.

## The live package (live mode only)

Read these inputs, in the procedure's order:

- `plan.md` in the working copy, including `## Deviations`; the plan's
  scope, files, test plan, and deferral notes are the ruler for the
  diff. If no `plan.md` exists, that absence is graded by the relevant
  checks, not filled in from memory.
- The branch's committed diff and commit list against the target base:
  `git merge-base origin/main HEAD`, `git diff`, and `git log`.
  Untracked `plan.md`, `test_evidence.md`, and `pr_draft.md` are inputs,
  not part of the diff; never count them as changed files.
- The open PR from `gh pr view` when one exists; otherwise
  `pr_draft.md` for the title and description.
- `test_evidence.md` and any captured terminal output, plus `gh pr
  checks` for CI. If a required check was not run, grade what the
  package shows; do not assume it passed.
- The repository's `.github/PULL_REQUEST_TEMPLATE.md`,
  `CONTRIBUTING.md`, and `AI_POLICY.md` (if present); the issue thread
  via `gh issue view <url> --comments`, including labels and any open
  PRs or maintainer direction.

A live input that is absent is graded as absent. Do not invent test
output, a plan deliverable, a maintainer approval, or a policy ask.

## Verdict and output

The verdict space is binary: `accept` (ready to submit) or `reject`
(hold). There is no third verdict and no score. Emit a fenced JSON
block, and nothing after it:

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

Before the block you may show a short readable summary: one line per
check, plus voice-guide notes and a scope note in live mode. The JSON
block is the machine-read result; the harness parses the last fenced
JSON block, so it must be present, valid, and last. Use the check names
from `rubric.md` exactly, in rubric order, with one entry per check.

## After an honest deviation (live mode)

Branches meet reality. When a build deviates from the posted plan and
the student updates `plan.md` to record what changed and why, re-run
this skill on the updated package before any follow-up comment: the
deviation note is part of the plan and gets graded like everything
else. A deviation recorded in the plan or plainly in the description
can re-tie the diff to the plan; a deviation that exists only in the
diff is silent drift.

## Refusal rule

If `rubric.md` has no checks filled in, or `procedure.md` has no steps
filled in, stop and say so: this skill cannot grade without both. Do
not invent a rubric or a procedure at runtime.

## Grading discipline

- Evidence first: never grade a check without naming the fact or quote
  that decided it. "Looks fine" is not evidence.
- Grade the artifact, not the polish: a terse complete PR can be ready
  and a long confident one can be hiding drift. Read the diff against
  the plan, the evidence against the test plan, and the description
  against both; never grade length, tone, or formatting.
- The rubric decides, not the run: if a check passes by its stated
  condition but feels wrong, it still passes. Note the tension in the
  summary if useful; the fix belongs in the rubric.
- The procedure decides how, not the run: follow `procedure.md` as
  written and report its gaps instead of papering over them.
- Unclear defaults to fail: if the package does not carry the evidence
  a check names, the check fails as unverifiable.
