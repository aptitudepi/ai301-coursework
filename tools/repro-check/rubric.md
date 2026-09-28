# Rubric: is this reproduction package ready to post?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever
checks you define here. It ships empty on purpose: the judgment is your
work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the claim comment, the repro report's environment
     record, the artifacts read against the issue's description, the
     repo-facts block) or a location from your
     references/evidence-guide.md. "The report" is not a source; "the
     output excerpt read against the error the issue describes" is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself (does
     the artifact show the issue's behavior?), never the write-up's
     shape (how many steps it has, how long it is, whether it uses a
     template's headings). Structure-shaped checks are what make
     graders disagree with themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (ready) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. If you write no rule for
   `unclear`, the skill treats it as fail.

Cover what actually gets bad packages posted. The lecture named the
proof families: the environment is recorded, the steps are complete
and followable, the behavior shown matches the issue (not an adjacent
one), the outcome is stated honestly (an evidenced cannot-reproduce is
a pass, a confident wrong-target is not), and the words respect the
repo's conventions. A rubric that ignores a family will fail eval
packages designed around that family.
-->

# Rubric: is this reproduction package ready to post?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Claim names the target | In the claim comment, compare the named issue behavior, affected component, command, test, or next investigation step against the issue title and body. | Pass if the claim identifies the issue’s specific reported behavior or affected component and promises to investigate/reproduce it. Fail if it only says the contributor will “take this,” “work on it,” or “fix it” without tying the claim to the issue’s concrete problem. In a claim-only draft, evaluate this check normally. | required |
| Claim makes an honest promise | In the claim comment, inspect verbs describing what the contributor will do next. | Pass if the claim promises investigation, environment setup, reproduction, or a report. Fail if it promises a fix, merge, completion date, or successful reproduction before evidence exists. In a claim-only draft, evaluate this check normally. | required |
| Environment recorded | In the repro report’s environment record, look for the operating system, runtime/tool version needed by the project, dependency/install state, and the repository revision or branch/commit used. | Pass if the report records enough concrete environment facts for another contributor to recreate the relevant runtime: OS, at least one relevant runtime/tool version, installation/setup state, and a repo revision, branch, or commit. Fail if the report leaves the runtime or code version unspecified. In a claim-only draft, report `unclear` with `not yet applicable: claim-only draft` and leave it out of the claim-only verdict. | required |
| Repro steps can be rerun | In the repro report’s command or step sequence, inspect the exact commands, inputs, fixture names, files, UI actions, and prerequisites. | Pass if another contributor could follow the reported commands/actions from the stated environment to reach the observed result without guessing a required input, file, or setup action. Fail if required commands, inputs, fixtures, or actions are missing or replaced by vague phrases such as “run the app” or “test it.” In a claim-only draft, report `unclear` with `not yet applicable: claim-only draft` and leave it out of the claim-only verdict. | required |
| Evidence matches the issue | Compare the issue context with the repro report’s exact terminal output, failed assertion, stack trace, screenshot, log excerpt, generated result, or explicitly documented absence of the expected failure. For a cannot-reproduce outcome, also inspect the environment record and the reported commands/actions. | Pass if either: (1) an artifact demonstrates the same user-visible failure, incorrect output, crash, missing behavior, or failed assertion described by the issue; or (2) the report explicitly concludes that the issue could not be reproduced or was blocked, records a concrete environment, gives followable steps that target the issue trigger, shows the observed absence or blocking result, and identifies a relevant environment difference or blocking cause. Fail if the artifact only shows an unrelated setup failure, a different error, a bare assertion with no artifact, or a claimed reproduction without evidence matching the issue. In a claim-only draft, report `unclear` with `not yet applicable: claim-only draft` and leave it out of the claim-only verdict. | required |
| Outcome is evidence-based | In the repro report’s conclusion, compare the stated outcome with the recorded commands and artifacts. | Pass if the conclusion accurately states either that the issue reproduced with supporting evidence or that it could not be reproduced after the documented attempt, without overstating certainty. Fail if the report claims a reproduction without matching evidence, claims a fix without testing it, or treats an installation/setup failure as proof of the reported issue. In a claim-only draft, report `unclear` with `not yet applicable: claim-only draft` and leave it out of the claim-only verdict. | required |
| Repository conventions respected | In the repo-facts block, contribution policy, issue template, and candidate claim/repro comments, inspect explicit requirements for AI-assistance disclosure, including whether the policy requires naming the AI tool and the extent of assistance. | Pass if no explicit policy requires AI-use disclosure, or if every applicable mandatory convention is followed. If a policy explicitly requires disclosure of AI assistance with the tool used and extent of use, pass only when the claim comment or repro report includes that required disclosure. A policy that only requires contributors to understand, review, test, or take responsibility for AI-assisted work does not require a tool-and-extent disclosure unless it explicitly says so. Fail if a repository explicitly mandates tool-and-extent AI disclosure and neither candidate comment contains it, or if another explicit applicable convention is contradicted. In a claim-only draft, evaluate only conventions applicable to the claim and report repro-only conventions as `unclear` with `not yet applicable: claim-only draft`. | required |
| Diagnostic value | In the repro report, inspect whether the report identifies a likely file, test, input boundary, stack trace location, or minimal failing case beyond merely repeating the issue title. | Pass if the report provides at least one useful debugging anchor, such as a named failing test, source file/function, exact input, traceback location, or minimized reproduction. Fail if it contains no debugging anchor. In a claim-only draft, report `unclear` with `not yet applicable: claim-only draft` and leave it out of the claim-only verdict. | preferred |

## Verdict rule

For a full package, accept only if every required check passes; reject if any required check fails or is unclear. Preferred checks never change the verdict.

For a claim-only draft, evaluate only the required checks whose evidence is available in the claim comment or the repository’s stated conventions. Report every repro-dependent check as `unclear` with the evidence `not yet applicable: claim-only draft`, and exclude those checks from the claim-only verdict. Accept the claim-only draft only if every applicable required check passes; otherwise reject it.
