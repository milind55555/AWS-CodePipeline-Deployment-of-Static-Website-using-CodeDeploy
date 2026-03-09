AWS CodePipeline Deployment of Static Website using CodeDeploy

This project demonstrates the end-to-end deployment of a static website (index.html) on an EC2 instance using AWS CodePipeline and CodeDeploy. The deployment process automatically installs the Apache web server and copies the website files to the server.

The pipeline fetches the code from a Git repository and deploys it to an EC2 instance using automated scripts.

Project Architecture
Git Repository
      │
      │ (Source Stage)
      ▼
AWS CodePipeline
      │
      ▼
AWS CodeDeploy
      │
      ▼
EC2 Instance
      │
      ▼
Apache Web Server
      │
      ▼
Deployed Website (index.html)
AWS Services Used

This project uses the following AWS services:

Amazon EC2 – To host the web server

AWS CodePipeline – For continuous deployment pipeline

AWS CodeDeploy – To deploy application on EC2

AWS IAM – For role-based access control

Amazon S3 – To store pipeline artifacts

Project Structure
project-root
│
├── index.html
├── appspec.yml
└── scripts
     ├── install_apache.sh
     └── start_server.sh
EC2 Instance Setup

Launch an EC2 instance.

Choose Ubuntu as the operating system.

Configure security group to allow:

Port	Purpose
22	SSH Access
80	Web Server

Attach an IAM role to the instance.

IAM Role Name

EC2CodeDeployRole

Policies attached to the role:

AmazonEC2RoleforAWSCodeDeploy

AmazonSSMManagedInstanceCore

Install CodeDeploy Agent on EC2

Connect to the instance using SSH and run the following commands.

sudo apt update
sudo apt install ruby wget -y

Download the CodeDeploy agent.

wget https://aws-codedeploy-ap-south-1.s3.amazonaws.com/latest/install

Install the agent.

chmod +x install
sudo ./install auto

Start the agent service.

sudo systemctl start codedeploy-agent
sudo systemctl enable codedeploy-agent
Application Specification File (appspec.yml)

The appspec.yml file tells CodeDeploy how to deploy the application.

version: 0.0
os: linux

files:
  - source: /
    destination: /var/www/html

hooks:

  BeforeInstall:
    - location: scripts/install_apache.sh
      timeout: 300
      runas: root

  ApplicationStart:
    - location: scripts/start_server.sh
      timeout: 300
      runas: root
Deployment Scripts
Apache Installation Script

File location:

scripts/install_apache.sh
#!/bin/bash

apt update -y
apt install apache2 -y
Apache Start Script

File location:

scripts/start_server.sh
#!/bin/bash

systemctl start apache2
systemctl enable apache2
CodeDeploy Setup

Open the AWS console.

Go to AWS CodeDeploy.

Create a new application.

Application Name

MyWebsiteApp

Deployment group name

MyWebsiteDG

Deployment type:

EC2/On-Premise

Attach service role.

Role Name

CodeDeployServiceRole

IAM policy attached:

AWSCodeDeployRole
CodePipeline Setup

Create a new pipeline in AWS CodePipeline.

Pipeline name

WebsitePipeline
Source Stage

Select your Git repository as the source provider.

Example:

Repository: my-website
Branch: main
Build Stage

This project is a static website, so the build stage can be skipped.

Deploy Stage

Deployment provider

AWS CodeDeploy

Application

MyWebsiteApp

Deployment group

MyWebsiteDG
Deployment Process

Developer pushes code to the Git repository.

CodePipeline automatically detects the change.

Pipeline downloads the source code.

Artifact is stored in Amazon S3.

CodeDeploy triggers deployment.

Apache web server is installed on EC2.

Website files are copied to:

/var/www/html

Apache server starts and the website becomes available.

Access the Website

Open the browser and enter the EC2 public IP.

http://EC2-PUBLIC-IP

You should see the deployed webpage.

IAM Roles Summary

EC2 Instance Role

EC2CodeDeployRole

Policies:

AmazonEC2RoleforAWSCodeDeploy

AmazonSSMManagedInstanceCore

CodeDeploy Service Role

CodeDeployServiceRole

Policy:

AWSCodeDeployRole

CodePipeline Role

AWSCodePipelineServiceRole
Advantages of This Setup

Automated deployments

Continuous delivery pipeline

No manual server configuration

Faster application updates

Scalable deployment process
