# Terraform-helpers

### Remove first item from list
```terraform 
locals {
  items = ["item-0","item-1","item-2", "item-3"]
}

output "removed_first" {
  slice(locals.items, 1, length(locals.items)))
}

#result: ["item-1","item-2", "item-3"]
```

### Add tags using yaml file
```yaml
---
cost-centre: "0000000"
environment: "Enviroment-1"
managed-by: "Terraform"
```

```terraform
tags = yamldecode(templatefile("path/to/tags.yaml", {}))
```

### Split text file by new lines 
``` #emails.txt
firstemail@domain.com 
secondemail@domain.com
```

```terraform 
locals {
  email_addresses = split("\n", file("path/to/emails.txt"))
}
```
