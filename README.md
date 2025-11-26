
🎯 Where We Should Make Changes

Only Section 1 requires refinement — everywhere else is already consistent, crisp, and well-aligned.

Here’s what you should adjust:

1. Section 1 currently implies predictive scaling might be widely applicable

But Section 5 shows:

current ASGs cannot scale

no ASG metrics

many workloads might be singleton / stateful

So Section 1 should add a line acknowledging that:

“Its applicability depends on the workload being horizontally scalable and having sufficient ASG-level metrics.”

This creates alignment between sections.

2. Add one or two sentences about prerequisites

Right now Section 1 explains what predictive scaling is, but not what is required for it to work.
Add a line such as:

“Predictive scaling requires horizontal scalability, ASG metric history, and recurring patterns for the ML model to learn from.”

This prepares the reader for Sections 2 and 5.

3. Add a sentence about “recurring patterns ≠ enough”

Section 5 clarified an important distinction:

Instance-level recurring patterns exist

But predictive scaling needs ASG-level load metrics + horizontal scaling

This nuance is good to mention upfront so the reader doesn’t misunderstand the intent.

Add to Section 1:

“Even if instance-level metrics show recurring patterns, predictive scaling is only viable if the ASG can scale (min < max) and CloudWatch ASG metrics are available.”

4. Minor flow improvement

Section 1 currently mixes:

What predictive scaling is

Benefits

High-level findings

Recommendations

It is clean, but you can tighten the storytelling by adding a transition sentence:

“The sections below evaluate how the feature works, how it compares to existing scaling strategies, and whether it applies to our current workloads.”

This ties the whole document together.

🟢 No Other Sections Require Changes

Section 2 is technically correct and logically complete.

Section 3 compares strategies cleanly.

Section 4 is concise and polished.

Section 5 is well-reasoned and incorporates your real environment constraints.

The document already flows well — adjusting Section 1 is the only meaningful improvement.

✍️ Here is an improved Section 1 (light, minimal, aligned with Section 5)

Use this version if you want full consistency:

1. Overview

What is predictive auto-scaling?
Amazon EC2 Auto Scaling predictive scaling uses machine learning to forecast demand and proactively adjust EC2 capacity. Instead of waiting for CPU or request metrics to rise, the Auto Scaling Group (ASG) launches instances ahead of time so they are fully initialized and healthy before the predicted peak period.

Predictive scaling improves availability during recurring load patterns and works alongside reactive policies for unexpected spikes. It reduces scale-out lag during peak periods, minimizes reliance on scheduled actions, and helps maintain SLO performance. However, forecasts may occasionally over-provision, increasing cost.

Prerequisites and applicability
Predictive scaling requires workloads that can scale horizontally (min < max), stable ASG-level CloudWatch metrics, and recurring demand patterns for the model to learn. Instance-level patterns alone are not sufficient; the ASG must expose the load metric and be capable of adjusting capacity. These constraints are evaluated in Section 5.

Key findings

Most effective for workloads with stable, repeating demand patterns

Requires at least 24 hours of metrics; two weeks recommended for accurate forecasting

Predictive scale-out only; scale-in always handled by reactive policies

Works best when paired with target tracking

Not suitable for workloads with irregular spikes or single-instance architecture

High-level recommendations

Start with Forecast Only mode on a candidate ASG for two weeks

Validate recurring patterns and forecast alignment

If accurate, move to Forecast + Scale combined with existing target tracking

Continuously monitor accuracy, utilization, and cost impact
