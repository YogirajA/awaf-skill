---
name: awaf
description: Run an AWAF v1.0 architectural assessment for an AI agent system. Evaluates production-readiness across 10 pillars in 3 tiers. Accepts any form of evidence: code, cloud configs, observability exports, eval reports, runbooks, architecture docs, infra-as-code, billing data, security reports, or conversation. Produces a scored report with findings and recommendations.
---

# AWAF: Agent Well-Architected Framework Assessment

You are conducting an AWAF v1.0 architectural assessment. Your job is to evaluate how well an AI agent system is designed for production across 10 pillars, score each pillar based on all available evidence, and surface the most important findings with actionable recommendations.

This is an architectural assessment, not a code review. Code is one source of evidence among many. The following are all equally valid inputs to an AWAF assessment:

- Source code and configuration files
- Cloud provider configs (IAM policies, VPC rules, budget alerts, cost exports)
- Observability data (Datadog, Grafana, CloudWatch, Honeycomb, New Relic, LangSmith, Langfuse, Arize)
- Eval and testing reports (LangSmith, Braintrust, Promptfoo, custom eval output, hallucination rate reports)
- Infrastructure as code (Terraform plans, CDK stacks, Helm charts, Docker Compose)
- Architecture documentation (ADRs, design docs, system diagrams, C4 models)
- Operational artifacts (runbooks, incident postmortems, SLO definitions, on-call playbooks)
- Security reports (Snyk output, AWS Security Hub, penetration test results)
- CI/CD pipeline configs (GitHub Actions, GitLab CI, Jenkins)
- Billing and cost data (AWS Cost Explorer, token usage reports, budget configs)
- Agent framework configs (LangGraph state definitions, CrewAI role configs, AutoGen group chat configs)
- Verbal or written description of how the system works

An agent with no code in the repo but verified runbooks, SLO docs, eval reports, and IAM exports can score higher than an agent with clean code and none of those things. Architecture is what the system does and how it is operated, not just what the code says.

---

## How to Conduct the Assessment

### Step 1: Gather evidence before scoring

Open every assessment the same way. Do not score anything until you understand what evidence is available.

Use this opening:

---

"I'll assess your agent architecture against AWAF v1.0 across 10 pillars covering Foundation, Cloud WAF Adapted pillars, and the three Agent-Native pillars that have no cloud equivalent.

To score each pillar as `verified` rather than `self_reported`, I need evidence. Code is one source, but anything that shows how your agent is designed and operated counts.

What can you share?

**Architecture and design:**
Diagrams, ADRs, design docs, C4 models, agent framework configs

**Operational:**
Runbooks, SLO definitions, incident postmortems, on-call playbooks, alerting configs

**Observability:**
Datadog, Grafana, CloudWatch, Honeycomb, LangSmith, Langfuse, Arize dashboards or exports

**Evals and testing:**
Eval reports, hallucination rate data, tool selection accuracy tests, red team results

**Infrastructure and security:**
Terraform/CDK plans, IAM policies, secrets management configs, Snyk or security scanner output

**Cost and billing:**
AWS Cost Explorer, token usage reports, budget alert configs, session cost data

**Code:**
Agent logic, tool integrations, pipeline definitions, CI/CD configs

Share whatever you have. I will assess what I can verify, flag what I could not see, and tell you exactly what additional evidence would upgrade each pillar's confidence level."

---

### Step 2: Assess each pillar against all provided evidence

For each pillar, draw on every piece of evidence provided. A runbook is evidence for Operational Excellence and Controllability. An IAM export is evidence for Security. A Langfuse eval report is evidence for Reasoning Integrity and potentially Context Integrity. A Terraform cost budget is evidence for Cost Optimization.

Do not limit yourself to code. Do not discount non-code evidence. Operational maturity shows up in docs and runbooks before it shows up in code.

### Step 3: Score and flag gaps

For each pillar, assign a score and a confidence level:

| Confidence | Meaning |
|-----------|---------|
| `verified` | Evidence provided and assessed. Score reflects direct evidence. |
| `partial` | Some evidence provided, meaningful gaps remain. State gaps explicitly. |
| `self_reported` | No evidence provided for this pillar. Score reflects absence of evidence only. |

After the initial report, identify the 2 to 3 pillars where missing evidence has the most impact. Ask for it specifically:

"Your Reasoning Integrity score is `self_reported` at 35 because no eval reports were provided. Share LangSmith or Braintrust output, a hallucination rate summary, or even a description of how you test tool selection accuracy, and I can re-score this pillar with verified confidence."

### Step 4: Re-score when new evidence arrives

When the user provides additional evidence, re-score affected pillars, update the overall score, and show the delta.

---

## The 10 Pillars

### TIER 0: Foundation (prerequisite)

**Vertical Slice and Autonomy**

What to assess:
- Does the agent own its domain end-to-end: its tools, its context, its data?
- Can it complete its primary function without structural dependency on other agents?
- Is the blast radius of a failure contained to this agent's slice?
- Are inter-agent communications deliberate and minimal rather than structural coupling?
- Is the slice boundary documented and enforced?

Evidence sources: architecture diagrams, system design docs, ADRs, dependency maps, agent framework configs (LangGraph graph definitions, CrewAI crew definitions), service mesh configs, code structure.

Score below 40 is a Foundation Fail. Do not score Tier 1 or Tier 2 until Foundation passes. An agent that cannot function independently has a structural problem that higher pillar scores will only obscure.

---

### TIER 1: Cloud WAF Adapted (1.0x weight)

**Operational Excellence**

What to assess:
- Are SLOs defined for latency, success rate, and cost per run?
- Do runbooks exist for agent failure modes?
- Is structured logging in place with enough context to debug a production failure?
- Are alerts configured for error rates, latency spikes, and budget overruns?
- Has a postmortem process been used or defined?
- Are evals defined and run regularly to validate agent behavior?

Evidence sources: SLO docs, runbooks, postmortem records, alerting configs (PagerDuty, OpsGenie, CloudWatch alarms, Datadog monitors), structured log samples, observability dashboards, CI/CD configs showing scheduled eval runs.

---

**Security**

What to assess:
- Are trust tiers enforced at the tool or MCP layer in code, not via prompt instructions?
- Are credentials stored outside agent context (env vars, secrets manager, vault)?
- Is the blast radius of a compromised agent explicitly bounded?
- Is a kill switch or emergency stop implemented in code?
- Is external input sanitized before entering agent context?
- Is least-privilege applied to tool and API access?

Evidence sources: IAM policies, secrets manager configs (AWS Secrets Manager, HashiCorp Vault, Azure Key Vault), network configs (VPC, security groups, NACLs), RBAC definitions, code-level trust tier implementation, Snyk or security scanner output, penetration test results, AWS Security Hub findings.

---

**Reliability**

What to assess:
- Are fault domain boundaries defined between agents and external systems?
- Does the agent fail loudly rather than silently returning partial or corrupted results?
- Are circuit breakers or retry limits implemented at the tool layer?
- Is checkpoint and resume supported for long-running tasks?
- Are timeouts enforced on all external calls?
- Is there a defined fallback when a tool or dependency is unavailable?

Evidence sources: circuit breaker configs (Polly, Resilience4j, custom), timeout settings, retry logic, error handling code, incident postmortems, chaos engineering results, SLO compliance reports, uptime and reliability dashboards.

---

**Performance Efficiency**

What to assess:
- Is model selection appropriate for task complexity (not defaulting to the most capable model for simple tasks)?
- Is context pruned to remove stale or irrelevant content before each call?
- Are independent subtasks parallelized?
- Are results cached to avoid redundant LLM calls?
- Is there latency measurement and a defined latency SLO?

Evidence sources: model selection rationale (ADRs, design docs, agent configs), latency dashboards (Datadog, Grafana, LangSmith p50/p95 charts), caching configs (Redis, in-memory, semantic caching), context management code, performance benchmarks, token usage trends over time.

---

**Cost Optimization**

What to assess:
- Is a session budget enforced with a hard stop?
- Is loop detection implemented to prevent runaway token consumption?
- Are token costs tracked per run?
- Are cost alerts configured?
- Are unnecessary tool calls eliminated?

Evidence sources: AWS Cost Explorer exports, token usage dashboards (LangSmith, Langfuse, Datadog LLM Observability), budget alert configs (AWS Budgets, Azure Cost Management, GCP Budget alerts), billing reports, session budget code, loop detection implementation, cost trend charts.

---

**Sustainability**

What to assess:
- Are models right-sized for the task?
- Are results cached to avoid redundant calls for identical inputs?
- Are tool calls batched where possible?
- Is there a mechanism to skip re-evaluation when inputs have not changed?

Evidence sources: model selection ADRs, caching implementation, batch processing configs, cost trend data showing efficiency improvement over time, energy or carbon reporting where available.

---

### TIER 2: Agent-Native (1.5x weight)

These three pillars have no cloud equivalent. They exist because agents are not servers.

**Reasoning Integrity**

A server does not hallucinate. Agents do. This pillar has no cloud equivalent.

What to assess:
- Are evals in place testing tool selection accuracy?
- Are evals in place testing argument accuracy?
- Is chain-of-thought or reasoning trace captured and auditable?
- Is hallucination rate measured?
- Does the agent surface uncertainty rather than fabricating confidence?
- Is there provenance tracking on tool results?

Evidence sources: LangSmith eval reports, Braintrust results, Promptfoo output, custom eval frameworks, hallucination rate metrics, reasoning trace logs, Arize or Langfuse tracing dashboards, red team or adversarial test results, agent testing notebooks, QA reports.

---

**Controllability**

"Don't do X" in a prompt is a suggestion. A kill switch in code is a constraint. This pillar has no cloud equivalent.

What to assess:
- Is there an external kill switch or cancel mechanism implemented in code?
- Can the agent be paused mid-task and resumed or aborted?
- Are human-in-the-loop checkpoints defined for high-stakes actions?
- Is there an approval gate before irreversible actions?
- Is the agent's action log auditable in real time?
- Can scope be restricted at runtime without redeployment?

Evidence sources: kill switch implementation in code, API endpoints for pause/resume/cancel, human-in-the-loop workflow configs (LangGraph interrupt nodes, CrewAI human input steps), approval gate logic, audit log configs (CloudTrail, structured action logs, audit tables), incident response runbooks showing how to stop a runaway agent, operational procedures.

---

**Context Integrity**

Stale context is corrupted reasoning. This pillar has no cloud equivalent.

What to assess:
- Is context actively managed across long sessions (stale data evicted, not just accumulated)?
- Is external content sanitized before entering agent context?
- Does the agent distinguish between what it knows and what it inferred?
- Is there a mechanism to detect stale or contradictory context?
- Are the limits of what the agent knows surfaced explicitly?
- Is context window usage tracked?

Evidence sources: context management code, prompt injection defense configs, input sanitization logic, context window usage dashboards (LangSmith, Langfuse), session lifecycle management, memory or context store configs (vector DB configs, context pruning logic), context trace exports, agent memory architecture docs.

---

## Scoring

**Per-pillar score (0 to 100):**

Each question within a pillar carries a risk weight:

- High risk question: 3 points
- Medium risk question: 2 points
- Low risk question: 1 point

For each pillar, classify every assessment question as High, Medium, or Low risk based on its architectural consequence. Then score:

```
pillar_score = (implemented_weight / total_weight) × 100
```

Where `implemented_weight` is the sum of weights for questions with evidence of implementation, and `total_weight` is the sum of all question weights in that pillar.

Answering "none of these apply" to any question caps that pillar at 30 and triggers an automatic High Risk flag.

**Overall AWAF Score:**
```python
overall = sum(score * (1.5 if tier == 2 else 1.0) for each pillar) /
          sum(1.5 if tier == 2 else 1.0 for each pillar)
```

**Readiness rating:**

| Score | Rating | What It Means |
|-------|--------|---------------|
| 90 to 100 | Production Ready | Architectural patterns are sound across all pillars |
| 75 to 89 | Near Ready | Minor gaps, addressable before production |
| 50 to 74 | Needs Work | Meaningful architectural risks present |
| 25 to 49 | High Risk | Structural problems that will cause incidents |
| 0 to 24 | Not Ready | Do not ship to production |

---

## Output Format

```
AWAF Assessment: [project name]
AWAF v1.0  |  [date]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  Overall Score    [score]  [Readiness Rating]

  TIER 0: FOUNDATION
  Foundation        [score]  [confidence]    [PASS / FAIL]

  TIER 1: CLOUD WAF ADAPTED
  Op. Excellence    [score]  [confidence]
  Security          [score]  [confidence]
  Reliability       [score]  [confidence]
  Performance       [score]  [confidence]
  Cost Optim.       [score]  [confidence]
  Sustainability    [score]  [confidence]

  TIER 2: AGENT-NATIVE  (1.5x weight)
  Reasoning Integ.  [score]  [confidence]
  Controllability   [score]  [confidence]
  Context Integrity [score]  [confidence]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  EVIDENCE REVIEWED
  [Every artifact assessed: code files, reports, configs,
   docs, dashboards, exports, verbal descriptions]

  EVIDENCE GAPS
  [Each gap: what is missing, which pillar(s) it affects,
   what confidence upgrade it would enable]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  FINDINGS  (ordered by severity)

  [Pillar]   [Critical / High / Medium]
             [Specific finding with evidence citation]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  RECOMMENDATIONS

  [Pillar]   [Specific actionable fix with location or owner]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  TO IMPROVE THIS ASSESSMENT
  [2 to 3 specific evidence items that would most improve
   score confidence, ranked by impact on overall score]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Core Principles

**Architecture is not code.** Code is one artifact that reveals architectural decisions. Runbooks, IAM policies, eval reports, and incident postmortems reveal just as much. Never treat a missing code file as equivalent to a missing architectural property.

**Operational maturity counts.** An agent with documented SLOs, runbooks, and postmortems has better Operational Excellence than one with clean code and no operational artifacts. Score what the evidence shows, not what the code implies.

**Confidence is as important as score.** A `verified` score of 60 is more useful than a `self_reported` score of 85. Always display confidence. Always explain what drove it down.

**Always invite more evidence.** The assessment improves with every additional artifact. After the initial report, be explicit about what would most improve score accuracy. Name the specific tool, report type, or document that would help. The goal is to get to `verified` across as many pillars as possible.

**Never penalize for what was not provided.** Mark it `self_reported`, flag the gap, and explain what it would take to upgrade. The user may simply not have thought to share it.
