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

Now Terraform is installed and ready to be used