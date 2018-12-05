def files = []
def message = ''
def changeLogSets = []
def logs = []
def handleLogs(sets) {
    for (int i = 0; i < sets.size(); i++) {
        def entries = sets[i].items
        for (int j = 0; j < entries.length; j++) {
            def entry = entries[j]
            logs.push(entry.msg)
        }
    }
    logs.join('%0A')
}
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
        stage('Build') {
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
                message = sh (script: 'git show -s ${GIT_COMMIT} --format="format:%s"', returnStdout: true).trim()
                changeLogSets = currentBuild.rawBuild.changeSets
                handleLogs(sets: changeLogSets)
            }
            httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', responseHandle: 'NONE', url: "http://api-portal.inveritasoft.com:22180/api/sendMessage", httpMode: "POST", requestBody: '{\"chat_id\": \"-1001240674447\", \"text\":\"BRANCH: '+env.BRANCH_NAME+'%0ABUILD NUMBER: '+currentBuild.number+'%0ASTATUS: '+currentBuild.currentResult+'%0ACOMMIT: '+ env.GIT_COMMIT +'%0ACHANGE: '+ changeLogSets +'%0AJOB URL: '+env.JOB_URL+'\"}'
        }
        success {
            script {
                if(env.BRANCH_NAME == 'master') {
                    files = findFiles(glob: 'app/build/outputs/apk/release/*.apk');
                } 
                if(env.BRANCH_NAME == 'dev') {
                    files = findFiles(glob: 'app/build/outputs/apk/debug/*.apk');
                }
                
            }
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
