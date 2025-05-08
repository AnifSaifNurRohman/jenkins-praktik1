pipeline {
    agent {
        docker {
            image 'python:3.10'
            args '-v C:/ProgramData/Jenkins/.jenkins/workspace:/workspace'
        }
    }

    environment {
        VENV = 'C:/workspace/venv' 
    }

    stages {
        stage('Setup Environment & Install Dependencies') {
            steps {
                script {
                    bat '''
                        python -m venv %VENV%
                        call %VENV%\\Scripts\\activate.bat
                        pip install --upgrade pip
                        pip install -r requirements.txt
                    '''
                }
            }
        }
        
        stage('Run Tests') {
            steps {
                script {
                    bat '''
                        call %VENV%\\Scripts\\activate.bat
                        pytest test_app.py
                    '''
                }
            }
        }
        
        stage('Deploy') {
            when {
                anyOf {
                    branch 'main'
                    branch pattern: "release/*", comparator: "REGEXP"
                }
            }
            steps {
                echo "Simulating deploy from branch ${env.BRANCH_NAME}"
            }
        }
    }

    post {
        success {
            script {
                def payload = [
                    content: "✅ Build SUCCESS on `${env.BRANCH_NAME}`\nURL: ${env.BUILD_URL}"
                ]
                httpRequest(
                    httpMode: 'POST',
                    contentType: 'APPLICATION_JSON',
                    requestBody: groovy.json.JsonOutput.toJson(payload),
                    url: 'https://discord.com/api/webhooks/1369954796844617728/kCYiOI1uTqk40Rl44x-qPp6ww3h1fPGCLoeb9ZeREoTuqOs4M1ZkaJEUts5u7qw1qji4'
                )
            }
        }
        failure {
            script {
                def payload = [
                    content: "❌ Build FAILED on `${env.BRANCH_NAME}`\nURL: ${env.BUILD_URL}"
                ]
                httpRequest(
                    httpMode: 'POST',
                    contentType: 'APPLICATION_JSON',
                    requestBody: groovy.json.JsonOutput.toJson(payload),
                    url: 'https://discord.com/api/webhooks/1369954796844617728/kCYiOI1uTqk40Rl44x-qPp6ww3h1fPGCLoeb9ZeREoTuqOs4M1ZkaJEUts5u7qw1qji4'
                )
            }
        }
    }
}
