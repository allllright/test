
## Summary

Implement a reusable Terraform module to apply New Relic entity tags to one or more entities (APM, dashboards, SLOs, etc.) by GUID.

## What this MR includes

- `main.tf`, `variables.tf`, `outputs.tf`, `versions.tf` for the `newrelic_entity_tags` module
- Examples:
  - `examples/01-include-defaults`
  - `examples/02-exclude-defaults`
- `terratest/` integration test for the module

## Design

- Input:
  - `guids_to_tag` (set(string)) – one or more entity GUIDs
  - `include_default_entity_tags` (bool) – whether to merge default tags
  - `default_entity_tags` (map(string)) – AWS-style defaults, converted to `map(set(string))`
  - `additional_entity_tags` (map(set(string))) – NR-specific / per-use tags
- Module normalises tags to New Relic format (`map(set(string))`) and applies them via `newrelic_entity_tags` with `for_each`.

## Local checks

- `terraform fmt -recursive`
- `terraform init -backend=false`
- `terraform validate`
- `tflint`
- `terraform-docs markdown table . > README.md`
- `pre-commit run --all-files -c ~/pre-commit-config/pre-commit-config.yaml`
  - All hooks reported “no files to check / skipped” because this module repo does not use the `layers/` structure expected by the shared config.
- `cd terratest && go test -v ./...` (passes against sandbox New Relic account)

## Notes / Questions

- Open to feedback on:
  - variable naming (`include_default_entity_tags`, `additional_entity_tags`)
  - whether we want to keep `outputs.tf` in the module or drop it
