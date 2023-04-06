#!groovy
import groovy.json.JsonSlurperClassic
node {

    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def HUB_ORG="pdev@sf.com"
    def SFDC_HOST ="https://login.salesforce.com"
    def JWT_KEY_CRED_ID = env.JWT_KEY_CRED_ID_DH
    println JWT_KEY_CRED_ID
    def TEST_LEVEL='RunLocalTests'



    def CONNECTED_APP_CONSUMER_KEY= env.CONNECTED_APP_CONSUMER_KEY_DH

    println 'KEY IS' 
    println JWT_KEY_CRED_ID
    println HUB_ORG
    println SFDC_HOST
    println CONNECTED_APP_CONSUMER_KEY

    
    //printf JWT_KEY_FILE > server.key
    def toolbelt = tool 'toolbelt'
    //def sfdx = tool 'sfdxtool'
    stage('checkout source') {
        checkout scm
    }

    withEnv(["HOME=${env.WORKSPACE}"]) {
     withCredentials([file(credentialsId:JWT_KEY_CRED_ID,variable:'jwt_key_file')]){
        stage('SFDX Login') {
        //rcl = bat returnStatus: true, script: "\"${toolbelt}\\sfdx\" force:auth:logout --username ${HUB_ORG}"
        rc=shreturnStatus:true,script:"${toolbelt}/sfdxforce:auth:jwt:grant--clientid${CONNECTED_APP_CONSUMER_KEY}--username${HUB_ORG}--jwtkeyfile${jwt_key_file}--setdefaultdevhubusername--instanceurl${SFDC_HOST}"
        if(rc!=0){error'huborgauthorizationfailed'}
            
        }



     }
    
}
}
