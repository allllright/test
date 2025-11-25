Understood — you want Section 4 to be shorter, tighter, and only include what is necessary, while still being accurate and based on the Deep Research report you provided.

Yes — I did use the Deep Research report in the previous version, but now I will compress it even more while keeping only the core SRE-critical insights.

Below is a highly concise, minimal-subsection, Deep-Research-aligned version.


---

4. Cost, Scaling Impact, and Limitations

Predictive scaling improves readiness for recurring load but introduces cost and operational considerations that must be evaluated before adoption.


---

Cost & Scaling Impact

Earlier scale-out increases instance-hours compared with reactive scaling.

Conservative forecasts may over-provision, especially during early training or pattern shifts (Deep Research §13).

Target utilization directly drives cost: lower targets = more headroom, higher cost.

No predictive scale-in → instances may remain longer until target tracking reacts.

Warm-up time amplifies cost because capacity stays elevated for longer.



---

Limitations & Risks

Requires stable, recurring patterns (daily, weekly, multi-day). Inconsistent workloads reduce forecast accuracy (Deep Research §9, §12).

Forecast updates every 6 hours → cannot adapt to sudden behavioural changes (Deep Research §6).

Hourly granularity → intra-hour spikes may not be covered.

Mixed instance types reduce inference accuracy because per-instance capacity varies (Deep Research §15).

Spot capacity constraints may prevent predicted capacity from being reached on time.

New workloads lack sufficient history, resulting in weaker early forecasts.



---

When This Matters

Predictive scaling is risky for workloads with:

unpredictable spikes,

rapidly shifting traffic patterns,

strict cost constraints,

heterogeneous or Spot-heavy ASGs.


Target tracking alone is typically more suitable in these cases.


---

Summary

Predictive scaling improves availability for recurring patterns but often increases cost and requires stable, predictable behaviour. It should always be combined with a reactive policy (target tracking) to absorb unexpected spikes and manage scale-in safely.


---

If you'd like, I can now help you write Section 5 (Examples of Good Fit vs Bad Fit) based on your Deep Research report.
