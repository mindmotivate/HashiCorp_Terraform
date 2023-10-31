



# Download Terraform 

First things first! If you haven't already, Let's download Terraform from: https://developer.hashicorp.com/terraform/downloads

If you do not have terraform installed you may so so using the following options:

Depending on your operating system, choose one of the following:

Homebrew:

Enter the following into your Bash command line:

Choclety:

Enter the following into your Bash command line:


# Fork The Class 5 Repository from GITHUB
Go ahead and open your personal github account
Once inside, naviagte to the the "MalgusWaf/class5 Page" : Github https://github.com/malgus-waf/class5
Locate the repository entitled: "class5"
Click on the "Fork" button located next to the
We are simply making our own local copy of the the class5 module files
After you have completed forking, you will see the files appear in your very own "class5" repository
Keep these file readily available as we will be using them to create our modules.




# Run Authentication Module

Next, we will open up our copy of Visual Studio Code
From within VSC code, we will create a new directory called "Project1"
VSC will display a pop=up message: "Do you to trust this folder" (Click Yes)
From within our "project1" folder we will create a new file called: "0-Auth.tf" (be sure to add the ".tf" extention at the end or else VSC will not recognize the file as a "terraform" file)

Navigate to your forked repository and locate the: "1-auth.tf" file
Simply copy the code from the file and paste it into your newly created "1-auth.tf" file

*If you currently do not see your terminal window on the screen navigate the VSC menu bar and click on "new terminal"
From your terminal, Navigate to your "project1" directory by using the following commands:
pwd (this command will let you know which directory your are currently in)
cd .. (this command will allow you to move to parent directory)
cd "folder name" (This command will allow you to navigate to a specific folder within your directory"
once you have sucessfully located your "project1" directory, go ahead and save your file (ctrl-s)
*As a rule of thumb, alwas save and run your terrform commands within the correct folder!

10. (To see if your code work) Click on Terminal then on drop down click New Terminal
11. Click Terminal at the bottom screen
12. Type in pwd on Terminal then type in pwd on Gitbash type in pwd to compare directory
13. On terminal type in
- Terraform init
- Terraform plan
- Terraform apply
Hint: Do not erase, alternate terraform.tfstate file DO NOT DO IT!!!!!!!


## Prelimary Steps
Make sure you have an established planning document with your desired CIDR range ec: 10.32.9.0/16

# Run VPC Module


We will create the fiel for the VPC module inside of our "project1" folder.
From within your project1 directory, create a new file entitled: 1-VPC.tf
Navigate to your forked repository and locate the: "1-VPC.tf" file
Simply copy the code from the file and paste it into your newly created "1-VPC.tf" file

We will make the following adjustments to the code:
Replace the aws_vpc.ireland.id label with aws_vpc.app1.id
Replace the included cidr_block reference with your own (ensure that you chosen region is represented appropriately in second octet ex: replace 78 w/ 32)
Next, add your desired tags ex: Name= ”Public”, Service=app1, Owner=Chewbaca, Planet=Mustafar



- Terraform init
- Terraform plan
- Terraform apply



VPC file
1. On your VScode click on project1 on your tab
2. Click on the blank file with the + tab right beside it
3. Type in 1-VPC.tf as your new file
4. Copy and paste the script on Theo’s github on VPC
5. Change your Cidr_block = 10.78.0.0/16 and change the second octet
6. Change “ireland” to “app1”
7. Label your tags
8. Click on file then save
9. Click on Terminal then New Terminal then click on Terminal at the bottom of screen
10. Verify your files with ls and pwd with the Terminal and Gitbash
11. On Terminal type in
- Terraform init
- Terraform plan
- Terraform apply
12. It will ask to Enter a value….answer yes
13. Check AWS (The Gooey) to check if your code synchronized the file through Terraform
14. Check the VPC in that region and check for “app1”
15. Play around with with by alternating the tags etc
16. Viola you have synchronized your VPC
Next, add your desired tags ex: Name= ”Public”, Service=app1, Owner=Chewbaca, Planet=Mustafar

At this point "SAVE" (Ctrl-S) your file!




## Prelimary Steps
Make sure you are working from your Planning doxument which should contain you pre-written subnets!


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

Run the following command individually in the following order:

- Terraform init
- Terraform plan
- Terraform apply




3. Use one of Theo subnet sample code then copy and paste it to your created file VScode
4. Replace aws_vpc.ireland.id with aws_vpc.app1.id
5. 
6. Replace third octet (subnet) with the public and private ip subnet
7. Tag your subnets ex: Name=”Public”, Service=app1, Owner=Chewbaca, Planet=Jupiter
8. Click on File then click on Save
9. Type in ls on Terminal and type in ls on your gitbash to verify your folder is synced
10. Type in Terraform plan
11. Type in Terraform apply
12. Enter Value: yes
13. Check your AWS and click on subnets
14. Verify your subnets are in the Gooey
15. Copy and paste your subnets, add two and modify changes for AZ public and private
16. Click on File and Click Save
17. Type in Terraform plan
18. Type in Terraform apply
