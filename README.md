# 🎬 Hotstar Clone - Kubernetes Deployment with CI/CD

![Hotstar Banner](https://img.shields.io/badge/Disney+-Hotstar-0063e5?style=for-the-badge&logo=disney&logoColor=white)
![Build Status](https://img.shields.io/badge/build-passing-brightgreen?style=for-the-badge)
![License](https://img.shields.io/badge/license-MIT-blue?style=for-the-badge)

> A production-grade DevOps implementation of a Hotstar clone application deployed on AWS EKS with complete CI/CD pipeline, security scanning, and monitoring.

## 📋 Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Technologies Used](#technologies-used)
- [Prerequisites](#prerequisites)
- [Installation Guide](#installation-guide)
- [CI/CD Pipeline](#cicd-pipeline)
- [Monitoring Setup](#monitoring-setup)
- [Security](#security)
- [Contributing](#contributing)

## 🎯 Overview

This project demonstrates a complete DevOps workflow for deploying a streaming application (Hotstar clone) on Kubernetes with:

- ✅ Automated CI/CD using Jenkins
- ✅ Container orchestration with Amazon EKS
- ✅ Security scanning with Trivy & SonarQube
- ✅ Real-time monitoring with Prometheus & Grafana
- ✅ Infrastructure as Code with Terraform
- ✅ Automated email notifications

## 🏗️ Architecture

```
┌─────────────┐      ┌──────────────┐      ┌─────────────┐
│   GitHub    │─────▶│   Jenkins    │─────▶│   Docker    │
│ Repository  │      │   Pipeline   │      │   Registry  │
└─────────────┘      └──────────────┘      └─────────────┘
                            │                      │
                            ▼                      ▼
                     ┌──────────────┐      ┌─────────────┐
                     │  SonarQube   │      │   AWS EKS   │
                     │   & Trivy    │      │  Cluster    │
                     └──────────────┘      └─────────────┘
                                                  │
                                                  ▼
                                           ┌─────────────┐
                                           │ Prometheus  │
                                           │  & Grafana  │
                                           └─────────────┘
```

## 🛠️ Technologies Used

### Infrastructure & Cloud
![AWS](https://img.shields.io/badge/AWS-232F3E?style=flat&logo=amazon-aws&logoColor=white)
![Terraform](https://img.shields.io/badge/Terraform-7B42BC?style=flat&logo=terraform&logoColor=white)
![EKS](https://img.shields.io/badge/Amazon_EKS-FF9900?style=flat&logo=amazon-eks&logoColor=white)

### DevOps & CI/CD
![Jenkins](https://img.shields.io/badge/Jenkins-D24939?style=flat&logo=jenkins&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=flat&logo=kubernetes&logoColor=white)

### Monitoring & Security
![Prometheus](https://img.shields.io/badge/Prometheus-E6522C?style=flat&logo=prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/Grafana-F46800?style=flat&logo=grafana&logoColor=white)
![SonarQube](https://img.shields.io/badge/SonarQube-4E9BCD?style=flat&logo=sonarqube&logoColor=white)
![Trivy](https://img.shields.io/badge/Trivy-1904DA?style=flat&logo=aqua&logoColor=white)

### Development
![Node.js](https://img.shields.io/badge/Node.js-339933?style=flat&logo=node.js&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=flat&logo=javascript&logoColor=black)

## 📦 Prerequisites

Before you begin, ensure you have:

- AWS Account with appropriate permissions
- Ubuntu 22.04 LTS EC2 instance (t3.xlarge)
- Domain name (optional, for custom DNS)
- GitHub account
- Docker Hub account
- Gmail account (for SMTP notifications)

## 🚀 Installation Guide

### Step 1: Launch EC2 Instance

```bash
# Instance Configuration
Instance Type: t3.xlarge (4 vCPUs, 16 GB RAM)
AMI: Ubuntu Server 22.04 LTS
Storage: 30GB GP3 SSD
```

**Security Group Ports:**
| Port | Protocol | Service |
|------|----------|---------|
| 22 | TCP | SSH |
| 80 | TCP | HTTP |
| 443 | TCP | HTTPS |
| 8080 | TCP | Jenkins |
| 9000 | TCP | SonarQube |
| 3000 | TCP | Grafana/App |
| 587 | TCP | SMTP |
| 465 | TCP | SMTP SSL |

### Step 2: Install Required Tools

#### Basic Setup
```bash
sudo apt update
sudo apt install unzip -y
```

#### Git Installation
```bash
sudo apt update
sudo apt install git-all -y
git --version
```

#### AWS CLI
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws --version
```

#### Jenkins
```bash
sudo apt update
sudo apt install fontconfig openjdk-21-jre -y
java -version

sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt update
sudo apt install jenkins -y
sudo systemctl enable jenkins
sudo systemctl start jenkins
```

#### Docker
```bash
sudo apt-get update -y
sudo apt-get install docker.io -y
sudo usermod -aG docker ubuntu 
sudo usermod -aG docker jenkins
newgrp docker
sudo chmod 660 /var/run/docker.sock
sudo systemctl restart docker
```

#### Kubernetes Tools
```bash
# eksctl
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version

# kubectl
curl -LO https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl version --client
```

#### Terraform
```bash
sudo apt-get update && sudo apt-get install -y gnupg software-properties-common
wget -O- https://apt.releases.hashicorp.com/gpg | \
gpg --dearmor | \
sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null

echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt update
sudo apt-get install terraform -y
terraform -v
```

#### Trivy
```bash
sudo apt-get install wget apt-transport-https gnupg lsb-release -y
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update -y
sudo apt-get install trivy -y
```

#### Prometheus & Blackbox Exporter
```bash
wget https://github.com/prometheus/prometheus/releases/download/v3.7.0-rc.0/prometheus-3.7.0-rc.0.linux-amd64.tar.gz
tar -xvzf prometheus-3.7.0-rc.0.linux-amd64.tar.gz

wget https://github.com/prometheus/blackbox_exporter/releases/download/v0.27.0/blackbox_exporter-0.27.0.linux-amd64.tar.gz
tar -xvzf blackbox_exporter-0.27.0.linux-amd64.tar.gz
```

#### Grafana
```bash
sudo apt-get install -y apt-transport-https software-properties-common wget
sudo mkdir -p /etc/apt/keyrings/
wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null
echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
sudo apt-get update -y
sudo apt-get install grafana -y
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

### Step 3: Configure Jenkins

1. **Access Jenkins**
   ```bash
   http://<your-ec2-ip>:8080
   ```

2. **Get Initial Password**
   ```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

3. **Install Required Plugins**
   - Eclipse Temurin Installer
   - SonarQube Scanner
   - NodeJs Plugin
   - OWASP Dependency Check
   - Docker Pipeline
   - Docker plugins suite
   - AWS Credentials

### Step 4: Configure SonarQube

```bash
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
```

Access at `http://localhost:9000` (default: admin/admin)

### Step 5: Setup Credentials in Jenkins

Navigate to **Manage Jenkins → Credentials → Global**

1. **SonarQube Token** (Secret Text)
   - ID: `sonar-token`

2. **Docker Hub** (Username & Password)
   - ID: `docker-cred`

3. **Gmail SMTP** (Username & Password)
   - ID: `gmail-cred`
   - Use Gmail App Password

4. **GitHub Token** (Username & Password)
   - ID: `github-token`

### Step 6: Configure Tools in Jenkins

**Manage Jenkins → Tools**

- **JDK**: Install JDK 17 from Adoptium
- **NodeJS**: Version 16.20.2
- **SonarQube Scanner**: From Maven Central
- **OWASP Dependency Check**: From GitHub

## 🔄 CI/CD Pipeline

The Jenkins pipeline includes the following stages:

1. **Clean Workspace** - Ensures clean build environment
2. **Checkout from Git** - Pulls latest code
3. **SonarQube Analysis** - Code quality checks
4. **Install Dependencies** - NPM install
5. **Trivy FS Scan** - File system vulnerability scan
6. **Docker Build & Push** - Creates and pushes image
7. **Trivy Image Scan** - Container image scan
8. **Deploy to Container** - Runs application
9. **Email Notification** - Sends build status

### Sample Jenkinsfile

See the complete pipeline in the repository under `Jenkinsfile`

## ☸️ Kubernetes Deployment

### Create EKS Cluster

```bash
# Configure AWS CLI
aws configure

# Create EKS Cluster
eksctl create cluster --name hotstar-k8s \
  --region us-west-2 \
  --node-type m7i-flex.large \
  --nodes-min 2 \
  --nodes-max 2 \
  --zones us-west-2a,us-west-2b
```

### Deploy Application

```bash
cd hotstar-kubernetes/K8S/
kubectl apply -f manifest.yml
kubectl get all
```

### Test Auto-Healing

```bash
kubectl delete pod <pod-name>
kubectl get pods -w
```

## 📊 Monitoring Setup

### Configure Prometheus

Edit `prometheus.yml`:

```yaml
scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]
        labels:
          app: "prometheus"
  
  - job_name: 'blackbox'
    metrics_path: /probe
    params:
      module: [http_2xx]
    static_configs:
      - targets:
        - http://prometheus.io
        - http://www.rohandevops.co.in
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: 127.0.0.1:9115
```

Start Prometheus:
```bash
./prometheus &
```

### Configure Grafana

1. Access Grafana: `http://localhost:3000` (admin/admin)
2. Add Data Source:
   - **Connections → Data Sources → Prometheus**
   - URL: `http://localhost:9090`
3. Import Dashboard:
   - Use Blackbox Exporter dashboard ID
   - Select Prometheus data source

## 🔒 Security

### Security Scanning Tools

- **SonarQube**: Code quality and security vulnerabilities
- **Trivy**: Container and file system scanning
- **OWASP Dependency Check**: Dependency vulnerabilities

### Best Practices

✅ Regular security scans in CI/CD pipeline
✅ Image scanning before deployment
✅ Quality gates enforcement
✅ Secrets management via Jenkins credentials
✅ Network policies in Kubernetes
✅ RBAC implementation

## 🌐 DNS Configuration

If using a custom domain (e.g., GoDaddy):

1. Go to your domain DNS settings
2. Edit CNAME record
3. Add AWS ELB domain name (without http://)
4. Save changes

## 📧 Email Notifications

Gmail SMTP is configured for build notifications. The pipeline sends:

- Build status (Success/Failure)
- Build number and URL
- Trivy scan reports as attachments
- Build initiator information

## 🐛 Troubleshooting

### Port 3000 Conflict

```bash
sudo lsof -i :3000
sudo kill -9 <PID>
```

### Jenkins Permission Issues

```bash
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
```

### EKS Cluster Access

```bash
aws eks update-kubeconfig --region us-west-2 --name hotstar-k8s
kubectl config current-context
```

## 📝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.


## 🙏 Acknowledgments

- Disney+ Hotstar for the design inspiration
- The DevOps community for best practices
- Open-source tool maintainers

## 📞 Support

For support, email ruhondeb8@gmail.com or open an issue in the repository.

---

<div align="center">

**⭐ If you found this project helpful, please give it a star! ⭐**

Made with ❤️ by DevOps Engineers

</div>
