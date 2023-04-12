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
    def TEST_LEVEL='RunSpecifiedTests'
    def TEST_CLASSES
    def APEX_CLASSES
    def TEST_LEVEL_NO_RUN = 'NoTestRun'



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
            sh 'rm -v \${WORKSPACE}/force-app/main/default/classes/* | rm -v \${WORKSPACE}/force-app/main/default/triggers/*'
            sh 'echo y | sfdx plugins:install sfdx-git-delta'
            rc = sh returnStatus: true, script: "sfdx sgd:source:delta --to HEAD --from HEAD^ --output . --generate-delta"//--ignore ignorefile
            if (rc != 0) { error 'cannot calculate delta' }
            sh 'cat package/package.xml'
        }

        stage("Validate"){
            // Deploy steps here
            //sh 'cat package/package.xml | xq . | jq .Package.types | select(.name=="ApexClass") | .members | join(",")'
//sh 'grep -rl --include=*Test.cls "@testClass\s.*" ${WORKSPACE}/package/package.xml | xargs -I {} grep -H "@testClass" {} | awk '{print substr($0, index($0, "@testClass")+10)}' | xargs -I {}'

//rc = sh returnStatus: true, script: "sfdx force:source:deploy -p ${WORKSPACE}/package/package.xml -l RunSpecifiedTests -r {}  --checkonly --wait 120 -c -x ${WORKSPACE}/package/package.xml -u ${HUB_ORG}"
//sh 'IFS=',' read -ra TEST_CLASSES <<< "$TEST_CLASSES" for TEST_CLASS in "${TEST_CLASSES[@]}"; '

          //sh "export APEX_CLASES=$(xq . < package/package.xml | jq '.Package.types | [.] | flatten | map(select(.name=="ApexClass")) | .[] | .members | [.] | flatten | map(select(. | index("*") | not)) | unique | join(",")' -r) | echo ${APEX_CLASES}"
            //TESTCLASSS
          APEX_CLASSES = sh (script: "grep -ri -w @TestClass \${WORKSPACE}/force-app/main/default/classes ${WORKSPACE}/force-app/main/default/triggers| awk -F '@testClass ' '{print \$2}'| sort -u| sed 's/\$/,/'| tr -d '\n'", returnStdout:true)

          //sh "awk -F '@testClass ' '{print \$2}' '

//FIND_TEST_CLASSES_FROM_PACKAGE = sh (script:"xq . < package/package.xml | jq '.Package.types | [.] | flatten | map(select(.name=='ApexClass')) | .[] | .members | [.] | flatten | map(select(. | index("*") | not)) | unique | join(",")' -r"),returnStdout:true)


          //sh "sort -u"
          //sh 'awk 'BEGIN{  nlines = 0 }  { nlines ++ ; array[nlines] = \$1  } END{  for ( i = 1 ; i < nlines ; i ++ ) { printf  array[i]',' }}''

echo "${APEX_CLASSES}"

APEX_CLASSES = sh (script: "echo '${APEX_CLASSES}'| rev | cut -c 2- | rev", returnStdout:true)

echo "${APEX_CLASSES}"
 
def cmd
            
sh  "if [ -z '${APEX_CLASSES}' ]; then 
        cmd = true
       else
        cmd = false
fi"
            

          //rc = sh returnStatus: true, script: "sfdx force:source:deploy -p ${WORKSPACE}/package/package.xml -l RunSpecifiedTests -r ${APEX_CLASSES} --checkonly --wait 120 -c  -u ${HUB_ORG}"
//${WORKSPACE}/package/package.xml
//sh (script:"[ -z '${APEX_CLASSES}' ] && echo 'sfdx force:source:deploy --wait 120 -c -x ${WORKSPACE}/package/package.xml  -u ${HUB_ORG} --testlevel ${TEST_LEVEL_NO_RUN}' || echo 'sfdx force:source:deploy --wait 120 -c -x ${WORKSPACE}/package/package.xml  -u ${HUB_ORG} --testlevel ${TEST_LEVEL} -r ${APEX_CLASSES}'")
if (cmd) {          
echo "NO TEST RUN NEEDED FOR CHECKONLY"
rc = sh returnStatus: true, script: "sfdx force:source:deploy --wait 120 -c -x ${WORKSPACE}/package/package.xml  -u ${HUB_ORG} --testlevel ${TEST_LEVEL_NO_RUN}"
}
else{
echo "TEST RUN NEEDED FOR CHECKONLY"
rc = sh returnStatus: true, script: "sfdx force:source:deploy --wait 120 -c -x ${WORKSPACE}/package/package.xml  -u ${HUB_ORG} --testlevel ${TEST_LEVEL} -r ${APEX_CLASSES}"
}
     

            if (rc != 0) {
                error 'Salesforce deploy and test run failed.'
                sh "sfdx force:auth:logout -u ${HUB_ORG} -p"
            }
        }
        stage("Deploy"){
            // Deploy steps here
            rc = sh returnStatus: true, script: "sfdx force:source:deploy -x ${WORKSPACE}/package/package.xml -u ${HUB_ORG} --testlevel ${TEST_LEVEL_NO_RUN}"
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
