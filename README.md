# terraform-2
mkdir terraform-task-2
cd terraform-task-2

nano main.tf

############################################
# PROVIDERS FOR TWO REGIONS
############################################

provider "aws" {
  alias  = "us_east"
  region = "us-east-1"
}

provider "aws" {
  alias  = "ap_south"
  region = "ap-south-1"
}

############################################
# USER DATA TO INSTALL NGINX
############################################

locals {
  nginx_install_script = <<-EOF
    #!/bin/bash
    sleep 20
    apt update -y
    apt install -y nginx
    systemctl enable nginx
    systemctl start nginx
  EOF
}

############################################
# EC2 INSTANCE IN US-EAST-1
############################################

resource "aws_instance" "web_us_east" {
  provider = aws.us_east

  ami           = "ami-0e86e20dae9224db8"   # Ubuntu 22.04 - us-east-1
  instance_type = "t2.micro"

  user_data = local.nginx_install_script

  tags = {
    Name = "Terraform-US-East-NGINX"
  }
}

############################################
# EC2 INSTANCE IN AP-SOUTH-1
############################################

resource "aws_instance" "web_ap_south" {
  provider = aws.ap_south

  ami           = "ami-03bb6d83c60fc5f7c"   # Ubuntu 22.04 - ap-south-1
  instance_type = "t2.micro"

  user_data = local.nginx_install_script

  tags = {
    Name = "Terraform-AP-South-NGINX"
  }
}

############################################
# OUTPUT PUBLIC IPS
############################################

output "us_east_ip" {
  value = aws_instance.web_us_east.public_ip
}

output "ap_south_ip" {
  value = aws_instance.web_ap_south.public_ip
}



terraform init
terraform plan

terraform apply -auto-approve

then the two region ip address
