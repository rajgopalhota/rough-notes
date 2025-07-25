<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Complete Guide to Terraform, AWS, and Kubernetes: From Beginner to Advanced

## Table of Contents

1. [Introduction](#introduction)
2. [Terraform: Infrastructure as Code](#terraform-infrastructure-as-code)
3. [AWS: Amazon Web Services](#aws-amazon-web-services)
4. [Kubernetes: Container Orchestration](#kubernetes-container-orchestration)
5. [Advanced Configurations and Use Cases](#advanced-configurations-and-use-cases)
6. [Real-World Integration Examples](#real-world-integration-examples)

## Introduction

This comprehensive documentation covers three fundamental technologies that form the backbone of modern cloud infrastructure: **Terraform** for Infrastructure as Code (IaC), **AWS** for cloud services, and **Kubernetes** for container orchestration. Each section progresses from basic concepts to advanced real-world implementations.[^1][^2]

**What You'll Learn:**

- Complete understanding of each technology from scratch
- Practical examples for every concept
- Advanced configurations including routing and IP whitelisting
- Real-world use cases and integration patterns
- Best practices for production environments


## Terraform: Infrastructure as Code

### What is Terraform?

Terraform is an open-source Infrastructure as Code (IaC) tool by HashiCorp that enables you to define, provision, and manage cloud and on-premises resources using a declarative configuration language called HashiCorp Configuration Language (HCL).[^1][^2]

### Core Concepts

#### 1. Providers

Providers are plugins that interact with cloud platforms, SaaS services, and other APIs.[^1]

```hcl
# AWS Provider Configuration
provider "aws" {
  region = "us-west-2"
  profile = "default"
}

# Google Cloud Provider Configuration
provider "google" {
  project = "my-project-id"
  region  = "us-central1"
}

# Kubernetes Provider Configuration
provider "kubernetes" {
  config_path = "~/.kube/config"
}
```


#### 2. Resources

Resources are the most important element in Terraform language. Each resource block describes one or more infrastructure objects.[^1]

```hcl
# AWS EC2 Instance Resource
resource "aws_instance" "web_server" {
  ami           = "ami-0c94855ba95c71c99"
  instance_type = "t3.micro"
  
  tags = {
    Name        = "WebServer"
    Environment = "Production"
  }
}

# AWS S3 Bucket Resource
resource "aws_s3_bucket" "data_bucket" {
  bucket = "my-unique-data-bucket-2024"
}
```


#### 3. Variables

Variables allow you to parameterize your Terraform configurations.[^2]

```hcl
# variables.tf
variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t3.micro"
  validation {
    condition     = contains(["t3.micro", "t3.small", "t3.medium"], var.instance_type)
    error_message = "Instance type must be t3.micro, t3.small, or t3.medium."
  }
}

variable "environment" {
  description = "Environment name"
  type        = string
}

variable "allowed_cidr_blocks" {
  description = "List of CIDR blocks allowed to access resources"
  type        = list(string)
  default     = ["10.0.0.0/8"]
}
```


#### 4. Outputs

Outputs are used to extract information about your infrastructure.[^2]

```hcl
# outputs.tf
output "instance_ip" {
  description = "Public IP address of the EC2 instance"
  value       = aws_instance.web_server.public_ip
}

output "bucket_arn" {
  description = "ARN of the S3 bucket"
  value       = aws_s3_bucket.data_bucket.arn
}
```


### Terraform Workflow

#### Basic Commands

1. **Initialize**: `terraform init`
2. **Plan**: `terraform plan`
3. **Apply**: `terraform apply`
4. **Destroy**: `terraform destroy`

#### Advanced Workflow Example

```bash
# Initialize Terraform
terraform init

# Validate configuration
terraform validate

# Format code
terraform fmt

# Plan with variable file
terraform plan -var-file="prod.tfvars"

# Apply with auto-approve
terraform apply -auto-approve

# Show current state
terraform show

# Import existing resource
terraform import aws_instance.example i-1234567890abcdef0
```


### State Management

#### Local State

```hcl
# terraform.tf
terraform {
  required_version = ">= 1.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}
```


#### Remote State with S3 Backend

```hcl
# backend.tf
terraform {
  backend "s3" {
    bucket         = "my-terraform-state-bucket"
    key            = "prod/terraform.tfstate"
    region         = "us-west-2"
    encrypt        = true
    dynamodb_table = "terraform-locks"
  }
}
```


### Modules

#### Creating a Reusable VPC Module

```hcl
# modules/vpc/main.tf
resource "aws_vpc" "main" {
  cidr_block           = var.cidr_block
  enable_dns_hostnames = true
  enable_dns_support   = true

  tags = {
    Name = var.vpc_name
    Environment = var.environment
  }
}

resource "aws_subnet" "public" {
  count             = length(var.public_subnet_cidrs)
  vpc_id            = aws_vpc.main.id
  cidr_block        = var.public_subnet_cidrs[count.index]
  availability_zone = data.aws_availability_zones.available.names[count.index]
  
  map_public_ip_on_launch = true

  tags = {
    Name = "${var.vpc_name}-public-${count.index + 1}"
    Type = "Public"
  }
}

resource "aws_internet_gateway" "main" {
  vpc_id = aws_vpc.main.id

  tags = {
    Name = "${var.vpc_name}-igw"
  }
}
```


#### Module Variables

```hcl
# modules/vpc/variables.tf
variable "cidr_block" {
  description = "CIDR block for VPC"
  type        = string
  default     = "10.0.0.0/16"
}

variable "vpc_name" {
  description = "Name of the VPC"
  type        = string
}

variable "environment" {
  description = "Environment name"
  type        = string
}

variable "public_subnet_cidrs" {
  description = "CIDR blocks for public subnets"
  type        = list(string)
  default     = ["10.0.1.0/24", "10.0.2.0/24"]
}
```


#### Using the Module

```hcl
# main.tf
module "vpc" {
  source = "./modules/vpc"
  
  vpc_name             = "production-vpc"
  environment          = "prod"
  cidr_block          = "10.0.0.0/16"
  public_subnet_cidrs = ["10.0.1.0/24", "10.0.2.0/24", "10.0.3.0/24"]
}
```


### Advanced Terraform Features

#### Conditional Resources

```hcl
resource "aws_instance" "web" {
  count         = var.create_instance ? 1 : 0
  ami           = var.ami_id
  instance_type = var.instance_type
}

resource "aws_eip" "web" {
  count    = var.create_instance && var.assign_eip ? 1 : 0
  instance = aws_instance.web[^0].id
}
```


#### Dynamic Blocks

```hcl
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
    }
  }
}
```


#### For Expressions

```hcl
locals {
  instance_names = [for i in range(var.instance_count) : "web-${i + 1}"]
  
  security_group_rules = {
    for rule in var.security_rules :
    rule.name => {
      port        = rule.port
      protocol    = rule.protocol
      cidr_blocks = rule.cidr_blocks
    }
  }
}
```


## AWS: Amazon Web Services

### What is AWS?

Amazon Web Services (AWS) is a comprehensive cloud computing platform offering over 200 fully featured services including compute, storage, databases, networking, analytics, machine learning, and more.[^3]

### Core AWS Services

#### 1. Compute Services

##### EC2 (Elastic Compute Cloud)

```hcl
# Basic EC2 Instance
resource "aws_instance" "web" {
  ami                    = "ami-0c94855ba95c71c99"
  instance_type          = "t3.micro"
  subnet_id              = aws_subnet.public.id
  vpc_security_group_ids = [aws_security_group.web.id]
  
  user_data = <<-EOF
    #!/bin/bash
    yum update -y
    yum install -y httpd
    systemctl start httpd
    systemctl enable httpd
    echo "<h1>Hello from AWS EC2!</h1>" > /var/www/html/index.html
  EOF

  tags = {
    Name = "WebServer"
  }
}

# Auto Scaling Group
resource "aws_autoscaling_group" "web" {
  name                = "web-asg"
  vpc_zone_identifier = aws_subnet.private[*].id
  target_group_arns   = [aws_lb_target_group.web.arn]
  health_check_type   = "ELB"
  
  min_size         = 2
  max_size         = 10
  desired_capacity = 3

  launch_template {
    id      = aws_launch_template.web.id
    version = "$Latest"
  }

  tag {
    key                 = "Name"
    value               = "web-asg-instance"
    propagate_at_launch = true
  }
}
```


##### Lambda (Serverless Functions)

```hcl
# Lambda Function
resource "aws_lambda_function" "api_handler" {
  filename         = "lambda_function.zip"
  function_name    = "api-handler"
  role            = aws_iam_role.lambda_role.arn
  handler         = "index.handler"
  runtime         = "python3.9"
  timeout         = 30

  environment {
    variables = {
      DATABASE_URL = aws_rds_cluster.main.endpoint
      S3_BUCKET    = aws_s3_bucket.data.bucket
    }
  }
}

# API Gateway Integration
resource "aws_api_gateway_rest_api" "main" {
  name        = "main-api"
  description = "Main API Gateway"
}

resource "aws_api_gateway_integration" "lambda" {
  rest_api_id             = aws_api_gateway_rest_api.main.id
  resource_id             = aws_api_gateway_rest_api.main.root_resource_id
  http_method             = aws_api_gateway_method.proxy.http_method
  integration_http_method = "POST"
  type                    = "AWS_PROXY"
  uri                     = aws_lambda_function.api_handler.invoke_arn
}
```


#### 2. Storage Services

##### S3 (Simple Storage Service)

```hcl
# S3 Bucket with Advanced Configuration
resource "aws_s3_bucket" "data" {
  bucket = "my-company-data-bucket-${random_id.bucket_suffix.hex}"
}

resource "aws_s3_bucket_versioning" "data" {
  bucket = aws_s3_bucket.data.id
  versioning_configuration {
    status = "Enabled"
  }
}

resource "aws_s3_bucket_encryption" "data" {
  bucket = aws_s3_bucket.data.id

  server_side_encryption_configuration {
    rule {
      apply_server_side_encryption_by_default {
        kms_master_key_id = aws_kms_key.s3.arn
        sse_algorithm     = "aws:kms"
      }
    }
  }
}

resource "aws_s3_bucket_lifecycle_configuration" "data" {
  bucket = aws_s3_bucket.data.id

  rule {
    id     = "transition_to_ia"
    status = "Enabled"

    transition {
      days          = 30
      storage_class = "STANDARD_IA"
    }

    transition {
      days          = 90
      storage_class = "GLACIER"
    }

    expiration {
      days = 365
    }
  }
}
```


#### 3. Database Services

##### RDS (Relational Database Service)

```hcl
# RDS Aurora Cluster
resource "aws_rds_cluster" "main" {
  cluster_identifier      = "main-cluster"
  engine                 = "aurora-mysql"
  engine_version         = "8.0.mysql_aurora.3.02.0"
  database_name          = "maindb"
  master_username        = "admin"
  master_password        = var.db_password
  backup_retention_period = 7
  preferred_backup_window = "07:00-09:00"
  
  vpc_security_group_ids = [aws_security_group.rds.id]
  db_subnet_group_name   = aws_db_subnet_group.main.name
  
  storage_encrypted = true
  kms_key_id       = aws_kms_key.rds.arn

  skip_final_snapshot = false
  final_snapshot_identifier = "main-cluster-final-snapshot"

  tags = {
    Name = "MainCluster"
  }
}

resource "aws_rds_cluster_instance" "cluster_instances" {
  count              = 2
  identifier         = "main-cluster-${count.index}"
  cluster_identifier = aws_rds_cluster.main.id
  instance_class     = "db.r6g.large"
  engine             = aws_rds_cluster.main.engine
  engine_version     = aws_rds_cluster.main.engine_version
}
```


#### 4. Networking Services

##### VPC (Virtual Private Cloud)

```hcl
# Complete VPC Setup
resource "aws_vpc" "main" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_hostnames = true
  enable_dns_support   = true

  tags = {
    Name = "MainVPC"
  }
}

# Public Subnets
resource "aws_subnet" "public" {
  count             = 3
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.${count.index + 1}.0/24"
  availability_zone = data.aws_availability_zones.available.names[count.index]
  
  map_public_ip_on_launch = true

  tags = {
    Name = "Public-${count.index + 1}"
    Type = "Public"
  }
}

# Private Subnets
resource "aws_subnet" "private" {
  count             = 3
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.${count.index + 10}.0/24"
  availability_zone = data.aws_availability_zones.available.names[count.index]

  tags = {
    Name = "Private-${count.index + 1}"
    Type = "Private"
  }
}

# Internet Gateway
resource "aws_internet_gateway" "main" {
  vpc_id = aws_vpc.main.id

  tags = {
    Name = "MainIGW"
  }
}

# NAT Gateways
resource "aws_eip" "nat" {
  count  = 3
  domain = "vpc"

  tags = {
    Name = "NAT-EIP-${count.index + 1}"
  }
}

resource "aws_nat_gateway" "main" {
  count         = 3
  allocation_id = aws_eip.nat[count.index].id
  subnet_id     = aws_subnet.public[count.index].id

  tags = {
    Name = "NAT-${count.index + 1}"
  }
}
```


### Advanced AWS Configurations

#### Security Groups with IP Whitelisting

```hcl
# Security Group with IP Whitelisting
resource "aws_security_group" "web" {
  name_prefix = "web-sg"
  vpc_id      = aws_vpc.main.id

  # HTTP access from whitelisted IPs only
  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = var.whitelisted_ips
  }

  # HTTPS access from whitelisted IPs only
  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = var.whitelisted_ips
  }

  # SSH access from bastion host only
  ingress {
    from_port       = 22
    to_port         = 22
    protocol        = "tcp"
    security_groups = [aws_security_group.bastion.id]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "WebSecurityGroup"
  }
}
```


#### WAF with IP Whitelisting

```hcl
# WAF IP Set for Whitelisting
resource "aws_wafv2_ip_set" "whitelist" {
  name               = "whitelist-ip-set"
  description        = "IP whitelist for web application"
  scope              = "CLOUDFRONT"
  ip_address_version = "IPV4"

  addresses = var.whitelisted_ips

  tags = {
    Name = "WhitelistIPSet"
  }
}

# WAF Web ACL
resource "aws_wafv2_web_acl" "main" {
  name  = "main-web-acl"
  scope = "CLOUDFRONT"

  default_action {
    block {}
  }

  rule {
    name     = "IPWhitelistRule"
    priority = 1

    override_action {
      none {}
    }

    statement {
      ip_set_reference_statement {
        arn = aws_wafv2_ip_set.whitelist.arn
      }
    }

    action {
      allow {}
    }

    visibility_config {
      cloudwatch_metrics_enabled = true
      metric_name                = "IPWhitelistRule"
      sampled_requests_enabled   = true
    }
  }

  visibility_config {
    cloudwatch_metrics_enabled = true
    metric_name                = "MainWebACL"
    sampled_requests_enabled   = true
  }
}
```


#### Load Balancer with Advanced Routing

```hcl
# Application Load Balancer
resource "aws_lb" "main" {
  name               = "main-alb"
  internal           = false
  load_balancer_type = "application"
  security_groups    = [aws_security_group.alb.id]
  subnets            = aws_subnet.public[*].id

  enable_deletion_protection = false

  tags = {
    Name = "MainALB"
  }
}

# Target Groups
resource "aws_lb_target_group" "web" {
  name     = "web-tg"
  port     = 80
  protocol = "HTTP"
  vpc_id   = aws_vpc.main.id

  health_check {
    enabled             = true
    healthy_threshold   = 2
    unhealthy_threshold = 2
    timeout             = 5
    interval            = 30
    path                = "/health"
    matcher             = "200"
    port                = "traffic-port"
    protocol            = "HTTP"
  }

  tags = {
    Name = "WebTargetGroup"
  }
}

resource "aws_lb_target_group" "api" {
  name     = "api-tg"
  port     = 8080
  protocol = "HTTP"
  vpc_id   = aws_vpc.main.id

  health_check {
    enabled             = true
    healthy_threshold   = 2
    unhealthy_threshold = 2
    timeout             = 5
    interval            = 30
    path                = "/api/health"
    matcher             = "200"
    port                = "traffic-port"
    protocol            = "HTTP"
  }

  tags = {
    Name = "APITargetGroup"
  }
}

# Listener with Path-Based Routing
resource "aws_lb_listener" "main" {
  load_balancer_arn = aws_lb.main.arn
  port              = "80"
  protocol          = "HTTP"

  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.web.arn
  }
}

resource "aws_lb_listener_rule" "api" {
  listener_arn = aws_lb_listener.main.arn
  priority     = 100

  action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.api.arn
  }

  condition {
    path_pattern {
      values = ["/api/*"]
    }
  }
}

resource "aws_lb_listener_rule" "admin" {
  listener_arn = aws_lb_listener.main.arn
  priority     = 200

  action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.admin.arn
  }

  condition {
    path_pattern {
      values = ["/admin/*"]
    }
  }

  condition {
    source_ip {
      values = var.admin_whitelist_ips
    }
  }
}
```


## Kubernetes: Container Orchestration

### What is Kubernetes?

Kubernetes is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications across clusters of machines.[^4][^5]

### Core Kubernetes Concepts

#### 1. Pods

The smallest deployable unit in Kubernetes, containing one or more containers.[^5]

```yaml
# basic-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.21
    ports:
    - containerPort: 80
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```


#### 2. Deployments

Manages the lifecycle of Pods and ReplicaSets.[^4]

```yaml
# nginx-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 5
```


#### 3. Services

Provides stable network endpoints for Pods.[^5]

```yaml
# nginx-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: LoadBalancer
---
# ClusterIP Service for internal communication
apiVersion: v1
kind: Service
metadata:
  name: nginx-internal
spec:
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: ClusterIP
```


#### 4. ConfigMaps and Secrets

Managing configuration data and sensitive information.[^5]

```yaml
# configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  database_url: "postgres://db:5432/myapp"
  cache_size: "100"
  debug: "false"
  app.properties: |
    database.url=postgres://db:5432/myapp
    cache.size=100
    debug=false
---
# secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secrets
type: Opaque
data:
  database_password: cGFzc3dvcmQxMjM=  # base64 encoded
  api_key: YWJjZGVmZ2hpams=              # base64 encoded
```


### Advanced Kubernetes Configurations

#### 1. Ingress Controllers

Managing external access to services with advanced routing.[^5]

```yaml
# ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
spec:
  tls:
  - hosts:
    - api.example.com
    - app.example.com
    secretName: app-tls
  rules:
  - host: api.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 80
  - host: app.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
      - path: /admin
        pathType: Prefix
        backend:
          service:
            name: admin-service
            port:
              number: 80
```


#### 2. Network Policies for IP Whitelisting

Controlling network traffic between pods.[^5][^6]

```yaml
# network-policy.yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: web-netpol
  namespace: production
spec:
  podSelector:
    matchLabels:
      app: web
  policyTypes:
  - Ingress
  - Egress
  ingress:
  # Allow traffic from specific IP ranges
  - from:
    - ipBlock:
        cidr: 10.0.0.0/8
        except:
        - 10.0.1.0/24
    - ipBlock:
        cidr: 172.16.0.0/12
    ports:
    - protocol: TCP
      port: 80
  # Allow traffic from specific namespaces
  - from:
    - namespaceSelector:
        matchLabels:
          name: api-namespace
    ports:
    - protocol: TCP
      port: 8080
  egress:
  # Allow outbound to database
  - to:
    - podSelector:
        matchLabels:
          app: database
    ports:
    - protocol: TCP
      port: 5432
  # Allow DNS
  - to: []
    ports:
    - protocol: UDP
      port: 53
```


#### 3. StatefulSets for Stateful Applications

Managing stateful workloads like databases.[^7]

```yaml
# postgres-statefulset.yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres
spec:
  serviceName: postgres
  replicas: 3
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres:13
        env:
        - name: POSTGRES_DB
          value: myapp
        - name: POSTGRES_USER
          value: postgres
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: postgres-secret
              key: password
        ports:
        - containerPort: 5432
        volumeMounts:
        - name: postgres-storage
          mountPath: /var/lib/postgresql/data
        resources:
          requests:
            memory: "1Gi"
            cpu: "500m"
          limits:
            memory: "2Gi"
            cpu: "1000m"
  volumeClaimTemplates:
  - metadata:
      name: postgres-storage
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 10Gi
```


#### 4. Horizontal Pod Autoscaler

Automatically scaling based on metrics.[^4]

```yaml
# hpa.yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: web-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: web-deployment
  minReplicas: 3
  maxReplicas: 50
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 300
      policies:
      - type: Percent
        value: 10
        periodSeconds: 60
    scaleUp:
      stabilizationWindowSeconds: 0
      policies:
      - type: Percent
        value: 100
        periodSeconds: 15
      - type: Pods
        value: 4
        periodSeconds: 15
      selectPolicy: Max
```


#### 5. Service Mesh with Istio

Advanced traffic management and security.[^5]

```yaml
# istio-gateway.yaml
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: app-gateway
spec:
  selector:
    istio: ingressgateway
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "*"
    tls:
      httpsRedirect: true
  - port:
      number: 443
      name: https
      protocol: HTTPS
    tls:
      mode: SIMPLE
      credentialName: app-tls
    hosts:
    - "app.example.com"
---
# Virtual Service for routing
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: app-vs
spec:
  hosts:
  - "app.example.com"
  gateways:
  - app-gateway
  http:
  - match:
    - uri:
        prefix: "/api/v1"
    route:
    - destination:
        host: api-v1-service
        port:
          number: 80
  - match:
    - uri:
        prefix: "/api/v2"
    route:
    - destination:
        host: api-v2-service
        port:
          number: 80
  - match:
    - uri:
        prefix: "/"
    route:
    - destination:
        host: web-service
        port:
          number: 80
```


### Kubernetes Security Best Practices

#### 1. Pod Security Standards

```yaml
# pod-security-policy.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: secure-namespace
  labels:
    pod-security.kubernetes.io/enforce: restricted
    pod-security.kubernetes.io/audit: restricted
    pod-security.kubernetes.io/warn: restricted
---
# SecurityContext in Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: secure-app
  namespace: secure-namespace
spec:
  replicas: 3
  selector:
    matchLabels:
      app: secure-app
  template:
    metadata:
      labels:
        app: secure-app
    spec:
      securityContext:
        runAsNonRoot: true
        runAsUser: 1000
        runAsGroup: 3000
        fsGroup: 2000
        seccompProfile:
          type: RuntimeDefault
      containers:
      - name: app
        image: myapp:latest
        securityContext:
          allowPrivilegeEscalation: false
          readOnlyRootFilesystem: true
          capabilities:
            drop:
            - ALL
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
```


#### 2. RBAC (Role-Based Access Control)

```yaml
# rbac.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: app-service-account
  namespace: production
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: production
  name: app-role
rules:
- apiGroups: [""]
  resources: ["pods", "services", "configmaps"]
  verbs: ["get", "list", "watch"]
- apiGroups: ["apps"]
  resources: ["deployments"]
  verbs: ["get", "list", "watch", "update", "patch"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: app-rolebinding
  namespace: production
subjects:
- kind: ServiceAccount
  name: app-service-account
  namespace: production
roleRef:
  kind: Role
  name: app-role
  apiGroup: rbac.authorization.k8s.io
```


## Advanced Configurations and Use Cases

### IP Whitelisting Strategies

#### 1. Terraform IP Whitelist Module

```hcl
# modules/ip-whitelist/main.tf
locals {
  # Office IPs
  office_ips = [
    "203.0.113.0/24",    # Main office
    "198.51.100.0/24",   # Branch office
  ]
  
  # Developer IPs
  developer_ips = [
    "192.0.2.100/32",    # Developer 1
    "192.0.2.101/32",    # Developer 2
  ]
  
  # CI/CD IPs
  cicd_ips = [
    "203.0.113.50/32",   # Jenkins
    "203.0.113.51/32",   # GitHub Actions
  ]
  
  # Combined whitelist
  all_whitelisted_ips = concat(
    local.office_ips,
    local.developer_ips,
    local.cicd_ips
  )
}

# Output for use in other modules
output "whitelisted_cidrs" {
  description = "List of whitelisted CIDR blocks"
  value       = local.all_whitelisted_ips
}

output "office_cidrs" {
  description = "Office CIDR blocks only"
  value       = local.office_ips
}

output "developer_cidrs" {
  description = "Developer CIDR blocks only"
  value       = local.developer_ips
}
```


#### 2. Dynamic IP Whitelisting with AWS Lambda

```python
# lambda_function.py
import boto3
import json
import requests

def lambda_handler(event, context):
    """
    Automatically update security group rules based on external IP changes
    """
    ec2 = boto3.client('ec2')
    
    # Get current public IP of office
    office_ip = requests.get('https://api.ipify.org').text
    
    # Security group ID to update
    sg_id = 'sg-1234567890abcdef0'
    
    try:
        # Remove old rules
        current_rules = ec2.describe_security_groups(GroupIds=[sg_id])
        for rule in current_rules['SecurityGroups'][^0]['IpPermissions']:
            if rule.get('IpRanges'):
                for ip_range in rule['IpRanges']:
                    if ip_range.get('Description') == 'Office IP':
                        ec2.revoke_security_group_ingress(
                            GroupId=sg_id,
                            IpPermissions=[rule]
                        )
        
        # Add new rule with current IP
        ec2.authorize_security_group_ingress(
            GroupId=sg_id,
            IpPermissions=[
                {
                    'IpProtocol': 'tcp',
                    'FromPort': 443,
                    'ToPort': 443,
                    'IpRanges': [
                        {
                            'CidrIp': f'{office_ip}/32',
                            'Description': 'Office IP'
                        }
                    ]
                }
            ]
        )
        
        return {
            'statusCode': 200,
            'body': json.dumps(f'Updated office IP to {office_ip}')
        }
        
    except Exception as e:
        return {
            'statusCode': 500,
            'body': json.dumps(f'Error: {str(e)}')
        }
```


### Advanced Routing Configurations

#### 1. Multi-Environment Terraform Setup

```hcl
# environments/prod/main.tf
module "vpc" {
  source = "../../modules/vpc"
  
  environment = "prod"
  vpc_cidr    = "10.0.0.0/16"
  
  public_subnet_cidrs  = ["10.0.1.0/24", "10.0.2.0/24", "10.0.3.0/24"]
  private_subnet_cidrs = ["10.0.10.0/24", "10.0.11.0/24", "10.0.12.0/24"]
  
  enable_nat_gateway = true
  enable_vpn_gateway = true
}

module "routing" {
  source = "../../modules/routing"
  
  vpc_id                = module.vpc.vpc_id
  internet_gateway_id   = module.vpc.internet_gateway_id
  nat_gateway_ids       = module.vpc.nat_gateway_ids
  public_subnet_ids     = module.vpc.public_subnet_ids
  private_subnet_ids    = module.vpc.private_subnet_ids
  
  # Custom routes
  custom_routes = [
    {
      route_table_id         = module.vpc.private_route_table_ids[^0]
      destination_cidr_block = "192.168.0.0/16"
      vpc_peering_connection_id = aws_vpc_peering_connection.cross_region.id
    }
  ]
}
```


#### 2. Advanced Load Balancer Routing

```hcl
# Advanced ALB with multiple target groups
resource "aws_lb" "main" {
  name               = "main-alb"
  internal           = false
  load_balancer_type = "application"
  security_groups    = [aws_security_group.alb.id]
  subnets            = var.public_subnet_ids

  enable_deletion_protection = true

  access_logs {
    bucket  = aws_s3_bucket.alb_logs.bucket
    prefix  = "alb-logs"
    enabled = true
  }
}

# Multiple target groups for different services
resource "aws_lb_target_group" "web" {
  name     = "web-tg"
  port     = 80
  protocol = "HTTP"
  vpc_id   = var.vpc_id

  health_check {
    enabled             = true
    healthy_threshold   = 2
    unhealthy_threshold = 2
    timeout             = 5
    interval            = 30
    path                = "/health"
    matcher             = "200"
  }

  stickiness {
    type            = "lb_cookie"
    cookie_duration = 86400
    enabled         = true
  }
}

# Advanced listener rules with multiple conditions
resource "aws_lb_listener" "main" {
  load_balancer_arn = aws_lb.main.arn
  port              = "443"
  protocol          = "HTTPS"
  ssl_policy        = "ELBSecurityPolicy-TLS-1-2-2017-01"
  certificate_arn   = aws_acm_certificate.main.arn

  default_action {
    type = "fixed-response"
    fixed_response {
      content_type = "text/plain"
      message_body = "Not Found"
      status_code  = "404"
    }
  }
}

# API routing with version control
resource "aws_lb_listener_rule" "api_v1" {
  listener_arn = aws_lb_listener.main.arn
  priority     = 100

  action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.api_v1.arn
  }

  condition {
    path_pattern {
      values = ["/api/v1/*"]
    }
  }

  condition {
    http_header {
      http_header_name = "X-API-Version"
      values          = ["v1"]
    }
  }
}

# Geo-based routing
resource "aws_lb_listener_rule" "geo_routing" {
  listener_arn = aws_lb_listener.main.arn
  priority     = 150

  action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.eu_service.arn
  }

  condition {
    path_pattern {
      values = ["/app/*"]
    }
  }

  condition {
    http_header {
      http_header_name = "CloudFront-Viewer-Country"
      values          = ["DE", "FR", "UK", "IT"]
    }
  }
}
```


### Container Registry and CI/CD Integration

#### 1. ECR with Terraform

```hcl
# ECR Repository
resource "aws_ecr_repository" "app" {
  name                 = "myapp"
  image_tag_mutability = "MUTABLE"

  image_scanning_configuration {
    scan_on_push = true
  }

  lifecycle_policy {
    policy = jsonencode({
      rules = [
        {
          rulePriority = 1
          description  = "Keep last 30 production images"
          selection = {
            tagStatus     = "tagged"
            tagPrefixList = ["prod"]
            countType     = "imageCountMoreThan"
            countNumber   = 30
          }
          action = {
            type = "expire"
          }
        },
        {
          rulePriority = 2
          description  = "Keep last 10 development images"
          selection = {
            tagStatus     = "tagged"
            tagPrefixList = ["dev"]
            countType     = "imageCountMoreThan"
            countNumber   = 10
          }
          action = {
            type = "expire"
          }
        }
      ]
    })
  }
}

# ECR Repository Policy
resource "aws_ecr_repository_policy" "app" {
  repository = aws_ecr_repository.app.name

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Sid    = "AllowPull"
        Effect = "Allow"
        Principal = {
          AWS = [
            "arn:aws:iam::${data.aws_caller_identity.current.account_id}:role/EKSNodeGroupRole",
            "arn:aws:iam::${data.aws_caller_identity.current.account_id}:role/EKSClusterServiceRole"
          ]
        }
        Action = [
          "ecr:BatchCheckLayerAvailability",
          "ecr:BatchGetImage",
          "ecr:GetDownloadUrlForLayer"
        ]
      }
    ]
  })
}
```


#### 2. GitOps with ArgoCD

```yaml
# argocd-application.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: myapp
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/mycompany/myapp-manifests
    targetRevision: HEAD
    path: k8s/overlays/production
  destination:
    server: https://kubernetes.default.svc
    namespace: production
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
      allowEmpty: false
    syncOptions:
    - CreateNamespace=true
    - PrunePropagationPolicy=foreground
    - PruneLast=true
    retry:
      limit: 5
      backoff:
        duration: 5s
        factor: 2
        maxDuration: 3m
```


## Real-World Integration Examples

### Complete E-commerce Platform

#### 1. Infrastructure Setup with Terraform

```hcl
# main.tf - Complete e-commerce infrastructure
terraform {
  required_version = ">= 1.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
    kubernetes = {
      source  = "hashicorp/kubernetes"
      version = "~> 2.0"
    }
  }
}

# VPC and Networking
module "vpc" {
  source = "./modules/vpc"
  
  name = "ecommerce-vpc"
  cidr = "10.0.0.0/16"
  
  azs             = ["us-west-2a", "us-west-2b", "us-west-2c"]
  private_subnets = ["10.0.1.0/24", "10.0.2.0/24", "10.0.3.0/24"]
  public_subnets  = ["10.0.101.0/24", "10.0.102.0/24", "10.0.103.0/24"]
  
  enable_nat_gateway = true
  enable_vpn_gateway = true
  
  tags = {
    Environment = "production"
    Project     = "ecommerce"
  }
}

# EKS Cluster
module "eks" {
  source = "./modules/eks"
  
  cluster_name    = "ecommerce-cluster"
  cluster_version = "1.27"
  
  vpc_id     = module.vpc.vpc_id
  subnet_ids = module.vpc.private_subnets
  
  node_groups = {
    general = {
      desired_capacity = 3
      max_capacity     = 10
      min_capacity     = 1
      
      instance_types = ["t3.medium"]
      
      k8s_labels = {
        Environment = "production"
        GithubRepo  = "ecommerce-platform"
      }
    }
    
    high_memory = {
      desired_capacity = 1
      max_capacity     = 5
      min_capacity     = 0
      
      instance_types = ["r5.large"]
      
      k8s_labels = {
        WorkloadType = "memory-intensive"
      }
      
      taints = {
        high-memory = {
          key    = "workload-type"
          value  = "memory-intensive"
          effect = "NO_SCHEDULE"
        }
      }
    }
  }
}

# RDS for main database
module "rds" {
  source = "./modules/rds"
  
  identifier = "ecommerce-db"
  
  engine         = "postgres"
  engine_version = "14.9"
  instance_class = "db.r6g.large"
  
  allocated_storage     = 100
  max_allocated_storage = 1000
  
  db_name  = "ecommerce"
  username = "dbadmin"
  password = var.db_password
  
  vpc_security_group_ids = [aws_security_group.rds.id]
  db_subnet_group_name   = aws_db_subnet_group.main.name
  
  backup_retention_period = 7
  backup_window          = "07:00-09:00"
  maintenance_window     = "Mon:00:00-Mon:03:00"
  
  enabled_cloudwatch_logs_exports = ["postgresql"]
  
  tags = {
    Environment = "production"
    Project     = "ecommerce"
  }
}

# ElastiCache for Redis
resource "aws_elasticache_subnet_group" "main" {
  name       = "ecommerce-cache-subnet"
  subnet_ids = module.vpc.private_subnets
}

resource "aws_elasticache_replication_group" "main" {
  replication_group_id       = "ecommerce-redis"
  description                = "Redis cluster for ecommerce platform"
  
  node_type            = "cache.r6g.large"
  port                 = 6379
  parameter_group_name = "default.redis7"
  
  num_cache_clusters = 3
  
  subnet_group_name  = aws_elasticache_subnet_group.main.name
  security_group_ids = [aws_security_group.redis.id]
  
  at_rest_encryption_enabled = true
  transit_encryption_enabled = true
  auth_token                 = var.redis_auth_token
  
  tags = {
    Environment = "production"
    Project     = "ecommerce"
  }
}
```


#### 2. Kubernetes Manifests for Microservices

```yaml
# namespace.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: ecommerce
  labels:
    name: ecommerce
    pod-security.kubernetes.io/enforce: restricted
    pod-security.kubernetes.io/audit: restricted
    pod-security.kubernetes.io/warn: restricted
---
# user-service deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: user-service
  namespace: ecommerce
spec:
  replicas: 3
  selector:
    matchLabels:
      app: user-service
  template:
    metadata:
      labels:
        app: user-service
    spec:
      serviceAccountName: user-service-sa
      securityContext:
        runAsNonRoot: true
        runAsUser: 1000
        fsGroup: 2000
      containers:
      - name: user-service
        image: mycompany/user-service:v1.2.3
        ports:
        - containerPort: 8080
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: database-credentials
              key: url
        - name: REDIS_URL
          valueFrom:
            secretKeyRef:
              name: redis-credentials
              key: url
        - name: JWT_SECRET
          valueFrom:
            secretKeyRef:
              name: jwt-secret
              key: secret
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 5
        securityContext:
          allowPrivilegeEscalation: false
          readOnlyRootFilesystem: true
          capabilities:
            drop:
            - ALL
---
# product-service deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: product-service
  namespace: ecommerce
spec:
  replicas: 5
  selector:
    matchLabels:
      app: product-service
  template:
    metadata:
      labels:
        app: product-service
    spec:
      containers:
      - name: product-service
        image: mycompany/product-service:v2.1.0
        ports:
        - containerPort: 8080
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: database-credentials
              key: url
        - name: ELASTICSEARCH_URL
          valueFrom:
            configMapKeyRef:
              name: search-config
              key: elasticsearch_url
        resources:
          requests:
            memory: "512Mi"
            cpu: "500m"
          limits:
            memory: "1Gi"
            cpu: "1000m"
---
# order-service with high memory requirements
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order-service
  namespace: ecommerce
spec:
  replicas: 2
  selector:
    matchLabels:
      app: order-service
  template:
    metadata:
      labels:
        app: order-service
    spec:
      tolerations:
      - key: "workload-type"
        operator: "Equal"
        value: "memory-intensive"
        effect: "NoSchedule"
      nodeSelector:
        WorkloadType: "memory-intensive"
      containers:
      - name: order-service
        image: mycompany/order-service:v1.5.2
        ports:
        - containerPort: 8080
        resources:
          requests:
            memory: "2Gi"
            cpu: "1000m"
          limits:
            memory: "4Gi"
            cpu: "2000m"
```


#### 3. Advanced Service Mesh Configuration

```yaml
# istio-gateway.yaml
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: ecommerce-gateway
  namespace: ecommerce
spec:
  selector:
    istio: ingressgateway
  servers:
  - port:
      number: 443
      name: https
      protocol: HTTPS
    tls:
      mode: SIMPLE
      credentialName: ecommerce-tls
    hosts:
    - "api.ecommerce.com"
    - "app.ecommerce.com"
---
# Virtual Service with advanced routing
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: ecommerce-api
  namespace: ecommerce
spec:
  hosts:
  - "api.ecommerce.com"
  gateways:
  - ecommerce-gateway
  http:
  # API v2 routing with header-based routing
  - match:
    - uri:
        prefix: "/api/v2/"
    - headers:
        "x-api-version":
          exact: "2.0"
    route:
    - destination:
        host: api-v2-service
        port:
          number: 80
    timeout: 30s
    retries:
      attempts: 3
      perTryTimeout: 10s
  
  # Canary deployment for user service
  - match:
    - uri:
        prefix: "/api/v1/users"
    route:
    - destination:
        host: user-service
        subset: stable
      weight: 90
    - destination:
        host: user-service
        subset: canary
      weight: 10
    fault:
      delay:
        percentage:
          value: 0.1
        fixedDelay: 5s
  
  # Default routing
  - match:
    - uri:
        prefix: "/api/"
    route:
    - destination:
        host: api-service
        port:
          number: 80
    timeout: 15s
---
# Destination Rule for traffic policies
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: user-service
  namespace: ecommerce
spec:
  host: user-service
  trafficPolicy:
    loadBalancer:
      simple: LEAST_CONN
    connectionPool:
      tcp:
        maxConnections: 100
      http:
        http1MaxPendingRequests: 50
        maxRequestsPerConnection: 10
    circuitBreaker:
      consecutiveErrors: 5
      interval: 30s
      baseEjectionTime: 30s
      maxEjectionPercent: 50
  subsets:
  - name: stable
    labels:
      version: stable
  - name: canary
    labels:
      version: canary
```


### Monitoring and Observability

#### 1. Prometheus and Grafana Setup

```yaml
# prometheus-config.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: prometheus-config
  namespace: monitoring
data:
  prometheus.yml: |
    global:
      scrape_interval: 15s
      evaluation_interval: 15s
    
    rule_files:
      - "*.rules"
    
    alerting:
      alertmanagers:
        - static_configs:
            - targets:
              - alertmanager:9093
    
    scrape_configs:
      - job_name: 'kubernetes-apiservers'
        kubernetes_sd_configs:
        - role: endpoints
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        relabel_configs:
        - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
          action: keep
          regex: default;kubernetes;https
      
      - job_name: 'ecommerce-services'
        kubernetes_sd_configs:
        - role: endpoints
          namespaces:
            names:
            - ecommerce
        relabel_configs:
        - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_service_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        - action: labelmap
          regex: __meta_kubernetes_service_label_(.+)
        - source_labels: [__meta_kubernetes_namespace]
          action: replace
          target_label: kubernetes_namespace
        - source_labels: [__meta_kubernetes_service_name]
          action: replace
          target_label: kubernetes_name
```


#### 2. Alerting Rules

```yaml
# alert-rules.yaml
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: ecommerce-alerts
  namespace: monitoring
spec:
  groups:
  - name: ecommerce.rules
    rules:
    - alert: HighErrorRate
      expr: |
        (
          rate(http_requests_total{status=~"5.."}[5m]) /
          rate(http_requests_total[5m])
        ) > 0.05
      for: 5m
      labels:
        severity: warning
      annotations:
        summary: "High error rate detected"
        description: "Error rate is {{ $value | humanizePercentage }} for {{ $labels.service }}"
    
    - alert: HighLatency
      expr: |
        histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m])) > 0.5
      for: 5m
      labels:
        severity: warning
      annotations:
        summary: "High latency detected"
        description: "95th percentile latency is {{ $value }}s for {{ $labels.service }}"
    
    - alert: PodCrashLooping
      expr: |
        rate(kube_pod_container_status_restarts_total[15m]) > 0
      for: 5m
      labels:
        severity: critical
      annotations:
        summary: "Pod is crash looping"
        description: "Pod {{ $labels.pod }} in namespace {{ $labels.namespace }} is crash looping"
    
    - alert: DatabaseConnectionsHigh
      expr: |
        pg_stat_activity_count{state="active"} > 80
      for: 5m
      labels:
        severity: warning
      annotations:
        summary: "High database connections"
        description: "Database has {{ $value }} active connections"
```

This comprehensive documentation provides a complete guide from basic concepts to advanced real-world implementations for Terraform, AWS, and Kubernetes. The examples cover everything from simple resource creation to complex multi-service architectures with advanced networking, security, and monitoring configurations. Each section builds upon the previous one, ensuring a smooth learning progression from beginner to advanced levels.[^1][^2][^4][^5]

<div style="text-align: center">⁂</div>

[^1]: https://developer.hashicorp.com/terraform/tutorials

[^2]: https://dev.to/devopsking/the-ultimate-terraform-tutorial-from-beginner-to-advanced-2024-guide-3n1o

[^3]: https://aws.amazon.com/blogs/mt/query-your-resource-configuration-state-using-the-advanced-query-feature-of-aws-config/

[^4]: https://vlinkinfo.com/blog/top-kubernetes-use-cases

[^5]: https://www.scaler.com/topics/kubernetes/kubernetes-advanced-networking/

[^6]: https://kubernetes.io/docs/concepts/cluster-administration/networking/

[^7]: https://konghq.com/blog/learning-center/what-is-kubernetes

[^8]: https://www.youtube.com/watch?v=7xngnjfIlK4

[^9]: https://spacelift.io/blog/terraform-tutorial

[^10]: https://k21academy.com/terraform-iac/terraform-beginners-guide/

[^11]: https://www.webasha.com/blog/mastering-ip-whitelisting-what-are-advanced-strategies-and-real-time-security-examples

[^12]: https://www.youtube.com/watch?v=YyUwF_jvyok

[^13]: https://www.youtube.com/watch?v=hrwZ-iND3bs

[^14]: https://docs.aws.amazon.com/aws-supply-chain/latest/adminguide/create-instance-advanced.html

[^15]: https://github.com/hashicorp/terraform-provider-google-beta/blob/main/examples/cloud-armor/README.md

[^16]: https://docs.aws.amazon.com/vpc/latest/userguide/aws-ip-ranges.html

[^17]: https://zeet.co/blog/terraform-tutorial

[^18]: https://docs.aws.amazon.com/managedservices/latest/userguide/ams-modes-use-cases.html

[^19]: https://dev.to/abhay_yt_52a8e72b213be229/top-100-kubernetes-topics-to-master-for-developers-and-devops-engineers-5g83

[^20]: https://jonnyzzz.com/blog/2019/04/29/terraform-waf/

