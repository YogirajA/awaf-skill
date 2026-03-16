# awaf — Claude Code Skill

A [Claude Code](https://claude.ai/code) skill that runs an [AWAF v1.0](https://github.com/YogirajA/awaf) architectural assessment for AI agent systems.

## What is AWAF?

**Agent Well-Architected Framework (AWAF)** is an open specification defining production-readiness criteria for AI agents. It fills the same gap for agents that AWS WAF fills for cloud infrastructure: a vendor-neutral, community-owned standard for architectural rigour.

AWAF v1.0 evaluates agents across **10 pillars in 3 tiers**:

| Tier | Pillars | Weight |
|------|---------|--------|
| **Tier 0 — Foundation** | Vertical Slice & Autonomy | Prerequisite |
| **Tier 1 — Cloud WAF Adapted** | Operational Excellence, Security, Reliability, Performance Efficiency, Cost Optimization, Sustainability | 1.0× |
| **Tier 2 — Agent-Native** | Reasoning Integrity, Controllability, Context Integrity | 1.5× |

Tier 2 pillars carry extra weight because they have no cloud equivalent. Servers don't hallucinate, don't need kill switches in code, and don't accumulate stale reasoning context.

Full spec: [github.com/YogirajA/awaf](https://github.com/YogirajA/awaf)

---

## What This Skill Does

This skill is a **natural-language implementation** of the AWAF spec. Unlike `awaf-cli` (the code-scanning reference implementation), this skill accepts any form of evidence and conducts a dialogue-driven assessment:

- Source code and configuration files
- Cloud provider configs (IAM policies, VPC rules, budget alerts)
- Observability exports (Datadog, Grafana, CloudWatch, Honeycomb, LangSmith, Langfuse, Arize)
- Eval and testing reports (LangSmith, Braintrust, Promptfoo, hallucination rate data)
- Infrastructure as code (Terraform plans, CDK stacks, Helm charts)
- Architecture docs (ADRs, design docs, C4 models, system diagrams)
- Operational artifacts (runbooks, SLO definitions, incident postmortems)
- Security reports (Snyk output, AWS Security Hub, pen test results)
- CI/CD configs (GitHub Actions, GitLab CI, Jenkins)
- Billing and cost data (AWS Cost Explorer, token usage reports)
- **Verbal or written description of how your system works**

An agent with no code in the repo but verified runbooks, SLO docs, eval reports, and IAM exports can score higher than one with clean code and none of those things. Architecture is what the system does and how it is operated, not just what the code says.

---

## Installation

**Via the Claude Code VSCode extension:**

Open Manage Plugins, go to the Marketplaces tab, and add:

```
https://github.com/YogirajA/awaf-skill
```

Then install the `awaf` plugin from the marketplace.

**Via CLI:**

```bash
/plugin marketplace add YogirajA/awaf-skill
/plugin install awaf@awaf-marketplace
```

---

## Usage

```
/awaf
```

The skill opens by asking what evidence you can share, then:

1. **Gathers evidence** — accepts anything you provide across all evidence categories
2. **Scores each pillar** — assigns 0–100 with a confidence level (`verified` / `partial` / `self_reported`)
3. **Produces a structured report** — overall score, per-pillar breakdown, findings, recommendations
4. **Requests targeted evidence** — after the initial report, identifies the 2–3 gaps that would most improve score confidence and asks for them specifically
5. **Re-scores on new evidence** — when you provide more artifacts, affected pillars are re-scored and deltas are shown

---

## Scoring

**Per-pillar (0–100):** each question carries a risk weight (High = 3 pts, Medium = 2 pts, Low = 1 pt):

```
pillar_score = (implemented_weight / total_weight) × 100
```

Answering "none of these apply" to any question caps that pillar at **30** and triggers an automatic **High Risk** flag.

**Overall score** applies a 1.5× multiplier to Tier 2 pillars:

```python
overall = sum(score * (1.5 if tier == 2 else 1.0) for each pillar) /
          sum(1.5 if tier == 2 else 1.0 for each pillar)
```

**Readiness rating:**

| Score | Rating | What It Means |
|-------|--------|---------------|
| 90–100 | Production Ready | Architectural patterns are sound across all pillars |
| 75–89 | Near Ready | Minor gaps, addressable before production |
| 50–74 | Needs Work | Meaningful architectural risks present |
| 25–49 | High Risk | Structural problems that will cause incidents |
| 0–24 | Not Ready | Do not ship to production |

**Confidence levels:**

| Level | Meaning |
|-------|---------|
| `verified` | Evidence provided and assessed directly |
| `partial` | Some evidence provided, meaningful gaps remain |
| `self_reported` | No evidence provided; score reflects absence only |

A `verified` 60 is more useful than a `self_reported` 85. The skill always displays confidence and always explains what drove it down.

---

## Example Output

```
   _      _  _  _    _      ___
  /_\    | || || |  /_\    | __|
 / _ \   | \/ \/ | / _ \   | _|
/_/ \_\   \_/\_/  /_/ \_\  |_       Agent Well-Architected Framework

AWAF Assessment: my-research-agent
AWAF v1.0  |  2026-03-15
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Overall Score    61/100   Needs Work
  Notable gaps. Resolve High findings before production use.

  Scale: Production Ready >=90 · Near Ready >=75 · Needs Work >=50
         High Risk >=25 · Not Ready <25
  Foundation <40 = automatic FAIL regardless of overall score.
  Tier 2 pillars (Reasoning, Controllability, Context Integrity) carry 1.5x weight.

┌──────────────────────┬───────┬────────────┬────────────┬────────┐
│ Pillar               │ Score │ Progress   │ Confidence │ Status │
╞══════════════════════╪═══════╪════════════╪════════════╪════════╡
│ TIER 0 -- FOUNDATION                                            │
├──────────────────────┼───────┼────────────┼────────────┼────────┤
│ Foundation           │    72 │ [#######  ] │ partial    │   PASS │
╞══════════════════════╪═══════╪════════════╪════════════╪════════╡
│ TIER 1 -- CLOUD WAF ADAPTED                                     │
├──────────────────────┼───────┼────────────┼────────────┼────────┤
│ Op. Excellence       │    55 │ [######   ] │ partial    │        │
│ Security             │    80 │ [########] │ verified   │        │
│ Reliability          │    60 │ [######   ] │ self-rep.  │        │
│ Performance          │    70 │ [#######  ] │ partial    │        │
│ Cost Optim.          │    45 │ [####     ] │ self-rep.  │        │
│ Sustainability       │    50 │ [#####    ] │ partial    │        │
╞══════════════════════╪═══════╪════════════╪════════════╪════════╡
│ TIER 2 -- AGENT-NATIVE  (1.5x weight)                          │
├──────────────────────┼───────┼────────────┼────────────┼────────┤
│ Reasoning Integ.     │    35 │ [###      ] │ self-rep.  │   1.5x │
│ Controllability      │    75 │ [#######  ] │ verified   │   1.5x │
│ Context Integrity    │    55 │ [######   ] │ partial    │   1.5x │
└──────────────────────┴───────┴────────────┴────────────┴────────┘

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  FINDINGS  (ordered by severity)
  [High     ]  Reasoning Integ.    No evals provided; hallucination rate unknown
  [High     ]  Cost Optim.         No session budget cap or loop detection observed
  [Medium   ]  Op. Excellence      SLOs referenced in docs but no alerting config found
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  RECOMMENDATIONS
  Reasoning Integ.    Share LangSmith or Braintrust eval output to score tool
                      selection and hallucination rate
  Cost Optim.         Implement session budget cap before tool dispatch;
                      add loop detection on repeated tool calls
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  TO IMPROVE THIS ASSESSMENT
  Eval reports (LangSmith, Braintrust, or custom) to upgrade Reasoning
  Integ. from self-rep. to verified
  Token usage dashboard or budget alert config for Cost Optim.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  EVIDENCE GAPS
  No eval reports provided. Reasoning Integ. scored self-rep.; share
  LangSmith/Braintrust output to upgrade to verified.
  No cost/budget artifacts. Cost Optim. scored self-rep.; share token
  usage dashboard or budget alert config.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Relationship to the AWAF Spec

| | awaf-cli | awaf-skill (this repo) |
|---|---|---|
| Input | Code scanning, static analysis | Any evidence: code, docs, exports, verbal description |
| Verification | Automated, deterministic | AI-assessed, dialogue-driven |
| Can assess operational artifacts | No | Yes |
| Can assess verbal descriptions | No | Yes |
| Best for | CI/CD, automated gates | Architecture reviews, early-stage agents, teams without full tooling |

Both implement the same AWAF v1.0 spec and produce comparable scores.

---

## License

Apache 2.0. See [LICENSE](LICENSE).

The AWAF name and logo are trademarked by the AWAF project. Specification use is unrestricted per the [upstream license](https://github.com/YogirajA/awaf).
