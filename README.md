# flask-ci-cd# Testing Jenkins CI/CD
# CI/CD Pipeline Setup with GitHub Actions & Jenkins

## Overview
This project automates the deployment of a **Flask or Node.js application** using **GitHub Actions, Jenkins, and Codeberg Webhooks**. Every push to the repository triggers Jenkins, which pulls the latest code, installs dependencies, and deploys the application.

---

## Setup Instructions

### **1. Install Jenkins**
Jenkins needs to be installed on a local machine or cloud server.

```sh
sudo apt update
sudo apt install openjdk-11-jdk -y
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install jenkins -y
sudo systemctl start jenkins
sudo systemctl enable jenkins
Access Jenkins at: http://your-server-ip:8080

2. Configure Codeberg Webhook
The webhook ensures Jenkins runs the pipeline whenever code is pushed to the repository.

1.Go to Settings → Webhooks in Codeberg.

2.Click Add Webhook.

3.Use this URL: https://your-jenkins-server/github-webhook/

4.Select Push Events and save.

3. Set Up GitHub Actions
GitHub Actions automates the process by triggering Jenkins on each push.

Create .github/workflows/ci-cd.yml:

name: CI/CD Pipeline
on:
  push:
    branches:
      - main

jobs:
  trigger-jenkins:
    runs-on: ubuntu-latest
    steps:
      - name: Notify Jenkins
        run: |
          curl -X POST https://your-jenkins-server/job/GitHub-CI-CD/build \
          --user your-username:your-api-token

Push it:
git add .github/workflows/ci-cd.yml
git commit -m "Added GitHub Actions workflow"
git push origin main

4. Deployment Using Jenkins
Modify Jenkins pipeline to include cloning, installing dependencies, and deploying.
pipeline {
    agent any
    stages {
        stage('Clone Repository') {
            steps {
                sh '''
                if [ -d /var/lib/jenkins/workspace/flask-ci-cd ]; then
                    cd /var/lib/jenkins/workspace/flask-ci-cd
                    git pull origin main
                else
                    git clone https://github.com/your-repo/flask-ci-cd.git /var/lib/jenkins/workspace/flask-ci-cd
                fi
                '''
            }
        }
        stage('Install Dependencies') {
            steps {
                sh '''
                cd /var/lib/jenkins/workspace/flask-ci-cd
                python3 -m venv venv
                source venv/bin/activate
                pip install --break-system-packages -r requirements.txt
                '''
            }
        }
        stage('Deploy App') {
            steps {
                sh '''
                cd /var/lib/jenkins/workspace/flask-ci-cd
                nohup python3 app.py &
                '''
            }
        }
    }
}

Dockerized Deployment
Using Docker allows deployment inside a container.
FROM python:3.12
WORKDIR /app
COPY . .
RUN pip install --break-system-packages -r requirements.txt
CMD ["python3", "app.py"]

docker build -t flask-app .
docker run -d -p 5000:5000 flask-app


How the Automation Works
1.Push code → Codeberg sends webhook to Jenkins.

2.Jenkins pulls the latest code, installs dependencies.

3.Application is restarted (or deployed using Docker).
