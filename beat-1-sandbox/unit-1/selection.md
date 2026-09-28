# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/55

**Verdict output**

```text
Ranked read-out

Accepted

#55 — Skill extractor fails to detect JavaScript and TypeScript — the only candidate with a clean claim surface: zero comments, no assignee, and no open PR anywhere in the repo targets it. The issue names exact failing tests and the related helpers in `ingestion/parsers/skill_extractor.py`, making it a single-component reproduce-then-fix debugging task with a defined expected outcome.

Rejected

- #54 — Resume section detection fails on leading whitespace — rejected because PR #76 (“Closes #54”) is open.
- #56 — Structural chunker silently drops heading-less documents — rejected because PR #81 (“Fixes #56”) is open.

```json
[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/55",
    "checks": [
      {
        "name": "Maintainer activity",
        "grade": "pass",
        "evidence": "Last main commit 2026-09-16, 11 days before today — within 180 days."
      },
      {
        "name": "Repository use",
        "grade": "pass",
        "evidence": "71 open issues and 9 open pull requests; repo not archived."
      },
      {
        "name": "AI contribution policy",
        "grade": "pass",
        "evidence": "Repository documentation contains no restriction on AI-generated contributions."
      },
      {
        "name": "Active claim or contested thread",
        "grade": "pass",
        "evidence": "Zero comments, no assignees, no linked pull requests, and none of the open PRs references #55."
      },
      {
        "name": "Actionable bug or feature",
        "grade": "pass",
        "evidence": "The issue is labeled bug and names concrete failing tests in tests/unit/test_skill_extractor.py."
      },
      {
        "name": "Bounded newcomer scope",
        "grade": "pass",
        "evidence": "The work is scoped to language/tool/database detection helpers in ingestion/parsers/skill_extractor.py."
      },
      {
        "name": "First-timer friendliness",
        "grade": "pass",
        "evidence": "The issue is labeled tier-1 starter difficulty."
      }
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/54",
    "checks": [
      {
        "name": "Active claim or contested thread",
        "grade": "fail",
        "evidence": "PR #76, 'fix: detect indented resume section headings,' is open and its body says 'Closes #54'."
      }
    ],
    "verdict": "reject"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/56",
    "checks": [
      {
        "name": "Active claim or contested thread",
        "grade": "fail",
        "evidence": "PR #81, 'fix: structural chunker keeps heading-less documents as one chunk,' is open and its body says 'Fixes #56'."
      }
    ],
    "verdict": "reject"
  }
]
```

***

## Eval iterations

**Run history**

1. My first evaluator attempt produced no valid score because the Claude CLI exited with code 1 for all 20 bundles. I fixed the local CLI/authentication problem before attempting rubric calibration.
2. My first successful complete run scored `12/20`. The category summary showed `claimed 2/4`, `clear-accept 4/8`, `dead-repo 3/3`, `policy 0/1`, and `scope 3/4`. This showed that the initial rubric handled inactive repositories but missed claimed issues and AI-policy restrictions.
3. My second successful complete run scored `16/20`. The categories were `claimed 4/4`, `clear-accept 6/8`, `dead-repo 3/3`, `policy 1/1`, and `scope 2/4`. I had added claim and policy checks, but needed to refine scope and distinguish clean closed PR history from active work.
4. My third complete run scored `19/20`, but the category floor was still unmet because `policy 0/1`. The remaining disagreement was `issue-12`, which I incorrectly accepted even though its repository explicitly rejected AI-generated contributions.
5. My final complete run scored `20/20`. The final harness result in `eval-run.txt` reports: `categories: claimed 4/4  clear-accept 8/8  dead-repo 3/3  policy 1/1  scope 4/4` and `agreement: 20/20 scored items  (bar: 18/20: PASS)`.

**Issue analysis**

I analyzed `issue-12`.

- My final rubric decision: `reject`.
- Gold label: `reject`.
- Reasoning: The repo-facts block for issue-12 states: `"Meaningful human interaction is the whole point of BookWyrm. We do not accept AI-generated code or documentation."` My earlier rubric lacked an explicit AI contribution-policy check, so it accepted this issue and missed the only policy category case. I added a required policy check that reads the repository's stated contribution policy and rejects a package when the repository explicitly bans or rejects AI-generated contributions. With that evidence-based rule, issue-12 correctly became `reject`, matching the gold label.

**Check rationale**

Current rubric check:

> `| AI contribution policy | In the repo-facts block, look specifically at the \`contribution policy\` line. Check whether the repository explicitly bans, rejects, or prohibits AI-generated code or contributions. | Pass if the repository has no contribution policy, or explicitly allows/welcomes AI assistance (even with standard requirements to test, review, and understand changes). Fail if the repository explicitly bans or rejects AI-generated contributions (for example: "We do not accept AI-generated code or documentation", as seen in BookWyrm). | required |`

I wrote this check to rely on a specific evidence source—the repo-facts contribution-policy line—rather than guessing whether maintainers would accept a contribution. An explicit ban is a concrete blocker for an AI-assisted course workflow, while no policy or an allowed-with-review policy does not block a newcomer. Making the condition required prevents a technically manageable issue from being accepted when a repository's rules would reject the contribution for policy reasons.

**Trade-offs**

The AI contribution-policy check can reject an otherwise small and technically approachable issue even if I could complete the change entirely by hand. It also treats an explicit AI ban as a hard boundary even when a repository might make exceptions in practice. I accept this trade-off because the assignment is about selecting an issue that is viable for the actual course workflow, and a written upstream policy is stronger evidence than speculation about possible exceptions.

***

## Selection rationale

**1. The issue's fit to my interests and the time available**

Issue #55 fits my interest in debugging Python-based extraction logic and building reliable developer tools. It has concrete failing tests—including JavaScript and TypeScript detection cases—and points to a small set of helpers in `ingestion/parsers/skill_extractor.py`. That makes the work bounded enough to set up, reproduce, and investigate within the Unit 2 time window without requiring a broad cross-repository change.

**2. What the verdict identified correctly, and what I weighed beyond the rubric**

The verdict correctly identified that #55 is active, in an active repository, has a clean claim surface, and has a specific bug with named tests and a likely component. It also correctly rejected #54 and #56 because each had an open linked pull request. Beyond the rubric, I weighed how helpful it would be to have pre-existing focused tests and a single Python file to inspect. Those details make #55 more approachable for learning the codebase and producing an independent reproduction report.

**3. The anticipated difficulty in claiming it**

I expect little difficulty posting the Unit 2 claim because #55 had no existing comments, assignee, or linked pull request when I evaluated it. The main challenge will be setting up the project environment and confirming the exact failing test behavior before I make any claim about a root cause or fix. Under the Path Review classroom rules, I will still post my own evidence-based claim and reproduction report rather than relying on someone else's work.
