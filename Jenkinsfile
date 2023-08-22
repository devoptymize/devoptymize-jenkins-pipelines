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
                            "return ['Create','Delete']"
                    ]
                ]
        ],
        // ChoiceParameter is also used to select a credential to use to provision the resources on the AWS account.
        [$class: 'ChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: 'Choose anyone of the below credential to perform the action on the AWS account', 
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
        string(
            defaultValue: '', 
            name: 'KEY_PAIR_NAME', 
            trim: true,
            description: 'The EC2 Instance name which needs to be provisioned.'
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
        ]
    ])
])


pipeline {
    agent any
    options { // Specifies pipeline-specific options/behaviors
        ansiColor('xterm') // Enables ANSI color formatting in the pipeline console output using the "xterm" color scheme
    }
    environment { // Defines environment variables used by the pipeline
        resource = "key-pair" // Sets the value of the "resource" variable to "key-pair"
        resource_name = "${env.KEY_PAIR_NAME}" // Sets the value of the "resource_name" variable to the value of the "KEY_PAIR_NAME" environment variable
        aws_region = "${env.REGION}".replace(',','') // Sets the value of the "aws_region" variable to the value of the "REGION" environment variable with commas removed (using the "replace" function)
        PROJECT_NAME = sh(returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 1").trim()  // Runs a shell command to extract the project name from the "CREDENTIAL" parameter and sets it as the value of the "PROJECT_NAME" variable
        ACCOUNT_ID = sh(returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 2").trim() // Runs a shell command to extract the account ID from the "CREDENTIAL" parameter and sets it as the value of the "ACCOUNT_ID" variable
    }
    
    stages {
        stage("Workspace cleanup") {
            steps {
                script { // Allows for execution of arbitrary Groovy code within the pipeline
                    if (params.KEY_PAIR_NAME == '') { // Checks if the "KEY_PAIR_NAME" parameter is empty
                        currentBuild.displayName = "Loading parameters" // Updates the Jenkins build display name to indicate that pipeline parameter loading is in progress
                        currentBuild.result = 'ABORTED' // Marks the Jenkins build as aborted
                        error('The parameter value is null') // Throws an error indicating that a required parameter value is missing
                    }
                    currentBuild.displayName = "${ACTION} the Key-Pair ${params.KEY_PAIR_NAME} - ${IAC_TOOL}" // Updates the Jenkins build display name to indicate the current action ("ACTION") and key-pair name ("KEY_PAIR_NAME")
                    currentBuild.description = "${ACTION} the Key-Pair ${params.KEY_PAIR_NAME} - ${IAC_TOOL}" // Updates the Jenkins build description to indicate the current action ("ACTION") and key-pair name ("KEY_PAIR_NAME")
                }
                wrkspace_cleanup() // Calls a function named "wrkspace_cleanup()" to clean up the workspace (defined in the Jenkins shared library)
            }
        }
        
        stage("SCM Checkout") {
            steps {
                script { // Allows for execution of arbitrary Groovy code within the pipeline
                    if (env.IAC_TOOL == "Terraform") {
                        tf_scm_checkout() // Calls a function named "tf_scm_checkout()" to perform an SCM checkout (defined in the Jenkins shared library)
                    } else {
                        cft_scm_checkout() // Calls a function named "cft_scm_checkout()" to perform an SCM checkout (defined in the Jenkins shared library)
                    }
                }
            }
        }
        
        stage("Provisioning resource") {
            steps {
                script { // Allows for execution of arbitrary Groovy code within the pipeline
                    if (env.IAC_TOOL == "Terraform") {
                        withAWS(region: "$REGION") {
                            tf_resource_provision() // Calls a function named "tf_resource_provision()" to provision a resource (defined in the jenkins shared library)
                            git_commit() // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
                        }
                    } else {
                        withAWS(region: "$REGION") {
                            cft_resource_provision('key-pair.j2') // Calls a function named "cft_resource_provision()" here we need to provide the "jinja2" file name to provision a resource (defined in the jenkins shared library) 
                            cft_git_commit() // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)

                            if (env.ACTION == "Create") {
                                sh '''
                                sleep 30
                                x=$(aws ec2 describe-key-pairs --filters Name=key-name,Values=$KEY_PAIR_NAME --region $REGION --query=KeyPairs[].KeyPairId --output text)
                                echo "$x"
                                aws ssm get-parameter --name /ec2/keypair/$x --with-decryption --query Parameter.Value --region $REGION --output text > $KEY_PAIR_NAME.pem
                                '''
                                archiveArtifacts artifacts: "${params.KEY_PAIR_NAME}.pem", fingerprint: true
                            }

                            if (env.ACTION == "Delete") {
                                sh "rm -f ${params.KEY_PAIR_NAME}.pem"
                            }
                        }
                    }
                }
            }
        }
    }
}
