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
            // ChoiceParameter is used to allow the user to select an AWS region in which to provision resources.      
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
                name: 'ASG_NAME', 
                trim: true,
                description: 'The Auto scaling group name which needs to be provisioned.'
            ),
            [$class: 'CascadeChoiceParameter', 
                choiceType: 'PT_SINGLE_SELECT', 
                description: 'Select the existing launch template name', 
                name: 'LAUNCH_TEMPLATE', 
                randomName: 'choice-parameter-56313113156178719', 
                referencedParameters: 'ACTION,CREDENTIAL,ASG_NAME,REGION', 
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
                        //The script checks for the ACTION Parameter value, where if the value is CREATE OR MODIFY it Executes the AWS CLI command to list all launch template in a particular region
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

                                        def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-launch-templates --filters  --query=LaunchTemplates[].LaunchTemplateName  --output text --region $REGION"
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
                description: 'Select the VPC in which the EC2 Instance needs to be provisioned. The default VPC are showed up as None.', 
                name: 'VPC_NAME', 
                randomName: 'choice-parameter-5631311156178619', 
                referencedParameters: 'ACTION,CREDENTIAL,REGION,ASG_NAME', 
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
                        //The script checks for the ACTION Parameter value, where if the value is CREATE OR MODIFY it Executes the AWS CLI command to list all vpc in a particular region
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
                                        
                                        def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-vpcs  --query=Vpcs[*].{Name:Tags[?Key==`Name`].Value|[0]}  --output text --region $REGION"
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
                referencedParameters: 'ACTION,CREDENTIAL,VPC_NAME,REGION,ASG_NAME', 
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
                        //The script checks for the ACTION Parameter value, where if the value is CREATE OR MODIFY it Executes the AWS CLI command to get the VPC_ID for the above selected VPC Name.
                        ''' import jenkins.model.*
                            import org.apache.http.client.methods.*
                            import org.apache.http.impl.client.*
                            import groovy.json.JsonSlurper;
                            
                            if (ACTION.equals("Create" ) || ACTION.equals("Modify") ){
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
                        '''
                    ]
                ]
            ],
            [$class: 'CascadeChoiceParameter', 
                choiceType: 'PT_CHECKBOX', 
                description: 'Subnet in which the EC2 instance needs to be launched.', 
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
                        //The script checks for the ACTION Parameter value, where if the value is CREATE OR MODIFY it Executes the AWS CLI command to list all subnets in a particular vpc.
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
                                        /*if(ACTION.equals("Modify")){
                                            command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-instances --filters Name=tag:Name,Values=$INSTANCE_NAME --query=Reservations[].Instances[].SubnetId  --output text --region $REGION"
                                        }
                                        else{
                                            command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-subnets --filter Name=vpc-id,Values=$VPC_ID --query=Subnets[*].Tags[?Key=='Name'].Value --output text --region $REGION"
                                        }*/
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
                description: 'Select the SUBNET in which the EC2 Instance needs to be provisioned', 
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
                        //The script checks for the ACTION Parameter value, where if the value is CREATE OR MODIFY it Executes the AWS CLI command to get the SUBNET_ID for the above selected Subnet Name.
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
                                        /*if(ACTION.equals("Modify")){
                                            command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-instances --filters Name=tag:Name,Values=$INSTANCE_NAME --query=Reservations[].Instances[].SubnetId  --output text --region $REGION"
                                        }
                                        else{
                                            command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-subnets --filter Name=tag:Name,Values=$SUBNET_NAME --query=Subnets[].SubnetId --region=$REGION --output text"
                                        }*/
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
                description: '''Choose a load balancer to distribute incoming traffic for your application across instances to make it more reliable and easily scalable.''', 
                filterLength: 4, 
                filterable: false, 
                randomName: 'choice-parameter-5631311156129619', 
                referencedParameters: 'ACTION,CREDENTIAL,REGION', 
                name: 'LOAD_BALANCING_ACTION', 
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
                                    return ['No_Load_Balancer','Attach_ALB_NLB','Attach_Classic_Load_Balancer']
                                }
                            '''
                    ]
                ]
            ],
            [$class: 'CascadeChoiceParameter', 
                choiceType: 'PT_CHECKBOX', 
                description: '''Select the load balancers that you want to attach to your Auto Scaling group. If u don't want to attach any load balancer select null.''', 
                name: 'LOAD_BALANCER', 
                randomName: 'choice-parameter-56322144511768619', 
                referencedParameters: 'ACTION,CREDENTIAL,REGION,LOAD_BALANCING_ACTION,VPC_ID', 
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
                        //The script checks for the ACTION Parameter value, where if the value is CREATE OR MODIFY 
                        // Then the script checks for the LOAD_BALANCING_ACTION parameter value
                        // it Executes the AWS CLI command to list available ALB or NLB or classic LB.
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
                                        if(LOAD_BALANCING_ACTION.equals("Attach_ALB_NLB")){
                                            command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws elbv2 describe-target-groups --query=TargetGroups[?VpcId=='$VPC_ID'].TargetGroupArn  --output text --region $REGION"
                                        }
                                        else if(LOAD_BALANCING_ACTION.equals("Attach_Classic_Load_Balancer")){
                                            command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws elb describe-load-balancers  --query=LoadBalancerDescriptions[].LoadBalancerName --output text --region $REGION"
                                        }
                                        
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
                                        return modifiedout
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
                description: 'The number of Amazon EC2 instances that should be running in the autoscaling group', 
                name: 'DESIRED_CAPACITY', 
                omitValueField: true,
                randomName: 'choice-parameter-56313113167178619', 
                referencedParameters: 'ACTION,CREDENTIAL,ASG_NAME,REGION', 
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
                        // defines a groovy script to generate the input box dynamically
                        ''' if (ACTION.equals("Create") || ACTION.equals("Modify")){
                                inputBox = "<input name='value' class='setting-input' type='text'>"
                                return inputBox
                            }
                        '''
                    ]
                ]
            ], 
            [$class: 'DynamicReferenceParameter', 
                choiceType: 'ET_FORMATTED_HTML', 
                description: 'The minimum size of the autoscaling group', 
                name: 'MIN_SIZE', 
                omitValueField: true,
                randomName: 'choice-parameter-56313113156190619', 
                referencedParameters: 'ACTION,CREDENTIAL,ASG_NAME,REGION', 
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
                        // defines a groovy script to generate the input box dynamically
                        '''
                            if (ACTION.equals("Create") || ACTION.equals("Modify")){
                                inputBox = "<input name='value' class='setting-input' type='text'>"
                                return inputBox
                            }
                        '''
                    ]
                ]
            ], 
            [$class: 'DynamicReferenceParameter', 
                choiceType: 'ET_FORMATTED_HTML', 
                description: 'The maximum size of the autoscaling group', 
                name: 'MAX_SIZE', 
                omitValueField: true,
                randomName: 'choice-parameter-56314013156178619', 
                referencedParameters: 'ACTION,CREDENTIAL,ASG_NAME,REGION', 
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
                        // defines a groovy script to generate the input box dynamically
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
                description: '''Send notifications to SNS topics whenever Amazon EC2 Auto Scaling launches or terminates the EC2 instances in your Auto Scaling group. Select null if you don't want to send any notifications. ''', 
                name: 'SNS_TOPIC', 
                randomName: 'choice-parameter-5634511768619', 
                referencedParameters: 'ACTION,CREDENTIAL,REGION,LOAD_BALANCING_ACTION', 
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
                        //The script checks for the ACTION Parameter value, where if the value is CREATE OR MODIFY it Executes the AWS CLI command to list SNS topics in a particular region.
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
                                    
                                        command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws sns list-topics --output=text --query=Topics[].TopicArn --region $REGION"
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
            ]
        
        ])
    ])

    pipeline {
        // Use any available agent to run the pipeline
        agent any
        // Configure pipeline options, in this case enabling ANSI color support
        options {
            ansiColor('xterm')
        }
        // Define environment variables for use throughout the pipeline
        environment {
            // Set the resource type to ASG
            resource = "asg"
            // Set the ASG name to the value of the ASG_NAME environment variable
            resource_name = "${env.ASG_NAME}"
            // Set the AWS region to the value of the REGION environment variable with commas removed
            aws_region = "${env.REGION}".replace(',','');
            // Parse the PROJECT_NAME and ACCOUNT_ID from the CREDENTIAL parameter using the shell command 'cut'
            PROJECT_NAME = sh (returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 1").trim()
            ACCOUNT_ID = sh (returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 2").trim()
        }

        stages{
            // Define a stage to clean up the workspace before running the pipeline
            stage("Workspace cleanup"){
                steps{       
                    script{
                        // Check if the ASG_NAME parameter is empty
                        if (params.ASG_NAME == '') { 
                            // If so, set the display name of the current build to indicate that the parameters are being loaded, mark the build as aborted, and throw an error
                            currentBuild.displayName = "Loading parameters"
                            currentBuild.result = 'ABORTED'
                            error('The parameter value is null')
                        }     
                        // Set the display name and description of the current build to indicate which ASG is being acted upon
                        currentBuild.displayName = "${ACTION} the ASG ${params.ASG_NAME} - ${params.IAC_TOOL}"
                        currentBuild.description = "${ACTION} the ASG ${params.ASG_NAME} - ${params.IAC_TOOL}"  
                    } 
                    // Call a function(defined in the jenkins shared library) to clean up the workspace
                    wrkspace_cleanup () 
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
                            withAWS(region:"$REGION"){
                            tf_resource_provision () // Calls a function named "tf_resource_provision()" to provision a resource (defined in the jenkins shared library)
                            git_commit() // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
                            }
                        }
                        else {
                            withAWS(region:"$REGION"){
                            cft_resource_provision ('asg.j2') // Calls a function named "cft_resource_provision()" here we need to provide the "jinja2" file name to provision a resource (defined in the jenkins shared library)                     
                            cft_git_commit () // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
                            }
                        }
                    }
            }
            }
        }
    }
