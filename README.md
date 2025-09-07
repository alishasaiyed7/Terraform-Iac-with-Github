# 🚀 Terraform with CI/CD Using GitHub Actions

This project demonstrates how to deploy a simple Node.js application on an AWS EC2 instance using **Terraform** as Infrastructure as Code (IaC), and automating the deployment via **GitHub Actions** CI/CD pipeline.

---

## 📘 What is Terraform?

**Terraform** is an open-source Infrastructure as Code (IaC) tool developed by HashiCorp. It allows you to define and provision infrastructure using a high-level configuration language called **HCL** (HashiCorp Configuration Language).

---

## 🤔 Why Use Terraform?

- ✅ **Infrastructure as Code** – Easily version-controlled using Git.
- ✅ **Automated provisioning** – No need to manually create cloud resources.
- ✅ **Cloud Agnostic** – Works with AWS, Azure, GCP, and many more.
- ✅ **Reproducible environments** – Same config = same infra every time.
- ✅ **Scalable** – Perfect for teams and production deployments.

---

---

## 🛠️ Steps to Setup and Deploy

### 1️⃣ Create the Files

#### `main.tf` – Terraform Configuration for AWS EC2:

```bash
provider "aws" {
  region = "us-east-1" # Change to your region
}

# Create an S3 bucket
  resource "aws_s3_bucket" "mybucket1456" {
  bucket = "my-terraform-bucket-123456-alishaaa"
}

# Create an EC2 instance with user-data
resource "aws_instance" "web" {
  ami           = "ami-0360c520857e3138f" # Example Amazon Linux 2 AMI
  instance_type = "t2.micro"
  subnet_id     = "subnet-06522f7690ffe0353"
  key_name      = "test-key" # Replace with your key pair


  tags = {
    Name = "Terraform-EC2"
  }
}

```
Note: aws configure and add access key, secret access key and  region etc.

---

**app.js – Simple Node.js App**

```bash
const express = require('express');
const bodyParser = require('body-parser');
const app = express();
const port = 3000;

let todos = []; // In-memory list (resets if server restarts)

app.use(bodyParser.urlencoded({ extended: true }));
app.use(express.static('views'));

// Home page
app.get('/', (req, res) => {
  res.sendFile(__dirname + '/views/index.html');
});

// Add a task
app.post('/add', (req, res) => {
  todos.push(req.body.task);
  res.redirect('/');
});

// Get tasks (for debugging)
app.get('/tasks', (req, res) => {
  res.json(todos);
});

app.listen(port, () => {
  console.log(`✅ To-Do app running at http://localhost:${port}`);
});

```
---
**app.test.js – Basic Test**

```bash
test("sample test", () => {
  expect(2 + 2).toBe(4);
});

```
---

**package.json**

```bash
{
  "name": "prj6-todo-app-on-nodejs-and-automate",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "start": "node app.js",
    "test": "jest"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "body-parser": "^2.2.0",
    "express": "^5.1.0"
  },
  "devDependencies": {
    "jest": "^29.0.0"
  }
}

```
---


**.gitignore** 

```bash
node_modules/
.terraform/
terraform.tfstate*
*.exe
*.log
.env
```
---

**.github/workflows/test.yaml – GitHub Actions Workflow**

```bash
name: To do Pipeline

on: 
  push:
    branches: ["main"]
  pull_request:
    branches: ["main"]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: "18"

      - name: Install dependencies
        run: npm install

      - name: Run Tests
        run: npm test


  deploy:
    runs-on: ubuntu-latest
    needs: build
    if: github.ref == 'refs/heads/main'

    steps:
      - name: checkout code
        uses: actions/checkout@v3

      - name: Deploy to EC2
        uses: appleboy/ssh-action@v0.1.10
        with:
          host: ${{ secrets.EC2_HOST }}
          username: ${{ secrets.EC2_USER }}
          key: ${{ secrets.EC2_SSH_KEY}}
          script: |
            sudo apt update -y
            sudo apt install -y git curl build-essential
            curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
            sudo apt-get install -y nodejs
            cd /home/ubuntu || git clone https://github.com/alishasaiyed7/Terraform-Iac-with-Github.git /home/ubuntu/Terraform-Iac-with-Github
            cd Terraform-Iac-with-Github
            git pull origin main
            sudo npm install 
              if ! command -v pm2 &> /dev/null
            then
               echo "⚙️ PM2 not found. Installing..."
               sudo npm install -g pm2
            else
               echo "✅ PM2 already installed"
              fi
             if pm2 list | grep -q "to-do-app"
            then
              echo "🔄 Restarting existing PM2 process..."
              pm2 restart to-do-app
            else
              echo "🚀 Starting new PM2 process..."
              pm2 start app.js --name to-do-app
            fi
            pm2 save

```
---

## 2️⃣ Push Project to GitHub

```bash
git init
git remote add origin https://github.com/your-username/your-repo-name.git
git add .
git commit -m "Initial commit"
git push -u origin main
```
---

## 3️⃣ Configure GitHub Secrets

Go to your GitHub repo → Settings → Secrets and variables → Actions:

  EC2_HOST ec2 public ip)
  EC2_USER (username)
  EC2_SSH_KEY (pemkey)

  ---

  ## 4️⃣ View Output

After pushing, GitHub Actions will run:

Run tests (npm test)
Initialize Terraform
Deploy infrastructure
Deploy Node.js app

Once deployed, copy the public IP of your EC2 instance and open it in a browser:

**http://<EC2_PUBLIC_IP>:3000**

## Project: Automated Infrastructure Deployment using Terraform & GitHub Actions
Technologies: Terraform, AWS EC2, GitHub Actions, Node.js, CI/CD

Summary:
Built a CI/CD pipeline to provision AWS infrastructure using Terraform and deployed a Node.js app on EC2. Automated testing and deployment using GitHub Actions. Managed secrets securely via GitHub's encrypted secrets.


 ## Author
 Alisha Saiyed } system Admin / AWS

