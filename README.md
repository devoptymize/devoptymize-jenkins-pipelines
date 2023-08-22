## The script to create the RDS Instance 

# Overview

- This is a Jenkins pipeline script that create RDS instances in an AWS account.

- The pipeline includes the following steps:

- First, it takes user input in the form of credentials to provision the resources on an AWS account. The pipeline uses the ChoiceParameter to provide a list of credentials to choose from. The GroovyScript block defines the logic to fetch the credentials available in the Jenkins instance and returns a list of credentials associated with the user's login.

- The pipeline then create a RDS instances. It extracts the environment name from the input parameter and uses it to generate the names of the resources. The withCredentials block reads the access key and secret key from the AWS credentials associated with the account and uses them to run the CFT and TF commands that create the RDS Instances.

- The agent any directive specifies that the pipeline can run on any agent machine with a specific label or without a label. In this case, it is not restricted to any agent machine.

- The environment block defines two environment variables that are derived from the user input. PROJECT_NAME is extracted from the credential name, and ACCOUNT_ID is extracted by splitting the credential name at the underscore (_) character.

- The pipeline has three stages:

  - The first stage cleans the workspace by removing any existing files from it. If the environment name parameter is empty, the pipeline stops and displays an error message. Otherwise, it sets the display name for the current build to include the project name, AWS account ID, and environment name.
  - The second stage used for SCM Checkout which instructs jenkins to obtain pipeline from SCM 
  - The third stage create the RDS instances in the defined region. It uses the if and else block to set the environment variables required for CloudFormationTemplate and Terraform. It then runs the CFT and TF  commands to create the RDS instances.

- Overall, this script provides a way to automate the creation of resources required for Terraform state management,CloudFormation and resource locking in an AWS account.

## DevOptymize_Jenkins_Pipelines Git repository:
A DevOptymize_Jenkins_Pipelines Git repository refers to a source code repository that contains the definition of a Jenkins pipeline which is configured to create the RDS instances in the defined region.

### Features:
- Creating AWS RDS instance using IaC (Terraform/CloudFormation) templates with parameterized jenkins build.
- Can be used to Create and Delete RDS instances incase of Cloudformation and can Create, Modify and Delete RDS instance incase of Terraform.
- Can be used to define environment in which resource can be created.

### Parameters:

| Parameters     |                                     Description                                                | Default Values  |
| :------------ |                                      :-----                                                     | :-------- |
| `Action`       | This parameter can be used to either Create or Modify or Delete a resource. Based on this parameter the pieline will decide whether to create, destroy or modify the resources.                    | `Create`   |
| `IAC_TOOL`     | Through this parameter, either Terraform or Cloudformation is used to create, modify or delete a resource | `Terraform`  |
| `CREDENTIAL`       | This parameter can be used to either Create or Modify or Delete a resource                     | `project_xxxxxxxxxx_aws_cred`   |
| `Environment`       | This parameter can be used to specify the Environment in which resources can be created                   | `prod`  |
| `Region`       | This parameter can be used to specify the AWS region in which the resources are to be created.                  | `us-east-1`   |
| `RDS_NAME`       | This parameter can be used to specify the name of the RDS Instances which has to be created       |  |
| `Engine_Type` |  This parameter can be used to choose the  database engine type.                  |   |
| `Engine_Version`  |  This parameter can be used to choose the engine version to use.              |   |
| `DB_Master_Username`  |  This parameter can be used to specify the login ID for the master user of your DB instance.  |   | 
| `DB_Master_Password`  |  This parameter can be used to Specify a string that defines the password for the master user.  |   |
| `DB_Instance_Class`   |  This parameter can be used to Choose the DB instance type that allocates the computational, network, and memory capacity required by planned workload of this DB instance.     |    |
| `Multi_AZ_Deployment` |  This parameter can be used to Select Yes to Create Replica in Different Zone to have Amazon RDS maintain a synchronous standby replica in a different Availability Zone than the DB instance.              |     |
| `VPC_NAME`  |   The parameter can be used to Select the VPC in which the RDS Instance needs to be provisioned. The default VPC are showed up as None.    |     |
| `Subnet_Group` |   The parameter can be used to Choose the DB subnet group. The DB subnet group defines which subnets and IP ranges the DB instance can use in the VPC that you selected.   |    |
| `Subnet_Name`  |  The parameter can be used to Choose the Subnet in which the RDS instance needs to be launched. If you will use the existing subnet groupt, then you don't want to select any subnets.   |    |
| `Security_Group_Name`  |  The parameter can be used to Choose the Security group which needs to be attached to RDS.  |   |
| `Public_Access`  |   The parameter can be used Select Yes if you want EC2 instances and other resources outside of the VPC hosting the database to connect to it. If you select No, Amazon RDS doesn't assign a public IP address to the database.    |    |
| `RDS_Storage` |    This parameter can be used to specify the Storage to be allocated to RDS            |     |
| `Storage_Autoscaling` |    This parameter can be used to Provides dynamic scaling support for your database’s storage based on your applications needs.     |     |
| `Encryption`  |    This parameter can be used to Choose to encrypt the given instance. Master key IDs and aliases appear in the list after they have been created using the AWS Key Management Service console. If encryption is not required select null    |   |
| `Log_exports`  |   This parameter can be used to Select yes if u need to export logs to cloudwatch.      |    |
| `Option_Group` |   This parameter can be used to Choose the DB option group that enables any optional functionality you want the DB instance to support. Select default to use a default option group provided by AWS.   |     |
| `Option_Group_New` |   This parameter can be used to Add the option group template with the required options.    |     |
| `Parameter_Group`  |  This parameter can be used to Choose the DB parameter group that defines the configuration settings you want applied to this DB instance. Select default to use a default parameter group provided by AWS.    |     |
| `Parameter_Group_New` |  This parameter can be used to specify the value to create the new  parameter group if required   |     |


### Contributing
We welcome contributions from the community to enhance the Jenkins pipeline. If you would like to contribute, please follow our guidelines outlined in the CONTRIBUTING.md file. You can submit feature requests, or pull requests to help us improve the template.

### License
