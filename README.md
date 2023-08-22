# The script to create AWS credentials and store it in aws secrets manager which is account and project specific

## Overview

- This is a Jenkins pipeline script that is responsible for creating AWS credentials using AWS Secret Manager. The pipeline also validates that the AWS Account ID is a valid 12-digit number.

- The script begins by defining the parameters block which includes the following parameters:

  - PROJECT_NAME: A string that serves as a prefix for the AWS credential name
  - ACCOUNT_ID: The AWS account ID for which the credentials will be created
  - ENV_NAME: The name of the environment for which the credentials will be created
  - AWS_ACCESS_KEY_ID: A password field that will contain the AWS access key ID
  - AWS_SECRET_ACCESS_KEY: A password field that will contain the AWS secret access key

- The script then defines the stages block which contains two stages:

  - The first stage is named "Clean workspace". This stage cleans up the workspace, sets a display name for the build, and checks the length of the AWS account ID parameter to ensure it is a valid 12-digit number. If the length is not valid, the build is aborted. Then, it fetches the code from GitLab repository by specifying the branch and credentials for GitLab.
  - The second stage is named "Storing the AWS credentials in AWS Secret manager". This stage wraps the access key ID and secret access key fields to hide them during the execution of the pipeline. The withAWS block specifies the AWS credentials and region to use when interacting with AWS Secret Manager. It then creates the AWS secret by calling the "aws secretsmanager create-secret" command with the AWS access key ID and secret access key. If the AWS Account ID is invalid, it will be aborted.

- Overall, this script automates the creation of AWS credentials for a specific project and environment using AWS Secret Manager in Jenkins pipeline.

### Contributing

We welcome contributions from the community to enhance the jenkins configuration. If you would like to contribute, please follow our guidelines outlined in the CONTRIBUTING.md file. You can submit feature requests, or pull requests to help us improve the template.

### License
