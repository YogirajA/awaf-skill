# AWAF Output Format

Read this when producing the final assessment report. The report must match the `awaf run` CLI output exactly so skill and CLI results are visually interchangeable.

Use Unicode box-drawing characters for the pillar table. Use `━` (U+2501) for separators.

**Text report plus HTML artifact.** This file specifies the in-conversation text report, which matches `awaf run` stdout. After the text report, the skill also writes a saved, self-contained HTML report to `awaf-report.html`. Its template and rules live in `references/html-report.md`.

```
   _      _  _  _    _      ___
  /_\    | || || |  /_\    | __|
 / _ \   | \/ \/ | / _ \   | _|
/_/ \_\   \_/\_/  /_/ \_\  |_       Agent Well-Architected Framework

AWAF Assessment: my-research-agent
AWAF v1.4  |  2026-03-15
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Overall Score    61/100   Needs Work
  Notable gaps. Resolve High findings before production use.

  Scale: Production Ready 85-100 · Near Ready 70-84 · Needs Work 50-69
         High Risk 25-49 · Not Ready 0-24
  Foundation <40 = automatic FAIL regardless of overall score.
  Tier 2 pillars (Reasoning, Controllability, Context Integrity) carry 1.5x weight.

┌──────────────────────┬───────┬──────────────┬────────────┬─────────┐
│ Pillar               │ Score │ Progress     │ Confidence │  Status │
╞══════════════════════╪═══════╪══════════════╪════════════╪═════════╡
│ TIER 0 -- FOUNDATION                                               │
├──────────────────────┼───────┼──────────────┼────────────┼─────────┤
│ Foundation           │    72 │ [#######   ] │ partial    │    PASS │
╞══════════════════════╪═══════╪══════════════╪════════════╪═════════╡
│ TIER 1 -- CLOUD WAF ADAPTED                                        │
├──────────────────────┼───────┼──────────────┼────────────┼─────────┤
│ Op. Excellence       │    55 │ [######    ] │ partial    │         │
│ Security             │    80 │ [########  ] │ verified   │         │
│ Reliability          │    60 │ [######    ] │ self-rep.  │         │
│ Performance          │    70 │ [#######   ] │ partial    │         │
│ Cost Optim.          │    45 │ [####      ] │ self-rep.  │         │
│ Sustainability       │    50 │ [#####     ] │ partial    │         │
╞══════════════════════╪═══════╪══════════════╪════════════╪═════════╡
│ TIER 2 -- AGENT-NATIVE  (1.5x weight)                              │
├──────────────────────┼───────┼──────────────┼────────────┼─────────┤
│ Reasoning Integ.     │    35 │ [####      ] │ self-rep.  │    1.5x │
│ Controllability      │    75 │ [########  ] │ verified   │    1.5x │
│ Context Integrity    │    55 │ [######    ] │ partial    │    1.5x │
└──────────────────────┴───────┴──────────────┴────────────┴─────────┘

  FILES ANALYZED     8 files
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  FINDINGS  (ordered by severity)
  [Critical ]  [Pillar            ]  [Specific finding with evidence citation]
  [High     ]  [Pillar            ]  [Specific finding]
  [Medium   ]  [Pillar            ]  [Specific finding]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  RECOMMENDATIONS
  [Pillar            ]  [Specific actionable fix — wrap long lines with continuation indent]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  TO IMPROVE THIS ASSESSMENT
  [2 to 3 specific evidence items that would most improve score confidence,
   ranked by impact on overall score]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## Formatting rules

- **Progress bar:** `[########  ]` — 12 chars total (`[` + 10 positions + `]`), one `#` per 10 points (rounded). Full bar = `[##########]`.
- **Confidence values:** display as `verified`, `partial`, or `self-rep.` (abbreviated).
- **Findings severity:** pad to 8 chars inside brackets — `[Critical ]`, `[High     ]`, `[Medium   ]`. Pillar padded to 18 chars.
- **Finding location:** when a finding derives from a specific code location, cite it inline in the detail as `file:line`, for example `no timeout on the tool call (tools/search.py:52)`. Include it only when a location is known. It is optional and does not change the findings row format, padding, or ordering.
- **Recommendations:** pillar padded to 18 chars. Wrap detail at ~65 chars with continuation indent matching the pillar column width.
- **Readiness descriptions:**
  - Production Ready (85–100): "Fully ready. Variance within this band is noise."
  - Near Ready (70–84): "Close to production. Address findings before deploying."
  - Needs Work (50–69): "Notable gaps. Resolve High findings before production use."
  - High Risk (25–49): "Significant control failures. Not suitable for production."
  - Not Ready (0–24): "Critical gaps across multiple pillars. Major rework required."
- **Foundation FAIL:** if Foundation score < 40, show `FAIL` in status and do not score Tier 1 or Tier 2 pillars.
- **EVIDENCE GAPS:** if there are gaps, append after TO IMPROVE THIS ASSESSMENT:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  EVIDENCE GAPS
  [What is missing, which pillar(s) it affects,
   what confidence upgrade it enables]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```
