# Terraform Interview Questions and Answers

## 1. What is a provider in Terraform?

**Answer:**  
Provider is a plugin that allows Terraform to interact with APIs of cloud platforms, such as AWS, Azure, GCP, Kubernetes, or GitHub.

**Example:**

```hcl
provider "aws" {
  region = "us-west-2"
}
```

---

## 2. What are modules in Terraform?

**Answer:**  
Terraform modules are reusable collections of Terraform configuration files.  
They help organize infrastructure code.

---

## 3. How can you secure the state file in Terraform?

**Answer:**  
Using a remote backend instead of storing it locally.

**Example:**

```hcl
terraform {
  backend "s3" {
    bucket  = "my-terraform-state"
    key     = "prod/terraform.tfstate"
    region  = "us-west-2"
    encrypt = true
  }
}
```

---

## 4. What is the purpose of the terraform init command?

**Answer:**  
`terraform init` initializes a Terraform working directory.

- Downloads required providers.
- Downloads modules.
- Initializes the configured backend.
- Creates the `.terraform` directory.
- Prepares the working directory for Terraform commands.

---

## 5. What does the terraform plan command do?

**Answer:**  
`terraform plan` creates an execution plan and shows what Terraform will create, modify, or destroy.

---

## 6. What is the terraform apply command used for?

**Answer:**  
`terraform apply` executes the Terraform configuration and creates, modifies, or destroys infrastructure according to the execution plan.

---

## 7. What is the purpose of the terraform destroy command?

**Answer:**  
`terraform destroy` removes the infrastructure managed by the current Terraform configuration.

---

## 8. How do you manage different environments (e.g., dev, prod) in Terraform?

**Answer:**  
Terraform provides multiple ways to handle environments.

**a. Directory-based separation:**  
Each directory maintains its own backend configuration and its own variable values.

**b. Terraform Workspaces:**  
Each workspace has its own separate state but shares the same configuration code. By default, Terraform starts with a workspace called `default`.

---

## 9. What is remote state and how do you configure it in Terraform?

**Answer:**  
Remote state means storing the Terraform state file in a remote backend instead of on a developer's local machine.

**Example:**

```hcl
terraform {
  backend "s3" {
    bucket = "my-terraform-state"
    key    = "global/s3/terraform.tfstate"
    region = "us-west-2"
  }
}
```

---

## 10. What are data sources in Terraform?

**Answer:**  
Data sources allow Terraform to fetch data from existing infrastructure or services to use in resource definitions.

---

## 11. Difference between 'local-exec' and "remote-exec" provisioner?

**Answer:**  
The primary difference between the `local-exec` and `remote-exec` provisioners in Terraform is where the command or script is executed.

**a. local-exec:**  
Executes commands on the machine running Terraform, such as your local workstation, laptop, or CI/CD runner.

**b. remote-exec:**  
Connects to the newly provisioned infrastructure, such as an EC2 instance or virtual machine, via SSH or WinRM and executes the commands inside that remote server.

---

## 12. What are provisioners in Terraform?

**Answer:**  
Provisioners are used to execute scripts or commands on a local or remote machine as part of the resource lifecycle.

**Example:**

```hcl
resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"

  provisioner "local-exec" {
    command = "echo ${self.public_ip} > ip_address.txt"
  }
}
```

---

## 13. What is the purpose of the terraform validate command?

**Answer:**  
`terraform validate` checks whether Terraform configuration files are syntactically valid.

---

## 14. How can you format Terraform configuration files?

**Answer:**  
Terraform configuration files can be formatted using the `terraform fmt` command.

---

## 15. What is the difference between 'count' and 'for_each' in Terraform?

**Answer:**  
`count` is used when creating multiple similar resources using a number, while `for_each` is used when creating resources from a map or set.

**Example (count):**

```hcl
resource "aws_instance" "example" {
  count         = 3
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
}
```

**Example (for_each):**

```hcl
resource "aws_instance" "example" {
  for_each      = toset(["instance1", "instance2"])
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"

  tags = {
    Name = each.key
  }
}
```

---

## 16. How do you use loops in Terraform?

**Answer:**  
Terraform supports several looping mechanisms:

- `count`
- `for_each`
- `for`
- Dynamic blocks

---

## 17. What are locals in Terraform and how do you use them?

**Answer:**  
Locals define reusable local values within a Terraform module.  
They improve readability and reduce repetition.

**Example:**

```hcl
locals {
  instance_type = "t2.micro"
  ami_id        = "ami-123456789"
}

resource "aws_instance" "example" {
  ami           = local.ami_id
  instance_type = local.instance_type
}
```

---

## 18. What is the purpose of the terraform taint command?

**Answer:**  
`terraform taint` marked a resource for replacement during the next `terraform apply`.  
The `terraform taint` command is deprecated.

In modern Terraform, `terraform apply -replace` is generally preferred.

**Example:**

```bash
terraform apply -replace="aws_instance.example"
```

---

## 19. How do you manage module versioning in Terraform?

**Answer:**  
For modules hosted on the Terraform Registry, use the `version` argument inside the module block.

**Example:**

```hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "5.1.2"    # Pin to an exact semantic version
}
```

**Exact Pinning:**  
`version = "5.1.2"` ensures that every `terraform init` downloads the exact same code.

**Pessimistic Constraint:**  
`version = "~> 5.1.0"` allows automatic upgrades for minor patches but blocks major, breaking changes.

---

## 20. What is the Terraform Registry?

**Answer:**  
The Terraform Registry is a repository for discovering Terraform providers and reusable modules.  
It allows you to use community and official modules instead of building everything from scratch.

**Example:**

```hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "2.0.0"
}
```

---

## 21. How do you perform a dry run in Terraform?

**Answer:**  
A dry run in Terraform can be performed using the `terraform plan` command. It shows the proposed changes without modifying infrastructure.

**Example:**

```bash
terraform plan
```

---

## 22. What is the terraform state command used for?

**Answer:**  
`terraform state` is used to inspect and manipulate Terraform state.

**Example:**

```bash
terraform state list
terraform state show aws_instance.example
terraform state mv
terraform state rm
```

---

## 23. How do you rename a resource in the state file?

**Answer:**  
You can move the state from the old resource address to the new address.

**Example:**

```bash
terraform state mv aws_instance.old_name aws_instance.new_name
```

---

## 24. What is the purpose of the terraform workspace command?

**Answer:**  
The primary purpose of the `terraform workspace` command group is to manage multiple, isolated state files for a single Terraform configuration, allowing you to deploy the exact same code across different environments like dev, staging, and production.

**Example:**

```bash
terraform workspace new dev
terraform workspace select dev
terraform workspace show
```

---

## 25. What is the difference between local and remote backends in Terraform?

**Answer:**  
Local backends store the state file on the local filesystem, while remote backends store the state file in a remote storage service, such as S3 or Consul.

**Example (local backend):**

```hcl
terraform {
  backend "local" {
    path = "terraform.tfstate"
  }
}
```

**Example (remote backend):**

```hcl
terraform {
  backend "s3" {
    bucket = "my-terraform-state"
    key    = "global/s3/terraform.tfstate"
    region = "us-west-2"
  }
}
```

---

## 26. What is the purpose of the terraform refresh command?

**Answer:**  
`terraform refresh` is deprecated in modern Terraform.

`-refresh-only` is used for resources Terraform already manages, to synchronize the state with changes that happened outside Terraform.

**Example:**  
You created an EC2 (`t2-micro`) instance using Terraform and someone manually changes EC2 (`t3-micro`) from EC2 (`t2-micro`) through the AWS Console.

Now we have:

```text
Terraform code:       t2.micro
Terraform state:      t2.micro
Actual AWS:           t3.micro
```

Terraform state doesn't automatically know about the manual change.

Then run:

```bash
terraform plan -refresh-only
```

Terraform checks the existing resource and says: "The resource I'm already managing has changed outside Terraform."

Then run:

```bash
terraform apply -refresh-only
```

Terraform updates the state file.

**Note:** Terraform did not change the EC2 instance. It only updated its state to accurately represent what already exists.

---

## 27. What is the terraform import command used for?

**Answer:**  
`terraform import` is used to bring an existing resource that Terraform doesn't currently manage into Terraform state.

**Example:**

```bash
terraform import aws_instance.example i-1234567890abcdef0
```

---

## 28. How do you import existing resources into Terraform?

**Answer:**  
Existing resources can be imported using the `terraform import` command, which maps the existing resource to a Terraform resource in the state file.

**Example:**

**Step 1: Define the resource in Terraform**

```hcl
resource "aws_instance" "example" {
  # Configuration
}
```

**Step 2: Import the existing resource**

```bash
terraform import aws_instance.example i-1234567890abcdef0
```

---

## 29. How do you use a lock file in Terraform?

**Answer:**  
A lock file (`.terraform.lock.hcl`) is used to lock versions of the provider. If someone is using it on another machine, they will not get version conflicts.

---

## 30. How do you manage secrets in Terraform?

**Answer:**  
I use `terraform.tfvars` for sensitive values during development. I never commit that file to Git using `.gitignore` and mark variables as sensitive.

In production, I prefer a secret manager like AWS Secrets Manager or HashiCorp Vault, and I secure the Terraform state because it can contain sensitive information.

**Example:**

```hcl
variable "db_password" {
  type      = string
  sensitive = true
}
```

---

## 31. How do you manage Terraform provider versioning?

**Answer:**  
I use provider version constraints in the `required_providers` block inside `provider.tf`.

I check the `.terraform.lock.hcl` file to see what version my project currently uses. If it shows something like `6.27.0`, then I define the constraint as `version = "~> 6.0"`.

**Example:**

```hcl
required_providers {
  aws = {
    source  = "hashicorp/aws"
    version = "~> 6.0"
  }
}
```

---

## 32. What do you do when your Terraform state file becomes too large?

**Answer:**

**a. Use remote state:**  
Store state in a proper remote backend.

**b. Remove resources no longer managed by Terraform:**

**Example:**

```bash
terraform state rm <resource_address>
```

This removes the resource from Terraform state only; it does not destroy the real resource.

---

## 33. What is Terraform state locking?

**Answer:**  
Terraform state locking is usually implemented by using a remote backend that supports locking.

**S3 backend + DynamoDB locking**

```text
S3 → stores the Terraform state
DynamoDB → historically used to provide state locking
```

**Example:**

```hcl
terraform {
  backend "s3" {
    bucket         = "my-terraform-state-bucket"
    key            = "production/terraform.tfstate"
    region         = "ap-south-1"
    dynamodb_table = "terraform-locks"
  }
}
```

---

## 34. How do you prevent DB destroy/recreate downtime in Terraform?

**Answer:**  
I use Terraform's `lifecycle` block to prevent accidental database destruction and avoid unnecessary replacement.

**Example:**

```hcl
resource "aws_db_instance" "mydb" {
  lifecycle {
    prevent_destroy = true
  }
}
```

---

## 35. What is the .terraform directory?

**Answer:**  
The `.terraform` directory is created by `terraform init` and contains downloaded provider plugins, modules, and Terraform's local working data.

I don't commit it to Git because it can be recreated by running `terraform init`.

---

## 36. How do you handle resource renaming without destroying the resource?

**Answer:**  
Add a `moved` block and tell Terraform: `"aws_instance.web"` and `"aws_instance.app"` are the same resource; I only changed its Terraform address.

**Example:**

```hcl
resource "aws_instance" "app" {
  ami           = "ami-0123456789abcdef0"
  instance_type = "t3.micro"

  tags = {
    Name = "my-web-server"
  }
}

moved {
  from = aws_instance.web
  to   = aws_instance.app
}
```