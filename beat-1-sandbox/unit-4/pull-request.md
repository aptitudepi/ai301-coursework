# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request I opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/264

**Branch**

`fix/55-skill-extractor-signals`

**Live precheck**

The same installed skill was run in live mode on the pre-open draft (`plan.md`, `pr_draft.md`, `test_evidence.md`, and the committed diff). Verdict: `accept`; all five required checks passed (`plan-fidelity`, `decisive-test-evidence`, `reviewable-diff`, `template-and-policy`, `ai-disclosure`). After the PR opened, CI passed all five jobs (run 39714056646).

## Eval iterations

**Run history**

I ran the harness in this order. Targeted runs used `--only`; the final run was a full run with `--save-run`.

1. `pkg-01` smoke: 1/1. `pkg-01` graded reject; gold reject (`standards-wall`).
2. First targeted batch (`pkg-01`, `pkg-02`, `pkg-03`, `pkg-04`, `pkg-07`, `pkg-11`, `pkg-12`, `pkg-20`): 8/8.
3. Second targeted batch: 8/12. The four misses were all clear accepts (`pkg-05`, `pkg-08`, `pkg-16`, `pkg-19`); the rubric was over-strict on secondary evidence, line placement, pre-existing helpers, and excerpt hunk counts.
4. Post-rewrite canaries (`pkg-01`, `pkg-02`, `pkg-03`, `pkg-05`): 4/4.
5. Final full run: `agreement: 20/20 scored items  (bar: 18/20: PASS)`; `categories: clear-accept 7/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3`. This is the run committed in `eval-run.txt`.

The last score matches `eval-run.txt`'s agreement line. The harness header says `# model: sonnet (pinned)`; the pinned `sonnet` slot was served by Claude Sonnet 4.6 through the OpenCode `chat.tamu.ai` provider because the Claude Code account had hit its individual spend limit. The harness, skill files, and gold labels were unchanged; only the backend serving the pinned model label differed. The final run was produced by the harness with `--save-run`, so `eval-run.txt` is the harness's own record.

**Package analysis**

`pkg-01` (pandas-dev/pandas#66657) is a standards-wall. The repo facts quote the pandas PR template as a required checklist: "closes #xxxx with the issue number, tests added and passed, all code checks passed (pre-commit), type annotations added to new arguments, and an entry in the latest doc/source/whatsnew/vX.X.X.rst when fixing a bug." The candidate's description is one paragraph with no `Closes #66657` and no checklist, and the diff changes only `pandas/_libs/src/parser/tokenizer.c` and the parser test — no `doc/source/whatsnew` entry. My `template-and-policy` check says "Fail for a required section absent or boilerplate, a required closing link missing, a required artifact absent from the diff, or an ignored checklist item." My rubric decided reject. The gold label is reject, category `standards-wall`. The package is not a test-evidence problem — its before/after repro and ASAN evidence are strong — it fails because the repo's required PR shape and release-note artifact are missing.

**Check rationale**

Quoted from `tools/pr-precheck/rubric.md`, the `plan-fidelity` check:

> Judge scope and behavior, not the exact line placement of a named change: a plan that names a function and a mechanism is satisfied by an equivalent implementation at the nearest sensible point in that function. A plan's statement that an untouched caller keeps its behavior is not a missing hunk; fail only when a changed file or behavior exceeds the boundary.

It reads that way because the first version treated a plan's named line placement and its untouched-caller notes as diff deliverables. That rejected clear accepts in the second batch — `pkg-05`, `pkg-08`, `pkg-16`, and `pkg-19` (all gold accept) — because the behavior was in the diff but the plan's line-level description did not match the hunk placement, a pre-existing helper was outside the excerpt, or the excerpt's hunk counts did not add up. I revised the check to judge the stated scope and behavior, and to re-tie a real mismatch only with a deviation note that names the missing work and why. A true scope drift, unplanned file, or contradictory description claim still fails (a)-(c). I re-ran `pkg-01`, `pkg-02`, `pkg-03`, and `pkg-05` after the revision; all four matched gold.

**Trade-offs**

The `decisive-test-evidence` revision lets a plan's secondary check be covered by a new test assertion plus the named passing suite, as long as the primary issue reproduction has a before/after. `pkg-05` is the package where that trade-off shows: it is a clear accept (gold accept), its issue repro shows both `atuin` rows before and after, and its secondary "same-name same-key prints a warning" behavior is asserted by the new `same_name_same_key_replaces_with_warning` test plus `cargo test -p nu-protocol` (312 tests); the raw warning output is not pasted. Before the revision, my rubric wanted a separate before/after for that secondary warning and rejected it. The cost is that a secondary plan behavior can pass without its own observable transcript, so a warning that is asserted but never actually printed could slip through if the test is weak. I accept that because the user-visible issue is the primary reproduction, the secondary behavior has a named assertion and a passing suite, and requiring a full transcript for every secondary check would fail clear accepts that the gold labels treat as ready. A primary repro with only an after state, or a missing repo check, still fails.
