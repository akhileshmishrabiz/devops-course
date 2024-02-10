# install tfenv : https://github.com/tfutils/tfenv
This is day 2 of the 3-month DevOps course.

Timing - 12 - 3 PM meeting link: https://meet.google.com/fvo-fxvx-pug Agenda for the day

Agenda:
1. Introduction to Terraform
2. getting started with Terraform
3. Understanding Terraform fundamentals

####### Small break #################

4. Deploying VPC, private.public subnet, routers, nat gateway, internet gateway, and private and public EC2 instances using Terraform

### Small Break #################

5. IAM and how it works in AWS and best practices




## Code usage ##

variables.tf— This file is used to define input variables for your Terraform configuration

output.tf — It is to define output values that you want to display after the Terraform apply

terraform.tfvars — This file is used to provide values for the variables defined in variables.tf



1. clone the repo

2. Initialize the Terraform 

terraform init

3. Plan the resource deployment

terraform plan

4. Apply the resources

terraform apply

5. To undo the deployment

terraform destroy
