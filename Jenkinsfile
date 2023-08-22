properties([
    parameters([
        // This block of code contains the configuration for the parameters of the Jenkins job.
        // There are several types of parameters included, such as ChoiceParameter, DynamicReferenceParameter, and string.

        // ChoiceParameter is used to allow the user to select one of several predefined options.
        // In this case, the user can choose between Create, Modify, or Delete as the ACTION to perform.
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
        string(
            defaultValue: 'prod', 
            name: 'ENVIRONMENT', 
            trim: true,
            description: 'The name of the environment in which resources needs to be provisioned.'
        ),
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
            name: 'SECURITY_GROUP_NAME', 
            trim: true,
            description: 'The Security Group name which needs to be provisioned.'
        ),
        //This is a Groovy code block defining a DynamicReferenceParameter in Jenkins for a security group description. 
        //The script then uses the AWS CLI to retrieve the security group's description and displays it as a pre-populated input box if the action is set to "modify." If the action is set to "create," it simply displays an empty input box.
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: 'The Description for the Security Group.', 
            name: 'DESCRIPTION',
            omitValueField: true, 
            randomName: 'choice-parameter-56313113156178619', 
            referencedParameters: 'ACTION,CREDENTIAL,SECURITY_GROUP_NAME,REGION', 
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
                        ''' import jenkins.model.*
                            import org.apache.http.client.methods.*
                            import org.apache.http.impl.client.*
                            import groovy.json.JsonSlurper;
                            if (ACTION.equals("Create")){
                            inputBox = "<input name='value' class='setting-input' type='text'>"
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
            
                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-security-groups --filters Name=group-name,Values=$SECURITY_GROUP_NAME --query=SecurityGroups[].Description  --output text --region $REGION"
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
                                    // return output.tokenize() 
                                    inputBox = "<input name='value' class='setting-input' type='text' value = '$output' >"
                                    return inputBox
                                } 
                            }
                            }
                        '''
                ]
            ]
        ],        
        //  This is a Groovy script defining a CascadeChoiceParameter in Jenkins for selecting a VPC for provisioning a security group. Here are the comments for each section of the script:                                                
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: 'Select the VPC in which the SecurityGroup needs to be provisioned. The default VPC are showed up as None.', 
            name: 'VPC_NAME', 
            randomName: 'choice-parameter-5631311156178619', 
            //Specifies other parameters that this parameter depends on.
            referencedParameters: 'ACTION,CREDENTIAL,REGION,SECURITY_GROUP_NAME', 
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
                    //The script checks if the selected action is "Create" or "Modify".
                    //If it's either of them, the script looks up the Jenkins credentials associated with the specified credential ID and extracts the AWS access key ID and secret access key from JSON data.
                    //If the action is "Modify," the script constructs an AWS CLI command to retrieve the VPC ID for the specified security group name and executes it.
                    //If the action is "Create," the script constructs an AWS CLI command to retrieve all VPC names and executes it.
                    //The script waits for the command to complete and retrieves the output as a list of VPC names.
                    //If there are any errors, the script prints them and returns the exit code. Otherwise, it returns the list of VPC names.
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
                                        command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-security-groups --filters Name=group-name,Values=$SECURITY_GROUP_NAME --query=SecurityGroups[].VpcId  --output text --region $REGION"
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
            description: 'Select the VPC in which the sg needs to be provisioned', 
            name: 'VPC_ID', 
            omitValueField: true,
            randomName: 'choice-parameter-56313116178619', 
            //This indicates that the parameter depends on the values of other parameters: ACTION, CREDENTIAL, VPC_NAME, REGION, and SECURITY_GROUP_NAME.
            referencedParameters: 'ACTION,CREDENTIAL,VPC_NAME,REGION,SECURITY_GROUP_NAME', 
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
                        // If ACTION is "Create" the below block will be executed
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
                            // Loop through the list of credentials to find the matching one
                            for (creds in jenkinsCredentials) {
                                if(creds.id == "$CREDENTIAL"){
                                    key = creds.secret.toString(creds.secret);
                                    def jsonSlurper = new JsonSlurper()
                                    cfg = jsonSlurper.parseText(key)
                                    // Get the AWS access key and secret access key from the credentials
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
                                    // If there's an error, print it and return the exit code
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
                        // If ACTION is "Modify" the below block will be executed
                        // It will populated the VPC_ID by getting the VPC attached to security group name
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
                                    
                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-security-groups --filters Name=group-name,Values=$SECURITY_GROUP_NAME --query=SecurityGroups[].VpcId  --output text --region $REGION"
                                                                       
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
        //Reference the description , where we need to input security group rules.
       [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            omitValueField: true,
            description: 'Enter the rules to be added to the Securit Group. Follow the format for Terraform [[port,"protocol",["cidr_block"],["security_grp_id"]]], where from_port 0  and protocol -1 is all traffic. Example: For ALL traffic, [[0,"-1",["0.0.0.0/0"],[]]], For Port 22 for all traffic & Port 80 for a particular security group use - [[22,"tcp",["0.0.0.0/0"],[]],[80,"tcp",[],["sg-0a7421047c555d574"]]]. Incase of Cloudformation ,use like port,protocol,cidr_block,security_grp_id. for example, 22,tcp,0.0.0.0/0,sg-12345', 
            name: 'INGRESS_RULES', 
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
                        ''' if (ACTION.equals("Create") || ACTION.equals("Modify")){
                            return """<textarea name=\"value\" rows=\"5\" class=\"setting-input\"></textarea>"""
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
        resource = "security-group" // Sets the value of the "resource" variable to "security-group"
        resource_name = "${env.SECURITY_GROUP_NAME}" // Sets the value of the "resource_name" variable to the value of the "SECURITY_GROUP_NAME" environment variable
        aws_region = "${env.REGION}".replace(',',''); // Sets the value of the "aws_region" variable to the value of the "REGION" environment variable with commas removed (using the "replace" function)
        PROJECT_NAME = sh (returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 1").trim() // Runs a shell command to extract the project name from the "CREDENTIAL" parameter and sets it as the value of the "PROJECT_NAME" variable
        ACCOUNT_ID = sh (returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 2").trim() // Runs a shell command to extract the account ID from the "CREDENTIAL" parameter and sets it as the value of the "ACCOUNT_ID" variable
    }

    stages{   
        stage("Workspace cleanup"){ 
            steps{ 
                script{ // Allows for execution of arbitrary Groovy code within the pipeline
                    if (params.SECURITY_GROUP_NAME== '') { // Checks if the "SECURITY_GROUP_NAME" parameter is empty
                        currentBuild.displayName = "Loading parameters" // Updates the Jenkins build display name to indicate that pipeline parameter loading is in progress
                        currentBuild.result = 'ABORTED' // Marks the Jenkins build as aborted
                        error('The parameter value is null') // Throws an error indicating that a required parameter value is missing
                    }     
                    // def item = Jenkins.instance.getItemByFullName("${env.PROJECT_NAME}_Resource_Multibranch/1.Terraform-VPC-Pipeline")
                    // item.setDescription(" The pipeline to create, modify and destroy the security-group resources. The resources created through this pipeline are security-group")                    
                    currentBuild.displayName = "${ACTION} the security_group ${SECURITY_GROUP_NAME} - ${IAC_TOOL}" // Updates the Jenkins build display name to indicate the current action ("ACTION") and SecurityGroup name ("SECURITY_GROUP_NAME")
                    currentBuild.description = "${ACTION} the security_group ${SECURITY_GROUP_NAME} - ${IAC_TOOL}"  // Updates the Jenkins build description to indicate the current action ("ACTION") and SecurityGroup name ("SECURITY_GROUP_NAME")
                }              
                wrkspace_cleanup () // Calls a function named "wrkspace_cleanup()" to clean up the workspace (defined in the jenkins shared library)
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
                        withAWS(region:"$REGION"){
                        tf_resource_provision () // Calls a function named "tf_resource_provision()" to provision a resource (defined in the jenkins shared library)
                        git_commit() // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
                        }
                    }
                    else {
                        withAWS(region:"$REGION"){
                        cft_resource_provision ('security-group.j2') // Calls a function named "cft_resource_provision()" here we need to provide the "jinja2" file name to provision a resource (defined in the jenkins shared library)                     
                        cft_git_commit () // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
                        }
                    }
                }
           }
        }
    }
}
