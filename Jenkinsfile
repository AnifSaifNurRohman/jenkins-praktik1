pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Run Python in Docker') {
            steps {
                bat 'docker run --rm python:3.10 python --version'
            }
        }
    }

    post {
        always {
            script {
                def payload = [
                    content: "Pipeline selesai dijalankan. Status: ${currentBuild.currentResult}"
                ]
                httpRequest httpMode: 'POST',
                            contentType: 'APPLICATION_JSON',
                            requestBody: groovy.json.JsonOutput.toJson(payload),
                            url: 'https://discord.com/api/webhooks/1369954796844617728/kCYiOI1uTqk40Rl44x-qPp6ww3h1fPGCLoeb9ZeREoTuqOs4M1ZkaJEUts5u7qw1qji4'
            }
        }
    }
}
