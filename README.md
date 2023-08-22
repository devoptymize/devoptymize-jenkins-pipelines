## The script to create the s3 bucket for storeing TF statefiles and dynamodB table which are project, account id and environment specific

# Overview

- This is a Jenkins pipeline script that creates an S3 bucket and DynamoDB table for storing Terraform state files and maintaining a lock on the resources provisioned in an AWS account.

- Overall, this script provides a way to automate the creation of resources required for Terraform state management and resource locking in an AWS account.

## The pipeline includes the following steps

- First, it takes user input in the form of credentials to provision the resources on an AWS account. The pipeline uses the ChoiceParameter to provide a list of credentials to choose from. The GroovyScript block defines the logic to fetch the credentials available in the Jenkins instance and returns a list of credentials associated with the user's login.

- The pipeline then creates an S3 bucket and a DynamoDB table. It extracts the environment name from the input parameter and uses it to generate the names of the resources. The withCredentials block reads the access key and secret key from the AWS credentials associated with the account and uses them to authenticate the aws CLI commands that create the S3 bucket and DynamoDB table.

- The environment block defines two environment variables that are derived from the user input. PROJECT_NAME is extracted from the credential name, and ACCOUNT_ID is extracted by splitting the credential name at the underscore (_) character.

## Parameters

| Parameters     |                                     Description                                                | Default Values  |
| :------------ |                                      :-----                                                     | :-------- |
| `CREDENTIAL`       | This parameter defines the credential to provision the resources on the AWS account                     | `project_xxxxxxxx_aws_cred`   |
| `ENV_NAME`       | This parameter can be used to specify the Environment in which resources can be created                   | `prod`  |

## The pipeline stages

- The first stage cleans the workspace by removing any existing files from it. If the environment name parameter is empty, the pipeline stops and displays an error message. Otherwise, it sets the display name for the current build to include the project name, AWS account ID, and environment name.
- The second stage creates the S3 bucket and DynamoDB table. It uses the withCredentials block to set the environment variables required for authentication with the AWS CLI. It then runs the aws CLI commands to create the S3 bucket and DynamoDB table.

### Contributing

We welcome contributions from the community to enhance the jenkins configuration. If you would like to contribute, please follow our guidelines outlined in the CONTRIBUTING.md file. You can submit feature requests, or pull requests to help us improve the template.

### License
