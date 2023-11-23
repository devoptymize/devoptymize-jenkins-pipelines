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
            description: 'AWS region in which the loadbalancer needs to be provisioned', 
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
            description: ''''This name will be added as a prefix to all the resource that will be created in AWS account. Additionally, stack names should not contain "_" '''
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
            randomName: 'choice-parameter-5631311156178614', // Sets a random name for the parameter (not important for functionality)
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
            //defaultValue: '["10.0.0.0/24","10.0.1.0/24"]',
            description: 'Private subnet CIDR ranges in a list format to create the private subnets.Here we can create number of subnet which is based on our requirement and the subnet will create based on the number of cidr range which we have provide ,Suggests providing a comma-separated list of CIDR ranges enclosed in square brackets, such as["10.0.0.0/24","10.0.1.0/24","10.0.1.0/24"]', // Describes the purpose of the parameter and provides an example input format
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
            //defaultValue: '["10.0.3.0/24","10.0.4.0/24"]',
            description: 'Public subnet CIDR ranges in a list format to create the public subnets.Here we can create number of subnet which is based on our requirement and the subnet will create based on the number of cidr range which we have provide ,Suggests providing a comma-separated list of CIDR ranges enclosed in square brackets, such as["10.0.0.0/24","10.0.3.0/24",10.0.4.0/24]', // Describes the purpose of the parameter and provides an example input format
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
            // defaultValue: '["10.0.6.0/24","10.0.7.0/24"]',
            description: 'Database subnet CIDR ranges in a list format to create the database subnets.Here we can create number of subnet which is based on our requirement and the subnet will create based on the number of cidr range which we have provide ,Suggests providing a comma-separated list of CIDR ranges enclosed in square brackets, such as["10.0.5.0/24","10.0.6.0/24","10.0.7.0/24"]', // Describes the purpose of the parameter and provides an example input format, // Describes the purpose of the parameter and provides an example input format
            name: 'DATABASE_PRIVATE_SUBNETS', // Sets the name of the parameter as "PUBLIC_SUBNETS"
            omitValueField: true, // Hides the value field in the generated form element (since the value will be dynamically generated)
            randomName: 'choice-parameter-5931314456171239', // Generates a unique random name for the parameter
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
        separator(name: "AUTOSCALING-GROUP-PARAMETERS", sectionHeader: "Autoscaling-group Parameters",			
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
            description: 'select the OS family which u need for this launch template, required for CFT', 
            filterLength: 4, 
            filterable: true, 
            randomName: 'choice-parameter-563131115617892200', 
            referencedParameters: 'IAC_TOOL', 
            name: 'OPERATINGSYSTEM', 
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
                              return ["Dont have to choose OS for TF"]
                            } else if (IAC_TOOL == "CloudFormation") {
                              return ["linux","windows"]
                            } else {
                              return ["select an option"]
                            }
                        '''
                ]
            ]
        ],
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            defaultValue: 'ami-02396cdd13e9a1257',
            description: '''The ID of the Amazon Machine Image (AMI) to be used for creating the Launch Template, example :- "ami-02396cdd13e9a1257" ''', 
            name: 'AMI_ID', 
            omitValueField: true,
            randomName: 'choice-parameter-56313113156178610', 
            referencedParameters: 'ACTION,CREDENTIAL,LAUNCH_TEMPLATE_NAME,REGION', 
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
                    // Checks if the referenced parameter ACTION equals "Create" , "Modify"
                    // Defines the input box HTML code to display for the parameter.
                    // Returns the input box HTML code
                    ''' 
                        if (ACTION.equals("Create") || ACTION.equals("Modify")){
                            inputBox = "<input name='value' class='setting-input' type='text'>"
                            return inputBox
                        }
                    '''
                ]
            ]
        ],                                                          
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: '''Launch Template Instance type in which the resources need to be provisioned.''', 
            filterLength: 4, 
            filterable: true, 
            randomName: 'choice-parameter-56313111561789616', 
            referencedParameters: 'ACTION', 
            name: 'INSTANCE_TYPE', 
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
                                return ['r6a.2xlarge','im4gn.8xlarge','r5ad.24xlarge','c3.8xlarge','c6a.16xlarge','m5a.4xlarge','is4gen.4xlarge','m5zn.2xlarge','m5zn.large','r6gd.medium','r4.16xlarge','c6i.large','m6id.12xlarge','r6id.2xlarge','m4.4xlarge','c6gd.metal','is4gen.xlarge','r6gd.2xlarge','m5.8xlarge','d3en.2xlarge','m6a.2xlarge','r6gd.xlarge','x2iedn.xlarge','m5ad.12xlarge','r5a.xlarge','g3.16xlarge','m5dn.large','c6a.24xlarge','d3en.8xlarge','x2iezn.4xlarge','t1.micro','c6i.2xlarge','c5n.2xlarge','x1e.2xlarge','c5ad.12xlarge','i3en.24xlarge','z1d.2xlarge','r6id.xlarge','g5.2xlarge','r6a.metal','c4.8xlarge','c4.4xlarge','m6i.large','m5dn.2xlarge','r5dn.8xlarge','r6id.16xlarge','m6a.large','x2iedn.2xlarge','m5d.8xlarge','g5.4xlarge','m6id.xlarge','r6g.8xlarge','c6id.8xlarge','m5a.16xlarge','r6i.xlarge','c6i.32xlarge','c5ad.24xlarge','c6g.16xlarge','i4i.16xlarge','m6gd.xlarge','r5b.12xlarge','c7g.8xlarge','m2.4xlarge','t2.2xlarge','i3en.3xlarge','r6g.medium','c5d.large','t4g.nano','m6gd.medium','r5.24xlarge','c6a.48xlarge','x1e.8xlarge','m5n.16xlarge','m6id.2xlarge','r6g.large','m6gd.4xlarge','r5n.12xlarge','m6gd.12xlarge','c6i.8xlarge','im4gn.2xlarge','x1e.32xlarge','m5dn.12xlarge','i3en.12xlarge','f1.2xlarge','r6a.xlarge','c6gn.16xlarge','m5a.2xlarge','r6g.16xlarge','r3.large','x2gd.8xlarge','m5zn.6xlarge','r5a.2xlarge','c3.large','x2gd.metal','d2.8xlarge','m5d.xlarge','x1.16xlarge','c5ad.4xlarge','c6i.4xlarge','i2.2xlarge','c6id.large','m6gd.metal','c5ad.16xlarge','m5ad.8xlarge','g4dn.metal','m6id.32xlarge','r6g.4xlarge','r5a.16xlarge','m5ad.4xlarge','r3.8xlarge','m5ad.2xlarge','m5.24xlarge','a1.medium','t3a.small','c6id.16xlarge','c7g.16xlarge','c5n.4xlarge','m6g.metal','c6a.32xlarge','c5ad.xlarge','m5dn.xlarge','c5.4xlarge','m6a.metal','g4dn.12xlarge','m5ad.large','t4g.2xlarge','d3.8xlarge','c6g.12xlarge','r6i.24xlarge','m5d.large','t3.medium','i4i.4xlarge','c6i.24xlarge','g4ad.8xlarge','m6g.xlarge','r5b.metal','c7g.4xlarge','c5d.2xlarge','m5dn.8xlarge','h1.8xlarge','a1.large','t3.large','c6id.24xlarge','t3.2xlarge','x2iedn.metal','g5.24xlarge','t2.large','m5.12xlarge','x2iezn.8xlarge','c5d.12xlarge','z1d.xlarge','t3.xlarge','d2.2xlarge','u-3tb1.56xlarge','im4gn.xlarge','m5d.metal','c5a.xlarge','x2idn.24xlarge','r5.large','r5ad.8xlarge','r6i.4xlarge','c7g.xlarge','m6i.24xlarge','p2.8xlarge','c1.xlarge','g4ad.2xlarge','r6gd.metal','r6a.12xlarge','g4ad.4xlarge','r5a.12xlarge','c5.xlarge','c6gn.xlarge','t4g.medium','i4i.metal','m4.xlarge','r6gd.large','c5a.4xlarge','r6id.12xlarge','t3a.2xlarge','r6a.32xlarge','r6id.4xlarge','c6g.xlarge','r5n.4xlarge','m5.metal','m5d.16xlarge','c6gn.4xlarge','m2.2xlarge','inf1.xlarge','r5ad.12xlarge','m6i.8xlarge','m5.xlarge','r5a.large','m5n.8xlarge','c5a.12xlarge','g5g.2xlarge','r6a.8xlarge','g3.8xlarge','c4.large','x2gd.4xlarge','c5n.large','m5n.large','m6gd.16xlarge','t4g.small','c6gn.medium','c6i.16xlarge','c1.medium','g3.4xlarge','m4.large','m5.4xlarge','t3a.medium','r5ad.large','r5b.large','u-12tb1.112xlarge','c6a.12xlarge','inf1.6xlarge','r5b.24xlarge','m6a.4xlarge','t2.micro','m3.xlarge','vt1.3xlarge','vt1.6xlarge','c5d.4xlarge','r6id.32xlarge','c3.xlarge','d3.xlarge','m1.xlarge','m5n.4xlarge','r5.16xlarge','mac2.metal','c5d.24xlarge','r6g.12xlarge','r5b.xlarge','g5.12xlarge','x2gd.16xlarge','a1.xlarge','r5d.2xlarge','c6a.metal','r5.4xlarge','r5dn.metal','g5g.16xlarge','p3.2xlarge','r5d.4xlarge','r3.2xlarge','t4g.large','m5n.metal','m6i.metal','m6i.32xlarge','m5zn.3xlarge','r5d.xlarge','r5n.2xlarge','i2.xlarge','x2gd.medium','mac1.metal','g2.2xlarge','m4.16xlarge','d3en.4xlarge','c5.12xlarge','c6a.8xlarge','r5n.16xlarge','c5a.24xlarge','r5b.2xlarge','c6gd.16xlarge','m6i.4xlarge','m5d.24xlarge','inf1.2xlarge','g5g.metal','u-6tb1.56xlarge','c6gd.xlarge','r5.metal','c5.24xlarge','p4d.24xlarge','r6a.48xlarge','c5a.2xlarge','m5zn.12xlarge','r5n.24xlarge','m6g.large','r5d.16xlarge','x2idn.metal','h1.2xlarge','r5dn.xlarge','i4i.2xlarge','r5d.large','d3.2xlarge','r5b.4xlarge','x2gd.xlarge','m6g.4xlarge','r6gd.4xlarge','d3en.6xlarge','r3.4xlarge','h1.4xlarge','c6gn.12xlarge','c6g.large','r5dn.large','m6i.16xlarge','x2gd.2xlarge','m5n.12xlarge','c5n.9xlarge','m6g.8xlarge','r6id.24xlarge','r5a.4xlarge','m5dn.4xlarge','r6gd.16xlarge','i3.8xlarge','c5a.16xlarge','h1.16xlarge','c5n.18xlarge','r3.xlarge','t2.medium','r4.xlarge','m6id.metal','i3.xlarge','m6id.16xlarge','c4.2xlarge','i2.4xlarge','is4gen.large','r6i.16xlarge','x2iedn.16xlarge','t4g.micro','c7g.medium','x2idn.32xlarge','x2gd.12xlarge','c5d.xlarge','m5zn.xlarge','r5ad.16xlarge','g5.16xlarge','a1.2xlarge','m5.16xlarge','t3.nano','r5dn.16xlarge','r6gd.8xlarge','z1d.metal','c5ad.2xlarge','c3.2xlarge','c5.metal','x2iezn.2xlarge','r6id.metal','r4.2xlarge','m5ad.xlarge','g4dn.xlarge','r5ad.xlarge','m5ad.24xlarge','i2.8xlarge','u-6tb1.112xlarge','g5.xlarge','x2gd.large','r5n.xlarge','g5g.8xlarge','t3.micro','r6i.metal','m5dn.metal','x1e.16xlarge','t3a.large','c4.xlarge','c7g.2xlarge','c5.9xlarge','m6g.16xlarge','c5n.metal','m5zn.metal','c6id.metal','z1d.6xlarge','c5d.18xlarge','r6id.large','c6id.32xlarge','r6i.12xlarge','c5d.metal','z1d.12xlarge','r5d.8xlarge','m6g.12xlarge','m5a.8xlarge','m5dn.16xlarge','m6gd.8xlarge','a1.4xlarge','m3.2xlarge','r5.xlarge','r6a.24xlarge','r5b.16xlarge','r5d.24xlarge','i3.large','x2iezn.12xlarge','m5dn.24xlarge','r5dn.12xlarge','m6a.16xlarge','c3.4xlarge','c6a.xlarge','m4.2xlarge','is4gen.2xlarge','r6a.4xlarge','i4i.large','a1.metal','c5.2xlarge','r5.2xlarge','x2idn.16xlarge','m5d.2xlarge','r5.8xlarge','c6a.4xlarge','m5d.12xlarge','t4g.xlarge','r5ad.2xlarge','x2iedn.4xlarge','c5.large','r6g.2xlarge','r4.8xlarge','d2.4xlarge','p3.16xlarge','r5.12xlarge','x2iedn.32xlarge','r6g.xlarge','vt1.24xlarge','r6i.32xlarge','x2iezn.metal','c6i.metal','c5ad.large','r5n.8xlarge','i3.2xlarge','x1e.4xlarge','c6g.4xlarge','r6gd.12xlarge','m5n.24xlarge','m6gd.2xlarge','c6id.xlarge','i3en.large','x1e.xlarge','is4gen.medium','t2.xlarge','c6i.xlarge','m6id.4xlarge','c6gn.8xlarge','m5n.2xlarge','m5.2xlarge','r5b.8xlarge','r5a.24xlarge','x2iedn.24xlarge','im4gn.16xlarge','m6a.xlarge','c6a.2xlarge','im4gn.large','c6gd.4xlarge','c6g.medium','m5a.large','i3en.xlarge','m6i.xlarge','r5dn.4xlarge','c7g.large','r5ad.4xlarge','t3.small','r6id.8xlarge','d3.4xlarge','g5.8xlarge','i3.metal','m2.xlarge','r5d.12xlarge','x1.32xlarge','r5d.metal','g3s.xlarge','c6id.12xlarge','m6a.24xlarge','im4gn.4xlarge','c6gn.2xlarge','i4i.32xlarge','r5dn.2xlarge','m6id.24xlarge','m5ad.16xlarge','i4i.xlarge','c7g.12xlarge','p2.xlarge','dl1.24xlarge','c6g.8xlarge','c5ad.8xlarge','m3.medium','c6i.12xlarge','c6id.2xlarge','inf1.24xlarge','x2iezn.6xlarge','m6gd.large','p2.16xlarge','m3.large','g4dn.16xlarge','r6g.metal','r4.large','m5n.xlarge','m4.10xlarge','i3en.2xlarge','r5a.8xlarge','t2.small','m6id.8xlarge','c6gn.large','m5a.xlarge','c6gd.medium','r6a.16xlarge','p3.8xlarge','r6i.8xlarge','m5.large','m6a.32xlarge','g4ad.16xlarge','i3.16xlarge','t3a.nano','z1d.large','c5a.8xlarge','r6a.large','c6gd.2xlarge','g5g.xlarge','c6gd.8xlarge','m1.medium','z1d.3xlarge','f1.4xlarge','r4.4xlarge','m6g.2xlarge','c5.18xlarge','d3en.xlarge','r5dn.24xlarge','c6g.metal','m6i.12xlarge','c5n.xlarge','i3en.6xlarge','g5.48xlarge','g4dn.2xlarge','f1.16xlarge','m5d.4xlarge','i4i.8xlarge','t2.nano','c6gd.large','is4gen.8xlarge','r5n.large','m6a.8xlarge','m6i.2xlarge','c6a.large','i3en.metal','g5g.4xlarge','m6a.12xlarge','g4dn.8xlarge','c6g.2xlarge','i3.4xlarge','m5a.12xlarge','r6i.2xlarge','g4dn.4xlarge','c5d.9xlarge','t3a.micro','d3en.12xlarge','c5a.large','t3a.xlarge','m6id.large','d2.xlarge','m1.large','g2.8xlarge','c6gd.12xlarge','p3dn.24xlarge','m6a.48xlarge','m6g.medium','r6i.large','m5a.24xlarge','m1.small','cc2.8xlarge','c6id.4xlarge','g4ad.xlarge','x2iedn.8xlarge','u-9tb1.112xlarge','r5n.metal','null']
                            }
                        '''
                ]
            ]
        ],
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: '''Key pair which needs to used to provision Launch Template, if u need to use a new key pair please create a new key-pair using KEY-PAIR-PIPELINE. You can use existing key pair in this list down Select null if you Don't want to include key pair in launch template''', 
            name: 'KEY_PAIR', 
            randomName: 'choice-parameter-56313144356155119', 
            referencedParameters: 'ACTION,CREDENTIAL,REGION', 
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
                    '''import jenkins.model.*
                        import org.apache.http.client.methods.*
                        import org.apache.http.impl.client.*
                        import groovy.json.JsonSlurper;
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
                                if(creds.id == "$CREDENTIAL"){
                                    key = creds.secret.toString(creds.secret);
                                    def jsonSlurper = new JsonSlurper()
                                    cfg = jsonSlurper.parseText(key)
                                    
                                    AWS_ACCESS_KEY_ID = cfg['accessKeyId']  
                                    AWS_SECRET_ACCESS_KEY = cfg['secretAccessKey']
                                    //  Execute an AWS CLI command to get the available key pairs
                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws ec2 describe-key-pairs  --query=KeyPairs[].KeyName --region=$REGION --output text"
                                    def proc = command.execute()
                                    proc.waitFor()              
                                    def output = proc.in.text
                                    def exitcode= proc.exitValue()
                                    def error = proc.err.text
                                    
                                    if (error) {
                                        println "Std Err: ${error}"
                                        println "Process exit code: ${exitcode}"
                                        return exitcode
                                    }
                                    def lst = output.tokenize()
                                    lst.add("null")
                                    return lst 
                                } 
                            }
                        }
                    '''
                ]
            ]
        ],
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: '''Enter the storage volume size to include it in the launch template, whose type will be GP3. For Linux flavour the size should be >= 8GB & for windows flavour the size should be >= 30GB. The format of input if u require more than one then the additional volumes should be provide like this  ["10","20","30"]. ''', 
            name: 'VOLUME_SIZE', 
            omitValueField: true,
            randomName: 'choice-parameter-563131445117869', 
            referencedParameters: 'ACTION', 
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
                    // defines a groovy script to generate the input box dynamically
                        ''' if (ACTION.equals("Create") || ACTION.equals("Modify")){
                            inputBox = "<input name='value' class='setting-input'  type='text'>"
                            return inputBox
                            }
                        '''
                ]
            ]
        ],
        string(
                defaultValue: '', 
                name: 'VOLUME_TYPE',
                description: 'The type of volume to attach. Example gp2, gp3', 
                trim: true
                
        ),
        [$class: 'DynamicReferenceParameter', 
                choiceType: 'ET_FORMATTED_HTML', 
                omitValueField: true,
                description: 'Enter the rules to be added to the Securit Group. Follow the format [[port,"protocol",["cidr_block"],["security_grp_id"]]], Example for Terraform: [[22,"tcp",["0.0.0.0/0"],[]] , For CFT: 22,tcp,0.0.0.0/0,;80,tcp,0.0.0.0/0', 
                name: 'INGRESSRULES', 
                randomName: 'choice-parameter-56313113156178611', 
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
                            ''' if (ACTION.equals("Create") || ACTION.equals("Modify")){
                                return """<textarea name=\"value\" rows=\"5\" class=\"setting-input\"></textarea>"""
                                }
                            '''
                    ]
                ]
        ],
        
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: 'The desired number of Amazon EC2 instances that should be running in the autoscaling group the parameter should be Number example:- "2" , Based on this number only it will decide how many number of instance should create', 
            name: 'ASG_DESIRED_CAPACITY', 
            omitValueField: true,
            randomName: 'choice-parameter-56313113156178612', 
            referencedParameters: 'ACTION,CREDENTIAL,ASG_NAME,REGION', 
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
                description: 'The minimum number of Amazon EC2 instances that should be running in the autoscaling group the parameter should be Number example:- "1" , Based on this number only it will decide how many number of instance should create', 
                name: 'ASG_MIN_SIZE', 
                omitValueField: true,
                randomName: 'choice-parameter-56313113156178614', 
                referencedParameters: 'ACTION,CREDENTIAL,ASG_NAME,REGION', 
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
                        '''
                            if (ACTION.equals("Create") || ACTION.equals("Modify")){
                                inputBox = "<input name='value' class='setting-input' type='text'>"
                                return inputBox
                            }
                        '''
                    ]
                ]
            ], 
        [$class: 'DynamicReferenceParameter', 
                choiceType: 'ET_FORMATTED_HTML', 
                description: 'The maximum number of Amazon EC2 instances that should be running in the autoscaling group the parameter should be Number example:- "3" , Based on this number only it will decide how many number of instance should create', 
                name: 'ASG_MAX_SIZE', 
                omitValueField: true,
                randomName: 'choice-parameter-56313113156178619', 
                referencedParameters: 'ACTION,CREDENTIAL,ASG_NAME,REGION', 
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
                        '''
                            if (ACTION.equals("Create") || ACTION.equals("Modify")){
                                inputBox = "<input name='value' class='setting-input' type='text'>"
                                return inputBox
                            }
                        '''
                    ]
                ]
            ],
        separator(name: "LOADBALANCER-PARAMETERS", sectionHeader: "Loadbalancer Parameters",			
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

        string(
            defaultValue: '', 
            name: 'ACM_CERTIFICATE_ARN_ALB', 
            trim: true,
            description: '''Add the certificate ARN for the load balancer'''
        ),
        
        separator(name: "RDS-PARAMETERS", sectionHeader: "RDS Parameters",			
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
        string(
                defaultValue: '', 
                name: 'RDS_CLUSTER_IDENTIFIER', 
                trim: true,
                description: '''Type a name for your DB cluster. The name must be unique across all DB clusters owned by your AWS account in the current AWS Region. The DB identifier is case-insensitive, but is stored as all lowercase. Constraints: 1 to 60 alphanumeric characters or hyphens. First character must be a letter. Can't contain two consecutive hyphens. Can't end with a hyphen.'''
        ),
        [$class: 'CascadeChoiceParameter', 
                choiceType: 'PT_SINGLE_SELECT', 
                description: 'The RDS Cluster engine type.', 
                filterLength: 4, 
                filterable: true, 
                randomName: 'choice-parameter-56313111561789619', 
                referencedParameters: 'ACTION,RDS_CLUSTER_IDENTIFIER,REGION,CREDENTIAL', 
                name: 'ENGINE_TYPE', 
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
                            '''import jenkins.model.*
                            import org.apache.http.client.methods.*
                            import org.apache.http.impl.client.*
                            import groovy.json.JsonSlurper; 

                            if (ACTION.equals("Create")){
                                return ['postgres']
                            }        

                            if (ACTION.equals("Modify")){
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
                                    if(creds.id == "$CREDENTIAL"){
                                        key = creds.secret.toString(creds.secret);
                                        def jsonSlurper = new JsonSlurper()
                                        cfg = jsonSlurper.parseText(key)
                                        
                                        AWS_ACCESS_KEY_ID = cfg['accessKeyId']  
                                        AWS_SECRET_ACCESS_KEY = cfg['secretAccessKey']
                                        

                                        def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws rds describe-db-instances --db-instance-identifier=$RDS_CLUSTER_IDENTIFIER --query=DBInstances[].Engine  --output text --region $REGION"
                                        def proc = command.execute()
                                        proc.waitFor()              
                                        def output = proc.in.text
                                        def exitcode= proc.exitValue()
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
                description: '''The engine version to use. The Multi-AZ DB cluster deployment option is available for MySQL version 8.0.28 or later and PostgreSQL version 13.4 R1.''', 
                name: 'ENGINE_VERSION', 
                randomName: 'choice-parameter-5631156178619', 
                referencedParameters: 'ACTION,CREDENTIAL,REGION,ENGINE_TYPE', 
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
                        ''' import jenkins.model.*
                            import org.apache.http.client.methods.*
                            import org.apache.http.impl.client.*
                            import groovy.json.JsonSlurper;
                            
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
                                    if(creds.id == "$CREDENTIAL"){
                                        key = creds.secret.toString(creds.secret);
                                        def jsonSlurper = new JsonSlurper()
                                        cfg = jsonSlurper.parseText(key)
                                        
                                        AWS_ACCESS_KEY_ID = cfg['accessKeyId']  
                                        AWS_SECRET_ACCESS_KEY = cfg['secretAccessKey']
                                        

                                        def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws rds describe-db-engine-versions   --engine=$ENGINE_TYPE --query=DBEngineVersions[].EngineVersion  --output text --region $REGION"
                                        def proc = command.execute()
                                        proc.waitFor()              
                                        def output = proc.in.text
                                        def exitcode= proc.exitValue()
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
                choiceType: 'ET_FORMATTED_HTML', 
                description: '''Specify a string that defines the  master user. Constraints: At least 8 printable ASCII characters. Can't contain any of the following: / (slash), '(single quote), "(double quote) and @ (at sign).''', 
                name: 'DB_MASTER_USERNAME', 
                omitValueField: true,
                randomName: 'choice-parameter-5631331445102319', 
                referencedParameters: 'ACTION', 
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
        [$class: 'DynamicReferenceParameter', 
                choiceType: 'ET_FORMATTED_HTML', 
                description: '''Specify a string that defines the password for the master user. Constraints: At least 8 printable ASCII characters. Can't contain any of the following: / (slash), '(single quote), "(double quote) and @ (at sign).''', 
                name: 'DB_MASTER_PASSWORD', 
                omitValueField: true,
                randomName: 'choice-parameter-56313314451178619', 
                referencedParameters: 'ACTION', 
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
                description: '''Choose the DB instance type that allocates the computational, network, and memory capacity required by planned workload of this DB instance.''', 
                filterLength: 4, 
                filterable: true, 
                randomName: 'choice-parameter-5631561789619', 
                referencedParameters: 'ACTION', 
                name: 'DB_INSTANCE_CLASS', 
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
                                    return ['db.m6g.16xlarge', 'db.m6g.12xlarge', 'db.m6g.8xlarge', 'db.m6g.4xlarge', 'db.m6g.2xlarge', 'db.m6g.xlarge', 'db.m6g.large', 'db.m6gd.16xlarge', 'db.m6gd.12xlarge', 'db.m6gd.8xlarge', 'db.m6gd.4xlarge', 'db.m6gd.2xlarge', 'db.m6gd.xlarge', 'db.m6gd.large', 'db.m6i.32xlarge', 'db.m6i.24xlarge', 'db.m6i.16xlarge', 'db.m6i.12xlarge', 'db.m6i.8xlarge', 'db.m6i.4xlarge', 'db.m6i.2xlarge', 'db.m6i.xlarge', 'db.m6i.large', 'db.m5d.24xlarge', 'db.m5d.16xlarge', 'db.m5d.12xlarge', 'db.m5d.8xlarge', 'db.m5d.4xlarge', 'db.m5d.2xlarge', 'db.m5d.xlarge', 'db.m5d.large', 'db.m5.24xlarge', 'db.m5.16xlarge', 'db.m5.12xlarge', 'db.m5.8xlarge', 'db.m5.4xlarge', 'db.m5.2xlarge', 'db.m5.xlarge', 'db.m5.large', 'db.m4.16xlarge', 'db.m4.10xlarge', 'db.m4.4xlarge', 'db.m4.2xlarge', 'db.m4.xlarge', 'db.m4.large', 'db.m3.2xlarge', 'db.m3.xlarge', 'db.m3.large', 'db.m3.medium', 'db.x2g.16xlarge', 'db.x2g.12xlarge', 'db.x2g.8xlarge', 'db.x2g.4xlarge', 'db.x2g.2xlarge', 'db.x2g.xlarge', 'db.x2g.large', 'db.z1d.12xlarge', 'db.z1d.6xlarge', 'db.z1d.3xlarge', 'db.z1d.2xlarge', 'db.z1d.xlarge', 'db.z1d.large', 'db.x1e.32xlarge', 'db.x1e.16xlarge', 'db.x1e.8xlarge', 'db.x1e.4xlarge', 'db.x1e.2xlarge', 'db.x1e.xlarge', 'db.x1.32xlarge', 'db.x1.16xlarge', 'db.r6g.16xlarge', 'db.r6g.12xlarge', 'db.r6g.8xlarge', 'db.r6g.4xlarge', 'db.r6g.2xlarge', 'db.r6g.xlarge', 'db.r6g.large', 'db.r6gd.16xlarge', 'db.r6gd.12xlarge', 'db.r6gd.8xlarge', 'db.r6gd.4xlarge', 'db.r6gd.2xlarge', 'db.r6gd.xlarge', 'db.r6gd.large', 'db.r6i.32xlarge', 'db.r6i.24xlarge', 'db.r6i.16xlarge', 'db.r6i.12xlarge', 'db.r6i.8xlarge', 'db.r6i.4xlarge', 'db.r6i.2xlarge', 'db.r6i.xlarge', 'db.r6i.large', 'db.r5d.24xlarge', 'db.r5d.16xlarge', 'db.r5d.12xlarge', 'db.r5d.8xlarge', 'db.r5d.4xlarge', 'db.r5d.2xlarge', 'db.r5d.xlarge', 'db.r5d.large', 'db.r5b.24xlarge', 'db.r5b.16xlarge', 'db.r5b.12xlarge', 'db.r5b.8xlarge', 'db.r5b.4xlarge', 'db.r5b.2xlarge', 'db.r5b.xlarge', 'db.r5b.large', 'db.r5.12xlarge.tpc2.mem2x', 'db.r5.8xlarge.tpc2.mem3x', 'db.r5.6xlarge.tpc2.mem4x', 'db.r5.4xlarge.tpc2.mem4x', 'db.r5.4xlarge.tpc2.mem3x', 'db.r5.4xlarge.tpc2.mem2x', 'db.r5.2xlarge.tpc2.mem8x', 'db.r5.2xlarge.tpc2.mem4x', 'db.r5.2xlarge.tpc1.mem2x', 'db.r5.xlarge.tpc2.mem4x', 'db.r5.xlarge.tpc2.mem2x', 'db.r5.large.tpc1.mem2x', 'db.r5.24xlarge', 'db.r5.16xlarge', 'db.r5.12xlarge', 'db.r5.8xlarge', 'db.r5.4xlarge', 'db.r5.2xlarge', 'db.r5.xlarge', 'db.r5.large', 'db.r4.16xlarge', 'db.r4.8xlarge', 'db.r4.4xlarge', 'db.r4.2xlarge', 'db.r4.xlarge', 'db.r4.large', 'db.r3.4xlarge', 'db.r3.2xlarge', 'db.r3.xlarge', 'db.r3.large', 'db.t4g.2xlarge', 'db.t4g.xlarge', 'db.t4g.large', 'db.t4g.medium', 'db.t4g.small', 'db.t4g.micro', 'db.t3.2xlarge', 'db.t3.xlarge', 'db.t3.large', 'db.t3.medium', 'db.t3.small', 'db.t3.micro', 'db.t2.2xlarge', 'db.t2.xlarge', 'db.t2.large', 'db.t2.medium', 'db.t2.small', 'db.t2.micro']
                                }
                            '''
                    ]
                ]
            ],
        string(
            defaultValue: '', 
            name: 'ALLOCATED_STORAGE', 
            trim: true,
            description: '''Specify the allocated storage for the rds cluster'''
        ),
        separator(name: "CLOUDFRONT-PARAMETERS", sectionHeader: "Route53 Parameters",
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
        string(
                defaultValue: '', 
                name: 'ACM_CERTIFICATE_ARN_CLOUDFRONT', 
                trim: true,
                description: '''Specify the certificate ARN from us-east-1 for the Cloudfront distribution'''
        ),


        separator(name: "ROUTE53-PARAMETERS", sectionHeader: "Route53 Parameters",
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
        string(
                defaultValue: '', 
                name: 'HOSTED_ZONE_ID', 
                trim: true,
                description: '''The Id Of Hostedzone which we have to create'''
        ),
        string(
                defaultValue: '', 
                name: 'DOMAIN_NAME', 
                trim: true,
                description: ''' The name of the cloudfront DNS that we need to create'''
		),

    ])
])

pipeline{ 
    agent any 
    options { // Specifies pipeline-specific options/behaviors
        ansiColor('xterm') // Enables ANSI color formatting in the pipeline console output using the "xterm" color scheme
    }
    environment { // Defines environment variables used by the pipeline
        resource = "aws-3t-arch" // Sets the value of the "resource" variable to "network"
        resource_name = "${env.STACK_NAME}" // Sets the value of the "resource_name" variable to the value of the "VPC_CIDR_RANGE" environment variable
        aws_region = "${env.REGION}".replace(',',''); // Sets the value of the "aws_region" variable to the value of the "REGION" environment variable with commas removed (using the "replace" function)
        PROJECT_NAME = sh (returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 1").trim() // Runs a shell command to extract the client name from the "CREDENTIAL" parameter and sets it as the value of the "CLIENT_NAME" variable
        ACCOUNT_ID = sh (returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 2").trim() // Runs a shell command to extract the account ID from the "CREDENTIAL" parameter and sets it as the value of the "ACCOUNT_ID" variable
    }

    stages{   
        stage("Workspace cleanup"){ 
            steps{ 
                script{ // Allows for execution of arbitrary Groovy code within the pipeline
                    if (params.VPC_CIDR_RANGE== '') { // Checks if the "VPC_CIDR_RANGE" parameter is empty
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
                        cft_resource_provision ('aws-3t-arch.j2') // Calls a function named "cft_resource_provision()" here we need to provide the "jinja2" file name to provision a resource (defined in the jenkins shared library)                     
                        cft_git_commit () // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
                        }
                    }
                }
            }
        }
    }
}