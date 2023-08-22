properties([
    parameters([
        [$class: 'ChoiceParameter',  // Defines a choice parameter for the pipeline
            choiceType: 'PT_SINGLE_SELECT',  // The type of choice parameter to create
            description: 'Choose anyone of the below credential to provision the resources on the AWS account',  // A description of the parameter
            filterLength: 1,  // The minimum number of characters required for the filter
            filterable: false,  // Whether the parameter is filterable
            name: 'CREDENTIAL',  // The name of the parameter
            script: [  // A script to run to populate the choices for the parameter
                $class: 'GroovyScript',  // The type of script to run
                fallbackScript: [  // A fallback script to use if the main script fails
                    classpath: [],  // The classpath to use for the fallback script
                    sandbox: false,  // Whether to run the fallback script in a sandbox
                    script: 
                        "return['Create']"  // The fallback script itself, which just returns the 'Create' string
                ], 
                script: [  // The main script to run
                    classpath: [],  // The classpath to use for the script
                    sandbox: false,  // Whether to run the script in a sandbox
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
                        '''  // The main script itself, which looks up the credentials available to the current user and returns them
                ]
            ]
        ],
        string(
            defaultValue: '',  // The default value for the parameter
            name: 'ENV_NAME',  // The name of the parameter
            trim: false,  // Whether to trim whitespace from the parameter value
            description: 'The Environment name in which the S3 for statefile and DynamoDB is created.'  // A description of the parameter
        )
    ])
])

pipeline{
    agent any
    environment {
        //Extract the project name from the parameter passed in and store it in the PROJECT_NAME variable
        PROJECT_NAME = sh (returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 1").trim()
        //Extract the AWS account ID from the parameter passed in and store it in the ACCOUNT_ID variable
        ACCOUNT_ID = sh (returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 2").trim()
    }

    stages{
        //The first stage is to clean the workspace
        stage('1. Clean workspace'){
            steps{
                script{
                    //If the environment name is null, then abort the build and throw an error
                    if (params.ENV_NAME == '') { 
                        currentBuild.displayName = "Loading parameters"
                        currentBuild.result = 'ABORTED'
                        error('The parameter value is null')
                    }
                    //Set the display name of the current build to show what is happening in this stage
                    currentBuild.displayName = "Creating S3 & Dynamodb for project ${env.PROJECT_NAME} with AWS account ID ${env.ACCOUNT_ID} for the environment ${ENV_NAME}"
                }
                //Remove all files from the workspace
                sh "rm -rf ${env.WORKSPACE}/*"
            }
        }

        stage('2. Creating S3 and Dynamodb for Terraform statefiles'){
            steps{
                script {
                    //Retrieve the AWS credentials from Jenkins credentials store using the project name and AWS account ID
                    withCredentials([string(credentialsId: "${env.PROJECT_NAME.toLowerCase()}_${env.ACCOUNT_ID}_aws_cred", variable: 'secret')]) {
                        //Extract the access key and secret access key from the credentials and set them as environment variables
                        def creds = readJSON text: secret
                        env.AWS_ACCESS_KEY_ID = creds['accessKeyId']
                        env.AWS_SECRET_ACCESS_KEY = creds['secretAccessKey']

                        //Execute AWS CLI commands to create S3 bucket and Dynamodb table
                        sh """
                            aws s3api create-bucket \
                            --bucket  "${env.PROJECT_NAME.toLowerCase()}-${env.ACCOUNT_ID}-${params.ENV_NAME.toLowerCase()}-terraform-statefile-bucket" \
                            --region  us-east-1 \
                            --acl private

                            aws s3api put-public-access-block \
                                --bucket "${env.PROJECT_NAME.toLowerCase()}-${env.ACCOUNT_ID}-${params.ENV_NAME.toLowerCase()}-terraform-statefile-bucket" \
                                --public-access-block-configuration "BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true" 

                            aws dynamodb create-table \
                                --table-name "${env.PROJECT_NAME.toLowerCase()}-${env.ACCOUNT_ID}-${params.ENV_NAME.toLowerCase()}-terraform-lock-db" \
                                --attribute-definitions AttributeName=LockID,AttributeType=S \
                                --key-schema AttributeName=LockID,KeyType=HASH \
                                --provisioned-throughput ReadCapacityUnits=1,WriteCapacityUnits=1 \
                                --table-class STANDARD \
                                --region us-east-1 
                            """
                    }   
                }
            }
        }
    }
}

    
