# ACIT4640-Lab4

This document will be going over Terraform installation and general setup.

## Installing Terraform in a Debian Environment

There are two ways you can go about to download Terraform onto your linux distribution. These options include through the package manager and downloading the actual binaries onto your system. Now Debian should come preinstalled with these packages, but I needed to download these individually, so just in case make sure you have `wget`, `gpg`, and `lsb-release` by running the following:

```bash
sudo apt update
sudo apt install wget
sudo apt install gpg
sudo apt install lsb-release
```

### Downloading Binaries

For the binary file, since we will be downloading a zip file we want to install unzip onto our system with:
```bash
sudo apt install unzip
```

Then after installing unzip we want to download the binaries with wget and unzip the folder:

```bash
wget https://releases.hashicorp.com/terraform/1.9.8/terraform_1.9.8_linux_amd64.zip

unzip terraform_1.9.8_linux_amd64.zip
```

After unzipping, we want to add terraform to our path with the following:
```bash
sudo mv terraform /usr/local/bin/
```

And then lastly you can double check the version of Terraform with:
```bash
terraform -version
```

### Downloading with the Package Manager
With the package manager, we just need to run a couple lines to install Terraform. First we need get the gpg keys to identify the hashicorp devs who maintain the repo. Then we will add the repo to a list of package repositories. Lastly, we will update our system and install Terraform.

```bash
wget -O - https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt update && sudo apt install terraform
```

Now Terraform is installed and ready to be used.

## Cloud-Init Config
Here we are going to setup some basic configuration for our EC2 instance that we will be setting up later with Terraform. But before we start working on on the cloud-config.yaml file, we need a ssh key that we will use later to connect to the EC2 instance that Terraform will make.

This can be done with the ssh-keygen command:
```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

Now that we have the key pairs, we can start working on our cloud-config.yaml file. Here we can specify the users, different packages we want installed, and even commands we want run when the EC2 is set up.

```yaml
#cloud-config
users:
  - name: web
    primary_group: web
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - <your public key here>

package_update: true
package_upgrade: true
packages:
  - <packages you want installed here>
  - nginx
  - nmap

runcmd: 
  - <commands you want to run here>
  - systemctl enable nginx
  - systemctl start nginx
```

Above is the cloud-config.yaml file, where we have specified our user for the EC2 instance and the key to connect to the EC2. Then we also specified that we want nginx and nmap installed once the EC2 is created, and lastly, we specified that we want nginx enabled and started once installed.

## Main.tf Configuration
Before we use Terraform, we want to edit the `main.tf` file. This file contains all of the configuration for the infrustructure we plan on making. Here we can specify the provider (aws), what ami we want to use (debian), creating VPCs, creating EC2s, allowing SSH, and much, much more. All the documentation can be found here: [Terraform Documentation](https://registry.terraform.io/providers/hashicorp/aws/latest/docs){:target="_blank"}

Here is a small example of what the main.tf file may look like:
```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }
}

# Configure the AWS Provider
provider "aws" {
  region = "us-east-1"
}

# Create a VPC
resource "aws_vpc" "example" {
  cidr_block = "10.0.0.0/16"
}
```
Once the main.tf file is set up, we can verify it with `terraform validate`. This will tell us if there is any errors with our syntax or other warnings if there are other issues.

## Using Terraform