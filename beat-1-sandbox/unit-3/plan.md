# Plan for #55: skill extractor misses JavaScript and TypeScript

- Issue: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/55
- Repo: codepath/pathreview-ai301-fa26-s3 (fork: aptitudepi/pathreview-ai301-fa26-s3)
- Base: main at 2f4e82f52efbcfcc57d65b3fa5348672163ca088
- My repro comment: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/55#issuecomment-5870199507

## Issue and repro evidence

Environment: Linux (CachyOS), Python 3.14.7 in `.venv`, pytest 9.1.1, repo at branch `main`, commit `2f4e82f`, source and tests untouched.

Commands and observed output:

```
$ pytest tests/unit/test_skill_extractor.py -q
..x....xx..xx.....                                                       [100%]
13 passed, 5 xfailed in 0.14s

$ pytest tests/unit/test_skill_extractor.py -v --runxfail
5 failed, 13 passed in 0.12s
```

The five failures are the tests the issue names, in file order:

- `test_text_with_typescript_files` — `assert any("typescript" in s.lower() for s in skill_names)` -> `assert False`
- `test_database_technology_detection` — `assert any("postgres" in s.lower() or "sql" in s.lower() ...)` -> `assert False`
- `test_devops_tool_detection` — `assert any("docker" in s.lower() ...)` -> `assert False`
- `test_javascript_detection` — `assert any("javascript" in s.lower() or "js" in s.lower() ...)` -> `assert False`
- `test_docker_compose_detection` — `assert any("docker" in s.lower() ...)` -> `assert False`

Control: the same file, same commit, without `--runxfail`, gives `13 passed, 5 xfailed`; the five bodies are not executed because of `strict=True`. The 13 other tests pass in both runs. No source or test file was changed to produce either output.

## Diagnosis

All five failures come from the same shape: each helper looks for a literal product name or a narrow text pattern, while the repro inputs express the technology through syntax or a driver name.

1. `_detect_languages` (lines 173-192). Its only text signal for JS/TS is `re.search(r"\b(import|require)\s+", text)`. `require('fs')` has no whitespace after `require`, so `test_javascript_detection` gets no language entry. The class already defines `JS_TS_KEYWORDS` (lines 30-41) with `export`, `class`, `async`, but the method never reads it. `test_text_with_typescript_files` has no `import`/`require`/`package.json` and no filename; it contains `export interface User`, `id: string`, and `Promise<User>`, so it also gets no language entry.
2. `_detect_tools` (lines 263-276). It adds Docker only when `"docker"` is a substring. The Dockerfile text has `FROM`/`RUN`/`EXPOSE`; the Compose text has `services:`/`build:`; neither contains the literal word.
3. `_detect_databases` (lines 248-261). It adds PostgreSQL only when `"postgresql"` is a substring. The test text imports `psycopg2`, which never spells "postgresql".

The observed assertions follow from these gates, and the 13 passing tests are the control: they exercise inputs that do contain the literal names or extensions, so they pass while the five syntax/driver inputs fail.

## Scope

In scope:

- `ingestion/parsers/skill_extractor.py`: `_detect_languages`, `_detect_tools`, `_detect_databases`, plus the small class-level signal tables they need.
- `tests/unit/test_skill_extractor.py`: delete the five `@pytest.mark.xfail(strict=True, reason="issue #55: ...")` decorators, as `docs/CONTRIBUTING.md` requires when a seeded bug is fixed ("removing the `@pytest.mark.xfail` line is part of fixing the issue"). The test bodies and assertions stay unchanged.

Not in scope:

- No new language, framework, database, or tool coverage beyond the five signals the tests exercise. In particular, I am not adding `asyncpg`, `mysqlclient`, other PostgreSQL drivers, Kubernetes manifests, or other Compose services.
- No new tests: the five existing tests are the regression coverage for this issue; changing their assertions would weaken the acceptance criteria.
- No refactor of the extractor's tables, dataclasses, confidence formula, pipeline, or unrelated lint/type suppressions.
- No docs, CI, frontend, or formatting-only changes.

## Files and areas

- `ingestion/parsers/skill_extractor.py`
  - `SkillExtractor._detect_languages`
  - `SkillExtractor._detect_databases`
  - `SkillExtractor._detect_tools`
- `tests/unit/test_skill_extractor.py` (marker removal only)

## Approach

1. `_detect_languages`
   - Change the JS/TS text match from `\b(import|require)\s+` to `\bimport\s+|\brequire\s*\(`, so `require('fs')` and `require("...")` match without a following space.
   - Add JavaScript declaration evidence: `re.search(r"\b(const|let|var)\s+\w+\s*=", text)` -> "JavaScript variable declarations". This covers JS described without an import, which the issue calls out.
   - Add a local `ts_evidence` list:
     - `re.search(r"\binterface\s+\w+\s*\{", text)` -> "TypeScript interface declarations"
     - `re.search(r":\s*(string|number|boolean|void|any)\b", text)` -> "TypeScript type annotations"
     - `"Promise<" in text` -> "TypeScript generic types"
   - Extend `js_evidence` with `ts_evidence`; set `lang = "TypeScript"` when `ts_evidence` is non-empty or the filename contains `.ts`, otherwise `"JavaScript"`. Keep the existing confidence formula and evidence style.
2. `_detect_databases`
   - Add a small `DATABASE_ALIASES = {"psycopg2": "postgresql"}` class attribute.
   - After the existing literal loop, for each alias found in `text_lower`, add a `PostgreSQL` `SkillDetection` if PostgreSQL is not already present, using the existing `DATABASES["postgresql"]` confidence and evidence naming the driver. Only `psycopg2` is mapped because that is the driver in the repro; other drivers are deferred.
3. `_detect_tools`
   - After the existing literal loop, collect Docker evidence:
     - Dockerfile: `^\s*FROM\s+\S+` AND one of `^\s*(RUN|CMD|EXPOSE|COPY|WORKDIR|ENTRYPOINT)\b`, both with `re.MULTILINE`. A lone `FROM` is not enough, so a SQL query does not match.
     - Compose: `^\s*services:\s*$` AND `^\s+(image|build):`, both with `re.MULTILINE`.
   - If evidence exists and Docker is not already present, add `Docker` with `TOOLS["docker"]` and the evidence list.
4. `tests/unit/test_skill_extractor.py`
   - Delete the five xfail decorator blocks. Do not change the test text or assertions.

## Test plan

Before (reproduce, on `main`):

- `pytest tests/unit/test_skill_extractor.py -q` -> `13 passed, 5 xfailed`
- `pytest tests/unit/test_skill_extractor.py -v --runxfail` -> `5 failed, 13 passed`, with the five assertions above

After (on the branch):

- `pytest tests/unit/test_skill_extractor.py -q` -> `18 passed`, with the five previously xfailed tests now passing and no xfails left in this file. This is the decisive check: the exact five tests that failed under `--runxfail` now pass without the flag or the markers.
- `make test-unit` -> no new failures; the five #55 xfails become passes. The exact count will be recorded from the run.
- `make lint` -> `All checks passed!`
- `make typecheck` -> `Success: no issues found`
- `make check` -> lint and typecheck pass (black may rewrite; I will inspect and include only intended changes)

I will save the before and after output under `repro-notes/` and paste both, with commands, into the Unit 3 evidence field.

## Risks and open questions

- The `const|let|var` declaration signal is not unique to JavaScript: C/C++ `const int x = ...` would also match. The extractor already classifies by loose text signals, and the issue asks for JS described without an import; I am accepting this trade-off to cover the issue's stated behavior. A stricter alternative is to rely only on `require(`; I am not choosing it because it leaves the "described any other way" case in the issue unfixed.
- A TypeScript file that uses only `type Foo = ...` aliases (no `interface`, primitive annotation, or `Promise<`) would still be missed. No repro test exercises that shape, so it is deferred rather than guessed at.
- `".ts" in filename` also matches `.tsx`, which labels TSX as TypeScript. That is existing behavior and is acceptable for this issue; I am not changing extension matching.
- The Docker rule requires two independent signals. A Dockerfile with only `FROM` (no `RUN`/`CMD`/`EXPOSE`/...) would still be missed; the repro Dockerfile contains `RUN` and `EXPOSE`, and the two-signal rule avoids matching a bare SQL `FROM`.
- PR #229 (https://github.com/codepath/pathreview-ai301-fa26-s3/pull/229) is open for the same issue and covers the same three helpers with a broader change (including `asyncpg`). I am not treating it as merged or as a substitute for my repro. My plan is narrower: `psycopg2` only, no `asyncpg`. Before opening my PR I will compare against #229 and call out any overlap or conflict; if it merges first, I will rebase and re-run these tests rather than duplicate the change blindly.

## Deviations

- The diff is limited to `ingestion/parsers/skill_extractor.py` and the five xfail decorators in `tests/unit/test_skill_extractor.py`. No new tests, no `asyncpg`, no other drivers, no docs or refactor. Commit `3af189f` on `fix/55-skill-extractor-signals`.
- `_detect_databases` uses the extractor's existing display convention, so the new entry is named `Postgresql` (from `db.title()`), not the plan's `PostgreSQL`. The test is case-insensitive and passes; using the existing style also avoids a duplicate entry when the text says both "postgresql" and "psycopg2".
- Targeted tests: before `13 passed, 5 xfailed` without `--runxfail` and `5 failed, 13 passed` with it; after `18 passed` in both runs. The five #55 tests are now passes. Full unit suite: `380 passed, 48 xfailed, 1 warning in 7.62s`. `make lint`: `All checks passed!`. `black --check .`: `110 files would be left unchanged`.
- Typecheck deviation: `make typecheck` fails identically on `main` and on the branch before checking repo code, with `.venv/lib/python3.14/site-packages/numpy/__init__.pyi:737: error: Type statement is only supported in Python 3.12 and greater [syntax]`. This is the installed numpy stub against mypy's default target, not the diff. The changed module passes `mypy --follow-imports=skip --ignore-missing-imports ingestion/parsers/skill_extractor.py` (`Success: no issues found in 1 source file`), and the whole tree passes `mypy --python-version 3.12 api/ core/ ingestion/ rag/ agent/ safety/` (`Success: no issues found in 76 source files`). I did not change `pyproject.toml` or the mypy target.
- `make check` was not run as-is because its `format` dependency runs `black .` over the whole repo. I ran the check-only equivalent (`make lint`, `make typecheck`, `black --check .`) instead, after formatting only the two touched files with `black`.
- No pull request was opened; the branch was pushed to the fork. The plan comment said I would compare against #229 before opening a PR, and that comparison remains pending for a future PR.
