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

## Creating ECR repositories

1. Create ECR for frontend and push image
2. Create ECR for backend and push image 

## Creating EKS Cluster

```bash
eksctl create cluster --name eks-three-tier-cluster --region us-east-1 --node-type t2.medium --nodes-min 2 --nodes-max 2
aws eks update-kubeconfig --region us-east-1 --name three-tier-cluster
kubectl get nodes
```
## Deploying the Kubernetes mainifest files

#### Creating secret for private image ecr

```bash
    kubectl create secret generic ecrsecret \
    --from-file=.dockerconfigjson=/home/ubuntu/.docker/config.json \
    --type=kubernetes.io/dockerconfigjson -n three-tier
```
#### deploying all mainifest
  1. deploy all backednd
  2. deploying all database
  3. deploying all front end

## Installing AWS Load Balancer

1. Creating policy

```bash
    curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/a2e02479398b9f637c978db5081bb1775d65798b/docs/install/iam_policy.json
    aws iam create-policy --policy-name AWSLoadBalancerControllerIAMPolicy --policy-document file://iam_policy.json
```
2. attaching policy to cluster via util
```bash
    eksctl utils associate-iam-oidc-provider --region=us-east-1 --cluster=eks-three-tier-cluster --approve
```
3. Creating Service Account (allowing ALB to EKS Cluster)
```bash
    eksctl create iamserviceaccount --cluster=eks-three-tier-cluster --namespace=kube-system --name=aws-load-balancer-controller --role-name AmazonEKSLoadBalancerControllerRole --attach-policy-arn=arn:aws:iam::646535719493:policy/AWSLoadBalancerControllerIAMPolicy --approve --region=us-east-1
```

## Deploying AWS Load Balancer Controller via HELM

```bash
sudo snap install helm --classic
helm repo add eks https://aws.github.io/eks-charts
helm repo update eks
```
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=eks-three-tier-cluster --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller

#### Verify the Install Services
```bash
kubectl get deployment -n kube-system aws-load-balancer-controller
```
#### Delete
eksctl delete cluster --name eks-three-tier-cluster --region us-east-1
