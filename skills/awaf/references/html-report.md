# AWAF HTML Report Template

Read this when writing the saved HTML report. Produce a single self-contained HTML file at `awaf-report.html` that is visually interchangeable with `awaf report --format html` from the CLI. The CLI renderer `awaf-cli/awaf/report_html.py` is the source of truth for this theme; keep this template faithful to it.

## Rules

- **Self-contained.** Inline CSS only. No `<script>`, no images, no external fetch. The single Google-Fonts `<link>` with local fallbacks is the only external reference.
- **Escape all assessment text.** Any text you substitute into a placeholder that did not originate from this document's own fixed vocabulary (the band labels, the severity buckets, and the hex colors defined here) must have `<` replaced with `&lt;`, `>` with `&gt;`, and `&` with `&amp;` before you insert it. That includes the project name, every finding detail and recommendation detail, the `file:line` location value, and every evidence, gap, and improvement item. File paths and finding text come from the assessed repository and can contain markup characters, so escaping them is what keeps the report free of injected `<script>` and true to the self-contained rule above.
- **Fill in, do not restructure.** Substitute the assessment's real values into the skeleton and section snippets below. Keep the class names and structure exactly as written so the styling applies.
- **No em dashes** in any copy you write into the report. The band ranges use en dashes (already in the template). The middot `·` is used as written.

## Document skeleton

Replace `PROJECT_NAME` with the escaped project or agent name. Replace `BODY` with the concatenation of the section fragments below, in this order: masthead, readiness bands, action items, pillar scorecard, recommendations, evidence, improvements, footer.

```html
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>AWAF · PROJECT_NAME</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Playfair+Display:ital,wght@0,400;0,600;0,700;1,400;1,600&amp;family=Hanken+Grotesk:wght@400;500;600;700&amp;family=JetBrains+Mono:wght@400;500;700&amp;display=swap" rel="stylesheet">
<style>
:root{
  --font-display:'Playfair Display',Georgia,'Times New Roman',serif;
  --font-sans:'Hanken Grotesk',system-ui,-apple-system,'Segoe UI',sans-serif;
  --font-mono:'JetBrains Mono',ui-monospace,'SFMono-Regular',Consolas,monospace;
  --paper:#fdfbfe;--ink:#160f1c;--muted:#6c6375;--hair:#e7e1ea;
}
*{box-sizing:border-box}
body{margin:0;background:#ece7ef;color:var(--ink);font-family:var(--font-sans);
  -webkit-font-smoothing:antialiased;line-height:1.6}
.wrap{max-width:900px;margin:0 auto;padding:32px 20px 64px}
.eyebrow{font-family:var(--font-mono);font-size:12px;letter-spacing:.18em;
  text-transform:uppercase;color:var(--muted)}
h1,h2,h3{font-family:var(--font-display);font-weight:600;letter-spacing:-.02em;margin:0}
section.block{margin-top:34px}
section.block>h2{font-size:26px;color:var(--ink);margin-bottom:16px}
.muted-line{color:var(--muted);font-style:italic}
.masthead{position:relative;overflow:hidden;border-radius:20px;color:#fdfbfe;
  padding:48px 44px;background:linear-gradient(150deg,#271539 0%,#3a2154 55%,#5b3663 100%)}
.masthead .glow-a{position:absolute;top:-120px;right:-80px;width:420px;height:420px;
  border-radius:50%;background:radial-gradient(circle,rgba(239,125,36,.38),rgba(239,125,36,0) 68%)}
.masthead .glow-b{position:absolute;bottom:-140px;left:-70px;width:380px;height:380px;
  border-radius:50%;background:radial-gradient(circle,rgba(15,143,134,.26),rgba(15,143,134,0) 66%)}
.masthead .inner{position:relative}
.masthead .eyebrow{color:#f0a672}
.masthead h1{font-size:46px;color:#fdfbfe;margin:14px 0 6px}
.masthead .meta{font-family:var(--font-mono);font-size:13px;letter-spacing:.06em;
  color:rgba(253,251,254,.7)}
.scoreline{display:flex;align-items:baseline;gap:18px;margin-top:26px;flex-wrap:wrap}
.scoreline .num{font-family:var(--font-display);font-size:74px;line-height:1;color:#fdfbfe}
.pill{display:inline-block;font-family:var(--font-mono);font-size:13px;letter-spacing:.08em;
  text-transform:uppercase;padding:6px 14px;border-radius:999px;background:rgba(253,251,254,.14);
  color:#fdfbfe;border:1px solid rgba(253,251,254,.28)}
.scoreline .blurb{color:rgba(253,251,254,.8);font-size:16px;max-width:420px}
.bands{display:flex;gap:8px;margin-top:8px;flex-wrap:wrap}
.bandcell{flex:1;min-width:120px;border:1px solid var(--hair);border-radius:12px;
  background:#fff;padding:12px 14px}
.bandcell.here{border-color:#c4407e;box-shadow:0 0 0 2px rgba(196,64,126,.16)}
.bandcell .lab{font-family:var(--font-display);font-weight:600;font-size:16px}
.bandcell .rng{font-family:var(--font-mono);font-size:11px;letter-spacing:.06em;color:var(--muted)}
.action{border:1px solid var(--hair);border-left-width:5px;border-radius:12px;
  padding:16px 20px;margin-top:12px}
.action .top{display:flex;align-items:center;gap:12px;flex-wrap:wrap;margin-bottom:8px}
.sev{font-family:var(--font-mono);font-size:11px;letter-spacing:.1em;text-transform:uppercase;
  font-weight:700;padding:3px 10px;border-radius:6px;background:#fff}
.tag{font-family:var(--font-mono);font-size:10px;letter-spacing:.1em;text-transform:uppercase;
  padding:2px 8px;border-radius:5px;border:1px solid currentColor}
.action .pillar{font-family:var(--font-mono);font-size:12px;letter-spacing:.08em;
  text-transform:uppercase;font-weight:700}
.action .detail{color:var(--ink);font-size:16px}
.loc{font-family:var(--font-mono);font-size:12px;color:var(--muted);background:#f4f2f6;
  padding:2px 8px;border-radius:6px;margin-top:8px;display:inline-block}
.tier{margin-top:22px}
.tier>.eyebrow{margin-bottom:10px;display:block}
.scorecard .row{display:flex;align-items:center;gap:16px;padding:12px 0;
  border-bottom:1px solid var(--hair)}
.scorecard .row:last-child{border-bottom:none}
.scorecard .pname{flex:0 0 190px;font-family:var(--font-display);font-weight:600;font-size:18px}
.scorecard .barwrap{flex:1;height:10px;border-radius:999px;background:#efeaf2;overflow:hidden}
.scorecard .bar{height:100%;border-radius:999px}
.scorecard .val{flex:0 0 120px;text-align:right;font-family:var(--font-mono);font-size:14px}
.scorecard .val .s{font-size:18px;font-weight:700;color:var(--ink)}
.scorecard .val .c{color:var(--muted)}
.foundfail{margin-top:14px;border-left:5px solid #c4407e;background:#fbeef3;border-radius:12px;
  padding:14px 18px;color:#8a1f4b;font-size:15px}
.rec{border:1px solid var(--hair);border-radius:12px;padding:14px 18px;margin-top:10px}
.rec .pillar{font-family:var(--font-mono);font-size:12px;letter-spacing:.08em;text-transform:uppercase;
  color:var(--muted);font-weight:700;margin-bottom:4px}
.twocol{display:grid;grid-template-columns:1fr 1fr;gap:20px}
ul.plain{margin:0;padding-left:20px}
ul.plain li{margin:6px 0}
.foot{margin-top:44px;padding-top:20px;border-top:1px solid var(--hair);display:flex;
  justify-content:space-between;flex-wrap:wrap;gap:12px;font-family:var(--font-mono);
  font-size:12px;letter-spacing:.06em;color:var(--muted)}
@media (max-width:640px){
  .scorecard .pname{flex-basis:120px;font-size:15px}
  .twocol{grid-template-columns:1fr}
  .masthead h1{font-size:34px}
  .scoreline .num{font-size:56px}
}
@media print{
  body{background:#fff}
  .card,.action,.rec,.bandcell{box-shadow:none}
  .masthead{-webkit-print-color-adjust:exact;print-color-adjust:exact}
}
</style>
</head>
<body>
<div class="wrap">
BODY
</div>
</body>
</html>
```

## Section fragments

**Masthead.** `SCORE` is the integer overall score. `BAND` and `BLURB` come from the readiness-band table below. `DATE` is today's date in YYYY-MM-DD format.

```html
<header class="masthead"><div class="glow-a"></div><div class="glow-b"></div><div class="inner">
<div class="eyebrow">AWAF v1.4 · Assessment</div>
<h1>PROJECT_NAME</h1>
<div class="meta">DATE · AWAF skill</div>
<div class="scoreline"><div class="num">SCORE</div><div class="pill">BAND</div><div class="blurb">BLURB</div></div>
</div></header>
```

**Readiness bands.** Emit all five cells in this order. Add ` here` to the class of the one cell whose label matches the current band (`<div class="bandcell here">`).

```html
<section class="block"><div class="eyebrow">Readiness bands</div><div class="bands">
<div class="bandcell"><div class="lab">Production Ready</div><div class="rng">85–100</div></div>
<div class="bandcell"><div class="lab">Near Ready</div><div class="rng">70–84</div></div>
<div class="bandcell"><div class="lab">Needs Work</div><div class="rng">50–69</div></div>
<div class="bandcell"><div class="lab">High Risk</div><div class="rng">25–49</div></div>
<div class="bandcell"><div class="lab">Not Ready</div><div class="rng">0–24</div></div>
</div></section>
```

**Action items.** One `.action` card per finding, ordered by severity (High and Critical first, then Medium, then Low). Pick the colors from the severity table. The `<div class="loc">` is present only when the finding has a `file:line`.

```html
<section class="block"><h2>Action items</h2>
<div class="action" style="border-left-color:BORDER;background:TINT">
<div class="top"><span class="sev" style="color:PILL_TEXT">SEVERITY</span><span class="pillar" style="color:BORDER">PILLAR</span></div>
<div class="detail">DETAIL</div>
<div class="loc">FILE:LINE</div>
</div>
</section>
```

If there are no findings, use `<section class="block"><h2>Action items</h2><div class="muted-line">No action items recorded.</div></section>`.

**Pillar scorecard.** Three tier groups. Include the `.foundfail` callout only when Foundation scored below 40. Each pillar is one `.row`; the bar `width` is the pillar score clamped to 0 to 100, and `ACCENT` comes from the pillar-accent table. For a pillar you did not score (Foundation failed), render the value cell as `<span class="c">not scored</span>` and omit the bar.

```html
<section class="block"><h2>Pillar scores</h2>
<div class="foundfail">Foundation scored below 40: the Foundation gate failed, so the other pillars are advisory only until it is resolved.</div>
<div class="tier"><span class="eyebrow">Tier 0 · Foundation</span><div class="scorecard">
<div class="row"><div class="pname">Foundation</div><div class="barwrap"><div class="bar" style="width:SCORE%;background:#3a2154"></div></div><div class="val"><span class="s">SCORE</span><span class="c"> · CONFIDENCE</span></div></div>
</div></div>
<div class="tier"><span class="eyebrow">Tier 1 · Cloud WAF Adapted</span><div class="scorecard">
<div class="row"><div class="pname">Op. Excellence</div><div class="barwrap"><div class="bar" style="width:SCORE%;background:#6c6375"></div></div><div class="val"><span class="s">SCORE</span><span class="c"> · CONFIDENCE</span></div></div>
<div class="row"><div class="pname">Security</div><div class="barwrap"><div class="bar" style="width:SCORE%;background:#6c6375"></div></div><div class="val"><span class="s">SCORE</span><span class="c"> · CONFIDENCE</span></div></div>
<div class="row"><div class="pname">Reliability</div><div class="barwrap"><div class="bar" style="width:SCORE%;background:#6c6375"></div></div><div class="val"><span class="s">SCORE</span><span class="c"> · CONFIDENCE</span></div></div>
<div class="row"><div class="pname">Performance</div><div class="barwrap"><div class="bar" style="width:SCORE%;background:#6c6375"></div></div><div class="val"><span class="s">SCORE</span><span class="c"> · CONFIDENCE</span></div></div>
<div class="row"><div class="pname">Cost Optim.</div><div class="barwrap"><div class="bar" style="width:SCORE%;background:#6c6375"></div></div><div class="val"><span class="s">SCORE</span><span class="c"> · CONFIDENCE</span></div></div>
<div class="row"><div class="pname">Sustainability</div><div class="barwrap"><div class="bar" style="width:SCORE%;background:#6c6375"></div></div><div class="val"><span class="s">SCORE</span><span class="c"> · CONFIDENCE</span></div></div>
</div></div>
<div class="tier"><span class="eyebrow">Tier 2 · Agent-Native (1.5x weight)</span><div class="scorecard">
<div class="row"><div class="pname">Reasoning Integ.</div><div class="barwrap"><div class="bar" style="width:SCORE%;background:#ef7d24"></div></div><div class="val"><span class="s">SCORE</span><span class="c"> · CONFIDENCE</span></div></div>
<div class="row"><div class="pname">Controllability</div><div class="barwrap"><div class="bar" style="width:SCORE%;background:#c4407e"></div></div><div class="val"><span class="s">SCORE</span><span class="c"> · CONFIDENCE</span></div></div>
<div class="row"><div class="pname">Context Integrity</div><div class="barwrap"><div class="bar" style="width:SCORE%;background:#0f8f86"></div></div><div class="val"><span class="s">SCORE</span><span class="c"> · CONFIDENCE</span></div></div>
</div></div>
</section>
```

**Recommendations.** One `.rec` per recommendation. If none, use `<div class="muted-line">No recommendations recorded.</div>` inside the section.

```html
<section class="block"><h2>Recommendations</h2>
<div class="rec"><div class="pillar">PILLAR</div><div class="detail">DETAIL</div></div>
</section>
```

**Evidence.** Two columns. Each side is a `<ul class="plain">` of escaped items, or `<div class="muted-line">None recorded.</div>` when empty.

```html
<section class="block"><h2>Evidence</h2><div class="twocol">
<div><div class="eyebrow">Reviewed</div><ul class="plain"><li>ITEM</li></ul></div>
<div><div class="eyebrow">Gaps</div><ul class="plain"><li>ITEM</li></ul></div>
</div></section>
```

**Improvements.**

```html
<section class="block"><h2>To improve this assessment</h2><ul class="plain"><li>ITEM</li></ul></section>
```

**Footer.**

```html
<footer class="foot"><span>AWAF v1.4 assessment · DATE</span><span>Generated by the AWAF skill · awaf.ai</span></footer>
```

## Readiness bands (label, range, blurb)

| Label | Range | Blurb |
|-------|-------|-------|
| Production Ready | 85 to 100 | Fully ready. Variance within this band is noise. |
| Near Ready | 70 to 84 | Close to production. Address findings before deploying. |
| Needs Work | 50 to 69 | Notable gaps. Resolve High findings before production use. |
| High Risk | 25 to 49 | Significant control failures. Not suitable for production. |
| Not Ready | 0 to 24 | Critical gaps across multiple pillars. Major rework required. |

## Severity colors (action items)

| Severity | BORDER | TINT | PILL_TEXT |
|----------|--------|------|-----------|
| High or Critical | #c4407e | #fbeef3 | #c4407e |
| Medium | #ef7d24 | #fdf4ec | #c9660f |
| Low | #0f8f86 | #f4faf8 | #0b6b64 |
| other | #6c6375 | #f4f2f6 | #6c6375 |

## Pillar accents (scorecard bars)

| Pillar | ACCENT |
|--------|--------|
| Foundation | #3a2154 |
| Op. Excellence, Security, Reliability, Performance, Cost Optim., Sustainability | #6c6375 |
| Reasoning Integ. | #ef7d24 |
| Controllability | #c4407e |
| Context Integrity | #0f8f86 |
