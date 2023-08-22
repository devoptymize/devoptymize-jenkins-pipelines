## The script to create the Security group for attaching it to required resources.

# Overview

- This is a Jenkins pipeline script that creates security group for attaching it to required resources later on.

- Overall, this script provides a way to automate the creation of security groups for attaching it to the AWS resources if and when required.

- The pipeline includes the following steps

- First, it takes user input in the form of credentials to provision the resources on an AWS account. The pipeline uses the ChoiceParameter to provide a list of credentials to choose from. The GroovyScript block defines the logic to fetch the credentials available in the Jenkins instance and returns a list of credentials associated with the user's login.

- Second, it takes user input for security group name and description. The pipeline uses the string Parameter to provide a security group name and description.

- The pipeline then creates the security group. It extracts the environment name from the input parameter and uses it to generate the names of the resources. The withCredentials block reads the access key and secret key from the AWS credentials associated with the account and uses them to authenticate the aws CLI commands that security group.

- The agent any directive specifies that the pipeline can run on any agent machine with a specific label or without a label. In this case, it is not restricted to any agent machine.

- The environment block defines two environment variables that are derived from the user input. PROJECT_NAME is extracted from the credential name, and ACCOUNT_ID is extracted by splitting the credential name at the underscore (_) character.

- The pipeline has 3 stages

- The first stage cleans the workspace by removing any existing files from it. If the environment name parameter is empty, the pipeline stops and displays an error message. Otherwise, it sets the display name for the current build to include the project name, AWS account ID, and environment name.
- The second stage is the SCM checkout stage where the code will be cloned from the Source repository.
- The third stage creates the Security group. It uses the withCredentials block to set the environment variables required for authentication with the AWS CLI. It then runs the aws CLI commands to create the security group.

## DevOptymize_Jenkins_Pipelines Git repository:
A DevOptymize_Jenkins_Pipelines Git repository refers to a source code repository that contains the definition of a Jenkins pipeline which is configured to create the security group in the defined region.

## Features:

- Creating AWS security group using IaC (Terraform/CloudFormation) templates with parameterized jenkins build.
- Can be used to Create and Delete security group incase of Cloudformation and can Create, Modify and Delete security group incase of Terraform.

### Parameters:
Once you have the jenkins set up is done create a Job with the resource specified jenkins file. Then select the **Build with Parameters** in which the following parameters have to be specified.


| Parameters     |                                     Description                                                | Default Values  |
| :------------ |                                      :-----                                                     | :-------- |
| `Action`       | This parameter can be used to either Create or Modify or Delete a resource                     | `Create`   |
| `IAC_TOOL`     | Through this parameter, either Terraform or Cloudformation is used to create, modify or delete a resource | `Terraform`  |
| `CREDENTIAL`       | This parameter can be used to either Create or Modify or Delete a resource                     | `project_123456789012_aws_cred`   |
| `Environment`       | This parameter can be used to specify the Environment in which resources can be created                   | `prod`  |
| `Region`       | This parameter can be used to specify the AWS region in which the resources are to be created.                  | `us-east-1`   |
| `Security_group_name`       | This parameter can be used to specify the name of the security group which has to be created       |  |
| `Description`       | This parameter can be used to specify the description of the Security group which has to be created        |   |
| `VPC_NAME`       | This parameter can be used to select the VPC from the list of VPCs within the selected region in the Region Parameter |  |
| `INGRESSRULES`       | This parameter can be used to specify the Ingress rules or Inbound rules for the security group                   |   |



### Contributing
We welcome contributions from the community to enhance the Jenkins pipeline. If you would like to contribute, please follow our guidelines outlined in the CONTRIBUTING.md file. You can submit feature requests, or pull requests to help us improve the template.

### License
