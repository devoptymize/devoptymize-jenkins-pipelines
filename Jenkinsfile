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
            description: 'AWS region in which the loadbalancer needs to be provisioned', 
            filterLength: 4, 
            filterable: false, 
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


        // string(
        //     defaultValue: '', 
        //     name: 'LOADBALANCER_NAME', 
        //     trim: true,
        //     description: 'Enter name of the load balancer'
        // ),

        [$class: 'DynamicReferenceParameter', 
                choiceType: 'ET_FORMATTED_HTML', 
                description: 'Enter the name of the load balancer', 
                name: 'LOADBALANCER_NAME', 
                omitValueField: true, 
                randomName: 'choice-parameter-5531310676174239', 
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
                            """ if (ACTION.equals("Create") || ACTION.equals("Modify")){
                                inputBox = "<input name='value'  class='setting-input' type='text' >"
                                return inputBox
                                }
                                
                        """
                ]
            ]
        ],



        [$class: 'CascadeChoiceParameter', 
                choiceType: 'PT_SINGLE_SELECT', 
                description: '''Choose the type of Load balancer''', 
                filterLength: 4, 
                filterable: true, 
                randomName: 'choice-parameter-5631561789618', 
                referencedParameters: 'ACTION', 
                name: 'LOADBALANCER_TYPE', 
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
                                    return ['application','network']
                                }
                            '''
                    ]
                ]
            ],


         [$class: 'CascadeChoiceParameter', 
                choiceType: 'PT_SINGLE_SELECT', 
                description: '''Choose the type of scheme you want for load balancer''', 
                filterLength: 4, 
                filterable: true, 
                randomName: 'choice-parameter-5631561789614', 
                referencedParameters: 'ACTION', 
                name: 'SCHEME', 
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
                                    return ['internet-facing','internal']
                                }
                            '''
                    ]
                ]
            ],

         [$class: 'CascadeChoiceParameter', 
                choiceType: 'PT_SINGLE_SELECT', 
                description: '''Select the type of IP addresses that your subnets use''', 
                filterLength: 4, 
                filterable: true, 
                randomName: 'choice-parameter-56315617896176', 
                referencedParameters: 'ACTION', 
                name: 'IP_ADDRESS_TYPE', 
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
                                    return ['ipv4','dualstack']
                                }
                            '''
                    ]
                ]
            ],


       

        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: 'Select the VPC in which LB needs to be deployed', 
            name: 'VPC_NAME', 
            randomName: 'choice-parameter-5631311156178619', 
            referencedParameters: 'ACTION,CREDENTIAL,REGION,LOADBALANCER_NAME,LOADBALANCER_TYPE,SCHEME,IP_ADDRESS_TYPE',
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
                                    if(ACTION.equals("Modify")){
                                        alb_command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws elbv2 describe-load-balancers --names $LOADBALANCER_NAME --query LoadBalancers[0].VpcId --output text --region $REGION"
                                        def proc = alb_command.execute()
                                        proc.waitFor()              
                                        def output = proc.in.text
                                        def exitcode= proc.exitValue()
                                        def error = proc.err.text
                                        out = output.tokenize()
                                        command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-vpcs --filters Name=vpc-id,Values=$out --query Vpcs[0].Tags[?Key==`Name`].Value --output text --region $REGION"
                                    }
                                    else{
                                        command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-vpcs  --query=Vpcs[*].{Name:Tags[?Key==`Name`].Value|[0]}  --output text --region $REGION"
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
            description: 'Select the VPC in which the LB needs to be provisioned', 
            name: 'VPC_ID', 
            omitValueField: true,
            randomName: 'choice-parameter-56313116178619', 
            referencedParameters: 'ACTION,CREDENTIAL,LOADBALANCER_TYPE,VPC_NAME,REGION,SCHEME,IP_ADDRESS_TYPE,LOADBALANCER_NAME', 
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
                                    else if(ACTION.equals("Modify")) {
                                        command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws elbv2 describe-load-balancers --names $LOADBALANCER_NAME --query LoadBalancers[0].VpcId --output text --region $REGION"
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
                                    
                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws elbv2 describe-load-balancers --names $LOADBALANCER_NAME --query LoadBalancers[0].VpcId --output text --region $REGION"
                                                                       
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
            description: 'Specify the subnets for LB', 
            name: 'SUBNET_NAME', 
            randomName: 'choice-parameter-5632214451178615', 
            referencedParameters: 'ACTION,CREDENTIAL,VPC_NAME,VPC_ID,REGION,LOADBALANCER_TYPE,SCHEME,IP_ADDRESS_TYPE', 
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
                                        command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-subnets --filter Name=vpc-id,Values=$VPC_ID --query=Subnets[*].Tags[?Key=='Name'].Value --output text --region $REGION"
                                   
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
            description: 'Select the SUBNET for the LB', 
            name: 'SUBNET_ID', 
            omitValueField: true,
            randomName: 'choice-parameter-563116178626', 
            referencedParameters: 'ACTION,CREDENTIAL,SUBNET_NAME,VPC_NAME,REGION,LOADBALANCER_TYPE,SCHEME,IP_ADDRESS_TYPE', 
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
            description: 'Security group which needs to be attached to LB. if u need to create a new security group please use SECURITYGROUP-PIPELINE.', 
            name: 'SECURITY_GROUP_NAME', 
            randomName: 'choice-parameter-5631314417619', 
            referencedParameters: 'ACTION,CREDENTIAL,VPC_NAME,REGION,VPC_ID,LOADBALANCER_TYPE,SCHEME', 
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
            description: 'Select the SUBNET for the LB', 
            name: 'SECURITY_GROUP_ID', 
            omitValueField: true,
            randomName: 'choice-parameter-563116178628', 
            referencedParameters: 'ACTION,CREDENTIAL,VPC_NAME,REGION,VPC_ID,LOADBALANCER_TYPE,SCHEME,SECURITY_GROUP_NAME', 
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
            description: 'select the security policy which you need to attach with your load balancer', 
            filterLength: 4, 
            filterable: false, 
            randomName: 'choice-parameter-5634411561789411', 
            referencedParameters: 'ACTION,LOADBALANCER_TYPE', 
            name: 'PROTOCOL', 
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
                        ''' if (ACTION.equals("Create") && LOADBALANCER_TYPE.equals("application") || ACTION.equals("Modify") && LOADBALANCER_TYPE.equals("application")){
                                return ['HTTP','HTTPS']
                            }
                            if (ACTION.equals("Create") && LOADBALANCER_TYPE.equals("network") || ACTION.equals("Modify") && LOADBALANCER_TYPE.equals("network")){
                                return ['TCP','TCP_UDP','TLS','UDP']
                            }    
                        '''
                ]
            ]
        ],

           [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: 'enter the port for listner you have choosen ["HTTP-80"],["HTTPS-443"],["TCP-80"],["TCP_UDP=53"],["TLS=443"],["UDP=53"]', 
            name: 'PORT', 
            omitValueField: true, 
            randomName: 'choice-parameter-5631311156178613', 
            referencedParameters: 'ACTION', 
            script: [ // Defines a script within the DynamicReferenceParameter object that will be executed to determine the dynamic choices for this parameter
                $class: 'GroovyScript', 
                fallbackScript: [ // Defines a fallback script to be executed if the main script fails (this script simply returns an empty string)
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        'return  ""'
                ],
                script: [ // Defines the main script to be executed (this script generates different HTML input fields based on the value of the "ACTION" parameter)
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        ''' if (ACTION.equals("Create")){
                            inputBox = "<input name='value' class='setting-input' type='text' >"
                            return inputBox
                            }
                            else if (ACTION.equals("Modify") ){
                            inputBox = "<input name='value' class='setting-input' type='text'>"
                            return inputBox
                            }      
                        '''
                ]
            ]
          ],




        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: 'Target group which needs to be attached to LOADBALANCER', 
            name: 'TARGET_GROUP_ARN', 
            randomName: 'choice-parameter-56313144176110', 
            referencedParameters: 'ACTION,CREDENTIAL,VPC_NAME,REGION,VPC_ID,PROTOCOL,LOADBALANCER_TYPE,SCHEME', 
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
                                    if (PROTOCOL.equals("HTTP") || PROTOCOL.equals("HTTPS")){ 
                                        command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws elbv2 describe-target-groups --query=TargetGroups[?(Protocol=='HTTP'||Protocol=='HTTPS')&&VpcId=='$VPC_ID'&&length(LoadBalancerArns)==`0`].TargetGroupArn --output text --region $REGION"
                                    }
                                    else if (PROTOCOL.equals("TLS")){ 
                                        command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws elbv2 describe-target-groups --query TargetGroups[?VpcId=='$VPC_ID'&&length(LoadBalancerArns)==`0`]|[?(Protocol=='TCP'||Protocol=='TLS')]|[].TargetGroupArn  --output text --region $REGION"   
                                    }
                                    else if (PROTOCOL.equals("TCP")){ 
                                        command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws elbv2 describe-target-groups --query TargetGroups[?VpcId=='$VPC_ID'&&length(LoadBalancerArns)==`0`]|[?Protocol=='TCP']|[].TargetGroupArn  --output text --region $REGION"   
                                    } 
                                    else if (PROTOCOL.equals("UDP")){
                                        command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws elbv2 describe-target-groups --query=TargetGroups[?VpcId=='$VPC_ID'&&length(LoadBalancerArns)==`0`]|[?(Protocol=='UDP'||Protocol=='TCP_UDP')]|[].TargetGroupArn --output text --region $REGION"
                                    }
                                    else if (PROTOCOL.equals("TCP_UDP")){
                                        command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws elbv2 describe-target-groups --query=TargetGroups[?VpcId=='$VPC_ID'&&length(LoadBalancerArns)==`0`]|[?Protocol=='TCP_UDP']|[].TargetGroupArn --output text --region $REGION"
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
                                    target_group_arn = output.tokenize() 

                                    return target_group_arn
                                } 
                            }
                        }
                    '''
                ]
            ]
        ],




        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: 'select the security policy which you need to attach with your load balancer', 
            filterLength: 4, 
            filterable: true, 
            randomName: 'choice-parameter-5634411561789611', 
            referencedParameters: 'ACTION,LOADBALANCER_TYPE,PROTOCOL', 
            name: 'SECURITY_POLICY', 
            script: [
                $class: 'GroovyScript', 
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        "return['null']"
                ], 
                script: [
                    classpath: [], 
                    sandbox: true,
                    script: 
                        ''' if (ACTION.equals("Create") && PROTOCOL.equals("HTTPS") && LOADBALANCER_TYPE.equals("application") || ACTION.equals("Modify") && PROTOCOL.equals("HTTPS") && LOADBALANCER_TYPE.equals("application")){
                                return ['ELBSecurityPolicy-2016-08','ELBSecurityPolicy-TLS-1-2-2017-01','ELBSecurityPolicy-TLS-1-1-2017-01','ELBSecurityPolicy-2015-05','ELBSecurityPolicy-2015-03','ELBSecurityPolicy-2015-02','ELBSecurityPolicy-2014-10','ELBSecurityPolicy-2014-01','ELBSecurityPolicy-2011-08','ELBSample-ELBDefaultNegotiationPolicy','ELBSample-OpenSSLDefaultNegotiationPolicy']
                            }
                            if (ACTION.equals("Create") && PROTOCOL.equals("TLS") && LOADBALANCER_TYPE.equals("network") || ACTION.equals("Modify") && PROTOCOL.equals("TLS") && LOADBALANCER_TYPE.equals("network")){
                                return ['ELBSecurityPolicy-TLS13-1-2-2021-06','ELBSecurityPolicy-TLS13-1-2-Res-2021-06','ELBSecurityPolicy-TLS13-1-2-Ext1-2021-06','ELBSecurityPolicy-TLS13-1-2-Ext2-2021-06','ELBSecurityPolicy-TLS13-1-1-2021-06','ELBSecurityPolicy-TLS13-1-0-2021-06','ELBSecurityPolicy-TLS13-1-3-2021-06','ELBSecurityPolicy-FS-1-2-Res-2020-10','ELBSecurityPolicy-FS-1-2-Res-2019-08','ELBSecurityPolicy-FS-1-2-2019-08','ELBSecurityPolicy-FS-1-1-2019-08','ELBSecurityPolicy-FS-2018-06','ELBSecurityPolicy-TLS-1-2-Ext-2018-06','ELBSecurityPolicy-TLS-1-2-2017-01','ELBSecurityPolicy-TLS-1-1-2017-01','ELBSecurityPolicy-2016-08','ELBSecurityPolicy-TLS-1-0-2015-04','ELBSecurityPolicy-2015-05']
                            }    
                        '''
                ]
            ]
        ],

         [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: 'select the options below for the SSL/TLS certificate', 
            filterLength: 4, 
            filterable: true, 
            randomName: 'choice-parameter-56313111561789614', 
            referencedParameters: 'ACTION,LOADBALANCER_TYPE,PROTOCOL,SECURITY_POLICY', 
            name: 'CERTIFICATE_TYPE', 
            script: [
                $class: 'GroovyScript', 
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        "return['null']"
                ], 
                script: [
                    classpath: [], 
                    sandbox: true,
                    script: 
                        ''' if (ACTION.equals("Create") && PROTOCOL.equals("HTTPS") && LOADBALANCER_TYPE.equals("application") || ACTION.equals("Modify") && PROTOCOL.equals("HTTPS") && LOADBALANCER_TYPE.equals("application")){
                            return ['from ACM']
                            }
                            if (ACTION.equals("Create") && PROTOCOL.equals("TLS") && LOADBALANCER_TYPE.equals("network") || ACTION.equals("Modify") && PROTOCOL.equals("TLS") && LOADBALANCER_TYPE.equals("network")){
                            return ['from ACM']
                            }
                        '''
                ]
            ]
        ],



         
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: 'Select the certificates for the load balancer', 
            filterLength: 4, 
            filterable: true,
            name: 'CERTIFICATE_NAME', 
            randomName: 'choice-parameter-5631314418916', 
            referencedParameters: 'ACTION,CREDENTIAL,VPC_NAME,REGION,VPC_ID,CERTIFICATE_TYPE,LOADBALANCER_TYPE,PROTOCOL', 
            script: [
                $class: 'GroovyScript', 
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        "return['null']"
                ], 
                script: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                    ''' 
                        import jenkins.model.*
                        import org.apache.http.client.methods.*
                        import org.apache.http.impl.client.*
                        import groovy.json.JsonSlurper;
                        if (ACTION.equals("Create") && PROTOCOL.equals("HTTPS") && LOADBALANCER_TYPE.equals("application") || ACTION.equals("Modify") && PROTOCOL.equals("HTTPS") && LOADBALANCER_TYPE.equals("application") || ACTION.equals("Create") && PROTOCOL.equals("TLS") && LOADBALANCER_TYPE.equals("network") || ACTION.equals("Modify") && PROTOCOL.equals("TLS") && LOADBALANCER_TYPE.equals("network")){
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

                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY  aws acm list-certificates --query=CertificateSummaryList[].DomainName --output text --region=$REGION"
                                    
                                
                                   
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
            description: 'Select the certificate for the load balancer', 
            name: 'CERTIFICATE_LIST', 
            omitValueField: true,
            randomName: 'choice-parameter-563116178635', 
            referencedParameters: 'ACTION,CREDENTIAL,VPC_NAME,REGION,VPC_ID,CERTIFICATE_TYPE,LOADBALANCER_TYPE,PROTOCOL,CERTIFICATE_NAME', 
            script: [
                $class: 'GroovyScript',  
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script:
                    ''' 
                      "return['null']"
                    '''
                ],
                script: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                    ''' 
                        import jenkins.model.*
                        import org.apache.http.client.methods.*
                        import org.apache.http.impl.client.*
                        import groovy.json.JsonSlurper;
                        if (ACTION.equals("Create") && PROTOCOL.equals("HTTPS") && LOADBALANCER_TYPE.equals("application") || ACTION.equals("Modify") && PROTOCOL.equals("HTTPS") && LOADBALANCER_TYPE.equals("application") || ACTION.equals("Create") && PROTOCOL.equals("TLS") && LOADBALANCER_TYPE.equals("network") || ACTION.equals("Modify") && PROTOCOL.equals("TLS") && LOADBALANCER_TYPE.equals("network")){
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

                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY  aws acm list-certificates --query=CertificateSummaryList[?DomainName=='$CERTIFICATE_NAME'].CertificateArn --output text --region $REGION"
                                    
                                
                                    
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
            description: 'select the ALPN policy', 
            filterLength: 4, 
            filterable: true, 
            randomName: 'choice-parameter-56313131561789620', 
            referencedParameters: 'ACTION,LOADBALANCER_TYPE,PROTOCOL,CREDENTIAL', 
            name: 'ALPN_POLICY', 
            script: [
                $class: 'GroovyScript', 
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script:
                        "return['null']"
                ], 
                script: [
                    classpath: [], 
                    sandbox: true,
                    script: 
                    ''' if (ACTION.equals("Create") && LOADBALANCER_TYPE.equals("network") && PROTOCOL.equals("TLS") || ACTION.equals("Modify") && LOADBALANCER_TYPE.equals("network") && PROTOCOL.equals("TLS")){
                       return ['None','HTTP1Only','HTTP2Only','HTTP2Optional','HTTP2Preffered'] 
                    } '''
                ]
            ]
        ],

    ])
])

pipeline{
    agent any
    options {
        ansiColor('xterm')
    }
    environment {
        resource = "lb"
        resource_name = "${LOADBALANCER_NAME}"
        aws_region = "${env.REGION}".replace(',','');
        PROJECT_NAME = sh (returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 1").trim()
        ACCOUNT_ID = sh (returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 2").trim()

    }

    stages{
        stage("Workspace cleanup"){
            steps{    
                script{
                    if (params.LOADBALANCER_NAME == '') { 
                        currentBuild.displayName = "Loading parameters"
                        currentBuild.result = 'ABORTED'
                        error('The parameter value is null')
                    }     
                     def item = Jenkins.instance.getItemByFullName("${env.PROJECT_NAME}_Resource_Multibranch/08.Devoptymize-LoadBalancer")
                     item.setDescription(" The pipeline to create Load Balancer resource.")             
                    currentBuild.displayName = "${ACTION} the LB ${params.LOADBALANCER_NAME} - ${params.IAC_TOOL}"
                    currentBuild.description = "${ACTION} the LB ${params.LOADBALANCER_NAME} - ${params.IAC_TOOL}"  
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
                        print "${env.SECURITY_GROUP_NAME}"
                        }
            }
          }
        }
        stage("Provisioning resource"){ 
            steps {
                script{ // Allows for execution of arbitrary Groovy code within the pipeline
                    if (env.IAC_TOOL == "Terraform") {
                        withAWS(region:"${env.REGION}"){
                        tf_resource_provision () // Calls a function named "tf_resource_provision()" to provision a resource (defined in the jenkins shared library)
                        git_commit() // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
                        }
                    }
                    else {
                        withAWS(region:"${env.REGION}"){
                        cft_resource_provision ('elb.j2') // Calls a function named "cft_resource_provision()" here we need to provide the "jinja2" file name to provision a resource (defined in the jenkins shared library)                     
                        cft_git_commit () // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
                        }
                    }
                }
           }
        }
    }
}
