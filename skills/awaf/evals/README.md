# AWAF skill eval cases

`evals.json` holds the scenario cases that specify correct AWAF report behavior:
each case has a prompt, an `expected_output` summary, and a list of `expectations`
(natural-language assertions about the report).

These cases are graded by the awaf-cli grader, not run from this repo. From a
checkout of `awaf-cli` next to this repo:

```bash
cd ../awaf-cli
uv run awaf eval-skill --skill-dir ../awaf-skill
```

The grader runs each case through the configured provider (system prompt =
`SKILL.md` + `references/output-format.md`, user = the case prompt), applies
deterministic report-shape checks, then judges every expectation with a stronger
model. It writes a metrics JSON and exits non-zero below the pass-rate gate. CI
runs it nightly (`awaf-cli/.github/workflows/eval-grader.yml`).
