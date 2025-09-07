# 🚀 Ultimate Corporate CI/CD Pipeline for Kubernetes

A comprehensive, enterprise-grade DevOps pipeline implementing CI, CD, and DevSecOps best practices to automate the deployment of a containerized application onto a highly available Kubernetes cluster.


## 📖 Table of Contents
- [Architecture](#-architecture-overview)
- [Features](#-key-features--devsecops-integration)
- [Tech Stack](#-technology-stack)
- [Prerequisites](#-prerequisites)
- [Setup & Installation](#-step-by-step-setup--installation)
- [Pipeline Stages](#-pipeline-stages-breakdown)
- [Project Structure](#-project-structure)
- [Accessing Applications](#-accessing-the-application)
- [Troubleshooting](#-troubleshooting--common-issues)
- [License](#-license)

---

## 🏗️ Architecture Overview

The following diagram illustrates the end-to-end flow of the CI/CD process:


**Flow Explanation:**
1.  A developer pushes code to the **GitHub** repository.
2.  This triggers a webhook to the **Jenkins** server.
3.  Jenkins executes the pipeline defined in the `Jenkinsfile`, spinning up a dynamic agent.
4.  The pipeline progresses through stages for code analysis, building, scanning, and storing artifacts.
5.  The final stage deploys the validated application to the **Kubernetes cluster**.
6.  Notifications are sent via email upon success or failure.

---

## ✨ Key Features & DevSecOps Integration

- **🔐 Shift-Left Security:** Integrated security scanning early in the pipeline.
  - **SonarQube:** Static Application Security Testing (SAST) for source code analysis.
  - **Trivy:** Container scanning for vulnerabilities (CVEs) in Docker images.
- **📦 Artifact Management:** Uses **Nexus Repository** as a single source of truth for versioned Docker images, ensuring reproducibility and traceability.
- **☸️ Kubernetes-Native Deployment:** Automated rolling deployments to a multi-node (1 Master, 2 Worker) K3s cluster for high availability.
- **📧 Notifications:** Instant email alerts with build status for rapid feedback.

---

## 🛠️ Technology Stack

| Category       | Tools                                                                                           |
|----------------|-------------------------------------------------------------------------------------------------|
| **SCM**        | GitHub                                                                                          |
| **CI/CD**      | Jenkins                                                                                         |
| **Build**      | Docker                                                                                          |
| **Code Quality** | SonarQube                                                                                       |
| **Security**   | Trivy                                                                                           |
| **Artifact Repo** | Sonatype Nexus                                                                                  |
| **Orchestration** | Kubernetes (kubeadm)                                                                           |
| **Infrastructure** | VirtualBox, Ubuntu 20.04 LTS                                                                   |
| **Notification** | Jenkins Email Extension Plugin                                                                 |

---

## ✅ Prerequisites

**Software Requirements:**
- **VirtualBox/VMware:** For provisioning virtual machines.
- **Vagrant (Optional):** For automated VM provisioning.

**Hardware Requirements:**
- Minimum **8GB RAM** (16GB Recommended)
- **4 vCPUs**
- **50GB** Free Disk Space

**VM Specifications:**
- **Jenkins Server:** 1 vCPU, 2GB RAM, 20GB Disk
- **Kubernetes Master:** 2 vCPUs, 2GB RAM, 20GB Disk
- **Kubernetes Worker 1:** 1 vCPU, 2GB RAM, 20GB Disk
- **Kubernetes Worker 2:** 1 vCPU, 2GB RAM, 20GB Disk

---

## 🔧 Step-by-Step Setup & Installation

### 1. Infrastructure Provisioning
Provision four Ubuntu 20.04 VMs as per the specifications above. Ensure they can communicate over the network.

### 2. Kubernetes Cluster Setup (on Master & Worker Nodes)
```bash
# On all nodes: Install Docker, kubeadm, kubelet, kubectl
sudo apt-get update && sudo apt-get install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker

sudo apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
sudo apt-get update && sudo apt-get install -y kubeadm kubelet kubectl
sudo apt-mark hold kubeadm kubelet kubectl

# On Master Node Only:
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# Install a Pod Network (Weave Net)
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"


# On Worker Nodes:
# Use the kubeadm join command generated from the master's init output.
sudo kubeadm join <master-ip>:6443 --token <token> --discovery-token-ca-cert-hash <hash>


## Jenkins Setup
# On Jenkins Server
- java -jar jenkins.war --httpPort=8080
- Install Plugins: Navigate to Manage Jenkins > Plugins Manager and install: Docker Pipeline, SonarQube Scanner, Email Extension, Kubernetes CLI, Blue Ocean.

- Configure Tools: In Global Tool Configuration, set paths for Docker, JDK, SonarQube Scanner, and Kubectl.

- Add Credentials: Add credentials for Docker Hub, Nexus, and Kubernetes config file in the Jenkins credential store.

## Deploy Supporting Tools (Nexus, SonarQube)
- Deploy these on the Jenkins server or a separate VM using Docker.
# Example: Run SonarQube
- docker run -d --name sonarqube -p 9000:9000 sonarqube:lts-community
## ⚙️ Pipeline Stages Breakdown
- The pipeline is defined in the Jenkinsfile and consists of the following stages:

- Checkout SCM: Clones the source code from GitHub.

- SonarQube Analysis: Performs static code analysis and fails the build if quality gates are not met.

- Build Docker Image: Builds the Docker image, tagging it with the BUILD_ID.

- Trivy Vulnerability Scan: Scans the Docker image for CVEs. The build fails on critical vulnerabilities.

- Push to Nexus: Tags the image as RELEASE and pushes it to the Nexus Docker repository.

- Deploy to Kubernetes: Applies the Kubernetes manifests, which pull the image from Nexus and deploy it to the cluster.

- Email Notification: Sends a build status report to the configured email ID.


## 🌐 Accessing the Application
- After a successful pipeline run, the application is deployed in the Kubernetes cluster. To access it:

- Get the NodePort of your service:

- kubectl get svc -n <namespace>
- Open your browser and navigate to: http://<worker-node-ip>:<node-port>

## 🐛 Troubleshooting & Common Issues
- "ImagePullBackOff" Error: Ensure your Nexus credentials are correct in the Kubernetes secret and that the image tag exists in Nexus.

- Kubernetes Master Not Ready: Often due to a missing pod network. Confirm Weave Net pods are running with kubectl get pods -n kube-system.

- Jenkins Agent Lacks Permissions: Ensure the user running the Jenkins agent is part of the docker group to run Docker commands (sudo usermod -aG docker jenkins).

- SonarQube Scanner Failing: Check the SonarQube server URL and token in the Jenkins credentials.


## 👨‍💻 Author
- Your Name
- GitHub: https://github.com/Rajeswararao89
- LinkedIn: https://www.linkedin.com/in/rajeswararao-jangiti/

## 💡 Acknowledgments: This project was inspired by the need to demonstrate real-world corporate DevOps and DevSecOps practices using open-source tools.











