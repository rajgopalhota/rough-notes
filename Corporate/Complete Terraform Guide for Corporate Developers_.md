<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Complete Terraform Guide for Corporate Developers: From Beginner to Advanced

## Table of Contents

1. [What is Terraform?](#what-is-terraform)
2. [Core Concepts](#core-concepts)
3. [Installation and Setup](#installation-and-setup)
4. [Basic Terraform Commands](#basic-terraform-commands)
5. [Configuration Language (HCL)](#configuration-language-hcl)
6. [State Management](#state-management)
7. [Modules and Reusability](#modules-and-reusability)
8. [Provider Management](#provider-management)
9. [Advanced Features](#advanced-features)
10. [Corporate Workflows](#corporate-workflows)
11. [Best Practices](#best-practices)
12. [Common Corporate Scenarios](#common-corporate-scenarios)
13. [Troubleshooting](#troubleshooting)
14. [Cheatsheet](#cheatsheet)

## What is Terraform?

Terraform is an **Infrastructure as Code (IaC)** tool created by HashiCorp that allows you to define, provision, and manage cloud and on-premises infrastructure using a declarative configuration language called HashiCorp Configuration Language (HCL).

**Simple Analogy**: Think of Terraform as an architect's blueprint for your digital infrastructure. Instead of manually building each component (like a server or database), you write a plan, and Terraform constructs everything according to your specifications.

### Why Use Terraform in Corporate Environment?

- **Consistency**: Same infrastructure across dev, staging, and production
- **Version Control**: Infrastructure changes tracked like code
- **Collaboration**: Teams can work together on infrastructure
- **Automation**: Reduces manual errors and deployment time
- **Multi-Cloud**: Works with AWS, Azure, GCP, and 100+ providers
- **Cost Management**: Better resource tracking and optimization


## Core Concepts

### 1. Infrastructure as Code (IaC)

Instead of clicking through web consoles, you write configuration files that describe your desired infrastructure state.

**Traditional Approach**:

```
1. Log into AWS Console
2. Click "Launch Instance"
3. Select AMI, instance type, etc.
4. Manually configure security groups
5. Repeat for each environment
```

**Terraform Approach**:

```hcl
resource "aws_instance" "web_server" {
  ami           = "ami-0c94855ba95c71c99"
  instance_type = "t3.micro"
  
  tags = {
    Name = "WebServer"
    Environment = "Production"
  }
}
```


### 2. Declarative vs Imperative

**Declarative** (Terraform): You describe **what** you want

```hcl
# I want 3 web servers
resource "aws_instance" "web" {
  count = 3
  # configuration...
}
```

**Imperative** (Traditional scripting): You describe **how** to achieve it

```bash
# Create first server
aws ec2 run-instances --image-id ami-123 --count 1
# Create second server
aws ec2 run-instances --image-id ami-123 --count 1
# Create third server
aws ec2 run-instances --image-id ami-123 --count 1
```


### 3. Key Components

| Component | Purpose | Example |
| :-- | :-- | :-- |
| **Provider** | Integrates with cloud platforms | AWS, Azure, GCP |
| **Resource** | Infrastructure component | EC2 instance, S3 bucket |
| **Data Source** | Read existing infrastructure | Existing VPC, AMI |
| **Variable** | Parameterize configurations | Environment names, sizes |
| **Output** | Return values after creation | IP addresses, URLs |
| **Module** | Reusable configuration packages | VPC module, database module |

## Installation and Setup

### 1. Install Terraform

**Windows (Using Chocolatey)**:

```cmd
choco install terraform
```

**macOS (Using Homebrew)**:

```bash
brew install terraform
```

**Linux (Ubuntu/Debian)**:

```bash
wget https://releases.hashicorp.com/terraform/1.6.0/terraform_1.6.0_linux_amd64.zip
unzip terraform_1.6.0_linux_amd64.zip
sudo mv terraform /usr/local/bin/
```

**Verify Installation**:

```bash
terraform version
```


### 2. IDE Setup

**VS Code Extensions**:

- HashiCorp Terraform
- Terraform doc snippets

**Configuration**:

```json
{
  "terraform.languageServer.enable": true,
  "terraform.validation.enable": true,
  "terraform.format.enable": true
}
```


### 3. Provider Authentication

**AWS**:

```bash
# Using AWS CLI
aws configure

# Using environment variables
export AWS_ACCESS_KEY_ID="your-access-key"
export AWS_SECRET_ACCESS_KEY="your-secret-key"
export AWS_DEFAULT_REGION="us-west-2"
```

**Azure**:

```bash
# Using Azure CLI
az login

# Using service principal
export ARM_CLIENT_ID="your-client-id"
export ARM_CLIENT_SECRET="your-client-secret"
export ARM_SUBSCRIPTION_ID="your-subscription-id"
export ARM_TENANT_ID="your-tenant-id"
```


## Basic Terraform Commands

### Essential Command Workflow

```bash
# 1. Initialize Terraform (downloads providers)
terraform init

# 2. Validate configuration syntax
terraform validate

# 3. Format code consistently
terraform fmt

# 4. Preview changes
terraform plan

# 5. Apply changes
terraform apply

# 6. Destroy infrastructure
terraform destroy
```


### Detailed Command Examples

#### Initialize Project

```bash
# Basic initialization
terraform init

# Upgrade providers to latest versions
terraform init -upgrade

# Initialize with specific backend
terraform init -backend-config="bucket=my-terraform-state"
```


#### Planning Changes

```bash
# Basic plan
terraform plan

# Save plan to file
terraform plan -out=tfplan

# Plan with variable file
terraform plan -var-file="production.tfvars"

# Plan for specific resource
terraform plan -target=aws_instance.web_server
```


#### Applying Changes

```bash
# Apply with confirmation prompt
terraform apply

# Apply saved plan
terraform apply tfplan

# Apply without confirmation (dangerous)
terraform apply -auto-approve

# Apply with specific variables
terraform apply -var="instance_count=5"
```


#### State Management

```bash
# Show current state
terraform show

# List resources in state
terraform state list

# Show specific resource
terraform state show aws_instance.web_server

# Import existing resource
terraform import aws_instance.web_server i-1234567890abcdef0

# Remove resource from state (doesn't destroy)
terraform state rm aws_instance.web_server
```


## Configuration Language (HCL)

### 1. Basic Syntax

**File Structure**:

```hcl
# Terraform configuration files end with .tf
# main.tf - Primary configuration
# variables.tf - Variable definitions
# outputs.tf - Output definitions
# terraform.tf - Terraform and provider requirements
```

**Comments**:

```hcl
# Single line comment
// Another single line comment

/*
Multi-line comment
Used for documentation blocks
*/
```


### 2. Terraform Block

```hcl
terraform {
  # Terraform version constraint
  required_version = ">= 1.0"
  
  # Provider requirements
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
    random = {
      source  = "hashicorp/random"
      version = "~> 3.1"
    }
  }
  
  # Backend configuration
  backend "s3" {
    bucket = "my-terraform-state"
    key    = "prod/terraform.tfstate"
    region = "us-west-2"
  }
}
```


### 3. Provider Configuration

```hcl
# AWS Provider
provider "aws" {
  region  = "us-west-2"
  profile = "production"
  
  # Default tags for all resources
  default_tags {
    tags = {
      Project     = "MyApp"
      Environment = "Production"
      ManagedBy   = "Terraform"
    }
  }
}

# Multiple provider instances
provider "aws" {
  alias  = "east"
  region = "us-east-1"
}

# Using aliased provider
resource "aws_s3_bucket" "east_bucket" {
  provider = aws.east
  bucket   = "my-east-coast-bucket"
}
```


### 4. Resources

**Basic Resource Syntax**:

```hcl
resource "resource_type" "resource_name" {
  argument1 = "value1"
  argument2 = "value2"
  
  # Nested block
  nested_block {
    setting = "value"
  }
}
```

**Real Examples**:

```hcl
# AWS EC2 Instance
resource "aws_instance" "web_server" {
  ami                    = "ami-0c94855ba95c71c99"
  instance_type          = "t3.micro"
  key_name              = "my-key-pair"
  vpc_security_group_ids = [aws_security_group.web_sg.id]
  subnet_id             = aws_subnet.public_subnet.id
  
  user_data = <<-EOF
    #!/bin/bash
    yum update -y
    yum install -y httpd
    systemctl start httpd
    systemctl enable httpd
    echo "<h1>Hello from Terraform!</h1>" > /var/www/html/index.html
  EOF
  
  tags = {
    Name = "WebServer"
    Type = "Frontend"
  }
}

# AWS S3 Bucket with versioning
resource "aws_s3_bucket" "data_bucket" {
  bucket = "my-company-data-${random_id.bucket_suffix.hex}"
}

resource "aws_s3_bucket_versioning" "data_bucket_versioning" {
  bucket = aws_s3_bucket.data_bucket.id
  versioning_configuration {
    status = "Enabled"
  }
}

# Random ID for unique naming
resource "random_id" "bucket_suffix" {
  byte_length = 4
}
```


### 5. Data Sources

Data sources allow you to fetch information about existing infrastructure:

```hcl
# Get latest Amazon Linux AMI
data "aws_ami" "amazon_linux" {
  most_recent = true
  owners      = ["amazon"]
  
  filter {
    name   = "name"
    values = ["amzn2-ami-hvm-*-x86_64-gp2"]
  }
}

# Get current AWS region
data "aws_region" "current" {}

# Get current AWS account ID
data "aws_caller_identity" "current" {}

# Use data in resource
resource "aws_instance" "example" {
  ami           = data.aws_ami.amazon_linux.id
  instance_type = "t3.micro"
  
  tags = {
    Region    = data.aws_region.current.name
    AccountId = data.aws_caller_identity.current.account_id
  }
}
```


### 6. Variables

**Variable Definition** (`variables.tf`):

```hcl
# String variable
variable "environment" {
  description = "Environment name (dev, staging, prod)"
  type        = string
  default     = "dev"
}

# Number variable
variable "instance_count" {
  description = "Number of EC2 instances"
  type        = number
  default     = 1
  
  validation {
    condition     = var.instance_count >= 1 && var.instance_count <= 10
    error_message = "Instance count must be between 1 and 10."
  }
}

# List variable
variable "availability_zones" {
  description = "List of availability zones"
  type        = list(string)
  default     = ["us-west-2a", "us-west-2b"]
}

# Map variable
variable "instance_types" {
  description = "Instance types for different environments"
  type        = map(string)
  default = {
    dev     = "t3.micro"
    staging = "t3.small"
    prod    = "t3.medium"
  }
}

# Object variable
variable "database_config" {
  description = "Database configuration"
  type = object({
    engine         = string
    engine_version = string
    instance_class = string
    allocated_storage = number
  })
  default = {
    engine         = "mysql"
    engine_version = "8.0"
    instance_class = "db.t3.micro"
    allocated_storage = 20
  }
}

# Sensitive variable
variable "db_password" {
  description = "Database password"
  type        = string
  sensitive   = true
}
```

**Using Variables**:

```hcl
resource "aws_instance" "web" {
  count         = var.instance_count
  ami           = data.aws_ami.amazon_linux.id
  instance_type = var.instance_types[var.environment]
  
  tags = {
    Name        = "web-${var.environment}-${count.index + 1}"
    Environment = var.environment
  }
}
```

**Providing Variable Values**:

**Command Line**:

```bash
terraform apply -var="environment=prod" -var="instance_count=3"
```

**Variable Files** (`terraform.tfvars`):

```hcl
environment    = "production"
instance_count = 5
availability_zones = ["us-west-2a", "us-west-2b", "us-west-2c"]

database_config = {
  engine         = "postgres"
  engine_version = "14.9"
  instance_class = "db.r6g.large"
  allocated_storage = 100
}
```

**Environment-specific files**:

```bash
# development.tfvars
environment = "dev"
instance_count = 1

# production.tfvars
environment = "prod"
instance_count = 5
```


### 7. Outputs

**Output Definition** (`outputs.tf`):

```hcl
# Simple output
output "instance_ip" {
  description = "Public IP address of the web server"
  value       = aws_instance.web_server.public_ip
}

# Sensitive output
output "database_endpoint" {
  description = "Database endpoint"
  value       = aws_db_instance.main.endpoint
  sensitive   = true
}

# Complex output
output "load_balancer_info" {
  description = "Load balancer information"
  value = {
    dns_name = aws_lb.main.dns_name
    zone_id  = aws_lb.main.zone_id
    arn      = aws_lb.main.arn
  }
}

# List output
output "instance_ips" {
  description = "IP addresses of all web servers"
  value       = aws_instance.web[*].public_ip
}
```

**Using Outputs**:

```bash
# View all outputs
terraform output

# View specific output
terraform output instance_ip

# Output in JSON format
terraform output -json
```


### 8. Local Values

Local values help avoid repetition and make configurations more readable:

```hcl
locals {
  # Common tags
  common_tags = {
    Project     = "MyApplication"
    Environment = var.environment
    ManagedBy   = "Terraform"
    Team        = "DevOps"
  }
  
  # Naming convention
  name_prefix = "${var.project_name}-${var.environment}"
  
  # Conditional logic
  instance_type = var.environment == "prod" ? "t3.large" : "t3.micro"
  
  # Complex expressions
  subnets = {
    public = [
      for idx, az in var.availability_zones : {
        name              = "${local.name_prefix}-public-${idx + 1}"
        cidr_block        = cidrsubnet(var.vpc_cidr, 8, idx)
        availability_zone = az
      }
    ]
    private = [
      for idx, az in var.availability_zones : {
        name              = "${local.name_prefix}-private-${idx + 1}"
        cidr_block        = cidrsubnet(var.vpc_cidr, 8, idx + 10)
        availability_zone = az
      }
    ]
  }
}

# Using local values
resource "aws_instance" "web" {
  ami           = data.aws_ami.amazon_linux.id
  instance_type = local.instance_type
  
  tags = merge(local.common_tags, {
    Name = "${local.name_prefix}-web-server"
    Type = "WebServer"
  })
}
```


## State Management

### Understanding Terraform State

Terraform state is a **JSON file** that maps your configuration to real-world resources. It's Terraform's way of keeping track of what it has created.

**State Contains**:

- Resource metadata
- Resource dependencies
- Current configuration snapshot
- Performance optimization data


### 1. Local State (Default)

**Location**: `terraform.tfstate` in your project directory

**Pros**: Simple, no setup required
**Cons**: Not suitable for teams, no collaboration, single point of failure

```bash
# View state
terraform show

# List resources
terraform state list

# Get specific resource info
terraform state show aws_instance.web_server
```


### 2. Remote State (Corporate Standard)

Remote state enables team collaboration and provides backup.

#### S3 Backend (Most Popular)

**Backend Configuration**:

```hcl
# terraform.tf
terraform {
  backend "s3" {
    bucket         = "my-company-terraform-state"
    key            = "prod/web-app/terraform.tfstate"
    region         = "us-west-2"
    encrypt        = true
    dynamodb_table = "terraform-locks"
    
    # Optional: Role assumption for cross-account access
    role_arn = "arn:aws:iam::123456789012:role/TerraformRole"
  }
}
```

**Setup State Bucket and DynamoDB Table**:

```hcl
# state-backend.tf (run this first)
resource "aws_s3_bucket" "terraform_state" {
  bucket = "my-company-terraform-state"
}

resource "aws_s3_bucket_versioning" "terraform_state" {
  bucket = aws_s3_bucket.terraform_state.id
  versioning_configuration {
    status = "Enabled"
  }
}

resource "aws_s3_bucket_server_side_encryption_configuration" "terraform_state" {
  bucket = aws_s3_bucket.terraform_state.id

  rule {
    apply_server_side_encryption_by_default {
      sse_algorithm = "AES256"
    }
  }
}

resource "aws_dynamodb_table" "terraform_locks" {
  name           = "terraform-locks"
  billing_mode   = "PAY_PER_REQUEST"
  hash_key       = "LockID"

  attribute {
    name = "LockID"
    type = "S"
  }

  tags = {
    Name = "TerraformStateLock"
  }
}
```


#### Azure Backend

```hcl
terraform {
  backend "azurerm" {
    resource_group_name  = "terraform-rg"
    storage_account_name = "terraformstatestorage"
    container_name       = "tfstate"
    key                  = "prod.terraform.tfstate"
  }
}
```


#### Google Cloud Backend

```hcl
terraform {
  backend "gcs" {
    bucket = "my-terraform-state-bucket"
    prefix = "terraform/state"
  }
}
```


### 3. State Commands

```bash
# Initialize with remote backend
terraform init

# Pull remote state to local
terraform state pull

# Push local state to remote
terraform state push terraform.tfstate

# Lock state (manual - usually automatic)
terraform force-unlock LOCK_ID

# Import existing resource
terraform import aws_instance.web i-1234567890abcdef0

# Move resource in state
terraform state mv aws_instance.web aws_instance.web_server

# Remove resource from state (doesn't destroy)
terraform state rm aws_instance.web

# Replace resource (destroy and recreate)
terraform apply -replace=aws_instance.web
```


### 4. State File Structure

**Example State Snippet**:

```json
{
  "version": 4,
  "terraform_version": "1.6.0",
  "serial": 1,
  "lineage": "12345678-1234-1234-1234-123456789012",
  "outputs": {},
  "resources": [
    {
      "mode": "managed",
      "type": "aws_instance",
      "name": "web_server",
      "provider": "provider[\"registry.terraform.io/hashicorp/aws\"]",
      "instances": [
        {
          "schema_version": 1,
          "attributes": {
            "ami": "ami-0c94855ba95c71c99",
            "instance_type": "t3.micro",
            "public_ip": "54.123.45.67"
          }
        }
      ]
    }
  ]
}
```


### 5. Workspace Management

Workspaces allow you to manage multiple environments with the same configuration:

```bash
# List workspaces
terraform workspace list

# Create new workspace
terraform workspace new staging

# Switch workspace
terraform workspace select production

# Show current workspace
terraform workspace show

# Delete workspace
terraform workspace delete staging
```

**Using Workspaces in Configuration**:

```hcl
locals {
  environment = terraform.workspace
  
  instance_sizes = {
    default    = "t3.micro"
    staging    = "t3.small"
    production = "t3.large"
  }
}

resource "aws_instance" "web" {
  instance_type = local.instance_sizes[terraform.workspace]
  
  tags = {
    Name        = "web-${terraform.workspace}"
    Environment = terraform.workspace
  }
}
```


## Modules and Reusability

### Understanding Modules

Modules are **reusable packages** of Terraform configuration. Think of them as functions in programming - you define them once and use them multiple times with different parameters.

### 1. Module Structure

**Standard Module Layout**:

```
modules/
├── vpc/
│   ├── main.tf          # Primary configuration
│   ├── variables.tf     # Input variables
│   ├── outputs.tf       # Output values
│   ├── versions.tf      # Provider requirements
│   └── README.md        # Documentation
├── ec2/
│   ├── main.tf
│   ├── variables.tf
│   ├── outputs.tf
│   └── user_data.sh
└── rds/
    ├── main.tf
    ├── variables.tf
    └── outputs.tf
```


### 2. Creating a VPC Module

**`modules/vpc/variables.tf`**:

```hcl
variable "vpc_name" {
  description = "Name of the VPC"
  type        = string
}

variable "vpc_cidr" {
  description = "CIDR block for VPC"
  type        = string
  default     = "10.0.0.0/16"
}

variable "availability_zones" {
  description = "Availability zones"
  type        = list(string)
}

variable "public_subnet_cidrs" {
  description = "CIDR blocks for public subnets"
  type        = list(string)
}

variable "private_subnet_cidrs" {
  description = "CIDR blocks for private subnets"
  type        = list(string)
}

variable "enable_nat_gateway" {
  description = "Enable NAT Gateway for private subnets"
  type        = bool
  default     = true
}

variable "single_nat_gateway" {
  description = "Use single NAT Gateway for all private subnets"
  type        = bool
  default     = false
}

variable "tags" {
  description = "Tags to apply to resources"
  type        = map(string)
  default     = {}
}
```

**`modules/vpc/main.tf`**:

```hcl
# VPC
resource "aws_vpc" "main" {
  cidr_block           = var.vpc_cidr
  enable_dns_hostnames = true
  enable_dns_support   = true

  tags = merge(var.tags, {
    Name = var.vpc_name
  })
}

# Internet Gateway
resource "aws_internet_gateway" "main" {
  vpc_id = aws_vpc.main.id

  tags = merge(var.tags, {
    Name = "${var.vpc_name}-igw"
  })
}

# Public Subnets
resource "aws_subnet" "public" {
  count             = length(var.public_subnet_cidrs)
  vpc_id            = aws_vpc.main.id
  cidr_block        = var.public_subnet_cidrs[count.index]
  availability_zone = var.availability_zones[count.index]
  
  map_public_ip_on_launch = true

  tags = merge(var.tags, {
    Name = "${var.vpc_name}-public-${count.index + 1}"
    Type = "Public"
  })
}

# Private Subnets
resource "aws_subnet" "private" {
  count             = length(var.private_subnet_cidrs)
  vpc_id            = aws_vpc.main.id
  cidr_block        = var.private_subnet_cidrs[count.index]
  availability_zone = var.availability_zones[count.index]

  tags = merge(var.tags, {
    Name = "${var.vpc_name}-private-${count.index + 1}"
    Type = "Private"
  })
}

# Elastic IPs for NAT Gateways
resource "aws_eip" "nat" {
  count  = var.enable_nat_gateway ? (var.single_nat_gateway ? 1 : length(var.private_subnet_cidrs)) : 0
  domain = "vpc"

  depends_on = [aws_internet_gateway.main]

  tags = merge(var.tags, {
    Name = "${var.vpc_name}-nat-eip-${count.index + 1}"
  })
}

# NAT Gateways
resource "aws_nat_gateway" "main" {
  count         = var.enable_nat_gateway ? (var.single_nat_gateway ? 1 : length(var.private_subnet_cidrs)) : 0
  allocation_id = aws_eip.nat[count.index].id
  subnet_id     = aws_subnet.public[count.index].id

  tags = merge(var.tags, {
    Name = "${var.vpc_name}-nat-${count.index + 1}"
  })

  depends_on = [aws_internet_gateway.main]
}

# Public Route Table
resource "aws_route_table" "public" {
  vpc_id = aws_vpc.main.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.main.id
  }

  tags = merge(var.tags, {
    Name = "${var.vpc_name}-public-rt"
  })
}

# Public Route Table Association
resource "aws_route_table_association" "public" {
  count          = length(aws_subnet.public)
  subnet_id      = aws_subnet.public[count.index].id
  route_table_id = aws_route_table.public.id
}

# Private Route Tables
resource "aws_route_table" "private" {
  count  = var.enable_nat_gateway ? length(var.private_subnet_cidrs) : 0
  vpc_id = aws_vpc.main.id

  route {
    cidr_block     = "0.0.0.0/0"
    nat_gateway_id = var.single_nat_gateway ? aws_nat_gateway.main[0].id : aws_nat_gateway.main[count.index].id
  }

  tags = merge(var.tags, {
    Name = "${var.vpc_name}-private-rt-${count.index + 1}"
  })
}

# Private Route Table Association
resource "aws_route_table_association" "private" {
  count          = var.enable_nat_gateway ? length(aws_subnet.private) : 0
  subnet_id      = aws_subnet.private[count.index].id
  route_table_id = aws_route_table.private[count.index].id
}
```

**`modules/vpc/outputs.tf`**:

```hcl
output "vpc_id" {
  description = "ID of the VPC"
  value       = aws_vpc.main.id
}

output "vpc_cidr_block" {
  description = "CIDR block of the VPC"
  value       = aws_vpc.main.cidr_block
}

output "public_subnet_ids" {
  description = "IDs of the public subnets"
  value       = aws_subnet.public[*].id
}

output "private_subnet_ids" {
  description = "IDs of the private subnets"
  value       = aws_subnet.private[*].id
}

output "internet_gateway_id" {
  description = "ID of the Internet Gateway"
  value       = aws_internet_gateway.main.id
}

output "nat_gateway_ids" {
  description = "IDs of the NAT Gateways"
  value       = aws_nat_gateway.main[*].id
}

output "public_route_table_id" {
  description = "ID of the public route table"
  value       = aws_route_table.public.id
}

output "private_route_table_ids" {
  description = "IDs of the private route tables"
  value       = aws_route_table.private[*].id
}
```


### 3. Using Modules

**`main.tf`**:

```hcl
# Use local module
module "vpc" {
  source = "./modules/vpc"
  
  vpc_name               = "production-vpc"
  vpc_cidr              = "10.0.0.0/16"
  availability_zones    = ["us-west-2a", "us-west-2b", "us-west-2c"]
  public_subnet_cidrs   = ["10.0.1.0/24", "10.0.2.0/24", "10.0.3.0/24"]
  private_subnet_cidrs  = ["10.0.10.0/24", "10.0.11.0/24", "10.0.12.0/24"]
  enable_nat_gateway    = true
  single_nat_gateway    = false
  
  tags = {
    Environment = "production"
    Project     = "webapp"
    ManagedBy   = "Terraform"
  }
}

# Use module outputs in other resources
resource "aws_security_group" "web" {
  name   = "web-sg"
  vpc_id = module.vpc.vpc_id

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "web-security-group"
  }
}

resource "aws_instance" "web" {
  ami                    = "ami-0c94855ba95c71c99"
  instance_type          = "t3.micro"
  subnet_id              = module.vpc.public_subnet_ids[0]
  vpc_security_group_ids = [aws_security_group.web.id]

  tags = {
    Name = "web-server"
  }
}
```


### 4. Module Sources

**Local Modules**:

```hcl
module "vpc" {
  source = "./modules/vpc"
}
```

**Git Repository**:

```hcl
module "vpc" {
  source = "git::https://github.com/company/terraform-modules.git//vpc?ref=v1.0.0"
}
```

**Terraform Registry**:

```hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "~> 5.0"
}
```

**HTTP URL**:

```hcl
module "vpc" {
  source = "https://example.com/modules/vpc.zip"
}
```


### 5. Module Versioning

**Git Tags**:

```hcl
module "vpc" {
  source = "git::https://github.com/company/terraform-modules.git//vpc?ref=v2.1.0"
}
```

**Version Constraints**:

```hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "~> 5.0"  # Any version 5.x
}
```


### 6. Advanced Module Patterns

#### Conditional Resource Creation

```hcl
# modules/database/main.tf
resource "aws_db_instance" "main" {
  count = var.create_database ? 1 : 0
  
  identifier     = var.db_name
  engine         = var.engine
  instance_class = var.instance_class
  # ... other configuration
}

resource "aws_rds_cluster" "main" {
  count = var.create_cluster ? 1 : 0
  
  cluster_identifier = var.cluster_name
  engine            = var.engine
  # ... other configuration
}
```


#### Dynamic Blocks in Modules

```hcl
# modules/security-group/main.tf
resource "aws_security_group" "main" {
  name   = var.name
  vpc_id = var.vpc_id

  dynamic "ingress" {
    for_each = var.ingress_rules
    content {
      from_port   = ingress.value.from_port
      to_port     = ingress.value.to_port
      protocol    = ingress.value.protocol
      cidr_blocks = ingress.value.cidr_blocks
      description = ingress.value.description
    }
  }

  dynamic "egress" {
    for_each = var.egress_rules
    content {
      from_port   = egress.value.from_port
      to_port     = egress.value.to_port
      protocol    = egress.value.protocol
      cidr_blocks = egress.value.cidr_blocks
      description = egress.value.description
    }
  }
}
```


#### For_each in Modules

```hcl
# Create multiple VPCs
module "vpcs" {
  source = "./modules/vpc"
  
  for_each = var.vpc_configs
  
  vpc_name               = each.key
  vpc_cidr              = each.value.cidr
  availability_zones    = each.value.azs
  public_subnet_cidrs   = each.value.public_subnets
  private_subnet_cidrs  = each.value.private_subnets
}

# Variable definition
variable "vpc_configs" {
  type = map(object({
    cidr            = string
    azs             = list(string)
    public_subnets  = list(string)
    private_subnets = list(string)
  }))
}
```


## Provider Management

### 1. Provider Versions

**Version Constraints**:

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"    # Pessimistic constraint: >= 5.0, < 6.0
    }
    random = {
      source  = "hashicorp/random"
      version = ">= 3.1"    # Minimum version
    }
    local = {
      source  = "hashicorp/local"
      version = "= 2.4.0"   # Exact version
    }
  }
}
```

**Version Constraint Operators**:


| Operator | Meaning | Example |
| :-- | :-- | :-- |
| `=` | Exact version | `= 1.2.0` |
| `!=` | Not equal | `!= 1.2.0` |
| `>` | Greater than | `> 1.2.0` |
| `>=` | Greater than or equal | `>= 1.2.0` |
| `<` | Less than | `< 1.2.0` |
| `<=` | Less than or equal | `<= 1.2.0` |
| `~>` | Pessimistic constraint | `~> 1.2` |

### 2. Multiple Provider Instances

```hcl
# Default AWS provider (us-west-2)
provider "aws" {
  region = "us-west-2"
}

# East coast provider
provider "aws" {
  alias  = "east"
  region = "us-east-1"
}

# Europe provider
provider "aws" {
  alias  = "europe"
  region = "eu-west-1"
}

# Use different providers
resource "aws_s3_bucket" "west_bucket" {
  bucket = "my-west-bucket"
  # Uses default provider
}

resource "aws_s3_bucket" "east_bucket" {
  provider = aws.east
  bucket   = "my-east-bucket"
}

resource "aws_s3_bucket" "europe_bucket" {
  provider = aws.europe
  bucket   = "my-europe-bucket"
}
```


### 3. Provider Configuration

#### AWS Provider Advanced Configuration

```hcl
provider "aws" {
  region  = "us-west-2"
  profile = "production"
  
  # Assume role
  assume_role {
    role_arn     = "arn:aws:iam::123456789012:role/TerraformRole"
    session_name = "terraform-session"
    external_id  = "unique-external-id"
  }
  
  # Default tags for all resources
  default_tags {
    tags = {
      Project     = "MyApp"
      Environment = "Production"
      ManagedBy   = "Terraform"
      Owner       = "DevOps Team"
    }
  }
  
  # Retry configuration
  retry_mode      = "adaptive"
  max_retries     = 3
  
  # Custom endpoint (for LocalStack, etc.)
  endpoints {
    s3  = "http://localhost:4566"
    ec2 = "http://localhost:4566"
  }
  
  # Ignore specific tags
  ignore_tags {
    keys         = ["CreatedBy", "LastModified"]
    key_prefixes = ["temp-"]
  }
}
```


#### Azure Provider Configuration

```hcl
provider "azurerm" {
  features {
    resource_group {
      prevent_deletion_if_contains_resources = true
    }
    
    key_vault {
      purge_soft_delete_on_destroy    = true
      recover_soft_deleted_key_vaults = true
    }
  }
  
  # Service Principal authentication
  client_id       = var.azure_client_id
  client_secret   = var.azure_client_secret
  tenant_id       = var.azure_tenant_id
  subscription_id = var.azure_subscription_id
}
```


#### Google Cloud Provider Configuration

```hcl
provider "google" {
  project = "my-project-id"
  region  = "us-central1"
  zone    = "us-central1-a"
  
  # Service account key
  credentials = file("path/to/service-account-key.json")
  
  # Or use Application Default Credentials
  # credentials = "path/to/service-account-key.json"
}
```


### 4. Provider Lock File

The `.terraform.lock.hcl` file locks provider versions:

```hcl
# This file is maintained automatically by "terraform init".
provider "registry.terraform.io/hashicorp/aws" {
  version     = "5.31.0"
  constraints = "~> 5.0"
  hashes = [
    "h1:ltxyuBWIy9cq0k9xwq3TK54cqcECbxhGf1HhGdXTfxw=",
    # ... more hashes
  ]
}
```

**Commands**:

```bash
# Update providers to latest allowed versions
terraform init -upgrade

# Lock providers to current versions
terraform providers lock

# Lock for multiple platforms
terraform providers lock -platform=linux_amd64 -platform=darwin_amd64
```


## Advanced Features

### 1. Dynamic Blocks

Dynamic blocks allow you to create repeated nested blocks programmatically:

```hcl
variable "ingress_rules" {
  type = list(object({
    from_port   = number
    to_port     = number
    protocol    = string
    cidr_blocks = list(string)
    description = string
  }))
  default = [
    {
      from_port   = 80
      to_port     = 80
      protocol    = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
      description = "HTTP"
    },
    {
      from_port   = 443
      to_port     = 443
      protocol    = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
      description = "HTTPS"
    }
  ]
}

resource "aws_security_group" "web" {
  name_prefix = "web-sg"
  vpc_id      = var.vpc_id

  dynamic "ingress" {
    for_each = var.ingress_rules
    content {
      from_port   = ingress.value.from_port
      to_port     = ingress.value.to_port
      protocol    = ingress.value.protocol
      cidr_blocks = ingress.value.cidr_blocks
      description = ingress.value.description
    }
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```


### 2. For Expressions

For expressions allow you to transform collections:

```hcl
locals {
  # Transform list to map
  users_map = {
    for user in var.users : user.name => user
  }
  
  # Filter and transform
  admin_users = [
    for user in var.users : user.name
    if user.is_admin
  ]
  
  # Create complex structures
  subnets = [
    for idx, cidr in var.subnet_cidrs : {
      name              = "subnet-${idx + 1}"
      cidr_block        = cidr
      availability_zone = var.availability_zones[idx % length(var.availability_zones)]
    }
  ]
  
  # Conditional transformation
  instance_configs = {
    for env, config in var.environments :
    env => {
      instance_type = config.instance_type
      min_size      = config.min_size
      max_size      = config.max_size
      desired_size  = env == "prod" ? config.max_size : config.min_size
    }
  }
}

# Using for expressions in resources
resource "aws_subnet" "subnets" {
  for_each = {
    for subnet in local.subnets : subnet.name => subnet
  }
  
  vpc_id            = var.vpc_id
  cidr_block        = each.value.cidr_block
  availability_zone = each.value.availability_zone
  
  tags = {
    Name = each.key
  }
}
```


### 3. Conditional Expressions

```hcl
locals {
  # Simple conditional
  instance_type = var.environment == "prod" ? "t3.large" : "t3.micro"
  
  # Complex conditional
  database_config = var.environment == "prod" ? {
    instance_class    = "db.r6g.xlarge"
    allocated_storage = 100
    multi_az         = true
  } : {
    instance_class    = "db.t3.micro"
    allocated_storage = 20
    multi_az         = false
  }
  
  # Conditional resource creation
  create_load_balancer = var.environment == "prod" || var.environment == "staging"
}

resource "aws_instance" "web" {
  ami           = data.aws_ami.amazon_linux.id
  instance_type = local.instance_type
  
  tags = {
    Name = "web-${var.environment}"
  }
}

resource "aws_lb" "main" {
  count = local.create_load_balancer ? 1 : 0
  
  name               = "${var.environment}-alb"
  internal           = false
  load_balancer_type = "application"
  subnets            = var.public_subnet_ids
}
```


### 4. Built-in Functions

**String Functions**:

```hcl
locals {
  # String manipulation
  uppercase_env = upper(var.environment)
  lowercase_env = lower(var.environment)
  title_env     = title(var.environment)
  
  # String interpolation
  bucket_name = "${var.project_name}-${var.environment}-${random_id.bucket_suffix.hex}"
  
  # String functions
  region_short = substr(var.aws_region, 0, 6)  # "us-wes" from "us-west-2"
  env_length   = length(var.environment)
  
  # Replace characters
  safe_name = replace(var.project_name, " ", "-")
}
```

**Collection Functions**:

```hcl
locals {
  # List operations
  all_subnets     = concat(var.public_subnets, var.private_subnets)
  unique_azs      = distinct(var.availability_zones)
  sorted_subnets  = sort(var.subnet_cidrs)
  
  # Map operations
  merged_tags = merge(var.default_tags, var.environment_tags)
  
  # Lookup values
  instance_type = lookup(var.instance_types, var.environment, "t3.micro")
  
  # Check if key exists
  has_staging = contains(keys(var.environments), "staging")
}
```

**Date and Time Functions**:

```hcl
locals {
  current_time = timestamp()
  backup_name  = "backup-${formatdate("YYYY-MM-DD-hhmm", timestamp())}"
  
  # Add time
  expiry_date = timeadd(timestamp(), "168h")  # 7 days from now
}
```

**Network Functions**:

```hcl
locals {
  # CIDR operations
  vpc_cidr        = "10.0.0.0/16"
  public_subnets  = [for i in range(3) : cidrsubnet(local.vpc_cidr, 8, i)]
  private_subnets = [for i in range(3) : cidrsubnet(local.vpc_cidr, 8, i + 10)]
  
  # Network calculations
  network_bits = cidrnetmask("10.0.0.0/24")  # "255.255.255.0"
  host_count   = pow(2, 32 - parseint(split("/", local.vpc_cidr)[1], 10)) - 2
}
```


### 5. Count vs For_each

**Count** - Use for simple repetition:

```hcl
resource "aws_instance" "web" {
  count = 3
  
  ami           = data.aws_ami.amazon_linux.id
  instance_type = "t3.micro"
  
  tags = {
    Name = "web-${count.index + 1}"
  }
}
```

**For_each** - Use for dynamic, map-based resources:

```hcl
variable "instances" {
  type = map(object({
    instance_type = string
    ami_id        = string
  }))
  default = {
    web = {
      instance_type = "t3.micro"
      ami_id        = "ami-0c94855ba95c71c99"
    }
    api = {
      instance_type = "t3.small"
      ami_id        = "ami-0c94855ba95c71c99"
    }
  }
}

resource "aws_instance" "servers" {
  for_each = var.instances
  
  ami           = each.value.ami_id
  instance_type = each.value.instance_type
  
  tags = {
    Name = each.key
    Type = each.key
  }
}
```


### 6. Lifecycle Rules

Control resource behavior with lifecycle rules:

```hcl
resource "aws_instance" "web" {
  ami           = data.aws_ami.amazon_linux.id
  instance_type = var.instance_type
  
  lifecycle {
    # Prevent accidental deletion
    prevent_destroy = true
    
    # Create new resource before destroying old one
    create_before_destroy = true
    
    # Ignore changes to specific attributes
    ignore_changes = [
      ami,
      user_data,
      tags["LastModified"]
    ]
    
    # Replace resource if certain attributes change
    replace_triggered_by = [
      aws_security_group.web.id
    ]
  }
  
  tags = {
    Name = "web-server"
  }
}
```


### 7. Provisioners

Provisioners allow you to run scripts on resources after creation:

```hcl
resource "aws_instance" "web" {
  ami           = data.aws_ami.amazon_linux.id
  instance_type = "t3.micro"
  key_name      = var.key_name
  
  # File provisioner - copy files
  provisioner "file" {
    source      = "app.conf"
    destination = "/tmp/app.conf"
    
    connection {
      type        = "ssh"
      user        = "ec2-user"
      private_key = file(var.private_key_path)
      host        = self.public_ip
    }
  }
  
  # Remote-exec provisioner - run commands
  provisioner "remote-exec" {
    inline = [
      "sudo yum update -y",
      "sudo yum install -y httpd",
      "sudo systemctl start httpd",
      "sudo systemctl enable httpd",
      "sudo cp /tmp/app.conf /etc/httpd/conf.d/"
    ]
    
    connection {
      type        = "ssh"
      user        = "ec2-user"
      private_key = file(var.private_key_path)
      host        = self.public_ip
    }
  }
  
  # Local-exec provisioner - run commands locally
  provisioner "local-exec" {
    command = "echo ${self.private_ip} >> private_ips.txt"
  }
}
```

**Note**: Provisioners are a last resort. Prefer using cloud-init, user_data, or configuration management tools.

## Corporate Workflows

### 1. GitOps Workflow

**Repository Structure**:

```
terraform-infrastructure/
├── environments/
│   ├── dev/
│   │   ├── main.tf
│   │   ├── terraform.tfvars
│   │   └── backend.tf
│   ├── staging/
│   │   ├── main.tf
│   │   ├── terraform.tfvars
│   │   └── backend.tf
│   └── prod/
│       ├── main.tf
│       ├── terraform.tfvars
│       └── backend.tf
├── modules/
│   ├── vpc/
│   ├── ec2/
│   └── rds/
├── .github/
│   └── workflows/
│       ├── terraform-plan.yml
│       └── terraform-apply.yml
└── scripts/
    ├── plan.sh
    └── apply.sh
```

**GitHub Actions Workflow** (`.github/workflows/terraform.yml`):

```yaml
name: Terraform

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

env:
  TF_VERSION: 1.6.0

jobs:
  terraform-plan:
    name: Terraform Plan
    runs-on: ubuntu-latest
    strategy:
      matrix:
        environment: [dev, staging, prod]
    
    steps:
    - name: Checkout
      uses: actions/checkout@v3
    
    - name: Setup Terraform
      uses: hashicorp/setup-terraform@v2
      with:
        terraform_version: ${{ env.TF_VERSION }}
    
    - name: Configure AWS Credentials
      uses: aws-actions/configure-aws-credentials@v2
      with:
        role-to-assume: ${{ secrets.AWS_ROLE_TO_ASSUME }}
        aws-region: us-west-2
    
    - name: Terraform Init
      run: terraform init
      working-directory: ./environments/${{ matrix.environment }}
    
    - name: Terraform Validate
      run: terraform validate
      working-directory: ./environments/${{ matrix.environment }}
    
    - name: Terraform Plan
      run: terraform plan -no-color -out=tfplan
      working-directory: ./environments/${{ matrix.environment }}
    
    - name: Upload Plan
      uses: actions/upload-artifact@v3
      with:
        name: ${{ matrix.environment }}-tfplan
        path: ./environments/${{ matrix.environment }}/tfplan

  terraform-apply:
    name: Terraform Apply
    runs-on: ubuntu-latest
    needs: terraform-plan
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'
    strategy:
      matrix:
        environment: [dev, staging, prod]
    
    steps:
    - name: Checkout
      uses: actions/checkout@v3
    
    - name: Setup Terraform
      uses: hashicorp/setup-terraform@v2
      with:
        terraform_version: ${{ env.TF_VERSION }}
    
    - name: Configure AWS Credentials
      uses: aws-actions/configure-aws-credentials@v2
      with:
        role-to-assume: ${{ secrets.AWS_ROLE_TO_ASSUME }}
        aws-region: us-west-2
    
    - name: Download Plan
      uses: actions/download-artifact@v3
      with:
        name: ${{ matrix.environment }}-tfplan
        path: ./environments/${{ matrix.environment }}
    
    - name: Terraform Init
      run: terraform init
      working-directory: ./environments/${{ matrix.environment }}
    
    - name: Terraform Apply
      run: terraform apply -auto-approve tfplan
      working-directory: ./environments/${{ matrix.environment }}
```


### 2. Multi-Environment Setup

**Environment-Specific Configuration**:

**`environments/dev/terraform.tfvars`**:

```hcl
environment = "dev"
instance_type = "t3.micro"
min_size = 1
max_size = 2
desired_capacity = 1
enable_monitoring = false
backup_retention_days = 7
```

**`environments/prod/terraform.tfvars`**:

```hcl
environment = "prod"
instance_type = "t3.large"
min_size = 3
max_size = 10
desired_capacity = 5
enable_monitoring = true
backup_retention_days = 30
```

**Shared Configuration** (`environments/dev/main.tf`):

```hcl
terraform {
  required_version = ">= 1.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
  
  backend "s3" {
    bucket = "company-terraform-state"
    key    = "dev/terraform.tfstate"
    region = "us-west-2"
    encrypt = true
    dynamodb_table = "terraform-locks"
  }
}

provider "aws" {
  region = var.aws_region
  
  default_tags {
    tags = {
      Environment = var.environment
      Project     = var.project_name
      ManagedBy   = "Terraform"
    }
  }
}

# Use modules
module "vpc" {
  source = "../../modules/vpc"
  
  vpc_name               = "${var.project_name}-${var.environment}"
  vpc_cidr              = var.vpc_cidr
  availability_zones    = var.availability_zones
  public_subnet_cidrs   = var.public_subnet_cidrs
  private_subnet_cidrs  = var.private_subnet_cidrs
  
  tags = {
    Environment = var.environment
  }
}

module "web_servers" {
  source = "../../modules/asg"
  
  name              = "${var.project_name}-${var.environment}-web"
  vpc_id            = module.vpc.vpc_id
  subnet_ids        = module.vpc.private_subnet_ids
  instance_type     = var.instance_type
  min_size          = var.min_size
  max_size          = var.max_size
  desired_capacity  = var.desired_capacity
  
  tags = {
    Environment = var.environment
  }
}
```


### 3. Approval Workflows

**Manual Approval for Production**:

```yaml
# .github/workflows/terraform-prod.yml
name: Production Deployment

on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Environment to deploy'
        required: true
        default: 'prod'
        type: choice
        options:
        - prod

jobs:
  terraform-plan:
    name: Terraform Plan
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout
      uses: actions/checkout@v3
    
    - name: Terraform Plan
      run: |
        terraform init
        terraform plan -detailed-exitcode -out=tfplan
      working-directory: ./environments/prod
    
    - name: Upload Plan
      uses: actions/upload-artifact@v3
      with:
        name: prod-tfplan
        path: ./environments/prod/tfplan

  manual-approval:
    name: Manual Approval
    runs-on: ubuntu-latest
    needs: terraform-plan
    environment: production
    
    steps:
    - name: Request Approval
      uses: trstringer/manual-approval@v1
      with:
        secret: ${{ github.TOKEN }}
        approvers: devops-team,senior-developers
        minimum-approvals: 2
        issue-title: "Production Terraform Deployment Approval"

  terraform-apply:
    name: Terraform Apply
    runs-on: ubuntu-latest
    needs: manual-approval
    environment: production
    
    steps:
    - name: Checkout
      uses: actions/checkout@v3
    
    - name: Download Plan
      uses: actions/download-artifact@v3
      with:
        name: prod-tfplan
        path: ./environments/prod
    
    - name: Terraform Apply
      run: |
        terraform init
        terraform apply -auto-approve tfplan
      working-directory: ./environments/prod
```


### 4. Cost Management

**Cost Estimation with Infracost**:

```yaml
# .github/workflows/cost-estimation.yml
name: Cost Estimation

on:
  pull_request:
    paths:
    - 'environments/**/*.tf'
    - 'modules/**/*.tf'

jobs:
  infracost:
    name: Infracost
    runs-on: ubuntu-latest
    
    steps:
    - name: Setup Infracost
      uses: infracost/actions/setup@v2
      with:
        api-key: ${{ secrets.INFRACOST_API_KEY }}
    
    - name: Checkout base branch
      uses: actions/checkout@v3
      with:
        ref: '${{ github.event.pull_request.base.ref }}'
    
    - name: Generate Infracost cost estimate baseline
      run: |
        infracost breakdown --path=environments/prod \
                            --format=json \
                            --out-file=/tmp/infracost-base.json
    
    - name: Checkout PR branch
      uses: actions/checkout@v3
    
    - name: Generate Infracost diff
      run: |
        infracost diff --path=environments/prod \
                       --format=json \
                       --compare-to=/tmp/infracost-base.json \
                       --out-file=/tmp/infracost.json
    
    - name: Post Infracost comment
      run: |
        infracost comment github --path=/tmp/infracost.json \
                                 --repo=$GITHUB_REPOSITORY \
                                 --github-token=${{ github.token }} \
                                 --pull-request=${{ github.event.pull_request.number }} \
                                 --behavior=update
```


## Best Practices

### 1. Code Structure and Organization

**File Organization**:

```
project/
├── main.tf                 # Main configuration
├── variables.tf            # Variable definitions
├── outputs.tf             # Output values
├── versions.tf            # Provider requirements
├── terraform.tfvars       # Variable values
├── locals.tf              # Local values
└── data.tf                # Data sources
```

**Naming Conventions**:

```hcl
# Resources: descriptive names with underscores
resource "aws_instance" "web_server" {}
resource "aws_security_group" "web_server_sg" {}

# Variables: descriptive with context
variable "vpc_cidr_block" {}
variable "database_instance_class" {}
variable "enable_monitoring" {}

# Outputs: what they represent
output "load_balancer_dns_name" {}
output "database_endpoint" {}
output "vpc_id" {}

# Locals: grouped by purpose
locals {
  common_tags = {
    Project     = var.project_name
    Environment = var.environment
    ManagedBy   = "Terraform"
  }
  
  naming_prefix = "${var.project_name}-${var.environment}"
}
```


### 2. Variable Management

**Variable Types and Validation**:

```hcl
variable "environment" {
  description = "Environment name"
  type        = string
  
  validation {
    condition = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Environment must be dev, staging, or prod."
  }
}

variable "instance_config" {
  description = "EC2 instance configuration"
  type = object({
    instance_type = string
    key_name      = string
    monitoring    = bool
  })
  
  validation {
    condition = contains([
      "t3.micro", "t3.small", "t3.medium", "t3.large"
    ], var.instance_config.instance_type)
    error_message = "Instance type must be a valid t3 type."
  }
}

variable "subnet_cidrs" {
  description = "List of subnet CIDR blocks"
  type        = list(string)
  
  validation {
    condition = alltrue([
      for cidr in var.subnet_cidrs : can(cidrhost(cidr, 0))
    ])
    error_message = "All subnet CIDRs must be valid."
  }
}
```


### 3. Security Best Practices

**Sensitive Data Handling**:

```hcl
# Mark sensitive variables
variable "database_password" {
  description = "Database master password"
  type        = string
  sensitive   = true
}

# Use random passwords
resource "random_password" "db_password" {
  length  = 16
  special = true
}

# Store in AWS Secrets Manager
resource "aws_secretsmanager_secret" "db_password" {
  name = "${var.project_name}-${var.environment}-db-password"
}

resource "aws_secretsmanager_secret_version" "db_password" {
  secret_id     = aws_secretsmanager_secret.db_password.id
  secret_string = random_password.db_password.result
}

# Reference in RDS
resource "aws_db_instance" "main" {
  password = random_password.db_password.result
  # other configuration...
}
```

**Resource Tagging Strategy**:

```hcl
locals {
  # Mandatory tags for all resources
  mandatory_tags = {
    Project     = var.project_name
    Environment = var.environment
    ManagedBy   = "Terraform"
    Owner       = var.team_name
    CostCenter  = var.cost_center
  }
  
  # Optional tags
  optional_tags = {
    BackupRequired = "true"
    Monitoring     = "enabled"
  }
  
  # Combined tags
  common_tags = merge(local.mandatory_tags, local.optional_tags, var.additional_tags)
}

# Apply to resources
resource "aws_instance" "web" {
  # configuration...
  
  tags = merge(local.common_tags, {
    Name = "${var.project_name}-${var.environment}-web"
    Type = "WebServer"
  })
}
```


### 4. State Management Best Practices

**Remote State Configuration**:

```hcl
terraform {
  backend "s3" {
    bucket         = "company-terraform-state"
    key            = "projects/myapp/prod/terraform.tfstate"
    region         = "us-west-2"
    encrypt        = true
    dynamodb_table = "terraform-state-locks"
    
    # Enable versioning and MFA delete on the bucket
    versioning = true
  }
}
```

**State Isolation**:

```bash
# Separate state files by environment and component
project/
├── environments/
│   ├── dev/
│   │   ├── networking/
│   │   │   └── terraform.tfstate
│   │   ├── compute/
│   │   │   └── terraform.tfstate
│   │   └── database/
│   │       └── terraform.tfstate
│   └── prod/
│       ├── networking/
│       ├── compute/
│       └── database/
```


### 5. Module Development Guidelines

**Module Standards**:

```hcl
# modules/vpc/versions.tf
terraform {
  required_version = ">= 1.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = ">= 5.0"
    }
  }
}

# modules/vpc/variables.tf
variable "vpc_name" {
  description = "Name of the VPC"
  type        = string
}

variable "vpc_cidr" {
  description = "CIDR block for VPC"
  type        = string
  
  validation {
    condition     = can(cidrhost(var.vpc_cidr, 0))
    error_message = "VPC CIDR must be a valid IPv4 CIDR block."
  }
}

# modules/vpc/outputs.tf
output "vpc_id" {
  description = "ID of the VPC"
  value       = aws_vpc.main.id
}

output "vpc_cidr_block" {
  description = "CIDR block of the VPC"
  value       = aws_vpc.main.cidr_block
}
```

**Module Documentation** (`modules/vpc/README.md`):

```markdown
# VPC Module

This module creates a VPC with public and private subnets across multiple AZs.

## Usage

```

module "vpc" {
source = "./modules/vpc"

vpc_name               = "my-vpc"
vpc_cidr              = "10.0.0.0/16"
availability_zones    = ["us-west-2a", "us-west-2b"]
public_subnet_cidrs   = ["10.0.1.0/24", "10.0.2.0/24"]
private_subnet_cidrs  = ["10.0.10.0/24", "10.0.11.0/24"]
}

```

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| vpc_name | Name of the VPC | `string` | n/a | yes |
| vpc_cidr | CIDR block for VPC | `string` | `"10.0.0.0/16"` | no |

## Outputs

| Name | Description |
|------|-------------|
| vpc_id | ID of the VPC |
| public_subnet_ids | IDs of the public subnets |
```


## Common Corporate Scenarios

### 1. Multi-Account AWS Setup

**Scenario**: Company has separate AWS accounts for dev, staging, and production.

**Solution**:

```hcl
# Cross-account role assumption
provider "aws" {
  alias  = "dev"
  region = "us-west-2"
  
  assume_role {
    role_arn = "arn:aws:iam::111111111111:role/TerraformDeploymentRole"
  }
}

provider "aws" {
  alias  = "prod"
  region = "us-west-2"
  
  assume_role {
    role_arn = "arn:aws:iam::333333333333:role/TerraformDeploymentRole"
  }
}

# Deploy to different accounts
module "dev_infrastructure" {
  source = "./modules/infrastructure"
  
  providers = {
    aws = aws.dev
  }
  
  environment = "dev"
}

module "prod_infrastructure" {
  source = "./modules/infrastructure"
  
  providers = {
    aws = aws.prod
  }
  
  environment = "prod"
}
```


### 2. Blue-Green Deployment

**Scenario**: Zero-downtime deployments using blue-green strategy.

**Solution**:

```hcl
variable "active_environment" {
  description = "Currently active environment (blue or green)"
  type        = string
  default     = "blue"
  
  validation {
    condition     = contains(["blue", "green"], var.active_environment)
    error_message = "Active environment must be blue or green."
  }
}

locals {
  inactive_environment = var.active_environment == "blue" ? "green" : "blue"
}

# Blue environment
module "blue_environment" {
  source = "./modules/web-app"
  
  environment_name = "blue"
  instance_count   = var.active_environment == "blue" ? var.active_instance_count : 0
  
  tags = {
    Environment = "blue"
    Status      = var.active_environment == "blue" ? "active" : "inactive"
  }
}

# Green environment
module "green_environment" {
  source = "./modules/web-app"
  
  environment_name = "green"
  instance_count   = var.active_environment == "green" ? var.active_instance_count : 0
  
  tags = {
    Environment = "green"
    Status      = var.active_environment == "green" ? "active" : "inactive"
  }
}

# Load balancer points to active environment
resource "aws_lb_target_group_attachment" "active" {
  target_group_arn = aws_lb_target_group.main.arn
  target_id        = var.active_environment == "blue" ? module.blue_environment.instance_ids[0] : module.green_environment.instance_ids[0]
  port             = 80
}
```


### 3. Disaster Recovery Setup

**Scenario**: Multi-region disaster recovery with automated failover.

**Solution**:

```hcl
# Primary region
provider "aws" {
  alias  = "primary"
  region = "us-west-2"
}

# DR region
provider "aws" {
  alias  = "dr"
  region = "us-east-1"
}

# Primary infrastructure
module "primary_infrastructure" {
  source = "./modules/full-stack"
  
  providers = {
    aws = aws.primary
  }
  
  region           = "us-west-2"
  environment      = "prod-primary"
  enable_backups   = true
  backup_retention = 30
}

# DR infrastructure (scaled down)
module "dr_infrastructure" {
  source = "./modules/full-stack"
  
  providers = {
    aws = aws.dr
  }
  
  region             = "us-east-1"
  environment        = "prod-dr"
  instance_count     = 1  # Minimal for cost
  instance_type      = "t3.small"
  enable_backups     = false
  restore_from_backup = module.primary_infrastructure.latest_backup_id
}

# Route 53 health checks and failover
resource "aws_route53_health_check" "primary" {
  fqdn                            = module.primary_infrastructure.load_balancer_dns
  port                            = 443
  type                            = "HTTPS"
  resource_path                   = "/health"
  failure_threshold               = 3
  request_interval                = 30
}

resource "aws_route53_record" "primary" {
  zone_id = var.hosted_zone_id
  name    = "app.company.com"
  type    = "CNAME"
  ttl     = 60
  
  failover_routing_policy {
    type = "PRIMARY"
  }
  
  health_check_id = aws_route53_health_check.primary.id
  records         = [module.primary_infrastructure.load_balancer_dns]
}

resource "aws_route53_record" "secondary" {
  zone_id = var.hosted_zone_id
  name    = "app.company.com"
  type    = "CNAME"
  ttl     = 60
  
  failover_routing_policy {
    type = "SECONDARY"
  }
  
  records = [module.dr_infrastructure.load_balancer_dns]
}
```


### 4. Compliance and Governance

**Scenario**: Implement compliance controls and governance policies.

**Solution**:

```hcl
# Policy validation using Sentinel (Terraform Cloud/Enterprise)
# sentinel.hcl
policy "require-tags" {
  source = "./policies/require-tags.sentinel"
  enforcement_level = "hard-mandatory"
}

policy "restrict-instance-types" {
  source = "./policies/restrict-instance-types.sentinel"
  enforcement_level = "soft-mandatory"
}

# Compliance module
module "compliance" {
  source = "./modules/compliance"
  
  # Enable required security features
  enable_cloudtrail        = true
  enable_config           = true
  enable_guardduty        = true
  enable_security_hub     = true
  
  # Encryption requirements
  require_ebs_encryption  = true
  require_s3_encryption   = true
  require_rds_encryption  = true
  
  # Backup requirements
  backup_retention_days   = 365
  cross_region_backup     = true
  
  # Network security
  restrict_ssh_access     = true
  allowed_ssh_cidrs       = var.management_cidrs
  
  tags = local.compliance_tags
}

# Resource tagging policy
resource "aws_organizations_policy" "tagging_policy" {
  name        = "RequiredTagsPolicy"
  description = "Enforce required tags on all resources"
  type        = "TAG_POLICY"
  
  content = jsonencode({
    tags = {
      Project = {
        tag_key = "Project"
        enforced_for = {
          "@@assign" = ["ec2:instance", "s3:bucket", "rds:db"]
        }
      }
      Environment = {
        tag_key = "Environment"
        tag_value = {
          "@@assign" = ["dev", "staging", "prod"]
        }
      }
    }
  })
}
```


### 5. Cost Optimization

**Scenario**: Implement automated cost optimization strategies.

**Solution**:

```hcl
# Spot instances for non-critical workloads
resource "aws_spot_instance_request" "worker" {
  count = var.enable_spot_instances ? var.worker_count : 0
  
  ami                  = data.aws_ami.amazon_linux.id
  instance_type        = "t3.medium"
  spot_price           = "0.05"
  wait_for_fulfillment = true
  spot_type            = "one-time"
  
  tags = {
    Name = "worker-spot-${count.index + 1}"
    Type = "Spot"
  }
}

# Scheduled scaling for dev environments
resource "aws_autoscaling_schedule" "scale_down_evening" {
  count = var.environment == "dev" ? 1 : 0
  
  scheduled_action_name  = "scale-down-evening"
  min_size              = 0
  max_size              = 0
  desired_capacity      = 0
  recurrence            = "0 19 * * MON-FRI"  # 7 PM weekdays
  time_zone             = "America/Los_Angeles"
  autoscaling_group_name = aws_autoscaling_group.web.name
}

resource "aws_autoscaling_schedule" "scale_up_morning" {
  count = var.environment == "dev" ? 1 : 0
  
  scheduled_action_name  = "scale-up-morning"
  min_size              = 1
  max_size              = 3
  desired_capacity      = 2
  recurrence            = "0 8 * * MON-FRI"   # 8 AM weekdays
  time_zone             = "America/Los_Angeles"
  autoscaling_group_name = aws_autoscaling_group.web.name
}

# S3 lifecycle policies
resource "aws_s3_bucket_lifecycle_configuration" "cost_optimization" {
  bucket = aws_s3_bucket.data.id

  rule {
    id     = "cost_optimization"
    status = "Enabled"

    transition {
      days          = 30
      storage_class = "STANDARD_IA"
    }

    transition {
      days          = 90
      storage_class = "GLACIER"
    }

    transition {
      days          = 365
      storage_class = "DEEP_ARCHIVE"
    }

    expiration {
      days = var.data_retention_days
    }
  }
}
```


### 6. Migration from Manual Infrastructure

**Scenario**: Import existing manually created resources into Terraform.

**Solution**:

```bash
# Step 1: Create Terraform configuration for existing resources
# existing-vpc.tf
resource "aws_vpc" "existing" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_hostnames = true
  enable_dns_support   = true
  
  tags = {
    Name = "existing-vpc"
  }
}

# Step 2: Import existing resources
terraform import aws_vpc.existing vpc-1234567890abcdef0
terraform import aws_subnet.existing subnet-1234567890abcdef0
terraform import aws_security_group.existing sg-1234567890abcdef0

# Step 3: Run terraform plan to see differences
terraform plan

# Step 4: Adjust configuration to match actual state
# Step 5: Apply to bring under Terraform management
terraform apply
```

**Migration Script**:

```bash
#!/bin/bash
# migrate-resources.sh

# List of resources to import
RESOURCES=(
  "aws_vpc.main:vpc-1234567890abcdef0"
  "aws_subnet.public[0]:subnet-1111111111111111"
  "aws_subnet.public[1]:subnet-2222222222222222"
  "aws_internet_gateway.main:igw-1234567890abcdef0"
  "aws_security_group.web:sg-1234567890abcdef0"
)

# Import each resource
for resource in "${RESOURCES[@]}"; do
  terraform_resource=$(echo $resource | cut -d: -f1)
  aws_resource_id=$(echo $resource | cut -d: -f2)
  
  echo "Importing $terraform_resource from $aws_resource_id"
  terraform import $terraform_resource $aws_resource_id
  
  if [ $? -eq 0 ]; then
    echo "Successfully imported $terraform_resource"
  else
    echo "Failed to import $terraform_resource"
    exit 1
  fi
done

echo "All resources imported successfully"
terraform plan
```


## Troubleshooting

### 1. Common Error Messages and Solutions

**Provider Errors**:

```bash
# Error: Failed to query available provider packages
# Solution: Clear Terraform plugins and reinitialize
rm -rf .terraform
terraform init

# Error: Provider configuration not present
# Solution: Add provider configuration
provider "aws" {
  region = "us-west-2"
}
```

**State Errors**:

```bash
# Error: Resource already exists
# Solution: Import the existing resource
terraform import aws_instance.web i-1234567890abcdef0

# Error: State lock timeout
# Solution: Force unlock (use carefully)
terraform force-unlock LOCK_ID

# Error: State file not found
# Solution: Initialize with correct backend configuration
terraform init -reconfigure
```

**Authentication Errors**:

```bash
# Error: NoCredentialsError
# Solution: Configure AWS credentials
aws configure
# or
export AWS_ACCESS_KEY_ID="your-key"
export AWS_SECRET_ACCESS_KEY="your-secret"

# Error: Access denied
# Solution: Check IAM permissions
aws sts get-caller-identity
```


### 2. Debugging Techniques

**Enable Debug Logging**:

```bash
# Enable trace logging
export TF_LOG=TRACE
export TF_LOG_PATH=./terraform.log
terraform apply

# Different log levels
export TF_LOG=DEBUG   # Detailed debug information
export TF_LOG=INFO    # General information
export TF_LOG=WARN    # Warning messages only
export TF_LOG=ERROR   # Error messages only
```

**Terraform Console**:

```bash
# Interactive console for testing expressions
terraform console

# Test expressions
> var.environment
"production"

> local.common_tags
{
  "Environment" = "production"
  "ManagedBy" = "Terraform"
  "Project" = "webapp"
}

> cidrsubnet("10.0.0.0/16", 8, 1)
"10.0.1.0/24"
```

**Graph Visualization**:

```bash
# Generate dependency graph
terraform graph | dot -Tpng > graph.png

# View resource dependencies
terraform graph -type=plan | dot -Tpng > plan-graph.png
```


### 3. State Recovery

**Backup and Restore State**:

```bash
# Backup current state
terraform state pull > terraform.tfstate.backup

# Restore from backup
terraform state push terraform.tfstate.backup

# List all resources in state
terraform state list

# Show specific resource
terraform state show aws_instance.web

# Remove resource from state
terraform state rm aws_instance.web

# Move resource to new address
terraform state mv aws_instance.web aws_instance.web_server
```


### 4. Performance Optimization

**Parallel Execution**:

```bash
# Increase parallelism (default is 10)
terraform apply -parallelism=20

# Reduce parallelism for rate-limited APIs
terraform apply -parallelism=5
```

**Targeted Operations**:

```bash
# Target specific resources
terraform apply -target=aws_instance.web
terraform plan -target=module.vpc

# Refresh only (don't make changes)
terraform refresh

# Replace specific resource
terraform apply -replace=aws_instance.web
```


## Cheatsheet

### Essential Commands

| Command | Purpose | Example |
| :-- | :-- | :-- |
| `terraform init` | Initialize working directory | `terraform init` |
| `terraform plan` | Preview changes | `terraform plan -out=tfplan` |
| `terraform apply` | Apply changes | `terraform apply tfplan` |
| `terraform destroy` | Destroy infrastructure | `terraform destroy` |
| `terraform validate` | Validate configuration | `terraform validate` |
| `terraform fmt` | Format code | `terraform fmt -recursive` |
| `terraform show` | Show current state | `terraform show` |
| `terraform output` | Show outputs | `terraform output -json` |

### State Management

| Command | Purpose | Example |
| :-- | :-- | :-- |
| `terraform state list` | List resources | `terraform state list` |
| `terraform state show` | Show resource details | `terraform state show aws_instance.web` |
| `terraform state rm` | Remove from state | `terraform state rm aws_instance.web` |
| `terraform state mv` | Move resource | `terraform state mv aws_instance.old aws_instance.new` |
| `terraform import` | Import existing resource | `terraform import aws_instance.web i-1234567890abcdef0` |
| `terraform refresh` | Refresh state | `terraform refresh` |

### Workspace Commands

| Command | Purpose | Example |
| :-- | :-- | :-- |
| `terraform workspace list` | List workspaces | `terraform workspace list` |
| `terraform workspace new` | Create workspace | `terraform workspace new staging` |
| `terraform workspace select |  |  |

