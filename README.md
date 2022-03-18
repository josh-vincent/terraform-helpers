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

### For_each excluding first item 
```terraform
locals {
  items = ["item-0","item-1","item-2", "item-3"]
}

data "test_block" "this" {
  for_each          = toset(slice(items.users, 1, length(items.users)))
  #...
}
```

### Add tags using yaml file
```yaml
# path/to/tags.yaml
---
cost-centre: "0000000"
environment: "Enviroment-1"
managed-by: "Terraform"
```

```terraform
tags = yamldecode(templatefile("path/to/tags.yaml", {}))
```

### Split text file by new lines 
``` 
# path/to/emails.txt
firstemail@domain.com 
secondemail@domain.com
```

```terraform 
locals {
  email_addresses = split("\n", file("path/to/emails.txt"))
}
```

### Create RSA Keys 
```
$ ssh-keygen -m PEM -t rsa -b 4096
# Enter file in which to save the key (/Users/Username/.ssh/id_rsa): /Users/Username/.ssh/NEW_KEYNAME_HERE
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 

Go to path where you saved your key: /Users/Username/.ssh/NEW_KEYNAME_HERE
Copy the public key or NEW_KEYNAME_HERE.pub
```

### Aws Keypair creation 
```
application 
  |- files 
  |   |- keypairs
  |   |  |- key1.pub 
  |   |  |- key2.pub 
  |   |  |_ etc....
  |   |
  |   |- tags 
  |     |_ tags.yaml 
  |
  |- keypairs.tf
  |- providers.tf 
  |- terraform.tfvars
  |- variables.tf
```

```terraform 
resource "aws_key_pair" "ec2-keypair" {
  for_each =  fileset(path.module,"files/keypairs/*pub")
  key_name   = basename(trimsuffix(each.key,".pub"))
  public_key = file(each.key)
  tags       =  yamldecode(templatefile("files/tags/tags.yaml",{}))
}
