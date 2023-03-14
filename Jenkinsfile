import groovy.json.JsonSlurper
node
{
def BUILD_NUMBER= env.BUILD_NUMBER
def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
def SFDC_USERNAME
def HUB_ORG=env.HUB_ORG_DH
def SFDC_HOST=env.SFDC_HOST_DH
def JWT_KEY_CRED_ID=env.JWT_CRED_ID_DH
def CONNECTED_APP_CONSUMER_KEY=env.CONNECTED_APP_CONSUMER_KEY_DH
def toolbelt= tool 'toolbelt'
 
println 'All environment variables to be printed here..'
println BUILD_NUMBER
println RUN_ARTIFACT_DIR
println SFDC_USERNAME
println HUB_ORG
println SFDC_HOST
println JWT_KEY_CRED_ID
println CONNECTED_APP_CONSUMER_KEY
 
stage('checkout source'){
            checkout scm
   print "Checkout done successfully..."
}
 
withCredentials([file(credentialsId:JWT_KEY_CRED_ID, variable:'jwt_key_file')])
{
print "JWT Key Credential Id Fetched successfully"
print "--before--"
def jwt_key_file = 'server.key'
println jwt_key_file
stage('Create Scratch Org') {
   print "Connecting to Salesforce.."
   rc = command("sfdx force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile  ${jwt_key_file} --setdefaultdevhubusername --setalias HubOrg --instanceurl ${SFDC_HOST}");
   print rc
   //if (rc != 0) { error 'hub org authorization failed' }
 
   rmsg = command("sfdx force:org:create --targetdevhubusername HubOrg -f config/project-scratch-def.json -a ebikes --setdefaultusername");
   /*printf rmsg
   printf 
   def jsonSlurper = new JsonSlurper()
   def robj = jsonSlurper.parseText(rmsg)
   if (robj.status!= 0) { error 'org creation failed: ' + robj.message }
   SFDC_USERNAME=robj.result.username
   robj = null
   print "scratch org created with username ${SFDC_USERNAME}"*/
   
}
stage('Push To Test Org') {
   rc = command("sfdx force:source:push --targetusername ebikes");
   
   if (rc != 0) {
              error 'push all failed'
   }
   print "push all completed"
   // assign permset
   rc = command("sfdx force:user:permset:assign --targetusername ebikes --permsetname ebikes");
   if (rc != 0) {
            error 'push permission set failed'
   }
   print "push permission set completed"
}
 
/*stage('Run Apex Test') {
   sh "mkdir -p ${RUN_ARTIFACT_DIR}"
   timeout(time: 120, unit: 'SECONDS') {
          rc = sh returnStatus: true, script: "${toolbelt}/sfdx force:apex:test:run --testlevel RunLocalTests --outputdir ${RUN_ARTIFACT_DIR} --resultformat json --targetusername ${SFDC_USERNAME}"
            if (rc != 0) {
                        error 'apex test run failed'
            }
   }
   print "apex test run completed"
}
 
stage('Collect Results') {
        junit keepLongStdio: true, testResults: 'tests/
   print "Test results collected successfully"
}
 
stage('Delete Test Org') {
        timeout(time: 120, unit: 'SECONDS') {
            rc = sh returnStatus: true, script: "${toolbelt}/sfdx force:org:delete --targetusername ${SFDC_USERNAME} --noprompt"
            if (rc != 0) {
                error 'org deletion request failed'
            }
        }
        print "Org delete request completed"
}*/
}
}
def command(script) {
    if (isUnix()) {
        return sh(returnStatus: true, script: script);
    } else {
        return bat(returnStatus: true, script: script);
    }
}
