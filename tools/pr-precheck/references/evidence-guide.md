# Evidence guide: where evidence lives in a PR package

## Plan fidelity (harness category: silent-drift)

Where it lives: in an eval bundle, the plan-context block's scope
statement, named files, test plan, and deferral notes; then the
candidate PR's diff changed-file list and hunks, its commit summaries,
and its title and description. In live mode, the same facts live in
`plan.md` (including `## Deviations`), the committed diff from the
target base (`git merge-base origin/main HEAD` through `HEAD`), the
commit list, and the draft title and description in `pr_draft.md` or
`gh pr view`.

What good looks like: every changed file and behavior falls inside the
plan's stated boundary, and every deliverable the plan names is in the
diff. A deviation is re-tied when the plan's deferral notes or the
description explicitly name the extra or missing work and give a
reason. Silent drift is either direction: an unplanned option, config,
flag, refactor, rename, or file riding along; or a plan deliverable
that is absent while the description still claims it. The description's
claims about files, behavior, tests, and scope are part of this family:
a description that claims more or less than the diff delivers is
silent drift, not a communication problem. Explanatory comments and
plan-named test, changelog, docs, or i18n files are not drift.

## Test evidence (harness category: not-tested)

Where it lives: in an eval bundle, the candidate PR's test-evidence
section and any before/after lines in the description, read against
the plan-context block's test plan and the issue reproduction's steps,
plus the repo-facts block's stated checks. In live mode, the same facts
live in `test_evidence.md`, captured terminal output, `gh pr checks`,
and the test assertions in the diff.

What good looks like: the same command, input, and failure path as the
issue is shown before the fix and after it, with the observed result
that differs (exact output, exit code, state, or assertion). A
failing-before and passing-after test counts when its assertion or
output is shown. A condensed transcript counts when it names the
command or input and both observed states. The repo's stated check or
test suite is run with a visible command and result, or the package
discloses why a required check could not run. Not decisive: "tests
pass", "tested locally", a screenshot or prose with no observable
behavior, only the after state, or evidence from a neighboring property
or wrong path (the wrong request method, the wrong casing, or a
single-file control when the bug is cross-file). A plan that explicitly
defers a subcase is proved on the remaining behavior by evidence plus
the stated deferral.

## Diff quality (harness category: unreviewable)

Where it lives: in an eval bundle, the unified diff's changed-file list
and added and removed lines, plus the commit summaries. In live mode,
the same facts live in the committed diff against the target base and
`git log`, not in untracked planning files.

What good looks like: the fix or behavior change is visible and the
diff contains only work the plan or repo asks for. Debris tells: debug
output left in (print, eprintln, console.log, dbg!, System.debug, or
fmt.Println, including commented-out calls), commented-out code, dead
or unused functions, imports, variables, or constants, TODO or FIXME
notes on the changed path, whitespace-only or re-indentation or
import-reordering hunks, and changed files or hunks unrelated to the
plan. Explanatory comments, plan-named tests, and changelog, docs, or
i18n files required by the plan or repo are not debris; a refactor the
plan names is in scope.

## Standards and comms (harness category: standards-wall)

Where it lives: in an eval bundle, the repo-facts block's PR-template
asks, contribution policy, and stated AI policy; then the candidate
title and description, and the diff for artifacts the asks name
(changelog, whatsnew, docs, tests, i18n, cheatsheets). In live mode,
the same asks live in `.github/PULL_REQUEST_TEMPLATE.md`,
`CONTRIBUTING.md`, `AI_POLICY.md` if present, the issue thread, and the
draft title and description or the open PR.

What good looks like: every ask the repo states for this PR is visibly
satisfied. Required sections carry specific content, required
checklist lines are present, the issue link or closing line is there,
and any required changelog, whatsnew, docs, test, i18n, or cheatsheet
artifact is in the diff; a stated title convention is followed. If the
repo states no template or asks, there is nothing to satisfy. A
required section that is absent or boilerplate, a missing closing link,
a required artifact absent from the diff, or an ignored checklist item
is a standards wall. A conditional ask (for example, a screenshot when
appropriate) does not fail a change with no visible surface. AI
disclosure is its own rubric check: apply the stated policy to the
description even when the description never mentions AI. A bare
"AI-assisted" does not satisfy a policy that requires the tool and the
extent.
