properties([
    parameters([
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

        [$class: 'ChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: 'Choose anyone of the below option to perform the action', 
            filterLength: 1, 
            filterable: false, 
            name: 'ACTION', 
            script: [
                $class: 'GroovyScript', 
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        "return['Create']"
                ], 
                script: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        "return ['Create','Modify','Delete']"
                ]
            ]
        ],
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
            name: 'CLUSTER_NAME', 
            trim: true,
            description: 'The name of eks cluster needs to be provisioned.'
        ),

        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: 'EC2 Instance type in which the resources need to be provisioned. Refer the link to get detailed Instance types https://instances.vantage.sh/', 
            // filterLength: 4, 
            // filterable: true, 
            randomName: 'choice-parameter-5631311156178961543', 
            referencedParameters: 'ACTION', 
            name: 'K8S_VERSION', 
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
                                return ['1.26','1.25','1.24','1.23','1.22']
                            }
                        '''
                ]
            ]
        ],
        string(
            defaultValue: '', 
            name: 'CLUSTER_ENDPOINT_PUBLIC_ACCESS', 
            trim: true,
            description: 'Controls whether the EKS cluster API server endpoint should have public access. Set to true for public access and false for private access.'
        ),
        string(
            defaultValue: '', 
            name: 'CLUSTER_ENDPOINT_PRIVATE_ACCESS', 
            trim: true,
            description: 'Controls whether the EKS cluster API server endpoint should have private access. Set to true for private access and false for public access'
        ),

        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: 'Select the VPC in which the EC2 Instance needs to be provisioned. The default VPC are showed up as None.', 
            name: 'VPC_NAME', 
            randomName: 'choice-parameter-5631311156178619', 
            referencedParameters: 'ACTION,CREDENTIAL,REGION,CLUSTER_NAME', 
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
                                if(ACTION.equals("Modify")){
                                    command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws eks describe-cluster --name $CLUSTER_NAME  --query=cluster.resourcesVpcConfig.vpcId --output text --region $REGION"
                                }
                                else{
                                    command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-vpcs --query=Vpcs[*].{Name:Tags[?Key==`Name`].Value|[0]} --output text --region $REGION"
                                }

                                    // def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-vpcs  --query=Vpcs[*].{Name:Tags[?Key==`Name`].Value|[0]}  --output text --region $REGION"
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
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HIDDEN_HTML', 
            description: 'Select the VPC in which the EC2 Instance needs to be provisioned', 
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
                        'return  ""'
                ],
                script: [
                    classpath: [], 
                    sandbox: false, 
                    script: 
                    ''' import jenkins.model.*
                        import org.apache.http.client.methods.*
                        import org.apache.http.impl.client.*
                        import groovy.json.JsonSlurper;
                        
                        if (ACTION.equals("Create" )){
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
                                    inputBox = "<input name='value' class='setting-input' type='text' value=$output>"
                                    return inputBox
                                } 
                            }
                        }
                        if (ACTION.equals("Modify")){
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
                                    
                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws eks describe-cluster --name $Cluster_NAME --region us-east-1 --query "cluster.resourcesVpcConfig.vpcId" --output text --region $REGION"
                                                                    
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
    [$class: 'CascadeChoiceParameter', 
        choiceType: 'PT_CHECKBOX', 
        description: 'Subnet in which the EC2 instance needs to be launched.', 
        name: 'SUBNET_NAME', 
        randomName: 'choice-parameter-5632214451178619345232', 
        referencedParameters: 'ACTION,CREDENTIAL,VPC_NAME,VPC_ID,REGION,CLUSTER_NAME', 
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
                                if(ACTION.equals("Modify")){
                                    command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws eks describe-cluster --name $CLUSTER_NAME --region $REGION --query=cluster.resourcesVpcConfig.subnetIds --output text"
                                }
                                else{
                                    command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-subnets --filter Name=vpc-id,Values=$VPC_ID --query=Subnets[*].Tags[?Key=='Name'].Value --output text --region $REGION"
                                }
                                // def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-subnets --filter Name=vpc-id,Values=$VPC_ID --query=Subnets[*].Tags[?Key=='Name'].Value --output text --region $REGION"
                                
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
    [$class: 'DynamicReferenceParameter', 
        choiceType: 'ET_FORMATTED_HIDDEN_HTML', 
        description: 'Select the SUBNET in which the EC2 Instance needs to be provisioned', 
        name: 'SUBNET_ID', 
        randomName: 'choice-parameter-563221774451178619345232', 
        omitValueField: true,
        referencedParameters: 'ACTION,CREDENTIAL,SUBNET_NAME,VPC_NAME,REGION,INSTANCE_NAME,VPC_ID', 
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
            description: 'EC2 Instance type in which the resources need to be provisioned. Refer the link to get detailed Instance types https://instances.vantage.sh/', 
            filterLength: 4, 
            filterable: true, 
            randomName: 'choice-parameter-5631311156178961543001', 
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
                                return ['r6a.2xlarge','im4gn.8xlarge','r5ad.24xlarge','c3.8xlarge','c6a.16xlarge','m5a.4xlarge','is4gen.4xlarge','m5zn.2xlarge','m5zn.large','r6gd.medium','r4.16xlarge','c6i.large','m6id.12xlarge','r6id.2xlarge','m4.4xlarge','c6gd.metal','is4gen.xlarge','r6gd.2xlarge','m5.8xlarge','d3en.2xlarge','m6a.2xlarge','r6gd.xlarge','x2iedn.xlarge','m5ad.12xlarge','r5a.xlarge','g3.16xlarge','m5dn.large','c6a.24xlarge','d3en.8xlarge','x2iezn.4xlarge','t1.micro','c6i.2xlarge','c5n.2xlarge','x1e.2xlarge','c5ad.12xlarge','i3en.24xlarge','z1d.2xlarge','r6id.xlarge','g5.2xlarge','r6a.metal','c4.8xlarge','c4.4xlarge','m6i.large','m5dn.2xlarge','r5dn.8xlarge','r6id.16xlarge','m6a.large','x2iedn.2xlarge','m5d.8xlarge','g5.4xlarge','m6id.xlarge','r6g.8xlarge','c6id.8xlarge','m5a.16xlarge','r6i.xlarge','c6i.32xlarge','c5ad.24xlarge','c6g.16xlarge','i4i.16xlarge','m6gd.xlarge','r5b.12xlarge','c7g.8xlarge','m2.4xlarge','t2.2xlarge','i3en.3xlarge','r6g.medium','c5d.large','t4g.nano','m6gd.medium','r5.24xlarge','c6a.48xlarge','x1e.8xlarge','m5n.16xlarge','m6id.2xlarge','r6g.large','m6gd.4xlarge','r5n.12xlarge','m6gd.12xlarge','c6i.8xlarge','im4gn.2xlarge','x1e.32xlarge','m5dn.12xlarge','i3en.12xlarge','f1.2xlarge','r6a.xlarge','c6gn.16xlarge','m5a.2xlarge','r6g.16xlarge','r3.large','x2gd.8xlarge','m5zn.6xlarge','r5a.2xlarge','c3.large','x2gd.metal','d2.8xlarge','m5d.xlarge','x1.16xlarge','c5ad.4xlarge','c6i.4xlarge','i2.2xlarge','c6id.large','m6gd.metal','c5ad.16xlarge','m5ad.8xlarge','g4dn.metal','m6id.32xlarge','r6g.4xlarge','r5a.16xlarge','m5ad.4xlarge','r3.8xlarge','m5ad.2xlarge','m5.24xlarge','a1.medium','t3a.small','c6id.16xlarge','c7g.16xlarge','c5n.4xlarge','m6g.metal','c6a.32xlarge','c5ad.xlarge','m5dn.xlarge','c5.4xlarge','m6a.metal','g4dn.12xlarge','m5ad.large','t4g.2xlarge','d3.8xlarge','c6g.12xlarge','r6i.24xlarge','m5d.large','t3.medium','i4i.4xlarge','c6i.24xlarge','g4ad.8xlarge','m6g.xlarge','r5b.metal','c7g.4xlarge','c5d.2xlarge','m5dn.8xlarge','h1.8xlarge','a1.large','t3.large','c6id.24xlarge','t3.2xlarge','x2iedn.metal','g5.24xlarge','t2.large','m5.12xlarge','x2iezn.8xlarge','c5d.12xlarge','z1d.xlarge','t3.xlarge','d2.2xlarge','u-3tb1.56xlarge','im4gn.xlarge','m5d.metal','c5a.xlarge','x2idn.24xlarge','r5.large','r5ad.8xlarge','r6i.4xlarge','c7g.xlarge','m6i.24xlarge','p2.8xlarge','c1.xlarge','g4ad.2xlarge','r6gd.metal','r6a.12xlarge','g4ad.4xlarge','r5a.12xlarge','c5.xlarge','c6gn.xlarge','t4g.medium','i4i.metal','m4.xlarge','r6gd.large','c5a.4xlarge','r6id.12xlarge','t3a.2xlarge','r6a.32xlarge','r6id.4xlarge','c6g.xlarge','r5n.4xlarge','m5.metal','m5d.16xlarge','c6gn.4xlarge','m2.2xlarge','inf1.xlarge','r5ad.12xlarge','m6i.8xlarge','m5.xlarge','r5a.large','m5n.8xlarge','c5a.12xlarge','g5g.2xlarge','r6a.8xlarge','g3.8xlarge','c4.large','x2gd.4xlarge','c5n.large','m5n.large','m6gd.16xlarge','t4g.small','c6gn.medium','c6i.16xlarge','c1.medium','g3.4xlarge','m4.large','m5.4xlarge','t3a.medium','r5ad.large','r5b.large','u-12tb1.112xlarge','c6a.12xlarge','inf1.6xlarge','r5b.24xlarge','m6a.4xlarge','t2.micro','m3.xlarge','vt1.3xlarge','vt1.6xlarge','c5d.4xlarge','r6id.32xlarge','c3.xlarge','d3.xlarge','m1.xlarge','m5n.4xlarge','r5.16xlarge','mac2.metal','c5d.24xlarge','r6g.12xlarge','r5b.xlarge','g5.12xlarge','x2gd.16xlarge','a1.xlarge','r5d.2xlarge','c6a.metal','r5.4xlarge','r5dn.metal','g5g.16xlarge','p3.2xlarge','r5d.4xlarge','r3.2xlarge','t4g.large','m5n.metal','m6i.metal','m6i.32xlarge','m5zn.3xlarge','r5d.xlarge','r5n.2xlarge','i2.xlarge','x2gd.medium','mac1.metal','g2.2xlarge','m4.16xlarge','d3en.4xlarge','c5.12xlarge','c6a.8xlarge','r5n.16xlarge','c5a.24xlarge','r5b.2xlarge','c6gd.16xlarge','m6i.4xlarge','m5d.24xlarge','inf1.2xlarge','g5g.metal','u-6tb1.56xlarge','c6gd.xlarge','r5.metal','c5.24xlarge','p4d.24xlarge','r6a.48xlarge','c5a.2xlarge','m5zn.12xlarge','r5n.24xlarge','m6g.large','r5d.16xlarge','x2idn.metal','h1.2xlarge','r5dn.xlarge','i4i.2xlarge','r5d.large','d3.2xlarge','r5b.4xlarge','x2gd.xlarge','m6g.4xlarge','r6gd.4xlarge','d3en.6xlarge','r3.4xlarge','h1.4xlarge','c6gn.12xlarge','c6g.large','r5dn.large','m6i.16xlarge','x2gd.2xlarge','m5n.12xlarge','c5n.9xlarge','m6g.8xlarge','r6id.24xlarge','r5a.4xlarge','m5dn.4xlarge','r6gd.16xlarge','i3.8xlarge','c5a.16xlarge','h1.16xlarge','c5n.18xlarge','r3.xlarge','t2.medium','r4.xlarge','m6id.metal','i3.xlarge','m6id.16xlarge','c4.2xlarge','i2.4xlarge','is4gen.large','r6i.16xlarge','x2iedn.16xlarge','t4g.micro','c7g.medium','x2idn.32xlarge','x2gd.12xlarge','c5d.xlarge','m5zn.xlarge','r5ad.16xlarge','g5.16xlarge','a1.2xlarge','m5.16xlarge','t3.nano','r5dn.16xlarge','r6gd.8xlarge','z1d.metal','c5ad.2xlarge','c3.2xlarge','c5.metal','x2iezn.2xlarge','r6id.metal','r4.2xlarge','m5ad.xlarge','g4dn.xlarge','r5ad.xlarge','m5ad.24xlarge','i2.8xlarge','u-6tb1.112xlarge','g5.xlarge','x2gd.large','r5n.xlarge','g5g.8xlarge','t3.micro','r6i.metal','m5dn.metal','x1e.16xlarge','t3a.large','c4.xlarge','c7g.2xlarge','c5.9xlarge','m6g.16xlarge','c5n.metal','m5zn.metal','c6id.metal','z1d.6xlarge','c5d.18xlarge','r6id.large','c6id.32xlarge','r6i.12xlarge','c5d.metal','z1d.12xlarge','r5d.8xlarge','m6g.12xlarge','m5a.8xlarge','m5dn.16xlarge','m6gd.8xlarge','a1.4xlarge','m3.2xlarge','r5.xlarge','r6a.24xlarge','r5b.16xlarge','r5d.24xlarge','i3.large','x2iezn.12xlarge','m5dn.24xlarge','r5dn.12xlarge','m6a.16xlarge','c3.4xlarge','c6a.xlarge','m4.2xlarge','is4gen.2xlarge','r6a.4xlarge','i4i.large','a1.metal','c5.2xlarge','r5.2xlarge','x2idn.16xlarge','m5d.2xlarge','r5.8xlarge','c6a.4xlarge','m5d.12xlarge','t4g.xlarge','r5ad.2xlarge','x2iedn.4xlarge','c5.large','r6g.2xlarge','r4.8xlarge','d2.4xlarge','p3.16xlarge','r5.12xlarge','x2iedn.32xlarge','r6g.xlarge','vt1.24xlarge','r6i.32xlarge','x2iezn.metal','c6i.metal','c5ad.large','r5n.8xlarge','i3.2xlarge','x1e.4xlarge','c6g.4xlarge','r6gd.12xlarge','m5n.24xlarge','m6gd.2xlarge','c6id.xlarge','i3en.large','x1e.xlarge','is4gen.medium','t2.xlarge','c6i.xlarge','m6id.4xlarge','c6gn.8xlarge','m5n.2xlarge','m5.2xlarge','r5b.8xlarge','r5a.24xlarge','x2iedn.24xlarge','im4gn.16xlarge','m6a.xlarge','c6a.2xlarge','im4gn.large','c6gd.4xlarge','c6g.medium','m5a.large','i3en.xlarge','m6i.xlarge','r5dn.4xlarge','c7g.large','r5ad.4xlarge','t3.small','r6id.8xlarge','d3.4xlarge','g5.8xlarge','i3.metal','m2.xlarge','r5d.12xlarge','x1.32xlarge','r5d.metal','g3s.xlarge','c6id.12xlarge','m6a.24xlarge','im4gn.4xlarge','c6gn.2xlarge','i4i.32xlarge','r5dn.2xlarge','m6id.24xlarge','m5ad.16xlarge','i4i.xlarge','c7g.12xlarge','p2.xlarge','dl1.24xlarge','c6g.8xlarge','c5ad.8xlarge','m3.medium','c6i.12xlarge','c6id.2xlarge','inf1.24xlarge','x2iezn.6xlarge','m6gd.large','p2.16xlarge','m3.large','g4dn.16xlarge','r6g.metal','r4.large','m5n.xlarge','m4.10xlarge','i3en.2xlarge','r5a.8xlarge','t2.small','m6id.8xlarge','c6gn.large','m5a.xlarge','c6gd.medium','r6a.16xlarge','p3.8xlarge','r6i.8xlarge','m5.large','m6a.32xlarge','g4ad.16xlarge','i3.16xlarge','t3a.nano','z1d.large','c5a.8xlarge','r6a.large','c6gd.2xlarge','g5g.xlarge','c6gd.8xlarge','m1.medium','z1d.3xlarge','f1.4xlarge','r4.4xlarge','m6g.2xlarge','c5.18xlarge','d3en.xlarge','r5dn.24xlarge','c6g.metal','m6i.12xlarge','c5n.xlarge','i3en.6xlarge','g5.48xlarge','g4dn.2xlarge','f1.16xlarge','m5d.4xlarge','i4i.8xlarge','t2.nano','c6gd.large','is4gen.8xlarge','r5n.large','m6a.8xlarge','m6i.2xlarge','c6a.large','i3en.metal','g5g.4xlarge','m6a.12xlarge','g4dn.8xlarge','c6g.2xlarge','i3.4xlarge','m5a.12xlarge','r6i.2xlarge','g4dn.4xlarge','c5d.9xlarge','t3a.micro','d3en.12xlarge','c5a.large','t3a.xlarge','m6id.large','d2.xlarge','m1.large','g2.8xlarge','c6gd.12xlarge','p3dn.24xlarge','m6a.48xlarge','m6g.medium','r6i.large','m5a.24xlarge','m1.small','cc2.8xlarge','c6id.4xlarge','g4ad.xlarge','x2iedn.8xlarge','u-9tb1.112xlarge','r5n.metal']
                            }
                        '''
                ]
            ]
        ],

        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_CHECKBOX', 
            description: 'Specify the types of logs to enable.', 
            // filterLength: 4, 
            // filterable: true, 
            randomName: 'choice-parameter-56313011561700619004', 
            referencedParameters: 'ACTION', 
            name: 'LOG_TYPES_LIST',
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
                        ''' if (ACTION.equals("Create")){
                                return ['api','audit']
                            }
                        '''
                ]
            ]
        ], 
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HIDDEN_HTML', 
            description: 'Select the SUBNET in which the EC2 Instance needs to be provisioned', 
            name: 'LOG_TYPES', 
            randomName: 'choice-parameter-563221774452278619345232', 
            omitValueField: true,
            referencedParameters: 'ACTION,CREDENTIAL,LOG_TYPES_LIST,IAC_TOOL', 
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
                    ''' 
                         if (ACTION.equals("Create") && IAC_TOOL.equals("Terraform")){
                                    def input = "$LOG_TYPES_LIST"
                                    def items = input.split(',').collect { '"' + it + '"' }
                                    def formattedOutput = "[ ${items.join(', ')} ]"
                                    inputBox = "<input name='value' class='setting-input' type='text' value='$formattedOutput'>"
                                    return inputBox

                        } 
                        else{
                            def input = "$LOG_TYPES_LIST"
                            inputBox = "<input name='value' class='setting-input' type='text' value='$input'>"
                            return inputBox
                        }
                                        
                    '''
                ]
            ]
        ],                                                                                       
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: '''NAME of the node group''', 
            name: 'NODE_GROUP_NAME', 
            omitValueField: true,
            randomName: 'choice-parameter-56313314451178619003', 
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
            description: 'Type of capacity associated with the EKS Node Group', 
            // filterLength: 4, 
            // filterable: true, 
            randomName: 'choice-parameter-56313111561789619004', 
            referencedParameters: 'ACTION', 
            name: 'NODE_CAPACITY_TYPE',
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
                                return ['ON_DEMAND','SPOT']
                            }
                        '''
                ]
            ]
        ],

        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: '''Minimum no of nodes can be in the cluster''', 
            name: 'NODE_GROUP_MIN_SIZE', 
            omitValueField: true,
            randomName: 'choice-parameter-56313314451178619005', 
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
                        ''' if (ACTION.equals("Create") || ACTION.equals("Modify")){
                            inputBox = "<input name='value' class='setting-input'  type='text'>"
                            return inputBox
                            }
                        '''
                ]
            ]
        ],
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: '''Max no of nodes can be in the cluster''', 
            name: 'NODE_GROUP_MAX_SIZE', 
            omitValueField: true,
            randomName: 'choice-parameter-56313314451178619006', 
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
                        ''' if (ACTION.equals("Create") || ACTION.equals("Modify")){
                            inputBox = "<input name='value' class='setting-input'  type='text'>"
                            return inputBox
                            }
                        '''
                ]
            ]
        ],
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: '''Minimum no of nodes can be in the cluster''', 
            name: 'NODE_GROUP_DESIRED_SIZE', 
            omitValueField: true,
            randomName: 'choice-parameter-56313314451178619007', 
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
                        ''' if (ACTION.equals("Create") || ACTION.equals("Modify")){
                            inputBox = "<input name='value' class='setting-input'  type='text'>"
                            return inputBox
                            }
                        '''
                ]
            ]
        ],

        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: 'Enter the default volume size, whose type will be GP3. For Linux flavour the size should be >= 8GB & for windows flavour the size should be >= 30GB ', 
            name: 'VOLUME_SIZE', 
            omitValueField: true,
            randomName: 'choice-parameter-5631314451178619009', 
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
                        ''' if (ACTION.equals("Create") || ACTION.equals("Modify")){
                            inputBox = "<input name='value' class='setting-input'  type='number'>"
                            return inputBox
                            }
                        '''
                ]
            ]
        ],
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: 'Type of capacity associated with the EKS Node Group', 
            // filterLength: 4, 
            // filterable: true, 
            randomName: 'choice-parameter-563131115617896190011', 
            referencedParameters: 'ACTION', 
            name: 'VOLUME_TYPE',
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
                                return ['gp3','gp2R']
                            }
                        '''
                ]
            ]
        ],
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: '''The provisioned IOPS for a storage volume''', 
            name: 'IOPS', 
            omitValueField: true,
            randomName: 'choice-parameter-56313310051178619005', 
            referencedParameters: 'ACTION, IAC_TOOL',
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
                        ''' if (ACTION.equals("Create") && IAC_TOOL.equals("Terraform")){
                            inputBox = "<input name='value' class='setting-input'  type='text'>"
                            return inputBox
                            }
                        '''
                ]
            ]
        ],    
    
    ])
])

pipeline{
    agent any
    options { // Specifies pipeline-specific options/behaviors
        ansiColor('xterm') // Enables ANSI color formatting in the pipeline console output using the "xterm" color scheme
    }
    environment { // Defines environment variables used by the pipeline
        resource = "eks" // Sets the value of the "resource" variable to "ecr"
        resource_name = "${env.CLUSTER_NAME}" // Sets the value of the "resource_name" variable to the value of the "ECR_REPO_NAME" environment variable
        aws_region = "${env.REGION}".replace(',',''); // Sets the value of the "aws_region" variable to the value of the "REGION" environment variable with commas removed (using the "replace" function)
        PROJECT_NAME = sh (returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 1").trim() // Runs a shell command to extract the project name from the "CREDENTIAL" parameter and sets it as the value of the "PROJECT_NAME" variable
        ACCOUNT_ID = sh (returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 2").trim() // Runs a shell command to extract the account ID from the "CREDENTIAL" parameter and sets it as the value of the "ACCOUNT_ID" variable

    }

    stages{
        stage("Workspace cleanup"){
            steps{       
                script{ // Allows for execution of arbitrary Groovy code within the pipeline
                    if (params.CLUSTER_NAME == '') { // Checks if the "CLUSTER_NAME" parameter is empty
                        currentBuild.displayName = "Loading parameters" // Updates the Jenkins build display name to indicate that pipeline parameter loading is in progress
                        currentBuild.result = 'ABORTED' // Marks the Jenkins build as aborted
                        error('The parameter value is null') // Throws an error indicating that a required parameter value is missing
                    }     
                                
                    currentBuild.displayName = "${ACTION} the eks ${params.CLUSTER_NAME} - ${IAC_TOOL}" // Updates the Jenkins build display name to indicate the current action ("ACTION") and ecr repo name ("CLUSTER_NAME")
                    currentBuild.description = "${ACTION} the eks ${params.CLUSTER_NAME} - ${IAC_TOOL}" // Updates the Jenkins build description to indicate the current action ("ACTION") and ecr repo name ("CLUSTER_NAME")

                } 
                wrkspace_cleanup () //cleanup the worksapce
            }
        }
        stage("SCM Checkout"){ 
            steps {
                script{ // Allows for execution of arbitrary Groovy code within the pipeline
                    if (env.IAC_TOOL == "Terraform") {
                        tf_scm_checkout ()  // Calls a function named "tf_scm_checkout()" to perform an SCM checkout (defined in the jenkins shared library)
                        }
                        else {
                        cft_scm_checkout ()  // Calls a function named "tf_scm_checkout()" to perform an SCM checkout (defined in the jenkins shared library)
                        
                        }
            }
        }
        }
        stage("Provisioning resource"){ 
            steps {
                script{ // Allows for execution of arbitrary Groovy code within the pipeline
                    if (env.IAC_TOOL == "Terraform") {
                        withAWS(region: "${env.REGION}") {
                        tf_resource_provision () // Calls a function named "tf_resource_provision()" to provision a resource (defined in the jenkins shared library)
                        git_commit() // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
                        }
                    }
                    else {
                        withAWS(region: "${env.REGION}") {
                        cft_resource_provision ('eks.j2') // Calls a function named "cft_resource_provision()" here we need to provide the "jinja2" file name to provision a resource (defined in the jenkins shared library)                     
                        cft_git_commit () // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
                        }
                    }
                }
            }
        }
    }
}
                                
