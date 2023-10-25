# Getting Started with Terraform on VSC

Tools required for utilizing Terraform with AWS Console, including VSCode, the appropriate VSCode extensions, and properly authenticating your AWS account with VSCode using the CLI:<br>

>****NOTE: DO NOT INSTALL ANY OF THE FOLLOWING COMPONENTS ON AN CLOUD DRIVE AS THEY WILL NOT FUNCTION PROPERLY!***

**VSCode**

* [VSCode](https://code.visualstudio.com/)

**VSCode extensions**

* [HashiCorp Terraform extension](https://marketplace.visualstudio.com/items?itemName=HashiCorp.terraform)
* [AWS Toolkit for VSCode](https://marketplace.visualstudio.com/items?itemName=AmazonWebServices.aws-toolkit-vscode)


**AWS CLI**

* [AWS CLI](https://aws.amazon.com/cli/)



## Brief overview of Process:

***Create or paste in a Terraform configuration file.*** *This file will define the AWS resources that you want to create.*<br>
If you create the file from scratch just make sure you save with the appropriate ".tf" extension<br>

***Initialize Terraform.*** *This will download the necessary providers and modules.*<br>

***Plan*** the changes that Terraform will make to your AWS resources.<br>

***Apply*** the changes. *This will create or update your AWS resources.*<br>



## VSCode extension features:

**HashiCorp Terraform extension:**
Syntax highlighting
Code completion
Linting
VSCode extensions and features for managing AWS resources:

**AWS Toolkit for VSCode:**
Tree view of AWS resources
Ability to execute AWS CLI commands


## To check if Terraform is properly installed and its version:

````
terraform version
````
> This command should print the Terraform version that is installed on your system. If Terraform is not installed, you will need to install it before you can use it.<br>


## To authenticate your AWS account with VSCode using the CLI:

1. Install the AWS CLI.
2. Configure the AWS CLI to use your AWS credentials.
3. Open VSCode and install the AWS Toolkit extension.
4. In VSCode, click the **Command Palette** (Ctrl+Shift+P) and search for **AWS: Add a New Connection**.
5. Select **AWS CLI** from the list of authentication methods.
6. Enter your AWS access key ID and secret access key.
7. Click **Connect**.

>Once the AWS Toolkit extension is installed, you can authenticate your AWS account with VSCode. To do this, click the Command Palette (Ctrl+Shift+P) and search for AWS: Add a New Connection. Select AWS CLI from the list of authentication methods and enter your AWS access key ID and secret access key. Click Connect to authenticate your account.

**Here is an example of how the above process would look in a cmd line environment:**
````
# Install the AWS CLI
pip install awscli

# Configure the AWS CLI
aws configure

# Open VSCode and install the AWS Toolkit extension
code

# Authenticate your AWS account with VSCode
Ctrl+Shift+P
AWS: Add a New Connection
AWS CLI
[Enter your AWS access key ID and secret access key]
Connect
````



## Example of a simple Terraform configuration file that creates an EC2 instance:
````
provider "aws" {
region = var.region
}

resource "aws_instance" "example" {
ami = "ami-00000000"
instance_type = "t2.micro"
}

output "public_ip" {
value = aws_instance.example.public_ip
}
````

## To use this configuration file:

### Initialize Terraform:
````
terraform init
````

### Plan the changes that Terraform will make to your AWS resources:
````
terraform plan
````

### Apply the changes:
````
terraform apply
````



***Ok great, now that you have everything set-up we will go more in-depth into creating an EC2 and tearing it down after were done!
Check out the next tutorial!***

