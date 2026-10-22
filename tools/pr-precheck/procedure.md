# Procedure: how this tool grades a PR package

## Read order

1. In live mode, read `scope.md` first. Record the scoped repository.
   If the `Repo:` line is still a bracketed placeholder, stop without
   grading and tell the student to fill it. If the issue or PR is
   outside the scoped repository, refuse. In eval mode, skip this step:
   `scope.md` is ignored.
2. Read the package header and issue context. Record one line naming
   the reported behavior, the expected outcome, and the issue number.
   In live mode, also record the issue's labels, the maintainer
   direction in the thread, and any open PR on the same issue.
3. Read the repo-facts block (or, live, `.github/PULL_REQUEST_TEMPLATE.md`,
   `CONTRIBUTING.md`, and `AI_POLICY.md`). Record every stated PR ask,
   test or check command, changelog or whatsnew ask, branch or title
   convention, and AI policy. Do not invent an ask the repo does not
   state.
4. Read the plan-context block before the diff. Record its scope pair,
   the files it names, the test plan, and every deferral or known
   limit. In live mode, read `plan.md` including `## Deviations` and
   record the same four things. The plan is the ruler; read it before
   the diff so the diff cannot anchor the judgment.
5. Read the candidate PR title and description. Record every claim
   about files, behavior, scope, tests, docs, and disclosures as a
   short claim line. In live mode use `gh pr view` when a PR exists,
   otherwise `pr_draft.md`.
6. Read the candidate diff and commit list. In an eval bundle, read the
   unified diff and commit summaries. In live mode, read the committed
   diff against the target base and `git log`; do not include untracked
   `plan.md`, `test_evidence.md`, or `pr_draft.md` in the diff. Record
   the changed-file list, each behavior-changing hunk, and any debris
   or unrelated hunk.
7. Read the test evidence last. Record each command or input, the
   before state, the after state, and the repo check result. In live
   mode read `test_evidence.md`, captured output, and `gh pr checks`.
8. Before grading, write a one-line evidence note for each check in
   `rubric.md` order. Do not grade from memory; quote or paraphrase the
   sentence, diff line, or output that will decide the check.

## Evidence gathering

- **Plan fidelity**: list the plan's files and deliverables, then list
  the diff's files and behavior changes. Pair each diff item with a
  plan item or a deviation note; pair each plan item with a diff item
  or a deferral. Then read the description's claim lines against the
  same lists. Record every unmatched item and every claim the diff
  contradicts. Grade silent drift in both directions.
- **Test evidence**: pair the plan's test plan and the issue
  reproduction's steps with the evidence section's commands and
  results. Record the exact command or input, the before observation,
  the after observation, and the expected-after line. Then record the
  repo's stated check command and its visible result, or the disclosed
  reason it could not run. A before/after test assertion counts when
  the assertion or its output is shown; "tests pass" alone does not.
- **Diff quality**: read every added and removed line, not only the
  file names. Record debug output, commented-out code, dead or unused
  symbols, TODO or FIXME notes, formatting-only hunks, and any hunk
  unrelated to the plan. Distinguish explanatory comments and
  plan-named tests, changelog, docs, and i18n files from debris.
- **Standards and comms**: for each repo ask, record the ask, where the
  description or diff satisfies it, and the quoted evidence. Treat a
  required section, checklist line, closing link, or named artifact as
  failed only when the repo states it. In live mode, also record the
  PR's base repository and head repository: the base must match the
  scoped repository; a fork may be the head. Record voice-guide notes
  separately for the summary; they do not change a check grade.
- **AI disclosure**: if the repo states an AI policy, quote it and
  record whether the description discloses the tool or assistance and
  its extent in the author's own words. If the repo states no policy,
  record that fact and pass the check without inferring a rule.

## Check execution

Run the checks in the order they appear in `rubric.md`:

1. `plan-fidelity`
2. `decisive-test-evidence`
3. `reviewable-diff`
4. `template-and-policy`
5. `ai-disclosure`

For each check, apply its pass condition literally to the evidence note
already gathered. Grade the artifact, not the polish: a terse complete
PR can pass every check, and a long confident one can fail on a diff
line. Do not let one failed check contaminate another; a plan that
drifts can still have decisive evidence, and a clean diff can still be
untested.

If evidence for a check is genuinely absent, grade the check `fail`
and say what was missing. Do not reconstruct absent test output from a
description claim, assume a repo check passed because no failure is
shown, or treat silence as compliance. If the plan explicitly defers a
subcase, apply the rubric's deferral clause before failing the evidence
or fidelity check.

In live mode, after the five checks, hold the draft title and
description against `voice-guide.md` and write one note per broken
rule, quoting the rule. Report those notes in the summary. They do not
change the verdict unless a rubric check reads the voice guide, and
none does.

## Verdict assembly

Apply the rubric's verdict rule: `accept` only if every required check
passes; any `fail` or `unclear` is `reject`. There is no third verdict
and no score. When more than one required check fails, quote the first
failing required check in rubric order in the readable summary; the
JSON still reports every check in rubric order.

Write the JSON block with the exact check names and one entry per
check. End the reply with that fenced JSON block and nothing after it.
