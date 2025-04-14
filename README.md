# TASK-03
Elevate Labs

# 🚀 Docker + Terraform + WSL Setup Guide

This project demonstrates how to:
- Install and configure WSL on Windows
- Install Docker inside WSL (Ubuntu 24.04)
- Install Terraform
- Use Terraform to deploy an NGINX container via Docker

---

## 🛠️ Step 1: Install WSL on Windows

Open **PowerShell as Administrator** and run the following commands:

```powershell
winget install --id Microsoft.WSL -e --source winget
wsl --version

Start-Process -FilePath "dism.exe" -ArgumentList "/online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart" -Verb RunAs
Start-Process -FilePath "dism.exe" -ArgumentList "/online /enable-feature /featurename:VirtualMachinePlatform /all /norestart" -Verb RunAs
Start-Process -FilePath "bcdedit.exe" -ArgumentList "/set hypervisorlaunchtype auto" -Verb RunAs
Start-Process -FilePath "bcdedit.exe" -ArgumentList "/enum" -NoNewWindow -PassThru | Out-String | Select-String -Pattern "hypervisorlaunchtype"
🔁 Restart your system after running the above commands.

Then, run:


wsl --set-default-version 2
wsl --install -d Ubuntu-24.04
wsl -d Ubuntu-24.04
wsl -l -v
🐳 Step 2: Install Docker in WSL (Ubuntu)

sudo apt-get update
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

sudo apt-get update
sudo apt-get install docker-ce

sudo systemctl enable docker
sudo systemctl start docker
sudo systemctl status docker
🌍 Step 3: Install Terraform

sudo apt update && sudo apt install -y gnupg software-properties-common curl

curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt update && sudo apt install -y terraform
terraform -version
📁 Step 4: Create Terraform Configuration
Create a file named main.tf:


terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 3.0"
    }
  }
}

provider "docker" {}

resource "docker_image" "nginx_image" {
  name         = "nginx:latest"
  keep_locally = false
}

resource "docker_container" "nginx_container" {
  name  = "nginx_server"
  image = docker_image.nginx_image.image_id
  ports {
    internal = 80
    external = 8080
  }
}
⚙️ Step 5: Deploy Using Terraform
Run the following commands:

sudo terraform init             # Initialize Terraform
sudo terraform plan             # (Optional) Preview the plan
sudo terraform apply            # Apply and deploy container
👉 Type yes when prompted.

✅ Step 6: Verify and Manage

sudo docker ps                 # Check running Docker containers
sudo terraform state list     # View Terraform-managed resources
sudo terraform destroy        # Destroy resources
👉 Type yes when prompted.
