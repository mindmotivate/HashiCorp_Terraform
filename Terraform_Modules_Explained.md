
![image](https://github.com/mindmotivate/HashiCorp_Terraform/assets/130941970/3e81ac10-669f-4c5c-850e-00d3ee103b48)
![OIG ANG_F0tCuUWhc](https://github.com/mindmotivate/HashiCorp_Terraform/assets/130941970/e7a6e1df-35e1-4754-97d4-dbb3f8fc73a5)
![OIG](https://github.com/mindmotivate/HashiCorp_Terraform/assets/130941970/d6f98125-6bb4-4d7c-8907-2936af75045f)

## Terraform Modules

If you are completely new to the concept of Terraform Modules, think of them like a Pre-fab house that is built off-site and then transported to the desired location for assembly. This process makes them much faster and easier to build than a traditional home structure. Similarly, Terraform modules are pre-built infrastructure components that can be easily reused across different environments and projects.

In other words, Terraform modules are like the individual building blocks of a pre-fab house, such as the walls, roof, and floors.
Terraform providers are like the tools and materials needed to assemble the pre-fab house, such as the foundation, electrical wiring, and plumbing.
Terraform configurations are like the blueprints for the pre-fab house, which specify how the individual building blocks should be assembled.
To build a pre-fab house, you would first need to choose a design and order the necessary building blocks from the manufacturer. Once the building blocks have been delivered, you would then need to assemble them according to the blueprints.

To build cloud infrastructure with Terraform, you would first need to choose the necessary Terraform modules for the components you need, such as EC2 instances, EBS volumes, and security groups. Once you have chosen the modules, you would then need to configure them and assemble them into a complete infrastructure configuration.

Terraform providers provide the necessary tools and materials to interact with the cloud provider of your choice. For example, the AWS provider provides Terraform with the ability to create and manage AWS resources.

Terraform configurations are written in their own proprietary HashiCorp Configuration Language "HCL", (which may remind of you of JSON a bit) which is a declarative language that allows you to specify what you want to achieve, rather than how to achieve it. This makes Terraform configurations easy to read and maintain.

Imagine you want to create a new web server on AWS. You could write all of the necessary Terraform code yourself, but that would be time-consuming and error-prone. Instead, you could use a pre-built Terraform module for web servers on AWS. This module would contain all of the necessary configuration to create and manage the web server, including the EC2 instance, EBS volume, and security group.

To use the module, you would simply need to add it to your Terraform configuration and specify the necessary parameters, such as the instance size and EBS volume size. Terraform would then take care of creating and managing the web server for you.

To put it bluntly, using pre-built Terraform modules, you can create infrastructure more efficiently and with fewer errors.

## Top 3 Benefits of using modules in AWS Terraform!

If i had to narrow the field down to 3, I would include:

> **1. Reusability**
One of the main benefits of creating individual Terraform modules is that they can be reused in multiple deployments, saving time and effort. This is especially beneficial for organizations that deploy similar infrastructure across multiple environments. For example, a module for a basic VPC with public and private subnets and route tables could be reused in all deployments, simply passing in different parameters for the CIDR block, number of subnets, and resource tags.

> **2. Maintainability**
Individual modules also improve maintainability. Modules encapsulate implementation details, allowing users to focus on configuration. This makes modules easier to understand and modify, especially for large and complex infrastructure deployments. For example, a module for a NAT gateway could encapsulate all logic required to create and configure a NAT gateway, such as creating the instance, attaching an elastic IP address, and configuring routing rules. This allows users to deploy NAT gateways without having to worry about the underlying details.

> **3. Consistency**
Creating individual modules can also help ensure consistency. Modules can be tested and validated before being used in production, which helps ensure that infrastructure is created consistently across deployments. For example, a module for a web application stack could encapsulate all resources required to deploy a web application, such as EC2 instances, a load balancer, and a database. This module could then be used in all deployments to ensure that web applications are deployed consistently.


Here are some explanations regarding some of the more commonly used modules in Terraform:


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
