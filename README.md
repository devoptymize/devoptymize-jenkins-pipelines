## The script to create the Route53 (hosted zone) for provisioning a public or private hosted zone

# Overview

- This is a Jenkins pipeline script that creates the Route53 (hosted zone) for provisioning a public or private hosted zone.

- Overall, this script provides a way to automate the creation of hosted zone which can either be a public or private.

- The pipeline includes the following steps

- First, it takes user input in the form of credentials to provision the resources on an AWS account. The pipeline uses the ChoiceParameter to provide a list of credentials to choose from. The GroovyScript block defines the logic to fetch the credentials available in the Jenkins instance and returns a list of credentials associated with the user's login.

- The pipeline then creates the Route53 (hosted zone). It extracts the environment name from the input parameter and uses it to generate the names of the resources. The withCredentials block reads the access key and secret key from the AWS credentials associated with the account and uses them to authenticate the aws CLI commands that create the Route53 (hosted zone).

- The agent any directive specifies that the pipeline can run on any agent machine with a specific label or without a label. In this case, it is not restricted to any agent machine.

- The environment block defines two environment variables that are derived from the user input. PROJECT_NAME is extracted from the credential name, and ACCOUNT_ID is extracted by splitting the credential name at the underscore (_) character.

- The pipeline has 3 stages

  - The first stage cleans the workspace by removing any existing files from it. If the environment name parameter is empty, the pipeline stops and displays an error message. Otherwise, it sets the display name for the current build to include the project name, AWS account ID, and environment name.
  - The second stage is the SCM checkout where the code is cloned from the gitlab.
  - The third stage is to create the Route53 (hosted zone).  It uses the if and else block to set the environment variables required for CloudFormationTemplate and Terraform. For CFT, The process will wait, it outputs a message indicating that the stack creation is still in progress and waits for 30 seconds before checking the status again, after the stack creation complete. it will commit the output file in ops_devoptimize repo. It then runs the CFT and TF  commands to create the create the Route53 (hosted zone).
- Overall, this script provides a way to automate the creation of resources required for Route53

## DevOptymize_Jenkins_Pipelines Git repository

A DevOptymize_Jenkins_Pipelines Git repository refers to a source code repository that contains the definition of a Jenkins pipeline which is configured to create the Route53 in the defined region.

### Features

- Creating Route53 (hosted zone) using IaC (Terraform/CloudFormation) templates with parameterized jenkins build.
- Can be used to Create a public or a private hosted zone.
- Can be used to Create and Delete hosted zone with both Terraform and CloudFormation templates.
- Can be used to Create the hosted zone with all the specifications similar to the AWS console.

## Parameters

| Parameters     |                                     Description                                                | Default Values  |
| :------------ |                                      :-----                                                     | :-------- |
| `Action`       | This parameter can be used to either Create or Modify or Delete a resource                     | `Create`   |
| `IAC_TOOL`     | Through this parameter, either Terraform or Cloudformation is used to create, modify or delete a resource | `Terraform`  |
| `CREDENTIAL`       | This parameter provides a list of credential to provision the resources on the AWS account                     | `project_xxxxxxxx_aws_cred`   |
| `Region`       | This parameter can be used to specify the AWS region in which the resources are to be created. If its a private hosted zone, for listing the VPCs belonging to a specific region, this parameter is required.                  | `us-east-1`   |
| `ENV_NAME`       | This parameter can be used to specify the Environment in which resources can be created                   | `prod`  |
| `HOSTEDZONENAME`       | This parameter requires a user input for specifying the desired hosted zone name |  |
| `HOSTED_ZONE_TYPE`       | This parameter requires the user input to select the type of hosted zone which can either be a public or a private. If its a private, the user input is required for ROUTE53_VPCNAME.|  |
| `ROUTE53_VPCNAME`       | This parameter requires the user input to select the VPC from the list of VPCs within the selected region in the Region Parameter if the selected hosted_zone_type is private |  |

### Contributing

We welcome contributions from the community to enhance the jenkins configuration. If you would like to contribute, please follow our guidelines outlined in the CONTRIBUTING.md file. You can submit feature requests, or pull requests to help us improve the template.

### License
