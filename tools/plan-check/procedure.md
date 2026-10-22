# Procedure: how this skill grades a plan package

## Read order

1. Read the package header and issue context first: title, body, labels, state. Record one line naming the reported behavior and the expected outcome.
2. Read the thread highlights next. Record every explicit maintainer direction (named culprit, settled option, open PR, requested test) and every convention signal in the thread.
3. Read the repo-facts block. Record the bug-report template asks, contribution policy, AI policy, and any house rule that applies to comments.
4. Read the repro-evidence block before the plan. Record the environment, numbered steps, observed artifact, control runs, and the expected-vs-actual line.
5. Read the candidate plan in section order: diagnosis, scope, files/areas, approach, test plan, risks. For each section, note the sentence that will be graded.
6. Read the candidate plan comment last. Note every sentence that responds to the thread or the repo facts, and every sentence that does not.
7. Before grading, write a one-line evidence note per check in the order the checks appear in rubric.md. Do not grade from memory; quote or paraphrase the exact package text.

## Evidence gathering

1. Diagnosis: copy the plan's cause sentence. Copy the repro block's observed artifact and every control. Mark whether the cause explains the artifact and survives each control.
2. Scope: list every change the plan proposes, including verbs such as "audit", "while in the area", "also", and "consider". For each, mark necessary, deferred, or unrelated, using the issue's reported behavior as the test.
3. Executability: copy the named files, functions, modules, or areas and the chosen mechanism. If none are named, record "no concrete target".
4. Test plan: copy the commands or steps and the stated observable outcome. Map each to a repro step or artifact; mark whether the outcome would differ before and after the fix.
5. Honesty: copy the risk, open-question, and deferral sentences. If none exist, record "no risks stated".
6. Comms: copy the comment sentences that engage the thread and the repo facts. Quote the exact maintainer direction or policy sentence they must answer. Treat the plan and comment as AI-assisted work by construction when reading any AI policy.
7. If a check's evidence is not in the package, record "absent" and the part you looked in; do not infer it from other sections.

## Check execution

1. Execute the checks in this fixed order: Diagnosis follows the repro evidence; Change is bounded to the issue; Approach is executable; Test plan is decisive; Plan comment engages the thread and repo conventions; Unknowns are stated honestly.
2. For each check, apply only the pass condition in rubric.md to the evidence recorded for that check. Do not import evidence from another check.
3. Grade `pass` only when the pass condition is met on the package's own text. Grade `fail` when the condition is contradicted. Grade `unclear` only when the evidence the check names is genuinely absent from the package.
4. Do not re-read the whole package for each check. Re-read only the part a check names when its evidence note is incomplete.
5. Record the grade and a one-line evidence note for every check before moving to the next.
6. If the procedure is silent about a step, record the gap in the summary; do not invent a rule.

## Verdict assembly

1. Apply the rubric's verdict rule to the recorded grades: reject if any required check is `fail` or `unclear`; accept only if every required check is `pass`.
2. Preferred checks never change the verdict; record their grades but do not use them in the decision.
3. Name the deciding check or checks in the summary and quote the fact that decided each one.
4. Emit the JSON block with the item id, every check's name/grade/evidence, and the verdict. The JSON block must be last.
