# CI/CD Pipeline for Node.js Application

## Objective
The objective of this project is to set up a CI/CD pipeline to build, test, and deploy a Node.js application using Jenkins. The application is deployed to a Kubernetes cluster on AWS EKS, ensuring scalability and high availability.

---

## Prerequisites

- Ubuntu or another compatible Linux distribution
- AWS Account
- Jenkins installed with JDK 17
- Docker installed and configured
- GitHub repository with the application code
- AWS CLI installed and configured with the output format set to JSON
- Kubernetes cluster set up on AWS EKS (nodejs-eks in ap-south-1 region)

---

## Steps to Set Up the Environment

### Install Docker
1. Update and install Docker:
   ```bash
   sudo apt update
   sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
   echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   sudo apt update
   sudo apt install -y docker-ce
   docker --version
   ```
2. Grant root privileges for Docker:
   ```bash
   sudo usermod -aG docker $USER
   newgrp docker
   docker run hello-world
   ```

### Install Jenkins (with JDK 17)
1. Install JDK 17:
   ```bash
   sudo apt update
   sudo apt install -y openjdk-17-jdk
   java -version
   ```
2. Add Jenkins repository and install Jenkins:
   ```bash
   curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
   echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
   sudo apt update
   sudo apt install -y jenkins
   sudo systemctl start jenkins
   sudo systemctl enable jenkins
   ```
3. Access Jenkins at `http://<server-ip>:8080` and complete the setup.

### Install and Configure AWS CLI
1. Install AWS CLI:
   ```bash
   curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
   unzip awscliv2.zip
   sudo ./aws/install
   aws --version
   ```
2. Configure AWS CLI with output format as JSON:
   ```bash
   aws configure
   ```
   Provide your AWS Access Key, Secret Key, default region, and set the output format to JSON.

### Set Up GitHub Repository

1. Initialize and push code to GitHub:
   ```bash
   mkdir Nodeapp
   cd Nodeapp
   git init
   git remote add origin https://github.com/rpillaiakshay/Nodeapp.git
   git add .
   git commit -m "Deploy NodeApp to AWS EKS"
   git push -u origin main
   ```

---

## Jenkins Pipeline

### Setting Up Jenkins
1. Assign credentials:
   - GitHub: Add secret text with ID `GitHub`
   - DockerHub: Add secret text with ID `DockerHub`

2. Install necessary plugins:
   - NodeJS Plugin
   - Docker Pipeline Plugin

---

## Setting Up Monitoring

### Steps to Set Up Prometheus
1. Create a `prometheus.yml` configuration file to define scrape targets for monitoring.
2. Start Prometheus using the Docker Hub image:
   ```bash
   mkdir -p /etc/prometheus
   vim /etc/prometheus/prometheus.yml
   ```
   Write the configuration and start Prometheus:
   ```bash
   docker run -d --name=prometheus -p 9090:9090 -v /etc/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus:latest
   ```

### Steps to Set Up Node Exporter
1. Start Node Exporter using Docker Hub image:
   ```bash
   docker run -d --name=node_exporter -p 9100:9100 prom/node-exporter:latest
   ```

### Steps to Set Up Grafana
1. Start Grafana using the Docker Hub image:
   ```bash
   docker run -d --name grafana -p 3000:3000 grafana/grafana
   ```
2. Access Grafana at `http://<server-ip>:3000` and add Prometheus as a data source.

---

## Output

- Successful pipeline execution in Jenkins.
- Docker image pushed to Docker Hub (`rpillaiakshay/node-app:latest`).
- Application deployed to Kubernetes cluster.
- Monitor system status using Grafana-Prometheus.

---

## Links

- **DockerHub Image**: `docker pull rpillaiakshay/node-app:latest`

---

## Conclusion
This project demonstrates the complete lifecycle of a CI/CD pipeline for a Node.js application using Jenkins, Docker and Kubernetes. The setup ensures a streamlined process for deploying scalable and highly available applications while laying the groundwork for future enhancements or complex implementations.
