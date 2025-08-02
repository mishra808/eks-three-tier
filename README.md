# Three Tier App (Backend-Node, Front End- React, Database MongoDB)

## Overview
it is simple three tier app that will deploy AWS EKS with AWS ECR. 

## Project Details
🛠️ **Tools Explored:**
- Terraform & AWS CLI for AWS infrastructure
- Kubectl and eksctl

# Getting Started

## Step AWS Prerequisites Dependencies 
1. Install AWS CLI
```bash 
sudo apt update
sudo apt install -y unzip curl
```
```bash 
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
```
```bash 
sudo ./aws/install
```
```bash
aws --version
```
2. Installing Docker
   #### Add Docker's official GPG key:
```bash
        sudo apt-get update
        sudo apt-get install ca-certificates curl
        sudo install -m 0755 -d /etc/apt/keyrings
        sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
        sudo chmod a+r /etc/apt/keyrings/docker.asc
```

    Add the repository to Apt sources:
```bash
        echo \
        "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
        $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
        sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```
```bash
sudo apt install docker.io -y
```
#### Adding Permission

```bash
sudo usermod -aG docker $USER && newgrp docker
```
#### Configure AWS Access key and Scret key
```bash
aws configure
```

3. Installing Kubectl
    #### Kubernetes 1.33 (https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html#linux_amd64_kubectl)
    ```bash
    curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.33.0/2025-05-01/bin/darwin/amd64/kubectl

    curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.33.0/2025-05-01/bin/darwin/amd64/kubectl.sha256

    chmod +x ./kubectl

    mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$HOME/bin:$PATH

    echo 'export PATH=$HOME/bin:$PATH' >> ~/.bash_profile
    ```

4. Installing eksctl

    ```bash
    curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
    sudo mv /tmp/eksctl /usr/local/bin
    eksctl version
    ```

