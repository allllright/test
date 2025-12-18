Yes — at this point the only change you need is a documentation update, and README.md is exactly the right place to capture this decision.

What you’re doing now is documenting an intentional design choice, not changing code. That’s good engineering.

Below is how to structure the README update, what to say, and why each part matters.

1️⃣ Add a clear “Design decision” section

Create a short, explicit section so reviewers and future users don’t re-open this debate later.

Suggested section title

## Design Decisions 

or, if you want it more explicit:

## Entity Identification Strategy 

2️⃣ Explain why GUIDs are required (this is the key part)

You want to clearly state the risk with names and the behavior of the data source.

Suggested wording (you can copy–paste)

### Why this module requires Entity GUIDs This module requires New Relic **Entity GUIDs** as input instead of entity names. Although using entity names can appear more convenient, entity names in New Relic are **not globally unique**. Even when combined with `domain` and `type`, multiple entities can legitimately share the same name. The `newrelic_entity` data source resolves entities by name using a *best-match* approach: - If multiple entities match the provided search parameters (`name`, `domain`, `type`) - The data source returns **the first matching entity** - There is no mechanism to detect or surface ambiguity This creates a risk of **accidentally tagging the wrong entity**. To avoid this risk and ensure deterministic behavior, this module **only accepts explicit Entity GUIDs**. 

This matches exactly what you proved and discussed.

3️⃣ Clarify responsibility: how users get GUIDs

Make it clear that resolving GUIDs is intentionally outside the module.

### Responsibility for GUID Resolution Resolving entity GUIDs is intentionally left to the caller. GUIDs can be obtained via: - Terraform resources that create New Relic entities - New Relic NerdGraph / API - Existing Terraform data sources used explicitly by the caller - Manual lookup in the New Relic UI (for one-off usage) This separation keeps the module safe, predictable, and suitable for production use. 

This shows you thought about usability without compromising safety.

4️⃣ Add a small usage example reinforcing GUID-first usage

### Example ```hcl module "newrelic_entity_tags" { source = "./modules/newrelic-entity-tags" guids_to_tag = [ newrelic_application.my_app.guid, newrelic_dashboard.my_dashboard.guid, ] entity_tags = { environment = ["production"] team = ["sre"] } } 

This subtly teaches users the right integration pattern.

5️⃣ Optional: explicitly close the door on names (recommended)

This prevents future “why not names?” questions.

> ❗ Entity names are intentionally not supported as inputs. > This avoids ambiguous resolution and accidental tagging of unintended entities. 

Short. Firm. Professional.

6️⃣ What you do NOT need to change

✔ No Terraform code changes
✔ No variable changes
✔ No data sources
✔ No Terratest updates

This is a documentation-only resolution, which is ideal after a design discussion.

Why this is a strong outcome (important for you)

You defended correctness over convenience

You aligned with how New Relic actually behaves

You documented the tradeoff explicitly

You reduced long-term operational risk

This is very solid SRE-level design judgment.


