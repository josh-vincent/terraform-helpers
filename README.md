# Terraform-helpers

### Remove first item from list
```terraform 
locals {
  items = ["item-0","item-1","item-2", "item-3"]
}

output "removed_first" {
  slice(locals.items, 1, length(locals.items)))
}
```
