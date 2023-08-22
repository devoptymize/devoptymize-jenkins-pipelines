## The script to create ECR repository

## Overview

- This is a Jenkins script that create a public/private repository in AWS account.

- The pipeline includes the following steps:

- First, it takes user input in the form of credentials to provision the resources on an AWS account. The pipeline uses the ChoiceParameter to provide a list of credentials to choose from. The GroovyScript block defines the logic to fetch the credentials available in the Jenkins instance and returns a list of credentials associated with the user's login.

- The pipeline then create the ECR repository. It extracts the environment name from the input parameter and uses it to generate the names of the resources. With the Credentials block reads the access key and secret key from the AWS credentials associated with the account and uses them to run the CFT and TF commands that create the ECR repository.

- The agent any directive specifies that the pipeline can run on any agent machine with a specific label or without a label. In this case, it is not restricted to any agent machine.

- The environment block defines two environment variables that are derived from the user input. PROJECT_NAME is extracted from the credential name, and ACCOUNT_ID is extracted by splitting the credential name at the underscore (_) character.

- The pipeline has three stages:

  - The first stage cleans the workspace by removing any existing files from it. If the environment name parameter is empty, the pipeline stops and displays an error message. Otherwise, it sets the display name for the current build to include the project name, AWS account ID, and environment name.
  - The second stage used for SCM Checkout which instructs jenkins to obtain pipeline from SCM.
  - The third stage is to create the ECR repository in the defined region. It uses the if and else block to set the environment variables required for CloudFormationTemplate and Terraform. For CFT, The process will wait, it outputs a message indicating that the stack creation is still in progress and waits for 30 seconds before checking the status again, after the stack creation complete. it will commit the output file in ops_devoptimize repo. It then runs the CFT and TF commands to create the ECR repository.

- Overall, this script provides a way to automate the creation of resources required for Terraform state management,CloudFormation and resource locking in an AWS account.

#### Features

- Creating AWS network resources using IaC (Terraform/CloudFormation) templates with parameterized jenkins build.
- Can be used to Create and Delete network resources incase of Cloudformation and can Create, Modify and Delete network resources incase of Terraform.
- Can be used to define environment in which resource can be created.

| Parameters     |                                     Description                                                | Default Values  |
| :------------ |                                      :-----                                                     | :-------- |
| `ACTION`       | This parameter allows the user to select either Create or modify or delete a resources in the AWS account. This parameter will have list of actions such as Create, Modify and Delete.                    | `Create`   |
| `IAC_TOOL`     | This parameter allows the user to select one of the two IAC_TOOLS for creating the resource. The IAC-TOOLS which can be used are Cloudformation or Terraform | `Terraform`  |
| `CREDENTIAL`       | This parameter allows the user to select the credential which has necessary permission to create a resource in the AWS account                   | `project_xxxxxxxx_aws_cred`   |
| `ENVIRONMENT`       | The parameter allows the user to enter the Environment in which the required resource can be created. for example: dev and prod environment.                 | `dev`  |
| `REGION`       | This parameter allows the user to select the region in which the Network resources can be created. This parameter will have a list of all the regions upon which the user can select the desired region.                | `us-east-1`   |
| `ECR_REPO_NAME`       | This parameter allows the user to specify a custom name for the Amazon Elastic Container Registry (ECR) repository.                 
| `REPOSITORY_TYPE`       | This parameter allows the user to specify whether the ECR repository should be created as private or public. Please note that when using the commandline, public repositories can only be created in the us-east-1 (N. Virginia) region, regardless of the value specified for the REPOSITORY_TYPE parameter in the CloudFormation and Terraform template. | `private`  |  
| `REPOSITORY_IMAGE_TAG_MUTABILITY`       | This parameter allows the user to specify the tag mutability behavior for the ECR repository. There are two options for tag mutability IMMUTABLE and MUTABLE | `IMMUTABLE`  |   
| `SCAN_REPO`       | This parameter allows the user to specify whether image scanning should be enabled on ECR repository. When image scanning is enabled on a repository each image is scanned for known vulnerabilities after being pushed to the repository | `true`  | 
| `IMAGE_COUNT`       | This parameter enables users to pass a numeric value, representing the desired number of most recent images to retain in the ECR repository.
| `READ_ACCESS_ARNS`       | This parameter will used to list out the AWS Identity and Access Management Amazon Resource Names (ARNs) of users or roles that should have read access to the associated Amazon Elastic Container Registry (ECR) repository.  
| `READ_WRITE_ACCESS_ARNS`       | This parameter will used to list out the AWS Identity and Access Management Amazon Resource Names (ARNs) of users or roles that should have read write access to the associated Amazon Elastic Container Registry (ECR) repository.

### Contributing

We welcome contributions from the community to enhance the Jenkins pipeline. If you would like to contribute, please follow our guidelines outlined in the CONTRIBUTING.md file. You can submit feature requests, or pull requests to help us improve the template.

### License
