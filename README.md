## The script to create the EC2 

# Overview

- This is a Jenkins pipeline script that create EC2 and maintaining a lock on the resources provisioned in an AWS account.

- The pipeline includes the following steps:

- First, it takes user input in the form of credentials to provision the resources on an AWS account. The pipeline uses the ChoiceParameter to provide a list of credentials to choose from. The GroovyScript block defines the logic to fetch the credentials available in the Jenkins instance and returns a list of credentials associated with the user's login.

- The pipeline then create an EC2. It extracts the environment name from the input parameter and uses it to generate the names of the resources. The withCredentials block reads the access key and secret key from the AWS credentials associated with the account and uses them to run the CFT and TF commands that create the EC2.

- The agent any directive specifies that the pipeline can run on any agent machine with a specific label or without a label. In this case, it is not restricted to any agent machine.

- The environment block defines two environment variables that are derived from the user input. PROJECT_NAME is extracted from the credential name, and ACCOUNT_ID is extracted by splitting the credential name at the underscore (_) character.

- The pipeline has three stages:

  - The first stage cleans the workspace by removing any existing files from it. If the environment name parameter is empty, the pipeline stops and displays an error message. Otherwise, it sets the display name for the current build to include the project name, AWS account ID, and environment name.
  - The second stage used for SCM Checkout which instructs jenkins to obtain pipeline from SCM 
  - The third stage is to create the EC2 in the defined region. It uses the if and else block to set the environment variables required for CloudFormationTemplate and Terraform. It then runs the CFT and TF  commands to create the EC2.

- Overall, this script provides a way to automate the creation of resources required for Terraform state management,CloudFormation and resource locking in an AWS account.



## DevOptymize_Jenkins_Pipelines Git repository:
A DevOptymize_Jenkins_Pipelines Git repository refers to a source code repository that contains the definition of a Jenkins pipeline which is configured to create the EC2 resources in the defined region.

### Features:
- Creating AWS EC2 resources using IaC (Terraform/CloudFormation) templates with parameterized jenkins build.
- Can be used to Create and Delete EC2 resources with both Terraform and CloudFormation templates.
- Can be used to Create both Linux and Windows servers with and without the additional volumes.
- Can be used to Create and Delete the EC2 resources in the user specified region.

### Parameters:
Once you have the jenkins set up is done create a Job with the resource specified jenkins file. Then select the **Build with Parameters** in which the following parameters have to be specified.


| Parameters     |                                     Description                                                | Default Values  |
| :------------ |                                      :-----                                                     | :-------- |
| `IAC_TOOL`     | This parameter allows the user to select one of the two IAC_TOOLS for creating the resource. The IAC-TOOLS which can be used are Cloudformation or Terraform  | `Terraform`  |
| `OPERATINGSYSTEM` | This parameter allows the user to select the OS (Operating system) for the EC2 resource. And this will list only Linux and Windows operating system. For example When the user chooses the terraform as an IAC then this parameter will not show the list of operating system and it will take the input from AMI_ID. If the user chooses CloudFormation as an IAC then we can choose it from the list.      |  `Windows`             |
| `Action`       | This parameter allows the user to select either Create or modify or delete a resources in the AWS account. This parameter will have list of actions such as Create, Modify and Delete.                  | `Create`   |
| `CREDENTIAL`       | This parameter allows the user to select the credential which has necessary permission to create a resource in the AWS account.                       |  For example:`devoptymize_12345678_aws_cred`   |
| `Region`       | This parameter allows the user to select the region in which the EC2 resources can be created. This parameter will have a list of all the regions upon which the user can select the desired region.         | `us-east-1`   |
| `Environment`       | The parameter allows the user to enter the Environment in which the required resource can be created. for example: dev and prod environment.                   | `prod`  |
| `INSTANCE_NAME`   | This parameter allows the user to give name for their instance which will be creating through this pipeline. |          |
| `AMI_ID`          | This parameter allows the user to pass the AMI ID of EC2 instance.                                 |                  |
| `INSTANCE_TYPE`   | This parameter allows the user to select the type of the instance which is need to be created. And it will list all available types in this which is easy for the user to choose specifically. |                             |
| `KEY_PAIR`        | This parameter allows the user to choose the key pair for the instance which is available.       |                       |
| `VPC_NAME`       | This parameter allows the user to select the VPC for launching the EC2. It will list all the VPC based on the region which user selects. |  |
| `SUBNET_NAME`     |This parameter allows the user to choose the subnets for their EC2 resources. And it will list all the subnets which is available.                   |                |
| `SECURITY_GROUP_NAME` | This parameter allows the user to choose the security groups for the EC2 resource which they are going to create.  |   |
| `ROOT_VOLUME_SIZE` | This parameter allows the user to specify the root volume for the EC2 resource.                 |                       |
| `ADDITIONAL_VOLUME` |  This parameter allows the user to choose if the additional volume is needed or not. If the user chooses "YES" then the ADDITIONAL_VOLUME_SIZE parameter will be opened to give values.           |  `Yes`                |
| `ADDITIONAL_VOLUME_SIZE` | This parameter allows the user to specify the additional volumes which is needed for the EC2 resource. For example When the user selects the terraform as a IAC tool to create the resource, the user needs to pass the value ["40"] in this format. If the user chooses CloudFormation then value will be passed as 40   |        |

### Contributing
We welcome contributions from the community to enhance the Jenkins pipeline. If you would like to contribute, please follow our guidelines outlined in the CONTRIBUTING.md file. You can submit feature requests, or pull requests to help us improve the template.


### License
