   properties([
         parameters([
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
                   '''
                   if (IAC_TOOL == "Terraform") {
                   return ['Create', 'Modify', 'Delete']
                 }
                 else {
                   return ['Create', 'Delete']
                 }
                 '''
               ]
             ]
           ],

           // ChoiceParameter is also used to select a credential to use to provision the resources on the AWS account.
           // The script starts by getting all the credentials available in Jenkins and gets the the client_name frow the current_user functon. Then it loops on the credentials to get onl the credentials which starts with the particular client_name.
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
           ],
           [$class: 'CascadeChoiceParameter',
             choiceType: 'PT_SINGLE_SELECT',
             description: 'Choose the distribution type - S3 or Load Balancer',
             filterLength: 4,
             filterable: true,
             randomName: 'choice-parameter-56313111561789619',
             referencedParameters: 'ACTION',
             name: 'DISTRIBUTION_TYPE',
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
                 if (ACTION.equals("Create") || ACTION.equals("Modify")){
                 return ['S3', 'Loadbalancer']
               }
               '''
             ]
           ]
         ],
         string(
           defaultValue: '',
           name: 'CDN_NAME',
           trim: true,
           description: 'Name for the CDN Distribution'
         ),
         [$class: 'CascadeChoiceParameter',
           choiceType: 'PT_SINGLE_SELECT',
           description: 'Set to "true" to use a custom domain (CNAME) for the CloudFront distribution',
           filterLength: 4,
           filterable: true,
           randomName: 'choice-parameter-56313111561789289',
           referencedParameters: 'ACTION',
           name: 'USE_CUSTOM_DOMAIN',
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
               if (ACTION.equals("Create") || ACTION.equals("Modify")){
               return ['true', 'false']
             }
             '''
           ]
         ]
       ],

       [$class: 'CascadeChoiceParameter',
         choiceType: 'PT_SINGLE_SELECT',
         description: 'Origin Shield can help reduce the load on your origin.',
         filterLength: 4,
         filterable: true,
         randomName: 'choice-parameter-56313111561389389',
         referencedParameters: 'ACTION',
         name: 'ORIGIN_SHIELD',
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
             if (ACTION.equals("Create") || ACTION.equals("Modify")){
             return ['true', 'false']
           }
           '''
         ]
       ]
     ],

     [$class: 'DynamicReferenceParameter',
       choiceType: 'ET_FORMATTED_HTML',
       description: 'A path that CloudFront appends to the origin domain name when CloudFront requests content from the origin',
       name: 'ORIGIN_PATH',
       omitValueField: true,
       randomName: 'choice-parameter-5631322231178619',
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
           '''
           if (ACTION.equals("Create") || ACTION.equals("Modify")){
           inputBox = "<input name='value' class='setting-input'  type='text'>"
           return inputBox
         }
         '''
       ]
     ]],

   [$class: 'DynamicReferenceParameter',
     choiceType: 'ET_FORMATTED_HTML',
     description: 'The comment for the CloudFront Origin Access Identity (OAc).',
     name: 'OAC_NAME',
     omitValueField: true,
     randomName: 'choice-parameter-56313298383731178619',
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
         '''
         if (ACTION.equals("Create") || ACTION.equals("Modify")){
         inputBox = "<input name='value' class='setting-input'  type='text'>"
         return inputBox
       }
       '''
     ]
   ]
   ],
   [$class: 'CascadeChoiceParameter',
     choiceType: 'PT_SINGLE_SELECT',
     description: 'The price class for this distribution. One of PriceClass_All, PriceClass_200, PriceClass_100',
     filterLength: 4,
     filterable: true,
     randomName: 'choice-parameter-56313234561389389',
     referencedParameters: 'ACTION',
     name: 'PRICE_CLASS',
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
         if (ACTION.equals("Create") || ACTION.equals("Modify")){
         return ['PriceClass_All', 'PriceClass_200', 'PriceClass_100']
       }
       '''
     ]
   ]
   ],
   [$class: 'CascadeChoiceParameter',
     choiceType: 'PT_SINGLE_SELECT',
     description: 'Specifies the protocol (HTTP or HTTPS) that CloudFront uses to connect to the origin',
     filterLength: 4,
     filterable: true,
     randomName: 'choice-parameter-56313111561789229',
     referencedParameters: 'DISTRIBUTION_TYPE, ACTION',
     name: 'ORIGIN_PROTOCOL_POLICY',
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
         if (DISTRIBUTION_TYPE == "Loadbalancer" && ACTION == "Create" || DISTRIBUTION_TYPE == "Loadbalancer" && ACTION == "Modify") {
           return ["http-only", "https-only", "match-viewer"]
         } else {
           return [""]
         }
         '''
       ]
     ]
   ],
   [$class: 'CascadeChoiceParameter',
     choiceType: 'PT_SINGLE_SELECT',
     description: 'Choose the protocol policy that you want viewers to use to access your content in CloudFront edge locations',
     filterLength: 4,
     filterable: true,
     randomName: 'choice-parameter-56313246561389389',
     referencedParameters: 'ACTION',
     name: 'VIEWER_PROTOCOL_POLICY',
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
         if (ACTION.equals("Create") || ACTION.equals("Modify")){
         return ['allow-all', 'https-only', 'redirect-to-https']
       }
       '''
     ]
   ]
   ],
   [$class: 'CascadeChoiceParameter',
     choiceType: 'PT_SINGLE_SELECT',
     description: 'controls which HTTP methods CloudFront processes and forwards to your Amazon S3 bucket or your custom origin',
     filterLength: 4,
     filterable: true,
     randomName: 'choice-parameter-563132398761389389',
     referencedParameters: 'ACTION',
     name: 'ALLOWED_HTTP_METHODS',
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
         if (ACTION.equals("Create") || ACTION.equals("Modify")){
         return ['GET,HEAD', 'GET,HEAD,OPTIONS', 'GET,HEAD,OPTIONS,PUT,PATCH,POST,DELETE']
       }
       '''
     ]
   ]
   ],

   [$class: 'CascadeChoiceParameter',
     choiceType: 'PT_SINGLE_SELECT',
     description: 'Whether you want CloudFront to automatically compress certain files for this cache behavior. If so, specify true; if not, specify false',
     filterLength: 4,
     filterable: true,
     randomName: 'choice-parameter-563132465844987389389',
     referencedParameters: 'ACTION',
     name: 'COMPRESS',
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
         if (ACTION.equals("Create") || ACTION.equals("Modify")){
         return ['true', 'false']
       }
       '''
     ]
   ]
   ],

   [$class: 'CascadeChoiceParameter',
     choiceType: 'PT_SINGLE_SELECT',
     description: 'If you want CloudFront to respond to IPv6 DNS requests with an IPv6 address for your distribution, specify true. If you specify false, CloudFront responds to IPv6 DNS requests with the DNS response code NOERROR and with no IP addresses.',
     filterLength: 4,
     filterable: true,
     randomName: 'choice-parameter-56313246584765389389',
     referencedParameters: 'ACTION',
     name: 'IPV6',
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
         if (ACTION.equals("Create") || ACTION.equals("Modify")){
         return ['true', 'false']
       }
       '''
     ]
   ]
   ],
   [$class: 'CascadeChoiceParameter',
     choiceType: 'PT_SINGLE_SELECT',
     description: 'The name of your S3 bucket (only needed for S3 distribution)',
     name: 'BUCKET_NAME',
     randomName: 'choice-parameter-5631311156178619',
     referencedParameters: 'ACTION,DISTRIBUTION_TYPE,REGION,CREDENTIAL',
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
           // Looks up Jenkins credentials
           def jenkinsCredentials = com.cloudbees.plugins.credentials.CredentialsProvider.lookupCredentials(
             com.cloudbees.plugins.credentials.Credentials.class,
             Jenkins.instance,
             null,
             null
           );

           def AWS_ACCESS_KEY_ID
           def AWS_SECRET_ACCESS_KEY
           def key
           // Iterates over the Jenkins credentials.
           for (creds in jenkinsCredentials) {
             // Checks if the credential ID matches the referenced parameter CREDENTIAL.
             if (creds.id == "$CREDENTIAL") {
               // Retrieves the credential secret as a string.
               key = creds.secret.toString(creds.secret);
               // Creates a new instance of the JSON Slurper.
               def jsonSlurper = new JsonSlurper()
               // Parses the credential secret as JSON and assigns it to the cfg variable.
               cfg = jsonSlurper.parseText(key)

               AWS_ACCESS_KEY_ID = cfg['accessKeyId']
               AWS_SECRET_ACCESS_KEY = cfg['secretAccessKey']
               if (DISTRIBUTION_TYPE.equals("S3")) {
                 // Executes the AWS CLI command to list all vpc in a particular region
                 command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws s3api list-buckets --query Buckets[].Name --output text --region $REGION"
               } else {
                 return ""
               }
               def proc = command.execute()
               proc.waitFor()
               def output = proc.in.text
               def exitcode = proc.exitValue()
               def error = proc.err.text
               // If there's an error, print it and return the exit code
               if (error) {
                 println "Std Err: ${error}"
                 println "Process exit code: ${exitcode}"
                 return exitcode
               }
               // The output is tokenized and returned lst variable. If there's any error, the script prints the error message and returns the exit code.
               def lst = output.tokenize()
               // the "null" will be appended to the lst values
               //lst.add("null")
               return lst
               // return output.tokenize() 
             }
           }
         }
         '''
         
       ]
     ]
   ],
   [$class: 'CascadeChoiceParameter',
     choiceType: 'PT_SINGLE_SELECT',
     description: 'Specify the Name of the Loadbalancer ',
     filterLength: 4,
     filterable: true,
     name: 'LOADBALANCER_NAME',
     randomName: 'choice-parameter-5686535314418916',
     referencedParameters: 'ACTION,CREDENTIAL,DISTRIBUTION_TYPE,REGION',
     script: [
       $class: 'GroovyScript',
       fallbackScript: [
         classpath: [],
         sandbox: true,
         script:
         "return['']"
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
             if (creds.id == "$CREDENTIAL") {
               key = creds.secret.toString(creds.secret);
               def jsonSlurper = new JsonSlurper()
               cfg = jsonSlurper.parseText(key)

               AWS_ACCESS_KEY_ID = cfg['accessKeyId']
               AWS_SECRET_ACCESS_KEY = cfg['secretAccessKey']
               def command
               if (DISTRIBUTION_TYPE.equals("Loadbalancer")) {
                 command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws elbv2 describe-load-balancers --query LoadBalancers[*].LoadBalancerName --region $REGION --output text"
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
     description: 'ACM ARN to secure HTTPS protocol',
     name: 'LOADBALANCER_DOMAINNAME',
     omitValueField: true,
     randomName: 'choice-parameter-563116847478635',
     referencedParameters: 'ACTION,CREDENTIAL,REGION,LOADBALANCER_NAME',
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
             if (creds.id == "$CREDENTIAL") {
               key = creds.secret.toString(creds.secret);
               def jsonSlurper = new JsonSlurper()
               cfg = jsonSlurper.parseText(key)

               AWS_ACCESS_KEY_ID = cfg['accessKeyId']
               AWS_SECRET_ACCESS_KEY = cfg['secretAccessKey']

               def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY aws elbv2 describe-load-balancers --query LoadBalancers[?LoadBalancerName=='$LOADBALANCER_NAME'].DNSName --region $REGION --output text"

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
     description: 'Specify the maximum HTTP version(s) that you want viewers to use to communicate with CloudFront. The default value for new distributions is http1.1',
     filterLength: 4,
     filterable: true,
     randomName: 'choice-parameter-56313246897765389389',
     referencedParameters: 'ACTION',
     name: 'HTTP_VERSION',
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
         if (ACTION.equals("Create") || ACTION.equals("Modify")){
         return ['http1.1', 'http2', 'http2and3', 'http3']
       }
       '''
     ]
   ]
   ],

   [$class: 'DynamicReferenceParameter',
     choiceType: 'ET_FORMATTED_HTML',
     description: 'The object that you want CloudFront to request from your origin (for example, index.html)',
     name: 'DEFAULT_ROOT_OBJECT',
     omitValueField: true,
     randomName: 'choice-parameter-563109383731178619',
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
         '''
         if (ACTION.equals("Create") || ACTION.equals("Modify")){
         inputBox = "<input name='value' class='setting-input'  type='text'>"
         return inputBox
       }
       '''
     ]
   ]
   ],

   [$class: 'CascadeChoiceParameter',
     choiceType: 'PT_SINGLE_SELECT',
     description: 'ACM CERTIFICATE to secure HTTPS protocol ',
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
             if (creds.id == "$CREDENTIAL") {
               key = creds.secret.toString(creds.secret);
               def jsonSlurper = new JsonSlurper()
               cfg = jsonSlurper.parseText(key)

               AWS_ACCESS_KEY_ID = cfg['accessKeyId']
               AWS_SECRET_ACCESS_KEY = cfg['secretAccessKey']

               def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY  aws acm list-certificates --query=CertificateSummaryList[].DomainName --output text --region=us-east-1"

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
     description: 'ACM ARN to secure HTTPS protocol',
     name: 'ACM_CERTIFICATE_ARN',
     omitValueField: true,
     randomName: 'choice-parameter-56311736648635',
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
             if (creds.id == "$CREDENTIAL") {
               key = creds.secret.toString(creds.secret);
               def jsonSlurper = new JsonSlurper()
               cfg = jsonSlurper.parseText(key)

               AWS_ACCESS_KEY_ID = cfg['accessKeyId']
               AWS_SECRET_ACCESS_KEY = cfg['secretAccessKey']

               def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY  aws acm list-certificates --query=CertificateSummaryList[?DomainName=='$CERTIFICATE_NAME'].CertificateArn --output text --region=us-east-1"

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

   [$class: 'DynamicReferenceParameter',
     choiceType: 'ET_FORMATTED_HTML',
     description: 'Extra CNAMEs (alternate domain names), if any, for this distribution',
     name: 'ALIASES',
     omitValueField: true,
     randomName: 'choice-parameter-563132909818273731178619',
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
         '''
         if (ACTION.equals("Create") || ACTION.equals("Modify")){
         inputBox = "<input name='value' class='setting-input'  type='text'>"
         return inputBox
       }
       '''
       
     ]
   ]
   ],
   [$class: 'CascadeChoiceParameter',
     choiceType: 'PT_SINGLE_SELECT',
     description: 'A unique identifier that specifies the AWS WAF web ACL, if any, to associate with this distribution',
     filterLength: 4,
     filterable: true,
     name: 'WEBACL_NAME',
     randomName: 'choice-parameter-563158694418916',
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
             if (creds.id == "$CREDENTIAL") {
               key = creds.secret.toString(creds.secret);
               def jsonSlurper = new JsonSlurper()
               cfg = jsonSlurper.parseText(key)

               AWS_ACCESS_KEY_ID = cfg['accessKeyId']
               AWS_SECRET_ACCESS_KEY = cfg['secretAccessKey']

               def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY  aws wafv2 list-web-acls --scope=CLOUDFRONT --region=$REGION --query WebACLs[].Name --output=text"

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
     description: 'ACM ARN to secure HTTPS protocol',
     name: 'WEBACL_ID',
     omitValueField: true,
     randomName: 'choice-parameter-563116178635',
     referencedParameters: 'ACTION,CREDENTIAL,REGION,WEBACL_NAME',
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
             if (creds.id == "$CREDENTIAL") {
               key = creds.secret.toString(creds.secret);
               def jsonSlurper = new JsonSlurper()
               cfg = jsonSlurper.parseText(key)

               AWS_ACCESS_KEY_ID = cfg['accessKeyId']
               AWS_SECRET_ACCESS_KEY = cfg['secretAccessKey']

               def command = "env AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY  aws wafv2 list-web-acls --scope=CLOUDFRONT --region=$REGION --query WebACLs[?Name=='$WEBACL_NAME'].ARN --output=text"

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
   [$class: 'DynamicReferenceParameter',
     choiceType: 'ET_FORMATTED_HTML',
     description: 'A comment to describe the distribution. The comment cannot be longer than 128 characters. ',
     name: 'DESCRIPTION',
     omitValueField: true,
     randomName: 'choice-parameter-5631311231178619',
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
         '''
         if (ACTION.equals("Create") || ACTION.equals("Modify")){
         inputBox = "<input name='value' class='setting-input'  type='text'>"
         return inputBox
       }
       '''
     ]
   ]
   ],

   string(
   defaultValue: '',
   name: 'ENVIRONMENT',
   trim: true,
   description: 'The name of the environment in which resources needs to be provisioned.'
   ),

   ]
   )
   ]
   )
   pipeline {
     // Use any available agent to run the pipeline
     agent any
     // Configure pipeline options, in this case enabling ANSI color support
     options {
       ansiColor('xterm')
     }
     // Define environment variables for use throughout the pipeline
     environment {
       // Set the resource type to Bucket
       resource = "cloudfront"
       resource_name = "${env.CDN_NAME}"
       // Set the AWS region to the value of the REGION environment variable with commas removed
       aws_region = "${env.REGION}".replace(',', '');
       // Parse the CLIENT_NAME and ACCOUNT_ID from the CREDENTIAL parameter using the shell command 'cut'
       PROJECT_NAME = sh(returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 1").trim()
       ACCOUNT_ID = sh(returnStdout: true, script: "echo ${params.CREDENTIAL} | cut -d'_' -f 2").trim()
     }

     stages {
       // Define a stage to clean up the workspace before running the pipeline
       stage("Workspace cleanup") {
         steps {
           script {
             if (params.CDN_NAME == '') {
               // If so, set the display name of the current build to indicate that the parameters are being loaded, mark the build as aborted, and throw an error
               currentBuild.displayName = "Loading parameters"
               currentBuild.result = 'ABORTED'
               error('The parameter value is null')
             }
             // Set the display name and description of the current build to indicate which Bucket is being acted upon
             currentBuild.displayName = "${ACTION} the CLOUDFRONT ${params.CDN_NAME} - ${params.IAC_TOOL}"
             currentBuild.description = "${ACTION} the CLOUDFRONT ${params.CDN_NAME} - ${params.IAC_TOOL}"
           }
           // Call a function (defined in the Jenkins shared library) to clean up the workspace
           wrkspace_cleanup()
         }
       }
       stage("SCM Checkout") {
         steps {
           script { // Allows for execution of arbitrary Groovy code within the pipeline
             if (env.IAC_TOOL == "Terraform") {
               tf_scm_checkout() // Calls a function named "tf_scm_checkout()" to perform an SCM checkout (defined in the jenkins shared library)
             } else {
               cft_scm_checkout() // Calls a function named "tf_scm_checkout()" to perform an SCM checkout (defined in the jenkins shared library)

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
                 cft_resource_provision('cloudfront.j2') // Calls a function named "cft_resource_provision()" here we need to provide the "jinja2" file name to provision a resource (defined in the jenkins shared library)                     
                 cft_git_commit() // Calls a function named "git_commit()" to commit changes to a Git repository (defined in the jenkins shared library)
               }
             }
           }
         }
       }
     }
   }
