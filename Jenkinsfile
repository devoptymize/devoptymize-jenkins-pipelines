properties([
  parameters([
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
    // ChoiceParameter is used to allow the user to select one of several predefined options.
    // In this case, the user can choose between Create, Modify, or Delete as the ACTION to perform. 
    [$class: 'CascadeChoiceParameter', 
    choiceType: 'PT_SINGLE_SELECT', 
    description: 'Choose anyone of the below option to perform the action', 
    filterLength: 4, 
    filterable: true, 
    randomName: 'choice-parameter-5631312345789229', 
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
      name: 'TARGET_GROUP_NAME',
      trim: true,
      description: 'The EC2 Instance name which needs to be provisioned.'
    ),
    [$class: 'CascadeChoiceParameter',
      choiceType: 'PT_SINGLE_SELECT',
      description: 'Select the AWS target type from the list below',
      filterLength: 4,
      filterable: true,
      randomName: 'choice-parameter-56313111561789229',
      referencedParameters: 'ACTION',
      name: 'TARGET_TYPE',
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
            return ['instance', 'ip', 'lambda', 'alb']
          } else if (ACTION == "Modify") {
            return ['instance', 'ip', 'lambda', 'alb']
          } else {
            return [""]
          }
          '''          
        ]
      ]
    ],
    [$class: 'CascadeChoiceParameter',
      choiceType: 'PT_SINGLE_SELECT',
      description: 'Select the VPC in which the EC2 Instance needs to be provisioned. The default VPC are showed up as None.',
      name: 'VPC_NAME',
      randomName: 'choice-parameter-5631311156178619',
      referencedParameters: 'ACTION,CREDENTIAL,REGION,INSTANCE_NAME,TARGET_TYPE',
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

          if ((ACTION.equals("Create") || ACTION.equals("Modify")) && (TARGET_TYPE.equals("instance") || TARGET_TYPE.equals("ip") || TARGET_TYPE.equals("alb"))) {
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

                def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-vpcs  --query=Vpcs[*].{Name:Tags[?Key==`Name`].Value|[0]}  --output text --region $REGION"
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
      description: 'Select the VPC in which the EC2 Instance needs to be provisioned',
      name: 'VPC_ID',
      omitValueField: true,
      randomName: 'choice-parameter-56313116178619',
      referencedParameters: 'ACTION,CREDENTIAL,VPC_NAME,REGION',
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
                if (VPC_NAME.equals("None") || VPC_NAME.equals("Default")) {
                  command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-vpcs --query=Vpcs[].VpcId --region $REGION  --filters Name=is-default,Values=true --output text"
                } else {
                  command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-vpcs --filters Name=tag:Name,Values=$VPC_NAME --query=Vpcs[].VpcId --region $REGION --output text"
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

                inputBox = "<input name='value' class='setting-input' type='text' value=$output>"
                return inputBox
              }
            }
          }
          '''

        ]
      ]
    ],
    [$class: 'CascadeChoiceParameter',
      choiceType: 'PT_CHECKBOX',
      description: 'Select the EC2 Instance needs to be set up as target',
      name: 'INSTANCE_NAME',
      randomName: 'choice-parameter-5631314417619',
      referencedParameters: 'ACTION,CREDENTIAL,REGION,TARGET_TYPE,VPC_ID',
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
          if (ACTION.equals("Create") && TARGET_TYPE.equals("instance") || ACTION.equals("Modify") && TARGET_TYPE.equals("instance")) {
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

                def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY  aws ec2 describe-instances --query=Reservations[].Instances[].Tags[?Key==`Name`].Value[] --filters Name=instance-state-name,Values=running Name=vpc-id,Values=$VPC_ID --output text --region $REGION"

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
    // This specific parameter is a Dynamic Reference Parameter which allows for selecting a security group in which an EC2 instance needs to be provisioned.
    // The script sets up the AWS credentials, executes a command to describe the security groups of the specified VPC with the given name, and returns the output as a list of strings surrounded by double quotes. The returned value is used to populate the input field in the Jenkins job configuration.
    [$class: 'DynamicReferenceParameter',
      choiceType: 'ET_FORMATTED_HIDDEN_HTML',
      description: 'Select the SecurityGroup in which the Launch Template Instance needs to be provisioned',
      name: 'EC2_ID',
      omitValueField: true,
      randomName: 'choice-parameter-56311617864419',
      referencedParameters: 'ACTION,CREDENTIAL,INSTANCE_NAME,REGION',
      script: [
        $class: 'GroovyScript',
        fallbackScript: [
          classpath: [],
          sandbox: true,
          script:
          "return['error']"
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

                def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-instances --filters Name=tag:Name,Values=$INSTANCE_NAME --query=Reservations[].Instances[].InstanceId --region $REGION --output text"
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

    [$class: 'DynamicReferenceParameter',
      choiceType: 'ET_FORMATTED_HTML',
      description: 'Enter IP Address in the list incase of Terraform. For example ["X.X.X.X", "X.X.X.X"]. For Cloudformation ,enter as a string separated by comma. For example, X.X.X.X, X.X.X.X',
      name: 'IP_ADDRESS',
      omitValueField: true,
      randomName: 'choice-parameter-5631314178619',
      referencedParameters: 'ACTION,CREDENTIAL,REGION,TARGET_TYPE',
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
          if (ACTION.equals("Create") && TARGET_TYPE.equals("ip") || (ACTION.equals("Modify") && TARGET_TYPE.equals("ip"))) {
            inputBox = "<input name='value' class='setting-input'  type='text'>"
            return inputBox
          }
          '''
        ]
      ]
    ],
    [$class: 'CascadeChoiceParameter',
      choiceType: 'PT_SINGLE_SELECT',
      description: 'Select the Lambda function needs to be set up as target',
      name: 'LAMBDA_FUNCTION_NAME',
      randomName: 'choice-parameter-109000',
      referencedParameters: 'ACTION,CREDENTIAL,REGION,TARGET_TYPE',
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

          if (ACTION.equals("Create") && TARGET_TYPE.equals("lambda") || ACTION.equals("Modify") && TARGET_TYPE.equals("lambda")) {
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
                def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws lambda list-functions --query=Functions[].FunctionName --output text --region $REGION"
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
      description: 'Select the VPC in which the EC2 Instance needs to be provisioned',
      name: 'LAMBDA_FUNCTION_ARN',
      omitValueField: true,
      randomName: 'choice-parameter-56387556178619',
      referencedParameters: 'ACTION,CREDENTIAL,LAMBDA_FUNCTION_NAME,REGION',
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
                // if (VPC_NAME.equals("None") || VPC_NAME.equals("Default")) {
                // 	command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-vpcs --query=Vpcs[].VpcId --region $REGION  --filters Name=is-default,Values=true --output text"
                // } else {
                command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws lambda list-functions --query Functions[?FunctionName=='$LAMBDA_FUNCTION_NAME'].FunctionArn --output text --region $REGION --output text"
                // }

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

                inputBox = "<input name='value' class='setting-input' type='text' value=$output>"
                return inputBox
              }
            }
          }
          '''	
        ]
      ]
    ],
    [$class: 'CascadeChoiceParameter',
      choiceType: 'PT_SINGLE_SELECT',
      description: 'Select the APPLICATION_LOAD_BALANCER ARN needs to be set as a target',
      name: 'APPLICATION_LOAD_BALANCER',
      randomName: 'choice-parameter-56361000S',
      referencedParameters: 'ACTION,CREDENTIAL,REGION,TARGET_TYPE',
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

          if (ACTION.equals("Create") && TARGET_TYPE.equals("alb") || ACTION.equals("Modify") && TARGET_TYPE.equals("alb")) {
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

                def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws elbv2 describe-load-balancers --query=LoadBalancers[].LoadBalancerArn --output text --region $REGION"
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

    [$class: 'CascadeChoiceParameter',
      choiceType: 'PT_SINGLE_SELECT',
      description: 'Select the type of protocol. Always ensure to give TCP when target_type is alb',
      filterLength: 4,
      filterable: true,
      randomName: 'choice-parameter-563131124651789229',
      referencedParameters: 'ACTION,TARGET_TYPE',
      name: 'PROTOCOL_TYPE',
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
          if ((ACTION.equals("Create") && TARGET_TYPE.equals("alb")) || (ACTION.equals("Modify") && TARGET_TYPE.equals("alb"))) {
            return ['TCP']
          } else if ((ACTION.equals("Create") && TARGET_TYPE.equals("ip")) || (ACTION.equals("Create") && TARGET_TYPE.equals("alb")) || (ACTION.equals("Create") && TARGET_TYPE.equals("instance"))) {
            return ['HTTP', 'HTTPS', 'TCP', 'TLS', 'UDP', 'TCP_UDP', 'GENEVE']
          } else if ((ACTION.equals("Modify") && TARGET_TYPE.equals("ip")) || (ACTION.equals("Modify") && TARGET_TYPE.equals("alb")) || (ACTION.equals("Modify") && TARGET_TYPE.equals("instance"))) {
            return ['HTTP', 'HTTPS', 'TCP', 'TLS', 'UDP', 'TCP_UDP', 'GENEVE']
          } else {
            return [""]
          }
          '''	
        ]
      ]
    ],
    [$class: 'DynamicReferenceParameter',
      choiceType: 'ET_FORMATTED_HTML',
      description: 'Enter the PORT_NUMBER',
      name: 'PORT_NUMBER',
      omitValueField: true,
      randomName: 'choice-parameter-56313178000',
      referencedParameters: 'ACTION,CREDENTIAL,REGION,TARGET_TYPE',
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
          if ((ACTION.equals("Create") || ACTION.equals("Modify")) && (TARGET_TYPE.equals("instance") || TARGET_TYPE.equals("ip") || TARGET_TYPE.equals("alb"))) {
            inputBox = "<input name='value' class='setting-input'  type='text'>"
            return inputBox
          }
          '''
        ]
      ]
    ]
  ])
])

pipeline {
  agent any
  options {
    ansiColor('xterm')
  }

  environment {
    resource = "lb-target-group"
    resource_name = "${TARGET_GROUP_NAME}"
    aws_region = "${env.REGION}".replace(',', '');
    PROJECT_NAME = sh(returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 1").trim()
    ACCOUNT_ID = sh(returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 2").trim()

  }

  stages {
    stage("Workspace cleanup") {
      steps {
        script {
          if (params.TARGET_GROUP_NAME == '') {
            currentBuild.displayName = "Loading parameters"
            currentBuild.result = 'ABORTED'
            error('The parameter value is null')
          }
          currentBuild.displayName = "${ACTION} the TargetGroup ${params.TARGET_GROUP_NAME} - ${IAC_TOOL}"
          currentBuild.description = "${ACTION} the TargetGroup ${params.TARGET_GROUP_NAME} - ${IAC_TOOL}"
        }

        wrkspace_cleanup() //cleanup the worksapce
      }
    }

    stage("SCM Checkout") {
      steps {
        script { // Allows for execution of arbitrary Groovy code within the pipeline
          if (env.IAC_TOOL == "Terraform") {
            tf_scm_checkout() // Calls a function named "tf_scm_checkout()" to perform an SCM checkout (defined in the jenkins shared library)
          } else {
            cft_scm_checkout() // Calls a function named "tf_scm_checkout()" to perform an SCM checkout (defined in the jenkins shared library)
            print "${env.TARGET_GROUP_NAME}"
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
          } else {
            withAWS(region: "${env.REGION}") {
              cft_resource_provision('lb-target-group.j2') // Calls a function named "cft_resource_provision()" here we need to provide the "jinja2" file name to provision a resource (defined in the jenkins shared library)                     
              cft_git_commit() // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
            }
          }
        }
      }
    }
  }
}
