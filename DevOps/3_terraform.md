# 1️⃣ What is Terraform?

**Terraform** is an **Infrastructure as Code (IaC)** tool created by **HashiCorp**.

👉 It lets you define **cloud infrastructure using declarative configuration files** and then **automatically creates, updates, or deletes resources**.

Instead of clicking in AWS Console:

* Create EC2
* Create VPC
* Attach IAM
* Configure security groups

You write **code**, and Terraform does it for you.

---

# 2️⃣ Why Terraform? (Problem it Solves)

### ❌ Without Terraform

* Manual AWS console clicks
* Error-prone
* Not reproducible
* No version control
* Hard to rollback

### ✅ With Terraform

* Infrastructure as **code**
* **Repeatable & consistent**
* Versioned (Git)
* Automated deployments
* Easy rollback & auditing

Think of Terraform as:

> **“Git for Infrastructure”**

---

# 3️⃣ Key Concepts in Terraform

Let’s understand the building blocks.

---

## 3.1 Infrastructure as Code (IaC)

You describe **WHAT you want**, not HOW to do it.

Example:

```hcl
resource "aws_s3_bucket" "my_bucket" {
  bucket = "my-company-data"
}
```

Terraform figures out:

* API calls
* Creation order
* Dependencies

---

## 3.2 Declarative Language (HCL)

Terraform uses **HCL (HashiCorp Configuration Language)**.

Example:

```hcl
resource "aws_instance" "server" {
  ami           = "ami-0abcdef"
  instance_type = "t2.micro"
}
```

You say:

> “I want one EC2 instance”

Terraform handles the rest.

---

## 3.3 Providers

Providers tell Terraform **which cloud or service to talk to**.

Examples:

* AWS
* Azure
* GCP
* Kubernetes
* Databricks
* Snowflake

Example:

```hcl
provider "aws" {
  region = "ap-south-1"
}
```

---

## 3.4 Resources

Resources are **actual infrastructure objects**.

Examples:

* EC2
* S3
* IAM Role
* VPC
* SageMaker Endpoint

Example:

```hcl
resource "aws_s3_bucket" "data" {
  bucket = "ml-training-data"
}
```

---

## 3.5 State File (Very Important 🔥)

Terraform maintains a **state file** (`terraform.tfstate`).

### What is inside state?

* What resources exist
* Resource IDs
* Current configuration
* Dependencies

Terraform compares:

```
Code (desired state) vs State (current state)
```

Then decides:

* Create
* Update
* Destroy

👉 **State file is critical** → usually stored in **S3 + DynamoDB lock** in production.

---

# 4️⃣ Terraform Workflow (Very Important)

Terraform follows a **standard lifecycle**:

### Step 1: Initialize

```bash
terraform init
```

* Downloads providers
* Sets up backend
* Prepares environment

---

### Step 2: Plan

```bash
terraform plan
```

Shows:

* What will be created
* What will be modified
* What will be destroyed

👉 No changes yet.

---

### Step 3: Apply

```bash
terraform apply
```

* Executes the plan
* Creates real infrastructure

---

### Step 4: Destroy (optional)

```bash
terraform destroy
```

* Deletes everything defined in code

---

# 5️⃣ Example: Create EC2 in AWS

### provider.tf

```hcl
provider "aws" {
  region = "ap-south-1"
}
```

### main.tf

```hcl
resource "aws_instance" "my_ec2" {
  ami           = "ami-0abcdef123"
  instance_type = "t2.micro"

  tags = {
    Name = "Terraform-EC2"
  }
}
```

### Commands:

```bash
terraform init
terraform plan
terraform apply
```

👉 EC2 instance is created automatically.

---

# 6️⃣ Variables & Outputs

### Variables (Reusable Config)

```hcl
variable "instance_type" {
  default = "t2.micro"
}
```

Use it:

```hcl
instance_type = var.instance_type
```

---

### Outputs (Expose Values)

```hcl
output "ec2_ip" {
  value = aws_instance.my_ec2.public_ip
}
```

After apply:

```bash
terraform output
```

---

# 7️⃣ Terraform Modules (Very Important for Companies)

Modules = **Reusable Infrastructure Components**

Example modules:

* VPC module
* EC2 module
* EKS module
* SageMaker module

Structure:

```
modules/
  ec2/
    main.tf
    variables.tf
    outputs.tf
```

Usage:

```hcl
module "ec2" {
  source        = "./modules/ec2"
  instance_type = "t3.medium"
}
```

👉 Makes infra **clean, reusable, scalable**.

---

# 8️⃣ Terraform Backend (State Storage)

### ❌ Local backend (default)

* State stored on laptop
* Not safe for teams

### ✅ Remote backend (Best Practice)

AWS Example:

```hcl
backend "s3" {
  bucket         = "terraform-state-bucket"
  key            = "prod/terraform.tfstate"
  region         = "ap-south-1"
  dynamodb_table = "terraform-lock"
}
```

Benefits:

* Team collaboration
* State locking
* Disaster recovery

---

# 9️⃣ Terraform vs CloudFormation

| Feature          | Terraform | CloudFormation |
| ---------------- | --------- | -------------- |
| Multi-cloud      | ✅ Yes     | ❌ No           |
| Language         | HCL       | JSON/YAML      |
| Reusable modules | ✅ Strong  | Limited        |
| Community        | Huge      | AWS only       |
| Learning curve   | Easy      | Medium         |

👉 Most companies prefer **Terraform**.

---

# 🔟 Terraform in MLOps (Your Context 🔥)

You can use Terraform to create:

* S3 buckets (training data)
* SageMaker notebooks
* SageMaker endpoints
* ECR repositories
* IAM roles
* EKS clusters
* Airflow (MWAA)
* Feature stores

Example:

```hcl
resource "aws_sagemaker_endpoint" "model" {
  name = "embedding-endpoint"
}
```

---

# 1️⃣1️⃣ Best Practices (Industry Level)

✅ Use modules
✅ Remote state (S3 + DynamoDB)
✅ Separate environments (dev / staging / prod)
✅ Use `terraform plan` in CI/CD
✅ Never commit state file
✅ Use `terraform fmt` & `terraform validate`

---

# 1️⃣2️⃣ Terraform Interview Summary (One-Liner)

> **Terraform is an Infrastructure as Code tool that uses a declarative language to provision and manage cloud resources in a reproducible, version-controlled, and automated manner.**

---
