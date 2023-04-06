#!groovy
import groovy.json.JsonSlurperClassic
node {

    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def HUB_ORG="pdev@sf.com"
    def SFDC_HOST ="https://login.salesforce.com"
    def JWT_KEY_CRED_ID = "3d46dce5-02ff-4c66-a0db-1d392a6b1182"
    def JWT_KEY_FILE= env.JWT_FILE ?:env.JWT_PATH
    def DEPLOYDIR='src'
    def TEST_LEVEL='RunLocalTests'



    def CONNECTED_APP_CONSUMER_KEY="3MVG9vvlaB0y1YsJEx2GTDHujtGeAKj6n7TyOlU1EuPwwKdwAbLu0Ai_6qTXypf9N5M_T4GWsyQuvM5633wnb"

    println 'KEY IS' 
    println JWT_KEY_CRED_ID
    println HUB_ORG
    println SFDC_HOST
    println CONNECTED_APP_CONSUMER_KEY
    println JWT_KEY_FILE
    //printf $JWT_KEY_CRED_ID > code.key
    def toolbelt = tool 'toolbelt'
    //def sfdx = tool 'sfdxtool'
    stage('checkout source') {
        checkout scm
    }

    withEnv(["HOME=${env.WORKSPACE}"]) {

    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'JWT_KEY_FILE')]) {        
        stage('SFDX Login') {
        //rcl = bat returnStatus: true, script: "\"${toolbelt}\\sfdx\" force:auth:logout --username ${HUB_ORG}"
        //rc = sh returnStatus: true, script: "\"${toolbelt}\\sfdx\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
          println 'COMMAND' 
          rc = command "${toolbelt}/sfdx force:auth:jwt:grant --instanceurl ${SFDC_HOST} --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${JWT_KEY_DEVORG} --setdefaultdevhubusername"
        if (rc != 0) { error 'hub org authorization failed' 
        }
            
        }



     }
    
}
}
