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
			description: 'Choose anyone of the below IAC Tools to provision the route53',
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
			name: 'HOSTEDZONENAME',
			trim: false,
			description: 'The name for the hosted zone. For example, cloudifyops.com'
		),
		[$class: 'CascadeChoiceParameter',
			choiceType: 'PT_SINGLE_SELECT',
			description: 'Choose anyone of the below Hosted zone type. The type can either be Public or Private',
			filterLength: 4,
			filterable: true,
			randomName: 'choice-parameter-56313111561789229',
			referencedParameters: 'ACTION',
			name: 'HOSTED_ZONE_TYPE',
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
						return ["Public", "Private"]
					} else if (ACTION == "Modify") {
						return ["Public", "Private"]
					} else {
						return [""]
					}
					'''
					

				]
			]
		],
		[$class: 'CascadeChoiceParameter',
			choiceType: 'PT_CHECKBOX',
			description: 'To use this hosted zone to resolve DNS queries for one or more VPCs, choose the VPCs. To associate a VPC with a hosted zone when the VPC was created using a different AWS account, you must use a programmatic method, such as the AWS CLI',
			filterLength: 4,
			filterable: true,
			randomName: 'choice-parameter-56313111561773386',
			referencedParameters: 'ACTION, HOSTED_ZONE_TYPE, REGION, CREDENTIAL',
			name: 'ROUTE53_VPCNAME',
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

					if (ACTION.equals("Create") || ACTION.equals("Modify")) {
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
								// if (HOSTED_ZONE_TYPE.equals("Private")) {
								// 	command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY "
								// } else {
								// 	'return ""'
								// }
								if (HOSTED_ZONE_TYPE.equals("Private")) {
									command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-vpcs --region $REGION --query Vpcs[*].Tags[?Key==`Name`].Value --output text"
								} else {
									'return ""'
								}

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
					}
					'''
					

				]
			]
		],
		[$class: 'DynamicReferenceParameter',
			choiceType: 'ET_FORMATTED_HIDDEN_HTML',
			description: 'The VPC IDS to which the Hosted zone needs to be linked. This is a hidden parameter which works in the backend',
			name: 'ROUTE53_VPCID',
			omitValueField: true,
			randomName: 'choice-parameter-563116178619',
			referencedParameters: 'ACTION,CREDENTIAL,ROUTE53_VPCNAME,REGION',
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
					'''
					import jenkins.model.*
					import org.apache.http.client.methods.*
					import org.apache.http.impl.client.*
					import groovy.json.JsonSlurper;

					if (ACTION.equals("Create") || ACTION.equals("Modify")) {
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

								// def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-subnets --filter Name=tag:Name,Values=$SUBNET_NAME --query=Subnets[].SubnetId --region=$REGION --output text"
								if (ROUTE53_VPCNAME.equals("None") || ROUTE53_VPCNAME.equals("Default")) {
									command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-vpcs --query=Vpcs[].VpcId --region $REGION  --filters Name=is-default,Values=true --output text"
								} else {
									command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-vpcs --filters Name=tag:Name,Values=$ROUTE53_VPCNAME --query=Vpcs[].VpcId --region $REGION --output text"
								}

								def proc = command.execute()
								proc.waitFor()
								def output = proc.in.text
								def out = output.tokenize()
								List modifiedout = out.collect {
									'"' + it + '"'
								}

								def exitcode = proc.exitValue()
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
	])
])

pipeline {
	agent any
	options {
		ansiColor('xterm')
	}

	environment {
		resource = "route-53"
		resource_name = "${env.HOSTEDZONENAME}".replace('.', '');
		aws_region = "${env.REGION}".replace(',', '');
		PROJECT_NAME = sh(returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 1").trim()
		ACCOUNT_ID = sh(returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 2").trim()

	}

	stages {
		stage("Workspace cleanup") {
			steps {
				script {
					if (params.HOSTEDZONENAME == '') {
						currentBuild.displayName = "Loading parameters"
						currentBuild.result = 'ABORTED'
						error('The parameter value is null')
					}
					currentBuild.displayName = "${ACTION} the host zone ${params.HOSTEDZONENAME} - ${IAC_TOOL}"
					currentBuild.description = "${ACTION} the host zone ${params.HOSTEDZONENAME} - ${IAC_TOOL}"

				}

				wrkspace_cleanup() //cleanup the workspace
			}
		}

		stage("SCM Checkout") {
			steps {
				script { // Allows for execution of arbitrary Groovy code within the pipeline
					if (env.IAC_TOOL == "Terraform") {
						tf_scm_checkout() // Calls a function named "tf_scm_checkout()" to perform an SCM checkout (defined in the jenkins shared library)
					} else {
						cft_scm_checkout() // Calls a function named "tf_scm_checkout()" to perform an SCM checkout (defined in the jenkins shared library)
						// print "${env.ZONE_NAME}"
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
						cft_resource_provision('route53.j2') // Calls a function named "cft_resource_provision()" here we need to provide the "jinja2" file name to provision a resource (defined in the jenkins shared library)                     
						cft_git_commit() // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
                        }
					}
				}
			}
		}
	}
}
