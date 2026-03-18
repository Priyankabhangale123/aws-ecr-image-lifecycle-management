# aws-ecr-image-lifecycle-management
# Secure Private Container Registry with Image Lifecycle and Retention Policies

## Project Overview
This project demonstrates how to implement a secure private container registry using AWS services. The solution ensures secure storage and management of Docker images with controlled access and automated lifecycle cleanup.

The project uses Amazon Elastic Container Registry (ECR) as a private container registry and implements IAM-based access control along with lifecycle policies for automatic image cleanup.

## Objectives
* Create a private container registry
* Push multiple Docker image versions
* Implement access control using IAM
* Configure lifecycle policy for automatic cleanup
* Demonstrate security validation using unauthorized access

## Technologies Used
* Docker
* AWS EC2
* Amazon ECR
* AWS IAM
* AWS CLI

## Architecture Workflow
Developer → Build Docker Image → Push to ECR → Apply Lifecycle Policy → Secure Access via IAM

# Step 1: Launch EC2 Instance
An EC2 instance is launched to build and push Docker images.

Steps:

Launch Ubuntu EC2 instance
Connect using SSH
Install Docker and AWS CLI

Commands:
  sudo apt update
  sudo apt install docker.io -y
  sudo systemctl start docker
  sudo systemctl enable docker


  <img width="1168" height="190" alt="Amazon Web Services Switch Role - Google Chrome 16-03-2026 22_06_48" src="https://github.com/user-attachments/assets/06235fe9-e048-4082-aae7-f4a8a6e97d94" />

 <img width="1037" height="110" alt="Amazon Web Services Switch Role - Google Chrome 16-03-2026 22_06_58" src="https://github.com/user-attachments/assets/2476c44b-3785-4b43-977d-06079d82581b" />

 Install AWS CLI:
 sudo apt install unzip -y
 curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
 unzip awscliv2.zip
 sudo ./aws/install


 <img width="1260" height="659" alt="Amazon Web Services Switch Role - Google Chrome 16-03-2026 22_19_31" src="https://github.com/user-attachments/assets/86442867-f388-491a-bdf4-85f8d3d1fdd1" />

 ## Step 2: Configure AWS CLI
 aws configure

 Provide:

  * Access Key
  * Secret Key
  *  Region
  * utput format

<img width="1260" height="659" alt="Amazon Web Services Switch Role - Google Chrome 16-03-2026 22_19_31" src="https://github.com/user-attachments/assets/86442867-f388-491a-bdf4-85f8d3d1fdd1" />

## Step 3: Create Private ECR Repository
  1. Go to AWS Console
  2. Open Amazon ECR
  3. Create repository

Repository Name:
  myroll
<img width="1920" height="1020" alt="Elastic Container Registry - Create Private Repository - Google Chrome 12-03-2026 21_38_34" src="https://github.com/user-attachments/assets/0a6d15fc-3e40-466c-b8cc-d47219b88ed8" />

## Step 4: Docker Application 

## app.py
 print("Hello DevOps Secure Registry Project")

## Dockerfile
  FROM python:3.9
  WORKDIR /app
  COPY app.py .
  CMD ["python","app.py"]

## Step 5: Build Docker Image
   docker build -t secure-app:v1 .

   <img width="1255" height="578" alt="aws-ecr-image-lifecycle-management_README md at master · megha1002240_aws-ecr-image-lifecycle-management - Google Chrome 18-03-2026 22_29_50" src="https://github.com/user-attachments/assets/5af726fe-cae5-48a1-9c0d-ae01e0e58ad4" />
   
## Step 6: Login to Amazon ECR
aws ecr get-login-password --region eu-north-1 \
| docker login --username AWS \
--password-stdin 718383533665.dkr.ecr.eu-north-1.amazonaws.com

<img width="1920" height="199" alt="ubuntu@ip-172-31-18-216_ ~_docker_project 12-03-2026 22_17_00" src="https://github.com/user-attachments/assets/cc052e55-cbd8-4425-b6b7-34b375d7722b" />

## Step 7: Tag Docker Image
docker tag secure-app:v1 \
718383533665.dkr.ecr.eu-north-1.amazonaws.com/secure-app:v1

## Step 8: Push Image to ECR
docker push \
718383533665.dkr.ecr.eu-north-1.amazonaws.com/secure-app:v1

<img width="1920" height="120" alt="image" src="https://github.com/user-attachments/assets/60ec9f12-ae7e-43ee-8139-6fe3cb62663f" />
Multiple versions were pushed:
v1
v2
v3
v4
v5

<img width="1252" height="653" alt="aws-ecr-image-lifecycle-management_README md at master · megha1002240_aws-ecr-image-lifecycle-management - Google Chrome 18-03-2026 22_51_30" src="https://github.com/user-attachments/assets/8389004d-7d05-42ba-a7e5-38cc99bf8fa1" />

## Step 9: Lifecycle Policy

A lifecycle policy was configured to retain only the latest 3 images.

<img width="1920" height="1020" alt="Elastic Container Registry - Create Lifecycle Rule - Google Chrome 12-03-2026 22_31_58" src="https://github.com/user-attachments/assets/69187eae-062c-48c5-8ece-f05ac6b523bc" />
<img width="1920" height="1020" alt="Elastic Container Registry - Create Lifecycle Rule - Google Chrome 12-03-2026 22_32_41" src="https://github.com/user-attachments/assets/3b590034-e39d-4c3a-ba32-1dbc6978658b" />
Result:
Before Cleanup

v1
v2
v3
v4
v5
<img width="1920" height="1020" alt="Elastic Container Registry - Create Lifecycle Rule - Google Chrome 12-03-2026 22_33_09" src="https://github.com/user-attachments/assets/93c77469-15ed-432f-83fe-ad3d5a234277" />
After Cleanup

v3
v4
v5
<img width="1920" height="1020" alt="AWS CLI install fix - Google Chrome 12-03-2026 22_59_23" src="https://github.com/user-attachments/assets/fc86f2f1-bd68-4804-822c-e0bc30013978" />
Older images are automatically deleted.

## Step 10: Repository Security Policy
Access to the repository is restricted using IAM role.

Example repository policy:

```
{
  "Version": "2008-10-17",
  "Statement": [
    {
      "Sid": "AllowPushPullFromDevOpsRole",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::718383533665:role/DevOpsRole"
      },
      "Action": [
        "ecr:GetDownloadUrlForLayer",
        "ecr:BatchGetImage",
        "ecr:BatchCheckLayerAvailability",
        "ecr:PutImage"
      ]
    }
  ]
}
```
## Step 11: Security Validation
An unauthorized IAM user attempted to push an image to the repository.
 
 Make a IAM User
 <img width="1920" height="1020" alt="Create user _ IAM _ Global - Google Chrome 19-03-2026 00_02_33" src="https://github.com/user-attachments/assets/31ad5a5d-637a-4494-9d73-213488295a60" />
Pass the access key Result:
```
AccessDeniedException
```
This confirms that only authorized roles can push or pull images.
 <img width="1920" height="354" alt="ubuntu@ip-172-31-18-216_ ~_docker_project 12-03-2026 21_50_34" src="https://github.com/user-attachments/assets/c15edcb6-2080-4455-865f-b767278c5f79" />








  








