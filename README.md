🚀 QuickShip: Zero-Downtime K8s Migration

A production-grade DevOps project demonstrating the migration of a legacy Python application to AWS EKS using Terraform, ArgoCD, and Blue/Green Deployment strategies.

📋 Project Overview

The Challenge: A critical payment service required zero-downtime updates and instant rollback capabilities.
The Solution: An automated GitOps pipeline that provisions infrastructure, deploys containers, and manages traffic shifting using Argo Rollouts.

🏗️ Architecture

Infrastructure: AWS VPC (Multi-AZ), EKS Cluster (v1.30), Managed Node Groups.

CI/CD: GitHub (Source) -> ArgoCD (Controller) -> EKS (Target).

Deployment Strategy: Blue/Green (Argo Rollouts).

Security: Non-root containers, Private Subnets, Security Group whitelisting.

🛠️ Technologies Used

Infrastructure as Code: Terraform (AWS Provider, VPC Module, EKS Module).

Containerization: Docker (Multi-stage builds for security).

Orchestration: Kubernetes (Manifests & Helm).

GitOps: ArgoCD.

Progressive Delivery: Argo Rollouts (Blue/Green).

📸 Key Highlights

Feature

Description

Status

IaC Foundation

Complete VPC & EKS provisioned via Terraform.

✅

Self-Healing

ArgoCD automatically detects and fixes configuration drift.

✅

Blue/Green

Zero-downtime updates with "Preview" environments.

✅

Observability

Visualized rollout status via Argo Console.

✅

🚀 How to Run

Prerequisites

AWS Account & CLI configured.

Terraform installed.

Kubectl & Helm installed.

1. Provision Infrastructure

cd infra
terraform init
terraform apply -auto-approve


2. Deploy ArgoCD

helm repo add argo [https://argoproj.github.io/argo-helm](https://argoproj.github.io/argo-helm)
helm install argocd argo/argo-cd --namespace argocd --create-namespace


3. Execute a Rollout

Update the rollout.yaml image tag and push to Git. Watch ArgoCD spin up the "Green" stack before switching traffic.

🐛 Challenges Solved

Dependency Hell: Fixed broken Python dependencies by pinning versions in requirements.txt.

Deprecated APIs: Upgraded EKS module to v1.30 to resolve AWS deprecation errors.

Ingress Timeouts: Diagnosed and fixed AWS Security Group rules to allow Load Balancer traffic.

DevOps Technical Interview Prep

Based on the "QuickShip" Zero-Downtime Project.

Part 1: Technical Interview Questions (Based on your Project)

Q1: "Tell me about a time a deployment failed and how you fixed it."

Your Answer (Based on this project):
"During a GitOps implementation using ArgoCD, I encountered an InvalidImageName error on my pods. It turned out that environment variables (like $ACCOUNT_ID) used in my local generation script didn't expand correctly in the YAML manifest.
The Fix: I adopted a GitOps mindset. Instead of patching it in the cluster, I corrected the manifest in the Git repository with the explicit ECR URL. ArgoCD's self-healing mechanism detected the Git commit and automatically synced the cluster to the correct state."

Q2: "Why did you use Argo Rollouts instead of a standard Kubernetes Deployment?"

Your Answer:
"Standard Kubernetes Deployments use a 'Rolling Update' strategy. While this is good, it replaces pods incrementally. If the new version has a bug that only appears after startup, users might experience errors before we can stop it.
With Argo Rollouts (Blue/Green), I can spin up the entire new version (Green) in the background. I can verify it on a private 'Preview' service before any real user traffic touches it. Once verified, I promote it, and the traffic switch is atomic. It guarantees zero downtime."

Q3: "You faced a Timeout error on your Load Balancer. How did you debug it?"

Your Answer:
"I checked the pod status first; they were running, so it wasn't an app crash. I then checked the Service definition, which looked correct. Since the request was timing out (hanging) rather than being rejected immediately, I suspected a firewall issue. I checked the AWS Security Groups for the Worker Nodes and realized they didn't allow traffic from the Load Balancer's port range. I authorized the ingress rule, and traffic started flowing immediately."

Part 2: Deep Dive Concepts (The 3 Questions)

1. How does ArgoCD really work under the hood?

ArgoCD is a Kubernetes Controller.

The Compare Loop: Every 3 minutes (default), it pulls the Desired State from your Git Repo (YAML).

The Cluster State: It queries the Kubernetes API Server for the Live State (what is actually running).

The Diff: It compares the two. If they don't match (e.g., Image v1 vs Image v2), it marks the app as OutOfSync.

The Sync: When you click "Sync" (or if Auto-Sync is on), ArgoCD applies the YAML from Git to the Cluster, forcing Kubernetes to match Git. This is why we call Git the "Single Source of Truth."

2. What is the role of the "Preview Service" in Blue/Green?

In a standard K8s Service, the selector points to any pod with the label app=myapp.
In an Argo Rollout:

The Active Service is modified dynamically to point only to the ReplicaSet of the old version (Blue).

The Preview Service is modified dynamically to point only to the ReplicaSet of the new version (Green).
This allows you to have two different URLs hitting two different versions of the code simultaneously, even though they live in the same cluster.

3. Scenario: Monitoring (What comes next?)

If we were to extend this project to Module 6 (Monitoring), we would:

Install Prometheus: To scrape metrics from our EKS nodes (CPU, Memory) and our Flask App (Request count, Latency).

Install Grafana: To visualize these metrics.

The "Golden Signals" Dashboard: We would build a dashboard showing:

Latency: Time it takes to serve a request.

Traffic: Requests per second.

Errors: Rate of 500 errors.

Saturation: How "full" your service is (Memory/CPU usage).

👨‍💻 Author

Rajkumar DevOps Project Portfolio
