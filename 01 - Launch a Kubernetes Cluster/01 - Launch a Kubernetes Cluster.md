# 🚀 Launch a Kubernetes Cluster on Amazon EKS

<div align="center">

[![NextWork](https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg)](http://learn.nextwork.org/projects/aws-compute-eks1)

**Project Link:** [View Project on NextWork](http://learn.nextwork.org/projects/aws-compute-eks1)

---

**Author:** Ngurah Gede Wisnu Gudakesa  
📧 **Email:** ngurahgedewisnugk@gmail.com

</div>

---

## 📋 Table of Contents

- [Overview](#overview)
- [What is Kubernetes?](#what-is-kubernetes)
- [What is Amazon EKS?](#what-is-amazon-eks)
- [Cluster Setup with eksctl](#cluster-setup-with-eksctl)
- [eksctl and CloudFormation](#eksctl-and-cloudformation)
- [The EKS Console](#the-eks-console)
- [Extra: Deleting Nodes](#extra-deleting-nodes)
- [Key Takeaways](#key-takeaways)

---

## 🎯 Overview

In this project, I deployed my first Kubernetes cluster on **Amazon EKS**. I learned how `eksctl` automates resource creation through CloudFormation and how to operate the cluster effectively.

<div align="center">

![EKS Cluster](http://learn.nextwork.org/motivated_teal_shy_hog/uploads/aws-compute-eks1_e5f6g7h8)

</div>

---

## 🐳 What is Kubernetes?

**Kubernetes** is a container orchestration platform that coordinates and manages multiple containers across servers. It provides several key benefits:

- ✅ Ensures containers run smoothly
- 📈 Scales them automatically to meet demand
- 🔄 Restarts them if they crash
- ⏱️ All without causing downtime

### Why Use Kubernetes?

Companies and developers use Kubernetes because it's the **standard tool** for:
- Keeping large, container-based applications steady
- Making applications easy to scale with traffic
- Automating complex manual tasks of managing many containers

---

## ☁️ What is Amazon EKS?

**Amazon Elastic Kubernetes Service (EKS)** is a managed Kubernetes service that makes it easy to run Kubernetes on AWS without needing to install, operate, and maintain your own Kubernetes control plane.

---

## ⚙️ Cluster Setup with eksctl

### Configuration Details

I used `eksctl` to set up an EKS cluster with the following specifications:

| Parameter | Value |
|-----------|-------|
| **Cluster Name** | `nextwork-eks-cluster` |
| **Node Group** | `nextwork-nodegroup` |
| **Instance Type** | `t3.micro` |
| **Initial Nodes** | 3 |
| **Min Nodes** | 1 |
| **Max Nodes** | 3 |
| **Kubernetes Version** | 1.33 |

### 🐛 Troubleshooting

#### Issue #1: Missing eksctl
**Error:** The initial error occurred because the `eksctl` command-line tool was not yet installed on the EC2 instance.

<div align="center">

![Error Resolution](http://learn.nextwork.org/motivated_teal_shy_hog/uploads/aws-compute-eks1_ff9bfc221)

</div>

**Solutions:** install `eksctl` command-line tool on the EC2 Instance.

![alt text](<assets/install eksctl.png>)

#### Issue #2: IAM Permissions
**Error:** The EC2 instance lacked the necessary IAM role and permissions to create an EKS cluster.

![#arn iam permissions.png](<assets/arn iam permissions.png>)

**Solutions:** grant IAM permission `AdministratorAccess` and attaching to IAM Role For EC2 Instances.  

---

## 🏗️ eksctl and CloudFormation

### How CloudFormation Powers EKS

CloudFormation helped create my EKS cluster because `eksctl` uses CloudFormation under the hood to set up resources according to `eksctl` instructions.

#### Stack 1: Core EKS Cluster
Created essential VPC networking resources:
- 🌐 VPC (Virtual Private Cloud)
- 🔌 Subnets
- 🚪 Internet Gateways
- 🔒 Security Groups

This ensures containers can communicate and access the internet securely while maintaining privacy.
![Stack 1: Core EKS Cluster](<assets/core EKS Cluster.png>)

#### Stack 2: Node Group
A separate CloudFormation stack for the node group (collection of EC2 instances that run containers).
![node group](<assets/node group.png>)

> **Why separate stacks?** CloudFormation separates the core EKS cluster stack from the node group stack to make it easier to manage and troubleshoot each part independently.

### 🤔 Cluster vs Node Group

| Concept | Description |
|---------|-------------|
| **Kubernetes Cluster** | The entire environment that manages containerized applications. Includes nodes (servers running containers) and the control plane (the "brain" deciding how and where containers run). |
| **Node Group** | A way to organize multiple nodes within your cluster. Allows you to manage settings like instance type and resource limits for a group of nodes together, making it simpler to scale and configure. |

<div align="center">

![CloudFormation Stacks](http://learn.nextwork.org/motivated_teal_shy_hog/uploads/aws-compute-eks1_w3e4r5t6)

</div>

---

## 🖥️ The EKS Console

### Setting Up IAM Access Entry

I set up an IAM access entry because **AWS and Kubernetes have separate permission systems:**

- **AWS:** Uses IAM for authentication
- **Kubernetes:** Uses RBAC (Role-Based Access Control)

The access entry acts like a **"handshake"** to connect my AWS IAM identity with Kubernetes' RBAC, allowing the cluster to recognize my permissions and grant me access to the nodes.

![alt text](<assets/access entries.png>)

### Configuration Steps

1. ✅ Selected **Standard** type for account permission
2. ✅ Granted **AmazonEKSClusterAdminPolicy**
3. ✅ Refreshed console to view nodes

This policy gave my account administrative rights to see and control the nodes within the cluster.

### ⏱️ Deployment Time

**Total time:** ~45 minutes

While this still takes time, it's **significantly faster and more consistent** than manual setup. Using `eksctl` with CloudFormation as an Infrastructure as Code (IaC) template helps:
- 🤖 Automate resource creation
- 📊 Standardize deployments
- 🚀 Make future deployments much more efficient

<div align="center">

![EKS Console](http://learn.nextwork.org/motivated_teal_shy_hog/uploads/aws-compute-eks1_e5f6g7h8)

</div>

---

## 🔧 Extra: Deleting Nodes

### Finding Nodes in EC2 Console

I can find my Amazon EKS cluster's nodes in the **Amazon EC2 console** because EKS uses EC2 instances as the underlying infrastructure for worker nodes. These EC2 instances are where my Kubernetes pods (which run my containers) are actually scheduled and executed.

![Finding Nodes](<assets/Finding Nodes in EC2 Console.png>)

### Understanding Node Scaling

| Parameter | Purpose |
|-----------|---------|
| **Desired Size** | Target number of nodes the cluster aims to maintain |
| **Minimum Size** | Lower boundary for automatic scaling |
| **Maximum Size** | Upper boundary for automatic scaling |

### Why This Matters

These parameters are crucial for ensuring:
- 🎯 **High Availability:** Cluster can scale based on demand
- 💪 **Resilience:** Automatically regenerates failed nodes
- 📈 **Performance:** Applications remain available during traffic spikes

### 🔄 Auto-Healing in Action

When I terminated the EC2 instances, Kubernetes automatically:
1. Detected the change
2. Launched new nodes to maintain the desired state
3. Ensured application continued running

This demonstrates Kubernetes' **built-in resilience and high availability**, ensuring applications remain operational and reducing downtime.

<div align="center">

![Node Regeneration](http://learn.nextwork.org/motivated_teal_shy_hog/uploads/aws-compute-eks1_q7r8s9t0)

</div>

---

## 🎓 Key Takeaways

| Lesson | Description |
|--------|-------------|
| 🐳 **Container Orchestration** | Kubernetes automates container management at scale |
| ☁️ **Managed Services** | EKS simplifies Kubernetes operations on AWS |
| 🤖 **Infrastructure as Code** | eksctl + CloudFormation enables repeatable deployments |
| 🔐 **Permission Integration** | IAM access entries bridge AWS and Kubernetes security |
| 💪 **Self-Healing** | Kubernetes automatically maintains desired cluster state |
| 📈 **Auto-Scaling** | Dynamic node scaling ensures availability and cost optimization |