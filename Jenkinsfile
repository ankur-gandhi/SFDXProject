#!groovy
import groovy.json.JsonSlurperClassic
node {

    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SF_USERNAME='a.gandhi@playful-narwhal-v5gbyo.com'
    def SF_INSTANCE_URL = 'https://playful-narwhal-v5gbyo-dev-ed.my.salesforce.com'
    def server_key_file = '3469184a-b3d7-436c-ad0d-86b9ffe96e6a'
    def SF_CONSUMER_KEY= '3MVG9wt4IL4O5wvIOhUs.8yhyCQiTRs.Y5M4Egwxn8Ep23d2boKVR4TsPM6RJxfIuDD.qepAE59p6cU7spXpZ'
    def ORG_ALIAS = 'SNZ-DEV-Jenkins'
    def toolbelt = tool 'toolbelt'
    def SFDX_PATH = '/usr/local/bin'

    stage('checkout source') {
        // when running in multi-branch job, one must issue this command
        checkout scm
    }

    withCredentials([file(credentialsId: server_key_file, variable: 'jwt_key_file')]) {
        stage('Connect to ORG'){
            // -------------------------------------------------------------------------
            // Handle Error by performing a logout, just before the login
            //
            // ERROR running force:source:deploy:  We encountered a JSON web token error, which is 
            // likely not an issue with Salesforce CLI. Here’s the error: ENOENT: no such file or 
            // directory, open '/data/jenkins/workspace/xxxx@tmp/secretFiles/xxx/server.key''
            // -------------------------------------------------------------------------
            
            /*rc = command "\$(echo y | /usr/local/bin/sfdx force:auth:logout -u "+SF_USERNAME+" -p)"
            if (rc != 0) {
                println 'Salesforce logout  failed.'
            }*/

            // -------------------------------------------------------------------------
            // Perform Login
            // -------------------------------------------------------------------------
            rc = sh returnStatus: true, script: "\"${SFDX_PATH}/sfdx\" auth:jwt:grant --clientid ${SF_CONSUMER_KEY} --username ${SF_USERNAME} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl ${SF_INSTANCE_URL}"
            println rc
            if (rc != 0) {
                error 'Salesforce org authorization failed.'
            }
        }
        stage('Deploye Code') {
            // need to pull out assigned username
			if (isUnix()) {
				rmsg = sh returnStdout: true, script: "${SFDX_PATH}/sfdx force:source:deploy -d manifest/package.xml -u ${SF_USERNAME}"
			}else{
			   rmsg = bat returnStdout: true, script: "\"${SFDX_PATH}/sfdx\" force:source:deploy -d manifest/package.xml -u ${SF_USERNAME}"
			}
			  
            //printf rmsg
            println('Hello from a Job DSL script!')
            //println(rmsg)
        }
    }
}
