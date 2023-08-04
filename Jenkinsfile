#!groovy
import groovy.json.JsonSlurperClassic
node {

    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def SF_USERNAME='a.gandhi@salesforce.com.developer'
    def SF_INSTANCE_URL = 'https://test.salesforce.com'
    def server_key_file = '47e939a7-7bd0-464b-9d1c-3fed5f3b960e'
    def SF_CONSUMER_KEY= '3MVG9jnZi5GMCfE5r1R69zSIvfdMm8NyQ7c8ouJNp6lRHBKyZiguaVhF3K2iWp8HvYil74kgKhxEfBeFvNNNS'
    def ORG_ALIAS = 'SNZ-DEV-Jenkins'

    println 'KEY IS' 
    println server_key_file
    println SF_USERNAME
    println SF_INSTANCE_URL
    println SF_CONSUMER_KEY
    println ORG_ALIAS
    def toolbelt = tool 'toolbelt'

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
            rc = command "/usr/local/bin/sfdx force:auth:jwt:grant --instanceurl ${SF_INSTANCE_URL} --clientid ${SF_CONSUMER_KEY} --username ${SF_USERNAME} --jwtkeyfile ${server_key_file} --setdefaultdevhubusername --setalias "+ORG_ALIAS
            if (rc != 0) {
                error 'Salesforce org authorization failed.'
            }
        }
        stage('Deploye Code') {
            /*if (isUnix()) {
                rc = sh returnStatus: true, script: "${toolbelt} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --instanceurl ${SFDC_HOST}"
            }else{
                 rc = bat returnStatus: true, script: "\"${toolbelt}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key_file}\" --instanceurl ${SFDC_HOST}"
            }
            if (rc != 0) { error 'hub org authorization failed' }

			println rc
			
			// need to pull out assigned username
			/*if (isUnix()) {
				rmsg = sh returnStdout: true, script: "${toolbelt} force:mdapi:deploy -d manifest/. -u ${HUB_ORG}"
			}else{
			   rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:mdapi:deploy -d manifest/. -u ${HUB_ORG}"
			}*/
			  
            //printf rmsg
            println('Hello from a Job DSL script!')
            //println(rmsg)
        }
    }
}
