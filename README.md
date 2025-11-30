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

👨‍💻 Author

Rajkumar DevOps Project Portfolio
