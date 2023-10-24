
# Launching an AWS VPC in Terraform

Terraform is an infrastructure as code (IaC) tool that allows you to manage your AWS infrastructure using declarative code. This means that you can define your desired infrastructure state in Terraform code, and Terraform will take care of creating, updating, and deleting resources to achieve that state.

# VPC / Terraform Basics

Let's quickly check and make sure you have terraform installed on your local machine via VSCode:

Using the following command in your terminal, you can check and see what version you currently have:
````
terraform -version
Terraform v1.5.6
on windows_amd64
````

Ok Great! Now lets provide credentials and define the region which which we will set up our VPC
````
provider "aws" {
  region     = "us-east-1"
  access_key = "my-access-key"
  secret_key = "my-secret-key"
}
````
This code can be found here [TFM::Registry](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)<br>

To launch an AWS VPC in Terraform, you will need to create a Terraform configuration file that defines the VPC resources that you want to create. This configuration file can be as simple as the following:
``````
resource "aws_vpc" "default" {
  cidr_block = "10.0.0.0/16"
}
```````
This configuration will create a VPC with the CIDR block 10.0.0.0/16. You can then use Terraform to create other resources in your VPC, such as subnets, internet gateways, and routing tables.

In this example we will create a subnet CIDR block. Let's use the following code:
````
resource "aws_subnet" "main" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.36.0.0/16"

  tags = {
    Name = "Main"
  }
}
````
This code can be found here: [TFM::Registry](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/subnet)<br>

Next we will add our Public and Private Subnets:
````
# us-east-1a"
resource "aws_subnet" "public_subnet_a" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.36.1.0/24"
  availability_zone = "us-east-1a"
  map_public_ip_on_launch = true
}
# us-east-1a"
resource "aws_subnet" "private_subnet_a" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.36.11.0/24"
  availability_zone = "us-east-1a"
  map_public_ip_on_launch = false
}
# us-east-1b"
resource "aws_subnet" "public_subnet_b" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.36.2.0/24"
  availability_zone = "us-east-1b"
  map_public_ip_on_launch = true
}
# us-east-1b"
resource "aws_subnet" "private_subnet_b" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.36.12.0/24"
  availability_zone = "us-east-1b"
  map_public_ip_on_launch = false
}
````

Now we will build our Internet Gateway:
````
resource "aws_internet_gateway" "gw" {
  vpc_id = aws_vpc.main.id

  tags = {
    Name = "main"
  }
}
````
This code can be found here: [TFM::Registry](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/internet_gateway)


OK Here is the final script:
````
provider "aws" {
  region     = "us-east-1"
  access_key = ""
  secret_key = ""

}

# Create a VPC
resource "aws_vpc" "example" {
  cidr_block = "10.36.0.0/16"
}

# us-east-1a"
resource "aws_subnet" "public_subnet_a" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.36.1.0/24"
  availability_zone = "us-east-1a"
  map_public_ip_on_launch = true
}
# us-east-1a"
resource "aws_subnet" "private_subnet_a" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.36.11.0/24"
  availability_zone = "us-east-1a"
  map_public_ip_on_launch = false
}
# us-east-1b"
resource "aws_subnet" "public_subnet_b" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.36.2.0/24"
  availability_zone = "us-east-1b"
  map_public_ip_on_launch = true
}
# us-east-1b"
resource "aws_subnet" "private_subnet_b" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.36.12.0/24"
  availability_zone = "us-east-1b"
  map_public_ip_on_launch = false
}
# internet gateway
resource "aws_internet_gateway" "gw" {
  vpc_id = aws_vpc.main_vpc.id
}
````



Once you have created your Terraform configuration file, you can use the following commands to launch your VPC:

`terraform init`: This command will initialize Terraform and download the necessary providers and modules.<br>
`terraform plan`: This command will generate a plan of the changes that Terraform will make to your infrastructure.<br>
`terraform apply`: This command will apply the changes that Terraform planned in the previous step.<br>
