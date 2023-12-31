

 <img src="https://github.com/mindmotivate/HashiCorp_Terraform/assets/130941970/067d3999-20dd-4470-bedd-5d14b4749279" width="20%" height="20%">
 <img src="https://github.com/mindmotivate/HashiCorp_Terraform/assets/130941970/417ac0b9-ad96-4434-888a-d2389210845b" width="20%" height="20%">
 <img src="https://github.com/mindmotivate/HashiCorp_Terraform/assets/130941970/ccc54cbd-1a3b-45bc-8988-e1862fbce232" width="20%" height="20%">
 <img src="https://github.com/mindmotivate/HashiCorp_Terraform/assets/130941970/77ea6eea-73e3-47cd-ad26-743eed6f0f83" width="15%" height="15%">
<img src="https://github.com/mindmotivate/HashiCorp_Terraform/assets/130941970/40e91a80-8f4e-43b4-a529-4a3f55b225be" width="20%" height="20%">

# Launching An AWS VPC in Terraform


This tutorial will walk you through the steps of creating an AWS VPC with subnets using Terraform. At this point in time we have demonstrated or ability to create VPC's and Subnets from within the AWS console. We will not apply that knowledge towards the creation of our Terrafrom modules as we build out infrastructure using the terraform in our VSC terminal! 

## Prerequisites
But before we begin let's make sure we have the following items ready to go:

* AWS account
* Terraform installed
* Git client installed
* [Forked GitHub Repository](https://github.com/malguswaf/class5)
* VSC Up and running with new project1 folder ready to go!


## Terraform Install (via Choclety for Windows)
>*If you **already have terraform** installed please skip this step.*
>*If you **do not** have terraform installed, you may so using the following options:*

**Choclety:** Enter the following into your Bash command line:
```
install choclety
```
Now let's say you already have Terraform installed, you can upgrade your vesriosn with the following command:
```
choco upgrade terraform
```
If you simply want to check to see which version of terraform you currently have, use the following command:
```
terraform version
```

**Note: If for some reason your choco install fails you can download Terraform from: https://developer.hashicorp.com/terraform/downloads**

<br>

### Open VSC editor
Next, we will open up our copy of Visual Studio Code
From within VSC code, we will create a new directory called "Project1"
VSC will display a pop=up message: "Do you to trust this folder" (Click "Yes")
From within our "project1" folder we will create a new file called: "0-Auth.tf" (be sure to add the ".tf" extention at the end or else VSC will not recognize the file as a "terraform" file)

### Open your Github account
Next, we will open "github" so that we can "fork" the code from our class repository
From within your github account navigate to: (https://github.com/malguswaf)
*simply type this in a seperate tab or type in your web browser search bar*

### "Fork" the code
-Locate the repository entitled: "class5"<br>
-Click on the "Fork" button located next to the<br>
*We are simply making our own local copy of the the class5 module files*<br>
-After you have completed forking, you will see the files appear in your very own "class5" repository<br>
-Keep these file readily available as we will be using them to create our modules.<br>


### Obtain module code
Navigate to your forked repository and locate the: "1-auth.tf" file
Here's the "malguswaf/class5" repository again: [Forked GitHub Repository](https://github.com/malguswaf/class5)
Simply copy the code from the file and paste it into your newly created "1-auth.tf" file

## Let's Begin Building Our Modules in VSC!
In your newly created "project1" directory please perform the following:

1. ### Run Authentication Module

    1. Create a new file called `0-Auth.tf` in your project directory.
    2. Copy and paste the code from the `0-Auth.tf` file in the forked "class5" repository.
    3. Make the following adjustments to the code:
        * Replace region = "eu-west-1" with your desired region
        * *(This should be the same region which your AWS console is situated in)*
        * Add your desired tags.
        * At this point "SAVE" (Ctrl-S) your file!

Your code should look similar to this:

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


Once you have completed all of the steps above, you can run the following commands to create your VPC and subnets:
```
Terraform init
Terraform plan
Terraform apply
```

2. ### Run VPC Module

    1. Create a new file called `1-VPC.tf` in your "project1" directory.
    2. Copy and paste the code from the `1-VPC.tf` file in the forked "class5" repository.
    3. Make the following adjustments to the code:
        * Replace the included `cidr_block` reference if needed so that it matches your region and planning document.
        * Add your desired tags.
      

Your code should look similar to this:

```
# this  makes  vpc.id which is aws_vpc.app1.id
resource "aws_vpc" "app1" {
  cidr_block = "10.32.0.0/16"

  tags = {
    Name = "app1"
    Service = "application1"
    Owner = "Chewbacca"
    Planet = "Mustafar"
  }
}

```

Once you have completed all of the steps above, you can run the following commands to create your VPC and subnets:

```
Terraform init
Terraform plan
Terraform apply
```

3. ### Run Subnet Module**

    1. Create a new file called `2-subnets.tf` in your project directory.
    2. Copy and paste the code from the `2-subnets.tf` file in the forked class5 repository.
    3. Make the following adjustments to the code:
        * Ensure that the name is approprate:`aws_vpc.app1.id` (for your public and private subnets in each AZ)
        * Replace the included `cidr_block` reference with your own (for your public and private subnets in each AZ)
        * Replace the third octet with the appropriate public and private IP subnet designations (for your public and private subnets in each AZ)
        * Add your desired tags.
        * At this point "SAVE" (Ctrl-S) your file!
      
Your code should look similar to this:
```
resource "aws_subnet" "public-us-east-1a" {
  vpc_id                  = aws_vpc.app1.id
  cidr_block              = "10.32.1.0/24"
  availability_zone       = "us-east-1a"
  map_public_ip_on_launch = true

  tags = {
    Name = "public-us-east-1a"
    Service = "application1"
    Owner = "Chewbacca"
    Planet = "Musafar"
  }
}



#these are for private
resource "aws_subnet" "private-us-east-1a" {
  vpc_id            = aws_vpc.app1.id
  cidr_block        = "10.32.11.0/24"
  availability_zone = "us-east-1a"

  tags = {
    Name = "private-us-east-1a"
    Service = "application1"
    Owner = "Chewbacca"
    Planet = "Musafar"
  }
}
#these are for public
resource "aws_subnet" "public-us-east-1b" {
  vpc_id                  = aws_vpc.app1.id
  cidr_block              = "10.32.2.0/24"
  availability_zone       = "us-east-1b"
  map_public_ip_on_launch = true

  tags = {
    Name = "public-us-east-1b"
    Service = "application1"
    Owner = "Chewbacca"
    Planet = "Musafar"
  }
}

#these are for private
resource "aws_subnet" "private-us-east-1b" {
  vpc_id            = aws_vpc.app1.id
  cidr_block        = "10.32.12.0/24"
  availability_zone = "us-east-1b"

  tags = {
    Name = "private-us-east-1b"
    Service = "application1"
    Owner = "Chewbacca"
    Planet = "Musafar"
  }
}
#these are for public
resource "aws_subnet" "public-us-east-1c" {
  vpc_id                  = aws_vpc.app1.id
  cidr_block              = "10.32.3.0/24"
  availability_zone       = "us-east-1c"
  map_public_ip_on_launch = true

  tags = {
    Name = "public-us-east-1c"
    Service = "application1"
    Owner = "Chewbacca"
    Planet = "Musafar"
  }
}

#these are for private
resource "aws_subnet" "private-us-east-1c" {
  vpc_id            = aws_vpc.app1.id
  cidr_block        = "10.32.13.0/24"
  availability_zone = "us-east-1c"

  tags = {
    Name = "private-us-east-1c"
    Service = "application1"
    Owner = "Chewbacca"
    Planet = "Musafar"
  }
}
```

Once you have completed all of the steps above, you can run the following commands to create your VPC and subnets:

```
Terraform init
Terraform plan
Terraform apply
```

Tips

Congratutlations! You have build your first VPC and Subnet architecture with Terraform!

For those of you who want a more granular process approach continue reading below:


## Need More Details?

# Download Terraform 



# Fork The Class 5 Repository from GITHUB
Go ahead and open your personal github account
Once inside, naviagte to the the "MalgusWaf/class5 Page" : Github https://github.com/malgus-waf/class5
Locate the repository entitled: "class5"
Click on the "Fork" button located next to the
We are simply making our own local copy of the the class5 module files
After you have completed forking, you will see the files appear in your very own "class5" repository
Keep these file readily available as we will be using them to create our modules.

# Run Authentication Module



### Perform Code Adjustments
We will make the following adjustments to the code:
?????????????????????????????????

At this point "SAVE" (Ctrl-S) your file!

### Navigate to present working directory
*If you currently do not see your terminal window on the screen navigate the VSC menu bar and click on "new terminal"
From your terminal, Navigate to your "project1" directory by using the following commands:
pwd (this command will let you know which directory your are currently in)
cd .. (this command will allow you to move to the "parent" directory)
cd "folder name" (This command will allow you to navigate to a specific folder within your directory"
once you have sucessfully located your "project1" directory, go ahead and save your file (ctrl-s)
*As a rule of thumb, always save and run your terrform commands from within the correct folder!

### Run Terraform Terminal Commands
 On terminal type in
- Terraform init
- Terraform plan
- Terraform apply
Hint: Do not erase, alternate terraform.tfstate file DO NOT DO IT!!!!!!!


## Prelimary Steps
Make sure you have an established planning document with your desired CIDR range ec: 10.32.9.0/16

# Run VPC Module
We will create the file for the VPC module inside of our "project1" folder.
From within your project1 directory, create a new file entitled: 1-VPC.tf
Navigate to your forked repository and locate the: "1-VPC.tf" file
Simply copy the code from the file and paste it into your newly created "1-VPC.tf" file

We will make the following adjustments to the code:
Replace the aws_vpc.ireland.id label with aws_vpc.app1.id
Replace the included cidr_block reference with your own (ensure that you chosen region is represented appropriately in second octet ex: replace 78 w/ 32)
Next, add your desired tags ex:
*Name= ”Public”, Service=app1, Owner=Chewbaca, Planet=Mustafar*

At this point "SAVE" (Ctrl-S) your file!

```
Terraform init
Terraform plan
Terraform apply
```


## Prelimary Steps
Make sure you are working from your Planning document which should contain you pre-written subnets!


# Run Subnet Module

Now that we have an established VPC, lets add our subnets with the following module

We will remain in the same "project1" folder that we have been using throughout the duration of the tutorial.
From within your project1 directory, create a new file entitled: 2-subnets.tf

Naviagte to your forked repository and locate the: "2-subnets.tf" file
Simply copy the code from the file and paste it into your newly created "2-subnets.tf" file

We will make a few adjustments to the code:
Replace the aws_vpc.ireland.id label with aws_vpc.app1.id
Repalce the included cidr_block reference with your own (ensure that you chosen reagion is represented appropriately in second octet ex: replace 78 w/ 32)
Replace third octet (subnet) with the appropriate public and private ip subnet designations ex: public 10.32.1.0/24 private 10.12.0/24
Next, add your desired tags ex: Name= ”Public”, Service=app1, Owner=Chewbaca, Planet=Mustafar

At this point "SAVE" (Ctrl-S) your file!

Your code should look similar to this:

Run the following command individually in the following order:

```
Terraform init
Terraform plan
Terraform apply
```

