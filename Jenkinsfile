// The pipeline consistes of creating  aws creds
pipeline{
    agent any

    parameters{
        string(
            defaultValue: '', 
            name: 'PROJECT_NAME', 
            trim: false,
            description: 'Enter the project name, which will be prefix for the AWS cred.'
        )
        string(
            defaultValue: '', 
            name: 'ACCOUNT_ID', 
            trim: false,
            description: 'The project AWS Account ID for which the credentials are created.'
        )
        string(
            defaultValue: '', 
            name: 'ENV_NAME', 
            trim: false,
            description: 'The Environment name for which the credentials are created.'
        )
        password( 
            name: 'AWS_ACCESS_KEY_ID', 
            description: 'AWS_ACCESS_KEY_ID'
        )
        password(
            name: 'AWS_SECRET_ACCESS_KEY', 
            description: 'AWS_SECRET_ACCESS_KEY'
        )
    }

    stages{
        stage('1. Clean workspace'){
            steps{
                script{
                    // If the PROJECT_NAME parameter is empty, set the display name and result and abort the build
                    if (params.PROJECT_NAME == '') { 
                        currentBuild.displayName = "Loading parameters"
                        currentBuild.result = 'ABORTED'
                        error('The parameter value is null')
                    }
                    // Get the Jenkins item based on the PROJECT_NAME and set the item description
                    def item = Jenkins.instance.getItemByFullName("${params.PROJECT_NAME}_Config_Multibranch/1.Create_AWS_Secrets")
                    item.setDescription(" The pipeline is to create the AWS credentials in AWS Secret manager for the project ${PROJECT_NAME}")
                    // Set the display name of the current build
                    currentBuild.displayName = "Creating AWS creds for project ${PROJECT_NAME} with AWS account ID ${ACCOUNT_ID} for the environment ${ENV_NAME}"
                }
                // Remove all files from the workspace directory
                sh "rm -rf ${env.WORKSPACE}/*"
                // Change to the "import" directory and clone the git repository
                dir ("import") {
                    git branch: '1.Create_AWS_Secrets', credentialsId: 'devoptymize', url: 'https://gitlab.cloudifyops.com/devoptymize_infrastructure/devoptymize_jenkins_config.git'
                }
            }

        }
            
        stage('2. Storing the AWS credentials in AWS Secret manager'){
            steps {
                script{
                    // Mask the AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY parameters
                    wrap([$class: 'MaskPasswordsBuildWrapper', varPasswordPairs: [ 
                        [password: "${AWS_ACCESS_KEY_ID}"], 
                        [password: "${AWS_SECRET_ACCESS_KEY}"] ] ]) {
                            // Authenticate with AWS using the provided credentials
                            withAWS( credentials : "${params.PROJECT_NAME.toLowerCase()}_aws_cred" ){
                                // Change the working directory to 'import'
                                dir ("import") {
                                    // Declare a variable for automatic cancellation of the pipeline
                                    def autoCancelled = false
                                    // Check if the AWS account ID has the correct length (12)
                                    n = params.ACCOUNT_ID.length()
                                    if (n == 12) { 
                                        // Print the length of the AWS account ID
                                        println(params.ACCOUNT_ID.length())
                                        // Execute the shell command to create a secret in AWS Secret Manage
                                        sh """
                                            aws secretsmanager create-secret \
                                                --name "${params.PROJECT_NAME.toLowerCase()}_${params.ACCOUNT_ID}_aws_cred" \
                                                --region  us-east-1 \
                                                --description "AWS cred of project ${params.PROJECT_NAME.toLowerCase()} with account id ${params.ACCOUNT_ID}" \
                                                --tags 'Key=jenkins:credentials:type,Value=string' \
                                                --secret-string "{\\"accessKeyId\\":\\"${params.AWS_ACCESS_KEY_ID}\\",\\"secretAccessKey\\":\\"${params.AWS_SECRET_ACCESS_KEY}\\"}"
                                       
                                            sleep 30
                                        """ 
                                    } else { 
                                        // Print an error message and set autoCancelled to true if the AWS account ID is incorrect
                                        println("The AWS Account ID is incorrect")
                                        autoCancelled = true
                                        error('Please check your AWS Account ID')
                                    }
                                            
                                }
                            }
                    }
                }
            }
        }
   
    }
}
