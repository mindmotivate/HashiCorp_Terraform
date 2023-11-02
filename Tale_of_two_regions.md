# Tale Of Two Regions

Your Mission...(should you choose to except it!)

This project will require us to create a 2-region AWS deployment with 4 availability zones in which we will include: 
2 target groups, security groups, IGW, and route tables at minimum. 

We will execute this architecture using Terraform!

> Ok, first let's outline the steps and figure out what is to be included:

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


































  Method 1


  # providers.tf
provider "aws" {
  alias = "oregon"
  region = "us-west-2"
}

provider "aws" {
  alias = "virginia"
  region = "us-east-1"
}

# main.tf
resource "aws_security_group" "web" {
  name = "terraform-demo-web-sg"
  description = "Security group for web servers"
  ingress = [
    {
      from_port = 443
      to_port = 443
      protocol = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
    }
  ]
  egress = [
    {
      from_port = 0
      to_port = 0
      protocol = "-1"
      cidr_blocks = ["0.0.0.0/0"]
    }
  ]
}

resource "aws_acm_certificate" "web" {
  domain_name = "example.com"
  validation_method = "dns"
}

resource "aws_lb_listener" "web" {
  load_balancer_arn = aws_lb.alb.arn
  port = 443
  protocol = "HTTPS"
  ssl_policy = "ELBSecurityPolicy-TLS-1-2-Ext-2018-06"
  certificate_arn = aws_acm_certificate.web.arn

  default_action {
    type = "forward"
    target_group_arn = aws_lb_target_group.web1_oregon.arn
  }
}

resource "aws_lb_target_group" "web1_oregon" {
  name = "terraform-demo-web-tg1-oregon"
  port = 80
  protocol = "HTTP"
  vpc_id = var.vpc_id
  region = aws.oregon.region
}

resource "aws_lb_target_group" "web2_oregon" {
  name = "terraform-demo-web-tg2-oregon"
  port = 80
  protocol = "HTTP"
  vpc_id = var.vpc_id
  region = aws.oregon.region
}

resource "aws_lb_target_group" "web1_virginia" {
  name = "terraform-demo-web-tg1-virginia"
  port = 80
  protocol = "HTTP"
  vpc_id = var.vpc_id
  region = aws.virginia.region
}

resource "aws_lb_target_group" "web2_virginia" {
  name = "terraform-demo-web-tg2-virginia"
  port = 80
  protocol = "HTTP"
  vpc_id = var.vpc_id
  region = aws.virginia.region
}

resource "aws_internet_gateway" "oregon" {
  tags = {
    Name = "terraform-demo-oregon-igw"
  }
  region = aws.oregon.region
}

resource "aws_internet_gateway" "virginia" {
  tags = {
    Name = "terraform-demo-virginia-igw"
  }
  region = aws.virginia.region
}

resource "aws_route_table" "oregon_main" {
  vpc_id = var.vpc_id
  tags = {
    Name = "terraform-demo-oregon-main-route-table"
  }
  region = aws.oregon.region
}

resource "aws_route_table" "virginia_main" {
  vpc_id = var.vpc_id
  tags = {
    Name = "terraform-demo-virginia-main-route-table"
  }
  region = aws.virginia.region
}

resource "aws_route" "oregon_default" {
  route_table






















method 2

variable "oregon_region" {
  type = string
  default = "us-west-2"
}

variable "virginia_region" {
  type = string
  default = "us-east-1"
}

provider "aws" {
  region = var.oregon_region
}

data "aws_availability_zones" "available" {}

resource "aws_internet_gateway" "oregon" {
  tags = {
    Name = "terraform-demo-oregon-igw"
  }
}

resource "aws_internet_gateway" "virginia" {
  tags = {
    Name = "terraform-demo-virginia-igw"
  }
  region = var.virginia_region
}

resource "aws_route_table" "oregon_main" {
  vpc_id = var.vpc_id
  tags = {
    Name = "terraform-demo-oregon-main-route-table"
  }
}

resource "aws_route_table" "virginia_main" {
  vpc_id = var.vpc_id
  region = var.virginia_region
  tags = {
    Name = "terraform-demo-virginia-main-route-table"
  }
}

resource "aws_route" "oregon_default" {
  route_table_id = aws_route_table.oregon_main.id
  destination_cidr_block = "0.0.0.0/0"
  gateway_id = aws_internet_gateway.oregon.id
}

resource "aws_route" "virginia_default" {
  route_table_id = aws_route_table.virginia_main.id
  destination_cidr_block = "0.0.0.0/0"
  gateway_id = aws_internet_gateway.virginia.id
  region = var.virginia_region
}

resource "aws_security_group" "web" {
  name = "terraform-demo-web-sg"
  description = "Security group for web servers"
  ingress = [
    {
      from_port = 443
      to_port = 443
      protocol = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
    }
  ]
  egress = [
    {
      from_port = 0
      to_port = 0
      protocol = "-1"
      cidr_blocks = ["0.0.0.0/0"]
    }
  ]
}

resource "aws_acm_certificate" "web" {
  domain_name = "example.com"
  validation_method = "dns"
}

resource "aws_lb_listener" "web" {
  load_balancer_arn = aws_lb.alb.arn
  port = 443
  protocol = "HTTPS"
  ssl_policy = "ELBSecurityPolicy-TLS-1-2-Ext-2018-06"
  certificate_arn = aws_acm_certificate.web.arn

  default_action {
    type = "forward"
    target_group_arn = aws_lb_target_group.web1_oregon.arn
  }
}

resource "aws_lb_target_group" "web1_oregon" {
  name = "terraform-demo-web-tg1-oregon"
  port = 80
  protocol = "HTTP"
  vpc_id = var.vpc_id
  region = var.oregon_region
}

resource "aws_lb_target_group" "web2_oregon" {
  name = "terraform-demo-web-tg2-oregon"
  port = 80
  protocol = "HTTP"
  vpc_id = var.vpc_id
  region = var.oregon_region
}

resource "aws_lb_target_group" "web1_virginia" {
  name = "terraform-demo-web-tg1-virginia"
  port = 80
  protocol = "HTTP"
  vpc_id = var.vpc_id
  region = var.virginia_region
