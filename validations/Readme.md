## Strings
### String may not contain character

Scenario: String may not contain a /.
```hcl
variable "string_may_not_contain" {
  type = string
  default = "test"

  validation {
    error_message = "Value cannot contain a \"/\"."
    condition = !can(regex("/", var.string_may_not_contain))
  }
}
```

### Url 
Scenario: String must be a valid url
```hcl
variable "string_contains_url" {
  type = string
  description = "String must be a valid URL"
  validation {
    error_message = "Must be valid url."
    condition     = can(regex("^(?:(?P<scheme>[^:/?#]+):)?(?://(?P<authority>[^/?#]*))?", var.string_contains_url))
  }
}
```

### String with valid options

Scenario: Here we have a string and we only allow to values "approved" or "disapproved". I show 2 examples of the same check using different methods:
```hcl
variable "string_only_valid_options" {
  type = string
  default = "approved"

  # using regex
  validation {
    condition     = can(regex("^(approved|disapproved)$", var.string_only_valid_options))
    error_message = "Invalid input, options: \"approved\", \"disapproved\"."
  }

  # using contains()
  validation {
    condition     = contains(["approved", "disapproved"], var.string_only_valid_options)
    error_message = "Invalid input, options: \"approved\", \"disapproved\"."
  }
}
```

### Valid AWS Region Name

Scenario: string must be like AWS region
```hcl
variable "string_like_aws_region" {
  type = string
  default = "us-east-1"

  validation {
    condition     = can(regex("[a-z][a-z]-[a-z]+-[1-9]", var.string_like_aws_region))
    error_message = "Must be valid AWS Region names."
  }
```
### Valid IAM ARN

Scenario: Your string must be a valid IAM role
```hcl
variable "string_valid_iam_role_name" {
    type = string
    default = "arn:aws:iam::123456789012:role/MyCoolRole"

    validation {
      condition     = can(regex("^[a-zA-Z][a-zA-Z\-\_0-9]{1,64}$", var.string_valid_iam_role_name))
      error_message = "IAM role name must start with letter, only contain letters, numbers, dashes, or underscores and must be between 1 and 64 characters"
    }
}
```
### Valid IPv4 CIDR

Scenario: Your string input needs to look like a IPv4 CIDR.
```hcl
variable "string_like_valid_ipv4_cidr" {
  type    = string
  default = "10.0.0.0/16"

  validation {
    condition     = can(cidrhost(var.string_like_valid_ipv4_cidr, 32))
    error_message = "Must be valid IPv4 CIDR."
  }
}
```
## Maps
### Map with optional conflicting keys

Scenario: You have a map variable and 2 keys conflict, in this case, you can only set either cidr or netmask.
```hcl
variable "only_one_optional_key" {
    type = object({
        name = optional(string)
        cidrs = optional(list(string))
        netmask = optional(number)
    })

    default = {
        cidr = "10.0.0.0/16"
        name = "test"
    }

    validation {
        error_message = "Can only specify either \"cidrs\", or \"netmask\"."
        condition = length(setintersection(keys(var.only_one_optional_key), ["cidrs", "netmask"])) == 1
    }
}
```

## Numbers
### Number within a range

Scenario: number must be between 1-16.

```hcl
variable "num_in_range" {
  type        = number
  default     = 1

  validation {
    condition     = var.num_in_range >= 1 && var.num_in_range <= 16 && floor(var.num_in_range) == var.num_in_range
    error_message = "Accepted values: 1-16."
  }
}
```

