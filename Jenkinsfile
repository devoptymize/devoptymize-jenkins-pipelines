properties([
	parameters([

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
					'''
					import jenkins.model.*
					import org.apache.http.client.methods.*
					import org.apache.http.impl.client.*
					import groovy.json.JsonSlurper;
					import jenkins.*
					import hudson.model.*
					def user = User.current().getId()
					String result = user.takeWhile {
						it != '_'
					}

					def jenkinsCredentials = com.cloudbees.plugins.credentials.CredentialsProvider.lookupCredentials(
						com.cloudbees.plugins.credentials.Credentials.class,
						Jenkins.instance,
						null,
						null
					);

					def lst = []
					for (creds in jenkinsCredentials) {
						if ((creds.id).contains(result)) {
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
			name: 'ECR_REPO_NAME',
			trim: false,
			description: 'Provide a concise name and A developer should be able to identify the repository contents by the name'
		),
		//This is a Groovy code block defining a DynamicReferenceParameter in Jenkins for a security group description. 
		//The script then uses the AWS CLI to retrieve the security group's description and displays it as a pre-populated input box if the action is set to "modify." If the action is set to "create," it simply displays an empty input box.
    [$class: 'CascadeChoiceParameter',
			choiceType: 'PT_SINGLE_SELECT',
			description: 'Select the type of the ECR repo. Can be either a public or private one. ( For now public repo is only supported in us-east-1 )',
			filterLength: 4,
			filterable: true,
			randomName: 'choice-parameter-56313111561789229',
			referencedParameters: 'ACTION',
			name: 'REPOSITORY_TYPE',
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
						return ['private','public']
					} else if (ACTION == "Modify") {
						return ['private','public']
					} else {
						return [""]
					}
					'''
					

				]
			]
		],

		[$class: 'CascadeChoiceParameter',
			choiceType: 'PT_CHECKBOX',
			description: 'Select the appropriate users arn who can have read access to ecr repo. Requires at least onr ARN to be selected',
			filterLength: 4,
			filterable: true,
			randomName: 'choice-parameter-56313111561779229',
			referencedParameters: 'ACTION, CREDENTIAL, REPOSITORY_TYPE',
			name: 'READ_ACCESS_ARNS',
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
						if (creds.id == "$CREDENTIAL") {
							key = creds.secret.toString(creds.secret);
							def jsonSlurper = new JsonSlurper()
							cfg = jsonSlurper.parseText(key)

							AWS_ACCESS_KEY_ID = cfg['accessKeyId']
							AWS_SECRET_ACCESS_KEY = cfg['secretAccessKey']
							if (ACTION == "Create" && REPOSITORY_TYPE == "private") {
								command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws iam list-users --query Users[].Arn --output text"
							} else if (ACTION == "Modify") {
								command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws iam list-users --query Users[].Arn --output text"
							} else {
                                return [""]
                            }

							// def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-vpcs  --query=Vpcs[*].{Name:Tags[?Key==`Name`].Value|[0]}  --output text --region $REGION"
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
		//This is a Groovy code block defining a DynamicReferenceParameter in Jenkins for a security group description. 
		//The script then uses the AWS CLI to retrieve the security group's description and displays it as a pre-populated input box if the action is set to "modify." If the action is set to "create," it simply displays an empty input box.

		[$class: 'CascadeChoiceParameter',
			choiceType: 'PT_CHECKBOX',
			description: 'Select the appropriate users arn who can have read and write access to ecr repo. Requires at least onr ARN to be selected',
			filterLength: 4,
			filterable: true,
			randomName: 'choice-parameter-56313111561779231',
			referencedParameters: 'ACTION, CREDENTIAL, REPOSITORY_TYPE',
			name: 'READ_WRITE_ACCESS_ARNS',
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
						if (creds.id == "$CREDENTIAL") {
							key = creds.secret.toString(creds.secret);
							def jsonSlurper = new JsonSlurper()
							cfg = jsonSlurper.parseText(key)

							AWS_ACCESS_KEY_ID = cfg['accessKeyId']
							AWS_SECRET_ACCESS_KEY = cfg['secretAccessKey']
							if (ACTION == "Create" && REPOSITORY_TYPE == "private") {
								command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws iam list-users --query Users[].Arn --output text"
							} else if (ACTION == "Modify") {
								command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws iam list-users --query Users[].Arn --output text"
							} else {
                                return [""]
                            }

							// def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-vpcs  --query=Vpcs[*].{Name:Tags[?Key==`Name`].Value|[0]}  --output text --region $REGION"
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
		
    [$class: 'CascadeChoiceParameter',
			choiceType: 'PT_SINGLE_SELECT',
			description: 'Select Immutable to prevent image tags from being overwritten by subsequent image pushes using the same tag. Select mutable to allow image tags to be overwritten',
			filterLength: 4,
			filterable: true,
			randomName: 'choice-parameter-5631311147889229',
			referencedParameters: 'ACTION',
			name: 'REPOSITORY_IMAGE_TAG_MUTABILITY',
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
						return ['IMMUTABLE','MUTABLE']
					} else if (ACTION == "Modify") {
						return ['IMMUTABLE','MUTABLE']
					} else {
						return [""]
					}
					'''
					

				]
			]
		],

   	[$class: 'CascadeChoiceParameter',
			choiceType: 'PT_SINGLE_SELECT',
			description: 'Set true to have each image automatically scanned after being pushed to a repository and If false, each image scan must be manually started to get scan results',
			filterLength: 4,
			filterable: true,
			randomName: 'choice-parameter-5631321347889229',
			referencedParameters: 'ACTION',
			name: 'SCAN_REPO',
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
		string(
			defaultValue: '',
			name: 'IMAGE_COUNT',
			trim: false,
			description: 'Number of most recent images to keep in the ecr repository'
		),
	])
])

pipeline {
	agent any
	options { // Specifies pipeline-specific options/behaviors
		ansiColor('xterm') // Enables ANSI color formatting in the pipeline console output using the "xterm" color scheme
	}

	environment { // Defines environment variables used by the pipeline
		resource = "ecr" // Sets the value of the "resource" variable to "ecr"
		resource_name = "${ECR_REPO_NAME}" // Sets the value of the "resource_name" variable to the value of the "ECR_REPO_NAME" environment variable
		aws_region = "${env.REGION}".replace(',', ''); // Sets the value of the "aws_region" variable to the value of the "REGION" environment variable with commas removed (using the "replace" function)
		PROJECT_NAME = sh(returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 1").trim() // Runs a shell command to extract the project name from the "CREDENTIAL" parameter and sets it as the value of the "PROJECT_NAME" variable
		ACCOUNT_ID = sh(returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 2").trim() // Runs a shell command to extract the account ID from the "CREDENTIAL" parameter and sets it as the value of the "ACCOUNT_ID" variable

	}

	stages {
		stage("Workspace cleanup") {
			steps {
				script { // Allows for execution of arbitrary Groovy code within the pipeline
					if (params.ECR_REPO_NAME == '') { // Checks if the "ECR_REPO_NAME" parameter is empty
						currentBuild.displayName = "Loading parameters" // Updates the Jenkins build display name to indicate that pipeline parameter loading is in progress
						currentBuild.result = 'ABORTED' // Marks the Jenkins build as aborted
						error('The parameter value is null') // Throws an error indicating that a required parameter value is missing
					}
			             
					currentBuild.displayName = "${ACTION} the ecr ${params.ECR_REPO_NAME} - ${IAC_TOOL}" // Updates the Jenkins build display name to indicate the current action ("ACTION") and ecr repo name ("ECR_REPO_NAME")
					currentBuild.description = "${ACTION} the ecr ${params.ECR_REPO_NAME} - ${IAC_TOOL}" // Updates the Jenkins build description to indicate the current action ("ACTION") and ecr repo name ("ECR_REPO_NAME")
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
                        withAWS(region: "${env.REGION}") {
						tf_resource_provision() // Calls a function named "tf_resource_provision()" to provision a resource (defined in the jenkins shared library)
						git_commit() // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
                        }
                    } 
                    else {
                        withAWS(region: "${env.REGION}") {
						cft_resource_provision('ecr.j2') // Calls a function named "cft_resource_provision()" here we need to provide the "jinja2" file name to provision a resource (defined in the jenkins shared library)                     
						cft_git_commit() // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
                        }
                    }
                }
            }
        }
    }
}
