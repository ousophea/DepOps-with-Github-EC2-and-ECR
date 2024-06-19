# DepOps-with-Github-EC2-and-ECR

## Integrating with GitHub Actions – CICD pipeline to Deploy a Web App to Amazon EC2

1- Prepare node project
    a. Create new project
    b. Check local service is work fine?
    c. Push to Github repository (initial) 
    d. Prepare Docker in Github
        - /.github/workflow/docker-images.yml
            □ Code " 
    e. Add secret in Github
        i. Setting -->  secrets and variables --> Action --> New Repository secrets (image 1)
    f. Test push to Github test pipeline access
    g. Create Runner
        i. Setting --> runner --> new runner (image 2)
    
2- Create EC2
    a. Ubutus
        i. Anable : 80 , 443
        ii. Update EC2
            1) sudo apt update
            2) sudo apt-get upgrade -y
            3) Add ubuntu user to docker
                a) sudo usermod -a -G docker  ubuntu --> request logout
        iii. Install docker
        iv. Install nginx 
            1) Config proxy
        v. Login to docker regestry (dockehub, nexus…)
        vi. Check docker images

            
        i. Install Github runer
            1) Code ???
            2) Go to Github to check runner is connect
            
3- Create CD pipeline
    a. Go to project directory -- > create  cd.yml
    b. Access to EC2 with github action 




## Solution Overview

The solution utilizes following services:

1.	[GitHub Actions](https://docs.github.com/en/actions) : Workflow Orchestration tool that will host the Pipeline. 
2.	[AWS CodeDeploy](https://aws.amazon.com/codedeploy/) : AWS service to manage deployment on Amazon EC2 Autoscaling Group.
3.	[AWS Auto Scaling](https://aws.amazon.com/ec2/autoscaling/) : AWS Service to help maintain application availability and elasticity by automatically adding or removing EC2 instances. 
4.	[Amazon EC2](https://docs.aws.amazon.com/ec2/index.html?nc2=h_ql_doc_ec2#amazon-ec2) : Destination Compute server for the application deployment.
5.	[AWS CloudFormation](https://aws.amazon.com/cloudformation/) : AWS infrastructure as code (IaC) service used to spin up the initial infrastructure on AWS side.
6.	[IAM OIDC identity provider](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_oidc.html) : Federated authentication service to establish trust between GitHub and AWS to allow GitHub Actions to deploy on AWS without maintaining AWS Secrets and credentials. 
7.	[Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html) : Amazon S3 to store the deployment artifacts.

The following diagram illustrates the architecture for the solution:
![Alt Text](aws-coodedeplooy-github-action-deploymentV3.png?raw=true  "Title")

## Prerequisites
Before you begin, you need to complete the following prerequisites:
    
   * An AWS account with permissions to create the necessary resources.
   * A [Git Client](https://git-scm.com/downloads) to clone the provided source code.
   * A [GitHub account](https://github.com/) with permissions to configure GitHub repositories, create workflows, and configure GitHub secrets.

## Walkthrough
The following steps provide a high-level overview of the walkthrough:

  1.	Clone the project from the AWS code samples repository.
  2.	Deploy the AWS CloudFormation template to create the required services.
  3.	Update the source code.
  4.	Setup GitHub secrets.
  5.	Integrate CodeDeploy with GitHub
  6.	Trigger the GitHub Action to build and deploy the code.
  7.	Verify the deployment.

## Download the source code

Clone this repository aws-codedeploy-github-actions-deployment 

    git clone https://github.com/aws-samples/aws-codedeploy-github-actions-deployment.git

Create an empty repository in your personal GitHub account.

    git clone https://github.com/<username>/<repoName>.git
    
Copy the code. We need contents from the hidden .github folder for the GitHub actions to work.

    cp -r aws-codedeploy-github-actions-deployment/. <new repository>

    e.g. GitActionsDeploytoAWS

## Deploying the CloudFormation template
To deploy the CloudFormation template, complete the following steps:

    1.	Open AWS CloudFormation console. Enter your account ID, user name and Password. 
    2.	Check your region, this solution uses us-east-1.
    3.	If this is  new AWS CloudFormation account, click Create New Stack. Otherwise, select Create Stack.
    4.	Select Template is Ready
    5.	Click Upload a template file
    6.	Click Choose File. Navigate to template.yml file in your cloned repository at “aws-codedeploy-github-actions-deployment/cloudformation/template.yaml” 
    7.	Select the template.yml file and select next.
    8.	In Specify Stack Details, add or modify values as needed.
            - Stack name = CodeDeployStack.
            - VPC and Subnets = (these are pre-populated for you) you can change these values if you prefer to use your own Subnets)
            - GitHubThumbprintList = 6938fd4d98bab03faadb97b34396831e3780aea1
            - GitHubRepoName – Name of your GitHub personal repository which you created.
    9.	On the Options page, click Next.
    10.	Select the acknowledgement box to allow the creation of IAM resources, and then select Create. 
    It will take CloudFormation about 5 minutes to create all the resources. This stack would create below resources.
           - Two EC2 Linux instances with Tomcat server and CodeDeploy agent installed 
           - Autoscaling group with Internet Application load balancer
           - CodeDeploy application name and deployment group
           - S3 bucket to store build artifacts
           - Identity and Access Management (IAM) OIDC identity provider
           - Instance profile for Amazon EC2 
           - Service role for CodeDeploy
           - Security groups for ALB and Amazon EC2

## GitHub configuration and Testing

Please follow the [blog post](https://aws.amazon.com/blogs/devops/integrating-with-github-actions-ci-cd-pipeline-to-deploy-a-web-app-to-amazon-ec2/) to setup GitHub actions and test the CICD flow.

## Clean up

To avoid incurring future changes, you should clean up the resources that you created.

    1. Empty the Amazon S3 bucket:
    2. Delete the CloudFormation stack (CodeDeployStack) from the AWS console.
    3. Delete the GitHub Secret (‘IAMROLE_GITHUB’)
        1. Go to the repository settings on GitHub Page.
        2. Select Secrets under Actions.
        3. Select IAMROLE_GITHUB, and delete it.


## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.
