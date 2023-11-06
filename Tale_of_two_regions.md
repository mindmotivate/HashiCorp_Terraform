# Tale Of Two Regions

Your Mission...(should you choose to except it!)

This project will require us to create a 2-region AWS deployment 
The first region we w ill outline is Oregon
It will be configured with:
4 availability zones
SSL
2 Target Groups

We will execute this architecture using Terraform!

> Ok, first let's outline the steps and figure out what is to be included:


Components:

VPC (Virtual Private Cloud)
4 Public subnets (1 for each AZ)
4 Private subnets (1 for each AZ)
1 Internet gateway (IGW)
1 NAT gateway
2 Public route tables (1 for each AZ)
2 Private route tables (1 for each AZ)
2 Security groups (1 for the public web servers and 1 for the private web servers)
1 Launch template for the web servers
2 Target groups (1 for the public web servers and 1 for the private web servers)
1 Application load balancer (ALB)
1 Autoscaling group for the web servers
1 SSL certificate

Steps:

1. Create a Terraform configuration file for the Region 1: Oregon deployment.
2. Initialize Terraform.
3. Plan the deployment.
4. Apply the deployment.
5. Configure the SSL certificate for the ALB.
6. Configure the ALB to listen on port 443 and forward traffic to the two target groups.
7. Configure the web servers to use the SSL certificate.
8. Configure the autoscaling group to launch and maintain the web servers.
9. Verify the deployment.







### What do we need? 
* VPC: This would be our logically isolated porton of AWS cloud real estate where you can launch AWS resources in a private network.
* Subnet: A subnet is a division of a VPC. Subnets is simply a range of IP addresses.  Your basically dividing your VPC into smaller networks
* EC2 instance: An EC2 instance is a virtual machine that you can run in the AWS cloud. (the most commonly used service in AWS!)
* Target group: A target group is a collection of EC2 instances that you can use to distribute traffic across.
* Security group: A security group acts as a firewall of sorts for your EC2 instances. It controls inbound and outbound traffic to your instances.
* Internet gateway (IGW): An IGW allows traffic from your VPC to flow to and from the internet.
* Route table: A route table determines how traffic is routed within your VPC and to and from the internet.


### Whats steps do we take?
* Create a Terraform configuration file for each region.
* Initialize Terraform.
* Plan the deployment.
* Apply the deployment.
* Verify the deployment.

## Prerequisites
> Just like in our previous tutorials, we need to make sure our prerequisites are met!

* AWS account
* Terraform installed
* Git client installed
* [Forked GitHub Repository](https://github.com/malguswaf/class5)
* VSC Up and running with new project folder ready to go!

### Authentication
> Betore we do anything else, we should ensure that our account is authenticated!

    1. Create the first of two directories. The first will be called " 
    2. Create a new file called `0-Auth.tf` in your project directory.
    3. Copy and paste the code from the `0-Auth.tf` file in the forked "class5" repository.
    4. Make the following adjustments to the code:
        * Replace region = "eu-west-1" with your desired region
        * *(This should be the same region which your AWS console is situated in)*
        * Add your desired tags.
        * At this point "SAVE" (Ctrl-S) your file!
































```

module "vpc" {
  source = "hashicorp/aws/vpc"
  version = "~> 3.4"

  name = "my-vpc"
  cidr = "10.0.0.0/16"

  azs = ["us-west-1a", "us-west-1b", "us-west-1c", "us-west-1d"]

  tags = {
    Name = "my-vpc"
    Environment = "production"
  }
}

module "public_subnets" {
  source = "hashicorp/aws/subnet"
  version = "~> 3.4"

  for_each = var.public_subnets

  vpc_id = module.vpc.vpc_id
  cidr_block = each.value
  availability_zone = each.key
  map_public_ip_on_launch = true

  tags = {
    Name = each.key
    Environment = "production"
  }
}

module "private_subnets" {
  source = "hashicorp/aws/subnet"
  version = "~> 3.4"

  for_each = var.private_subnets

  vpc_id = module.vpc.vpc_id
  cidr_block = each.value
  availability_zone = each.key

  tags = {
    Name = each.key
    Environment = "production"
  }
}

module "internet_gateway" {
  source = "hashicorp/aws/internet_gateway"
  version = "~> 3.4"

  vpc_id = module.vpc.vpc_id

  tags = {
    Name = "my-igw"
    Environment = "production"
  }
}

module "nat_gateway" {
  source = "hashicorp/aws/nat_gateway"
  version = "~> 3.4"

  allocation_id = var.nat_gateway_allocation_id
  subnet_id = module.public_subnets[0].id

  tags = {
    Name = "my-nat"
    Environment = "production"
  }

  depends_on = [
    module.internet_gateway,
  ]
}

module "route_tables" {
  source = "hashicorp/aws/route_table"
  version = "~> 3.4"

  vpc_id = module.vpc.vpc_id

  public_route_table = {
    name = "my-public-route-table"
    route = [
      {
        cidr_block = "0.0.0.0/0"
        gateway_id = module.internet_gateway.id
      }
    ]
  }

  private_route_table = {
    name = "my-private-route-table"
    route = [
      {
        cidr_block = "0.0.0.0/0"
        nat_gateway_id = module.nat_gateway.id
      }
    ]
  }
}

module "route_table_associations" {
  source = "hashicorp/aws/route_table_association"
  version = "~> 3.4"

  for_each = var.route_table_associations

  subnet_id = each.key
  route_table_id = each.value
}

module "security_groups" {
  source = "hashicorp/aws/security_group"
  version = "~> 3.4"

  web_server_sg = {
    name = "my-web-server-sg"
    description = "Security group for web servers"
    vpc_id = module.vpc.vpc_id

    ingress = [
      {
        description = "HTTP"
        from_port = 80
        to_port = 80
        protocol = "tcp"
        cidr_blocks = ["0.0.0.0/0"]
      },
      {
        description = "HTTPS"
        from_port = 443
        to_port = 443
        protocol = "tcp"
        cidr_blocks = ["0.0.0.0/0"]
      }
    ]

    egress = [
      {

      ```


````
### Components
```
provider "aws" {
  region = "us-west-1"
}

resource "aws_vpc" "my_vpc" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "my-vpc"
    Environment = "production"
  }
}
```
### Create public subnets
```
resource "aws_subnet" "public_subnet_1a" {
  vpc_id = aws_vpc.my_vpc.id
  cidr_block = "10.0.1.0/24"
  availability_zone = "us-west-1a"
  map_public_ip_on_launch = true

  tags = {
    Name = "public-subnet-1a"
    Environment = "production"
  }
}
```
### ... Create public subnets for 1b, 1c, and 1d

### Create private subnets
```
resource "aws_subnet" "private_subnet_1a" {
  vpc_id = aws_vpc.my_vpc.id
  cidr_block = "10.0.10.0/24"
  availability_zone = "us-west-1a"

  tags = {
    Name = "private-subnet-1a"
    Environment = "production"
  }
}
```
### ... Create private subnets for 1b, 1c, and 1d

### Create an internet gateway
```
resource "aws_internet_gateway" "my_igw" {
  vpc_id = aws_vpc.my_vpc.id

  tags = {
    Name = "my-igw"
    Environment = "production"
  }
}
```
### Create a NAT gateway
```
resource "aws_nat_gateway" "my_nat_gateway" {
  allocation_id = aws_eip.my_nat_eip.id
  subnet_id = aws_subnet.public_subnet_1a.id

  tags = {
    Name = "my-nat_gateway"
    Environment = "production"
  }
}
```
### Create an EIP for the NAT gateway
```
resource "aws_eip" "my_nat_eip" {
  vpc = true
}
```
### Create public route tables
```
resource "aws_route_table" "public_route_table_1a" {
  vpc_id = aws_vpc.my_vpc.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.my_igw.id
  }

  tags = {
    Name = "public-route-table-1a"
    Environment = "production"
  }
}
```
#### ... Create public route tables for 1b, 1c, and 1d

### Create private route tables
```
resource "aws_route_table" "private_route_table_1a" {
  vpc_id = aws_vpc.my_vpc.id

  route {
    cidr_block = "0.0.0.0/0"
    nat_gateway_id = aws_nat_gateway.my_nat_gateway.id
  }

  tags = {
    Name = "private-route-table-1a"
    Environment = "production"
  }
}
```
### ... Create private route tables for 1b, 1c, and 1d

### Create a security group for the web servers in the public subnet
```
resource "aws_security_group" "public_web_servers_sg" {
  name = "public-web-servers-sg"
  description = "Security group for web servers in the public subnet"
  ingress = [{
    from_port = 443
    to_port = 443
    protocol = "tcp"
    cidr_blocks = ["10.0.1.0/24"]
  }]
}
```
### Create a security group for the web servers in the private subnet
```
resource "aws_security_group" "private_web_servers_sg" {
  name = "private_web_servers-sg"
  description = "Security group for web servers in the private
````


