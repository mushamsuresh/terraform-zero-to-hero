# MIGRATION TO TERRAFORM & DRIFT DETECTION

create an instance in aws.
create an main.tf file like below
---------------------------------------
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "example" {
  
}
----------------------------------------
Run command terraform init
  # terraform import (resource).(resource_name) instance_id
Run command terrform import aws_instance.example i-07061e58bbc540588
new statefile with all the instance attributes were created
