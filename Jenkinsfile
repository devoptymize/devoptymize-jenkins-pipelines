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
            name: 'VPC_PRODUCTION_CIDR', 
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
            description: 'Specify the range of IP addresses for your VPC in CIDR notation. For example, if you want to allocate IP addresses from 192.168.0.0 to 192.168.255.255, you would use "192.168.0.0/16" as the CIDR range.', 
            name: 'VPC_MANAGEMENT_CIDR', 
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
            description: 'Specify the range of IP addresses for your VPC in CIDR notation. For example, if you want to allocate IP addresses from 172.16.0.0 to 172.16.255.255, you would use "172.16.0.0/16" as the CIDR range.', 
            name: 'VPC_DEVELOPMENT_CIDR', 
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
            description: 'Customize the CIDR ranges for your public subnets using a list format. To define multiple private subnets, provide a comma-separated list of CIDR ranges enclosed in square brackets. For instance: ["10.0.0.0/24", "10.0.1.0/24", "10.0.2.0/24"]. ', 
            name: 'PRODUCTION_PUBLIC_CIDR', 
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
            description: 'Customize the CIDR ranges for your private subnets using a list format. To define multiple private subnets, provide a comma-separated list of CIDR ranges enclosed in square brackets. For instance: ["10.0.0.0/24", "10.0.1.0/24", "10.0.2.0/24"]. ', 
            name: 'PRODUCTION_PRIVATE_CIDR', 
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

        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: 'Customize the CIDR ranges for your isolated subnets using a list format. To define multiple private subnets, provide a comma-separated list of CIDR ranges enclosed in square brackets. For instance: ["10.0.0.0/24", "10.0.1.0/24", "10.0.2.0/24"]. ', 
            name: 'PRODUCTION_ISOLATED_CIDR', 
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
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: 'Tailor the CIDR ranges for  subnets using a list format. To define multiple subnets, provide a comma-separated list of CIDR ranges enclosed in square brackets. For example: ["192.168.1.0/24", "192.168.3.0/24"].', 
            name: 'MANAGEMENT_PUBLIC_CIDR', 
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
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: 'Tailor the CIDR ranges for  subnets using a list format. To define multiple subnets, provide a comma-separated list of CIDR ranges enclosed in square brackets. For example: ["192.168.1.0/24", "192.168.3.0/24"].', 
            name: 'MANAGEMENT_PRIVATE_CIDR', 
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
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: 'Tailor the CIDR ranges for  subnets using a list format. To define multiple subnets, provide a comma-separated list of CIDR ranges enclosed in square brackets. For example:["172.16.1.0/24", "172.16.3.0/24"].', 
            name: 'DEVELOPMENT_PUBLIC_CIDR', 
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
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: 'Tailor the CIDR ranges for  subnets using a list format. To define multiple subnets, provide a comma-separated list of CIDR ranges enclosed in square brackets. For example:["172.16.1.0/24", "172.16.3.0/24"].', 
            name: 'DEVELOPMENT_PRIVATE_CIDR', 
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
        [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: 'Tailor the CIDR ranges for  subnets using a list format. To define multiple subnets, provide a comma-separated list of CIDR ranges enclosed in square brackets. For example:["172.16.1.0/24", "172.16.3.0/24"].',
            name: 'DEVELOPMENT_ISOLATED_CIDR', 
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
            [$class: 'DynamicReferenceParameter', 
            choiceType: 'ET_FORMATTED_HTML', 
            description: 'Client CIDR block to connect with VPN ',
            name: 'CLIENTCIDRBLOCK', 
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


         [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: 'The Amazon Resource Name (ARN) of the server certificate',  
            filterLength: 4, 
            filterable: true,
            name: 'SERVER_CERTIFICATE', 
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

                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY  aws acm list-certificates --query=CertificateSummaryList[].DomainName --output text --region=$REGION"
                                    
                                
                                   
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
            description: 'The Amazon Resource Name (ARN) of the server certificate', 
            name: 'SERVER_CERTIFICATION', 
            omitValueField: true,
            randomName: 'choice-parameter-563116178635', 
            referencedParameters: 'ACTION,CREDENTIAL,REGION,SERVER_CERTIFICATE', 
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

                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY  aws acm list-certificates --query=CertificateSummaryList[?DomainName=='$SERVER_CERTIFICATE'].CertificateArn --output text --region=$REGION"
                                    
                                
                                    
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
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_SINGLE_SELECT', 
            description: 'The Amazon Resource Name (ARN) of the clients root certificate chain',  
            filterLength: 4, 
            filterable: true,
            name: 'CLIENT_CERTIFICATE', 
            randomName: 'choice-parameter-56313144189164', 
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

                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY  aws acm list-certificates --query=CertificateSummaryList[].DomainName --output text --region=$REGION"
                                    
                                
                                   
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
            description: 'The Amazon Resource Name (ARN) of the clients root certificate chain', 
            name: 'CLIENTROOTCERTIFICATE', 
            omitValueField: true,
            randomName: 'choice-parameter-563116178633', 
            referencedParameters: 'ACTION,CREDENTIAL,REGION,CLIENT_CERTIFICATE', 
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

                                    def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY  aws acm list-certificates --query=CertificateSummaryList[?DomainName=='$CLIENT_CERTIFICATE'].CertificateArn --output text --region=$REGION"
                                    
                                
                                    
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
        [$class: 'CascadeChoiceParameter', 
            choiceType: 'PT_CHECKBOX', 
            description: 'The aws_configrules variable allows you to specify a list of AWS Config rules or configurations within your infrastructure code.',  
            name: 'AWS_CONFIGRULES', 
            randomName: 'choice-parameter-5631314417619', 
            referencedParameters: 'ACTION,CREDENTIAL', 
            script: [
                $class: 'GroovyScript', 
                fallbackScript: [
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        'return ""'
                ], 
                 script: [ // Defines the main script to be executed (this script generates different HTML input fields based on the value of the "ACTION" parameter)
                    classpath: [], 
                    sandbox: true, 
                    script: 
                        ''' if (ACTION.equals("Create")){
                                return ['iam','s3','controltower','nist','pci']
                                }
                            else if (ACTION.equals("Modify") ){
                                return ['iam','s3','controltower','nist','pci']
                                }
                            else if (ACTION.equals("Delete") ){
                                return ['iam','s3','controltower','nist','pci']
                                }
                        '''
                 ]
            ]
        ]
 

    ])
])
pipeline{ 
    agent any 
    options { // Specifies pipeline-specific options/behaviors
        ansiColor('xterm') // Enables ANSI color formatting in the pipeline console output using the "xterm" color scheme
    }
    environment { // Defines environment variables used by the pipeline
        resource = "aws-fintech" 
        resource_name = "aws-fintech" 
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
                        cft_resource_provision ('aws-fintech.j2') // Calls a function named "cft_resource_provision()" here we need to provide the "jinja2" file name to provision a resource (defined in the jenkins shared library)                 
                      if (params.AWS_CONFIGRULES && params.ACTION.equals("Create")) {
                        def parts = params.AWS_CONFIGRULES.split(',')
                        // Print each part
                        parts.each { part ->
                            println(part)
                            def file= "configpack."+part+".bestpractices.yaml"
                            println(file)
                            sh "pwd"
                            sh "cd  $WORKSPACE/cloudformation/child/services/aws-fintech/   "
                            sh """
                            cd $WORKSPACE/cloudformation/child/services/aws-fintech/ 
                            pwd
                            cat "configpack."$part".bestpractices.yaml"
                            aws cloudformation create-stack --stack-name ${params.STACK_NAME}-$part --template-body file://$file
                            """
                        }
                      }
                      else if (params.AWS_CONFIGRULES && params.ACTION.equals("Delete")) {
                        def parts = params.AWS_CONFIGRULES.split(',')
                        // Print each part
                        parts.each { part ->
                            println(part)
                            def file= "configpack."+part+".bestpractices.yaml"
                            println(file)
                            sh "pwd"
                            sh "cd  $WORKSPACE/cloudformation/child/services/aws-fintech/   "
                            sh """
                            cd $WORKSPACE/cloudformation/child/services/aws-fintech/ 
                            pwd
                            cat "configpack."$part".bestpractices.yaml"
                            aws cloudformation delete-stack --stack-name ${params.STACK_NAME}-$part 
                            """
                        }
                      }
                        cft_git_commit () // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
                        }
                    }
                }
           }
        }
    }
}
