# Jenkins Deployment on AWS ECS using Terraform
This Terraform configuration deploys a Jenkins master and worker server on AWS ECS using Fargate. It leverages various AWS services, including ALB, ECR, ECS, EFS, IAM, CloudWatch Logs, and Route 53.

### Modules: 

Contains reusable Terraform modules for different AWS resources.
#### Root Module: 
Coordinates the deployment of all modules to set up Jenkins.
#### Folder Structure
1. modules/: Contains subfolders for each AWS resource, such as ALB, ECR, ECS, IAM, Security Groups, CloudWatch Logs, etc.
2. deploy_jenkins_terraform/: Contains the root module files, including the main configuration file (main.tf), variables file (variables.tf), provider configurations (providers.tf), and variable definitions (terraform.tfvars).


#### Jenkins Configuration as Code (JCasC)
`What is JCasC ?`

Jenkins Configuration as Code (JCasC) is a feature that allows you to define Jenkins configuration in a declarative manner using YAML. This approach ensures that Jenkins configurations are reproducible and version-controlled.

`JCasC in This Project`

In this project, the JCasC file is dynamically generated during the Docker image build process. The jcasc.tftpl template file defines the Jenkins configuration, including the setup of ECS workers, security realms, authorization strategies, and job definitions.

`Steps for JCasC Generation and Usage and Template Rendering:`



The jcasc.tftpl template file uses variables defined in Terraform to generate the final jenkins.yaml file.
These variables include ECS cluster ARNs, security groups, subnets, task roles, execution roles, and more.
Docker Image Creation:

A null_resource in the ECR module uses the local-exec provisioner to render the jcasc.tftpl template into a jenkins.yaml file.
The Dockerfile then copies this jenkins.yaml file into the Jenkins image at the appropriate location (/usr/share/jenkins/ref/jenkins.yaml).

#### Jenkins Container Configuration:

When the Jenkins container starts, it reads the jenkins.yaml file and configures itself accordingly.
An ECS user (ecsuser) is created with a password stored as an environment variable (JENKINS_ADMIN_PWD).
This password value is securely fetched from AWS Secrets Manager and injected into the Jenkins container at runtime.



#### Notes
1. Make sure AWS credentials are configured properly.
2. Update terraform.tfvars with your specific values.
3. The Jenkins configuration is handled via a JCasC file, which is rendered from a template and included in the Docker image build process.
4. To keep it working before every terraform apply remove the jenkins.yaml JCASC file so that this file gets from scratch everytime.
5. Ensure the required IAM roles and policies are correctly set up for your AWS environment.
6. Ensure that Certificate manager has rewuired certifictes generated before the Terraform apply and also create AWS Hosted ZOne 