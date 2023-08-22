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
        // ChoiceParameter is also used to select a credential to use to provision the resources on the AWS account.
        // The script starts by getting all the credentials available in Jenkins and gets the the PROJECT_NAME frow the current_user functon. Then it loops on the credentials to get onl the credentials which starts with the particular PROJECT_NAME.
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
            description: 'The name of the environment in which resources needs to be provisioned.',
        ),

        string(
            defaultValue: '', 
            name: 'BUCKET_NAME', 
            trim: false,
            description: 'Enter the bucket name, It will create the private bucket'
        ),
        [$class: 'ChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: 'Specify whether to use an S3 Bucket Key for encryption.', 
            filterLength: 4, 
            filterable: true, 
            name: 'BUCKET_KEY_ENABLED', 
            script: [
                $class: 'GroovyScript', 
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        "return['true']"
                ], 
                script: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        "return ['true','false']"
                ]
            ]
        ],
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', //The choiceType is set to ET_FORMATTED_HTML, which specifies that the input field should support HTML formatting.
            omitValueField: true,
            description: '''Specify commaseperated value for cloudformation to create s3 bucket policy. For example, Allow,s3:PutObject,arn:aws:iam::xxxxx:root. Specify the bucket policy in json format for terraform. For Example
            {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Effect": "Allow",
                        "Principal": {"AWS": "arn:aws:iam::**************"},
                        "Action": [
                            "s3:GetObject"
                        ],
                        "Resource": [
                            "arn:aws:s3:::your-bucket-name/*"
                        ]
                    }
                ]
            }''', 
            name: 'BUCKET_POLICY', 
            randomName: 'choice-parameter-56313113156178619', 
            referencedParameters: 'IAC_TOOL', 
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
                        ''' if (IAC_TOOL.equals("Terraform") ){
                            return """<textarea name=\"value\" rows=\"8\" class=\"setting-input\"></textarea>"""
                            }
                            else {
                            return """<textarea name=\"value\" rows=\"2\" class=\"setting-input\"></textarea>""" 
                            }
                        '''
                ]
            ]
        ],
    ]
    )
]
)
pipeline {
    agent any
    options { // Specifies pipeline-specific options/behaviors
        ansiColor('xterm')  // Enables ANSI color formatting in the pipeline console output using the "xterm" color scheme
    }
    environment { // Defines environment variables used by the pipeline
        resource = "s3" // Sets the value of the "resource" variable to "s3"
        resource_name = "${env.BUCKET_NAME}" // Sets the value of the "resource_name" variable to the value of the "BUCKET_NAME" environment variable
        aws_region = "${env.REGION}".replace(',',''); // Sets the value of the "aws_region" variable to the value of the "REGION" environment variable with commas removed (using the "replace" function)
        PROJECT_NAME = sh (returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 1").trim() // Runs a shell command to extract the project name from the "CREDENTIAL" parameter and sets it as the value of the "PROJECT_NAME" variable
        ACCOUNT_ID = sh (returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 2").trim() // Runs a shell command to extract the account ID from the "CREDENTIAL" parameter and sets it as the value of the "ACCOUNT_ID" variable
    }

    stages{
        stage("Workspace cleanup"){ // Allows for execution of arbitrary Groovy code within the pipeline
            steps{       
                script{
                    
                    if (params.BUCKET_NAME == '') { // Checks if the "BUCKET_NAME" parameter is empty
                        currentBuild.displayName = "Loading parameters" // Updates the Jenkins build display name to indicate that pipeline parameter loading is in progress
                        currentBuild.result = 'ABORTED' // Marks the Jenkins build as aborted
                        error('The parameter value is null') // Throws an error indicating that a required parameter value is missing
                    }     
                    
                    currentBuild.displayName = "${ACTION} the BUCKET ${params.BUCKET_NAME} - ${IAC_TOOL}" // Updates the Jenkins build display name to indicate the current action ("ACTION") and bucket name ("BUCKET_NAME")
                    currentBuild.description = "${ACTION} the BUCKET ${params.BUCKET_NAME} - ${IAC_TOOL}" // Updates the Jenkins build description to indicate the current action ("ACTION") and bucket name ("BUCKET_NAME")
                } 
                
                wrkspace_cleanup () // Calls a function named "wrkspace_cleanup()" to clean up the workspace (defined in the Jenkins shared library)
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
                        cft_resource_provision ('s3.j2') // Calls a function named "cft_resource_provision()" here we need to provide the "jinja2" file name to provision a resource (defined in the jenkins shared library)                     
                        cft_git_commit () // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
                        }
                    }
                }
            }
        }
    }
}
                
    


            

