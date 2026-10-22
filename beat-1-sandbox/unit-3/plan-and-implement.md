# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

aptitudepi

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/55#issuecomment-6330190269

I reproduced this in week 2 on `main` at `2f4e82f` (Linux, Python 3.14.7 venv, pytest 9.1.1). The baseline is `13 passed, 5 xfailed`; with `--runxfail` it is `5 failed, 13 passed` for the five tests the issue names. My commands and assertion excerpts are in my repro comment: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/55#issuecomment-5870199507

ArshAnan's repro and root-cause note match what I saw, and PR #229 is open for the same three helpers. I am not treating #229 as settled or as a substitute for my own repro; my plan is narrower, and I will compare against it before opening my PR so we do not conflict.

From reading `skill_extractor.py`, the common cause is that each helper gates on a literal name. `_detect_languages` only matches `import`/`require` followed by whitespace, so `require('fs')` and text-only TypeScript (`export interface`, `: string`, `Promise<User>`) produce no language entry. `_detect_tools` only adds Docker when the word "docker" appears, so Dockerfile (`FROM`/`RUN`/`EXPOSE`) and Compose (`services:`/`build:`) text are missed. `_detect_databases` only adds PostgreSQL when "postgresql" appears, so `import psycopg2` is missed.

Plan:
- In `_detect_languages`, accept `require(` with no space, count `const`/`let`/`var` declarations as JavaScript evidence, and add TypeScript signals for `interface Name {`, primitive type annotations, and `Promise<`. Label the entry TypeScript when those match or the filename has `.ts`, otherwise JavaScript.
- In `_detect_databases`, map the `psycopg2` driver to PostgreSQL. Other drivers are out of scope.
- In `_detect_tools`, require two signals: a Dockerfile `FROM` plus another instruction, or Compose `services:` plus `image:`/`build:`. A lone `FROM` or a SQL query should not count.
- Remove the five `@pytest.mark.xfail` markers, per `docs/CONTRIBUTING.md`; the test bodies stay unchanged.

The change is limited to `ingestion/parsers/skill_extractor.py` and those five markers. I am not adding new tests, other database drivers, or refactoring the extractor. Verification after the change will be `pytest tests/unit/test_skill_extractor.py -q` (expect `18 passed`), then `make test-unit`, `make lint`, and `make typecheck`; I will report the before/after output and flag any deviation, including overlap with #229.

---

## Your branch

**Branch**

fix/55-skill-extractor-signals

**Evidence**

Before, on `main` at `2f4e82f`:

```
$ .venv/bin/pytest tests/unit/test_skill_extractor.py -q
..x....xx..xx.....                                                       [100%]
13 passed, 5 xfailed in 0.16s

$ .venv/bin/pytest tests/unit/test_skill_extractor.py -v --runxfail
...
FAILED tests/unit/test_skill_extractor.py::TestSkillExtractor::test_text_with_typescript_files
FAILED tests/unit/test_skill_extractor.py::TestSkillExtractor::test_database_technology_detection
FAILED tests/unit/test_skill_extractor.py::TestSkillExtractor::test_devops_tool_detection
FAILED tests/unit/test_skill_extractor.py::TestSkillExtractor::test_javascript_detection
FAILED tests/unit/test_skill_extractor.py::TestSkillExtractor::test_docker_compose_detection
5 failed, 13 passed in 0.20s
```

After, on `fix/55-skill-extractor-signals` at `3af189f`:

```
$ .venv/bin/pytest tests/unit/test_skill_extractor.py -q
..................                                                       [100%]
18 passed in 0.11s

$ .venv/bin/pytest tests/unit/test_skill_extractor.py -v --runxfail
...
PASSED tests/unit/test_skill_extractor.py::TestSkillExtractor::test_text_with_typescript_files
PASSED tests/unit/test_skill_extractor.py::TestSkillExtractor::test_database_technology_detection
PASSED tests/unit/test_skill_extractor.py::TestSkillExtractor::test_devops_tool_detection
PASSED tests/unit/test_skill_extractor.py::TestSkillExtractor::test_javascript_detection
PASSED tests/unit/test_skill_extractor.py::TestSkillExtractor::test_docker_compose_detection
...
18 passed in 0.10s
```

Other planned checks:

```
$ make test-unit
380 passed, 48 xfailed, 1 warning in 7.62s

$ make lint
All checks passed!

$ .venv/bin/black --check .
All done! ✨ 🍰 ✨
110 files would be left unchanged.

$ make typecheck
.venv/lib/python3.14/site-packages/numpy/__init__.pyi:737: error: Type statement is only supported in Python 3.12 and greater  [syntax]
Found 1 error in 1 file (errors prevented further checking)
```

The bare `make typecheck` failure reproduces on `main` before any branch code is checked; it is the installed numpy stub against mypy's default target, not this diff. The changed module and the whole tree check clean when the stub is skipped or the target is 3.12:

```
$ .venv/bin/mypy --follow-imports=skip --ignore-missing-imports ingestion/parsers/skill_extractor.py
Success: no issues found in 1 source file

$ .venv/bin/mypy --python-version 3.12 api/ core/ ingestion/ rag/ agent/ safety/
Success: no issues found in 76 source files
```

---

## Eval iterations

**Run history**

1. Smoke run, six packages (`pkg-01`, `pkg-02`, `pkg-04`, `pkg-06`, `pkg-10`, `pkg-20`): **6/6** agreement.
2. Full run, twenty packages: **19/20** agreement. The miss was `pkg-20` (thread-convention): my rubric accepted it; the gold label rejected it.
3. Canary run with `--only pkg-03,pkg-04,pkg-05,pkg-09,pkg-20`: **5/5** agreement, re-checking the AI-policy accept cases plus `pkg-20` after the rubric change.
4. Full run, twenty packages: **20/20** agreement. This is the run in `eval-run.txt`: `agreement: 20/20 scored items  (bar: 18/20: PASS)`.

**Package analysis**

`pkg-20` (ghostty-org/ghostty#11261). The gold label is **reject** in `thread-convention`, with the note: "excellent bounded plan that follows the thread's direction, but the comment contains no AI-use disclosure and ghostty's stated policy requires disclosing all AI usage; every package here is treated as AI-assisted work". My first full run accepted it, which is why that run scored 19/20 and why thread-convention was 1/2. My rubric read the missing disclosure as optional because the candidate comment never mentioned AI use. The issue thread, however, states a policy that requires disclosing all AI usage in comments, and the package is AI-assisted by construction. The rubric now says: "Treat the candidate plan and plan comment as AI-assisted work by construction: every package is a candidate output of an AI-assisted planning task, so an AI-use disclosure requirement applies to the comment even when the comment never mentions AI." The re-run rejected `pkg-20`, matching the gold label.

**Check rationale**

The check, exactly as it reads in `rubric.md`:

```
| Plan comment engages the thread and repo conventions | The candidate plan comment read against the thread highlights and the repo-facts block (bug-report template asks, contribution policy, AI policy, maintainer direction). | Treat the candidate plan and plan comment as AI-assisted work by construction: every package is a candidate output of an AI-assisted planning task, so an AI-use disclosure requirement applies to the comment even when the comment never mentions AI. Pass if the comment engages explicit maintainer direction or prior work in the thread (a named culprit, a settled option, an open PR, a requested test) when such direction exists, or the thread has none and the comment does not contradict it; and if the comment follows every applicable stated convention, including AI-use disclosure when the policy requires naming the tool and extent. A policy that requires disclosure only in the pull request, or only human-authored comment wording, does not fail a plan comment that meets that stated scope. Fail if the comment ignores explicit maintainer direction, or omits a disclosure the repo's stated policy requires in issue comments. | required |
```

It reads that way because the first full run exposed a false accept on `pkg-20`: the check only failed an omitted AI disclosure when the candidate comment mentioned AI, so a comment that said nothing about AI slipped through even though the package's policy requires disclosing all AI usage. The revised check states the by-construction premise and keeps the scope limit: a policy that requires disclosure only in the pull request does not fail an issue comment. After the revision, the canary was 5/5 and the full run was 20/20.

**Trade-offs**

The by-construction premise changes `pkg-20` from accept to reject. I re-ran the canary with `--only pkg-03,pkg-04,pkg-05,pkg-09,pkg-20` and got 5/5: the other four packages kept their gold-matching verdicts, while `pkg-20` moved to reject for the right reason. The check still accepts a plan comment under a policy that requires AI disclosure only in the pull request, or only in human-authored wording; that is deliberate, because the rubric follows the policy's stated scope rather than inferring a broader one. A package whose issue thread has no AI policy at all is unaffected.
