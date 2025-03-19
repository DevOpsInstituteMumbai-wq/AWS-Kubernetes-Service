# AWS Kubernetes (EKS) Project: Deploy a Node.js App

This project sets up an AWS EKS cluster and deploys a simple Node.js application using Kubernetes.

## Project Structure
```
aws-eks-project/
│── src/
│   ├── app.js
│   ├── package.json
│── Dockerfile
│── k8s/
│   ├── deployment.yaml
│   ├── service.yaml
│── terraform/
│   ├── main.tf
│   ├── variables.tf
│   ├── outputs.tf
│── README.md
```

## Prerequisites
- AWS CLI installed & configured (`aws configure`)
- kubectl installed (`brew install kubectl` or `apt install kubectl`)
- Terraform installed (`brew install terraform`)
- Docker installed (`docker -v`)

---

## Step 1: Set Up AWS EKS with Terraform
Navigate to the `terraform/` directory and apply the Terraform script:
```sh
cd terraform
terraform init
terraform apply -auto-approve
```
This will create:
- An AWS EKS cluster
- A node group
- IAM roles

---

## Step 2: Configure kubectl
Retrieve the kubeconfig:
```sh
aws eks update-kubeconfig --name my-eks-cluster --region us-east-1
kubectl get nodes  # Verify cluster
```

---

## Step 3: Build & Push Docker Image
```sh
cd src
# Build Docker image
docker build -t my-node-app .

# Push to AWS Elastic Container Registry (ECR)
aws ecr create-repository --repository-name my-node-app
aws ecr get-login-password | docker login --username AWS --password-stdin <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com

docker tag my-node-app:latest <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/my-node-app:latest
docker push <AWS_ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/my-node-app:latest
```

---

## Step 4: Deploy Application to Kubernetes
Apply the Kubernetes manifests:
```sh
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
```
Check the deployment:
```sh
kubectl get pods
kubectl get svc
```

---

## Step 5: Access the Application
Find the external LoadBalancer IP:
```sh
kubectl get svc my-node-app-service
```
Open in browser:
```
http://<EXTERNAL-IP>:3000
```

---

## Cleanup
To delete the cluster:
```sh
cd terraform
terraform destroy -auto-approve
```

---

## Conclusion
You have successfully deployed a Node.js app on AWS EKS! 🚀
