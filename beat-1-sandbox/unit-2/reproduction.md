# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

aptitudepi

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/55#issuecomment-5868786426

Hi, I’m a sophomore CS undergrad with prior open-source documentation experience and an AI/ML research background.

I’d like to investigate #55, where the skill extractor misses JavaScript and TypeScript in `_detect_languages` in `ingestion/parsers/skill_extractor.py`. I will set up the documented Python environment, run `pytest tests/unit/test_skill_extractor.py -q` with attention to `test_javascript_detection` and `test_text_with_typescript_files`, and inspect the relevant language-detection helpers.

I’ll report back with my environment, commands, and observed output—whether I reproduce the issue or cannot reproduce it with evidence. I am not drawing a root-cause conclusion until I have test results.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/55#issuecomment-5870199507

# Repro for #55 — skill extractor misses JS/TS and related signals

## Environment

- Machine: `Linux slim 7.2.0-1-cachyos #1 SMP PREEMPT_DYNAMIC Thu, 20 Aug 2026 16:18:38 +0000 x86_64 GNU/Linux` from `uname -a`
- System Python: `Python 3.14.7` from `python3 --version`; venv Python matches at `Python 3.14.7` from `.venv/bin/python --version`
- Test runner: `pytest 9.1.1` from `.venv/bin/pytest --version`
- Code location: `/home/db/repos/pathreview-ai301-fa26-s3`, branch `main`
- Commit: short `2f4e82f`, full `2f4e82f52efbcfcc57d65b3fa5348672163ca088` from `git rev-parse HEAD`
- Working tree: untracked `repro-notes/` only; source and tests untouched
- Fresh setup used the documented commands:
  ```
  python3 -m venv .venv
  source .venv/bin/activate
  python -m pip install -e ".[dev]"
  ```
- Evidence files refreshed in the workdir: `repro-notes/environment.txt`, `repro-notes/normal-test-run.txt`, `repro-notes/runxfail-test-run.txt`

## Steps

1. Opened the workdir `/home/db/repos/pathreview-ai301-fa26-s3` and enabled the existing venv with `source .venv/bin/activate`.
2. Confirmed baseline without changing code:
   ```
   pytest tests/unit/test_skill_extractor.py -q 2>&1 | tee repro-notes/normal-test-run.txt
   ```
   This shows the strict-xfail tests as `x` / `xfailed` rather than running their bodies.
3. Forced the xfail-marked checks to execute:
   ```
   pytest tests/unit/test_skill_extractor.py -v --runxfail 2>&1 | tee repro-notes/runxfail-test-run.txt
   ```
   Reason for the flag: the five checks carry `@pytest.mark.xfail(strict=True)`. Without `--runxfail` pytest reports them as expected skips. With `--runxfail` pytest runs the assertion bodies, so genuine detection gaps surface as `FAILED` instead of `XFAIL`.

## Observed

Baseline run:

```
..x....xx..xx.....                                                       [100%]
13 passed, 5 xfailed in 0.14s
```

Forced run:

```
========================= 5 failed, 13 passed in 0.12s =========================
```

The five failures, in file order:

- `tests/unit/test_skill_extractor.py::TestSkillExtractor::test_text_with_typescript_files`
- `tests/unit/test_skill_extractor.py::TestSkillExtractor::test_database_technology_detection`
- `tests/unit/test_skill_extractor.py::TestSkillExtractor::test_devops_tool_detection`
- `tests/unit/test_skill_extractor.py::TestSkillExtractor::test_javascript_detection`
- `tests/unit/test_skill_extractor.py::TestSkillExtractor::test_docker_compose_detection`

Assertion excerpts copied from my `--runxfail` output:

TypeScript case (`test_text_with_typescript_files`):

```
skill_names = [s.name for s in result]
assert any("typescript" in s.lower() for s in skill_names)
E       assert False
```

JavaScript case (`test_javascript_detection`):

```
assert any("javascript" in s.lower() or "js" in s.lower() for s in skill_names)
E       assert False
```

DevOps/Docker case (`test_devops_tool_detection`):

```
assert any("docker" in s.lower() for s in skill_names)
E       assert False
```

Database case (`test_database_technology_detection`):

```
assert any("postgres" in s.lower() or "sql" in s.lower() for s in skill_names)
E       assert False
```

Docker Compose case (`test_docker_compose_detection`) fails on the Docker assertion with this output:

```
assert any("docker" in s.lower() for s in skill_names)
E       assert False
```

No source or test files were edited. No post, commit, or push was made.

## Expected

Following #55, the extractor should pick up JavaScript/TypeScript inputs plus the related technology signals exercised by these five tests (TypeScript interface/class text, `require('fs')` JavaScript text, Dockerfile text, Compose YAML text, and `psycopg2` database text). After a fix those checks should pass without needing xfail markers.

## Conclusion

I reproduced the behavior described in #55...

No fix has been applied yet; this report only documents the failing assertions above.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

In order:

1. Initial full run: 18/20 agreement — disclosure floor 0/1 unmet (all other floors met).
2. Two rubric revisions targeting the disclosure gap, each re-checked with canary `--only pkg-10,pkg-20,pkg-07,pkg-14` — all four canary packages matched gold after the revisions.
3. Final full run: 20/20 agreement, all floors met — clear-accept 8/8, disclosure 1/1, no-evidence 4/4, unfollowable-comms 3/3, wrong-target 4/4, bar 18/20: PASS. This matches the agreement line in the committed `eval-run.txt` (`agreement: 20/20 scored items  (bar: 18/20: PASS)`).

**Package analysis**

pkg-20: my rubric initially returned accept while the gold label was reject. The repo-facts block required AI tool-and-extent disclosure, but neither the claim comment nor the repro report contained that disclosure, so the package omitted a mandatory convention. My initial check passed it; the revised `Repository conventions respected` check correctly rejected it, and the final full run records pkg-20 as reject/reject (agree).

**Check rationale**

Final `rubric.md` row, quoted exactly:

| Repository conventions respected | In the repo-facts block, contribution policy, issue template, and candidate claim/repro comments, inspect explicit requirements for AI-assistance disclosure, including whether the policy requires naming the AI tool and the extent of assistance. | Pass if no explicit policy requires AI-use disclosure, or if every applicable mandatory convention is followed. If a policy explicitly requires disclosure of AI assistance with the tool used and extent of use, pass only when the claim comment or repro report includes that required disclosure. A policy that only requires contributors to understand, review, test, or take responsibility for AI-assisted work does not require a tool-and-extent disclosure unless it explicitly says so. Fail if a repository explicitly mandates tool-and-extent AI disclosure and neither candidate comment contains it, or if another explicit applicable convention is contradicted. In a claim-only draft, evaluate only conventions applicable to the claim and report repro-only conventions as `unclear` with `not yet applicable: claim-only draft`. | required |

This wording distinguishes an explicit tool-and-extent mandate from generic review/test responsibility: a policy that only requires contributors to understand, review, test, or take responsibility for AI-assisted work does not trigger a disclosure failure, while a repository that explicitly mandates naming the AI tool and the extent of assistance fails when neither candidate comment contains it. I revised toward this explicit-mandate reading (two revisions, verified on canary pkg-10, pkg-20, pkg-07, pkg-14) and rejected broader readings that would have failed packages on generic responsibility language.

**Trade-offs**

The strict check can reject an otherwise excellent report with no AI use or with incomplete voluntary disclosure (no tool named, or tool named without extent), even when the technical reproduction is perfect. I accept that cost because it prevents violating explicit upstream conditions: when a repo mandates tool-and-extent disclosure, posting without it risks rejection or policy violation, and the final 20/20 run shows nothing else changed (clear-accept 8/8, no-evidence 4/4, unfollowable-comms 3/3, wrong-target 4/4 all still met).

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
