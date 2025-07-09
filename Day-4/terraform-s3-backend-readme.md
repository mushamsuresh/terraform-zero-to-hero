# Terraform Remote State with S3 & DynamoDB

This is a minimal example to practice using an **S3 bucket** and **DynamoDB table** as the backend for storing Terraform state securely.

---

## 🏗️ Step 1: Create S3 Bucket and DynamoDB Table

Create a file named `s3_backend_setup.tf` with the following:

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_s3_bucket" "tf_state" {
  bucket = "my-terraform-state-suresh123" # Must be globally unique
}

resource "aws_dynamodb_table" "tf_lock" {
  name           = "terraform-lock"
  billing_mode   = "PAY_PER_REQUEST"
  hash_key       = "LockID"

  attribute {
    name = "LockID"
    type = "S"
  }
}
```

Then run:

```bash
terraform init
terraform apply
```

This will create:
- S3 bucket: stores your `terraform.tfstate`
- DynamoDB table: used for state locking

---

## 🛠️ Step 2: Configure Terraform to Use the Backend

In a **new project folder**, create a `main.tf` file:

```hcl
terraform {
  backend "s3" {
    bucket         = "my-terraform-state-suresh123"
    key            = "dev/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-lock"
    encrypt        = true
  }
}

provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "test" {
  ami           = "ami-0c55b159cbfafe1f0" # Update as per region
  instance_type = "t2.micro"
}
```

---

## ⚙️ Step 3: Initialize and Apply

Run the following commands in the new project directory:

```bash
terraform init
terraform apply
```

Terraform will ask if you want to copy the state to the backend — say `yes`.

---

## 🔐 Security Notes

- State file is stored in a **private S3 bucket** with encryption.
- **DynamoDB table** prevents concurrent changes using state locking.
- Use **IAM policies** to control who can access the S3 bucket and table.

---

## ✅ Resources Created

| Resource Type | Name               |
|---------------|--------------------|
| S3 Bucket     | my-terraform-state-suresh123 |
| DynamoDB      | terraform-lock     |
| EC2 Instance  | aws_instance.test  |

---

## 📘 References

- [Terraform Backends](https://developer.hashicorp.com/terraform/language/settings/backends/s3)
- [State Locking](https://developer.hashicorp.com/terraform/language/state/locking)
