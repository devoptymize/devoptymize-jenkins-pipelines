## The script to create the load balancer 

### Overview

- This is a Jenkins pipeline script that can provision load balancer in an AWS account and maintain the IAC script for the same.

- The pipeline includes the following steps:

- First, it takes user input in the form of credentials to provision the resources on an AWS account. The pipeline uses the ChoiceParameter to provide a list of credentials to choose from. The GroovyScript block defines the logic to fetch the credentials available in the Jenkins instance and returns a list of credentials associated with the user's login.

- The pipeline then create a load balancer. It extracts the environment name from the input parameter and uses it to generate the names of the resources. The withCredentials block reads the access key and secret key from the AWS credentials associated with the account and uses them to run the CFT and TF commands that create the load balancer.

- The agent any directive specifies that the pipeline can run on any agent machine with a specific label or without a label. In this case, it is not restricted to any agent machine.

- The environment block defines two environment variables that are derived from the user input. PROJECT_NAME is extracted from the credential name, and ACCOUNT_ID is extracted by splitting the credential name at the underscore (_) character.

- The pipeline has three stages:

  - The first stage cleans the workspace by removing any existing files from it. If the environment name parameter is empty, the pipeline stops and displays an error message. Otherwise, it sets the display name for the current build to include the project name, AWS account ID, and environment name.
  - The second stage used for SCM Checkout which instructs jenkins to obtain pipeline from SCM 
  - The third stage create the load balancer in the defined region. It uses the if and else block to set the environment variables required for CloudFormationTemplate and Terraform. It then runs the CFT and TF  commands to create the loadbalancer.

- Overall, this script provides a way to automate the creation of resources required for Terraform state management,CloudFormation and resource locking in an AWS account.


### Features:
- Creating AWS Load Balancer using IaC (Terraform/CloudFormation) templates with parameterized jenkins build.
- Can be used to Create and Delete Load Balancer incase of Cloudformation and can Create, Modify and Delete Load Balancer incase of Terraform.
- Can be used to define environment in which resource can be created.

### Parameters:
Once you have the jenkins set up is done create a Job with the resource specified jenkins file. Then select the **Build with Parameters** in which the following parameters have to be specified.


| Parameters     |                                     Description                                                | Default Values TF | Default Values CFT |
| :------------ |                                      :-----                                                     | :-------- | :-------- |
| `Action`       | This parameter can be used to either Create or Modify or Delete a resource                     | `Create`  | `Create` |
| `IAC_TOOL`     | Through this parameter, either Terraform or Cloudformation is used to create, modify or delete a resource | `Terraform`  | `CloudFormation` |
| `CREDENTIAL`       | This parameter can be used to either Create or Modify or Delete a resource                     | For example: `project_123456789012_aws_cred`  | `project_123456789012_aws_cred` |
| `Environment`       | This parameter can be used to specify the Environment in which resources can be created                   |   |
| `Region`       | This parameter can be used to specify the AWS region in which the resources are to be created.                  | `us-east-1` | `us-east-1` |
| `LOADBALANCER_TYPE`       | This parameter can be used to specify the type of the Load Balancer which has to be created       | `Application_Load_Balancer` |
| `LOADBALANCER_NAME`       | This parameter can be used to create the name of the Load Balancer       |  |
| `SCHEME`       | This parameter can be used to choose the type of scheme for  the Load Balancer      | `internet-facing` | `internet-facing` |
| `IP_ADDRESS_TYPE`       | This parameter can be used to specify the type of IP Address       | `ipv4` | `ipv4` |
| `VPC_NAME`       | This parameter can be used to select the VPC in which Load Balancer to be deployed       | `Default` | `Default` |
| `SUBNET_NAME`       | This parameter can be used to specify the subnets for Load Balancer       | |
| `SECURITY_GROUP_NAME`       | This parameter can be used to select the security group which needs to be attached to Load Balancer      |  |
| `PROTOCOL`       | This parameter can be used to to select the protocol which needs to be attached to Load Balancer       | `HTTP` | `HTTP` |
| `PORT`       | This parameter can be used to specify the port for listener        |  |
| `TARGET_GROUP_ARN`       |This parameter can be used to select the target group  which needs to be attached to Load Balancer       |  |
| `SECURITY_POLICY`       | This parameter can be used to select the security policy that need to be attached with  Load Balancer       | `null` | `null` |
| `CERTIFICATE_TYPE`       | This parameter can be used to select the options for SSL/TLS Certificate       | `null`| `null` |
| `CERTIFICATE_NAME`       | This parameter can be used to select the certificate for the Load Balancer | `null` | `null` |
| `ALPN_POLICY`            | This parameter can be used to select the ALPN policy for the Load balancer | `null` | `null` |


### Contributing
We welcome contributions from the community to enhance the Jenkins pipeline. If you would like to contribute, please follow our guidelines outlined in the [CONTRIBUTING.md](./CONTRIBUTING.md) file. You can submit feature requests, or pull requests to help us improve the template.

### License
