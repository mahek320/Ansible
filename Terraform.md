## Security group - All traffic
#### Install Terraform

sudo yum install -y yum-utils shadow-utils

sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/AmazonLinux/hashicorp.repo

sudo yum -y install terraform

#### Version

terraform -v

#### Install aws cli

``` yml

curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

unzip awscliv2.zip

sudo ./aws/install

```

#### Make directory

mkdir /project

cd /project

#### Vim file 
#### Naming extension for terraform file is tf
#### Create an user for access key and secret key
#### The region should be of the ami created

vim provider.tf

``` tf

provider "aws" {
  region     = "us-west-2"
  access_key = "my-access-key"     #add the keys
  secret_key = "my-secret-key"
}

```

#### Terminal

l.

yum install tree -y

tree -a  (*To check the generated file*)

terraform init

#### Inside vim file 
#### my ec2 code
#### copy ami from other other resources on a different region
#### Give the name of your instance
#### Name of the key should be of new region 
#### Create new key pair from key pair option

``` tf

resource "aws_instance" "this" {
  ami                     = "ami-0dcc1e21636832c5d"
  instance_type           = "t2.micro"
  key_name                = "terraform-key"      # *Give name of the key pair*
}

```

#### Terraform init

terraform init

terraform validate (*The configuration is valid*)

terraform plan

terraform apply 

#### Connect the new isntance on a new terminal 

#### Security group (SSH, All traffic)

yum install httpd -y

#### Old terminal 
cd /project

aws configure

vim provider.tf (*Backspace the first para*)

terraform destroy

ll
(*After ll you will get a file that wasn't created by you, do cat filename*)
cat terraform.tstate

#### Manage tag new instance

manage tag >> name

terraform fmt

cat provider.tf

terraform apply 

terraform destroy

#### Old terminal 

mkdir /mahek
cd /project
cd provider.tf /mahek
cd /mahek
mv provider.tf my-resource.tf 
vim my-resource.tf

resource "aws_instance" "this" {
  ami                     = "ami-0dcc1e21636832c5d"
  instance_type           = "t2.micro"
  availability_zone       = "us-east-1a"
  key_name                = "terraform-key"      # *Give name of the key pair*
  
}

#### To create an instance from terminal, add security groups, attach abs and volume.
#### Configure aws
#### Create a new key pair in the new region where you want to create a new instance
#### Terraform init, fmt (format), validate, plan, apply

vim security.tf

``` tf


provider "aws" {
  region = "ap-northeast-2"
}

resource "aws_security_group" "my_security_group" {
  name        = "my-sg1"
  description = "Allow SSH and HTTP traffic"

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
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
}

resource "aws_instance" "my_instance" {
  ami                    = "ami-06f73fc34ddfd65c2" (# *Replace with a valid AMI ID for your region*)
  instance_type          = "t2.micro"
  key_name               = "seoul-key"
  vpc_security_group_ids = [aws_security_group.my_security_group.id]

  tags = {
    Name = "MyInstance"
  }
}

resource "aws_ebs_volume" "example" {
  availability_zone = "ap-northeast-2a"
  size              = 40

  tags = {
    Name = "HelloWorld"
  }
}

resource "aws_volume_attachment" "ebs_att" {
  device_name = "/dev/sdh"
  volume_id   = aws_ebs_volume.example.id
  instance_id = aws_instance.my_instance.id     (*Name of the instance created*)
}

```

terraform init 

terraform fmt 

terraform validate 

terraform plan

terraform apply 

#### Go check on aws ,open your instance > storage > volume > open your attached volume.

#### Create vpc and subnet

vim vpc.tf

``` tf
resource "aws_vpc" "main" {
  cidr_block       = "10.0.0.0/16"
  instance_tenancy = "default"

  tags = {
    Name = "main"
  }
}
resource "aws_subnet" "main" {
  vpc_id     = aws_vpc.main.id
  cidr_block = "10.0.1.0/24"

  tags = {
    Name = "Main"
  }
}

```









