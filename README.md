# CI/CD Pipeline with Jenkins, SonarQube, and Docker

## Overview
This project implements a CI/CD pipeline that automates code deployment using Jenkins, SonarQube for static code analysis, and Docker for containerization. The entire setup is hosted on AWS EC2 instances.

## Project Workflow
1. **Push Code to GitHub**
   - Create a GitHub repository and upload the project code.
   - Set up a GitHub webhook to trigger Jenkins upon code changes.

2. **Pull Code via Jenkins**
   - Jenkins pulls the latest code from GitHub.
   - The pipeline is configured to automatically start upon repository updates.

3. **Static Code Analysis with SonarQube**
   - The code is scanned for bugs and vulnerabilities using SonarQube.
   - If the code passes SonarQube analysis, it proceeds to the next stage.

4. **Deploy on Docker**
   - If the code passes testing, it is deployed inside a Docker container.
   - The Docker container is hosted on an EC2 instance.

5. **Access Deployed Application**
   - The application is accessible via a web browser to verify its functionality.

## AWS EC2 Instances Used
### 1. Jenkins Server (Ubuntu, t2.medium, 4GB RAM, 2 vCPUs)
- Installed Java 11 (OpenJDK) and Jenkins.
- Configured Jenkins to pull code from GitHub.
- Installed plugins: `SonarQube Scanner`, `GitHub Integration`, `SSH Plugin`.
- Created a Jenkins pipeline to automate the CI/CD process.

### 2. SonarQube Server (Ubuntu, t2.medium, 4GB RAM, 2 vCPUs)
- Installed Java 17 (OpenJDK) required for SonarQube.
- Downloaded and configured SonarQube Community Edition.
- Allowed inbound traffic on port 9000.
- Generated a token for authentication in Jenkins.

### 3. Docker Server (Ubuntu, t2.medium, 4GB RAM, 2 vCPUs)
- Installed Docker and configured it to run containers.
- Set up SSH access between Jenkins and the Docker server.
- Configured Jenkins to execute remote shell commands for deployment.

## Step-by-Step Setup
### 1. Setting Up Jenkins
```sh
sudo apt update
sudo apt install openjdk-11-jre
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install jenkins
```
- Start Jenkins and access it at `http://<Jenkins-IP>:8080`.

### 2. Setting Up SonarQube
```sh
sudo apt update
sudo apt install openjdk-17-jre
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.9.1.zip
unzip sonarqube-9.9.1.zip
sudo mv sonarqube-9.9.1 /opt/sonarqube
/opt/sonarqube/bin/linux-x86-64/sonar.sh start
```
- Access it at `http://<SonarQube-IP>:9000` (default login: `admin` / `admin`).

### 3. Setting Up Docker
```sh
sudo apt update
sudo apt install docker.io -y
sudo usermod -aG docker $USER
```

## Jenkins Pipeline Configuration
1. Create a **Freestyle Project** in Jenkins.
2. Add **GitHub as the Source Code Management**.
3. Enable **GitHub Webhook Triggers**.
4. Add a **Build Step to Execute SonarQube Analysis**.
5. Add a **Build Step to Deploy Code to Docker**.
6. Add **Shell Commands to Build and Run the Docker Container**.

## Deployment Process
### 1. Build Docker Image
```sh
docker build -t my-website .
```
### 2. Run Docker Container
```sh
docker run -d -p 8085:80 --name my-website-container my-website
```
### 3. Access the Application
- Open a web browser and navigate to `http://<Docker-Server-IP>:8085`

## Security & Access Management
- SSH keys are used to connect EC2 instances securely.
- SonarQube authentication is managed via tokens.
- Security group rules are configured to allow access to required ports (8080 for Jenkins, 9000 for SonarQube, 8085 for Docker container).

## Conclusion
This CI/CD pipeline provides an automated solution for code deployment. It integrates GitHub, Jenkins, SonarQube, and Docker to streamline the process of code validation and deployment. Future improvements could include Kubernetes for container orchestration and enhanced security measures.
