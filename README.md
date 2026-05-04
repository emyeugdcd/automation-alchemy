# Automation Alchemy

Welcome to **Automation Alchemy**, the third chapter of the 8-part DevOps module! 

This project is an upgraded and enhanced version of the previous projects, *Infrastructure Insight* and *Server Sorcery* which is available on my Github Profile github.com/emyeugdcd/infra-insight and github.com/emyeugdcd/server-sorcery. While the previous projects focused on manually provisioning and configuring our servers, this chapter introduces **end-to-end automation** and **Continuous Integration / Continuous Deployment (CI/CD)** using Github Actions.


## How the System Works
The infrastructure is built using a modern, layered automation approach. Here is the step-by-step lifecycle of how the systems are provisioned and applications are deployed.

1. **Infrastructure as Code (Vagrant)**: 
   - First, **Vagrant** spins up the virtual machines (VMs) using the `Vagrantfile`. This creates the baseline infrastructure (Load Balancer, Web Servers, Backend Server) from a blank slate.
   
2. **Configuration Management (Ansible)**: 
   - Once the VMs are running, **Ansible** takes over. It connects to each VM via SSH and executes the `setup.yml` playbook. 
   - This script installs necessary packages, configures networking, sets up users and permissions (like the `devops` user), enforces security rules, and installs Docker.

3. **Containerization (Docker)**: 
   - With the servers prepared, **Docker** is used to run the backend applications in isolated containers. The frontend and backend applications are packaged into Docker images and spun up as containers, ensuring consistency across environments.

4. **Continuous Integration & Deployment (CI/CD Pipeline)**: 
   - Finally, the CI/CD pipeline (using GitHub Actions) automates the deployment process.
   - Whenever new code is pushed to the repository, the pipeline automatically runs tests, builds the new Docker images, and deploys the updated containers to the running servers. It also includes health checks and an automatic rollback mechanism if the deployment fails.
   - Why I chose Github Actions over Jenkins? You can check the comparison in `jenkins_vs_github_actions.md`.

## How to Test Locally
To ensure this project works as expected follow these steps:

### Prerequisites
- VMware Fusion
- Vagrant
- Ansible

### 1. Start from a Clean Slate
Ensure you have no project-related VMs currently running. You can check the status and destroy any existing ones:
```bash
vagrant status
vagrant destroy -f
```

### 2. Run the Automated Deployment
You can deploy the entire infrastructure with a single command using the provided script:
```bash
./super_deploy.sh
```

### 3. Verify the Setup
Once the scripts complete, verify that all 5 virtual machines are running:
```bash
vagrant status
```

### 4. Access the Application
The application should now be deployed and accessible. Navigate to the Load Balancer IP in your web browser:
```text
http://192.168.56.11
```
From here, you can verify that the app loads properly and infrastructure metrics are displayed correctly.

### 5. Test CI/CD (Simulated)
In a real-world scenario (or if using a self-hosted runner), you could test the CI/CD pipeline by pushing a commit to the `main` branch. GitHub Actions will automatically trigger the `deploy.yml` workflow to run tests, build artifacts, and deploy updates to the servers. However, with GitHub Actions, it is physically impossible for a public cloud server to ping [IP_ADDRESS], because that is a private IP address that only exists on your local machine. The connection will just time out. Therefore, for demonstrating and learning purposes, I have added a `deploy_apps.sh` script that does the same thing as the `deploy.yml` workflow, but without the need for a self-hosted runner to be executed.
This script will run automatically after the `super_deploy.sh` script completes, so you don't need to run it manually.

Afterwards, you can go to docs/how-to-test.md to make sure that the project meets all the requirements. I have included there the guidance of what you need to check for and what commands are needed and so on to help make your work easier.

## Docs
After you have finised reading the README.md, if there is any confusion about this project (which I am sure there will be some), I suggest you to read the following documents to get a better understanding:
- An overview of this project can be found here: [Project Overview](./docs/project-overview.md)
- My learning notes that I took during this module can be found here [Learning Notes](./docs/learning-notes.md). This learning notes serves as a guidance to help you get a better understanding of this project
- To get a sense of the project infrastructure and deployment flow, please check project walkthrough page: [Project Walkthrough](./docs/project-walkthrough.md)
- For kood/sisu students, here is a guide on [How to Test](./docs/how-to-test.md) to make sure I have checked all the requirements on the testing page.
- For those who are interested in learning about Jenkins vs Github Actions, here is a comparison: [Jenkins vs Github Actions](./docs/jenkins_vs_github_actions.md)
- My notes about github actions and the errors I have encountered and how I fixed them, can be found here: [Github-Actions-notes.md](./docs/Github-Actions-notes.md)

## Repository Structure

- `Vagrantfile`: Defines the virtual machines.
- `setup.yml`: The Ansible playbook for server configuration.
- `super_deploy.sh`: A single-command script to trigger the entire build process.
- `deploy_apps.sh`: A single-command script to deploy the applications.
- `inventory.ini`: The Ansible inventory file.
- `.README.md`: Self explanatory.
- `docs/`: All documents related to this project.
- `backend/`: The backend application code.
- `frontend/`: The frontend application code.
- `frontend/Dockerfile`: Dockerfile for the frontend application.
- `backend/Dockerfile`: Dockerfile for the backend application.
- `.github/workflows/deploy.yml`: Our GitHub Actions CI/CD pipeline.
- `Jenkinsfile`: Legacy CI/CD pipeline reference for studying.
