pipeline {
    agent any
    environment {
        VERSION = "1.0"
        CRED_SERVER_CLIENT_ID = credentials("HOMA_SERVER_CLIENT_ID")
        CRED_SERVER_CLIENT_SECRET = credentials("HOMA_SERVER_CLIENT_SECRET")
        BUILD = "AUC-$BUILD_NUMBER-$BUILD_TIMESTAMP"
        STAGING_CERT_PWD = credentials("HOMA_STAGING_CERT_PWD")
    }
    stages {
        stage('Install') {
            steps {
                nodejs(nodeJSInstallationName: 'NodeJS 12.0.0') {
                  catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                      sh 'npm install'
                  }
                }
            }
        }
        stage('Build') {
            steps {
                nodejs(nodeJSInstallationName: 'NodeJS 12.0.0') {
                    sh 'npm run compile:js:prod'
                    sh 'npm run compile:scss:prod'
                    sh 'npm run compile:fonts'
                    sh 'npm run compile:svg'
                }
            }
        }
        stage('Deploy to Staging') {
            when {
              expression {
                SERVER_NAME.contains('staging')
              }
            }
            steps {
              echo 'DEPLOYING to staging'
              echo 'HOMA_SERVER_NAME_CERT - $HOMA_SERVER_NAME_CERT '
              echo 'CRED_SERVER_CLIENT_ID $CRED_SERVER_CLIENT_ID'
              echo 'CRED_SERVER_CLIENT_SECRET $CRED_SERVER_CLIENT_SECRET'
              echo 'STAGING_CERT_PWD $STAGING_CERT_PWD'
              
            }
        }
        stage('Deploy to Sandbox') {
            when {
              expression {
                !SERVER_NAME.contains('staging')
              }
            }
            steps {
              println "SERVER_NAME: ${SERVER_NAME}"
              echo 'DEPLOYING'
                           echo 'HOMA_SERVER_NAME_CERT - $HOMA_SERVER_NAME_CERT '
              echo 'CRED_SERVER_CLIENT_ID $CRED_SERVER_CLIENT_ID'
              echo 'CRED_SERVER_CLIENT_SECRET $CRED_SERVER_CLIENT_SECRET'
              echo 'STAGING_CERT_PWD $STAGING_CERT_PWD'
                echo 'DEPLOY DONE 🐤'
                office365ConnectorSend message: "🚀 Deploy done! [$SERVER_NAME][$BUILD] by Maister 🐤", status:"SUCCESS", webhookUrl:'https://capgemini.webhook.office.com/webhookb2/eef0ecc5-f053-4000-97bf-2ecd3fc0f44d@76a2ae5a-9f00-4f6b-95ed-5d33d77c4d61/JenkinsCI/8b723115931948afb142db13c2a23da9/b17c15fd-acd9-46a4-bd3b-6edfb893e677'
              }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
