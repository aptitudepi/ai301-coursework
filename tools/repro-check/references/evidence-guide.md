# Evidence guide: where proof lives in a reproduction package

## Environment

**Where it lives**

- In an eval bundle: look in the repro report’s environment/setup section, commands, dependency output, and repo-facts block.
- In live mode: look in the student’s repro draft for the environment record and command output; compare it with setup requirements in the repository README, contributing guide, `pyproject.toml`, `requirements*.txt`, lockfiles, or documented supported versions.
- For issue #55 specifically, the environment record should support running the Python project and the targeted skill-extractor tests.

**What good looks like**

A sufficient environment record states the operating system, the relevant runtime/tool version such as Python, how dependencies were installed or the environment was created, and the repository revision, branch, or commit used. The stated versions either match the issue’s target environment or clearly identify any difference. A setup failure is useful evidence only when the exact command and error are recorded; it is not automatically evidence that the reported issue reproduced.

## Steps

**Where it lives**

- In an eval bundle: look in the repro report’s numbered commands/actions, fixture or input descriptions, and the issue context that describes the trigger.
- In live mode: look in the student’s draft repro comment, the issue body and comments, and repository documentation that defines setup, test commands, or reproduction instructions.
- Commands, file paths, test names, copied inputs, and UI actions are all steps evidence.

**What good looks like**

A stranger can begin from the recorded environment and follow the stated actions without inventing an input, file, command flag, prerequisite, or expected starting state. The sequence identifies the trigger that matters to the issue and reaches the displayed result. A command such as `pytest tests/unit/test_skill_extractor.py` is followable only when the report also says how the project dependencies/environment were prepared and identifies the targeted failing test or input when needed.

## Behavior shown

**Where it lives**

- In an eval bundle: compare the issue context with the repro report’s exact terminal output, failed assertion, stack trace, screenshot, log excerpt, generated file, or before/after result.
- In live mode: compare the issue body and comments with the artifacts pasted or linked in the student’s repro draft. For code issues, inspect the test name, test output, error message, input, and actual result.
- Do not use a conclusion sentence by itself as behavior evidence; use the artifact that the conclusion cites.

**What good looks like**

The artifact demonstrates the same behavior the issue describes: the same failed test, missing extraction, wrong output, crash, or user-visible result under the relevant trigger. A dependency-installation error, unrelated traceback, timeout, or a different test failure does not show the issue behavior unless the issue itself describes that exact failure. For issue #55, good evidence connects a named failing detection test or sample input with JavaScript/TypeScript not being detected as the issue reports.

## Honesty

**Where it lives**

- In an eval bundle: read the claim comment and the repro report’s final outcome/conclusion alongside the commands and artifacts earlier in the report.
- In live mode: read the student’s draft claim or repro comment alongside its stated commands, environment record, output excerpts, and the issue’s requested/observed behavior.
- Compare verbs such as “reproduced,” “confirmed,” “fixed,” “cannot reproduce,” and “blocked” against the available proof.

**What good looks like**

A claim comment promises investigation, setup, reproduction, and a report; it does not promise a fix, merge, deadline, or successful reproduction before evidence exists. A repro report says “reproduced” only when its artifacts demonstrate the issue behavior, and it says “could not reproduce” or “blocked during setup” when that is what the documented attempt actually showed. An evidenced cannot-reproduce result is good proof when it includes the environment, steps attempted, and observed outcome.

## Comms

**Where it lives**

- In an eval bundle: compare the candidate claim and repro comments with the issue context and the repo-facts block’s contribution-policy, template, disclosure, and communication requirements.
- In live mode: compare the student’s draft comments with the issue thread, issue template, contribution guide, code-of-conduct/contribution policy, and any explicit AI-use or AI-disclosure policy in repository documentation.
- Also read the draft against the student’s installed `voice-guide.md`; this applies in live mode only.

**What good looks like**

A claim names the issue-specific behavior or component and states the next investigation/reproduction step. A repro comment reports the contributor’s own evidence rather than saying “same as above.” If the repository explicitly requires AI-use disclosure, the relevant comment includes the required disclosure; if the repository bans AI-generated contributions, the package should not be presented as ready for that workflow. Concrete wording that names the test, input, file, command, or observed result is stronger than generic text such as “I am working on it” or “confirmed.”
