# Tale Of Two Regions Part 2

This tutorial will focus soley on our second region. Theo has given us the following requirements....

> **Region 2:** Your choice. 
Terraform with Security Groups 
IGW 
Route Tables 
....and maybe something more

*For the purposes of this project we will assume:*
***Application Load Balancer, Launch Template, Target Group, Autoscaling Group potetnially...***

### What components do we need to complete this task? 
* **VPC (Virtual Private Cloud)**: This would be our logically isolated porton of AWS cloud real estate where you can launch AWS resources in a private network.
* **Subnet**
    * A subnet is a division of a VPC. Subnets are simply ranges of IP addresses.
    * You basically divide your VPC into smaller networks using subnets.
* **Internet gateway (IGW)**
    * An IGW allows traffic from your VPC to flow to and from the internet.
* **NAT**
    * Network Address Translation (NAT) allows EC2 instances in your private subnet to access the internet.
    * NAT works by translating the private IP addresses of your EC2 instances to a public IP address.
* **Route Tables**
    * Route tables determine how traffic is routed within your VPC and to and from the internet.
    * You can create multiple route tables in your VPC and assign them to subnets.
* **Security group for VPC**
    * A security group acts as a firewall of sorts for your EC2 instances. It controls inbound and outbound traffic to your instances.
    * You can create multiple security groups in your VPC and assign them to EC2 instances.
* **Security Group for Application Load Balancer**
    * A security group for an Application Load Balancer (ALB) controls inbound traffic to the ALB.
    * You can create a separate security group for your ALB to control traffic to it.
* **Launch Template**
    * A launch template is a blueprint that defines the configuration for an EC2 instance.
    * Launch templates allow you to create and launch EC2 instances with consistent configurations.
* **Target group**
    * A target group is a collection of EC2 instances that you can use to distribute traffic across.
    * Target groups can be used with ALBs and other load balancers.
* **Load Balancer**
    * A load balancer distributes traffic across multiple EC2 instances.
    * This improves the performance and availability of your applications.
* **Autoscaling Group**
    * An autoscaling group is a group of EC2 instances that automatically scales up or down based on demand.
    * Autoscaling groups can help you maintain performance and availability for your applications.

### Whats steps do we take?
* Create a Terraform configuration file for each region.
* Initialize Terraform.
* Plan the deployment.
* Apply the deployment.
* Verify the deployment.

## Prerequisites
> Just like in Part 1 of our previous tutorials, we need to make sure our prerequisites are met!

* AWS account
* Terraform installed
* Git client installed
* [Forked GitHub Repository](https://github.com/malguswaf/class5)
* VSC Up and running with new project folder ready to go!

### 1. Choose your region and Authenticate!<br>
> Before you proceed, ensure that your account is authenticated!<br>

  1. Create your region 2 directory. (our 2nd region will be "us-east-1")  
  2. Create a new file called `0-Auth.tf` in your project directory.
  3. Copy and paste the code from the `0-Auth.tf` file in the forked "class5" repository.
  4. Make the following adjustments to the code:
        * Specify region = "us-east-1" 
        * Specifcy that this is the same region which your AWS console is situated in)*
        * Add your desired tags.
        * At this point "SAVE" (Ctrl-S) your file!
    
  Here is our **"0-Auth.tf"** code:
```
provider "aws" {
  region = "us-east-1"
}

terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.0"
    }
  }
}
```  
Once you have pasted the code run the following terrrafor terminal commands:
```
terraform init
terraform plan
terraform apply
```
### 2. VPC (Virtual Private Cloud)**
1. Create a new file called `1-VPC.tf` in your project directory.
2. Copy and paste the following code into the file:
```  
resource "aws_vpc" "app1" {
  cidr_block           = "10.32.0.0/16"
  enable_dns_support   = true  # Enable DNS support
  enable_dns_hostnames = true  # Enable DNS hostnames

  tags = {
    Name    = "app1"
    Service = "application1"
    Owner   = "Chewbacca"
    Planet  = "Mustafar"
  }
}
```
Once you have pasted the code run the following terrrafor terminal commands:
```
terraform init
terraform plan
terraform apply
``` 
### 3. Subnet**
1. Create a new file called `1-VPC.tf` in your project directory.
2. Copy and paste the following code into the file:
```  
  # Public Subnets
  resource "aws_subnet" "public_us_east_1a" {
    vpc_id                  = aws_vpc.app1.id
    cidr_block              = "10.32.1.0/24"
    availability_zone       = "us-east-1a"
    map_public_ip_on_launch = true

    tags = {
      Name    = "public-us-east-1a"
      Service = "application1"
      Owner   = "Chewbacca"
      Planet  = "Mustafar"
    }
  }

  resource "aws_subnet" "public_us_east_1b" {
    vpc_id                  = aws_vpc.app1.id
    cidr_block              = "10.32.2.0/24"
    availability_zone       = "us-east-1b"
    map_public_ip_on_launch = true

    tags = {
      Name    = "public-us-east-1b"
      Service = "application1"
      Owner   = "Chewbacca"
      Planet  = "Mustafar"
    }
  }

    resource "aws_subnet" "public_us_east_1c" {
    vpc_id                  = aws_vpc.app1.id
    cidr_block              = "10.32.3.0/24"
    availability_zone       = "us-east-1c"
    map_public_ip_on_launch = true

    tags = {
      Name    = "public-us-east-1c"
      Service = "application1"
      Owner   = "Chewbacca"
      Planet  = "Mustafar"
    }
  }

  # Private Subnets
  resource "aws_subnet" "private_us_east_1a" {
    vpc_id            = aws_vpc.app1.id
    cidr_block        = "10.32.11.0/24"
    availability_zone = "us-east-1a"

    tags = {
      Name    = "private-us-east-1a"
      Service = "application1"
      Owner   = "Chewbacca"
      Planet  = "Mustafar"
    }
  }

  resource "aws_subnet" "private_us_east_1b" {
    vpc_id            = aws_vpc.app1.id
    cidr_block        = "10.32.12.0/24"
    availability_zone = "us-east-1b"

    tags = {
      Name    = "private-us-east-1b"
      Service = "application1"
      Owner   = "Chewbacca"
      Planet  = "Mustafar"
    }
  }

    resource "aws_subnet" "private_us_east_1c" {
    vpc_id            = aws_vpc.app1.id
    cidr_block        = "10.32.13.0/24"
    availability_zone = "us-east-1c"

    tags = {
      Name    = "private-us-east-1c"
      Service = "application1"
      Owner   = "Chewbacca"
      Planet  = "Mustafar"
    }
  }
```
Once you have pasted the code run the following terrrafor terminal commands:
```
terraform init
terraform plan
terraform apply
```  
### 4. Internet gateway (IGW)**
```  
resource "aws_internet_gateway" "app1_igw" {
  vpc_id = aws_vpc.app1.id

  tags = {
    Name = "app1_IGW01"
    Service = "application1"
    Owner = "Chewbacca"
    Planet = "Musafar"
  }
}
```
Once you have pasted the code run the following terrraform terminal commands:
```
terraform init
terraform plan
terraform apply
```  
### 5. NAT**
```
resource "aws_eip" "nat" {
#  description = "nat"
  vpc = true

  tags = {
    Name = "nat"
    Service = "application1"
    Owner = "Chewbacca"
    Planet = "Musafar"
  }
}

resource "aws_nat_gateway" "nat" {
  allocation_id = aws_eip.nat.id
  subnet_id = aws_subnet.public_us_east_1a.id
  tags = {
    Name = "nat"
    Service = "application1"
    Owner = "Chewbacca"
    Planet = "Musafar"
  }

  depends_on = [aws_internet_gateway.app1_igw]
}
```
Once you have pasted the code run the following terrraform terminal commands:
```
terraform init
terraform plan
terraform apply
```  
### 6. Route Tables**
```
resource "aws_route_table" "private" {
  vpc_id = aws_vpc.app1.id

   route = [
    {
      cidr_block                 = "0.0.0.0/0"
      nat_gateway_id             = aws_nat_gateway.nat.id
      carrier_gateway_id         = ""
      destination_prefix_list_id = ""
      egress_only_gateway_id     = ""
      gateway_id                 = ""
      instance_id                = ""
      ipv6_cidr_block            = ""
      local_gateway_id           = ""
      network_interface_id       = ""
      transit_gateway_id         = ""
      vpc_endpoint_id            = ""
      vpc_peering_connection_id  = ""
    },
  ]

  tags = {
    Name = "private"
  }
}

resource "aws_route_table" "public" {
  vpc_id = aws_vpc.app1.id

  route = [
    {
      cidr_block                 = "0.0.0.0/0"
      gateway_id                 = aws_internet_gateway.app1_igw.id
      nat_gateway_id             = ""
      carrier_gateway_id         = ""
      destination_prefix_list_id = ""
      egress_only_gateway_id     = ""
      instance_id                = ""
      ipv6_cidr_block            = ""
      local_gateway_id           = ""
      network_interface_id       = ""
      transit_gateway_id         = ""
      vpc_endpoint_id            = ""
      vpc_peering_connection_id  = ""
    },
  ]

  tags = {
    Name = "public"
  }
}


# Route Table Associations
resource "aws_route_table_association" "private_us_east_1a" {
  subnet_id      = aws_subnet.private_us_east_1a.id
  route_table_id = aws_route_table.private.id
}

resource "aws_route_table_association" "private_us_east_1b" {
  subnet_id      = aws_subnet.private_us_east_1b.id
  route_table_id = aws_route_table.private.id
}

resource "aws_route_table_association" "private_us_east_1c" {
  subnet_id      = aws_subnet.private_us_east_1c.id
  route_table_id = aws_route_table.private.id
}

resource "aws_route_table_association" "public_us_east_1a" {
  subnet_id      = aws_subnet.public_us_east_1a.id
  route_table_id = aws_route_table.public.id
}

resource "aws_route_table_association" "public_us_east_1b" {
  subnet_id      = aws_subnet.public_us_east_1b.id
  route_table_id = aws_route_table.public.id
}

resource "aws_route_table_association" "public_us_east_1c" {
  subnet_id      = aws_subnet.public_us_east_1c.id
  route_table_id = aws_route_table.public.id
}
```
Once you have pasted the code run the following terrraform terminal commands:
```
terraform init
terraform plan
terraform apply
```  
### 7. Security group for VPC**
```
resource "aws_security_group" "app1_sg01_servers" {
  name        = "app1_sg01_server"
  description = "app1_sg01_server"
  vpc_id      = aws_vpc.app1.id

  ingress {
    description = "MyHomePage"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

```
Once you have pasted the code run the following terrraform terminal commands:
```
terraform init
terraform plan
terraform apply
``` 
### 8. Security Group for Application Load Balancer**
```
resource "aws_security_group" "app1_sg02_LB01" {
  name        = "app1_sg02_LB01"
  description = "app1_sg02_LB01"
  vpc_id      = aws_vpc.app1.id

  ingress {
    description = "LBExternal"
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
    Name    = "app1_sg02_LB01"
    Service = "application1"
    Owner   = "Chewbacca"
    Planet  = "Mustafar"
  }
}
```
Once you have pasted the code run the following terrraform terminal commands:
```
terraform init
terraform plan
terraform apply
```  
### 9. Launch Template**
```
resource "aws_launch_template" "app1_LT" {
  name_prefix   = "app1_LT"
  image_id      = "ami-05c13eab67c5d8861"
  instance_type = "t2.micro"

  #key_name = "form"

  vpc_security_group_ids = [aws_security_group.app1_sg01_servers.id]

  user_data = base64encode(<<-EOF
    #!/bin/bash
    yum update -y
    yum install -y httpd
    systemctl start httpd
    systemctl enable httpd

    # Get the IMDSv2 token
    TOKEN=$(curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")

    # Background the curl requests
    curl -H "X-aws-ec2-metadata-token: $TOKEN" -s http://169.254.169.254/latest/meta-data/local-ipv4 &> /tmp/local_ipv4 &
    curl -H "X-aws-ec2-metadata-token: $TOKEN" -s http://169.254.169.254/latest/meta-data/placement/availability-zone &> /tmp/az &
    curl -H "X-aws-ec2-metadata-token: $TOKEN" -s http://169.254.169.254/latest/meta-data/network/interfaces/macs/ &> /tmp/macid &
    wait

    macid=$(cat /tmp/macid)
    local_ipv4=$(cat /tmp/local_ipv4)
    az=$(cat /tmp/az)
    vpc=$(curl -H "X-aws-ec2-metadata-token: $TOKEN" -s http://169.254.169.254/latest/meta-data/network/interfaces/macs/$macid/vpc-id)

    # Create HTML file
    cat <<-HTML > /var/www/html/index.html
    <!doctype html>
    <html lang="en" class="h-100">
    <head>
    <title>Details for EC2 instance</title>
    </head>
    <body>
    <div>
    <h1>AWS Instance Details</h1>
    <h1>Samurai Katana</h1>
    <p><b>Instance Name:</b> $(hostname -f) </p>
    <p><b>Instance Private Ip Address: </b> $local_ipv4</p>
    <p><b>Availability Zone: </b> $az</p>
    <p><b>Virtual Private Cloud (VPC):</b> $vpc</p>
    </div>
    </body>
    </html>
    HTML

    # Clean up the temp files
    rm -f /tmp/local_ipv4 /tmp/az /tmp/macid
  EOF
  )

  tag_specifications {
    resource_type = "instance"
    tags = {
      Name    = "app1_LT"
      Service = "application1"
      Owner   = "Chewbacca"
      Planet  = "Mustafar"
    }
  }

  lifecycle {
    create_before_destroy = true
  }
}

```
Once you have pasted the code run the following terrraform terminal commands:
```
terraform init
terraform plan
terraform apply
```  
### 10.Target group**
```
resource "aws_lb_target_group" "app1_tg" {
  name     = "app1-target-group"
  port     = 80
  protocol = "HTTP"
  vpc_id   = aws_vpc.app1.id
  target_type = "instance"

  health_check {
    enabled             = true
    interval            = 30
    path                = "/"
    protocol            = "HTTP"
    healthy_threshold   = 5
    unhealthy_threshold = 2
    timeout             = 5
    matcher             = "200"
  }

  tags = {
    Name    = "App1TargetGroup"
    Service = "App1"
    Owner   = "User"
    Project = "Web Service"
  }
} 
```
Once you have pasted the code run the following terrraform terminal commands:
```
terraform init
terraform plan
terraform apply
```  
### 11.Load Balancer**
```
resource "aws_lb" "app1_alb" {
  name               = "app1-load-balancer"
  internal           = false
  load_balancer_type = "application"
  security_groups    = [aws_security_group.app1_sg02_LB01.id]
  subnets            = [
    aws_subnet.public_us_east_1a.id,
    aws_subnet.public_us_east_1b.id,
    aws_subnet.public_us_east_1c.id
  ]
  enable_deletion_protection = false

  tags = {
    Name    = "App1LoadBalancer"
    Service = "App1"
    Owner   = "User"
    Project = "Web Service"
  }
}

resource "aws_lb_listener" "http" {
  load_balancer_arn = aws_lb.app1_alb.arn
  port              = 80
  protocol          = "HTTP"

  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.app1_tg.arn
  }
}
```
Once you have pasted the code run the following terrraform terminal commands:
```
terraform init
terraform plan
terraform apply
```  
### 12.Autoscaling Group**
```  
resource "aws_autoscaling_group" "app1_asg" {
  name_prefix           = "app1-auto-scaling-group-"
  min_size              = 3
  max_size              = 15
  desired_capacity      = 6
  vpc_zone_identifier   = [
    aws_subnet.private_us_east_1a.id,
    aws_subnet.private_us_east_1b.id,
    aws_subnet.private_us_east_1c.id
  ]
  health_check_type          = "ELB"
  health_check_grace_period  = 300
  force_delete               = true
  target_group_arns          = [aws_lb_target_group.app1_tg.arn]

  launch_template {
    id      = aws_launch_template.app1_LT.id
    version = "$Latest"
  }

  enabled_metrics = ["GroupMinSize", "GroupMaxSize", "GroupDesiredCapacity", "GroupInServiceInstances", "GroupTotalInstances"]

  # Instance protection for launching
  initial_lifecycle_hook {
    name                  = "instance-protection-launch"
    lifecycle_transition  = "autoscaling:EC2_INSTANCE_LAUNCHING"
    default_result        = "CONTINUE"
    heartbeat_timeout     = 60
    notification_metadata = "{\"key\":\"value\"}"
  }

  # Instance protection for terminating
  initial_lifecycle_hook {
    name                  = "scale-in-protection"
    lifecycle_transition  = "autoscaling:EC2_INSTANCE_TERMINATING"
    default_result        = "CONTINUE"
    heartbeat_timeout     = 300
  }

  tag {
    key                 = "Name"
    value               = "app1-instance"
    propagate_at_launch = true
  }

  tag {
    key                 = "Environment"
    value               = "Production"
    propagate_at_launch = true
  }
}


# Auto Scaling Policy
resource "aws_autoscaling_policy" "app1_scaling_policy" {
  name                   = "app1-cpu-target"
  autoscaling_group_name = aws_autoscaling_group.app1_asg.name

  policy_type = "TargetTrackingScaling"
  estimated_instance_warmup = 120

  target_tracking_configuration {
    predefined_metric_specification {
      predefined_metric_type = "ASGAverageCPUUtilization"
    }
    target_value = 75.0
  }
}

```
Once you have pasted the code run the following terrraform terminal commands:
```
terraform init
terraform plan
terraform apply
```




## Benefits of using modules in AWS Terraform

Terraform modules provide a number of benefits for creating and managing AWS infrastructure, including:

> **1. Reusability**
One of the main benefits of creating individual Terraform modules is that they can be reused in multiple deployments, saving time and effort. This is especially beneficial for organizations that deploy similar infrastructure across multiple environments. For example, a module for a basic VPC with public and private subnets and route tables could be reused in all deployments, simply passing in different parameters for the CIDR block, number of subnets, and resource tags.

> **2. Maintainability**
Individual modules also improve maintainability. Modules encapsulate implementation details, allowing users to focus on configuration. This makes modules easier to understand and modify, especially for large and complex infrastructure deployments. For example, a module for a NAT gateway could encapsulate all logic required to create and configure a NAT gateway, such as creating the instance, attaching an elastic IP address, and configuring routing rules. This allows users to deploy NAT gateways without having to worry about the underlying details.

> **3. Consistency**
Creating individual modules can also help ensure consistency. Modules can be tested and validated before being used in production, which helps ensure that infrastructure is created consistently across deployments. For example, a module for a web application stack could encapsulate all resources required to deploy a web application, such as EC2 instances, a load balancer, and a database. This module could then be used in all deployments to ensure that web applications are deployed consistently.

Ok great, so what about the modules we created? what are they for exactly?

Here's a summary...

### VPC (Virtual Private Cloud)

**Purpose:** To provide a logically isolated network for your AWS resources. This helps to improve security and performance.

**Components:**

* A VPC is a logically isolated section of the AWS cloud where you can launch AWS resources.
* VPCs are divided into subnets, which are ranges of IP addresses.
* VPCs can have multiple subnets, which can be used to create different types of networks, such as public and private subnets.

## Subnet

**Purpose:** To divide your VPC into smaller networks. This helps to improve security and performance.

**Components:**

* A subnet is a range of IP addresses within a VPC.
* Subnets can be public or private. Public subnets have direct access to the internet, while private subnets do not.
* Subnets can be assigned to different availability zones to improve the resilience of your applications.

## Internet gateway (IGW)

**Purpose:** To allow traffic to flow between your VPC and the internet.

**Components:**

* An IGW is a highly available and scalable service that allows traffic to flow between your VPC and the internet.
* IGWs are attached to VPCs and route traffic between the VPC and the internet.

## NAT (Network Address Translation)

**Purpose:** To allow EC2 instances in private subnets to access the internet.

**Components:**

* A NAT gateway allows EC2 instances in private subnets to access the internet by translating their private IP addresses to a public IP address.
* NAT gateways are allocated an EIP (Elastic IP) address, which is used to translate the private IP addresses of EC2 instances to a public IP address.

## Route Tables

**Purpose:** To control how traffic is routed within your VPC and to and from the internet.

**Components:**

* Route tables contain a set of rules that determine how traffic is routed within your VPC and to and from the internet.
* Each subnet in a VPC is associated with a route table.
* Route tables can contain multiple routes, which are evaluated in order until a match is found.

## Security group for VPC

**Purpose:** To control the inbound and outbound traffic to your EC2 instances.

**Components:**

* A security group is a firewall that controls the inbound and outbound traffic to your EC2 instances.
* Security groups can be used to restrict traffic to specific ports and IP addresses.
* You can create multiple security groups and assign them to EC2 instances.

## Security Group for Application Load Balancer

**Purpose:** To control the inbound traffic to your Application Load Balancer (ALB).

**Components:**

* A security group for an ALB controls the inbound traffic to the ALB.
* You can create a separate security group for your ALB to control traffic to it.

## Launch Template

**Purpose:** To define a blueprint for launching EC2 instances.

**Components:**

* A launch template is a blueprint that defines the configuration for an EC2 instance.
* Launch templates can be used to create and launch EC2 instances with consistent configurations.

## Target group

**Purpose:** To distribute traffic across multiple EC2 instances.

**Components:**

* A target group is a collection of EC2 instances that you can use to distribute traffic across.
* Target groups can be used with ALBs and other load balancers.

## Load Balancer

**Purpose:** To distribute traffic across multiple EC2 instances. This improves the performance and availability of your applications.

**Components:**

* A load balancer distributes traffic across multiple EC2 instances.
* Load balancers can be used to improve the performance and availability of your applications.

## Autoscaling Group

**Purpose:** To automatically scale your applications up or down based on demand.

**Components:**

* An autoscaling group is a group of EC2 instances that automatically scales up or down based on demand.
* Autoscaling groups can be used to maintain performance and availability for your applications.
