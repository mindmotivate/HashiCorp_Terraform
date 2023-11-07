
![Oregon-US-WEST-2 drawio](https://github.com/mindmotivate/HashiCorp_Terraform/assets/130941970/b08f3878-3267-4504-90ad-ec16bad6d38d)

# Tale Of Two Regions Part 1

> This project will require us to create a 2-region AWS deployment<br>The first region we will outline is Oregon (us-west-2)

***It will be configured with the following requirements:***
* 4 Availability Zones
* SSL Enabled
* 2 Target Groups

***We will execute this architecture using the following Terraform modules:***

### Modules List:

* VPC (Virtual Private Cloud)
* Public subnet (1 for each of 4 AZs)
* Private subnets (1 for each of 4AZs)
* Internet gateway (IGW)
* NAT gateway
* Route tableS
* Security group (Allowing SSL)
* 2 Target Groups
-----------------------------------------------

### * These "add-ons" are not required *
* Launch template (for potential servers)
* Application load balancer (for potential infrastructure expansion)
* Autoscaling group (for potential web servers)

### Whats steps do we take?
* Create Terraform configuration file in VSC
* Plan the deployment, specifiying us-west-2
* Create the required terraform modules
* Apply the deployment
* Verify the deployment in AWS console
   
## Prerequisites
Ensure that you have the following items first:

* AWS account
* Terraform installed
* Git client installed
* Github Account
* [Forked GitHub Repository](https://github.com/malguswaf/class5)
* VSC Up and running with new project folder ready to go!

# Modules

### 1. Authentication
> Before we do anything else, we should ensure that our account is authenticated!<br>
<br>
    1. Create the first of two directories. (our region will be "us-west-2") <br>
    2. Create a new file called `0-Auth.tf` in your project directory. <br>
    3. Copy and paste the code from the `0-Auth.tf` file in the forked "class5" repository. <br>
    4. Make the following adjustments to the code: Ensure your region is: "us-west-2" <br> 
         *(This should be the same region which your AWS console is situated in)* <br>
    5. Add your desired tags.<br>
    6. At this point "SAVE" (Ctrl-S) your file!

---------------------------------------------

Here is our **"0-Auth.tf"** code:
```
provider "aws" {
  region = "us-west-2"
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
Once you have pasted the code run the following terraform terminal commands:
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

resource "aws_vpc_endpoint" "s3" {
  vpc_id       = aws_vpc.app1.id
  service_name = "com.amazonaws.us-west-2.s3"
  vpc_endpoint_type = "Gateway"

  route_table_ids = [
    aws_route_table.private.id,
    aws_route_table.public.id
  ]

  tags = {
    Name    = "app1_s3_endpoint"
    Service = "application1"
    Owner   = "Chewbacca"
    Planet  = "Mustafar"
  }
}

resource "aws_vpc_endpoint" "s3" {
  vpc_id       = aws_vpc.app1.id
  service_name = "com.amazonaws.us-west-1.s3"
  vpc_endpoint_type = "Gateway"

  route_table_ids = [
    aws_route_table.private.id,
    aws_route_table.public.id
  ]

  tags = {
    Name    = "app1_s3_endpoint"
    Service = "application1"
    Owner   = "Chewbacca"
    Planet  = "Mustafar"
  }
}

```
Once you have properly configured the code, run the following terraform terminal commands:
```
terraform init
terraform plan
terraform apply
``` 
### 3. Subnet**

  # Public Subnets
```
  resource "aws_subnet" "public_us_west_1a" {
    vpc_id                  = aws_vpc.app1.id
    cidr_block              = "10.32.1.0/24"
    availability_zone       = "us-west-1a"
    map_public_ip_on_launch = true

    tags = {
      Name    = "public-us-west-1a"
      Service = "application1"
      Owner   = "Chewbacca"
      Planet  = "Mustafar"
    }
  }

  resource "aws_subnet" "public_us_west_1b" {
    vpc_id                  = aws_vpc.app1.id
    cidr_block              = "10.32.2.0/24"
    availability_zone       = "us-west-1b"
    map_public_ip_on_launch = true

    tags = {
      Name    = "public-us-west-1b"
      Service = "application1"
      Owner   = "Chewbacca"
      Planet  = "Mustafar"
    }
  }

    resource "aws_subnet" "public_us_west_1c" {
    vpc_id                  = aws_vpc.app1.id
    cidr_block              = "10.32.3.0/24"
    availability_zone       = "us-west-1c"
    map_public_ip_on_launch = true

    tags = {
      Name    = "public-us-west-1c"
      Service = "application1"
      Owner   = "Chewbacca"
      Planet  = "Mustafar"
    }
  }

    resource "aws_subnet" "public_us_west_1d" {
    vpc_id                  = aws_vpc.app1.id
    cidr_block              = "10.32.4.0/24"
    availability_zone       = "us-west-1d"
    map_public_ip_on_launch = true

    tags = {
      Name    = "public-us-west-1d"
      Service = "application1"
      Owner   = "Chewbacca"
      Planet  = "Mustafar"
    }
  }

  # Private Subnets
  resource "aws_subnet" "private_us_west_1a" {
    vpc_id            = aws_vpc.app1.id
    cidr_block        = "10.32.11.0/24"
    availability_zone = "us-west-1a"

    tags = {
      Name    = "private-us-west-1a"
      Service = "application1"
      Owner   = "Chewbacca"
      Planet  = "Mustafar"
    }
  }

  resource "aws_subnet" "private_us_west_1b" {
    vpc_id            = aws_vpc.app1.id
    cidr_block        = "10.32.12.0/24"
    availability_zone = "us-west-1b"

    tags = {
      Name    = "private-us-west-1b"
      Service = "application1"
      Owner   = "Chewbacca"
      Planet  = "Mustafar"
    }
  }

    resource "aws_subnet" "private_us_west_1c" {
    vpc_id            = aws_vpc.app1.id
    cidr_block        = "10.32.13.0/24"
    availability_zone = "us-west-1c"

    tags = {
      Name    = "private-us-west-1c"
      Service = "application1"
      Owner   = "Chewbacca"
      Planet  = "Mustafar"
    }
  }

      resource "aws_subnet" "private_us_west_1d" {
    vpc_id            = aws_vpc.app1.id
    cidr_block        = "10.32.14.0/24"
    availability_zone = "us-west-1d"

    tags = {
      Name    = "private-us-west-1d"
      Service = "application1"
      Owner   = "Chewbacca"
      Planet  = "Mustafar"
    }
  }
```
Once you have properly configured the code, run the following terraform terminal commands:
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
Once you have properly configured the code, run the following terraform terminal commands:
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
  subnet_id = aws_subnet.public_us_west_1a.id
  tags = {
    Name = "nat"
    Service = "application1"
    Owner = "Chewbacca"
    Planet = "Musafar"
  }
}

```
Once you have properly configured the code, run the following terraform terminal commands:
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
Once you have properly configured the code, run the following terraform terminal commands:
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

   ingress {
    description = "HTTPS"
    from_port   = 443
    to_port     = 443
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
    Name    = "app1_sg01_server"
    Service = "application1"
    Owner   = "Chewbacca"
    Planet  = "Mustafar"
  }
}

```
Once you have pasted the code run the following terraform terminal commands:
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
Once you have pasted the code run the following terraform terminal commands:
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
Once you have pasted the code run the following terraform terminal commands:
```
terraform init
terraform plan
terraform apply
```  
### 10. Target group**
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

resource "aws_lb_target_group" "app1_tg_https" {
  name     = "app1-target-group-https"
  port     = 443
  protocol = "HTTPS"
  vpc_id   = aws_vpc.app1.id
  target_type = "instance"

  health_check {
    enabled             = true
    interval            = 30
    path                = "/"
    protocol            = "HTTPS"
    healthy_threshold   = 5
    unhealthy_threshold = 2
    timeout             = 5
    matcher             = "200"
  }

  tags = {
    Name    = "App1TargetGroupHTTPS"
    Service = "App1"
    Owner   = "User"
    Project = "Web Service"
  }
}

```
Once you have pasted the code run the following terraform terminal commands:
```
terraform init
terraform plan
terraform apply
```
### 11. Load Balancer/**
```
resource "aws_lb" "app1_alb" {
  name               = "app1-load-balancer"
  internal           = false
  load_balancer_type = "application"
  security_groups    = [aws_security_group.app1_sg02_LB01.id]
  subnets            = [
    aws_subnet.public_us_west_1a.id,
    aws_subnet.public_us_west_1b.id,
    aws_subnet.public_us_west_1c.id,
    aws_subnet.public_us_west_1d.id
  ]
  enable_deletion_protection = false

  tags = {
    Name    = "App1LoadBalancer"
    Service = "App1"
    Owner   = "User"
    Project = "Web Service"
  }
}
```
### 12. Autoscaling Group**
```  
resource "aws_autoscaling_group" "app1_asg" {
  name_prefix           = "app1-auto-scaling-group-"
  min_size              = 3
  max_size              = 15
  desired_capacity      = 6
  vpc_zone_identifier   = [
    aws_subnet.private_us_west_1a.id,
    aws_subnet.private_us_west_1b.id,
    aws_subnet.private_us_west_1c.id
    aws_subnet.private_us_west_1d.id
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
Once you have pasted the code run the following terraform terminal commands:
```
terraform init
terraform plan
terraform apply
```

## Successful Deployment Images:

<img src= "https://raw.githubusercontent.com/mindmotivate/HashiCorp_Terraform/gh-pages-deployment/oregon_1.JPG">
<img src= "https://raw.githubusercontent.com/mindmotivate/HashiCorp_Terraform/gh-pages-deployment/oregon_2.JPG">
<img src= "https://raw.githubusercontent.com/mindmotivate/HashiCorp_Terraform/gh-pages-deployment/oregon_3.JPG">
<img src= "https://raw.githubusercontent.com/mindmotivate/HashiCorp_Terraform/gh-pages-deployment/oregon_4.JPG">
<img src= "https://raw.githubusercontent.com/mindmotivate/HashiCorp_Terraform/gh-pages-deployment/oregon_5.JPG">
<img src= "https://raw.githubusercontent.com/mindmotivate/HashiCorp_Terraform/gh-pages-deployment/oregon_6.JPG">
<img src= "https://raw.githubusercontent.com/mindmotivate/HashiCorp_Terraform/gh-pages-deployment/oregon_7.JPG">
<img src= "https://raw.githubusercontent.com/mindmotivate/HashiCorp_Terraform/gh-pages-deployment/oregon_8.JPG">
<img src= "https://raw.githubusercontent.com/mindmotivate/HashiCorp_Terraform/gh-pages-deployment/oregon_9.JPG">
