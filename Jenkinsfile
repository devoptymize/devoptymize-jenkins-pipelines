properties([
    parameters([
        separator(name: "GENERAL-PARAMETERS", sectionHeader: "General-Parameters",
			separatorStyle: "border-width: 0",
			sectionHeaderStyle: """
				background-color: #E1D9D1;
				text-align: center;
				padding: 4px;
				color: #343434;
				font-size: 13px;
				font-weight: bold;
				text-transform: uppercase;
				font-family: 'Helvetica', sans-serif;
				letter-spacing: 1px;
				font-style: normal;
			"""
		),
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
            description: 'AWS region in which the VPC needs to be provisioned', 
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
            defaultValue: 'dev', 
            name: 'ENVIRONMENT', 
            trim: true,
            description: 'The name of the environment in which resources needs to be provisioned.'
        ),
        string(
            defaultValue: '', 
            name: 'STACK_NAME', 
            trim: true,
            description: '''This name will be added as a prefix to all the resource that will be created in AWS account. Additionally, stack names should not contain "_" '''
        ),
        separator(name: "NETWORK-PARAMETERS", sectionHeader: "Network Parameters",	
            separatorStyle: "border-width: 0",
			sectionHeaderStyle: """
				background-color: #E1D9D1;
				text-align: center;
				padding: 4px;
				color: #343434;
				font-size: 13px;
				font-weight: bold;
				text-transform: uppercase;
				font-family: 'Helvetica', sans-serif;
				letter-spacing: 1px;
				font-style: normal;
			"""
		),
        [$class: 'DynamicReferenceParameter', // Defines a DynamicReferenceParameter object (used to dynamically generate pipeline parameters at runtime) and specifies its configuration options
            choiceType: 'ET_FORMATTED_HTML', // Sets the choice type to ET_FORMATTED_HTML (allows HTML content to be embedded in parameter descriptions)
            description: 'CIDR range for your VPC,Example:10.0.0.0/16', // Sets the parameter description text
            //defaultValue: '10.0.0.0/16',
            name: 'VPC_CIDR_RANGE', // Sets the parameter name
            omitValueField: true, // Hides the "Default Value" field on the parameter configuration page (since it is not needed for this parameter)
            randomName: 'choice-parameter-5631311156178014', // Sets a random name for the parameter (not important for functionality)
            referencedParameters: 'ACTION', // Specifies that the system should use the value of the "ACTION" parameter as a reference in generating the dynamic choices for this parameter
            script: [ // Defines a script within the DynamicReferenceParameter object that will be executed to determine the dynamic choices for this parameter
                $class: 'GroovyScript', // Defines a GroovyScript object (used to execute Groovy code within the pipeline) and specifies its configuration options
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
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', // Sets the type of form element to be generated as "formatted HTML"
            //defaultValue: '["10.0.0.0/24","10.0.1.0/24","10.0.2.0/24"]',
            description: 'Public subnet CIDR ranges in a list format to create the public subnets.Here we can create number of subnet which is based on our requirement and the subnet will create based on the number of cidr range which we have provide ,Suggests providing a comma-separated list of CIDR ranges enclosed in square brackets, such as ["10.0.0.0/24","10.0.1.0/24","10.0.2.0/24"]', // Describes the purpose of the parameter and provides an example input format
            name: 'PUBLIC_SUBNETS', // Sets the name of the parameter as "PRIVATE_SUBNETS"
            omitValueField: true, // Hides the value field in the generated form element (since the value will be dynamically generated)
            randomName: 'choice-parameter-5931214451178629', // Generates a unique random name for the parameter
            referencedParameters: 'ACTION', // Specifies that the parameter is dependent on another parameter named "ACTION"
            script: [
                $class: 'GroovyScript', // Defines the scripting language used to generate the form element
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        'return ""' // If no script is provided, return an empty string
                ], 
                script: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        """
                            if (ACTION.equals("Create")){
                            inputBox = "<input name='value'  class='setting-input' type='text' >"
                            return inputBox
                            }
                            if (ACTION.equals("Modify") ){
                            inputBox = "<input name='value' multiple class='setting-input' type='text'>"
                            return inputBox
                            }
                        """// Defines the Groovy script that generates the HTML form element based on the selected "ACTION" parameter value
                ]
            ]
        ],
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', // Sets the type of form element to be generated as "formatted HTML"
            //defaultValue: '["10.0.3.0/24","10.0.4.0/24","10.0.5.0/24"]',
            description: 'Private subnet CIDR ranges in a list format to create the private subnets.Here we can create number of subnet which is based on our requirement and the subnet will create based on the number of cidr range which we have provide ,Suggests providing a comma-separated list of CIDR ranges enclosed in square brackets, such as ["10.0.3.0/24","10.0.4.0/24","10.0.5.0/24"]', // Describes the purpose of the parameter and provides an example input format
            name: 'PRIVATE_SUBNETS', // Sets the name of the parameter as "PUBLIC_SUBNETS"
            omitValueField: true, // Hides the value field in the generated form element (since the value will be dynamically generated)
            randomName: 'choice-parameter-5631314446171119', // Generates a unique random name for the parameter
            referencedParameters: 'ACTION', // Specifies that the parameter is dependent on another parameter named "ACTION"
            script: [
                $class: 'GroovyScript', // Defines the scripting language used to generate the form element
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        'return  ""' // If no script is provided, return an empty string
                ], 
                script: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        """ if (ACTION.equals("Create")){
                            inputBox = "<input name='value' class='setting-input' type='text'>"
                            return inputBox
                            }
                            if (ACTION.equals("Modify") ){
                            inputBox = "<input name='value' multiple class='setting-input' type='text'>"
                            return inputBox
                            }
                        """ // Defines the Groovy script that generates the HTML form element based on the selected "ACTION" parameter value
                ]
            ]
        ],
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', // Sets the type of form element to be generated as "formatted HTML"
            // defaultValue: '["10.0.6.0/24","10.0.7.0/24","10.0.8.0/24"]',
            description: 'Database subnet CIDR ranges in a list format to create the database subnets.Here we can create number of subnet which is based on our requirement and the subnet will create based on the number of cidr range which we have provide ,Suggests providing a comma-separated list of CIDR ranges enclosed in square brackets, such as ["10.0.6.0/24","10.0.7.0/24","10.0.8.0/24"]', // Describes the purpose of the parameter and provides an example input format, // Describes the purpose of the parameter and provides an example input format
            name: 'DATABASE_PRIVATE_SUBNETS', // Sets the name of the parameter as "PUBLIC_SUBNETS"
            omitValueField: true, // Hides the value field in the generated form element (since the value will be dynamically generated)
            randomName: 'choice-parameter-5930014456170239', // Generates a unique random name for the parameter
            referencedParameters: 'ACTION', // Specifies that the parameter is dependent on another parameter named "ACTION"
            script: [
                $class: 'GroovyScript', // Defines the scripting language used to generate the form element
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        'return  ""' // If no script is provided, return an empty string
                ], 
                script: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        """ if (ACTION.equals("Create")){
                            inputBox = "<input name='value'  class='setting-input' type='text' >"
                            return inputBox
                            }
                            if (ACTION.equals("Modify") ){
                            inputBox = "<input name='value' multiple class='setting-input' type='text'>"
                            return inputBox
                            }
                        """ // Defines the Groovy script that generates the HTML form element based on the selected "ACTION" parameter value
                ]
            ]
        ],
        [$class: 'CascadeChoiceParameter',
            choiceType: 'PT_SINGLE_SELECT',
            description: 'Choose the VPC Endpoint Service',
            filterLength: 1,
            filterable: true,
            randomName: 'choice-parameter-5631414457171159',
            referencedParameters: 'REGION,CREDENTIAL,ACTION',
            name: 'SERVICE_NAME',
            script: [
                $class: 'GroovyScript',
                fallbackScript: [
                    classpath: [],
                    sandbox: true,
                    script: "return ['Error: AWS credentials not found']" // Default value if no options available
                ],
                script: [
                    classpath: [],
                    sandbox: false,
                    script: 
                        '''import jenkins.model.*
                        import org.apache.http.client.methods.*
                        import org.apache.http.impl.client.*
                        import groovy.json.JsonSlurper

                        def region = "${REGION}"
                        def credential = "${CREDENTIAL}"
                        
                            def jenkinsCredentials = com.cloudbees.plugins.credentials.CredentialsProvider.lookupCredentials(
                                com.cloudbees.plugins.credentials.Credentials.class,
                                Jenkins.instance,
                                null,
                                null
                            )

                            def AWS_ACCESS_KEY_ID
                            def AWS_SECRET_ACCESS_KEY
                            def key

                            for (creds in jenkinsCredentials) {
                                if (creds.id == credential) {
                                    key = creds.secret.toString(creds.secret)
                                    def jsonSlurper = new JsonSlurper()
                                    def cfg = jsonSlurper.parseText(key)
                                    AWS_ACCESS_KEY_ID = cfg['accessKeyId']
                                    AWS_SECRET_ACCESS_KEY = cfg['secretAccessKey']

                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY  aws ec2 describe-vpc-endpoint-services --region $REGION --query ServiceDetails[*].ServiceName --output text"
                                    def proc = command.execute()
                                    proc.waitFor()
                                    def output = proc.in.text
                                    def exitcode = proc.exitValue()
                                    def error = proc.err.text
                                        
                                    if (error) {
                                        println "Std Err: ${error}"
                                        println "Process exit code: ${exitcode}"
                                        return exitcode
                                    }
                                    return output.tokenize() 
                                } 
                            }
                    '''
                ]
            ]
        ],

        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: '''If the endpoint service name is s3 or dynamo DB, set the type as Gateway or if the endpoint service name is not s3 or dynamo DB, write the type as Interface''', 
            name: 'VPC_ENDPOINT_TYPE', 
            omitValueField: true,
            randomName: 'choice-parameter-58319114413178019', 
            referencedParameters: 'ACTION,CREDENTIAL', 
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
                description: 'Set true if the vpc endpoint type as interface or if the endpoint type is gateway set to false',
                filterLength: 4,
                filterable: true,
                randomName: 'choice-parameter-5631321347899219',
                referencedParameters: 'ACTION',
                name: 'PRIVATE_DNS_ENABLED',
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
                        '''
                        
                        if (ACTION == "Create") {
                            return ['true','false']
                        } else if (ACTION == "Modify") {
                            return ['true','false']
                        } else {
                            return [""]
                        }
                        '''
                        

                    ]
                ]
            ],

        separator(name: "VPC-PEERING-PARAMETERS", sectionHeader: "VPC-Peering Parameters",			
            separatorStyle: "border-width: 0",
			sectionHeaderStyle: """
				background-color: #E1D9D1;
				text-align: center;
				padding: 4px;
				color: #343434;
				font-size: 13px;
				font-weight: bold;
				text-transform: uppercase;
				font-family: 'Helvetica', sans-serif;
				letter-spacing: 1px;
				font-style: normal;
			"""
		),
        [$class: 'ChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: 'Region in which the acceptor VPC is present', 
            filterLength: 4, 
            filterable: false,
            referencedParameters: 'CREDENTIAL,ACTION', 
            name: 'PEER_REGION', 
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
                        ''' if (ACTION.equals("Create") || ACTION.equals("Modify")){
                                return ['us-east-1','us-east-2','us-west-1','us-west-2','af-south-1','ap-east-1','ap-southeast-3','ap-south-1','ap-northeast-3','ap-northeast-2','ap-southeast-1','ap-southeast-2','ap-northeast-1','ca-central-1','eu-central-1','eu-west-1','eu-west-2','us-gov-east-1','us-gov-west-1','sa-east-1','eu-south-1','eu-west-3','eu-north-1','me-south-1']
                        }
                    '''
                ]
            ]
        ],
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: 'PeerOwnerId identifies the AWS account ID of the owner of the accepter VPC (the VPC being peered with). It helps ensure that the correct AWS account is referenced when establishing the peering connection.', 
            name: 'PEER_OWNER_ID', 
            omitValueField: true,
            randomName: 'choice-parameter-56316113156179612', 
            referencedParameters: 'ACTION,CREDENTIAL,PEER_REGION', 
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
                            inputBox = "<input name='value' class='setting-input' type='text' defaultValue='1'>"
                            return inputBox
                        }
                    '''
                    ]
                ]
            ], 
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: '''The ID of the Acceptor VPC''', 
            name: 'PEER_VPC_ID', 
            omitValueField: true,
            randomName: 'choice-parameter-59319114450178019', 
            referencedParameters: 'ACTION,CREDENTIAL', 
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
 
        [$class: 'DynamicReferenceParameter', // Defines a DynamicReferenceParameter object (used to dynamically generate pipeline parameters at runtime) and specifies its configuration options
            choiceType: 'ET_FORMATTED_HTML', // Sets the choice type to ET_FORMATTED_HTML (allows HTML content to be embedded in parameter descriptions)
            description: 'CIDR range for the Acceptor VPC,Example:172.31.0.0/16', // Sets the parameter description text
            //defaultValue: '172.31.0.0/16',
            name: 'PEER_VPC_CIDR_RANGE', // Sets the parameter name
            omitValueField: true, // Hides the "Default Value" field on the parameter configuration page (since it is not needed for this parameter)
            randomName: 'choice-parameter-5621314156178614', // Sets a random name for the parameter (not important for functionality)
            referencedParameters: 'ACTION', // Specifies that the system should use the value of the "ACTION" parameter as a reference in generating the dynamic choices for this parameter
            script: [ // Defines a script within the DynamicReferenceParameter object that will be executed to determine the dynamic choices for this parameter
                $class: 'GroovyScript', // Defines a GroovyScript object (used to execute Groovy code within the pipeline) and specifies its configuration options
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
    ])
])
pipeline{ 
    agent any 
    options { // Specifies pipeline-specific options/behaviors
        ansiColor('xterm') // Enables ANSI color formatting in the pipeline console output using the "xterm" color scheme
    }
    environment { // Defines environment variables used by the pipeline
        resource = "aws-vpc-config" // Sets the value of the "resource" variable to "network"
        resource_name = "${env.STACK_NAME}" // Sets the value of the "resource_name" variable to the value of the "RESOURCE_NAME" environment variable
        aws_region = "${env.REGION}".replace(',',''); // Sets the value of the "aws_region" variable to the value of the "REGION" environment variable with commas removed (using the "replace" function)
        PROJECT_NAME = sh (returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 1").trim() // Runs a shell command to extract the client name from the "CREDENTIAL" parameter and sets it as the value of the "CLIENT_NAME" variable
        ACCOUNT_ID = sh (returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 2").trim() // Runs a shell command to extract the account ID from the "CREDENTIAL" parameter and sets it as the value of the "ACCOUNT_ID" variable
    }

    stages{   
        stage("Workspace cleanup"){ 
            steps{ 
                script{ // Allows for execution of arbitrary Groovy code within the pipeline
                    if (params.STACK_NAME==  '') { // Checks if the "VPC_CIDR_RANGE" parameter is empty
                        currentBuild.displayName = "Loading parameters" // Updates the Jenkins build display name to indicate that pipeline parameter loading is in progress
                        currentBuild.result = 'ABORTED' // Marks the Jenkins build as aborted
                        error('The parameter value is null') // Throws an error indicating that a required parameter value is missing
                    }     
                    // def item = Jenkins.instance.getItemByFullName("${env.CLIENT_NAME}_Resource_Multibranch/1.Terraform-VPC-Pipeline")
                    // item.setDescription(" The pipeline to create, modify and destroy the VPC resources. The resources created through this pipeline are VPC, public & private subnets, NAT & Internet gateway and the routes.") 
                    currentBuild.displayName = "${ACTION} the resource ${params.STACK_NAME} - ${IAC_TOOL}" // Updates the Jenkins build display name to indicate the current action ("ACTION") and resource name ("STACK_NAME")
                    currentBuild.description = "${ACTION} the resource ${params.STACK_NAME} - ${IAC_TOOL}" // Updates the Jenkins build description to indicate the current action ("ACTION") and resource name ("RESOURCE_STACK_NAME")                   
                }              
                wrkspace_cleanup () // Calls a function named "wrkspace_cleanup()" to clean up the workspace (defined in the jenkins shared library)
            }
        }
        stage("SCM Checkout"){ 
            steps {
                script{ // Allows for execution of arbitrary Groovy code within the pipeline
                    if (env.IAC_TOOL == "Terraform") {
                        tf_scm_checkout ()  // Calls a function named "cft_scm_checkout()" to perform an SCM checkout (defined in the jenkins shared library)
                        }
                        else {
                        cft_scm_checkout ()  // Calls a function named "cft_scm_checkout()" to perform an SCM checkout (defined in the jenkins shared library)
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
                        cft_resource_provision ('vpc-config.j2') // Calls a function named "cft_resource_provision()" here we need to provide the "jinja2" file name to provision a resource (defined in the jenkins shared library)                     
                        cft_git_commit () // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
                        }
                    }    
                }
           }
        }
    }
}
