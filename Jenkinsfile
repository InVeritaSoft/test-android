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
            script {
                if(env.BRANCH_NAME == 'master') {
                    def files = findFiles(glob: '**/release/*.apk');
                } else {
                    def files = findFiles(glob: '**/debug/*.apk');
                }
                
            }
            httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', responseHandle: 'NONE', url: "http://api-portal.inveritasoft.com:22180/api/sendMessage", httpMode: "POST", requestBody: '{\"chat_id\": \"-1001240674447\", \"text\":\"BRANCH: '+env.BRANCH_NAME+'%0ABUILD NUMBER: '+currentBuild.number+'%0ASTATUS: '+currentBuild.currentResult+'%0AJOB URL: '+env.JOB_URL+'\"}'
            script {
                if (env.BRANCH_NAME == 'master') {
                    httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', responseHandle: 'NONE', url: "http://api-portal.inveritasoft.com:22180/api/sendFile", httpMode: "POST", requestBody: '{\"chat_id\": \"-1001240674447\", \"documentPath\": \"'+ env.WORKSPACE +'/app/build/outputs/apk/release/'+files[0].name+'\" }'
                }
                if (env.BRANCH_NAME == 'dev') {
                    httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', responseHandle: 'NONE', url: "http://api-portal.inveritasoft.com:22180/api/sendFile", httpMode: "POST", requestBody: '{\"chat_id\": \"-1001240674447\", \"documentPath\": \"'+ env.WORKSPACE +'/app/build/outputs/apk/debug/'+files[0].name+'\" }'
                }
            }
        }
        failure {
            httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', responseHandle: 'NONE', url: "http://api-portal.inveritasoft.com:22180/api/sendMessage", httpMode: "POST", requestBody: '{\"chat_id\": \"-1001240674447\", \"text\": \"BUILD FAILED\" }'
        }
    }
}
