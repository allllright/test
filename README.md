You are a senior Terraform engineer.

I have a Terraform module that manages New Relic entity tags.  
The original implementation is:

- Inputs:
  - `guids_to_tag` (set(string)) – GUIDs of New Relic entities.
  - `entity_tags` (map(set(string))) – final tag set already merged by the caller.
- Behaviour:
  - `main.tf` defines a `newrelic_entity_tags` resource with:
    - `for_each = var.guids_to_tag`
    - a `dynamic "tag"` block that iterates over `var.entity_tags`.
  - All merge/combining logic (AWS default tags + NR-specific tags) happens **outside** the module in the caller’s code.

We’ve decided to refactor this to make the module API cleaner and move the merge logic *inside* the module.

### Target design

Refactor the module so that:

1. **Inputs become:**
   - `guids_to_tag` – `set(string)` (unchanged).
   - `include_default_tags` – `bool`, default `true`.
   - `default_tags` – `map(set(string))`, default `{}`.
   - `additional_tags` – `map(set(string))`, default `{}`.

2. **Inside the module:**
   - Introduce a `local.base_tags` that is:
     - `var.default_tags` if `var.include_default_tags` is `true`,
     - `{}` if `var.include_default_tags` is `false`.
   - Introduce `local.entity_tags = merge(local.base_tags, var.additional_tags)`.
   - Update `newrelic_entity_tags` to iterate over `local.entity_tags` instead of `var.entity_tags`.

3. **Outputs (optional but nice to keep):**
   - `total_entities_tagged` – number of resources created.
   - `tagged_entities` – map of GUID → `{ resource_id, applied_tags }`, where `applied_tags` is `local.entity_tags`.

4. **Caller-side usage should look like:**
   - Caller defines:
     - `local.default_tags` (AWS-style tags from the tagging standard).
     - `local.additional_entity_tags` (New Relic–specific extras).
     - `local.guids_to_tag`.
   - Then calls:
     ```hcl
     module "newrelic_entity_tags" {
       source             = "../modules/newrelic-entitytags"
       guids_to_tag       = local.guids_to_tag
       include_default_tags = true    # or false
       default_tags         = local.default_tags
       additional_tags      = local.additional_entity_tags
     }
     ```

### What I need from you

1. Rewrite the module (`variables.tf`, `main.tf`, `outputs.tf`) from the original design to the new one described above.
2. Make sure the code is valid HCL, using `map(set(string))` for tags, and explain any important design choices briefly in comments.
3. Keep the behaviour backwards-compatible where possible, but prioritize the new API shape: `include_default_tags`, `default_tags`, `additional_tags`.
