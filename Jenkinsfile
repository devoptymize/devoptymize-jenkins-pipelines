properties([
    parameters([
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
            name: 'RDS_NAME', 
            trim: true,
            description: '''Type a name for your DB. The name must be unique across all DBs owned by your AWS account in the current AWS Region. The DB identifier is case-insensitive, but is stored as all lowercase. Constraints: 1 to 60 alphanumeric characters or hyphens. First character must be a letter. Can't contain two consecutive hyphens. Can't end with a hyphen.'''
        ),
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: 'The database engine type.', 
            filterLength: 4, 
            filterable: true, 
            randomName: 'choice-parameter-56313111561789619', 
            referencedParameters: 'ACTION,RDS_NAME,REGION,CREDENTIAL', 
            name: 'ENGINE_TYPE', 
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
                        '''import jenkins.model.*
                        import org.apache.http.client.methods.*
                        import org.apache.http.impl.client.*
                        import groovy.json.JsonSlurper; 

                        if (ACTION.equals("Create")){
                               return ['aurora-mysql','custom-sqlserver-ee','custom-sqlserver-web','aurora-postgresql','mariadb','mysql','oracle-ee','oracle-ee-cdb','oracle-se2', 'oracle-se2-cdb','aurora','postgres','sqlserver-ee','sqlserver-ex','sqlserver-se','sqlserver-web']
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
                                    

                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws rds describe-db-instances --db-instance-identifier=$RDS_NAME --query=DBInstances[].Engine  --output text --region $REGION"
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
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: 'The engine version to use.', 
            name: 'ENGINE_VERSION', 
            randomName: 'choice-parameter-5631156178619', 
            referencedParameters: 'ACTION,CREDENTIAL,REGION,ENGINE_TYPE', 
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
                                    

                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws rds describe-db-engine-versions   --engine=$ENGINE_TYPE --query=DBEngineVersions[].EngineVersion  --output text --region $REGION"
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
            choiceType: 'ET_FORMATTED_HTML', 
            description: 'Type a login ID for the master user of your DB instance. ', 
            name: 'DB_MASTER_USERNAME', 
            omitValueField: true,
            randomName: 'choice-parameter-5631314451178619', 
            referencedParameters: 'ACTION,RDS_NAME,REGION,CREDENTIAL', 
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
                        '''import jenkins.model.*
                        import org.apache.http.client.methods.*
                        import org.apache.http.impl.client.*
                        import groovy.json.JsonSlurper; 
                        if (ACTION.equals("Create")){
                            inputBox = "<input name='value' class='setting-input'  type='text'>"
                            return inputBox
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
                                    

                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws rds describe-db-instances --db-instance-identifier=$RDS_NAME --query=DBInstances[].MasterUsername  --output text --region $REGION"
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
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: '''Specify a string that defines the password for the master user. Constraints: At least 8 printable ASCII characters. Can't contain any of the following: / (slash), '(single quote), "(double quote) and @ (at sign).''', 
            name: 'DB_MASTER_PASSWORD', 
            omitValueField: true,
            randomName: 'choice-parameter-56313314451178619', 
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
            description: '''Choose the DB instance type that allocates the computational, network, and memory capacity required by planned workload of this DB instance.''', 
            filterLength: 4, 
            filterable: true, 
            randomName: 'choice-parameter-5631561789619', 
            referencedParameters: 'ACTION', 
            name: 'DB_INSTANCE_CLASS', 
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
                                return ['db.m6g.16xlarge', 'db.m6g.12xlarge', 'db.m6g.8xlarge', 'db.m6g.4xlarge', 'db.m6g.2xlarge', 'db.m6g.xlarge', 'db.m6g.large', 'db.m6gd.16xlarge', 'db.m6gd.12xlarge', 'db.m6gd.8xlarge', 'db.m6gd.4xlarge', 'db.m6gd.2xlarge', 'db.m6gd.xlarge', 'db.m6gd.large', 'db.m6i.32xlarge', 'db.m6i.24xlarge', 'db.m6i.16xlarge', 'db.m6i.12xlarge', 'db.m6i.8xlarge', 'db.m6i.4xlarge', 'db.m6i.2xlarge', 'db.m6i.xlarge', 'db.m6i.large', 'db.m5d.24xlarge', 'db.m5d.16xlarge', 'db.m5d.12xlarge', 'db.m5d.8xlarge', 'db.m5d.4xlarge', 'db.m5d.2xlarge', 'db.m5d.xlarge', 'db.m5d.large', 'db.m5.24xlarge', 'db.m5.16xlarge', 'db.m5.12xlarge', 'db.m5.8xlarge', 'db.m5.4xlarge', 'db.m5.2xlarge', 'db.m5.xlarge', 'db.m5.large', 'db.m4.16xlarge', 'db.m4.10xlarge', 'db.m4.4xlarge', 'db.m4.2xlarge', 'db.m4.xlarge', 'db.m4.large', 'db.m3.2xlarge', 'db.m3.xlarge', 'db.m3.large', 'db.m3.medium', 'db.x2g.16xlarge', 'db.x2g.12xlarge', 'db.x2g.8xlarge', 'db.x2g.4xlarge', 'db.x2g.2xlarge', 'db.x2g.xlarge', 'db.x2g.large', 'db.z1d.12xlarge', 'db.z1d.6xlarge', 'db.z1d.3xlarge', 'db.z1d.2xlarge', 'db.z1d.xlarge', 'db.z1d.large', 'db.x1e.32xlarge', 'db.x1e.16xlarge', 'db.x1e.8xlarge', 'db.x1e.4xlarge', 'db.x1e.2xlarge', 'db.x1e.xlarge', 'db.x1.32xlarge', 'db.x1.16xlarge', 'db.r6g.16xlarge', 'db.r6g.12xlarge', 'db.r6g.8xlarge', 'db.r6g.4xlarge', 'db.r6g.2xlarge', 'db.r6g.xlarge', 'db.r6g.large', 'db.r6gd.16xlarge', 'db.r6gd.12xlarge', 'db.r6gd.8xlarge', 'db.r6gd.4xlarge', 'db.r6gd.2xlarge', 'db.r6gd.xlarge', 'db.r6gd.large', 'db.r6i.32xlarge', 'db.r6i.24xlarge', 'db.r6i.16xlarge', 'db.r6i.12xlarge', 'db.r6i.8xlarge', 'db.r6i.4xlarge', 'db.r6i.2xlarge', 'db.r6i.xlarge', 'db.r6i.large', 'db.r5d.24xlarge', 'db.r5d.16xlarge', 'db.r5d.12xlarge', 'db.r5d.8xlarge', 'db.r5d.4xlarge', 'db.r5d.2xlarge', 'db.r5d.xlarge', 'db.r5d.large', 'db.r5b.24xlarge', 'db.r5b.16xlarge', 'db.r5b.12xlarge', 'db.r5b.8xlarge', 'db.r5b.4xlarge', 'db.r5b.2xlarge', 'db.r5b.xlarge', 'db.r5b.large', 'db.r5.12xlarge.tpc2.mem2x', 'db.r5.8xlarge.tpc2.mem3x', 'db.r5.6xlarge.tpc2.mem4x', 'db.r5.4xlarge.tpc2.mem4x', 'db.r5.4xlarge.tpc2.mem3x', 'db.r5.4xlarge.tpc2.mem2x', 'db.r5.2xlarge.tpc2.mem8x', 'db.r5.2xlarge.tpc2.mem4x', 'db.r5.2xlarge.tpc1.mem2x', 'db.r5.xlarge.tpc2.mem4x', 'db.r5.xlarge.tpc2.mem2x', 'db.r5.large.tpc1.mem2x', 'db.r5.24xlarge', 'db.r5.16xlarge', 'db.r5.12xlarge', 'db.r5.8xlarge', 'db.r5.4xlarge', 'db.r5.2xlarge', 'db.r5.xlarge', 'db.r5.large', 'db.r4.16xlarge', 'db.r4.8xlarge', 'db.r4.4xlarge', 'db.r4.2xlarge', 'db.r4.xlarge', 'db.r4.large', 'db.r3.4xlarge', 'db.r3.2xlarge', 'db.r3.xlarge', 'db.r3.large', 'db.t4g.2xlarge', 'db.t4g.xlarge', 'db.t4g.large', 'db.t4g.medium', 'db.t4g.small', 'db.t4g.micro', 'db.t3.2xlarge', 'db.t3.xlarge', 'db.t3.large', 'db.t3.medium', 'db.t3.small', 'db.t3.micro', 'db.t2.2xlarge', 'db.t2.xlarge', 'db.t2.large', 'db.t2.medium', 'db.t2.small', 'db.t2.micro']
                            }
                        '''
                ]
            ]
        ],
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: '''Select Yes to Create Replica in Different Zone to have Amazon RDS maintain a synchronous standby replica in a different Availability Zone than the DB instance.''', 
            filterLength: 4, 
            filterable: false, 
            randomName: 'choice-parameter-5631313115561789619', 
            referencedParameters: 'ACTION,ENGINE_TYPE', 
            name: 'MULTI_AZ_DEPLOYMENT', 
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
                                return ['yes','no']
                            }
                        '''
                ]
            ]
        ],
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: '''Select the VPC in which the RDS Instance needs to be provisioned. The default VPC are showed up as None. ''', 
            name: 'VPC_NAME', 
            randomName: 'choice-parameter-5631311156178619', 
            referencedParameters: 'ACTION,CREDENTIAL,REGION,RDS_NAME', 
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
                                    if(ACTION.equals("Modify")){
                                        command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws rds describe-db-instances --db-instance-identifier=$RDS_NAME --query=DBInstances[].DBSubnetGroup.VpcId  --output text --region $REGION"
                                    }
                                    else{
                                        command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-vpcs  --query=Vpcs[*].{Name:Tags[?Key==`Name`].Value|[0]}  --output text --region $REGION"
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
            description: 'Select the VPC in which the RDS Instance needs to be provisioned', 
            name: 'VPC_ID',
            omitValueField: true, 
            randomName: 'choice-parameter-56313116178619', 
            referencedParameters: 'ACTION,CREDENTIAL,VPC_NAME,REGION,RDS_NAME', 
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
                                    
                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws rds describe-db-instances --db-instance-identifier $RDS_NAME --query=DBInstances[].DBSubnetGroup.VpcId  --output text --region $REGION"
                                                                       
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
            choiceType: 'PT_SINGLE_SELECT', 
            description: '''Choose the DB subnet group. The DB subnet group defines which subnets and IP ranges the DB instance can use in the VPC that you selected. Select null if you want to create a new subnet group, and select the subnets below.
''', 
            name: 'SUBNET_GROUP', 
            randomName: 'choice-parameter-56322144578619', 
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
                                    
                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws rds describe-db-subnet-groups --query=DBSubnetGroups[?VpcId=='$VPC_ID'].DBSubnetGroupName --output text --region $REGION"
                                    
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
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_CHECKBOX', 
            description: '''Subnet in which the RDS instance needs to be launched. If you will use the existing subnet groupt, then you don't want to select any subnets.''', 
            name: 'SUBNET_NAME', 
            randomName: 'choice-parameter-5632214451178619', 
            referencedParameters: 'ACTION,CREDENTIAL,VPC_NAME,VPC_ID,REGION,INSTANCE_NAME,SUBNET_GROUP', 
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
                        if (ACTION.equals("Create") && SUBNET_GROUP.equals("null") || ACTION.equals("Modify") && SUBNET_GROUP.equals("null")){
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
            description: 'Select the SUBNET in which the RDS Instance needs to be provisioned', 
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
            choiceType: 'PT_CHECKBOX', 
            description: '''Security group which needs to be attached to RDS. if u need to create a new security group please use SECURITYGROUP-PIPELINE.''', 
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
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HIDDEN_HTML', 
            description: 'Select the SecurityGroup in which the RDS Instance needs to be provisioned', 
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
            description: '''Select Yes if you want EC2 instances and other resources outside of the VPC hosting the database to connect to it. If you select No, Amazon RDS doesn't assign a public IP address to the database.''', 
            name: 'PUBLIC_ACCESS', 
            randomName: 'choice-parameter-56313143417619', 
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
                    ''' if (ACTION.equals("Create") || ACTION.equals("Modify")){
                            return ['Yes','No'] 
                            }                       
                    '''
                ]
            ]
        ],
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: 'Storage to be allocated to RDS ', 
            name: 'RDS_STORAGE', 
            omitValueField: true,
            randomName: 'choice-parameter-5631314451178619', 
            referencedParameters: 'ACTION,ENGINE_TYPE', 
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
                        ''' if(ACTION.equals("Create") || ACTION.equals("Modify")){
                                if(ENGINE_TYPE.equals("aurora")) {
                                    return ""
                                }
                                else{
                                    inputBox = "<input name='value' class='setting-input'  type='text'>"
                                    return inputBox
                                }
                            }
                        '''
                ]
            ]
        ], 
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: '''Provides dynamic scaling support for your database’s storage based on your applications needs.''', 
            name: 'STORAGE_AUTOSCALING', 
            randomName: 'choice-parameter-56313314417619', 
            referencedParameters: 'ACTION,CREDENTIAL,VPC_NAME,REGION,VPC_ID,ENGINE_TYPE', 
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
                    if(ACTION.equals("Create") || ACTION.equals("Modify")){
                        if(ENGINE_TYPE.equals("aurora")) {
                            return ""
                        }
                        else{
                            return ['Yes','No'] 
                        }
                    }
                    '''
                ]
            ]
        ],
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: '''Choose to encrypt the given instance. Master key IDs and aliases appear in the list after they have been created using the AWS Key Management Service console. If encryption is not required select null.''', 
            name: 'ENCRYPTION', 
            randomName: 'choice-parameter-563221578619', 
            referencedParameters: 'ACTION,CREDENTIAL,VPC_NAME,VPC_ID,REGION,ENGINE_TYPE', 
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
                                    
                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY  aws kms list-keys --query=Keys[].KeyArn --output text --region $REGION"
                                    
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
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: '''Select yes if u need to export logs to cloudwatch.''', 
            name: 'LOG_EXPORTS', 
            randomName: 'choice-parameter-5631331441887619', 
            referencedParameters: 'ACTION,CREDENTIAL,VPC_NAME,REGION,VPC_ID,ENGINE_TYPE', 
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
                    if (ACTION.equals("Create") || ACTION.equals("Modify")){
                        return ['Yes','No']
                    }
                    '''
                ]
            ]
        ],
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: '''Choose the DB option group that enables any optional functionality you want the DB instance to support. Select default to use a default option group provided by AWS. If you want to create a new option group please select null and add the terraform script below. ''', 
            name: 'OPTION_GROUP', 
            randomName: 'choice-parameter-5632291444578619', 
            referencedParameters: 'ACTION,CREDENTIAL,VPC_NAME,VPC_ID,REGION,ENGINE_TYPE', 
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
                                    
                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws rds describe-option-groups --query=OptionGroupsList[?EngineName=='$ENGINE_TYPE'].OptionGroupName --output text --region $REGION"
                                    
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
                                    lst.add(0,"default")
                                    return lst 
                                    //return output.tokenize() 
                                } 
                            }

                        }
                    '''
                ]
            ]
        ],
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            omitValueField: true,
            description: '''Add the option group template with the required options. The format of the input should be as below. Example script
            [
                {
                    option_name = "MARIADB_AUDIT_PLUGIN"

                    option_settings = [
                        {
                        name  = "SERVER_AUDIT_EVENTS"
                        value = "CONNECT"
                        },
                        {
                        name  = "SERVER_AUDIT_FILE_ROTATIONS"
                        value = "37"
                        },
                    ]
                    },
            ]''', 
            name: 'OPTION_GROUP_NEW', 
            randomName: 'choice-parameter-563131136178619', 
            referencedParameters: 'ACTION,OPTION_GROUP', 
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
                        ''' if (ACTION.equals("Create") && OPTION_GROUP.equals("null") || ACTION.equals("Modify") && OPTION_GROUP.equals("null")){
                            return """<textarea name=\"value\" rows=\"8\" class=\"setting-input\"></textarea>"""
                            }
                        '''
                ]
            ]
       ],
       [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: '''Choose the DB parameter group that defines the configuration settings you want applied to this DB instance. Select default to use a default parameter group provided by AWS. If you want to create a new parameter group please select null and add the input in the below parameter.''', 
            name: 'PARAMETER_GROUP', 
            randomName: 'choice-parameter-5632213444578619', 
            referencedParameters: 'ACTION,CREDENTIAL,VPC_NAME,VPC_ID,REGION,ENGINE_TYPE', 
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
                                    
                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws rds describe-db-parameter-groups --query=DBParameterGroups[?contains(DBParameterGroupFamily,'$ENGINE_TYPE')].DBParameterGroupName --output text --region $REGION"
                                    
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
                                    lst.add(0,"default")
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
            choiceType: 'ET_FORMATTED_HTML', 
            omitValueField: true,
            description: '''Enter the parameters with the value to create the new parameter group. The format should be [["<parameter_name>","<parameter_value>"],["<parameter_name>","<parameter_value>"]]. Example script for mysql, [["autocommit","0"],["binlog_format","MIXED"]]''', 
            name: 'PARAMETER_GROUP_NEW', 
            randomName: 'choice-parameter-56313156131546178619', 
            referencedParameters: 'ACTION,PARAMETER_GROUP', 
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
                        ''' if (ACTION.equals("Create") && PARAMETER_GROUP.equals("null") || ACTION.equals("Modify")  && PARAMETER_GROUP.equals("null")){
                            return """<textarea name=\"value\" rows=\"8\" class=\"setting-input\"></textarea>"""
                            }
                        '''
                ]
            ]
       ],
       [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HIDDEN_HTML', 
            description: 'The family of the DB parameter group', 
            name: 'DBPARAMETER_FAMILY',
            omitValueField: true, 
            randomName: 'choice-parameter-5631311643178619', 
            referencedParameters: 'ACTION,CREDENTIAL,ENGINE_TYPE,REGION,ENGINE_VERSION', 
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
                                   
                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws rds describe-db-engine-versions --engine $ENGINE_TYPE --engine-version $ENGINE_VERSION --query=DBEngineVersions[].DBParameterGroupFamily --region $REGION --output text"
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
        ]

    ])
])

pipeline{
    agent any
    options { // Specifies pipeline-specific options/behaviors
        ansiColor('xterm') // Enables ANSI color formatting in the pipeline console output using the "xterm" color scheme
    }
    environment { // Defines environment variables used by the pipeline
        resource = "rds" // Sets the value of the "resource" variable to "rds"
        resource_name = "${env.RDS_NAME}" // Sets the value of the "resource_name" variable to the value of the "RDS_NAME" environment variable
        aws_region = "${env.REGION}".replace(',',''); // Sets the value of the "aws_region" variable to the value of the "REGION" environment variable with commas removed (using the "replace" function)
        PROJECT_NAME = sh (returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 1").trim()  // Runs a shell command to extract the project name from the "CREDENTIAL" parameter and sets it as the value of the "PROJECT_NAME" variable
        ACCOUNT_ID = sh (returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 2").trim() // Runs a shell command to extract the account ID from the "CREDENTIAL" parameter and sets it as the value of the "ACCOUNT_ID" variable

    }

    stages{
        stage("Workspace cleanup"){
            steps{       
                script{ // Allows for execution of arbitrary Groovy code within the pipeline
                    if (params.RDS_NAME == '') { // Checks if the "RDS_NAME" parameter is empty
                        currentBuild.displayName = "Loading parameters" // Updates the Jenkins build display name to indicate that pipeline parameter loading is in progress
                        currentBuild.result = 'ABORTED' // Marks the Jenkins build as aborted
                        error('The parameter value is null') // Throws an error indicating that a required parameter value is missing
                    }     
                    currentBuild.displayName = "${ACTION} the RDS ${params.RDS_NAME} - ${IAC_TOOL}" // Updates the Jenkins build display name to indicate the current action ("ACTION") and the RDS ("RDS_NAME")
                    currentBuild.description = "${ACTION} the RDS ${params.RDS_NAME} - ${IAC_TOOL}" // Updates the Jenkins build description to indicate the current action ("ACTION") and the RDS ("RDS_NAME")
 
                } 
                wrkspace_cleanup () // Calls a function named "wrkspace_cleanup()" to clean up the workspace (defined in the Jenkins shared library)
            }
        }
        stage("SCM Checkout"){ 
            steps {
                script{ // Allows for execution of arbitrary Groovy code within the pipeline
                    if (env.IAC_TOOL == "Terraform") {
                        tf_scm_checkout ()   // Calls a function named "tf_scm_checkout()" to perform an SCM checkout (defined in the Jenkins shared library)
                        }
                        else {
                        cft_scm_checkout ()  // Calls a function named "cft_scm_checkout()" to perform an SCM checkout (defined in the Jenkins shared library)
                        print "${env.RDS_NAME}"
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
                        cft_resource_provision ('rds.j2') // Calls a function named "cft_resource_provision()" here we need to provide the "jinja2" file name to provision a resource (defined in the jenkins shared library)                     
                        cft_git_commit () // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
                        }
                    }
                }
           }
        }
    }
}
