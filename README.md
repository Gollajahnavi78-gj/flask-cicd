# flask-cicd
Project: Deploy a Web App using CI/CD on AWS
🚀 Project: Deploy a Web App using CI/CD on AWS
We'll use:

Python Flask app ✅

GitHub (code repo) ✅

Docker ✅

Jenkins ✅

CI/CD Pipeline ✅

AWS EC2 (for hosting Jenkins and App) ✅

🗺️ PHASE 1: Project Overview
Goal: Push code → GitHub → Jenkins builds & deploys the app (via Docker) → App runs on EC2

🔹 STEP-BY-STEP GUIDE
✅ STEP 1: Setup EC2 for Jenkins and Docker
Run all below commands on your Ubuntu EC2 instance:

1. Update & Install Essentials
bash
Copy
Edit
sudo apt update && sudo apt upgrade -y
sudo apt install git curl unzip -y
2. Install Java (Jenkins needs Java)
bash
Copy
Edit
sudo apt install openjdk-11-jdk -y
3. Install Docker
bash
Copy
Edit
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
Re-login or newgrp docker to apply permissions.

4. Install Jenkins
bash
Copy
Edit
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt update
sudo apt install jenkins -y
sudo systemctl enable jenkins
sudo systemctl start jenkins
5. Allow Port 8080 on AWS EC2
Go to AWS Console → EC2 → Security Group → Edit Inbound Rules → Add:

Type: Custom TCP, Port: 8080, Source: Your IP or 0.0.0.0/0

6. Access Jenkins in Browser
Open:
http://<your-ec2-public-ip>:8080

Unlock Jenkins:

bash
Copy
Edit
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
Install Suggested Plugins, then create Admin user.

✅ Jenkins is ready!

✅ STEP 2: Create Flask App + Dockerfile
On EC2:

bash
Copy
Edit
mkdir flask-cicd && cd flask-cicd
app.py
python
Copy
Edit
from flask import Flask
app = Flask(__name__)

@app.route('/')
def home():
    return "🚀 Deployed via Jenkins CI/CD Pipeline!"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
requirements.txt
nginx
Copy
Edit
flask
Dockerfile
Dockerfile
Copy
Edit
FROM python:3.9
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
Test locally (optional):

bash
Copy
Edit
docker build -t flaskapp .
docker run -p 5000:5000 flaskapp
✅ STEP 3: Push Code to GitHub
Create a new GitHub repo (e.g. flask-cicd)

Push your code:

bash
Copy
Edit
git init
git remote add origin https://github.com/<your-username>/flask-cicd.git
git add .
git commit -m "initial commit"
git push -u origin main
✅ STEP 4: Jenkins CI/CD Pipeline
1. In Jenkins:
Create New Item → Pipeline → Name: flask-cicd → OK

2. Pipeline Script (in Jenkins):
Paste this:

groovy
Copy
Edit
pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/<your-username>/flask-cicd.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t flaskapp .'
            }
        }
        stage('Run Container') {
            steps {
                sh '''
                    docker rm -f flaskapp || true
                    docker run -d -p 5000:5000 --name flaskapp flaskapp
                '''
            }
        }
    }
}
Replace Git URL accordingly.

Click Build Now → Check console → Visit http://<ec2-ip>:5000

✅ App is live via CI/CD!

