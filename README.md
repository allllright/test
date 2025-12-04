Refactored module code

variables.tf

variable "guids_to_tag" {
  description = "Set of New Relic entity GUIDs to tag"
  type        = set(string)

  validation {
    condition     = length(var.guids_to_tag) > 0
    error_message = "guids_to_tag must contain at least one GUID."
  }

  validation {
    condition = alltrue([
      for g in var.guids_to_tag : length(trimspace(g)) > 10
    ])
    error_message = "Each GUID must be a non-empty string with length > 10."
  }
}

variable "include_default_tags" {
  description = "Whether to include default tags (e.g. AWS tagging standard) when tagging entities."
  type        = bool
  default     = true
}

variable "default_tags" {
  description = "Default tags (usually coming from AWS tagging standard) as key -> set(values)."
  type        = map(set(string))
  default     = {}
}

variable "additional_tags" {
  description = "Extra entity tags to apply on top of default_tags."
  type        = map(set(string))
  default     = {}
}

> Note: I added default_tags as a separate variable – the module itself can’t see the caller’s local.tags, so we pass them in explicitly.




---

main.tf

terraform {
  required_providers {
    newrelic = {
      source  = "newrelic/newrelic"
      version = "~> 3.25"
    }
  }
}

locals {
  # Decide which base tags to start from
  base_tags = var.include_default_tags ? var.default_tags : {}

  # Final tag set used for newrelic_entity_tags
  entity_tags = merge(
    base_tags,
    var.additional_tags,
  )
}

resource "newrelic_entity_tags" "entity_tags" {
  for_each = var.guids_to_tag
  guid     = each.value

  dynamic "tag" {
    for_each = local.entity_tags
    content {
      key    = tag.key
      values = tolist(tag.value) # tag.value is a set(string)
    }
  }
}

outputs.tf (your existing idea still works)

output "total_entities_tagged" {
  description = "Total number of entities tagged"
  value       = length(newrelic_entity_tags.entity_tags)
}

output "tagged_entities" {
  description = "Map of GUIDs and their applied tags + resource ID"
  value = {
    for guid, res in newrelic_entity_tags.entity_tags :
    guid => {
      resource_id = res.id
      # Just echo back what we applied
      applied_tags = local.entity_tags
    }
  }
}


---

3. Updated usage in the consumer repo

This replaces the code you showed in the screenshot.

locals {
  # 1) Default / AWS-style tags (from the tagging standard doc)
  default_tags = {
    Environment    = toset(["Test"])
    Layer          = toset(["900"])
    Terraform      = toset(["True"])
    Project        = toset(["Test"])
    Repo           = toset(["https://gitlab.xm.com/rackspace/xm/..."])
    Owner          = toset(["SRE"])
    Team           = toset(["SRE"])
    BusinessStream = toset(["Funding"])
    Brand          = toset(["XM"])
  }

  # 2) Extra / New Relic–specific tags (optional)
  additional_entity_tags = {
    newrelicAccount = toset(["test_account"])
    # If you want multiple values:
    # Environment = toset(["Production", "Staging"])
  }

  guids_to_tag = toset([
    "NZIwNTMXMxFWFR8U0VSVk1DRV9MRVZFTHWzNzA3NZY",
    "NZIwNTMXMxBUE180VBQTElDQVRJT058NDUyODE5NZYy",
    "NZIwNTMXMxFWFR8U0VSVk1DRV9MRVZFTHWzNzU5MTc",
  ])
}

module "newrelic_entity_tags" {
  source = "../modules/newrelic-entitytags"

  guids_to_tag        = local.guids_to_tag
  include_default_tags = true      # or false if you want *only* additional tags
  default_tags         = local.default_tags
  additional_tags      = local.additional_entity_tags
}

