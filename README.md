## The script to create the Target group in AWS with desired registered target.

# Overview

- This is a Jenkins pipeline script that creates the target group.

- Overall, this script provides a way to automate the creation of target group which distributs incoming traffic across multiple instances or resources to ensure high availability and fault tolerance.

- The pipeline includes the following steps

- First, it takes user input in the form of credentials to provision the resources on an AWS account. The pipeline uses the ChoiceParameter to provide a list of credentials to choose from. The GroovyScript block defines the logic to fetch the credentials available in the Jenkins instance and returns a list of credentials associated with the user's login.

- The pipeline then creates the Target group. It extracts the environment name from the input parameter and uses it to generate the names of the resources. The withCredentials block reads the access key and secret key from the AWS credentials associated with the account and uses them to authenticate the aws CLI commands that create the target group

- The environment block defines two environment variables that are derived from the user input. PROJECT_NAME is extracted from the credential name, and ACCOUNT_ID is extracted by splitting the credential name at the underscore (_) character.

- The pipeline has three stages

    - The first stage cleans the workspace by removing any existing files from it. If the environment name parameter is empty, the pipeline stops and displays an error message. Otherwise, it sets the display name for the current build to include the project name, AWS account ID, and environment name.
    - The second stage is the SCM checkout where the code is cloned from the gitlab.
    - The third stage is to create the Target group. It uses the if and else block to set the environment variables required for CloudFormationTemplate and Terraform. For CFT, The process will wait, it outputs a message indicating that the stack creation is still in progress and waits for 30 seconds before checking the status again, after the stack creation complete. it will commit the output file in ops_devoptimize repo. It then runs the CFT and TF  commands to create the Target group.
- Overall, this script provides a way to automate the creation of resources required for Target group.

### Features:
- Creating target group using IaC (Terraform/CloudFormation) templates with parameterized jenkins build.
- Can be used to Create and Delete target group with both Terraform and CloudFormation templates.
- Can be used to Create the target group with all the specifications similar to the AWS console.

## Parameters

| Parameters     |                                     Description                                                | Default Values  |
| :------------ |                                      :-----                                                     | :-------- |
| `Action`       | This parameter can be used to either Create or Modify or Delete a resource                     | `Create`   |
| `IAC_TOOL`     | Through this parameter, either Terraform or Cloudformation is used to create, modify or delete a resource | `Terraform`  |
| `CREDENTIAL`       | This parameter provides a list of credential to provision the resources on the AWS account                     | `project_xxxxxxxx_aws_cred`   |
| `Region`       | This parameter can be used to specify the AWS region in which the resources are to be created. If its a private hosted zone, for listing the VPCs belonging to a specific region, this parameter is required.                  | `us-east-1`   |
| `ENV_NAME`       | This parameter can be used to specify the Environment in which resources can be created                   | `prod`  |
| `TARGET_TYPE`       | This parameter provides a list for target type. It can be either alb or lambda or instance or ip                | `instance`  |
| `INSTANCE_NAME`       | This parameter provides a list of instance names if the selected target_type is instance.            |   |
| `IP_ADDRESS`       | This parameter requires the user input for ip address if the selected target_type is ip.            |   |
| `LAMBDA_FUNCTION`       | This parameter provides a list of lambda functions if the selected target_type is lambda.            |   |
| `LAMBDA_FUNCTION_NAME`       | This parameter requires the user input for name of the lambda functions if the selected target_type is lambda.            |   |
| `APPLICATION_LOAD_BALANCER`       | This parameter provides a list of Alb arn if the selected target_type is alb.            |   |
| `PROTOCOL_TYPE`       | This parameter provides a list of Protocol type. It can be either be HTTP or HTTPS or TCP or TLS or UDP or TCP_UDP or GENEVE.          |   |  
| `PORT_NUMBER`       | This parameter requires the user input for port number            |   |
| `TARGET_GROUP_NAME`       | This parameter requires the user input for desired name of the target group.          |   |
| `VPC_NAME`       | This parameter provides a list of vpc names in the specific region selected in the region parameter.          |   |



### Contributing

We welcome contributions from the community to enhance the jenkins configuration. If you would like to contribute, please follow our guidelines outlined in the CONTRIBUTING.md file. You can submit feature requests, or pull requests to help us improve the template.

### License
