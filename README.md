🚀 Project: Deploy a Web App using CI/CD on AWS

prerequisites:
	• Python Flask app ✅
	• GitHub (code repo) ✅
	• Docker ✅
	• Jenkins ✅
	• CI/CD Pipeline ✅
	• AWS EC2 (for hosting Jenkins and App) ✅

🗺️ PHASE 1: Project Overview
Goal: Push code → GitHub → Jenkins builds & deploys the app (via Docker) → App runs on EC2

🔹 STEP-BY-STEP GUIDE

✅ STEP 1: Launch EC2 & Connect with MobaXterm

 1. Launch EC2 on AWS
	1. Go to AWS Console → EC2 → Launch Instance
	2. Use:
		○ Amazon Linux 2 (or Ubuntu)
		○ t2.micro (free tier)
		○ Create or use an existing key pair (.pem)
2. Connect via MobaXterm
	1. Open MobaXterm → Session → SSH → Add Hostname (your EC2 IP)
	2. Under "Use private key", add your .pem file
	3. Save and connect — you’re inside EC2 🎉
  
✅ STEP 2: Setup EC2 for Jenkins and Docker
Run all below commands on your Ubuntu EC2 instance:
1. Update & Install Essentials
sudo apt update && sudo apt upgrade -y
sudo apt install git curl unzip -y
○ Sudo → run commands as administrartor (root user).
○ apt → It is the package manager (like a software store for Linux)
○ apt update → Updates your system’s package list 
○ apt upgrade → Upgrades existing packages
○ git → Used to clone/push code from GitHub
○ curl → Useful for downloading files or API testing
○ unzip → For extracting .zip files
○ -y → means “Yes to all” — it skips the prompt asking for confirmation.

2. Install Docker
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
Re-login or newgrp docker to apply permissions.
○ systemctl → system control- used to manage system services (start, stop, enable, check status).
○ docker.io → is the name of the Docker package
○ sudo apt install docker.io -y → Docker gets installed
○ sudo systemctl start docker → It starts the Docker service immediately so you can begin using Docker commands
○ sudo systemctl enable docker → It tells your system to automatically start Docker every time your EC2 is restarted.
○ Usermod → means "modify a user" — it’s used to change settings for a user on your Linux system.
○ -a = append → This means: "don’t remove the user from existing groups, just add new ones."
○ -G = groups → This means: "add the user to this list of groups."
So, -aG docker means:
👉 “Add the user to the docker group, and don’t remove them from other groups.”
○ $USER → ubuntu (in my case)
○ sudo usermod -aG docker $USER →  After logging out and back in, you can run Docker commands without sudo

3. Install Java (Jenkins needs Java)
sudo apt install openjdk-17-jdk -y

4. Install Jenkins
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
	• Go to AWS Console → EC2 → Security Group → Edit Inbound Rules → Add:
		○ Type: Custom TCP, Port: 8080, Source: Your IP or 0.0.0.0/0
6. Access Jenkins in Browser
Open: http://<your-ec2-public-ip>:8080
Unlock Jenkins:
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
Install Suggested Plugins, then create Admin user.
✅ Jenkins is ready!

✅ STEP 3: Create Flask App + Dockerfile
On EC2:
Create one directory and change to that directory. for ex: mkdir flask-cicd && cd flask-cicd
Now, create three files using vim-
✏️ 1. Create app.py (The Flask App)
✏️ 2. Create requirements.txt (To install Flask)
✏️ 3. Create Dockerfile (To build the app container)
For more details of this files, check my git repo - https://github.com/Gollajahnavi78-gj/flask-cicd/tree/master

✅ STEP 4: Push Code to GitHub
	1. Create a new GitHub repo (e.g. flask-cicd)
	2. Push your code:
git init
git remote add origin https://github.com/<your-username>/flask-cicd.git
git add .
git commit -m "initial commit"
git push -u origin master
○ git → Git command
○ remote → you're working with remote repositories
○ add → you're adding a new one
○ origin → the default nickname for the remote
○ https://github.com/...  → the actual GitHub repo URL
○ Git will ask for a username and password.
	• Enter your GitHub username.
	• Paste the PAT instead of your password.
	Use a Personal Access Token (PAT)
	🔧 Step 1: Generate a PAT
		1. Go to: https://github.com/settings/tokens
		2. Click on “Generate new token” > Classic (or “Fine-grained” if you're advanced).
		3. Select the expiration (e.g. 30 days).
		4. Enable these scopes:
			○ ✅ repo
		5. Click Generate token.
		6. Copy the token and save it temporarily.
	


✅ STEP 5: Jenkins CI/CD Pipeline
1. In Jenkins:
	• Create New Item → Pipeline → Name: flask-cicd → OK
2. Pipeline Script (in Jenkins):
Paste this:
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
Click Build Now → Check console → Visit http://<ec2-public_ip>:5000
✅ App is live via CI/CD!


![image](https://github.com/user-attachments/assets/a221063c-5dd1-401d-a35d-5a191c80760c)
![image](https://github.com/user-attachments/assets/a221063c-5dd1-401d-a35d-5a191c80760c)
