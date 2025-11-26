Below is a concise, polished, SRE-style Section 5, incorporating:

What predictive scaling applies well to

What it does not apply well to

What you observed in your current environment (ASGs with desired=1 and ASG metrics disabled)

Clear reasoning and practical examples


This version is ready to paste directly into your Confluence document.


---

5. Applicability to Candidate Auto Scaling Groups

Predictive auto-scaling is effective only when workloads can scale horizontally and exhibit recurring load patterns that the forecasting model can learn. This section outlines where predictive scaling fits well, where it does not, and observations from current ASG configurations.


---

5.1 Characteristics of Good Candidates

Predictive scaling works best for ASGs that meet the following conditions:

Horizontal scalability
The application can run on multiple EC2 instances behind an ALB or another load-distributing layer.

Recurring load patterns
Metrics show clear, repeatable daily/weekly cycles (e.g., morning login surges, business-hour activity).

Sufficient ASG metric history
ASG-level CloudWatch metrics (GroupDesiredCapacity, ALBRequestCountPerTarget, CPU aggregated) are enabled and collected for at least 24 hours.

Non-trivial instance warm-up time
Instances require meaningful initialization time (e.g., JVM, caching, container warm-up), where proactive scale-out avoids latency degradation.


Example (Good Fit):
A stateless web/API service behind ALB, with noticeable weekday CPU and network traffic peaks and a warm-up period of 1–3 minutes. Predictive scaling could pre-launch instances before peak hours to maintain SLOs.


---

5.2 Workloads Not Suitable for Predictive Scaling

Predictive scaling is not effective for workloads that exhibit:

Irregular or unpredictable spikes driven by external events

Single-instance architectural constraints (stateful apps, cron-heavy nodes, non-distributed workloads)

ASG configurations where min = desired = max = 1
No scaling can occur, regardless of traffic patterns.

Missing ASG metrics
Without ASG-level CloudWatch metrics, forecasting cannot be trained.

Mixed or Spot-heavy instance groups
Forecasted capacity may not match actual available capacity.


Example (Not a Good Fit):
A stateful or singleton service running on one EC2 instance, where application logic prevents horizontal scaling. Even if instance-level metrics show weekday patterns, predictive scaling cannot be applied because the ASG cannot scale beyond one instance.


---

5.3 Findings from Current Environment

During review of existing Auto Scaling Groups:

All observed ASGs have min = desired = max = 1
→ Scaling is disabled; predictive scaling cannot apply.

ASG-level metrics collection is not enabled
→ Load patterns cannot be analysed at ASG level, preventing model training.

Instance-level metrics showed weekday activity peaks
→ Indicates a recurring pattern, but instance-level evidence is insufficient for predictive scaling unless the service is horizontally scalable.

Possible cost-saving path:
If the workload is fundamentally single-instance, predictive scaling is not applicable. Cost optimisation should focus on instance right-sizing, Graviton migration, or burstable instance classes rather than auto scaling.



---

5.4 Summary

Predictive scaling can only be considered for ASGs that:

support horizontal scaling,

have min < max,

expose ASG-level load metrics, and

show consistent recurring patterns.


Current ASGs do not meet these criteria; therefore predictive scaling is not applicable until the architecture and ASG configuration are adjusted.


---

If you'd like, I can now help you:

Finalize the entire document with a polished tone

Create a clear "Next Steps" section for your Jira ticket

Prepare talking points for your discussion with Christian or Anthi
