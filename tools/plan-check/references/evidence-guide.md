# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

Where it lives: in the candidate plan's diagnosis section, and in the repro-evidence block's observed artifact, expected-vs-actual line, and control runs. In live mode, the same two places: the plan draft's diagnosis, and the student's posted repro comment.

What good looks like: the stated cause explains the exact artifact the repro shows (same command, same failure, same exit code or output), and every control in the repro block is consistent with it. A cause that needs a control to be ignored is not grounded.

## Scope

Where it lives: the plan's scope statement, its in-scope/not-in-scope lines, and every change listed under approach or files. In live mode, the plan draft and any "not doing" lines in the plan comment.

What good looks like: each proposed change is necessary to fix the reproduced behavior, or is explicitly deferred with a reason. A plan that adds a migration, redesign, new option, UI feature, retry framework, or drive-by refactor the issue did not ask for is not bounded.

## Executability

Where it lives: the plan's files/areas section and approach steps. In live mode, the same sections of the plan draft.

What good looks like: at least one concrete file, function, module, or code area is named, and the mechanism is chosen well enough that a stranger could start. "Profile and optimize", "recover() somewhere", or "upstream or vendored, whichever is easier" is not a starting point.

## Test plan

Where it lives: the plan's test plan section, read against the repro-evidence block's steps and artifacts. In live mode, the plan draft's test plan and the repro comment's commands.

What good looks like: the repro command or steps are re-run, and the plan names an observable outcome that would differ before and after - an exact output, exit code, state, test assertion, or control comparison. "Run the full suite" or "should feel fast" is not decisive.

## Honesty

Where it lives: the plan's risks, open questions, and deferrals sections; after a build, the `## Deviations` heading in plan.md. In live mode, the same sections of the plan draft.

What good looks like: at least one material risk or open question is named, or the plan says none were identified; deferrals say why. Confident language that hides a known unknown is not honest.

## Comms

Where it lives: the candidate plan comment, read against the thread highlights and the repo-facts block (bug-report template asks, contribution policy, AI policy, maintainer direction). In live mode, the draft comment, the live issue thread, and the repo's docs.

The candidate plan and comment are AI-assisted work by construction; apply any AI-use disclosure requirement to the comment even if the comment never mentions AI. What good looks like: the comment answers explicit maintainer direction when it exists (a named culprit, a settled option, an open PR, a requested test) and follows the repo's stated conventions, including AI-use disclosure when the policy requires it. Boilerplate that ignores the thread, or a comment that omits a required disclosure, is not thread-aware.
