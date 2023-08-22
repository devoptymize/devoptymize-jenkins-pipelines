## The script to create the S3 Bucket
## Overview
- This is a Jenkins pipeline script that create an S3 bucket in AWS.

- The pipeline includes the following steps:

- First, it takes user input in the form of credentials to provision the resources on an AWS account. The pipeline uses the ChoiceParameter to provide a list of credentials to choose from. The GroovyScript block defines the logic to fetch the credentials available in the Jenkins instance and returns a list of credentials associated with the user's login.

- The pipeline then create the S3 bucket. It extracts the environment name from the input parameter and uses it to generate the names of the resources. The withCredentials block reads the access key and secret key from the AWS credentials associated with the account and uses them to run the CFT and TF commands that create the S3 bucket.

- The agent any directive specifies that the pipeline can run on any agent machine with a specific label or without a label. In this case, it is not restricted to any agent machine.

- The environment block defines two environment variables that are derived from the user input. PROJECT_NAME is extracted from the credential name, and ACCOUNT_ID is extracted by splitting the credential name at the underscore (_) character.

- The pipeline has three stages:

  - The first stage cleans the workspace by removing any existing files from it. If the environment name parameter is empty, the pipeline stops and displays an error message. Otherwise, it sets the display name for the current build to include the project name, AWS account ID, and environment name.
  - The second stage used for SCM Checkout which instructs jenkins to obtain pipeline from SCM 
  - The third stage is to create the S3 bucket in the defined region. It uses the if and else block to set the environment variables required for CloudFormationTemplate and Terraform. For CFT, The process will wait, it outputs a message indicating that the stack creation is still in progress and waits for 30 seconds before checking the status again, after the stack creation complete. it will commit the output file in ops_devoptimize repo. It then runs the CFT and TF commands to create the S3 bucket

- Overall, this script provides a way to automate the creation of resources required for Terraform state management,CloudFormation and resource locking in an AWS account.

### DevOptymize_Jenkins_Pipelines Git repository:

| Parameters     |                                     Description                                                | Default Values  |
| :------------ |                                      :-----                                                     | :-------- |
| `Action`       | This parameter can be used to either Create or Modify or Delete a resource                     | `Create`   |
| `IAC_TOOL`     | Through this parameter, either Terraform or Cloudformation is used to create, modify or delete a resource | `Terraform`  |
| `CREDENTIAL`       | This parameter provides a list of credential to provision the resources on the AWS account                     | `project_xxxxxxxx_aws_cred`   |
| `Environment`       | This parameter can be used to specify the Environment in which resources can be created                   | `dev`  |
| `Region`       | This parameter can be used to specify the AWS region in which the resources are to be created. If its a private hosted zone, for listing the VPCs belonging to a specific region, this parameter is required.                  | `us-east-1`   |
| `BUCKET_NAME`       | This parameter can be used to specify the name of the S3 bucket                  
| `BUCKET_POLICY`       | The bucket policy rules in JSON format  |  |
| `BUCKET_KEY_ENABLED`       | This parameter used to allow the user to specify whether to use an S3 Bucket Key for encryption. | `true`  |     

### Contributing
We welcome contributions from the community to enhance the Jenkins pipeline. If you would like to contribute, please follow our guidelines outlined in the CONTRIBUTING.md file. You can submit feature requests, or pull requests to help us improve the template.

### License
