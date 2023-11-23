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

        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: 'Specify the range of IP addresses for your VPC in CIDR notation. For example, if you want to allocate IP addresses from 10.0.0.0 to 10.0.255.255, you would use "10.0.0.0/16" as the CIDR range.', 
            name: 'VPC_CIDR_RANGE', 
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
            description: 'Specify the CIDR ranges for your public subnets in a list format. To define multiple subnets, provide a comma-separated list of CIDR ranges enclosed in square brackets. For instance: ["10.0.0.0/24", "10.0.3.0/24", "10.0.4.0/24"]. Each CIDR range.', 
            name: 'PUBLIC_SUBNETS', 
            omitValueField: true, 
            randomName: 'choice-parameter-5631314456171119', 
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
                            inputBox = "<input name='value' class='setting-input' type='text'>"
                            return inputBox
                            }
                            if (ACTION.equals("Modify") ){
                            inputBox = "<input name='value' multiple class='setting-input' type='text'>"
                            return inputBox
                            }
                        """ 
                ]
            ]
        ],

        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: 'Customize the CIDR ranges for your private subnets using a list format. To define multiple private subnets, provide a comma-separated list of CIDR ranges enclosed in square brackets. For instance: ["10.0.0.0/24", "10.0.1.0/24", "10.0.2.0/24"]. Each CIDR range corresponds to a unique private subnet.', 
            name: 'PRIVATE_SUBNETS', 
            omitValueField: true, 
            randomName: 'choice-parameter-5631314451178629', 
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
            choiceType: 'ET_FORMATTED_HTML', 
            description: 'Tailor the CIDR ranges for your database subnets using a list format. To define multiple database subnets, provide a comma-separated list of CIDR ranges enclosed in square brackets. For example: ["10.0.0.0/24", "10.0.1.0/24", "10.0.2.0/24"]. Each CIDR range corresponds to a distinct database subnet, providing a dedicated space for your database instances.', 
            name: 'DATABASE_PRIVATE_SUBNETS', 
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

        separator(name: "RDS-CLUSTER-PARAMETERS", sectionHeader: "RDS-Cluster Parameters",			
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
                description: 'Choose the engine type that best suits your requirements for the RDS Cluster. This decision determines the database engine software that powers your cluster.', 
                filterLength: 4, 
                filterable: true, 
                randomName: 'choice-parameter-56013111561789619', 
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

                            if (ACTION.equals("Create") || ACTION.equals("Modify")){
                                return ['postgres']
                            }                              
                            '''
                    ]
                ]
            ],
        [$class: 'CascadeChoiceParameter', 
                choiceType: 'PT_SINGLE_SELECT', 
                description: ''' Select the desired engine version for the Amazon RDS Cluster. This choice determines the version of the database engine that will be used within the cluster. Make your selection based on compatibility and features.''', 
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
                                        

                                        def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws rds describe-db-engine-versions   --engine=postgres --query=DBEngineVersions[?EngineVersion!='14.4']|[?EngineVersion!='11.16']|[?EngineVersion!='11.19']|[?EngineVersion!='11.20']|[?EngineVersion!='12.11']|[?EngineVersion!='11.18']|[?EngineVersion!='12.12']|[?EngineVersion!='11.17']|[?EngineVersion!='12.13']|[?EngineVersion!='12.14']|[?EngineVersion!='12.15']|[?EngineVersion!='14.3']|[?EngineVersion!='11.21']|[?EngineVersion!='12.16'].EngineVersion  --output text --region us-east-1"
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
                description: '''Specify the username for the master user of your database. The username you provide must adhere to certain constraints: it must be at least 8 printable ASCII characters in length and cannot contain the characters / (slash), ' (single quote), " (double quote), or @ (at sign).''', 
                name: 'DB_MASTER_USERNAME', 
                omitValueField: true,
                randomName: 'choice-parameter-5631331445102319', 
                referencedParameters: 'ACTION,CREDENTIAL,REGION,ENGINE_TYPE', 
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
                description: '''Define the password for the master user of your database. The password must follow specific constraints: it must be at least 8 printable ASCII characters in length and cannot contain the characters / (slash), ' (single quote), " (double quote), or @ (at sign).''', 
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
            description: '''Choose the DB instance type that allocates the computational, network, and memory capacity required by planned workload of this DB instance. tested with aurora-mysql-> db.t3.medium, aurora-postgreSQL-> db.t3.medium, MySQL --> db.m5d.large, postgreSQL -> db.m5d.large''', 
            filterLength: 4, 
            filterable: true, 
            randomName: 'choice-parameter-5621561799999', 
            referencedParameters: 'ACTION,CREDENTIAL,REGION,ENGINE_TYPE,ENGINE_VERSION',
            name: 'DB_INSTANCE_CLASS', 
            script: [
                $class: 'GroovyScript', 
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 'return  ""'
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
                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY  aws rds describe-orderable-db-instance-options --engine=postgres --engine-version=$ENGINE_VERSION --query=OrderableDBInstanceOptions[?SupportsClusters].DBInstanceClass --region $REGION --output text"

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
                description: '''Specify the size of storage to be allocated for the Amazon RDS instance. This value defines the storage capacity available to accommodate your data.''', 
                name: 'RDS_STORAGE', 
                omitValueField: true,
                randomName: 'choice-parameter-56313314451178633', 
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

        separator(name: "EKS-CLUSTER-PARAMETERS", sectionHeader: "EKS-cluster Parameters",			
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
                description: ' Select the specific version of the database cluster you want to use. The version choice directly influences the capabilities and performance of your RDS instance.', 
                // filterLength: 4, 
                // filterable: true, 
                randomName: 'choice-parameter-5631311156178961543', 
                referencedParameters: 'ACTION', 
                name: 'CLUSTER_VERSION', 
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
                                    return ['1.26','1.25','1.24','1.23','1.22']
                                }
                            '''
                    ]
                ]
            ],
            [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: ' Specify the instance types in either list or string format, depending on your chosen Infrastructure as Code (IAC) tool. If you are using Terraform, provide the instance types in list format, e.g., ["m5.large"]. If you are using CloudFormation, provide the instance type in string format, e.g., m5.large.', 
            name: 'EKS_NODE_GROUP_INSTANCE_TYPE', 
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
                        """ if (ACTION.equals("Create") || ACTION.equals("Modify")){
                            inputBox = "<input name='value'  class='setting-input' type='text' >"
                            return inputBox
                            }
                            
                        """ 
                ]
            ]
        ],

        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: 'Specify the minimum size of the EKS node group, up to a maximum of 3. ', 
            name: 'EKS_NODE_GROUP_MIN_SIZE', 
            omitValueField: true, 
            randomName: 'choice-parameter-5931314456174239', 
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
                        """ if (ACTION.equals("Create") || ACTION.equals("Modify")){
                            inputBox = "<input name='value'  class='setting-input' type='text' >"
                            return inputBox
                            }
                            
                        """
                ]
            ]
        ],

        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: 'Define the maximum size for the EKS node group, up to a maximum of 3. ', 
            name: 'EKS_NODE_GROUP_MAX_SIZE', 
            omitValueField: true, 
            randomName: 'choice-parameter-5531314456174239', 
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
                        """ if (ACTION.equals("Create") || ACTION.equals("Modify")){
                            inputBox = "<input name='value'  class='setting-input' type='text' >"
                            return inputBox
                            }
                            
                        """
                ]
            ]
        ],

        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: 'Define the desired size for the EKS node group, up to a maximum of 3.', 
            name: 'EKS_NODE_GROUP_DESIRED_SIZE', 
            omitValueField: true, 
            randomName: 'choice-parameter-5531310456174239', 
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
                        """ if (ACTION.equals("Create") || ACTION.equals("Modify")){
                            inputBox = "<input name='value'  class='setting-input' type='text' >"
                            return inputBox
                            }
                            
                        """
                ]
            ]
        ],
            

            [$class: 'CascadeChoiceParameter', 
                choiceType: 'PT_SINGLE_SELECT', 
                description: 'Choose the type of capacity that best suits the requirements of your EKS Node Group. This choice dictates the characteristics of the computational resources allocated to the nodes.', 
                // filterLength: 4, 
                // filterable: true, 
                randomName: 'choice-parameter-56313111561789619004', 
                referencedParameters: 'ACTION', 
                name: 'EKS_NODE_GROUP_CAPACITY_TYPE',
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
                                    return ['ON_DEMAND','SPOT']
                                }
                            '''
                    ]
                ]
            ],

           



            separator(name: "CLOUDFRONT-PARAMETERS", sectionHeader: "CloudFront Parameters",			
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
            description: 'Mention the CDN alias names for the CloudFront distributions. Provide the aliases in a list format of strings, for example: ["test.example.in"]. These aliases enable users to access your content through user-friendly domain names.', 
            name: 'CDN_ALIASES', 
            omitValueField: true, 
            randomName: 'choice-parameter-5634511156178614', 
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
            description: 'Select the certificates for the load balancer', 
            filterLength: 4, 
            filterable: true,
            name: 'CERTIFICATE_NAME', 
            randomName: 'choice-parameter-5631314418916', 
            referencedParameters: 'ACTION,CREDENTIAL,,REGION', 
            script: [
                $class: 'GroovyScript', 
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        "return['null']"
                ], 
                script: [
                    classpath: [], 
                    sandbox: true, 
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
                                if(creds.id == "$CREDENTIAL"){
                                    key = creds.secret.toString(creds.secret);
                                    def jsonSlurper = new JsonSlurper()
                                    cfg = jsonSlurper.parseText(key)
                                    
                                    AWS_ACCESS_KEY_ID = cfg['accessKeyId']  
                                    AWS_SECRET_ACCESS_KEY = cfg['secretAccessKey']

                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY  aws acm list-certificates --query=CertificateSummaryList[].DomainName --output text --region=us-east-1"
                                    
                                
                                   
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
            choiceType: 'ET_FORMATTED_HIDDEN_HTML', 
            description: 'Select the certificate for the load balancer', 
            name: 'ACM_CERTIFICATE_ARN', 
            omitValueField: true,
            randomName: 'choice-parameter-563116178635', 
            referencedParameters: 'ACTION,CREDENTIAL,REGION,CERTIFICATE_NAME', 
            script: [
                $class: 'GroovyScript',  
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script:
                    ''' 
                      "return['null']"
                    '''
                ],
                script: [
                    classpath: [], 
                    sandbox: true, 
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
                                if(creds.id == "$CREDENTIAL"){
                                    key = creds.secret.toString(creds.secret);
                                    def jsonSlurper = new JsonSlurper()
                                    cfg = jsonSlurper.parseText(key)
                                    
                                    AWS_ACCESS_KEY_ID = cfg['accessKeyId']  
                                    AWS_SECRET_ACCESS_KEY = cfg['secretAccessKey']

                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY  aws acm list-certificates --query=CertificateSummaryList[?DomainName=='$CERTIFICATE_NAME'].CertificateArn --output text --region=us-east-1"
                                    
                                
                                    
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
                                     inputBox = "<input name='value' class='setting-input' type='text' value=$output>"
                                    return inputBox

                                } 
                            }
                        }
                    '''
                ]
            ]
        ],

            separator(name: "ROUTE-53-PARAMETERS", sectionHeader: "ROUTE-53 Parameters",			
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
            choiceType: 'ET_FORMATTED_HTML', 
            description: 'Enter the ID of the existing Amazon Route 53 hosted zone. This ID uniquely identifies the hosted zone associated with your domain.', 
            name: 'HOSTED_ZONE_ID', 
            omitValueField: true, 
            randomName: 'choice-parameter-5631310156178614', 
            referencedParameters: 'ACTION', 
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
            
    ])
])

pipeline{ 
    agent any 
    options { // Specifies pipeline-specific options/behaviors
        ansiColor('xterm') // Enables ANSI color formatting in the pipeline console output using the "xterm" color scheme
    }
    environment { // Defines environment variables used by the pipeline
        resource = "aws-eks-cdn" 
        resource_name = "aws-eks-cdn" 
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
                        tf_resource_provision () // Calls a function named "tf_resource_provision()" to provision a resource (defined in the jenkins shared library)
                        git_commit() // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
                        }
                    }
                    else {
                        withAWS(region:"$REGION"){
                        cft_resource_provision ('aws-eks-cdn.j2') // Calls a function named "cft_resource_provision()" here we need to provide the "jinja2" file name to provision a resource (defined in the jenkins shared library)                     
                        cft_git_commit () // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
                        }
                    }
                }
           }
        }
    }
}
