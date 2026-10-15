# Hangar — A DevSecOps Project

A full-stack application deployed on AWS EKS with a complete **DevSecOps** workflow — Infrastructure as Code, containerization, Kubernetes orchestration, **security integrated at every stage**, CI/CD pipelines, and GitOps.

![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazonaws&logoColor=white)
![Terraform](https://img.shields.io/badge/Terraform-7B42BC?style=for-the-badge&logo=terraform&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-2088FF?style=for-the-badge&logo=githubactions&logoColor=white)
![ArgoCD](https://img.shields.io/badge/ArgoCD-EF7B4D?style=for-the-badge&logo=argo&logoColor=white)
![Trivy](https://img.shields.io/badge/Trivy-1904DA?style=for-the-badge&logo=aquasec&logoColor=white)
![Checkov](https://img.shields.io/badge/Checkov-3B82F6?style=for-the-badge&logo=bridgecrew&logoColor=white)

---

## 📖 What is Hangar?

**Hangar** is a hands-on, end-to-end **DevSecOps** project. It takes a full-stack application (frontend + backend + database) and walks through the entire journey of getting it into production on AWS — with **security baked into every phase**, not bolted on at the end.

The project follows the **DevSecOps principle of "shift-left security"** — vulnerabilities, misconfigurations, and policy violations are caught as early as possible in the pipeline, before they ever reach production.

You will:

1. Provision cloud infrastructure (VPC + EKS) with **Terraform** — scanned with **Checkov**
2. Package the apps into **Docker** images — scanned with **Trivy**
3. Deploy them onto **Kubernetes (EKS)** using hardened manifests
4. Automate builds, scanning & policy enforcement with **GitHub Actions**
5. Deliver changes automatically with **ArgoCD (GitOps)**

If you are learning DevSecOps, this repo is a complete reference you can clone, run, and learn from.

---

## 🔐 DevSecOps Mindset — Security at Every Stage

Security is not a single step — it is woven into **every phase** of this project:

| Phase | Security Practice | Tool |
|---|---|---|
| **Code** | Static code analysis (SAST) | Linters |
| **Dependencies** | Software Composition Analysis (SCA) | SCA scanners |
| **Container Build** | Vulnerability scanning of images | **Trivy** |
| **IaC** | Misconfiguration scanning | **Checkov** |
| **K8s Manifests** | Policy & misconfig scanning | **Checkov / Kubesec** |
| **Runtime (K8s)** | Network segmentation | **NetworkPolicies** |
| **Secrets** | Kubernetes Secrets + restricted RBAC | Native K8s |
| **Deployment** | GitOps (auditable, declarative) | **ArgoCD** |

> 💡 **Shift-Left:** Every security check runs **inside the CI pipeline**, so developers get feedback within minutes — not weeks later in production.

---

## 🏗️ Architecture

```
                           ┌───────────────────────┐
                           │      Internet         │
                           └───────────┬───────────┘
                                       │
                           ┌───────────▼───────────┐
                           │  AWS LoadBalancer     │
                           └───────────┬───────────┘
                                       │
        ┌──────────────────────────────┼──────────────────────────────┐
        │                        AWS EKS Cluster                       │
        │                                                              │
        │   ┌──────────────┐      ┌──────────────┐      ┌──────────┐  │
        │   │  Frontend    │─────▶│   Backend    │─────▶│ Database │  │
        │   │ Deployment   │      │ Deployment   │      │Deployment│  │
        │   │ + Service    │      │ + Service    │      │+Svc+PVC+ │  │
        │   │              │      │ + Secret     │      │StorageCls│  │
        │   │              │      │ + NetworkPol │      │+NetPol   │  │
        │   └──────────────┘      └──────────────┘      └──────────┘  │
        │                                                              │
        └──────────────────────────────────────────────────────────────┘
                                       ▲
                                       │
                        ┌──────────────┴───────────────┐
                        │   Terraform (VPC + EKS)      │
                        │   Provisioned on AWS         │
                        └──────────────────────────────┘
```

**Secure Build & Deploy Flow:**

```
Developer → GitHub → GitHub Actions (CI + Security Scans) → GHCR → ArgoCD → EKS
                          │
                          ├─ Lint (SAST)
                          ├─ SCA
                          ├─ Build
                          ├─ Trivy (image scan)
                          ├─ Checkov (IaC & K8s scan)
                          ├─ Update K8s manifests
                          └─ Commit
```

---

## 🧰 Tech Stack

| Category | Tool | Purpose |
|---|---|---|
| **Cloud** | AWS (EC2, VPC, EKS, EBS) | Hosting & infrastructure |
| **IaC** | Terraform | Provision VPC + EKS cluster |
| **Containers** | Docker, Docker Compose | Package & run apps locally |
| **Orchestration** | Kubernetes (EKS), kubectl, eksctl | Run containers at scale |
| **CI** | GitHub Actions | Lint, scan, build, push images |
| **CD (GitOps)** | ArgoCD | Declarative, auditable deployments |
| **Registry** | GitHub Container Registry (GHCR) | Store Docker images |
| **Image Scanning** | Trivy | Detect CVEs in containers |
| **IaC Scanning** | Checkov | Detect misconfigs in Terraform & K8s |

---

## 📁 Repository Structure

```
Hangar/
├── deploy/                     # Helper scripts
│   └── setup.sh                # Installs prerequisite packages
├── terraform/                  # IaC: VPC + EKS cluster (scanned by Checkov)
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
├── frontend/                   # Frontend app
│   └── Dockerfile
├── backend/                    # Backend app
│   └── Dockerfile
├── k8s/                        # Kubernetes manifests
│   ├── namespace.yaml
│   ├── secrets.yaml
│   ├── storageclass.yaml       # EBS StorageClass
│   ├── pvc.yaml                # PVC for database
│   ├── database/               # DB deployment + service + netpol
│   ├── backend/                # Backend deployment + service + netpol
│   ├── frontend/               # Frontend deployment + service
│   └── network-policies/       # All NetworkPolicies
├── docker-compose.yml          # Local dev: run frontend+backend
├── .github/workflows/          # CI pipeline (with security stages)
└── README.md
```

---

## ✅ Prerequisites

Before starting, make sure you have:

| Requirement | Why |
|---|---|
| **AWS Account** | To create EC2, VPC, EKS, EBS |
| **AWS Access Key + Secret Key** | For programmatic access to AWS |
| **Ubuntu EC2 instance** (t3.medium or larger) | Acts as your control/jump machine |
| **Git installed** | To clone the repo |
| **Basic Linux & Docker knowledge** | Helps understand the commands |
| **GitHub account** | For CI pipeline, GHCR, and GitOps |

> ⚠️ **Cost Warning:** EKS + EC2 + NAT gateways incur charges. Always run the [Cleanup](#-cleanup) steps when done.

---

## 🚀 Step-by-Step Implementation

Follow these steps **in order**. Each stage builds on the previous one.

---

### 🔹 Step 1 — Build Locally

**Goal:** Prepare an EC2 machine with all required tooling and clone the project.

```bash
# 1. Create an Ubuntu EC2 instance and SSH into it
ssh -i <your-key>.pem ubuntu@<EC2-PUBLIC-IP>

# 2. Update the system
sudo apt update && sudo apt upgrade -y

# 3. Clone your fork of the repo
git clone https://github.com/moazzam75/Hangar.git
cd Hangar

# 4. Run the setup script (installs prerequisite packages)
cd deploy
chmod +x setup.sh
./setup.sh
```

---

### 🔹 Step 2 — Setup Infrastructure on AWS

**Goal:** Configure AWS access, install IaC/CLI tools, and provision a VPC + EKS cluster with Terraform.

#### 2.1 Install & Configure AWS CLI

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

# Configure credentials
aws configure
```

You will be prompted for:

| Prompt | What to enter |
|---|---|
| AWS Access Key ID | Your access key |
| AWS Secret Access Key | Your secret key |
| Default region name | `us-east-1` (or your preferred region) |
| Default output format | `json` |

Verify:

```bash
aws sts get-caller-identity
```

> 🔐 **Security Tip:** Never commit AWS credentials to Git. Use IAM roles or environment variables in CI.

#### 2.2 Install kubectl

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.37/deb/Release.key | \
  sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

sudo chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.37/deb/ /' | \
  sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo chmod 644 /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl
```

#### 2.3 Install eksctl

```bash
ARCH=amd64
PLATFORM=$(uname -s)_$ARCH
curl -sLO "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$PLATFORM.tar.gz"
curl -sL "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_checksums.txt" | grep $PLATFORM | sha256sum --check
tar -xzf eksctl_$PLATFORM.tar.gz -C /tmp && rm eksctl_$PLATFORM.tar.gz
sudo install -m 0755 /tmp/eksctl /usr/local/bin && rm /tmp/eksctl
```

#### 2.4 Install Terraform

```bash
wget -O - https://apt.releases.hashicorp.com/gpg | \
  sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com $(grep -oP '(?<=UBUNTU_CODENAME=).*' /etc/os-release || lsb_release -cs) main" | \
  sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt update && sudo apt install -y terraform
```

#### 2.5 Provision VPC & EKS with Terraform

```bash
cd terraform
terraform init        # Downloads providers & modules
terraform plan        # Preview changes
terraform apply -auto-approve
```

This creates:
- A dedicated **VPC** with public + private subnets
- An **EKS cluster** named `hangar-eks`
- A **managed node group** for worker nodes

#### 2.6 Connect kubectl to the Cluster

```bash
aws eks update-kubeconfig --name hangar-eks --region us-east-1
kubectl get nodes
```

You should see your worker nodes in `Ready` state.

---

### 🔹 Step 3 — Dockerize Frontend & Backend

**Goal:** Install Docker, create Dockerfiles, build images, and optionally run locally with Compose.

#### 3.1 Install Docker

```bash
sudo apt update
sudo apt install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Optional — allow running Docker without `sudo`:

```bash
sudo usermod -aG docker $USER && newgrp docker
```

#### 3.2 Build Docker Images

Each service has its own `Dockerfile`:

```bash
# Backend image
cd backend
docker build -t hangar-backend:latest .

# Frontend image
cd ../frontend
docker build -t hangar-frontend:latest .
```

> 🔐 **DevSecOps Tip:** Use **multi-stage builds** and **distroless / slim base images** to reduce the attack surface. Scan every image with Trivy before pushing.

#### 3.3 Run Locally with Docker Compose

The `docker-compose.yml` at the repo root runs both services:

```bash
cd ..
docker compose up -d
docker compose ps
```

---

### 🔹 Step 4 — Deploy to Kubernetes

**Goal:** Create all necessary Kubernetes resources in the correct order, with security best practices applied.

#### 4.1 What Gets Created

| Component | Resources |
|---|---|
| **Frontend** | Deployment, Service (LoadBalancer) |
| **Backend** | Deployment, Service, Secret, NetworkPolicy |
| **Database** | Deployment, Service, StorageClass (EBS), PVC, NetworkPolicy |

#### 4.2 Apply Manifests in Order

```bash
# 1. Namespace
kubectl apply -f k8s/namespace.yaml

# 2. Secrets (DB credentials, app secrets)
kubectl apply -f k8s/secrets.yaml

# 3. StorageClass (EBS) + PVC for the database
kubectl apply -f k8s/storageclass.yaml
kubectl apply -f k8s/pvc.yaml

# 4. Database (deployment + service + netpol)
kubectl apply -f k8s/database/

# 5. Backend (deployment + service + netpol)
kubectl apply -f k8s/backend/

# 6. Frontend (deployment + service)
kubectl apply -f k8s/frontend/

# 7. Network Policies
kubectl apply -f k8s/network-policies/
```

#### 4.3 Verify the Deployment

```bash
kubectl get all -n <namespace>
kubectl get pvc -n <namespace>
kubectl get netpol -n <namespace>
```

#### 4.4 Access the Application

```bash
kubectl get svc -n <namespace>
```

Copy the **EXTERNAL-IP** (LoadBalancer DNS) of the frontend service and open it in your browser:

```
http://<LOADBALANCER-DNS>
```

#### 🔐 Kubernetes Security Practices Used

| Practice | Why It Matters |
|---|---|
| **NetworkPolicies** | Restrict traffic between pods (only frontend → backend → DB) |
| **Kubernetes Secrets** | Keep credentials out of plain manifests |
| **Resource Limits** | Prevent noisy-neighbor & DoS-style issues |
| **Non-root containers** (where applicable) | Reduce blast radius of compromise |
| **Read-only root FS** (where applicable) | Harden container runtime |
| **Namespace isolation** | Logical separation of resources |

---

### 🔹 Step 5 — CI/CD with Security Gates

**Goal:** Automate building, **scanning**, and deploying the app via GitHub Actions + ArgoCD.

#### 5.1 GitHub Actions (CI Pipeline)

Located in `.github/workflows/`, the pipeline runs on every push and includes **security gates that block deployment on failure**:

| Stage | What It Does | Security Role |
|---|---|---|
| **Lint** | Code style & syntax checks | SAST — catches issues early |
| **SCA** | Software Composition Analysis | Detects vulnerable dependencies |
| **Build** | Builds Docker images | — |
| **Scanning (Trivy)** | Image vulnerability scan | Blocks critical CVEs |
| **Checkov** | IaC + K8s manifest scan | Detects misconfigurations |
| **Update K8s** | Updates image tags in manifests | — |
| **Commit** | Pushes updated manifests back | Auditable Git history |

> **Note:** Docker images are pushed to **GitHub Container Registry (GHCR)**. Each stage runs separately so failures are easy to spot — and **the pipeline fails fast** on security issues.

#### 🔐 Why These Security Stages Matter

- **Lint / SAST** → catches bugs and insecure code patterns before build
- **SCA** → flags vulnerable open-source dependencies (log4j-style risks)
- **Trivy** → finds CVEs in base images and installed packages
- **Checkov** → catches misconfigs in Terraform (e.g., open S3, wide SG) and K8s (e.g., privileged pods, missing limits)

#### 5.2 ArgoCD (CD — GitOps)

ArgoCD watches the repo and automatically syncs any changes to the EKS cluster. **Every change is declarative and auditable in Git** — a core DevSecOps principle.

**Install ArgoCD:**

```bash
kubectl create namespace argocd
kubectl apply -n argocd --server-side --force-conflicts \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

**Get the initial admin password:**

```bash
kubectl get secrets -n argocd
kubectl edit secret argocd-initial-admin-secret -n argocd
# Copy the password value and decode it:
echo "<base64-password>" | base64 --decode
```

**Access the ArgoCD UI:**

```bash
kubectl get svc -n argocd
kubectl port-forward -n argocd svc/argocd-server 8080:80 --address 0.0.0.0
```

Open `http://<EC2-PUBLIC-IP>:8080` → login with `admin` / decoded password.

**Connect the repo in ArgoCD:**

1. In ArgoCD UI → **New App**
2. Repo URL: `https://github.com/moazzam75/Hangar`
3. Path: `k8s/`
4. Cluster: `https://kubernetes.default.svc`
5. Namespace: your app namespace
6. Enable **Auto-Sync**

Now, any manifest change pushed by CI is automatically deployed.

---

#### 🤔 Why `kubectl port-forward` for ArgoCD?

`port-forward` is used **only for quick access during development/testing** — it tunnels traffic from your local machine (or EC2) to the ArgoCD pod without exposing it publicly. **From a security standpoint, this is the safest default** because nothing is exposed to the internet.

**Other ways to expose ArgoCD:**

| Method | How | Security Notes |
|---|---|---|
| **NodePort** | Change `argocd-server` service to `NodePort` | Exposes on every node IP; use with caution |
| **LoadBalancer** | Change service type to `LoadBalancer` → AWS creates an ELB | Public by default; **enable TLS + restrict CIDRs** |
| **Ingress (recommended)** | Install Ingress Controller (NGINX, ALB) + TLS cert | Production-grade: HTTPS, host routing, WAF-ready |

Example — switch to LoadBalancer (only for testing):

```bash
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
kubectl get svc -n argocd
```

> 🔐 **DevSecOps Tip:** In production, always front ArgoCD with an **Ingress + TLS + SSO (OIDC)** and restrict access by IP allowlist.

---

## 🔄 End-to-End DevSecOps Flow (Summary)

1. Developer pushes code → GitHub.
2. **GitHub Actions** runs the pipeline:
   - Lint (SAST)
   - SCA (dependencies)
   - Build Docker images
   - **Trivy** scans images
   - **Checkov** scans Terraform & K8s manifests
   - On success → push images to **GHCR** and update manifest tags → commit
3. **ArgoCD** detects the manifest change and syncs the EKS cluster.
4. App is live behind an AWS LoadBalancer.
5. **Every step is logged, reproducible, and auditable.**

---

## 🧹 Cleanup (Important — Avoid AWS Charges)

Run these steps when you're done to tear everything down.

```bash
# 1. Delete all Kubernetes resources
kubectl delete -f k8s/ --recursive

# 2. Delete ArgoCD namespace (optional)
kubectl delete namespace argocd

# 3. Destroy all Terraform-managed infra (VPC, EKS, node groups, etc.)
cd terraform
terraform destroy --auto-approve
```

**Double-check in the AWS Console:**

- EC2 instances terminated
- LoadBalancers deleted (EKS sometimes leaves orphan ELBs)
- EBS volumes attached to the DB PVC deleted
- NAT Gateways removed (they cost per hour)

---

## ❓ Troubleshooting

| Problem | Likely Cause / Fix |
|---|---|
| `kubectl get nodes` shows no nodes | Wait 3–5 min after `terraform apply`; node group still joining |
| LoadBalancer EXTERNAL-IP stuck at `<pending>` | Check IAM permissions for AWS LoadBalancer Controller; ensure subnets are public |
| PVC stuck in `Pending` | EBS CSI driver not installed on the EKS cluster |
| ArgoCD login fails | Re-decode secret: `kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" \| base64 -d` |
| Images not updating after push | Ensure ArgoCD auto-sync is enabled and repo is reachable |
| Trivy / Checkov failing the pipeline | Review the report; either fix the issue or (with justification) adjust severity thresholds |

---

## 📝 License

This project is for learning and demonstration purposes. Feel free to fork, modify, and adapt.

---

> ⭐ If this repo helped you learn something new, consider giving it a star!
