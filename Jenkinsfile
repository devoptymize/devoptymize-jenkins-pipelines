properties([
    parameters([
        // ChoiceParameter is used to allow the user to select one of several predefined options.
        // In this case, the user can choose between Create, Modify, or Delete as the ACTION to perform.
        [$class: 'ChoiceParameter', 
                choiceType: 'PT_SINGLE_SELECT', 
                description: 'Choose anyone of the below option to perform the action', 
                filterLength: 1, 
                filterable: false, 
                name: 'IAC_TOOL', 
                script: [
                    $class: 'GroovyScript', 
                    fallbackScript: [
                        classpath: [], 
                        sandbox: true, 
                        script: 
                            "return['Terraform']"
                    ], 
                    script: [
                        classpath: [], 
                        sandbox: true, 
                        script: 
                            "return ['Terraform','CloudFormation']"
                    ]
                ]
        ],
        [$class: 'CascadeChoiceParameter', 
        choiceType: 'PT_SINGLE_SELECT', 
        description: 'Choose anyone of the below option to perform the action', 
        filterLength: 4, 
        filterable: true, 
        randomName: 'choice-parameter-56313111561789229', 
        referencedParameters: 'IAC_TOOL', 
        name: 'ACTION', 
        script: [
            $class: 'GroovyScript', 
            fallbackScript: [
                classpath: [], 
                sandbox: true, 
                script: 
                    'return ""'
            ], 
            script: [
                classpath: [], 
                sandbox: true,
                script: 
                    ''' if (IAC_TOOL == "Terraform") {
                            return ['Create','Modify','Delete']
                        }  else {
                            return ['Create','Delete']
                        }
                    '''
                ]
            ]
        ],
        // ChoiceParameter is also used to select a credential to use to provision the resources on the AWS account.
        // The script starts by getting all the credentials available in Jenkins and gets the the project_name frow the current_user functon. Then it loops on the credentials to get onl the credentials which starts with the particular project_name.
        [$class: 'ChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: 'Choose anyone of the below credential to provision the resources on the AWS account', 
            filterLength: 1, 
            filterable: false, 
            name: 'CREDENTIAL', 
            script: [
                $class: 'GroovyScript', 
                fallbackScript: [
                    classpath: [], 
                    sandbox: false, 
                    script: 
                        "return['Create']"
                ], 
                script: [
                    classpath: [], 
                    sandbox: false, 
                    script: 
                        ''' import jenkins.model.*
                            import org.apache.http.client.methods.*
                            import org.apache.http.impl.client.*
                            import groovy.json.JsonSlurper;
                            import jenkins.*
                            import hudson.model.*
                            def user = User.current().getId()
                            String result = user.takeWhile { it != '_' }
                            def jenkinsCredentials = com.cloudbees.plugins.credentials.CredentialsProvider.lookupCredentials(
                                    com.cloudbees.plugins.credentials.Credentials.class,
                                    Jenkins.instance,
                                    null,
                                    null
                            );
                            
                            def lst = []
                            for (creds in jenkinsCredentials) {
                                if((creds.id).contains(result)){
                                    lst.add(creds.id)
                                }
                            }
                            return lst                            
                        '''
                ]
            ]
        ],
        [$class: 'ChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: 'AWS region in which the resources need to be provisioned', 
            filterLength: 4, 
            filterable: true, 
            name: 'REGION', 
            script: [
                $class: 'GroovyScript', 
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        "return['us-east-1']"
                ], 
                script: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        "return ['us-east-1','us-east-2','us-west-1','us-west-2','af-south-1','ap-east-1','ap-southeast-3','ap-south-1','ap-northeast-3','ap-northeast-2','ap-southeast-1','ap-southeast-2','ap-northeast-1','ca-central-1','eu-central-1','eu-west-1','eu-west-2','us-gov-east-1','us-gov-west-1','sa-east-1','eu-south-1','eu-west-3','eu-north-1','me-south-1']"
                ]
            ]
        ],
        string(
            defaultValue: '', 
            name: 'ENVIRONMENT', 
            trim: true,
            description: 'The name of the environment in which resources needs to be provisioned.'
        ),
        string(
            defaultValue: '', 
            name: 'LAUNCH_TEMPLATE_NAME', 
            trim: true,
            description: 'The Launch Template name which needs to be provisioned.'
        ),

        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: 'select the OS family', 
            filterLength: 4, 
            filterable: true, 
            randomName: 'choice-parameter-56313111561789229', 
            referencedParameters: 'IAC_TOOL', 
            name: 'OPERATINGSYSTEM', 
            script: [
                $class: 'GroovyScript', 
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        'return ""'
                ], 
                script: [
                    classpath: [], 
                    sandbox: true,
                    script: 
                        ''' if (IAC_TOOL == "Terraform") {
                              return ["Dont have to choose OS"]
                            } else if (IAC_TOOL == "CloudFormation") {
                              return ["windows","linux"]
                            } else {
                              return ["select an option"]
                            }
                        '''
                ]
            ]
        ],
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: '''The AMI_ID which will be used to create the Launch Template. AMI ID is a must required value for the launch template to be created''', 
            name: 'AMI_ID', 
            omitValueField: true,
            randomName: 'choice-parameter-56313113156178619', 
            referencedParameters: 'ACTION,CREDENTIAL,LAUNCH_TEMPLATE_NAME,REGION', 
            script: [
                $class: 'GroovyScript',  
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        'return  ""'
                ],
                script: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                    // Checks if the referenced parameter ACTION equals "Create" , "Modify"
                    // Defines the input box HTML code to display for the parameter.
                    // Returns the input box HTML code
                    ''' 
                        if (ACTION.equals("Create") || ACTION.equals("Modify")){
                            inputBox = "<input name='value' class='setting-input' type='text'>"
                            return inputBox
                        }
                    '''
                ]
            ]
        ],                                                          
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: '''Launch Template Instance type in which the resources need to be provisioned. Refer the link to get detailed Instance types https://instances.vantage.sh/. Select null if you Don't want to include instance type in launch template''', 
            filterLength: 4, 
            filterable: true, 
            randomName: 'choice-parameter-56313111561789619', 
            referencedParameters: 'ACTION', 
            name: 'INSTANCE_TYPE', 
            script: [
                $class: 'GroovyScript', 
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        'return ""'
                ], 
                script: [
                    classpath: [], 
                    sandbox: true,
                    script: 
                        ''' if (ACTION.equals("Create") || ACTION.equals("Modify")){
                                return ['r6a.2xlarge','im4gn.8xlarge','r5ad.24xlarge','c3.8xlarge','c6a.16xlarge','m5a.4xlarge','is4gen.4xlarge','m5zn.2xlarge','m5zn.large','r6gd.medium','r4.16xlarge','c6i.large','m6id.12xlarge','r6id.2xlarge','m4.4xlarge','c6gd.metal','is4gen.xlarge','r6gd.2xlarge','m5.8xlarge','d3en.2xlarge','m6a.2xlarge','r6gd.xlarge','x2iedn.xlarge','m5ad.12xlarge','r5a.xlarge','g3.16xlarge','m5dn.large','c6a.24xlarge','d3en.8xlarge','x2iezn.4xlarge','t1.micro','c6i.2xlarge','c5n.2xlarge','x1e.2xlarge','c5ad.12xlarge','i3en.24xlarge','z1d.2xlarge','r6id.xlarge','g5.2xlarge','r6a.metal','c4.8xlarge','c4.4xlarge','m6i.large','m5dn.2xlarge','r5dn.8xlarge','r6id.16xlarge','m6a.large','x2iedn.2xlarge','m5d.8xlarge','g5.4xlarge','m6id.xlarge','r6g.8xlarge','c6id.8xlarge','m5a.16xlarge','r6i.xlarge','c6i.32xlarge','c5ad.24xlarge','c6g.16xlarge','i4i.16xlarge','m6gd.xlarge','r5b.12xlarge','c7g.8xlarge','m2.4xlarge','t2.2xlarge','i3en.3xlarge','r6g.medium','c5d.large','t4g.nano','m6gd.medium','r5.24xlarge','c6a.48xlarge','x1e.8xlarge','m5n.16xlarge','m6id.2xlarge','r6g.large','m6gd.4xlarge','r5n.12xlarge','m6gd.12xlarge','c6i.8xlarge','im4gn.2xlarge','x1e.32xlarge','m5dn.12xlarge','i3en.12xlarge','f1.2xlarge','r6a.xlarge','c6gn.16xlarge','m5a.2xlarge','r6g.16xlarge','r3.large','x2gd.8xlarge','m5zn.6xlarge','r5a.2xlarge','c3.large','x2gd.metal','d2.8xlarge','m5d.xlarge','x1.16xlarge','c5ad.4xlarge','c6i.4xlarge','i2.2xlarge','c6id.large','m6gd.metal','c5ad.16xlarge','m5ad.8xlarge','g4dn.metal','m6id.32xlarge','r6g.4xlarge','r5a.16xlarge','m5ad.4xlarge','r3.8xlarge','m5ad.2xlarge','m5.24xlarge','a1.medium','t3a.small','c6id.16xlarge','c7g.16xlarge','c5n.4xlarge','m6g.metal','c6a.32xlarge','c5ad.xlarge','m5dn.xlarge','c5.4xlarge','m6a.metal','g4dn.12xlarge','m5ad.large','t4g.2xlarge','d3.8xlarge','c6g.12xlarge','r6i.24xlarge','m5d.large','t3.medium','i4i.4xlarge','c6i.24xlarge','g4ad.8xlarge','m6g.xlarge','r5b.metal','c7g.4xlarge','c5d.2xlarge','m5dn.8xlarge','h1.8xlarge','a1.large','t3.large','c6id.24xlarge','t3.2xlarge','x2iedn.metal','g5.24xlarge','t2.large','m5.12xlarge','x2iezn.8xlarge','c5d.12xlarge','z1d.xlarge','t3.xlarge','d2.2xlarge','u-3tb1.56xlarge','im4gn.xlarge','m5d.metal','c5a.xlarge','x2idn.24xlarge','r5.large','r5ad.8xlarge','r6i.4xlarge','c7g.xlarge','m6i.24xlarge','p2.8xlarge','c1.xlarge','g4ad.2xlarge','r6gd.metal','r6a.12xlarge','g4ad.4xlarge','r5a.12xlarge','c5.xlarge','c6gn.xlarge','t4g.medium','i4i.metal','m4.xlarge','r6gd.large','c5a.4xlarge','r6id.12xlarge','t3a.2xlarge','r6a.32xlarge','r6id.4xlarge','c6g.xlarge','r5n.4xlarge','m5.metal','m5d.16xlarge','c6gn.4xlarge','m2.2xlarge','inf1.xlarge','r5ad.12xlarge','m6i.8xlarge','m5.xlarge','r5a.large','m5n.8xlarge','c5a.12xlarge','g5g.2xlarge','r6a.8xlarge','g3.8xlarge','c4.large','x2gd.4xlarge','c5n.large','m5n.large','m6gd.16xlarge','t4g.small','c6gn.medium','c6i.16xlarge','c1.medium','g3.4xlarge','m4.large','m5.4xlarge','t3a.medium','r5ad.large','r5b.large','u-12tb1.112xlarge','c6a.12xlarge','inf1.6xlarge','r5b.24xlarge','m6a.4xlarge','t2.micro','m3.xlarge','vt1.3xlarge','vt1.6xlarge','c5d.4xlarge','r6id.32xlarge','c3.xlarge','d3.xlarge','m1.xlarge','m5n.4xlarge','r5.16xlarge','mac2.metal','c5d.24xlarge','r6g.12xlarge','r5b.xlarge','g5.12xlarge','x2gd.16xlarge','a1.xlarge','r5d.2xlarge','c6a.metal','r5.4xlarge','r5dn.metal','g5g.16xlarge','p3.2xlarge','r5d.4xlarge','r3.2xlarge','t4g.large','m5n.metal','m6i.metal','m6i.32xlarge','m5zn.3xlarge','r5d.xlarge','r5n.2xlarge','i2.xlarge','x2gd.medium','mac1.metal','g2.2xlarge','m4.16xlarge','d3en.4xlarge','c5.12xlarge','c6a.8xlarge','r5n.16xlarge','c5a.24xlarge','r5b.2xlarge','c6gd.16xlarge','m6i.4xlarge','m5d.24xlarge','inf1.2xlarge','g5g.metal','u-6tb1.56xlarge','c6gd.xlarge','r5.metal','c5.24xlarge','p4d.24xlarge','r6a.48xlarge','c5a.2xlarge','m5zn.12xlarge','r5n.24xlarge','m6g.large','r5d.16xlarge','x2idn.metal','h1.2xlarge','r5dn.xlarge','i4i.2xlarge','r5d.large','d3.2xlarge','r5b.4xlarge','x2gd.xlarge','m6g.4xlarge','r6gd.4xlarge','d3en.6xlarge','r3.4xlarge','h1.4xlarge','c6gn.12xlarge','c6g.large','r5dn.large','m6i.16xlarge','x2gd.2xlarge','m5n.12xlarge','c5n.9xlarge','m6g.8xlarge','r6id.24xlarge','r5a.4xlarge','m5dn.4xlarge','r6gd.16xlarge','i3.8xlarge','c5a.16xlarge','h1.16xlarge','c5n.18xlarge','r3.xlarge','t2.medium','r4.xlarge','m6id.metal','i3.xlarge','m6id.16xlarge','c4.2xlarge','i2.4xlarge','is4gen.large','r6i.16xlarge','x2iedn.16xlarge','t4g.micro','c7g.medium','x2idn.32xlarge','x2gd.12xlarge','c5d.xlarge','m5zn.xlarge','r5ad.16xlarge','g5.16xlarge','a1.2xlarge','m5.16xlarge','t3.nano','r5dn.16xlarge','r6gd.8xlarge','z1d.metal','c5ad.2xlarge','c3.2xlarge','c5.metal','x2iezn.2xlarge','r6id.metal','r4.2xlarge','m5ad.xlarge','g4dn.xlarge','r5ad.xlarge','m5ad.24xlarge','i2.8xlarge','u-6tb1.112xlarge','g5.xlarge','x2gd.large','r5n.xlarge','g5g.8xlarge','t3.micro','r6i.metal','m5dn.metal','x1e.16xlarge','t3a.large','c4.xlarge','c7g.2xlarge','c5.9xlarge','m6g.16xlarge','c5n.metal','m5zn.metal','c6id.metal','z1d.6xlarge','c5d.18xlarge','r6id.large','c6id.32xlarge','r6i.12xlarge','c5d.metal','z1d.12xlarge','r5d.8xlarge','m6g.12xlarge','m5a.8xlarge','m5dn.16xlarge','m6gd.8xlarge','a1.4xlarge','m3.2xlarge','r5.xlarge','r6a.24xlarge','r5b.16xlarge','r5d.24xlarge','i3.large','x2iezn.12xlarge','m5dn.24xlarge','r5dn.12xlarge','m6a.16xlarge','c3.4xlarge','c6a.xlarge','m4.2xlarge','is4gen.2xlarge','r6a.4xlarge','i4i.large','a1.metal','c5.2xlarge','r5.2xlarge','x2idn.16xlarge','m5d.2xlarge','r5.8xlarge','c6a.4xlarge','m5d.12xlarge','t4g.xlarge','r5ad.2xlarge','x2iedn.4xlarge','c5.large','r6g.2xlarge','r4.8xlarge','d2.4xlarge','p3.16xlarge','r5.12xlarge','x2iedn.32xlarge','r6g.xlarge','vt1.24xlarge','r6i.32xlarge','x2iezn.metal','c6i.metal','c5ad.large','r5n.8xlarge','i3.2xlarge','x1e.4xlarge','c6g.4xlarge','r6gd.12xlarge','m5n.24xlarge','m6gd.2xlarge','c6id.xlarge','i3en.large','x1e.xlarge','is4gen.medium','t2.xlarge','c6i.xlarge','m6id.4xlarge','c6gn.8xlarge','m5n.2xlarge','m5.2xlarge','r5b.8xlarge','r5a.24xlarge','x2iedn.24xlarge','im4gn.16xlarge','m6a.xlarge','c6a.2xlarge','im4gn.large','c6gd.4xlarge','c6g.medium','m5a.large','i3en.xlarge','m6i.xlarge','r5dn.4xlarge','c7g.large','r5ad.4xlarge','t3.small','r6id.8xlarge','d3.4xlarge','g5.8xlarge','i3.metal','m2.xlarge','r5d.12xlarge','x1.32xlarge','r5d.metal','g3s.xlarge','c6id.12xlarge','m6a.24xlarge','im4gn.4xlarge','c6gn.2xlarge','i4i.32xlarge','r5dn.2xlarge','m6id.24xlarge','m5ad.16xlarge','i4i.xlarge','c7g.12xlarge','p2.xlarge','dl1.24xlarge','c6g.8xlarge','c5ad.8xlarge','m3.medium','c6i.12xlarge','c6id.2xlarge','inf1.24xlarge','x2iezn.6xlarge','m6gd.large','p2.16xlarge','m3.large','g4dn.16xlarge','r6g.metal','r4.large','m5n.xlarge','m4.10xlarge','i3en.2xlarge','r5a.8xlarge','t2.small','m6id.8xlarge','c6gn.large','m5a.xlarge','c6gd.medium','r6a.16xlarge','p3.8xlarge','r6i.8xlarge','m5.large','m6a.32xlarge','g4ad.16xlarge','i3.16xlarge','t3a.nano','z1d.large','c5a.8xlarge','r6a.large','c6gd.2xlarge','g5g.xlarge','c6gd.8xlarge','m1.medium','z1d.3xlarge','f1.4xlarge','r4.4xlarge','m6g.2xlarge','c5.18xlarge','d3en.xlarge','r5dn.24xlarge','c6g.metal','m6i.12xlarge','c5n.xlarge','i3en.6xlarge','g5.48xlarge','g4dn.2xlarge','f1.16xlarge','m5d.4xlarge','i4i.8xlarge','t2.nano','c6gd.large','is4gen.8xlarge','r5n.large','m6a.8xlarge','m6i.2xlarge','c6a.large','i3en.metal','g5g.4xlarge','m6a.12xlarge','g4dn.8xlarge','c6g.2xlarge','i3.4xlarge','m5a.12xlarge','r6i.2xlarge','g4dn.4xlarge','c5d.9xlarge','t3a.micro','d3en.12xlarge','c5a.large','t3a.xlarge','m6id.large','d2.xlarge','m1.large','g2.8xlarge','c6gd.12xlarge','p3dn.24xlarge','m6a.48xlarge','m6g.medium','r6i.large','m5a.24xlarge','m1.small','cc2.8xlarge','c6id.4xlarge','g4ad.xlarge','x2iedn.8xlarge','u-9tb1.112xlarge','r5n.metal','null']
                            }
                        '''
                ]
            ]
        ],
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: '''Select the VPC in which the Launch Template Instance needs to be provisioned. The default VPC are showed up as None. Select null if you Don't want to include VPC, subnet and security group in launch template''', 
            name: 'VPC_NAME', 
            randomName: 'choice-parameter-5631311156178619', 
            referencedParameters: 'ACTION,CREDENTIAL,REGION,INSTANCE_NAME', 
            script: [
                $class: 'GroovyScript',  
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        "return['error']"
                ],
                script: [
                    classpath: [], 
                    sandbox: false, 
                    script: 
                    ''' import jenkins.model.*
                        import org.apache.http.client.methods.*
                        import org.apache.http.impl.client.*
                        import groovy.json.JsonSlurper;
                        
                        if (ACTION.equals("Create") || ACTION.equals("Modify")){
                            // Looks up Jenkins credentials
                            def jenkinsCredentials = com.cloudbees.plugins.credentials.CredentialsProvider.lookupCredentials(
                                    com.cloudbees.plugins.credentials.Credentials.class,
                                    Jenkins.instance,
                                    null,
                                    null
                            ); 

                            def AWS_ACCESS_KEY_ID
                            def AWS_SECRET_ACCESS_KEY
                            def key 
                            // Iterates over the Jenkins credentials.
                            for (creds in jenkinsCredentials) {
                                // Checks if the credential ID matches the referenced parameter CREDENTIAL.
                                if(creds.id == "$CREDENTIAL"){
                                    // Retrieves the credential secret as a string.
                                    key = creds.secret.toString(creds.secret);
                                    // Creates a new instance of the JSON Slurper.
                                    def jsonSlurper = new JsonSlurper()
                                    // Parses the credential secret as JSON and assigns it to the cfg variable.
                                    cfg = jsonSlurper.parseText(key)
                                    
                                    AWS_ACCESS_KEY_ID = cfg['accessKeyId']  
                                    AWS_SECRET_ACCESS_KEY = cfg['secretAccessKey']
                                    // Executes the AWS CLI command to list all vpc in a particular region
                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-vpcs  --query=Vpcs[*].{Name:Tags[?Key==`Name`].Value|[0]}  --output text --region $REGION"
                                    def proc = command.execute()
                                    proc.waitFor()              
                                    def output = proc.in.text
                                    def exitcode= proc.exitValue()
                                    def error = proc.err.text
                                    // If there's an error, print it and return the exit code
                                    if (error) {
                                        println "Std Err: ${error}"
                                        println "Process exit code: ${exitcode}"
                                        return exitcode
                                    }
                                    // The output is tokenized and returned lst variable. If there's any error, the script prints the error message and returns the exit code.
                                    def lst = output.tokenize()
                                    // the "null" will be appended to the lst values
                                    lst.add("null")
                                    return lst 
                                    // return output.tokenize() 
                                } 
                            }
                        }                        
                    '''
                ]
            ]
        ],
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HIDDEN_HTML', 
            description: 'Select the VPC in which the Launch Template Instance needs to be provisioned', 
            name: 'VPC_ID',
            omitValueField: true, 
            randomName: 'choice-parameter-56313116178619', 
            referencedParameters: 'ACTION,CREDENTIAL,VPC_NAME,REGION,INSTANCE_NAME', 
            script: [
                $class: 'GroovyScript',  
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        "return['error']"
                ],
                script: [
                    classpath: [], 
                    sandbox: false, 
                    script: 
                    ''' import jenkins.model.*
                        import org.apache.http.client.methods.*
                        import org.apache.http.impl.client.*
                        import groovy.json.JsonSlurper;
                        
                        if (ACTION.equals("Create" ) || ACTION.equals("Modify")){
                            def jenkinsCredentials = com.cloudbees.plugins.credentials.CredentialsProvider.lookupCredentials(
                                    com.cloudbees.plugins.credentials.Credentials.class,
                                    Jenkins.instance,
                                    null,
                                    null
                            ); 

                            def AWS_ACCESS_KEY_ID
                            def AWS_SECRET_ACCESS_KEY
                            def key 
                            for (creds in jenkinsCredentials) {
                                if(creds.id == "$CREDENTIAL"){
                                    key = creds.secret.toString(creds.secret);
                                    def jsonSlurper = new JsonSlurper()
                                    cfg = jsonSlurper.parseText(key)
                                    
                                    AWS_ACCESS_KEY_ID = cfg['accessKeyId']  
                                    AWS_SECRET_ACCESS_KEY = cfg['secretAccessKey']
                                    //Checks for the VPC_NAME parameter and gets VPCID based on thae value of VPC_NAME
                                    if(VPC_NAME.equals("None") || VPC_NAME.equals("Default")){
                                        command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-vpcs --query=Vpcs[].VpcId --region $REGION  --filters Name=is-default,Values=true --output text"
                                    }
                                    else{
                                        command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-vpcs --filters Name=tag:Name,Values=$VPC_NAME --query=Vpcs[].VpcId --region $REGION --output text"
                                    }

                                    
                                    def proc = command.execute()
                                    proc.waitFor()              
                                    def output = proc.in.text
                                    def exitcode= proc.exitValue()
                                    def error = proc.err.text
                                    
                                    if (error) {
                                        println "Std Err: ${error}"
                                        println "Process exit code: ${exitcode}"
                                        return exitcode
                                    }
                                    // Construct the input box with the VPC ID value
                                    inputBox = "<input name='value' class='setting-input' type='text' value=$output>"
                                    return inputBox
                                } 
                            }
                        }                        
                    '''
                ]
            ]
        ],
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: '''Subnet in which the Launch Template instance needs to be launched. Select null if you Don't want to include subnet in launch template''', 
            name: 'SUBNET_NAME', 
            randomName: 'choice-parameter-5632214451178619', 
            referencedParameters: 'ACTION,CREDENTIAL,VPC_NAME,VPC_ID,REGION,INSTANCE_NAME', 
            script: [
                $class: 'GroovyScript', 
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        'return ""'
                ], 
                script: [
                    classpath: [], 
                    sandbox: false, 
                    script: 
                    // The Groovy script uses the AWS credentials provided by the user to make API calls to AWS to retrieve the list of available subnets based on the specified filters. It retrieves the names of all the subnets in the specified VPC_ID.
                    ''' 
                        import jenkins.model.*
                        import org.apache.http.client.methods.*
                        import org.apache.http.impl.client.*
                        import groovy.json.JsonSlurper;
                        if (ACTION.equals("Create") || ACTION.equals("Modify")){
                            def jenkinsCredentials = com.cloudbees.plugins.credentials.CredentialsProvider.lookupCredentials(
                                    com.cloudbees.plugins.credentials.Credentials.class,
                                    Jenkins.instance,
                                    null,
                                    null
                            ); 

                            def AWS_ACCESS_KEY_ID
                            def AWS_SECRET_ACCESS_KEY
                            def key 

                            for (creds in jenkinsCredentials) {
                                if(creds.id == "$CREDENTIAL"){
                                    key = creds.secret.toString(creds.secret);
                                    def jsonSlurper = new JsonSlurper()
                                    cfg = jsonSlurper.parseText(key)
                                    
                                    AWS_ACCESS_KEY_ID = cfg['accessKeyId']  
                                    AWS_SECRET_ACCESS_KEY = cfg['secretAccessKey']
                                    
                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-subnets --filter Name=vpc-id,Values=$VPC_ID --query=Subnets[*].Tags[?Key=='Name'].Value --output text --region $REGION"
                                    
                                    def proc = command.execute()
                                    proc.waitFor()              
                                    def output = proc.in.text
                                    def exitcode= proc.exitValue()
                                    def error = proc.err.text
                                    
                                    if (error) {
                                        println "Std Err: ${error}"
                                        println "Process exit code: ${exitcode}"
                                        return exitcode
                                    }
                                    def lst = output.tokenize()
                                    lst.add("null")
                                    return lst 
                                    // return output.tokenize() 
                                } 
                            }

                        }
                    '''
                ]
            ]
        ],
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HIDDEN_HTML', 
            description: 'Select the SUBNET in which the Launch Template Instance needs to be provisioned', 
            name: 'SUBNET_ID', 
            omitValueField: true,
            randomName: 'choice-parameter-563116178619', 
            referencedParameters: 'ACTION,CREDENTIAL,SUBNET_NAME,VPC_NAME,REGION,INSTANCE_NAME', 
            script: [
                $class: 'GroovyScript',  
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        'return  ""'
                ],
                script: [
                    classpath: [], 
                    sandbox: false, 
                    script: 
                    //The parameter allows the user to select a subnet in which an EC2 instance should be provisioned. The script uses the AWS CLI to retrieve the Subnet ID based on the selected parameters (Action, Credential, Subnet Name, VPC Name, Region, Instance Name). 
                    // It also handles errors and returns an input box with the retrieved Subnet ID as the default value.
                    ''' import jenkins.model.*
                        import org.apache.http.client.methods.*
                        import org.apache.http.impl.client.*
                        import groovy.json.JsonSlurper;
                        
                        if (ACTION.equals("Create") || ACTION.equals("Modify")){
                            def jenkinsCredentials = com.cloudbees.plugins.credentials.CredentialsProvider.lookupCredentials(
                                    com.cloudbees.plugins.credentials.Credentials.class,
                                    Jenkins.instance,
                                    null,
                                    null
                            ); 

                            def AWS_ACCESS_KEY_ID
                            def AWS_SECRET_ACCESS_KEY
                            def key 
                            for (creds in jenkinsCredentials) {
                                if(creds.id == "$CREDENTIAL"){
                                    key = creds.secret.toString(creds.secret);
                                    def jsonSlurper = new JsonSlurper()
                                    cfg = jsonSlurper.parseText(key)
                                    
                                    AWS_ACCESS_KEY_ID = cfg['accessKeyId']  
                                    AWS_SECRET_ACCESS_KEY = cfg['secretAccessKey']
                                    
                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-subnets --filter Name=tag:Name,Values=$SUBNET_NAME --query=Subnets[].SubnetId --region=$REGION --output text"
                                    def proc = command.execute()
                                    proc.waitFor()              
                                    def output = proc.in.text
                                    def exitcode= proc.exitValue()
                                    def error = proc.err.text
                                    
                                    if (error) {
                                        println "Std Err: ${error}"
                                        println "Process exit code: ${exitcode}"
                                        return exitcode
                                    }
                                    inputBox = "<input name='value' class='setting-input' type='text' value=$output>"
                                    return inputBox
                                } 
                            }
                        } 
                                     
                    '''
                ]
            ]
        ],
        //This Groovy script defines a dynamic choice parameter for a Jenkins job named "SECURITY_GROUP_NAME" which allows users to select a security group that needs to be attached to an EC2 instance. The parameter is of type CascadeChoiceParameter with multiple checkboxes, and has a description and a randomly generated name.
        //The script uses the AWS CLI to get a list of security groups associated with the specified VPC ID, in the given region. It sets up the AWS credentials by looking up the Jenkins credentials using the provided credential ID. Then it executes the 
        //aws ec2 describe-security-groups command with the necessary parameters, and returns the output as a list of strings surrounded by double quotes, which is then used to populate the input field in the Jenkins job configuration.
        //If the ACTION parameter is not equal to "Create" or "Modify", the script will return an empty string as fallback. If there are any errors during the execution of the script, it will print out the error message along with the process exit code. The script runs in the Jenkins sandbox mode, which limits the access to certain APIs and resources to ensure security.
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_CHECKBOX', 
            description: '''Security group which needs to be attached to Launch Template. if u need to create a new security group please use SECURITYGROUP-PIPELINE. Don't Select security groups if you Don't want to include security group in launch template''', 
            name: 'SECURITY_GROUP_NAME', 
            randomName: 'choice-parameter-5631314417619', 
            referencedParameters: 'ACTION,CREDENTIAL,VPC_NAME,REGION,VPC_ID', 
            script: [
                $class: 'GroovyScript', 
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        'return ""'
                ], 
                script: [
                    classpath: [], 
                    sandbox: false, 
                    script: 
                    ''' 
                        import jenkins.model.*
                        import org.apache.http.client.methods.*
                        import org.apache.http.impl.client.*
                        import groovy.json.JsonSlurper;
                        if (ACTION.equals("Create") || ACTION.equals("Modify")){
                            def jenkinsCredentials = com.cloudbees.plugins.credentials.CredentialsProvider.lookupCredentials(
                                    com.cloudbees.plugins.credentials.Credentials.class,
                                    Jenkins.instance,
                                    null,
                                    null
                            ); 

                            def AWS_ACCESS_KEY_ID
                            def AWS_SECRET_ACCESS_KEY
                            def key 

                            for (creds in jenkinsCredentials) {
                                if(creds.id == "$CREDENTIAL"){
                                    key = creds.secret.toString(creds.secret);
                                    def jsonSlurper = new JsonSlurper()
                                    cfg = jsonSlurper.parseText(key)
                                    
                                    AWS_ACCESS_KEY_ID = cfg['accessKeyId']  
                                    AWS_SECRET_ACCESS_KEY = cfg['secretAccessKey']

                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY  aws ec2 describe-security-groups --query=SecurityGroups[].GroupName --output text --region=$REGION --filter Name=vpc-id,Values=$VPC_ID"
                                    
                                
                                    def proc = command.execute()
                                    proc.waitFor()              
                                    def output = proc.in.text
                                    def exitcode= proc.exitValue()
                                    def error = proc.err.text
                                    
                                    if (error) {
                                        println "Std Err: ${error}"
                                        println "Process exit code: ${exitcode}"
                                        return exitcode
                                    }
                                    return output.tokenize()
                                } 
                            }
                        }
                    '''
                ]
            ]
        ],
        // This specific parameter is a Dynamic Reference Parameter which allows for selecting a security group in which an EC2 instance needs to be provisioned.
        // The script sets up the AWS credentials, executes a command to describe the security groups of the specified VPC with the given name, and returns the output as a list of strings surrounded by double quotes. The returned value is used to populate the input field in the Jenkins job configuration.
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HIDDEN_HTML', 
            description: 'Select the SecurityGroup in which the Launch Template Instance needs to be provisioned', 
            name: 'SECURITY_GROUP_ID', 
            omitValueField: true,
            randomName: 'choice-parameter-56311617864419', 
            referencedParameters: 'ACTION,CREDENTIAL,VPC_ID,SECURITY_GROUP_NAME,REGION', 
            script: [
                $class: 'GroovyScript',  
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        "return['error']"
                ],
                script: [
                    classpath: [], 
                    sandbox: false, 
                    script: 
                    ''' import jenkins.model.*
                        import org.apache.http.client.methods.*
                        import org.apache.http.impl.client.*
                        import groovy.json.JsonSlurper;
                        
                        if (ACTION.equals("Create") || ACTION.equals("Modify")){
                            def jenkinsCredentials = com.cloudbees.plugins.credentials.CredentialsProvider.lookupCredentials(
                                    com.cloudbees.plugins.credentials.Credentials.class,
                                    Jenkins.instance,
                                    null,
                                    null
                            ); 

                            def AWS_ACCESS_KEY_ID
                            def AWS_SECRET_ACCESS_KEY
                            def key 
                            for (creds in jenkinsCredentials) {
                                if(creds.id == "$CREDENTIAL"){
                                    key = creds.secret.toString(creds.secret);
                                    def jsonSlurper = new JsonSlurper()
                                    cfg = jsonSlurper.parseText(key)
                                    
                                    AWS_ACCESS_KEY_ID = cfg['accessKeyId']  
                                    AWS_SECRET_ACCESS_KEY = cfg['secretAccessKey']

                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-security-groups --filter Name=vpc-id,Values=$VPC_ID Name=group-name,Values=$SECURITY_GROUP_NAME --query=SecurityGroups[*].[GroupId] --region=$REGION --output text"
                                    def proc = command.execute()
                                    proc.waitFor()              
                                    def output = proc.in.text
                                    def out = output.tokenize()
                                    List modifiedout = out.collect{ '"' + it + '"'}
                                    def exitcode= proc.exitValue()
                                    def error = proc.err.text
                                    
                                    if (error) {
                                        println "Std Err: ${error}"
                                        println "Process exit code: ${exitcode}"
                                        return exitcode
                                    }
                                    inputBox = "<input name='value' class='setting-input' type='text' value='$modifiedout'>"
                                    return inputBox
                                } 
                            }
                        }                        
                    '''
                ]
            ]
        ],
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: '''Key pair which needs to used to provision Launch Template, if u need to use a new key pair please create a new key-pair using KEY-PAIR-PIPELINE. Select null if you Don't want to include key pair in launch template''', 
            name: 'KEY_PAIR', 
            randomName: 'choice-parameter-56313144356155119', 
            referencedParameters: 'ACTION,CREDENTIAL,REGION', 
            script: [
                $class: 'GroovyScript', 
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        'return  ""'
                ], 
                script: [
                    classpath: [], 
                    sandbox: false, 
                    script: 
                    '''import jenkins.model.*
                        import org.apache.http.client.methods.*
                        import org.apache.http.impl.client.*
                        import groovy.json.JsonSlurper;
                        if (ACTION.equals("Create") || ACTION.equals("Modify")){
                            def jenkinsCredentials = com.cloudbees.plugins.credentials.CredentialsProvider.lookupCredentials(
                                    com.cloudbees.plugins.credentials.Credentials.class,
                                    Jenkins.instance,
                                    null,
                                    null
                            ); 

                            def AWS_ACCESS_KEY_ID
                            def AWS_SECRET_ACCESS_KEY
                            def key 

                            for (creds in jenkinsCredentials) {
                                if(creds.id == "$CREDENTIAL"){
                                    key = creds.secret.toString(creds.secret);
                                    def jsonSlurper = new JsonSlurper()
                                    cfg = jsonSlurper.parseText(key)
                                    
                                    AWS_ACCESS_KEY_ID = cfg['accessKeyId']  
                                    AWS_SECRET_ACCESS_KEY = cfg['secretAccessKey']
                                    //  Execute an AWS CLI command to get the available key pairs
                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-key-pairs  --query=KeyPairs[].KeyName --region=$REGION --output text"
                                    def proc = command.execute()
                                    proc.waitFor()              
                                    def output = proc.in.text
                                    def exitcode= proc.exitValue()
                                    def error = proc.err.text
                                    
                                    if (error) {
                                        println "Std Err: ${error}"
                                        println "Process exit code: ${exitcode}"
                                        return exitcode
                                    }
                                    def lst = output.tokenize()
                                    lst.add("null")
                                    return lst 
                                } 
                            }
                        }
                    '''
                ]
            ]
        ],

        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: '''Enter the storage volume size to include it in the launch template, whose type will be GP3. For Linux flavour the size should be >= 8GB & for windows flavour the size should be >= 30GB. The format of input incase of terraform if u require two additional volumes is ["10","20"]. The format of input incase of Cloudformation if u require two additional volumes is 10,20 ''', 
            name: 'VOLUME_SIZE', 
            omitValueField: true,
            randomName: 'choice-parameter-5631314451178619', 
            referencedParameters: 'ACTION', 
            script: [
                $class: 'GroovyScript', 
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        'return ""'
                ], 
                script: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                    // defines a groovy script to generate the input box dynamically
                        ''' if (ACTION.equals("Create") || ACTION.equals("Modify")){
                            inputBox = "<input name='value' class='setting-input'  type='text'>"
                            return inputBox
                            }
                        '''
                ]
            ]
        ],
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: '''The IAM instance profile for the instance. Select null if you Don't want to include IAM instance profile in launch template''', 
            name: 'IAM_INSTANCE_PROFILE', 
            randomName: 'choice-parameter-56313414417619', 
            referencedParameters: 'ACTION,CREDENTIAL,VPC_NAME,REGION,VPC_ID', 
            script: [
                $class: 'GroovyScript', 
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        'return ""'
                ], 
                script: [
                    classpath: [], 
                    sandbox: false, 
                    script: 
                    ''' 
                        import jenkins.model.*
                        import org.apache.http.client.methods.*
                        import org.apache.http.impl.client.*
                        import groovy.json.JsonSlurper;
                        if (ACTION.equals("Create") || ACTION.equals("Modify")){
                            def jenkinsCredentials = com.cloudbees.plugins.credentials.CredentialsProvider.lookupCredentials(
                                    com.cloudbees.plugins.credentials.Credentials.class,
                                    Jenkins.instance,
                                    null,
                                    null
                            ); 

                            def AWS_ACCESS_KEY_ID
                            def AWS_SECRET_ACCESS_KEY
                            def key 

                            for (creds in jenkinsCredentials) {
                                if(creds.id == "$CREDENTIAL"){
                                    key = creds.secret.toString(creds.secret);
                                    def jsonSlurper = new JsonSlurper()
                                    cfg = jsonSlurper.parseText(key)
                                    
                                    AWS_ACCESS_KEY_ID = cfg['accessKeyId']  
                                    AWS_SECRET_ACCESS_KEY = cfg['secretAccessKey']
                                    // Execute an AWS CLI command to get the available instance profile
                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY  aws iam list-instance-profiles --query=InstanceProfiles[].InstanceProfileName --output text --region=$REGION"
                                    def proc = command.execute()
                                    proc.waitFor()              
                                    def output = proc.in.text
                                    // def out = output.tokenize()
                                    // List modifiedout = out.collect{ '"' + it + '"'}
                                    def exitcode= proc.exitValue()
                                    def error = proc.err.text
                                    
                                    if (error) {
                                        println "Std Err: ${error}"
                                        println "Process exit code: ${exitcode}"
                                        return exitcode
                                    }
                                    def lst = output.tokenize()
                                    lst.add(0,"null")
                                    return lst 
                                    // return output.tokenize() 
                                } 
                            }
                        }
                    '''
                ]
            ]
        ],
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', //The choiceType is set to ET_FORMATTED_HTML, which specifies that the input field should support HTML formatting.
            omitValueField: true,
            description: '''Specify user data to provide commands or a command script to run when you launch your instance. The format of the input for terraform should be <<EOT scritp-in-new-line EOT. Example script
             <<EOT
                #! /bin/bash
                sudo apt-get update
                sudo apt-get install -y apache2
             EOT. The format of input for Cloudformation is #! /bin/bash sudo apt-get update sudo apt-get install -y apache2''', 
            name: 'USER_DATA', 
            randomName: 'choice-parameter-56313113156178619', 
            referencedParameters: 'ACTION', 
            script: [
                $class: 'GroovyScript',  
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        'return  ""'
                ],
                script: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                    //The script defines the behavior of the parameter. If the ACTION parameter is 'Create' or 'Modify', the script returns a textarea input field for the user to enter their user data. Otherwise, it returns an empty string.
                        ''' if (ACTION.equals("Create") || ACTION.equals("Modify")){
                            return """<textarea name=\"value\" rows=\"8\" class=\"setting-input\"></textarea>"""
                            }
                        '''
                ]
            ]
       ]       
    ])
])



pipeline{
    agent any
    options {
        ansiColor('xterm')
    }
    environment {
        resource = "launch-template"
        resource_name = "${env.LAUNCH_TEMPLATE_NAME}" 
        aws_region = "${env.REGION}".replace(',',''); // Set the aws_region variable to the value of REGION environment variable with commas removed.
        PROJECT_NAME = sh (returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 1").trim() // Extract the project name from the CREDENTIAL parameter using shell command and set it to the PROJECT_NAME variable.
        ACCOUNT_ID = sh (returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 2").trim() // Extract the account ID from the CREDENTIAL parameter using shell command and set it to the ACCOUNT_ID variable.
    }

    stages{
        stage("Workspace cleanup"){
            steps{       
                script{ // Run a Groovy script.
                    if (params.LAUNCH_TEMPLATE_NAME == '') { // If the LAUNCH_TEMPLATE_NAME parameter is empty,
                        currentBuild.displayName = "Loading parameters" // Set the display name to "Loading parameters".
                        currentBuild.result = 'ABORTED' // Set the build result to ABORTED.
                        error('The parameter value is null') // Print an error message.
                    }     
                    currentBuild.displayName = "${ACTION} the LaunchTemplate ${params.LAUNCH_TEMPLATE_NAME} - ${IAC_TOOL}" // Set the display name to "ACTION the LaunchTemplate LAUNCH_TEMPLATE_NAME".
                    currentBuild.description = "${ACTION} the LaunchTemplate ${params.LAUNCH_TEMPLATE_NAME} - ${IAC_TOOL}" // Set the description to "ACTION the LaunchTemplate LAUNCH_TEMPLATE_NAME".  
                } 
                wrkspace_cleanup () // Call the wrkspace_cleanup function(defined in the jenkins shared library) to clean up the workspace.
            }
        }
        stage("SCM Checkout"){
            steps{  
                script{                              
                    if (env.IAC_TOOL == "Terraform") {
                    tf_scm_checkout ()  // Calls a function named "tf_scm_checkout()" to perform an SCM checkout (defined in the jenkins shared library)
                    }
                    else {
                    cft_scm_checkout ()  // Calls a function named "tf_scm_checkout()" to perform an SCM checkout (defined in the jenkins shared library)
                    }
                }
            }
        }

        stage('2.Provisioning Terraform'){
            steps{
                script{ // Allows for execution of arbitrary Groovy code within the pipeline
                    if (env.IAC_TOOL == "Terraform") {
                        withAWS(region:"$REGION"){
                        tf_resource_provision () // Calls a function named "tf_resource_provision()" to provision a resource (defined in the jenkins shared library)
                        git_commit() // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
                        }
                    }
                    else {
                        withAWS(region:"$REGION"){
                        cft_resource_provision ('launch-template.j2') // Calls a function named "cft_resource_provision()" here we need to provide the "jinja2" file name to provision a resource (defined in the jenkins shared library)                     
                        cft_git_commit () // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
                        }
                    }
                }
            }
        }
    }
}
