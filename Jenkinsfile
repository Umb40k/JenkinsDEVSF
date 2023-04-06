#!groovy
import groovy.json.JsonSlurperClassic
node {

    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def HUB_ORG="pdev@sf.com"
    def SFDC_HOST ="https://login.salesforce.com"
    def JWT_KEY_CRED_ID = "3d46dce5-02ff-4c66-a0db-1d392a6b1182"
    def JWT_KEY_FILE= "-----BEGIN PRIVATE KEY----- MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQDWGQc09jkETTks UIAdsNaeV9BWmFdQxAuKJ+p36CDIY9LY8Myrpteo8cN2m56LqXemYfuZyPxL6RAZ lHZifQeGsL4Jfo9h4FwFlM791+3EBTmkyDPi/uw+EZyTB2xRmOVjSn8vqAvAhm5O /yJp3ZCS07tPT14NiE2cQCR9b9QItCM6ugNFBJdCRB3ETSik8qcRxPpmnLXSHS9U hCtJWvQ/bR3+JOrOxRHEnyPjpQAeGEPNHSJQsFdG3TW/sHPCY0Giop4NCSjFhaZw 5kK8PYIm7/1982xhW6KSKLTvyEXPa6fZLptpDjiTgjJ7Teh0eF9USuY/wQL60gGP DHUW7ZOZAgMBAAECggEAYr/ghxkaa1WDDWGD9i3ej+JJAef6PhJukCahCvVSSZth YRaQEIw/5Hi+gzJ8Ckb8HQ4Mkn4VgJ3bAzFq+TpaAlH7FT3+vFJTn29nmcXCsSsH z21YIVcbd/V/loFSAMDY7NcrmruT0BUxQ9aB9Ppw+IgYWRhudSdtRKiBLCosTmaP 42ndEPPRmTQ+ey70+SpwX84f6h9vZN1er6Jl7B8+To0eOSb/ijusXlIMSxqa67aB vcB+5QoK1MVPyBmcr6nsHcT3ze/XTQwYEUuAGqsfKk3PGHG/jcwVW8/ycv4SLndv ttrm6wy62KpuELTuDwONqmZ5GZg41TFzrdWBEB7oQQKBgQDvivZ+lUaKoHQsorD0 3b3eE4cFbkJx+6rKmZfSha6GUGi4lZbPlTQXURWsaHVnAMcYvcqPKlb8DrS09fZ2 1wMKLkM7oebZlVnFChQ8dLBKCi1mKK1yi3lX8PETCZVw0U8XWEQfuGJ/Y/fyabx2 DAj5sJXHIci0KZXLDBbmO/1nXQKBgQDkzorFq6LKohn/S9dZJGdZSQ17n+h/ODTG 7YZMpjm9MCGoh/tITaCkDQq/sd701KzS8k5NDDBeG97i2MFvGq2oovFXnxGZGQoQ Gs59VbfjI7r1DG7dNtb779206Z2ZTHyViBv+o0JJHMu+Os24+MzsLYwaIwGaapJr boWkN5bFbQKBgQC6YiTTb3D40koypNANZy+hyQ3pAta+e7Rzebjw/EVEivJAlVxH 3m8uqU+0235mC+vea2ZdqPMPop2mpC8GXtwlosN7dcj6icPUlbgTJjQ5F9wlPgdf NfjPnVDmoDFgG+xvXKV9DOnO90jonK8gJtMg/O49iCLzU9wSpjB3KjxMnQKBgEKl 70JZFphT06LL4rSyp/AYGXdTQxQbuTPg6GfXW6ZCKOvAgG+1PP/MVL81/q3ubVEd B7XlwQkIsUn+vYmxibg/jnFpgmTIKGg8hr13hJMDS2gp8Pk7CjBk1H3oNuH6p6ee +BiaUaqeWrLUyiwBPR/mvJK4c7UZKgFlNkR5dM+ZAoGAbID5Doi2key8Lb3aPl26 pLQhQpVOc6LccBBjeNrG/Dswn84OcM/c4EKwmh3AtU1hjiUc2wlkU2UQiVb8L7/c 9lRDeyBGj8CHEIpxEaexW3DvmaLv3LzkgIVfJ+P2/Ea/DmQOjsqHwYSLQrNI53D8 p4zYj4X4bEx79hKVqcyq40o= -----END PRIVATE KEY-----"
    def DEPLOYDIR='src'
    def TEST_LEVEL='RunLocalTests'



    def CONNECTED_APP_CONSUMER_KEY="3MVG9vvlaB0y1YsJEx2GTDHujtGeAKj6n7TyOlU1EuPwwKdwAbLu0Ai_6qTXypf9N5M_T4GWsyQuvM5633wnb"

    println 'KEY IS' 
    println JWT_KEY_CRED_ID
    println HUB_ORG
    println SFDC_HOST
    println CONNECTED_APP_CONSUMER_KEY
    printf JWT_KEY_FILE > code.key
    def toolbelt = tool 'toolbelt'
    //def sfdx = tool 'sfdxtool'
    stage('checkout source') {
        checkout scm
    }

    withEnv(["HOME=${env.WORKSPACE}"]) {

    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: code.key)]) {        
        stage('SFDX Login') {
        //rcl = bat returnStatus: true, script: "\"${toolbelt}\\sfdx\" force:auth:logout --username ${HUB_ORG}"
        //rc = sh returnStatus: true, script: "\"${toolbelt}\\sfdx\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
          println 'COMMAND' 
          rc = command "${toolbelt}/sfdx force:auth:jwt:grant --instanceurl ${SFDC_HOST} --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile code.key --setdefaultdevhubusername"
        if (rc != 0) { error 'hub org authorization failed' 
        }
            
        }



     }
    
}
}
