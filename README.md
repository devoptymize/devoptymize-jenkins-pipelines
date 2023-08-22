## The script to create the launch template

# Overview

- This is a Jenkins pipeline script that creates the launch template for creating and managing instances within Amazon EC2 (Elastic Compute Cloud).

- Overall, this script provides a way to automate the creation of launch template for creating and managing instances within Amazon EC2 (Elastic Compute Cloud).

- The pipeline includes the following steps

- First, it takes user input in the form of credentials to provision the resources on an AWS account. The pipeline uses the ChoiceParameter to provide a list of credentials to choose from. The GroovyScript block defines the logic to fetch the credentials available in the Jenkins instance and returns a list of credentials associated with the user's login.

- The pipeline then creates a launch template. It extracts the environment name from the input parameter and uses it to generate the names of the resources. The withCredentials block reads the access key and secret key from the AWS credentials associated with the account and uses them to authenticate the aws CLI commands that create the launnch template.

- The agent any directive specifies that the pipeline can run on any agent machine with a specific label or without a label. In this case, it is not restricted to any agent machine.

- The environment block defines two environment variables that are derived from the user input. PROJECT_NAME is extracted from the credential name, and ACCOUNT_ID is extracted by splitting the credential name at the underscore (_) character.

- The environment section defines environment variables:
    - resource is set to "launch_template".
    - resource_name is set to the value of the environment variable LAUNCH_TEMPLATE_NAME
    - aws_region is set to the value of the environment variable 
    - REGION with commas removed.
    - PROJECT_NAME is set by running a shell command that extracts the client name from the parameter  CREDENTIAL
    - ACCOUNT_ID is set by running a shell command that extracts the account ID from the parameter CREDENTIAL  

- The pipeline has three stages

    - The first stage cleans the workspace by removing any existing files from it. If the environment name parameter is empty, the pipeline stops and displays an error message. Otherwise, it sets the display name for the current build to include the project name, AWS account ID, and environment name.
    - The second stage is the SCM checkout stage where the code will be cloned from the Source repository.
    - The third stage creates the launch template. It uses the if and else block to set the environment variables required for CloudFormationTemplate and Terraform. For CFT, The process will wait, it outputs a message indicating that the stack creation is still in progress and waits for 30 seconds before checking the status again, after the stack creation complete. it will commit the output file in ops_devoptimize repo. It then runs the CFT and TF  commands to create the Launchtemplate.
- Overall, this script provides a way to automate the creation of resources required for Terraform state management,CloudFormation and resource locking in an AWS account.

## DevOptymize_Jenkins_Pipelines Git repository:
A DevOptymize_Jenkins_Pipelines Git repository refers to a source code repository that contains the definition of a Jenkins pipeline which is configured to create the launch template in the defined region.


### Parameters:
Once you have the jenkins set up is done create a Job with the resource specified jenkins file. Then select the **Build with Parameters** in which the following parameters have to be specified.


| Parameters     |                                     Description                                                | Default Values  |
| :------------ |                                      :-----                                                     | :-------- |
| `IAC_TOOL`     | Through this parameter, either Terraform or Cloudformation is used to create, modify or delete a resource | `Terraform`  |
| `OPERATINGSYSTEM`     | This parameter can be used to specify Linux or windows if IAC_TOOL is Cloudformation. Incase of Terraform, no need to specify OS  | -  |
| `Action`       | This parameter can be used to either Create or Modify or Delete a resource                     | `Create`   |
| `CREDENTIAL`       | This parameter can be used to either Create or Modify or Delete a resource                     |     For example: `project_123456789012_aws_cred`   |
| `Region`       | This parameter can be used to specify the AWS region in which the resources are to be created.                  | `us-east-1`   |
| `Environment`       | This parameter can be used to specify the Environment in which resources can be created                   |   |
| `Launch_Template_Name` | This parameter can be used to specify the name of the launch template which is to be created.    |    |
| `AMI_ID`       | This parameter can be used to specify the AWS ami id which will be used in creation of EC2 when using launch template                  |    |
| `INSTANCE_TYPE`       | This parameter can be used to specify the type of Instance which will be used in creation of EC2 when using launch template               | `r6a.2Xlarge`   |
| `VPC_NAME`       | This parameter can be used to select the VPC from the list of VPCs within the selected region in the Region Parameter |  |
| `SUBNET_NAME`       | This parameter can be used to select the subnet from the list of subnetss within the selected VPC in the VPC Parameter |  |
| `Security_group_name`       | This parameter can be used to specify the name of the security group which has to be created       |  |
| `KEY_PAIR`       | This parameter can be used to select the keypair from the list of Keypairs within the selected region in the Region Parameter |  |
| `VOLUME_SIZE`       | This parameter can be used to enter the size of the volumes which is to be included in the launch template. For Cloudfromation, it has to be in string separated by comma (8,10). For Terraform, it should be in list of strings ["8","10"] |  |
| `IAM_INSTANCE_PROFILE`       | This parameter can be used to select the instance profile which is to be included in the launch template.       |   |
| `USER_DATA`       | This parameter can be used to specify the provide the User data script to get executed while launching an instance.                 |   |

## The actions that can be performed through the pipeline are,
- Create Launch Template
- Modify Launch Template
- Delete Launch Template

### Create Launch Template
- Select action as Create
- Select the credential to which the launch template will be provisioned
- Enter the environment name.
- Select the region in which launch template needs to be created.
- Enter the name for the launch template.
- Enter the AMI ID  which needs to be attached to launch template. Enter atleast one AMI ID for the launch template to be created.
- Enter Launch Template Instance type in which the resources need to be provisioned. Select null if you Don't want to include instance type in launch template
- Select the VPC in which the Launch Template Instance needs to be provisioned. The default VPC are showed up as None. Select null if you Don't want to include VPC, subnet and security group in launch template.
- Select Subnet in which the Launch Template instance needs to be launched. Select null if you Don't want to include subnet in launch template.
- Select Security group which needs to be attached to Launch Template. if u need to create a new security group please use SECURITYGROUP-PIPELINE. Don't Select security groups if you Don't want to include security group in launch template.
- Select Key pair which needs to used to provision Launch Template, if u need to use a new key pair please create a new key-pair using KEY-PAIR-PIPELINE. Select null if you Don't want to include key pair in launch template.
- Enter the storage volume size to include it in the launch template, whose type will be GP3. For Linux flavour the size should be >= 8GB & for windows flavour the size should be >= 30GB. The format of input incase of Terraform, if u require two additional volumes is ["10","20"]. The format of input incase of Cloudformation, if u require two additional volumes is 10,20
- Select The IAM instance profile for the instance. Select null if you Don't want to include IAM instance profile in launch template
- Specify user data to provide commands or a command script to run when you launch your instance. The format of the input incase of Terraform should be <<EOT scritp-in-new-line EOT. Example script,
 ```sh            
 <<EOT
    #! /bin/bash
    sudo apt-get update
    sudo apt-get install -y apache2
    sudo systemctl start apache2
    sudo systemctl enable apache2
    echo "The page was created by the user data" | sudo tee /var/www/html/index.html
EOT
```
The format of Cloudformation is 
```sh
#! /bin/bash sudo apt-get update sudo apt-get install -y apache2
```
###  Modify Launch Template
- Select action as Modify
- Select the credential in which the launch template exists
- Enter the environment and the launch template name.
- Select the region in which launch template needs to be created.
- Enter the name for the launch template.
- Enter the AMI ID  which needs to be attached to launch template. Enter atleast one AMI ID for the launch template to be created.
- Enter Launch Template Instance type in which the resources need to be provisioned. Select null if you Don't want to include instance type in launch template
- Select the VPC in which the Launch Template Instance needs to be provisioned. The default VPC are showed up as None. Select null if you Don't want to include VPC, subnet and security group in launch template.
- Select Subnet in which the Launch Template instance needs to be launched. Select null if you Don't want to include subnet in launch template.
- Select Security group which needs to be attached to Launch Template. if u need to create a new security group please use SECURITYGROUP-PIPELINE. Don't Select security groups if you Don't want to include security group in launch template.
- Select Key pair which needs to used to provision Launch Template, if u need to use a new key pair please create a new key-pair using KEY-PAIR-PIPELINE. Select null if you Don't want to include key pair in launch template.
- Enter the storage volume size to include it in the launch template, whose type will be GP3. For Linux flavour the size should be >= 8GB & for windows flavour the size should be >= 30GB. The format of input incase of Terraform, if u require two additional volumes is ["10","20"]. The format of input incase of Cloudformation, if u require two additional volumes is 10,20
- Select The IAM instance profile for the instance. Select null if you Don't want to include IAM instance profile in launch template
- Specify user data to provide commands or a command script to run when you launch your instance.

###  Delete Launch Template
- Select action as Delete
- Select the credential in which the launch template exists
- Enter the environment and the launch template name.
- Select the region in which launch template needs to be deleted.

### Contributing
We welcome contributions from the community to enhance the Jenkins pipeline. If you would like to contribute, please follow our guidelines outlined in the CONTRIBUTING.md file. You can submit feature requests, or pull requests to help us improve the template.

### License
