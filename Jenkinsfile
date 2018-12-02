pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh './gradlew clean assembleDebug'
            }
        }
    }

    post {
        always {
            httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', responseHandle: 'NONE', url: "http://api-portal.inveritasoft.com:22180/api/sendMessage", httpMode: "POST", requestBody: '{\"chat_id\": \"-1001240674447\", \"text\":\"'+currentBuild.currentResult+'\"}'
            httpRequest(url: "http://api-portal.inveritasoft.com:22180/api/sendFile", httpMode: "POST", requestBody: '{chat_id: -1001240674447, documentPath: \"'+ env.WORKSPACE +'\" }')
        }
        failure {
            httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', responseHandle: 'NONE', url: "http://api-portal.inveritasoft.com:22180/api/sendMessage", httpMode: "POST", requestBody: '{\"chat_id\": \"-1001240674447\", \"text\": \"BUILD FAILED\" }'
        }
    }
}
