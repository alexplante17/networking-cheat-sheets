# Terraform Cheat Sheet
Key Terraform Concepts:

Infrastructure as Code (IaC): Declarative description of infrastructure using HCL (HashiCorp Configuration Language)
Providers: Plugins that interact with cloud platforms, services, or APIs
Resources: Infrastructure objects managed by Terraform
State: Terraform's database of managed infrastructure
Variables: Reusable values in configurations
Outputs: Values exported by Terraform for use elsewhere
Modules: Reusable infrastructure components
Workspaces: Separate state environments

Terraform vs. Ansible Comparison:

Purpose:

Terraform: Infrastructure provisioning
Ansible: Configuration management and application deployment


Approach:

Terraform: Declarative (what you want)
Ansible: Procedural (how to do it)


State Management:

Terraform: Maintains state file
Ansible: Stateless


Primary Use Cases:

Terraform: Creating/modifying infrastructure
Ansible: Configuring existing systems


Idempotency:

Terraform: Built-in state comparison
Ansible: Module-dependent

## Installation & Setup
```bash
# Install Terraform
brew install terraform  # macOS
choco install terraform # Windows

# Initialize working directory
terraform init

# Check version
terraform version

# Format code
terraform fmt
```

## Basic Structure
```hcl
# Provider configuration
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.0"
    }
  }
}

# Provider block
provider "aws" {
  region = "us-west-2"
}

# Resource block
resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  
  tags = {
    Name = "example-instance"
  }
}
```

## Variables and Outputs
```hcl
# Variable definitions (variables.tf)
variable "region" {
  description = "AWS region"
  type        = string
  default     = "us-west-2"
}

variable "instance_tags" {
  description = "Tags for EC2 instance"
  type        = map(string)
  default = {
    Environment = "dev"
    Team        = "infrastructure"
  }
}

# Using variables
resource "aws_instance" "example" {
  ami           = var.ami_id
  instance_type = var.instance_type
  tags          = var.instance_tags
}

# Output definitions (outputs.tf)
output "instance_ip" {
  description = "Public IP of EC2 instance"
  value       = aws_instance.example.public_ip
}
```

## State Management
```bash
# Show current state
terraform show

# List resources in state
terraform state list

# Remove resource from state
terraform state rm aws_instance.example

# Move resource in state
terraform state mv aws_instance.example aws_instance.new_name

# Import existing resource
terraform import aws_instance.example i-1234567890abcdef0

# Push state to remote backend
terraform push

# Refresh state
terraform refresh
```

## Common Commands
```bash
# Initialize directory
terraform init

# Preview changes
terraform plan

# Apply changes
terraform apply

# Destroy infrastructure
terraform destroy

# Validate configuration
terraform validate

# Show providers
terraform providers

# Format code
terraform fmt

# Show output values
terraform output
```

## Data Sources
```hcl
# Query existing resources
data "aws_vpc" "existing" {
  id = "vpc-1234567890"
}

# Use data source
resource "aws_subnet" "example" {
  vpc_id     = data.aws_vpc.existing.id
  cidr_block = "10.0.1.0/24"
}
```

## Modules
```hcl
# Module structure
modules/
  vpc/
    main.tf
    variables.tf
    outputs.tf
    README.md

# Module usage
module "vpc" {
  source = "./modules/vpc"
  
  vpc_cidr        = "10.0.0.0/16"
  public_subnets  = ["10.0.1.0/24", "10.0.2.0/24"]
  private_subnets = ["10.0.3.0/24", "10.0.4.0/24"]
}

# Reference module outputs
output "vpc_id" {
  value = module.vpc.vpc_id
}
```

## Workspaces
```bash
# List workspaces
terraform workspace list

# Create workspace
terraform workspace new dev

# Select workspace
terraform workspace select prod

# Show current workspace
terraform workspace show
```

## Backend Configuration
```hcl
# S3 backend
terraform {
  backend "s3" {
    bucket         = "terraform-state"
    key            = "prod/terraform.tfstate"
    region         = "us-west-2"
    encrypt        = true
    dynamodb_table = "terraform-locks"
  }
}
```

## Provisioners
```hcl
# Local exec provisioner
resource "aws_instance" "example" {
  # ... other configuration ...

  provisioner "local-exec" {
    command = "echo ${aws_instance.example.private_ip} >> private_ips.txt"
  }
}

# Remote exec provisioner
resource "aws_instance" "example" {
  # ... other configuration ...

  provisioner "remote-exec" {
    inline = [
      "sudo apt-get update",
      "sudo apt-get install -y nginx"
    ]
  }
}
```

## Count and For Each
```hcl
# Count example
resource "aws_instance" "server" {
  count = 3
  
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  
  tags = {
    Name = "server-${count.index}"
  }
}

# For each example
resource "aws_instance" "server" {
  for_each = {
    web  = "t2.micro"
    api  = "t2.small"
    db   = "t2.medium"
  }
  
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = each.value
  
  tags = {
    Name = "server-${each.key}"
  }
}
```

## Best Practices
1. Use version constraints for providers
2. Store state remotely with encryption
3. Use workspaces for environment separation
4. Implement proper state locking
5. Use data sources for existing resources
6. Structure code using modules
7. Use variables for reusability
8. Document configurations
9. Use consistent naming conventions
10. Implement proper access controls

## Debugging
```bash
# Enable verbose logging
export TF_LOG=DEBUG

# Set log file
export TF_LOG_PATH=terraform.log

# Plan with detailed exit code
terraform plan -detailed-exitcode
```
