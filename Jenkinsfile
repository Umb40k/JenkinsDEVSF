#!groovy
import groovy.json.JsonSlurperClassic
node {

    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME
    def WORKSPACE = env.WORKSPACE

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
        stage('Org Login') {
//preLogout
        rcl = sh returnStatus: true, script: "sfdx force:auth:logout --targetusername ${HUB_ORG}"
//login
        rc = sh returnStatus: true, script: "sfdx force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST} --setalias ${HUB_ORG}"
        //rc = sh "sfdx force:auth:jwt:grant --clientid $CONNECTED_APP_CONSUMER_KEY --username $HUB_ORG --jwtkeyfile $jwt_key_file --instanceurl $SFDC_HOST"
            println rc
        if(rc!=0){error'hub orgauthorization failed'}
            
        }
       /* stage("Convert to mdapi"){                
            rc = sh returnStatus: true, script: "sfdx force:source:convert -d mdapi"
            if (rc != 0) { error 'cannot convert source to mdapi' }

        }*/
        stage("Calculate delta"){                
            sh 'echo y | sfdx plugins:install sfdx-git-delta
            rc = sh returnStatus: true, script: "sfdx sgd:source:delta --to HEAD --from HEAD^ --ignore ignorefile --output ."
            if (rc != 0) { error 'cannot calculate delta' }
            cat package/package.xml
        }
        
        stage("Validate"){
            // Deploy steps here                
            rc = sh returnStatus: true, script: "sfdx force:mdapi:deploy --wait 120 -c --deploydir ${WORKSPACE}/mdapi --targetusername ${HUB_ORG} --testlevel ${TEST_LEVEL}"
            if (rc != 0) {
                error 'Salesforce deploy and test run failed.'
                sh "sfdx force:auth:logout -u ${HUB_ORG} -p"                 
            }
        }
        stage("Deploy"){
            // Deploy steps here 
            rc = sh returnStatus: true, script: "sfdx force:source:deploy -x package/package.xml -u ${HUB_ORG}"
            //rc = sh returnStatus: true, script: "sfdx force:mdapi:deploy --wait 120 --deploydir ${WORKSPACE}/mdapi --targetusername ${HUB_ORG}"
            if (rc != 0) {
                error 'Salesforce deploy and test run failed.'
                sh "sfdx force:auth:logout -u ${HUB_ORG} -p"                 
            }
                sh "sfdx force:auth:logout -u ${HUB_ORG} -p"                 

        }


     }
    
}
}
