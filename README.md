# Nodejs-demo-app
🚀 Build & Deploy Node.js App with CI/CD Pipeline

(Docker + GitHub Actions + AWS EC2)

📌 Project Overview

This project demonstrates end-to-end CI/CD automation for deploying a Node.js application using:

Docker for containerization

GitHub Actions for CI/CD pipeline

AWS EC2 for hosting the containerized app


The pipeline automatically builds, tests, and deploys the Node.js application whenever changes are pushed to the main branch.


---

⚙️ Tools & Technologies

Node.js – Application runtime

Docker – Containerization

GitHub Actions – Continuous Integration & Deployment

AWS EC2 (Amazon Linux / Ubuntu) – Hosting environment

---

🏗 Project Workflow

1. Clone the repository and create a Node.js app.


2. Write a Dockerfile to containerize the application.


3. Push code to GitHub – triggers GitHub Actions workflow.


4. CI/CD Pipeline steps:

✅ Install dependencies & run tests

🐳 Build Docker image

📤 Push Docker image to EC2 (via SSH or Docker Hub)

🚀 Deploy the container on EC2



5. Access app via EC2 Public IP / Domain.

---

📂 Project Structure

nodejs-cicd-docker-ec2/
│
├── Dockerfile
├── docker-compose.yml       
├── .github/
│   └── workflows/
│       └── ci-cd.yml       
├── src/
│   └── app.js             
├── package.json
└── README.md


---

🐳 Docker Setup

Dockerfile Example

FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install --production

COPY . .

EXPOSE 3000

CMD ["npm", "start"]


---

⚡ GitHub Actions Workflow (ci-cd.yml)

name: Node.js CI/CD

on:
  push:
    branches: [ "main" ]

jobs:
  build-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@v3

      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: 18

      - name: Install Dependencies
        run: npm install

      - name: Run Tests
        run: npm test

      - name: Build Docker Image
        run: docker build -t nodejs-app .

      - name: Deploy to EC2
        uses: appleboy/ssh-action@v0.1.8
        with:
          host: ${{ secrets.EC2_HOST }}
          username: ${{ secrets.EC2_USER }}
          key: ${{ secrets.EC2_SSH_KEY }}
          script: |
            docker stop nodejs-app || true
            docker rm nodejs-app || true
            docker rmi nodejs-app || true
            docker pull <your-dockerhub-username>/nodejs-app:latest
            docker run -d -p 3000:3000 --name nodejs-app <your-dockerhub-username>/nodejs-app:latest


---

🚀 Deployment Steps

1. Launch AWS EC2 instance and install Docker:

sudo apt update && sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker


2. Configure GitHub Secrets:

EC2_HOST → Public IP of EC2

EC2_USER → EC2 username (ubuntu or ec2-user)

EC2_SSH_KEY → Private key for EC2 access



3. Push code to main branch → GitHub Actions will build & deploy automatically.


4. Access the app:

http://<EC2-Public-IP>:3000

---

✅ Conclusion

With this setup:

Code pushes automatically trigger CI/CD pipeline

Node.js app is containerized & deployed to AWS EC2

Deployment is fully automated with GitHub Actions
