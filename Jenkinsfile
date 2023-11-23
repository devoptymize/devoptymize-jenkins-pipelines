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
            description: 'In AWS region in which to provision the resources', 
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
        string(
            defaultValue: '', 
            name: 'STACK_NAME', 
            trim: true,
            description: '''This name will be added as a prefix to all the resource that will be created in AWS account.Additionally, stack names should not contain "_" '''
        ),


        separator(name: "AWS-CODEPIPELINE", sectionHeader: "AWS codepipeline",	
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
          [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: 'Name of the AWS CodeCommit repository where your application source code is stored.', 
            name: 'REPOSITORY_NAME', 
            omitValueField: true, 
            randomName: 'choice-parameter-5631311156178614', 
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
            [$class: 'DynamicReferenceParameter', 
                choiceType: 'ET_FORMATTED_HTML', 
                description: 'Name of the branch in the CodeCommit repository that will be monitored for changes.Whenever changes are pushed to this branch, the pipeline will automatically start.', 
                name: 'BRANCH_NAME', 
                omitValueField: true, 
                randomName: 'choice-parameter-5631311156178614', 
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
                description: '''The type of compute environment. This determines the number of CPU cores and memory the build environment uses.''', 
                filterLength: 4, 
                filterable: true, 
                randomName: 'choice-parameter-5631561789619', 
                referencedParameters: 'ACTION', 
                name: 'COMPUTE_TYPE', 
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
                                    return ['BUILD_GENERAL1_SMALL','BUILD_GENERAL1_MEDIUM','BUILD_GENERAL1_LARGE']
                                }
                            '''
                    ]
                ]
            ],

          [$class: 'CascadeChoiceParameter', 
                choiceType: 'PT_SINGLE_SELECT', 
                description: '''The operating system for the build environment in CodeBuild. Choose the appropriate operating system based on your application requirements.''', 
                filterLength: 4, 
                filterable: true, 
                randomName: 'choice-parameter-5631561789613', 
                referencedParameters: 'ACTION', 
                name: 'OPERATING_SYSTEM', 
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
                                    return ['Linux','Ubuntu','Windows']
                                }
                            '''
                    ]
                ]
            ],
        
        // Adding a new CascadeChoiceParameter for Image based on selected operating system
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: 'The image tag that identifies the Docker image to use for this build project.', 
            filterLength: 1, 
            filterable: false, 
            name: 'IMAGE', 
            referencedParameters: 'OPERATING_SYSTEM', 
            script: [
                $class: 'GroovyScript', 
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        "return []"
                ], 
                script: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        '''
                        def osImages = [
                            'Linux': [
                                'aws/codebuild/amazonlinux2-x86_64-standard:4.0',
                                'aws/codebuild/amazonlinux2-x86_64-standard:5.0',
                                'aws/codebuild/amazonlinux2-x86_64-standard:corretto8',
                                'aws/codebuild/amazonlinux2-x86_64-standard:corretto11',
                                'aws/codebuild/amazonlinux2-aarch64-standard:2.0',
                                'aws/codebuild/amazonlinux2-aarch64-standard:3.0'
                            ],
                            'Ubuntu': [
                                'aws/codebuild/standard:5.0',
                                'aws/codebuild/standard:6.0',
                                'aws/codebuild/standard:7.0'
                            ],
                            'Windows': [
                                'aws/codebuild/windows-base:2019-1.0',
                                'aws/codebuild/windows-base:2019-2.0'
                            ]
                        ]
                        return osImages[OPERATING_SYSTEM] ?: []
                        '''
                ]
            ]
        ],
            [$class: 'CascadeChoiceParameter', 
                choiceType: 'PT_SINGLE_SELECT', 
                description: '''The type of build environment to use for related builds.''', 
                filterLength: 4, 
                filterable: true, 
                randomName: 'choice-parameter-5631561789611', 
                referencedParameters: 'ACTION', 
                name: 'TYPE', 
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
                                    return ['ARM_CONTAINER','LINUX_CONTAINER','LINUX_GPU_CONTAINER','WINDOWS_SERVER_2019_CONTAINER']
                                }
                            '''
                    ]
                ]
            ],
             separator(name: "CDN", sectionHeader: "CDN",	
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
        [$class: 'CascadeChoiceParameter',
            choiceType: 'PT_SINGLE_SELECT',
            description: 'Name of the Existing Amazon S3 bucket where the artifacts generated by the build process will be stored',
            filterLength: 1,
            filterable: true,
            randomName: 'choice-parameter-5631414457171165',
            referencedParameters: 'REGION,CREDENTIAL,ACTION',
            name: 'S3BUCKETNAME',
            script: [
                $class: 'GroovyScript',
                fallbackScript: [
                    classpath: [],
                    sandbox: true,
                    script: 'return "" ' // Default value if no options available
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
                                if (creds.id == credential) {
                                    key = creds.secret.toString(creds.secret)
                                    def jsonSlurper = new JsonSlurper()
                                    def cfg = jsonSlurper.parseText(key)
                                    AWS_ACCESS_KEY_ID = cfg['accessKeyId']
                                    AWS_SECRET_ACCESS_KEY = cfg['secretAccessKey']

                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws s3 ls --region $REGION --output text --query 'Buckets[].Name'"
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
                                    def bucketNames = output.readLines().collect { it.split()[2] }
                                    return bucketNames
                                } 
                            }
                        }
                    '''
                ]
            ]
        ],


         [$class: 'CascadeChoiceParameter',
            choiceType: 'PT_SINGLE_SELECT',
            description: 'ID of the AWS CloudFront distribution associated with your application.This ID will be used to invalidate the CloudFront cache during the deployment process.',
            filterLength: 1,
            filterable: true,
            randomName: 'choice-parameter-563141445712',
            referencedParameters: 'REGION,CREDENTIAL,ACTION',
            name: 'CLOUDFRONT_DISTRIBUTION_ID',
            script: [
                $class: 'GroovyScript',
                fallbackScript: [
                    classpath: [],
                    sandbox: true,
                    script: 'return "" '// Default value if no options available
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
                                if (creds.id == credential) {
                                    key = creds.secret.toString(creds.secret)
                                    def jsonSlurper = new JsonSlurper()
                                    def cfg = jsonSlurper.parseText(key)
                                    AWS_ACCESS_KEY_ID = cfg['accessKeyId']
                                    AWS_SECRET_ACCESS_KEY = cfg['secretAccessKey']

                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws cloudfront list-distributions --query DistributionList.Items[*].Id --output text"
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
                                    def distributionIds = output.tokenize()
                                    return distributionIds
                                } 
                            }
                        }
                        
                    '''     
                ]
            ]
            ],
            separator(name: "AWS-SNS", sectionHeader: "AWS SNS (Simple Notification Service)",	
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
            [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: 'Email address to receive notifications via Amazon SNS (Simple Notification Service). You will receive status updates and notifications about pipeline events on this email address. enter email ["example2@gmail.com", "example1@gmail.com"]', 
            name: 'EMAILS', 
            omitValueField: true, 
            randomName: 'choice-parameter-5931314456171239', 
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

    ]    
)
])

pipeline{ 
    agent any 
    options { // Specifies pipeline-specific options/behaviors
        ansiColor('xterm') // Enables ANSI color formatting in the pipeline console output using the "xterm" color scheme
    }
    environment { // Defines environment variables used by the pipeline
        resource = "aws-cicd-sw" 
        resource_name = "${env.STACK_NAME}"  
        aws_region = "${env.REGION}".replace(',',''); // Sets the value of the "aws_region" variable to the value of the "REGION" environment variable with commas removed (using the "replace" function)
        PROJECT_NAME = sh (returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 1").trim() // Runs a shell command to extract the project name from the "CREDENTIAL" parameter and sets it as the value of the "PROJECT_NAME" variable
        ACCOUNT_ID = sh (returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 2").trim() // Runs a shell command to extract the account ID from the "CREDENTIAL" parameter and sets it as the value of the "ACCOUNT_ID" variable
    }

    stages{   
        stage("Workspace cleanup"){ 
            steps{ 
                script{ // Allows for execution of arbitrary Groovy code within the pipeline
                    if (params.STACK_NAME== '') { // Checks if the "STACK_NAME" parameter is empty
                        currentBuild.displayName = "Loading parameters" // Updates the Jenkins build display name to indicate that pipeline parameter loading is in progress
                        currentBuild.result = 'ABORTED' // Marks the Jenkins build as aborted
                        error('The parameter value is null') // Throws an error indicating that a required parameter value is missing
                    }     
                                        
                    currentBuild.displayName = "${ACTION} the ${params.STACK_NAME} - ${params.IAC_TOOL}" 
                    currentBuild.description = "${ACTION} the ${params.STACK_NAME} - ${params.IAC_TOOL}"  
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
                        withAWS(region:"$REGION"){
                            sh "pwd"
                            sh "ls -ltr"
                            sh "sed -i 's/CFDistributionId/${env.CLOUDFRONT_DISTRIBUTION_ID}/g' ${workspace}/terraform/child/services/aws-cicd-sw/lambda_function.py"
                        tf_resource_provision () // Calls a function named "tf_resource_provision()" to provision a resource (defined in the jenkins shared library)
                        git_commit() // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
                        }
                    }
                    else {
                        withAWS(region:"$REGION"){
                        cft_resource_provision ('aws-cicd-sw.j2') // Calls a function named "cft_resource_provision()" here we need to provide the "jinja2" file name to provision a resource (defined in the jenkins shared library)                     
                        cft_git_commit () // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
                        }
                    }
                }
           }
        }
    }
}
