# Terraform-Labs

## Lab-1: Creating an EC2 Instance in AWS and Installing Terraform

To begin, log in to AWS Console.

### Task-1: Installing Terraform on Ubuntu 20.04 operating system

* Manually Launch a `t2.micro` instance with OS version as `Ubuntu 22.04 LTS` in North Virginia (us-east-1) Region.
* Use tag "`Name : TerraformServer`"
* Create a new Keypair with the Name `Terraform-Keypair-YourName`
* In security groups, include ports `22 (SSH)` and `80 (HTTP)`.
* Configure Storage: 10 GiB
* Launch the Instance.
* Once Launched, Connect to the Instance using `MobaXterm` or `Putty` or `EC2 Instance Connect` with username "`ubuntu`".

Once the EC2 is ready, follow the below Commands to perform lab:
```
sudo hostnamectl set-hostname TerraformServer
bash
```
```
sudo apt update
```
```
sudo apt install wget unzip -y
```
```
wget https://releases.hashicorp.com/terraform/1.6.3/terraform_1.6.3_linux_amd64.zip
```
To know the latest Terraform version - [Install Terraform](https://developer.hashicorp.com/terraform/downloads)
```
unzip terraform_1.6.3_linux_amd64.zip
```
```
ls
sudo mv terraform /usr/local/bin
```
```
rm terraform_1.6.3_linux_amd64.zip
```
```
ls
terraform
```
```
terraform -v
```

### Task-2: Install Required Packages and login to Ubuntu server using Credentials. 
```
sudo apt-get install python3-pip -y
```
```
sudo pip3 install awscli
```
```
aws configure
```
* When it prompts for Credentials, Enter the Keys as example shown below
  
| `Access Key ID.` | `Secret Access Key ID.` |
| ------------------ | ------------------------- |
| AKIAIOSFODNN7EXAMPLE | wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY |

##### Note: If Credentials are not available generate from AWS IAM Service
Once LoggedIn check the account access
```
aws s3 ls
```
`Or` Use below command to check whether it is authenticated.
```
aws iam list-users
```
### Task-3: Now we are ready to perform the labs
Create directory as Lab1 & create local file
```
mkdir Lab1 && cd Lab1
```
```
vi local.tf
```
```
resource "local_file" "myfile" {

  filename = "/home/ubuntu/test.txt"
  content  = "wel come to terraform"
}
```
```
terraform init
```
```
terraform plan
```
```
terraform apply
```
To check whether the file is created follow the command

```
cd /home/ubuntu
ls
```
```
cat test.txt
```
Once Verified, you can destroy the File by changing it to the Lab1 Directory
```
cd Lab1
```
```
terraform destroy
```
```
cd ~
rm -rf Lab1
```
### Task-4: Launching your first AWS EC2 instance using Terraform 
```
cd ~
mkdir EC2-lab && cd EC2-lab/
```
```
vi example.tf
```
Add the given lines, by pressing "INSERT"

##### Note: 
1. Replace your allocated `Region` and `AMI ID` of the same Region.
2. `"Yourname-EC2-1"` with Your Name.
```
provider "aws" {
  profile = "default" # This line is not mandatory.
  region  = "us-east-2"
}

resource "aws_instance" "example" {
  ami           = "ami-07efac79022b86107"
  instance_type = "t2.micro"
  tags = {
    Name = "Yourname-EC2-1"
  }
}
```
Save the file using "ESCAPE + :wq!"
```
terraform init
```
```
terraform fmt
```
```
terraform validate
```
```
terraform plan
```
```
terraform apply
```
```
#List the files
ls 
```
To see what is saved in `terraform.tfstate` use the below command.

```
cat terraform.tfstate
```
Now, Let's Replace the AMI ID with `Amazon Linux AMI 2023` from the same region and see what happens.

`Note:` To get the `Amazon Linux AMI 2023` go to EC2 Instances, click on Launch, and select the `Amazon Linux AMI 2023` AMI and copy the ID and paste in File.
```
vi example.tf
```
Replace the AMI ID, by pressing "INSERT"  
```
provider "aws" {
  profile = "default"
  region  = "us-east-2"
}

resource "aws_instance" "example" {
  ami           = "ami-02a89066c48741345"
  instance_type = "t2.micro"
  tags = {
    Name = "Yourname-TF-1"
  }
}
```

Save the file using "ESCAPE + :wq!"

Now in Terraform Plan Verify the Changes.
```
terraform plan
```
```
terraform apply
```
Once Applied then go to the console and see that the Previous EC2 is being destroyed and new EC2 is getting created.
    
Also verify that the OS is `Amazon Linux AMI 2023`

Again when you cat the `terraform.tfstate` file it shows a new AMI ID.
```
cat terraform.tfstate
```
Use the `terraform destroy` command for cleaning the infrastructure used in this lab
```
terraform destroy
```
Finally, verify that the resources are deleted in the Console.
```
cd ~
rm -rf EC2-lab
```


## Lab-2: AWS EC2 instance creation using Terraform Variables


### Task-1: Create EC2 instance using variables 
```
mkdir variables-lab && cd variables-lab/
```
Now Create four Files ie. `provider.tf,` `vars.tf,` `terraform.tfvars,` `instance.tf.`
```
vi provider.tf
```
Add the given lines, by pressing "INSERT" 
```
provider "aws"{
  access_key=var.AWS_ACCESS_KEY
  secret_key=var.AWS_SECRET_KEY
  region=var.AWS_REGION
}
```
Save the file using "ESCAPE + :wq!"
```
vi vars.tf
```
Add the given lines, by pressing "INSERT" also replace your `Region`
```
variable "AWS_ACCESS_KEY"{}
variable "AWS_SECRET_KEY"{}
variable "AWS_REGION"{
  default = "us-east-2"
}
```
Save the file using "ESCAPE + :wq!"
```
vi terraform.tfvars
```
Add the given lines, by pressing "INSERT" Also replace them with your `AccessKey` and `Secret Access Keys` that you created and downloaded earlier.
```
AWS_ACCESS_KEY="< Insert your AWS Access Key >"
AWS_SECRET_KEY="< Insert your AWS Secret Key >"
```
Save the file using "ESCAPE + :wq!"
```
vi instance.tf
```
Add the given lines, by pressing "INSERT" Also replace your `region's AMI ID` and `YourName`
```
resource "aws_instance" "terraform_example"{
  ami = "ami-07efac79022b86107"
  instance_type="t2.micro"
  tags = {
    Name = "Variables-Lab2-YourName"
  }
}
```
Save the file using "ESCAPE + :wq!"
```
terraform init
```
```
terraform plan
```
```
terraform apply
```
Once applied, go to the Console and check that the new EC2 is created using Variables.

Use the `"terraform destroy"` command to clean the infrastructure used in this lab
```
terraform destroy -auto-approve
```

### Task-2: Implementing map variables that dynamically fetch AMI based on the Linux distro selected
```
vi instance.tf
```
Delete all the existing lines and Add the given code, by pressing "INSERT" and replacing `YourName`

`Note:` To delete all the lines at a time use the commands `Esc+gg+dG` and once cleared then add the new lines.
```
resource "aws_instance" "terraform_example"{
  ami = var.AMIS[var.Linux_distro]
  instance_type="t2.micro"
  tags = {
    Name = "YourName-Lab2-Task2"
  }
}
```
Save the file using "ESCAPE + :wq!"

Now, Also make changes in the `Vars.tf` file.
```
vi vars.tf
```
Delete all existing lines and Add the given lines, by pressing "INSERT" Also ensure to replace your `Region,` `AMi IDs` of the same region for `redhat,` `ubuntu` and `amazon`

`Note:` To delete all the lines at a time use `Esc+gg+dG`
```
variable "AWS_ACCESS_KEY"{}
variable "AWS_SECRET_KEY"{}
variable "AWS_REGION"{
  default = "us-east-2"
  }

variable "Linux_distro"{
  #description = "Please Enter the Linux distro (redhat, ubuntu, amazon)"
  default = "amazon"
  }

variable "AMIS"{
  type=map(string)
  default={
   redhat="ami-0ba62214afa52bec7"
   ubuntu="ami-0fb653ca2d3203ac1"
   amazon="ami-0231217be14a6f3ba"
  }
}
```
Save the file using "ESCAPE + :wq!"
```
terraform fmt
```
```
terraform validate
```
```
terraform plan -var 'Linux_distro=redhat' -out myplan
```
```
terraform apply myplan
```
Use the `terraform destroy` command to clean the infrastructure used in this lab
```
terraform destroy
```
Once Done remove the `EC2-lab` Directory.
```
cd ~
rm -rf variables-lab

```
