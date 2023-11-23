## Jenkins script for creating the RDS cluster
### Overview
- This is a Jenkins script that create an RDS cluster in AWS account.

- The pipeline includes the following steps:

- First, it takes user input in the form of credentials to provision the resources on AWS account. The pipeline uses the ChoiceParameter to provide a list of credentials to choose from. The GroovyScript block defines the logic to fetch the credentials available in the Jenkins instance and returns a list of credentials associated with the user's login.

- The pipeline then create the RDS cluster. It extracts the environment name from the input parameter and uses it to generate the names of the resources. With the Credentials block reads the access key and secret key from the AWS credentials associated with the account and uses them to run the CFT and TF commands that create the RDS database.

- The agent any directive specifies that the pipeline can run on any agent machine with a specific label or without a label. In this case, it is not restricted to any agent machine.

- The environment block defines two environment variables that are derived from the user input. PROJECT_NAME is extracted from the credential name, and ACCOUNT_ID is extracted by splitting the credential name at the underscore (_) character.

- The pipeline has three stages:

  - The first stage cleans the workspace by removing any existing files from it. If the environment name parameter is empty, the pipeline stops and displays an error message. Otherwise, it sets the display name for the current build to include the project name, AWS account ID, and environment name.
  - The second stage used for SCM Checkout which instructs jenkins to obtain pipeline from SCM.
  - The third stage is to create the RDS cluster in the defined region. It uses the if and else block to set the environment variables required for CloudFormationTemplate and Terraform. For CFT, The process will wait, it outputs a message indicating that the stack creation is still in progress and waits for 30 seconds before checking the status again, after the stack creation complete. It will commit the output file in ops_devoptimize repo. It then runs the CFT and TF commands to create the RDS cluster.

- Overall, this script provides a way to automate the creation of resources required for Terraform state management,CloudFormation and resource locking in an AWS account.

#### Limitations

- The create and delete actions were functioning correctly in the Terraform script, except for the modify action.
- The modify action should work as intended in the Terraform script, similar to how create and delete actions are functioning correctly.

### DevOptymize_Jenkins_Pipelines
A DevOptymize_Jenkins_Pipelines Git repository refers to a source code repository that contains the definition of a Jenkins pipeline.


#### Features:
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
| `RDS_CLUSTER_IDENTIFIER` | The parameter allows the user to enter the identifier for the RDS cluster to be created.
|`ENGINE_TYPE` | The database engine type for the RDS cluster, e.g., postgres, mysql, etc. | `aurora-mysql`|
| `ENGINE_VERSION` | This parameter allows the user to select the version of the selected database engine, e.g., 13.4 | `5.7.mysql-aurora.2.07.9` |
|` DB_INSTANCE_CLASS` | This parameter allows the user to select the instance class for the RDS instances within the cluster, e.g., db.t2.micro |  `db.m6g.16xlarge` |
| `DB_MASTER_USERNAME` | This parameter allows the user to enter the master username for the RDS cluster.
| `DB_MASTER_PASSWORD` | This parameter allows the user to enterThe master password for the RDS cluster.
| `VPC_ID` | This parameter allows the user to select the ID of the Virtual Private Cloud (VPC) where the RDS cluster will be deployed. | `default`
| `SUBNET_GROUP` | This parameter allows the user to select the name of the DB subnet group for the RDS cluster.   |`null` |
| `SUBNET_NAME` | This parameter allows the user to select the name of the subnet within the specified subnet group. |`def-pub-sb` |
| `SECURITY_GROUP_NAME` | This parameter allows the user to select the name of the securitygroup for the RDS cluster. |`launch-wizard-87` |
| `PUBLIC_ACCESS` | This parameter determine if the RDS instances should be publicly accessible. | `yes` |
| `DB_INSTANCE_ONE ` | This parameter allows the user to select settings for the first RDS instance within the Aurora cluster  |`default` |
| `DB_INSTANCE_ONE_CUSTOM` | The custom settings for the first RDS instance (if not using default settings).
| `DB_INSTANCE_TWO` | This parameter allows the user to select settings for the second RDS instance within the Aurora cluster.  |`default` |
| `DB_INSTANCE_TWO_CUSTOM` |  The custom settings for the second RDS instance (if not using default settings). 
| `DB_INSTANCE_THREE` | This parameter allows the user to select settings for the third RDS instance within the Aurora cluster.  | `default` |
| `DB_INSTANCE_THREE_CUSTOM` | The custom settings for the third RDS instance (if not using default settings).   
| `RDS_STORAGE` | This parameter allows the user to enetr the amount of storage needed for the RDS cluster
| `STORAGE_AUTOSCALING` | This [parameter provides a dynamic scaling support for your database’s storage based on your applications needs. (This is only availbale for the CFT template)] |`yes` |
|` ENCRYPTION`  | This parameter allows the user to select the encryption for the RDS cluster. | `arn:aws:kms:us-east-1:1234567890:key/00ba2e69-de7f-42e0-a345-e5c706340a3b` |
| `LOG_EXPORTS` | This parameter allows the user to determine that you need to export logs to cloudwatch. | `yes` |
| `PARAMETER_GROUP` | This parameter that defines the configuration settings you want applied to this DB instance |  |
| `PARAMETER_GROUP_NEW ` | This parameter that allows the user to enter the parameters with the value to create the new parameter group
| `CLUSTER_PARAMETER_GROUP` | This parameter allows to choose the Parameter group associated with this instance's DB Cluster. |  |
| `CLUSTER_PARAMETER_GROUP_NEW` | This parameter allows to choose the new parameter group associated with this instance's DB Cluster.


### Contributing
We welcome contributions from the community to enhance the Jenkins pipeline. If you would like to contribute, please follow our guidelines outlined in the CONTRIBUTING.md file. You can submit feature requests, or pull requests to help us improve the template.

### License
