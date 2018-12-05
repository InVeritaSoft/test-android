pipeline {
    agent any

    stages {
        stage('Build Release') {
            when {
                branch 'master'
            }
            steps {
                sh './gradlew clean assembleRelease'
            }
        }
        stage('Build Debug') {
            when {
                branch 'dev'
            }
            steps {
                sh './gradlew clean assembleDebug'
            }
        }
    }

    post {
        always {
            httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', responseHandle: 'NONE', url: "http://api-portal.inveritasoft.com:22180/api/sendMessage", httpMode: "POST", requestBody: '{\"chat_id\": \"-1001240674447\", \"text\":\"BRANCH: '+env.BRANCH_NAME+'\nBUILD NUMBER: '+currentBuild.number+'\nSTATUS: '+currentBuild.currentResult+'\nJOB URL: '+env.JOB_URL+'\"}'
            script {
                if (env.BRANCH_NAME == 'master') {
                    httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', responseHandle: 'NONE', url: "http://api-portal.inveritasoft.com:22180/api/sendFile", httpMode: "POST", requestBody: '{\"chat_id\": \"-1001240674447\", \"documentPath\": \"'+ env.WORKSPACE +'/app/build/outputs/apk/release/app-debug.apk\" }'
                }
                if (env.BRANCH_NAME == 'dev') {
                    httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', responseHandle: 'NONE', url: "http://api-portal.inveritasoft.com:22180/api/sendFile", httpMode: "POST", requestBody: '{\"chat_id\": \"-1001240674447\", \"documentPath\": \"'+ env.WORKSPACE +'/app/build/outputs/apk/debug/app-debug.apk\" }'
                }
            }
        }
        failure {
            httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', responseHandle: 'NONE', url: "http://api-portal.inveritasoft.com:22180/api/sendMessage", httpMode: "POST", requestBody: '{\"chat_id\": \"-1001240674447\", \"text\": \"BUILD FAILED\" }'
        }
    }
}
