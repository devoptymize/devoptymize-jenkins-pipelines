## The script to create the ASG

# Overview

- This is a Jenkins pipeline script that create ASG and maintaining a lock on the resources provisioned in an AWS account.

- The pipeline includes the following steps:

- First, it takes user input in the form of credentials to provision the resources on an AWS account. The pipeline uses the ChoiceParameter to provide a list of credentials to choose from. The GroovyScript block defines the logic to fetch the credentials available in the Jenkins instance and returns a list of credentials associated with the user's login.

- The pipeline then create an ASG. It extracts the environment name from the input parameter and uses it to generate the names of the resources. The withCredentials block reads the access key and secret key from the AWS credentials associated with the account and uses them to run the CFT and TF commands that create the ASG.

- The agent any directive specifies that the pipeline can run on any agent machine with a specific label or without a label. In this case, it is not restricted to any agent machine.

- The environment block defines two environment variables that are derived from the user input. PROJECT_NAME is extracted from the credential name, and ACCOUNT_ID is extracted by splitting the credential name at the underscore (_) character.

- The pipeline has three stages:

  - The first stage cleans the workspace by removing any existing files from it. If the environment name parameter is empty, the pipeline stops and displays an error message. Otherwise, it sets the display name for the current build to include the project name, AWS account ID, and environment name.
  - The second stage used for SCM Checkout which instructs jenkins to obtain pipeline from SCM 
  - The third stage is to create the ASG in the defined region. It uses the if and else block to set the environment variables required for CloudFormationTemplate and Terraform. It then runs the CFT and TF  commands to create the ASG.

- Overall, this script provides a way to automate the creation of resources required for Terraform state management,CloudFormation and resource locking in an AWS account.



## DevOptymize_Jenkins_Pipelines Git repository:
A DevOptymize_Jenkins_Pipelines Git repository refers to a source code repository that contains the definition of a Jenkins pipeline which is configured to create the ASG resources in the defined region.

### Features:
- Creating AWS ASG resources using IaC (Terraform/CloudFormation) templates with parameterized jenkins build.
- Can be used to Create and Delete ASG resources with both Terraform and CloudFormation templates.
- Can be used to Create and Delete the ASG resources in the user specified region.
- Can be used to Create the ASG resource with all the specifications similar to the AWS console.

### Parameters:
Once you have the jenkins set up is done create a Job with the resource specified jenkins file. Then select the **Build with Parameters** in which the following parameters have to be specified.

| Parameters     |                                     Description                                                | Default Values  |
| :------------ |                                      :-----                                                     | :-------- |
| `Action`       | This parameter can be used to either Create or Modify or Delete a resource                     | `Create`   |
| `IAC_TOOL`     | Through this parameter, either Terraform or Cloudformation is used to create, modify or delete a resource | `Terraform`  |
| `CREDENTIAL`       | This parameter can be used to either Create or Modify or Delete a resource                     |     |
| `Environment`       | This parameter can be used to specify the Environment in which resources can be created                   |   |
| `Region`       | This parameter can be used to specify the AWS region in which the resources are to be created.                  |    |
| `VPC_NAME`       | This parameter allows the user to select the VPC for launching the ASG. It will list all the VPC based on the region which user selects. |  |
| `SUBNET_NAME`  |   This parameter allows the user to choose the subnets for their ASG resources. And it will list all the subnets which is available.  |                                        |
|  `ASG_NAME`   |  This parameter allows the user to enter the name of the ASG resource which is going to be created through this pipeline.   |     |                      
|  `LAUNCH_TEMPLATE` | This parameter allows the user to select the launch template in the region which they have selected.  |        |
|  `LOAD_BALANCING_ACTION` | This parameter allows the user to select which load balancer they need to attach with it. If the user don't want attach any load balancer they can choose No_Load_Balancer in this parameter.     |   `No_Load_Balancer`                  |
|  `LOAD_BALANCER` | This parameter allows the user to select the target group ARN's of the Load balancer which they need to attach with it and If they choose classic load balancer then they can directly select the load balancer from the parameter. And if the user chooses No_Load_Balancer then this parameter will be hided.      |             |
|  `DESIRED_CAPACITY` | This parameter allows the user to enter the number of Amazon EC2 instances that should be running in the autoscaling group                  |                            |
|  `MIN_SIZE`   |  This parameter allows the user to enter the minimum size of the autoscaling group.     |                    |
|  `MAX_SIZE`    | This parameter allows the user to enter the maximum size of the autoscaling group.     |                    |
|  `SNS_TOPIC`   |  This parameter allows the user to select the ARN of the SNS topic which they have in their selected region. This is used to send the notifications to SNS topic whenever the EC2 autoscaling launches or terminates the instances in the autoscaling group. The user can select null if they don't want to send the notifications.    |    |


### Contributing
We welcome contributions from the community to enhance the Jenkins pipeline. If you would like to contribute, please follow our guidelines outlined in the CONTRIBUTING.md file. You can submit feature requests, or pull requests to help us improve the template.

### License
