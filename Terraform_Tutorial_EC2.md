# Terraform Introduction


Terraform is an open-source infrastructure as code (IaC) tool that allows you to define and manage your cloud infrastructure in a declarative manner. Terraform uses a simple, human-readable language called the HashiCorp Configuration Language (HCL) to define your infrastructure. With Terraform, you can create, modify, and destroy infrastructure resources across multiple cloud providers, including Amazon Web Services (AWS) 

In other words, its designed to make life easier for the average AWS Cloud Developer!

Terraform is useful because it allows you to manage your infrastructure as code, which means that you can version control your infrastructure changes, collaborate with other team members, and automate your infrastructure deployment process. Terraform also provides a consistent and repeatable way to create and manage your infrastructure resources, which helps to reduce errors and increase efficiency 

Terraform is highly compatible with AWS and provides a rich set of resources that allow you to manage your AWS infrastructure. You can use Terraform to create and manage a wide range of AWS resources, including EC2 instances, S3 buckets, RDS databases, VPCs, and more. 

Let's demonstrate the power of Terraform by creating a simple EC2 instance!

# Download Terraform 

First  things first! Lets download Terraform from: https://developer.hashicorp.com/terraform/downloads

![installterraform](https://github.com/mindmotivate/HashiCorp_Terraform/assets/130941970/42e34920-f35b-4b56-8596-ade9e4aab095)

***Make sure you download the appropriate version of Terraform for your operating system!***

# Create a new directory

After it is installed, you will need to create a new directory on your local computer where you will store your Terraform configuration files.

# Create a new file
Let's create a new file named `main.tf` in the directory you created!

We will paste our ec2 creation script here:

````
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "example" {
  ami           = "ami-0df435f331839b2d6"
  instance_type = "t2.micro"
}
````

*Make sure you save your file!*

If we use the command: ```terraform init``` we will intialize terraform on our copy of VSCode:
````
PS C:\Users\Owner\desktop\code\terraform> terraform init

Initializing the backend...

Initializing provider plugins...
- Reusing previous version of hashicorp/aws from the dependency lock file
- Using previously-installed hashicorp/aws v5.15.0
Terraform has been successfully initialized!
````

![t_intit](https://github.com/mindmotivate/HashiCorp_Terraform/assets/130941970/d22c95a8-9394-47a3-a5ea-9e8486dde8f7)

![terraform_init](https://github.com/mindmotivate/HashiCorp_Terraform/assets/130941970/1625d31b-21ed-4fc4-870e-22d4c37b9284)


If we use the command: ```terraform apply``` we will execute the launch of our EC2:

````

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_instance.example will be created
  + resource "aws_instance" "example" {
      + ami                                  = "ami-0df435f331839b2d6"
      + arn                                  = (known after apply)
      + associate_public_ip_address          = (known after apply)
      + availability_zone                    = (known after apply)
      + cpu_core_count                       = (known after apply)
      + cpu_threads_per_core                 = (known after apply)
      + disable_api_stop                     = (known after apply)
      + disable_api_termination              = (known after apply)
      + ebs_optimized                        = (known after apply)
      + get_password_data                    = false
      + host_id                              = (known after apply)
      + host_resource_group_arn              = (known after apply)
      + iam_instance_profile                 = (known after apply)
      + id                                   = (known after apply)
      + instance_initiated_shutdown_behavior = (known after apply)
      + instance_lifecycle                   = (known after apply)
      + instance_state                       = (known after apply)
      + instance_type                        = "t2.micro"
      + ipv6_address_count                   = (known after apply)
      + ipv6_addresses                       = (known after apply)
      + key_name                             = (known after apply)
      + monitoring                           = (known after apply)
      + outpost_arn                          = (known after apply)
      + password_data                        = (known after apply)
      + placement_group                      = (known after apply)
      + placement_partition_number           = (known after apply)
      + primary_network_interface_id         = (known after apply)
      + private_dns                          = (known after apply)
      + private_ip                           = (known after apply)
      + public_dns                           = (known after apply)
      + public_ip                            = (known after apply)
      + secondary_private_ips                = (known after apply)
      + security_groups                      = (known after apply)
      + source_dest_check                    = true
      + spot_instance_request_id             = (known after apply)
      + subnet_id                            = (known after apply)
      + tags_all                             = (known after apply)
      + tenancy                              = (known after apply)
      + user_data                            = (known after apply)
      + user_data_base64                     = (known after apply)
      + user_data_replace_on_change          = false
      + vpc_security_group_ids               = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.
  Enter a value: yes

aws_instance.example: Creating...
aws_instance.example: Still creating... [10s elapsed]
aws_instance.example: Still creating... [20s elapsed]
aws_instance.example: Still creating... [30s elapsed]
aws_instance.example: Creation complete after 32s [id=i-0bd6bd558c496ee4b]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
````
![terraform](https://github.com/mindmotivate/HashiCorp_Terraform/assets/130941970/ac2bc8d3-66cd-4565-800c-fd3e8f3ac428)



If we use the command: ````terraform show```` we will seee a summary of the ec2 we just created:

````
PS C:\Users\Owner\desktop\code\terraform> terraform show 
# aws_instance.example:
resource "aws_instance" "example" {
    ami                                  = "ami-0df435f331839b2d6"
    arn                                  = "arn:aws:ec2:us-east-1:732504928444:instance/i-0bd6bd558c496ee4b"
    associate_public_ip_address          = true
    availability_zone                    = "us-east-1c"
    cpu_core_count                       = 1
    cpu_threads_per_core                 = 1
    disable_api_stop                     = false
    disable_api_termination              = false
    ebs_optimized                        = false
    get_password_data                    = false
    hibernation                          = false
    id                                   = "i-0bd6bd558c496ee4b"
    instance_initiated_shutdown_behavior = "stop"
    instance_state                       = "running"
    instance_type                        = "t2.micro"
    ipv6_address_count                   = 0
    ipv6_addresses                       = []
    monitoring                           = false
    placement_partition_number           = 0
    primary_network_interface_id         = "eni-0fcfc55468a31997f"
    private_dns                          = "ip-172-31-23-57.ec2.internal"
    private_ip                           = "172.31.23.57"
    public_dns                           = "ec2-54-175-103-150.compute-1.amazonaws.com"
    public_ip                            = "54.175.103.150"
    secondary_private_ips                = []
    security_groups                      = [
        "default",
    ]
    source_dest_check                    = true
    subnet_id                            = "subnet-083c283b55b503c9b"
    tags_all                             = {}
    tenancy                              = "default"
    user_data_replace_on_change          = false
    vpc_security_group_ids               = [
        "sg-0b1ff78c5c5a2491b",
    ]

    capacity_reservation_specification {
        capacity_reservation_preference = "open"
    }

    cpu_options {
        core_count       = 1
        threads_per_core = 1
    }

    credit_specification {
        cpu_credits = "standard"
    }

    enclave_options {
        enabled = false
    }

    maintenance_options {
        auto_recovery = "default"
    }

    metadata_options {
        http_endpoint               = "enabled"
        http_protocol_ipv6          = "disabled"
        http_put_response_hop_limit = 2
        http_tokens                 = "required"
        instance_metadata_tags      = "disabled"
    }

    private_dns_name_options {
        enable_resource_name_dns_a_record    = false
        enable_resource_name_dns_aaaa_record = false
        hostname_type                        = "ip-name"
    }

    root_block_device {
        delete_on_termination = true
        device_name           = "/dev/xvda"
        encrypted             = false
        iops                  = 3000
        tags                  = {}
        throughput            = 125
        volume_id             = "vol-02c1e56c6390b1272"
        volume_size           = 8
        volume_type           = "gp3"
    }
}

`````




Congratulations, you have successfully launched your first EC2 using nothing more than a Terraform script!....Isn't Automation great!!!

If you navigate to your EC2 console you will notice that you have a fresh new EC2 instance running!

![ec2running](https://github.com/mindmotivate/HashiCorp_Terraform/assets/130941970/a84762ad-ddcd-4201-8674-c7f92e97e6f0)


Now let's DESTROY the EC2  isntance so we dont get charged!

## Simple Teardown with Terraform:

When you are finished with the EC2 instance, you can destroy it by running the following command:
````terraform destroy````
This command will destroy all resources that were created by Terraform, including the EC2 instance.
![destroy](https://github.com/mindmotivate/HashiCorp_Terraform/assets/130941970/a66ebb15-4dde-41e3-8bd6-0aace98a63a4)


If you check your AWS Console, you will see that your instance has infact...been terminated!

![console_termination](https://github.com/mindmotivate/HashiCorp_Terraform/assets/130941970/8d07d857-0ddf-42ab-90a1-90985981d4a0)


That’s it! You have successfully created and destroyed an EC2 instance in AWS using Terraform commands only 1

