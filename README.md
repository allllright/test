Here is your refined, polished, and fully-consistent Section 1, with every improvement applied exactly as discussed — no extra changes, no style drift.

It is ready to paste into Confluence.

1. Overview

What is predictive auto-scaling?
Amazon EC2 Auto Scaling predictive scaling is a feature that uses machine learning to forecast demand and proactively adjust EC2 capacity. Instead of waiting for CPU or request metrics to rise, the Auto Scaling Group (ASG) launches instances ahead of time so they are fully initialised and healthy before the predicted peak period.

Predictive scaling improves availability during recurring load patterns and works alongside reactive policies for unexpected spikes. It reduces scale-out lag during peak periods, minimises reliance on scheduled actions, and helps maintain SLO performance. However, forecasts may occasionally over-provision, increasing cost.

Prerequisites and applicability:
Predictive scaling requires workloads that can scale horizontally (min < max), stable ASG-level CloudWatch metrics, and recurring demand patterns for the model to learn. Instance-level patterns alone are not sufficient; the ASG must expose the load metric and be capable of adjusting capacity. These constraints and the state of current ASGs are evaluated in Section 5.

Key findings:

Most effective for workloads with stable, repeating demand patterns.

Requires at least 24 hours of ASG metric history, with 2+ weeks recommended for accurate forecasting.

Only performs predictive scale-out; scale-in always relies on reactive policies.

Predictive scaling + target tracking provides the best balance between proactive readiness and reactive stability.

Not suitable for workloads driven by unpredictable or irregular spikes or single-instance architecture.

Current ASGs do not meet prerequisite criteria; therefore, predictive scaling is not applicable until the architecture and ASG configuration are adjusted.

High-level recommendation and next steps:

Evaluate whether predictive auto scaling is needed. If yes, enable the prerequisites (ASG-level CloudWatch metrics and an ASG that can scale horizontally: min < max).

Enable Forecast Only mode on a candidate ASG to evaluate forecast quality for 2 weeks.

Confirm that the workload exhibits consistent daily/weekly cycles and that forecasts align with real peaks.

If accurate, consider a PoC using Forecast + Scale, combined with an existing target tracking policy.

Continue monitoring forecast accuracy, utilisation and cost impact before broader adoption.

If you’d like, I can now help you produce:

✅ a final polished version of the full document
✅ a summary for your Jira ticket
✅ meeting prep notes for Christian / Anthi

Just tell me what you need next.
