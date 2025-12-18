## Design Decisions

### Why this module requires Entity GUIDs

This module requires **New Relic Entity GUIDs** as input instead of entity names.

Although entity names can be more convenient, **entity names in New Relic are not globally unique**.
Even when combined with `domain` and `type`, multiple entities can legitimately share the same name
(e.g. multiple APM services, dashboards, or synthetics created independently).

The `newrelic_entity` data source resolves entities by name using a *best-match* strategy:
- It searches by the provided parameters (`name`, `domain`, `type`)
- If multiple entities match, **the first matching entity is returned**
- There is **no mechanism to detect, surface, or fail on ambiguity**

This creates a risk of **accidentally tagging the wrong entity**, which is unacceptable for
infrastructure and observability automation.

To avoid this risk and ensure deterministic behavior, this module **only accepts explicit Entity GUIDs**.

---

### Responsibility for GUID resolution

Resolving entity GUIDs is **intentionally left to the caller**.

Entity GUIDs can be obtained via:
- Terraform resources that create New Relic entities
- Explicit Terraform data sources used by the caller
- New Relic NerdGraph / API
- Manual lookup in the New Relic UI (for one-off usage)

By separating **entity identification** from **entity tagging**, the module remains:
- Safe
- Predictable
- Deterministic
- Suitable for production use

---

### Explicit non-goals

This module **does not** attempt to:
- Resolve entity GUIDs from names automatically
- Detect or handle ambiguous name matches
- Guess which entity should be tagged

These concerns are intentionally out of scope to prevent accidental mis-tagging.
