
![us-east-1 drawio](https://github.com/mindmotivate/HashiCorp_Terraform/assets/130941970/de62ab35-5d3e-4a38-9f12-d5fae110e721)

# Tale Of Two Regions Part 2

This tutorial will focus soley on our second region. Theo has given us the following requirements....

> **Region 2:** Your choice. 
Terraform with Security Groups 
IGW 
Route Tables 
....and maybe something more

*For the purposes of this project we will consider:*
***Application Load Balancer, Launch Template, Target Group, Autoscaling Group, Endpoint for a future service like an S3 Bucket perhaps...***<br>
<br>
Keep in mind we were not given specific instructions so feel free to add whatever features you would like!

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
* Determine our desired region
* Create a corresponding Terraform configuration file in VSC
* Plan the deployment
* Create the required terraform modules
* Apply the deployment
* Verify the deployment

## Prerequisites
> Just like in Part 1 of our previous tutorial, we need to make sure our prerequisites are met! <br>
Ensure that you have the following:
* AWS account
* Terraform installed
* Git client installed
* Github Account
* [Forked GitHub Repository](https://github.com/malguswaf/class5)
* VSC Up and running with new project folder ready to go!

### 1. Choose your region and Authenticate!<br>
> Before you proceed, ensure that your account is authenticated!<br>
  1. Create your region 2 directory. (our 2nd region will be "us-east-1") <br>
  2. Create a new file called `0-Auth.tf` in your project directory. <br>
  3. Copy and paste the code from the `0-Auth.tf` file in the forked "class5" repository.<br>
  4. Make the following adjustments to the code:<br>
        * Specify region = "us-east-1" <br>
        * Specifcy that this is the same region which your AWS console is situated in!*<br>
        * Add your desired tags.<br>
        * At this point "SAVE" (Ctrl-S) your file!<br>
    
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
Once you have pasted and saved your code, run the following terraform terminal commands:
```
terraform init
terraform plan
terraform apply
```
### 2. VPC (Virtual Private Cloud)**
1. Create a new file called `1-VPC.tf` in your project directory.
2. Copy and paste the VPC code from the forked "class5" repository.<br>
3. Since we will be selecting N. Virginia as our region, our CIDR will reflect such.
4. Ensure your CIDR range is: 10.36.0.0/16 (you may select another cidr range jsut make sure it does not overlap with your first region)
5. *Note: We chose to go with an S3 endpoint as our extra item (please ensure th at it is associated with the vpc as well)
6. Add your desired tags.<br>
7. "SAVE" (Ctrl-S) your file!<br>
```  
resource "aws_vpc" "app1" {
  cidr_block           = "10.36.0.0/16"
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
  service_name = "com.amazonaws.us-east-1.s3"
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
Once you have pasted and saved your code, run the following terraform terminal commands:
```
terraform init
terraform plan
terraform apply
``` 
### 3. Subnet**
1. Create a new file called `2-Subnets.tf` in your project directory.<br>
2. Copy and paste the Subnet code from the forked "class5" repository.<br>
3. *Note: We will be creating both our Public & Private subnets in this module, so ensure you have both!
4. Add your desired tags.<br>
5. "SAVE" (Ctrl-S) your file!<br>
```  
  # Public Subnets
  resource "aws_subnet" "public_us_east_1a" {
    vpc_id                  = aws_vpc.app1.id
    cidr_block              = "10.36.1.0/24"
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
    cidr_block              = "10.36.2.0/24"
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
    cidr_block              = "10.36.3.0/24"
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
    cidr_block        = "10.36.11.0/24"
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
    cidr_block        = "10.36.12.0/24"
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
    cidr_block        = "10.36.13.0/24"
    availability_zone = "us-east-1c"

    tags = {
      Name    = "private-us-east-1c"
      Service = "application1"
      Owner   = "Chewbacca"
      Planet  = "Mustafar"
    }
  }
```
Once you have pasted and saved your code, run the following terraform terminal commands:
```
terraform init
terraform plan
terraform apply
```  
### 4. Internet gateway (IGW)**
1. Create a new file called `3-IGW.tf` in your project directory.<br>
2. Copy and paste the IGW code from the forked "class5" repository.<br>
3. *Note: This will allow for our VPC to communicate with the Internet so ensure that it is assciated with the VPC id
4. Add your desired tags.<br>
5. "SAVE" (Ctrl-S) your file!<br>

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
Once you have pasted and saved your code, run the following terraform terminal commands:
```
terraform init
terraform plan
terraform apply
```  
### 5. NAT**
1. Create a new file called `4-NAT.tf` in your project directory.<br>
2. Copy and paste the NAT code from the forked "class5" repository.<br>
3. *Note: This will allow our Private subnet to communicate with the Internet by translating to a Public IP so ensure that it is assciated with the "Public" subnet id
4. Add your desired tags.<br>
5. "SAVE" (Ctrl-S) your file!<br>
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
Once you have pasted and saved your code, run the following terraform terminal commands:
```
terraform init
terraform plan
terraform apply
```  
### 6. Route Tables**
1. Create a new file called `5-Routed.tf` in your project directory.<br>
2. Copy and paste the Route code from the forked "class5" repository.<br>
3. *Note: The route table provides intructions regarding how traffic is routed.<br>
4. Add your desired tags.<br>
5. "SAVE" (Ctrl-S) your file!<br>
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
Once you have pasted and saved your code, run the following terraform terminal commands:
```
terraform init
terraform plan
terraform apply
```  
### 7. Security group for VPC**
1. Create a new file called `6-SG.tf` in your project directory.<br>
2. We will have to do a bit of digging to find resource. You may use the following reference: (https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/security_group)
3. Alter the provided registry code to consider traffic from HTTP, SSH & RDP sources
4. *Note: The security group is specifically fro the LB and will only HTTP ingress traffic<br>
5. Add your desired tags.<br>
6. "SAVE" (Ctrl-S) your file!<br>
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
    description = "SSH"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

   ingress {
    description = "RDP"
    from_port   = 3389
    to_port     = 3389
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
Once you have pasted and saved your code, run the following terraform terminal commands:
```
terraform init
terraform plan
terraform apply
``` 
### 8. Security Group for Application Load Balancer**
1. Create a new file called `7-LBSG.tf` in your project directory.<br>
2. You may use the following documentation: (https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/security_group),
(https://registry.terraform.io/modules/terraform-aws-modules/alb/aws/latest)<br> 
5. *Note: Alter the code as necessary and provide the appropriate ingress rule via port 80. The security group is specifically fro the LB and will only consider HTTP ingress traffic<br>
6. Add your desired tags.<br>
7. "SAVE" (Ctrl-S) your file!<br>>
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
Once you have pasted and saved your code, run the following terraform terminal commands:
```
terraform init
terraform plan
terraform apply
```  
### 9. Launch Template**
1. Create a new file called `8-LT.tf` in your project directory.<br>
2. You may use the following documentation: (https://registry.terraform.io/modules/bootlabstech/fully-loaded-eks-cluster/aws/latest/submodules/launch-templates)
3. *Note: Alter the code as necessary and provide the appropriate AMI Image and instance type and security group
4. Add your desired tags.<br>
5. "SAVE" (Ctrl-S) your file!<br>>
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
Once you have pasted and saved your code, run the following terraform terminal commands:
```
terraform init
terraform plan
terraform apply
```  
### 10.Target group**
1. Create a new file called `9-TG.tf` in your project directory.<br>
2. You may use the following documentation: (https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/lb_target_group)
3. *Note: Alter the code as necessary and provide the appropriate port and esure your target group is associated with your VPC
4. Add your desired tags.<br>
5. "SAVE" (Ctrl-S) your file!<br>>
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
Once you have pasted and saved your code, run the following terraform terminal commands:
```
terraform init
terraform plan
terraform apply
```  
### 11.Load Balancer**
1. Create a new file called `10-ALB.tf` in your project directory.<br>
2. You may use the following documentation: (https://registry.terraform.io/modules/terraform-aws-modules/alb/aws/latest)
3. *Note: load balancers must always be associated with public subnets. This is because load balancers need to have a public IP address in order to receive inbound traffic from the internet. Ensure that you have specified all of your public subnets accordingly!
4. Add your desired tags.<br>
5. "SAVE" (Ctrl-S) your file!<br>>
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
Once you have pasted and saved your code, run the following terraform terminal commands:
```
terraform init
terraform plan
terraform apply
```  
### 12.Autoscaling Group**
1. Create a new file called `11-ASG.tf` in your project directory.<br>
2. You may use the following documentation: (https://registry.terraform.io/modules/terraform-aws-modules/alb/aws/latest)
3. *Note: ASGs are often used to launch you templates in private environment. Ensure that you have specified all of your private subnets accordingly!
4. Add your desired tags.<br>
5. Similar to the AWS GUI, you will have the ability to select capacity allocations associated with you ASG by using the following module: (https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/autoscaling_policy)<br>
6. These optional items are not found in the registry, however if youd like to add them , you can find them in you AWS CLI here:
```
aws autoscaling describe-lifecycle-hooks
aws--auto-scaling-group-name my-asg
```
6. "SAVE" (Ctrl-S) your file!<br>>
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
Once you have pasted and saved your code, run the following terraform terminal commands:
```
terraform init
terraform plan
terraform apply
```

## Succesful Deployment:
<img src= "https://raw.githubusercontent.com/mindmotivate/HashiCorp_Terraform/gh-pages-deployment/virgnia_1.JPG">
<img src= "https://raw.githubusercontent.com/mindmotivate/HashiCorp_Terraform/gh-pages-deployment/virgnia_2.JPG">
<img src= "https://raw.githubusercontent.com/mindmotivate/HashiCorp_Terraform/gh-pages-deployment/virgnia_3.JPG">
<img src= "https://raw.githubusercontent.com/mindmotivate/HashiCorp_Terraform/gh-pages-deployment/virgnia_4.JPG">
<img src= "https://raw.githubusercontent.com/mindmotivate/HashiCorp_Terraform/gh-pages-deployment/virgnia_5.JPG">
<img src= "https://raw.githubusercontent.com/mindmotivate/HashiCorp_Terraform/gh-pages-deployment/virgnia_6.JPG">
<img src= "https://raw.githubusercontent.com/mindmotivate/HashiCorp_Terraform/gh-pages-deployment/virgnia_7.JPG">
<img src= "https://raw.githubusercontent.com/mindmotivate/HashiCorp_Terraform/gh-pages-deployment/virgnia_8.JPG">
<img src= "https://raw.githubusercontent.com/mindmotivate/HashiCorp_Terraform/gh-pages-deployment/virgnia_9.JPG">
<img src= "https://raw.githubusercontent.com/mindmotivate/HashiCorp_Terraform/gh-pages-deployment/virgnia_10.JPG">
<img src= "https://raw.githubusercontent.com/mindmotivate/HashiCorp_Terraform/gh-pages-deployment/virgnia_11.JPG">
<img src= "https://raw.githubusercontent.com/mindmotivate/HashiCorp_Terraform/gh-pages-deployment/virgnia_12.JPG">
