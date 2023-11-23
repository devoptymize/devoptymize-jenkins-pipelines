## The script to create the AWS Cloudfront.

### Description

- This is a Jenkins pipeline script that creates the Cloudfront. 

- Overall, this script provides a way to automate the creation of Cloudfront which improves the performance, scalability, and availability of your web applications by distributing content globally and delivering it to users from the nearest edge location.

- The pipeline includes the following steps

- First, it takes user input in the form of credentials to provision the resources on an AWS account. The pipeline uses the ChoiceParameter to provide a list of credentials to choose from. The GroovyScript block defines the logic to fetch the credentials available in the Jenkins instance and returns a list of credentials associated with the user's login.

- The pipeline then creates the Cloudfront. It extracts the environment name from the input parameter and uses it to generate the names of the resources. The withCredentials block reads the access key and secret key from the AWS credentials associated with the account and uses them to authenticate the aws CLI commands that create the target group

- The environment block defines two environment variables that are derived from the user input. PROJECT_NAME is extracted from the credential name, and ACCOUNT_ID is extracted by splitting the credential name at the underscore (_) character.

- The pipeline has three stages

    - The first stage cleans the workspace by removing any existing files from it. If the environment name parameter is empty, the pipeline stops and displays an error message. Otherwise, it sets the display name for the current build to include the project name, AWS account ID, and environment name.
    - The second stage is the SCM checkout where the code is cloned from the gitlab.
    - The third stage is to create the Cloudfront. It uses the if and else block to set the environment variables required for CloudFormationTemplate and Terraform. For CFT, The process will wait, it outputs a message indicating that the stack creation is still in progress and waits for 30 seconds before checking the status again, after the stack creation complete. it will commit the output file in ops_devoptimize repo. It then runs the CFT and TF  commands to create the CLoudfront.
- Overall, this script provides a way to automate the creation of resources required for Cloudfront.

### Pre-requisites
Jenkinsfile along with Jenkinsfile with Cloudformation template and Terraform scripts in respective place.

### Resources
- S3 bucket
    -  Creates a S3 bucket with configurable properties, such as the bucket name.
- CloudFront-Origin-Access-Control
    -  Creates a origin access control with configurable properties such as the name of the OAC.
- Cloudfront-Distribution
    -  Creates a Cloudfront Distribution with configurable properties, including dominname, Origin-access-control-id, Originpath, Originshield, Priceclass, defaultrootobject, Loggin-bucket, http-version, ipv6, targetoriginid, viewer_protocol_policy, compress, allowed_http_methods, viewer_certificate, aliases, comment, webaclid

### Parameters

| Parameters     |                                     Description                                                | Default Values TF |        Default Values CFT | 
| :------------ |                                      :-----                                                     | :-------- |        :------------ |
| `Action`       | This parameter can be used to either Create or Modify or Delete a resource                     | `Create`   |`Create`   |
| `IAC_TOOL`     | Through this parameter, either Terraform or Cloudformation is used to create, modify or delete a resource | `Terraform`  | `Cloudformation`  |
| `CREDENTIAL`       | This parameter provides a list of credential to provision the resources on the AWS account                     | `project_xxxxxxxx_aws_cred`   | `project_xxxxxxxx_aws_cred`   |
| `Region`       | This parameter can be used to specify the AWS region in which the resources are to be created. If its a private hosted zone, for listing the VPCs belonging to a specific region, this parameter is required.                  | `us-east-1`   | `us-east-1`   |
| `ENV_NAME`       | This parameter can be used to specify the Environment in which resources can be created                   |   |
| `DISTRIBUTION_TYPE`       | Choose the distribution type - S3 or Load Balancer               | `S3`  | `S3`  |
| `CDN_NAME`       | the name of the Cloudfront distribution           |   |
| `USE_CUSTOM_DOMAIN`       |   Set to "true" to use a custom domain (CNAME) for the CloudFront distribution         | `true`  |`true`  |
| `ORIGIN_SHIELD`       | Origin Shield can help reduce the load on your origin.           |  `true` |  `true` |
| `ORIGIN_PATH`       | A path that CloudFront appends to the origin domain name when CloudFront requests content from the origin    |   |
|  `OAC_NAME`       | The comment for the CloudFront Origin Access Identity (OAc).         |  `` |
| `PRICE_CLASS`       |The price class for this distribution. One of PriceClass_All, PriceClass_200, PriceClass_100   |  `PriceClass_All` |`PriceClass_All` |
| `ORIGIN_PROTOCOL_POLICY`       | Specifies the protocol (HTTP or HTTPS) that CloudFront uses to connect to the origin    |   |
| `VIEWER_PROTOCOL_POLICY`       | Specifies the protocol that CloudFront uses to connect to the origin    |   |
| `ALLOWED_HTTP_METHODS`       | controls which HTTP methods CloudFront processes and forwards to your Amazon S3 bucket or your custom origin   |  `GET, HEAD` |`GET, HEAD` |
| `COMPRESS`       | Whether you want CloudFront to automatically compress certain files for this cache behavior. If so, specify true; if not, specify false    |  `true` |  `true` |
| `IPV6`       | If you want CloudFront to respond to IPv6 DNS requests with an IPv6 address for your distribution, specify true. If you specify false, CloudFront responds to IPv6 DNS requests with the DNS response code NOERROR and with no IP addresses.   |  `true` |  `true` |
| `BUCKET_NAME`       | The name of your S3 bucket (only needed for S3 distribution)    |   |
| `LOADBALANCER_NAME`       | The name of your Loadbalancer   |   |
| `HTTP_VERSION `       | Specify the maximum HTTP version(s) that you want viewers to use to communicate with CloudFront. The default value for new distributions is http1.1   |  `true` | `true` |
| `DEFAULT_ROOT_OBJECT`       | The object that you want CloudFront to request from your origin (for example, index.html)   |   |
| `CERTIFICATE_NAME`       | ACM CERTIFICATE to secure HTTPS protocol  |   |
| `ALIASES`       | Extra CNAMEs (alternate domain names), if any, for this distribution  |   |
| `WEBACL_NAME`       | A unique identifier that specifies the AWS WAF web ACL, if any, to associate with this distribution   |   |
| `DESCRIPTION`       | A comment to describe the distribution. The comment cannot be longer than 128 characters.   |   |

### Contributing

We welcome contributions from the community to enhance the jenkins configuration. If you would like to contribute, please follow our guidelines outlined in the CONTRIBUTING.md file. You can submit feature requests, or pull requests to help us improve the template.

### License
