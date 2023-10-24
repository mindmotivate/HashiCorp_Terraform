
# Launching an AWS VPC in Terraform

Terraform is an infrastructure as code (IaC) tool that allows you to manage your AWS infrastructure using declarative code. This means that you can define your desired infrastructure state in Terraform code, and Terraform will take care of creating, updating, and deleting resources to achieve that state.

# VPC / Terraform Basics

To launch an AWS VPC in Terraform, you will need to create a Terraform configuration file that defines the VPC resources that you want to create. This configuration file can be as simple as the following:
``````
resource "aws_vpc" "default" {
  cidr_block = "10.0.0.0/16"
}
```````
This configuration will create a VPC with the CIDR block 10.0.0.0/16. You can then use Terraform to create other resources in your VPC, such as subnets, internet gateways, and routing tables.

Once you have created your Terraform configuration file, you can use the following commands to launch your VPC:

`terraform init`: This command will initialize Terraform and download the necessary providers and modules.<br>
`terraform plan`: This command will generate a plan of the changes that Terraform will make to your infrastructure.<br>
`terraform apply`: This command will apply the changes that Terraform planned in the previous step.<br>