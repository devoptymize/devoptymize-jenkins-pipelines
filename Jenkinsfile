properties([
    // This block of code contains the configuration for the parameters of the Jenkins job.
    // There are several types of parameters included, such as ChoiceParameter, DynamicReferenceParameter, and string.
    // ChoiceParameter is used to allow the user to select one of several predefined options.
    // In this case, the user can choose between Create, Modify, or Delete as the ACTION to perform.
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
            defaultValue: '', 
            name: 'VPC_NAME', 
            trim: true,
            description: 'The name of the VPC'
        ),    
        string(
            defaultValue: '', 
            name: 'ENVIRONMENT', 
            trim: true,
            description: 'The name of the environment in which resources needs to be provisioned.'
        ),  
        // ChoiceParameter is used to allow the user to select an AWS region in which to provision resources.                                                     
        [$class: 'ChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: 'AWS region in which the resources need to be provisioned', 
            filterLength: 1, 
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
        [$class: 'DynamicReferenceParameter', // Defines a DynamicReferenceParameter object (used to dynamically generate pipeline parameters at runtime) and specifies its configuration options
            choiceType: 'ET_FORMATTED_HTML', // Sets the choice type to ET_FORMATTED_HTML (allows HTML content to be embedded in parameter descriptions)
            description: 'CIDR range for your VPC,Example:10.0.0.0/16', // Sets the parameter description text
            name: 'VPC_CIDR_RANGE', // Sets the parameter name
            omitValueField: true, // Hides the "Default Value" field on the parameter configuration page (since it is not needed for this parameter)
            randomName: 'choice-parameter-5631311156178619', // Sets a random name for the parameter (not important for functionality)
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
                            inputBox = "<input name='value' class='setting-input' type='text'>"
                            return inputBox
                            }
                            if (ACTION.equals("Modify") ){
                            inputBox = "<input name='value' class='setting-input' type='text'>"
                            return inputBox
                            }
                        '''
                ]
            ]
        ],
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', // Sets the type of form element to be generated as "formatted HTML"
            description: 'Private subnet CIDR ranges in a list format to create that many number of private subnets ,Example:["10.0.0.0/24","10.0.1.0/24","10.0.2.0/24"]', // Describes the purpose of the parameter and provides an example input format
            name: 'PRIVATE_SUBNETS', // Sets the name of the parameter as "PRIVATE_SUBNETS"
            omitValueField: true, // Hides the value field in the generated form element (since the value will be dynamically generated)
            randomName: 'choice-parameter-5631314451178629', // Generates a unique random name for the parameter
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
                        """ if (ACTION.equals("Create")){
                            inputBox = "<input name='value' class='setting-input'  type='text'>"
                            return inputBox
                            }
                            if (ACTION.equals("Modify") ){
                            inputBox = "<input name='value' class='setting-input' type='text'>"
                            return inputBox
                            }
                        """ // Defines the Groovy script that generates the HTML form element based on the selected "ACTION" parameter value
                ]
            ]
        ],
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', // Sets the type of form element to be generated as "formatted HTML"
            description: 'Public subnet CIDR rangesin a list format to create that many number of public subnets ,Example:["10.0.3.0/24","10.0.4.0/24","10.0.5.0/24"]', // Describes the purpose of the parameter and provides an example input format
            name: 'PUBLIC_SUBNETS', // Sets the name of the parameter as "PUBLIC_SUBNETS"
            omitValueField: true, // Hides the value field in the generated form element (since the value will be dynamically generated)
            randomName: 'choice-parameter-5631314456171119', // Generates a unique random name for the parameter
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
        ]
    ])
])


pipeline{ 
    agent any 
    options { // Specifies pipeline-specific options/behaviors
        ansiColor('xterm') // Enables ANSI color formatting in the pipeline console output using the "xterm" color scheme
    }
    environment { // Defines environment variables used by the pipeline
        resource = "network" // Sets the value of the "resource" variable to "network"
        resource_name = "${env.VPC_NAME}" // Sets the value of the "resource_name" variable to the value of the "VPC_NAME" environment variable
        aws_region = "${env.REGION}".replace(',',''); // Sets the value of the "aws_region" variable to the value of the "REGION" environment variable with commas removed (using the "replace" function)
        PROJECT_NAME = sh (returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 1").trim() // Runs a shell command to extract the project name from the "CREDENTIAL" parameter and sets it as the value of the "PROJECT_NAME" variable
        ACCOUNT_ID = sh (returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 2").trim() // Runs a shell command to extract the account ID from the "CREDENTIAL" parameter and sets it as the value of the "ACCOUNT_ID" variable
    }

    stages{   
        stage("Workspace cleanup"){ 
            steps{ 
                script{ // Allows for execution of arbitrary Groovy code within the pipeline
                    if (params.VPC_NAME== '') { // Checks if the "VPC_NAME" parameter is empty
                        currentBuild.displayName = "Loading parameters" // Updates the Jenkins build display name to indicate that pipeline parameter loading is in progress
                        currentBuild.result = 'ABORTED' // Marks the Jenkins build as aborted
                        error('The parameter value is null') // Throws an error indicating that a required parameter value is missing
                    }     
                    // def item = Jenkins.instance.getItemByFullName("${env.PROJECT_NAME}_Resource_Multibranch/1.Terraform-VPC-Pipeline")
                    // item.setDescription(" The pipeline to create, modify and destroy the VPC resources. The resources created through this pipeline are VPC, public & private subnets, NAT & Internet gateway and the routes.")                    
                    currentBuild.displayName = "${ACTION} the VPC ${VPC_NAME} - ${IAC_TOOL}" // Updates the Jenkins build display name to indicate the current action ("ACTION") and VPC name ("VPC_NAME")
                    currentBuild.description = "${ACTION} the VPC ${VPC_NAME} - ${IAC_TOOL}"  // Updates the Jenkins build description to indicate the current action ("ACTION") and VPC name ("VPC_NAME")
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
                        cft_resource_provision ('network.j2') // Calls a function named "cft_resource_provision()" here we need to provide the "jinja2" file name to provision a resource (defined in the jenkins shared library)                     
                        cft_git_commit () // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
                        }
                    }
                }
           }
        }
    }
}



 
