## The script to create EKS cluster
### Overview
- This is a [Jenkins pipeline script](./Jenkinsfile) that create a public and private EKS cluster in an AWS account.

- The pipeline includes the following steps:

- First, it takes user input in the form of credentials to provision the resources on an AWS account. The pipeline uses the ChoiceParameter to provide a list of credentials to choose from. The GroovyScript block defines the logic to fetch the credentials available in the Jenkins instance and returns a list of credentials associated with the user's login.

- The pipeline then create the EKS cluster. It extracts the environment name from the input parameter and uses it to generate the names of the resources. With the Credentials block reads the access key and secret key from the AWS credentials associated with the account and uses them to run the CFT and TF commands that create the EKS cluster

- The agent any directive specifies that the pipeline can run on any agent machine with a specific label or without a label. In this case, it is not restricted to any agent machine.

- The environment block defines two environment variables that are derived from the user input. PROJECT_NAME is extracted from the credential name, and ACCOUNT_ID is extracted by splitting the credential name at the underscore (_) character.

- The pipeline has three stages:

  - The first stage cleans the workspace by removing any existing files from it. If the environment name parameter is empty, the pipeline stops and displays an error message. Otherwise, it sets the display name for the current build to include the project name, AWS account ID, and environment name.
  - The second stage used for SCM Checkout which instructs jenkins to obtain pipeline from SCM.
  - The third stage is to create the EKS cluster in the defined region. It uses the if and else block to set the environment variables required for CloudFormationTemplate and Terraform. For CFT, The process will wait, it outputs a message indicating that the stack creation is still in progress and waits for 30 seconds before checking the status again, after the stack creation complete. it will commit the output file in ops_devoptimize repo. It then runs the CFT and TF commands to create the EKS cluster.

- Overall, this script provides a way to automate the creation of resources required for Terraform state management,CloudFormation and resource locking in an AWS account.

#### Features:

- Creating a public and private EKS cluster using IaC (Terraform/CloudFormation) templates with parameterized jenkins build.
- Can be used to Create and Delete EKS cluster incase of Cloudformation and can Create, Modify and Delete VPC-config-guide-architecture incase of Terraform.
- Can be used to define environment in which resource can be created.

#### Parameters:
Once you have the jenkins set up is done create a Job with the resource specified jenkins file. Then select the **Build with Parameters** in which the following parameters have to be specified.

| Parameters     |                                     Description                                                | Default Values  TF |   Default Values  CFT |
| :------------ |                                      :-----                                                     | :-------- |             :-------- |
| `ACTION`       | This parameter allows the user to select either Create or modify or delete a resources in the AWS account. This parameter will have list of actions such as Create, Modify and Delete.                    | `Create`   | `Create`   |
| `IAC_TOOL`     | This parameter allows the user to select one of the two IAC_TOOLS for creating the resource. The IAC-TOOLS which can be used are Cloudformation or Terraform | `Terraform`  |  `CloudFormation` |
| `CREDENTIAL`       | This parameter allows the user to select the credential which has necessary permission to create a resource in the AWS account                   | `ops_xxxxxxxx_aws_cred`   |  `ops_xxxxxxxx_aws_cred`   |
| `ENVIRONMENT`       | The parameter allows the user to enter the Environment in which the required resource can be created. for example: dev and prod environment.                 | `dev`  |  `dev`  |
| `REGION`       | This parameter allows the user to select the region in which the Network resources can be created. This parameter will have a list of all the regions upon which the user can select the desired region.                | `us-east-1`   |
| `CLUSTER_NAME` | The name of eks cluster needs to be provisioned 
| `K8S_VERSION` | This parameter that allows users to specify a Kubernetes version | `1.26`  |    `1.26`  |
| `CLUSTER_ENDPOINT_PUBLIC_ACCESS ` | This parameter controls whether the Kubernetes cluster's API server endpoint should have public access. Set to true for public access and false for private access.
| `CLUSTER_ENDPOINT_PRIVATE_ACCESS ` | This parameter controls whether the Kubernetes cluster's API server endpoint should have private access. Set to true for private access and false for public access.
| ` VPC_ID` | This parameter specifies the ID of the VPC where the resources will be provisioned. |`Default` |  `Default` |
| `SUBNET_ID`|This parameter specifies the ID of the subnet within the selected VPC where the instances or resources will be placed. Use this parameter to specify the subnet where your resources will be launched.
| `INSTANCE_TYPE` | his parameter defines the instance type for the resources you're deploying. It determines the computing and memory capacity of the instances. | `r6a.2xlarge` |  `r6a.2xlarge` |
| `LOG_TYPES` | This parameter allows to enable specific types of cluster logging  | `api` |  `api` |
|` NODE_GROUP_NAME` | This parameter specifies the name of the node group within the EKS cluster.
| `NODE_GROUP_MIN_SIZE`| This parameter sets the minimum number of nodes in the node group.
| ` NODE_GROUP_MAX_SIZE` | This parameter sets the maximum number of nodes in the node group.
| `NODE_GROUP_DESIRED_SIZE` |  This parameter sets the desired number of nodes in the node group.
| `VOLUME_SIZE` |This parameter specifies the size (in GB) of the storage volume attached to each node. 
|`VOLUME_TYPE` | This parameter specifies the type of storage volume to be attached to each node. Choose a suitable value, such as "gp2, gp3"
|`IOPS` | This parameter defines the number of input/output operations per second (IOPS) for storage volumes that support it. (Provide the value for iops only for terraform)

#### Contributing
We welcome contributions from the community to enhance the Jenkins pipeline. If you would like to contribute, please follow our guidelines outlined in the [CONTRIBUTING.md](./CONTRIBUTING.md) file. You can submit feature requests, or pull requests to help us improve the template.

### License
