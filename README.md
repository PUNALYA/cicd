# AWS Fargate CI/CD Pipeline with Blue/Green Deployment

## 1. Project Overview

This project demonstrates the implementation of an automated Continuous Integration and Continuous Deployment (CI/CD) pipeline using Amazon Web Services (AWS).

It uses Amazon ECS Fargate, AWS CodeCommit, CodeBuild, CodeDeploy and CodePipeline to automate application deployment and demonstrate the Blue/Green deployment strategy.

## 2. Objectives

- Explore an application running on Amazon ECS Fargate.
- Configure CodeCommit as a source control repository.
- Create a CodeDeploy application and deployment group.
- Build an automated CI/CD pipeline using CodePipeline.
- Implement and observe Blue/Green deployment.

## 3. Technologies Used

| Technology | Purpose |
|---|---|
| Amazon ECS Fargate | Container execution |
| AWS CodeCommit | Source code management |
| AWS CodeBuild | Application build automation |
| AWS CodeDeploy | Deployment automation |
| AWS CodePipeline | CI/CD workflow |
| Amazon ECR | Docker image storage |
| Application Load Balancer | Traffic distribution |
| Git and GitHub | Version control and documentation |

## 4. Project Architecture

The CI/CD workflow follows this sequence:

1. Developer updates application source code.
2. CodeCommit stores the source code.
3. CodePipeline detects changes.
4. CodeBuild builds the Docker image.
5. Amazon ECR stores the image.
6. CodeDeploy manages application deployment.
7. ECS Fargate runs the application.
8. Application Load Balancer distributes traffic.

### Architecture Diagram
  
  Developer
           |
           v
     AWS CodeCommit
           |
           v
     AWS CodePipeline
           |
           v
      AWS CodeBuild
           |
           v
       Amazon ECR
           |
           v
      AWS CodeDeploy
           |
           v
     Amazon ECS Fargate
           |
           v
 Application Load Balancer
           |
       +---+---+
       |       |
       v       v
     BLUE    GREEN
   (Current) (Updated)

## 5. Lab Implementation

### Task 1: Explore the Application and Review Fargate Configuration

**Aim:** To explore the existing application and understand its Amazon ECS Fargate configuration.

**Procedure:**

1. Start the AWS lab environment and open the AWS Console.
2. Search for Amazon ECS.
3. Navigate to Clusters and select the existing cluster.
4. Open the Services tab and identify the application service.
5. Review desired tasks, running tasks and deployment status.
6. Navigate to Tasks and select a running task.
7. Examine CPU, memory, container image, networking and port configurations.
8. Open the associated Application Load Balancer.
9. Copy its DNS name and open it in a browser.
10. Verify that the application is running.

**Expected Output:** The application should be accessible through the load balancer, with ECS tasks running on Fargate.

**Result:** Successfully explored the application and reviewed its Fargate configuration.

---

### Task 2: Configure CodeCommit as a Source Control Repository

**Aim:** To create an AWS CodeCommit repository for managing application source code.

**Procedure:**

1. Open AWS CodeCommit.
2. Navigate to Repositories and click Create repository.
3. Enter the repository name as `Fargate-Application`.
4. Add a description and create the repository.
5. Copy the HTTPS clone URL.
6. Configure Git authentication using the supported AWS credentials.
7. Open Command Prompt or VS Code terminal.
8. Clone the repository.

**Git Commands:**

```bash
git clone <CODECOMMIT_REPOSITORY_URL>
cd Fargate-Application
```

Create the application files:

```text
Fargate-Application/
├── index.html
├── Dockerfile
├── appspec.yaml
├── taskdef.json
└── README.md
```

Add and push the files:

```bash
git add .
git commit -m "Initial application source code"
git push origin main
```

9. Open CodeCommit and verify the uploaded files and commit history.

**Expected Output:** Application source code is successfully stored in CodeCommit.

**Result:** Successfully created and configured AWS CodeCommit as the source control repository.

---

### Task 3: Create a CodeDeploy Application and Deployment Group

**Aim:** To configure AWS CodeDeploy for ECS Fargate Blue/Green deployment.

**Procedure:**

1. Open AWS CodeDeploy.
2. Navigate to Applications and click Create application.
3. Enter the name `Fargate-Application`.
4. Select Amazon ECS as the compute platform.
5. Create the application.
6. Open it and select Create deployment group.
7. Enter `Fargate-Deployment-Group`.
8. Select the appropriate IAM service role.
9. Choose the existing ECS cluster and service.
10. Configure the Application Load Balancer and production listener.
11. Configure two target groups for Blue and Green environments.
12. Select the Blue/Green deployment configuration.
13. Configure traffic rerouting and termination settings.
14. Review and create the deployment group.

**Expected Output:** A CodeDeploy application and deployment group connected to the ECS Fargate service.

**Result:** Successfully created an AWS CodeDeploy application and deployment group.

---

### Task 4: Build the CI/CD Pipeline

**Aim:** To create an automated CI/CD pipeline using AWS CodePipeline.

**Procedure:**

1. Open AWS CodePipeline.
2. Click Create pipeline.
3. Enter the pipeline name as `Fargate-CICD-Pipeline`.
4. Configure the pipeline settings and IAM service role.

**Source Stage:**

5. Select AWS CodeCommit as the source provider.
6. Choose the `Fargate-Application` repository.
7. Select the `main` branch.
8. Configure the source trigger.

**Build Stage:**

9. Select AWS CodeBuild as the build provider.
10. Create a project named `Fargate-Build-Project`.
11. Configure the build environment and permissions.
12. Configure Docker image building and Amazon ECR authentication.
13. Build, tag and push the Docker image.

**Example Commands:**

```bash
docker build -t fargate-app .
```

```bash
docker tag fargate-app:latest \
<ACCOUNT_ID>.dkr.ecr.<REGION>.amazonaws.com/fargate-app:latest
```

```bash
docker push \
<ACCOUNT_ID>.dkr.ecr.<REGION>.amazonaws.com/fargate-app:latest
```

**Deploy Stage:**

14. Select the Amazon ECS Blue/Green deployment provider using CodeDeploy.
15. Select the CodeDeploy application and deployment group.
16. Configure the deployment artifacts:
    - `appspec.yaml`
    - `taskdef.json`
    - `imageDetail.json`
17. Review and create the pipeline.
18. Verify the Source, Build and Deploy stages.

**Expected Output:** An automated pipeline that retrieves source code, builds the Docker image and deploys the updated application.

**Result:** Successfully created an automated CI/CD pipeline using CodePipeline, CodeBuild and CodeDeploy.

---

### Task 5: Observe a Blue/Green Deployment

**Aim:** To implement and observe Blue/Green deployment using AWS CodeDeploy and ECS Fargate.

**Introduction:**

Blue/Green deployment is a strategy that maintains two application environments:

- **Blue:** Existing application version.
- **Green:** Newly updated application version.

The new version is deployed and verified before production traffic is transferred.

**Procedure:**

1. Open Amazon ECS and select the existing cluster and service.
2. Verify that the current application tasks are running.
3. Open the application using the load balancer DNS name.
4. Record the existing application version (Blue environment).
5. Modify a visible element in the application source code.

**Example HTML Update:**

```html
<h1>Welcome to Green Environment</h1>
<p>Application successfully updated!</p>
```

6. Save and push the changes to CodeCommit.

```bash
git add .
git commit -m "Update application version"
git push origin main
```

7. Open AWS CodePipeline and observe the new execution.
8. Verify the Source and Build stages.
9. Observe the Deploy stage.
10. Open the ECS service and check the new replacement task set.
11. Verify that the Green environment tasks are running.
12. Check target group health in the EC2 Load Balancing console.
13. Observe traffic shifting through CodeDeploy.
14. Open the application using the load balancer DNS name.
15. Verify that the updated application is displayed.
16. Confirm the deployment status in CodeDeploy.

**Rollback:**

If deployment fails, the configured rollback mechanism can restore the previous working application version.

**Expected Output:** The updated application runs in the Green environment, and production traffic is shifted from Blue to Green.

**Result:** Successfully observed and verified Blue/Green deployment using AWS CodeDeploy and ECS Fargate.

---

## 6. Results and Observations

The lab demonstrates the implementation of an AWS-based CI/CD workflow.

- Explored ECS Fargate configuration.
- Created a CodeCommit repository.
- Configured a CodeDeploy application and deployment group.
- Built an automated CI/CD pipeline.
- Used Amazon ECR for Docker image storage.
- Observed Blue/Green deployment and traffic shifting.

## 7. Learning Outcomes

1. Understanding container deployment using ECS Fargate.
2. Managing source code using CodeCommit.
3. Automating builds using CodeBuild.
4. Implementing CI/CD using CodePipeline.
5. Automating deployment using CodeDeploy.
6. Understanding Docker image management using ECR.
7. Learning Blue/Green deployment and rollback mechanisms.


## 8. Conclusion

This lab provided practical experience in implementing an automated CI/CD pipeline using AWS services. CodeCommit was used for source code management, CodeBuild for building Docker images, CodePipeline for workflow automation and CodeDeploy for application deployment.

The Blue/Green deployment strategy demonstrated how updated application versions can be deployed and verified before transferring production traffic, helping minimize downtime and supporting rollback.

## 9. References

- [Amazon ECS Documentation](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/)
- [AWS Fargate Documentation](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/AWS_Fargate.html)
- [AWS CodeCommit Documentation](https://docs.aws.amazon.com/codecommit/latest/userguide/)
- [AWS CodeBuild Documentation](https://docs.aws.amazon.com/codebuild/latest/userguide/)
- [AWS CodeDeploy Documentation](https://docs.aws.amazon.com/codedeploy/latest/userguide/)
- [AWS CodePipeline Documentation](https://docs.aws.amazon.com/codepipeline/latest/userguide/)

---

## Author

**Punalya N**

B.Tech – Computer Science and Engineering (AI & ML)

---

*AWS DevOps Lab – CI/CD Pipeline with CodeCommit, CodeDeploy, Fargate and Blue/Green Deployment.*
