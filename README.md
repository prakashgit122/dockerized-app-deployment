# Dockerized Application Deployment using Jenkins, Docker, AWS ECR and EC2

## Project Overview

This project demonstrates an end-to-end CI workflow for deploying a Node.js application using Docker, Jenkins, GitHub, AWS Elastic Container Registry (ECR), and Amazon EC2.

The application source code is maintained in GitHub. Jenkins checks out the source code, builds a Docker image, tags it, and pushes it to AWS ECR. The Application EC2 instance pulls the image from ECR and runs the container.

## Architecture

```text
          Developer
              |
              v
         Local VS Code
              |
              v
           GitHub
              |
              v
       Jenkins EC2 Server
              |
        Build Docker Image
              |
         Tag Docker Image
              |
         Push Docker Image
              v
          AWS ECR
              |
              v
       Application EC2
              |
         Pull Image
              |
         Run Container
              |
              v
       Node.js Application
         Port: 3000
```

## Technologies Used

- AWS EC2
- AWS Elastic Container Registry (ECR)
- IAM Roles
- Ubuntu 24.04 LTS
- Jenkins
- Docker
- Node.js
- Express.js
- Git and GitHub
- Linux

## Project Structure

```text
dockerized-app-deployment/
│
├── app/
│   ├── app.js
│   └── package.json
│
├── Dockerfile
├── Jenkinsfile
└── .dockerignore
```

## Step 1: Create the Node.js Application

**package.json**

```dockerfile

### Dockerfile Explanation

- `FROM`: Uses Node.js 20 Alpine as the base image.
- `WORKDIR`: Sets the working directory inside the container.
- `COPY`: Copies application dependency files.
- `RUN npm install`: Installs application dependencies.
- `COPY app/ .`: Copies the application source code.
- `EXPOSE`: Documents the application port.
- `CMD`: Starts the Node.js application.

## Step 3: Create .dockerignore

Created a `.dockerignore` file to exclude unnecessary files from the Docker build context.

```text
node_modules
.git
```

## Step 4: Push the Project to GitHub

Created a GitHub repository:

`dockerized-app-deployment`

Pushed the application source code, Dockerfile, Jenkinsfile, and .dockerignore to the `main` branch.

## Step 5: Launch Jenkins EC2 Instance

Created an Ubuntu 24.04 LTS EC2 instance for Jenkins.

Installed the required tools:

- Java 21
- Jenkins
- Git
- Docker
- AWS CLI

Started Jenkins and accessed the Jenkins dashboard through port 8080.

Added the Jenkins user to the Docker group so Jenkins could execute Docker commands.

## Step 6: Configure AWS IAM Role

Created an IAM role for EC2 and attached the following policy:

`AmazonEC2ContainerRegistryPowerUser`

Attached the role to the Jenkins EC2 instance.

This allows Jenkins to authenticate with AWS ECR without storing AWS access keys in the pipeline.

Installed AWS CLI and configured the AWS region:

```bash
aws configure set region ap-south-1
```

## Step 7: Create AWS ECR Repository

Created a private ECR repository:

**Repository Name:** `dockerized-app`

**AWS Region:** `ap-south-1`

**Repository URI:**

```text
387701295760.dkr.ecr.ap-south-1.amazonaws.com/dockerized-app
```

## Step 8: Configure Jenkins Pipeline

Created a Jenkins Pipeline job and configured it to use Pipeline script from SCM.

Selected:

- SCM: Git
- Repository URL: `https://github.com/prakashgit122/dockerized-app-deployment.git`
- Branch: `main`
- Script Path: `Jenkinsfile`

The Jenkinsfile contains the following stages:

1. Checkout
2. Build Docker Image
3. Tag Docker Image
4. Push Docker Image to AWS ECR

## Step 9: Execute Jenkins Pipeline

Executed the Jenkins pipeline.

Jenkins successfully:

- Checked out the source code from GitHub.
- Built the Docker image.
- Tagged the image with the ECR repository URI.
- Authenticated with AWS ECR.
- Pushed the Docker image to ECR.

Verified that the `latest` image tag was available in the ECR repository.

## Step 10: Launch Application EC2 Instance

Created a second Ubuntu 24.04 LTS EC2 instance for running the application.

Installed Docker:

```bash
sudo apt update
sudo apt install docker.io -y

sudo systemctl start docker
sudo systemctl enable docker
```

Installed AWS CLI and configured the AWS region:

```bash
aws configure set region ap-south-1
```

Attached the IAM role with ECR permissions to the Application EC2 instance.

## Step 11: Authenticate Application EC2 with AWS ECR

Authenticated Docker with ECR:

```bash
aws ecr get-login-password --region ap-south-1 | sudo docker login --username AWS --password-stdin 387701295760.dkr.ecr.ap-south-1.amazonaws.com
```

## Step 12: Pull Docker Image from ECR

Pulled the image from AWS ECR:

```bash
sudo docker pull 387701295760.dkr.ecr.ap-south-1.amazonaws.com/dockerized-app:latest
```

Verified the downloaded image:

```bash
sudo docker images
```

## Step 13: Run the Docker Container

Started the application container:

```bash
sudo docker run -d --name dockerized-app -p 3000:3000 387701295760.dkr.ecr.ap-south-1.amazonaws.com/dockerized-app:latest
```

Verified the running container:

```bash
sudo docker ps
```

## Step 14: Access the Application

Opened the application using the public IP address of the Application EC2 instance:

```text
http://APPLICATION_EC2_PUBLIC_IP:3000
```

The application was successfully accessible in the browser.

## Security Configuration

The EC2 security groups were configured with:

| Port | Purpose             |
| ---- | ------------------- |
| 22   | SSH access          |
| 8080 | Jenkins dashboard   |
| 3000 | Node.js application |

Restrict inbound access to trusted IP addresses. Do not expose Jenkins or SSH publicly without appropriate security controls.

IAM roles were used to provide EC2 instances with access to AWS ECR.

## Project Outcome

Successfully implemented a Docker-based application deployment workflow using Jenkins and AWS.

---

**Project:** Dockerized Application Deployment

**Author:** DevOps Project

**Status:** CI pipeline and application deployment successfully tested.
