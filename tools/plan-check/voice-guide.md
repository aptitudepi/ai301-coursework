# Voice guide: how I talk upstream

<!--
THIS IS THE PART YOU WRITE (new this week). Live mode reads this file
before any comment of yours goes out the door; eval mode ignores it
entirely, because your voice is yours and carries no gold labels.

This is not etiquette. "Be polite and concise" is advice for everyone
and therefore rules for no one. Write rules YOU need, in your own
words, each one concrete enough that the skill can hold a draft
against it and say which rule it breaks.

Three sections. Fill all three.
-->

## Who I am in threads

<!-- 2-3 lines. Who is talking when you comment on an issue: your
experience level stated plainly, what you are doing in this repo, what
readers can expect from you. This is the register your rules protect. -->

I have had prior open source contributions, only at the documentation level. Hvae built my own projects, but not professionally maintained outside of private internship repos. Sophomore in college, CS undergrad, background in AI/ML+Health research.


## Rules I write by

### Rule: Promise investigation, not a fix

In a claim comment, I say what I will investigate and report back on. I do not promise that I will fix, merge, or finish the issue before I have reproduced it and understood the cause.

- Wrong: "I’ll fix the JavaScript and TypeScript detection bug today."
- Right: "I’ll set up the project, run the relevant skill-extractor tests, and report whether I can reproduce the detection failures."

### Rule: Name the concrete behavior

I connect my comment to the issue’s actual failing behavior, test, input, or component instead of posting a generic claim that could apply to any issue.

- Wrong: "I’m taking this issue."
- Right: "I’ll investigate why the skill extractor misses JavaScript and TypeScript in the failing detection tests and report the observed results."

### Rule: Separate evidence from guesses

I clearly distinguish what I ran and observed from possible explanations. Before I have evidence, I frame possible causes as questions or next steps, not as conclusions.

- Wrong: "The language regex is definitely broken."
- Right: "I’ll first run the named tests and inspect the language-detection helpers to determine whether the failure is in the regex or another part of the extraction flow."

### Rule: Make reproduction rerunnable

When reporting a reproduction, I include the actual command, relevant environment details, and the observed output so another contributor can repeat the attempt without filling in missing steps.

- Wrong: "I ran the tests and got the same result."
- Right: "On Python 3.x in the documented project environment, I ran `pytest tests/unit/test_skill_extractor.py` and observed [exact failing test name and output]."

### Rule: Report the outcome honestly

I report whether I reproduced the issue based on the evidence, including an evidenced cannot-reproduce result. I do not turn setup failures or partial observations into proof that the issue happened.

- Wrong: "Confirmed—the issue is reproducible," when the command only failed during dependency installation.
- Right: "I could not complete the targeted test run because [specific setup failure]. I am not treating that setup failure as a reproduction of the reported detection bug."

### Rule: Plan an approach, do not promise the outcome

In a plan comment, I state the approach I will take and the evidence behind it. I do not promise that the approach will work, that the fix will merge, or a delivery date before I have built and tested it.

- Wrong: "This will fix the leak; I'll have the PR merged this week."
- Right: "I plan to ...; I'll report back with the test results and flag anything that moves during implementation."

### Rule: Answer the direction already in the thread

If a maintainer has named a culprit, settled an option, opened a PR, or asked for a test, I say how my plan follows that direction or why I am deliberately choosing a different one.

- Wrong: "I'll document a workaround" when the owner has already isolated the culprit and posted a patched binary.
- Right: "The owner's patched binary points at ...; my plan follows that direction by ..."

### Rule: State the boundary of the change

In a plan comment, I say what the change will not touch, especially when the thread or issue mentions adjacent work.

- Wrong: "I'll clean up the surrounding code while I'm there."
- Right: "The change is limited to ...; I am leaving ... out because ..."

## Things I never post

- A promise to fix, merge, finish, or deliver work by a date before I have reproduced and scoped the issue.
- A generic claim such as “I’ll take this” without naming the issue behavior I will investigate.
- “Confirmed,” “reproduced,” or “fixed” without commands and observed evidence that support the statement.
- A guess presented as a root cause or a confident technical conclusion before testing it.
- “Same as above” or copied reproduction evidence from another student; I post my own commands, environment, and observations.
- Blameful or dismissive language about the reporter, maintainers, or earlier contributors.
- A plan that ignores a maintainer's explicit direction or an open PR on the same issue.
- A plan comment that omits an AI-use disclosure the repo's policy requires.
- A promise that an untested approach will work.
