
# 1. Introduction

This project is about deploying a simple Flask web app using modern DevOps tools.

We start from local development, move to Docker, then Kubernetes, and finally deploy to AWS EKS with CI/CD automation.

Real-world analogy:
Think of this like building a product in your room (local), packaging it in a box (Docker), sending it to a warehouse (Kubernetes), and then distributing it globally using AWS.

---

# 2. Core Concepts

## Docker

Docker is used to package the application along with all dependencies.

- Image → blueprint
- Container → running instance

We used Docker to:
- Build image
- Push to Docker Hub
- Run container on EC2

---

## Kubernetes

Kubernetes manages containers.

Main components used:

- Deployment → manages pods
- Pod → running container
- Service → exposes app

Types of service:
- NodePort (local testing)
- LoadBalancer (EKS)

---

## Minikube

Used for local Kubernetes testing.

- Runs single-node cluster
- Helps test before production

---

## AWS EKS

Managed Kubernetes service.

- Automatically manages control plane
- Uses EC2 nodes (nodegroup)
- Integrates with LoadBalancer

---

## GitHub Actions (CI/CD)

Automates:

- Build Docker image
- Push to Docker Hub
- Deploy to Kubernetes

Pipeline flow:

Code → GitHub → Build → Push → Deploy → Live app

---

## IAM + RBAC (Important)

GitHub Actions needs permission to access EKS.

We created:

- IAM user
- Access keys
- Mapped user in aws-auth configmap

---

# 3. Important Commands

## Docker

```bash
docker build -t krishna2915/flask-app .
docker push krishna2915/flask-app
docker run -d -p 80:5000 krishna2915/flask-app
```

- build → creates image
- push → uploads to Docker Hub
- run → starts container

---

## Kubernetes (Minikube)

```bash
kubectl create deployment flask-app --image=krishna2915/flask-app
kubectl get pods
kubectl expose deployment flask-app --type=NodePort --port=5000
minikube service flask-app
```

---

## Kubernetes (EKS)

```bash
kubectl create deployment flask-app --image=krishna2915/flask-app
kubectl expose deployment flask-app --type=LoadBalancer --port=80 --target-port=5000
kubectl get svc
```

---

## Scaling

```bash
kubectl scale deployment flask-app --replicas=3
```

---

## Logs

```bash
kubectl logs <pod-name>
```

---

## Restart deployment

```bash
kubectl rollout restart deployment flask-app
```

---

## EKS cluster

```bash
eksctl create cluster --name flask-cluster --region us-east-1
eksctl delete cluster --name flask-cluster --region us-east-1
```

---

# 4. Step-by-Step Project Implementation

## Step 1: Create Flask App

Basic app returning text:

```python
return "Hello from DevOps Flask App"
```

---

## Step 2: Dockerize App

- Create Dockerfile
- Build image
- Push to Docker Hub

---

## Step 3: Run on EC2

- Install Docker
- Pull image
- Run container
- Access via public IP

---

## Step 4: Deploy on Minikube

- Create deployment
- Expose service
- Access via browser

---

## Step 5: Setup EKS

- Create cluster using eksctl
- Configure kubectl
- Verify nodes

---

## Step 6: Deploy on EKS

- Create deployment
- Expose as LoadBalancer
- Get external URL

---

## Step 7: CI/CD Setup

- Create GitHub repo
- Add workflow file
- Add secrets (AWS keys)

Pipeline:

- Build image
- Push image
- Update deployment

---

## Step 8: Update App Version

Change:

```python
return "Hello from DevOps Flask App v2"
```

Push code → pipeline runs → app updates

---

# 5. Problems Faced & Troubleshooting

## Problem: requirements.txt not found

Cause:
File missing in repo

Solution:
Add file or remove install step

---

## Problem: docker ps empty

Cause:
Container not running

Solution:
Check logs or run container properly

---

## Problem: CrashLoopBackOff

Cause:
App crashing inside container

Solution:
Check logs using:

```bash
kubectl logs <pod>
```

---

## Problem: latest image not updating

Cause:
Kubernetes caching image

Solution:

```bash
kubectl rollout restart deployment flask-app
```

Better solution:
Use version tags instead of latest

---

## Problem: EKS access denied

Cause:
IAM user not mapped

Solution:
Edit aws-auth configmap and add:

```yaml
mapUsers:
- userarn: arn:aws:iam::<account-id>:user/github-actions-user
  username: github-actions-user
  groups:
    - system:masters
```

---

## Problem: container name not found

Cause:
Wrong container name in workflow

Solution:
Check using:

```bash
kubectl get deployment flask-app -o yaml
```

---

## Problem: pods pending (EKS)

Cause:
Not enough nodes

Solution:
Scale nodegroup or wait

---

## Problem: LoadBalancer not working

Cause:
Security group or pending status

Solution:
- Wait few minutes
- Check AWS security group (port 80 open)

---

# 6. Mistakes & Things to Remember

- Do not use "latest" tag in production
- Always use versioned images
- Always check logs for debugging
- Wait for LoadBalancer (takes time)
- Clean AWS resources after use
- Ensure IAM permissions are correct
- Container name must match in deployment

---

# 7. Quick Revision Summary

- Build Flask app
- Dockerize app
- Push to Docker Hub
- Deploy on Kubernetes
- Expose using Service
- Setup EKS cluster
- Deploy app to EKS
- Setup CI/CD pipeline
- Automate deployment
- Fix issues using logs and rollout

---

# Final Understanding

This project shows full DevOps lifecycle:

Code → Build → Container → Deploy → Scale → Automate

This is how real-world applications are deployed.
```
