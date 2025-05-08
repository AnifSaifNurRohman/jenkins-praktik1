pipeline {
    agent {
        docker {
            image 'python:3.10'
            args '-v $PWD:/workspace'  // Mounting current directory to container's workspace
        }
    }

    environment {
        VENV = '/workspace/venv'  // Path for virtual environment in container
    }

    stages {
        stage('Setup Environment & Install Dependencies') {
            steps {
                script {
                    // Activate the venv and install dependencies
                    sh '''
                        python -m venv $VENV
                        . $VENV/bin/activate
                        pip install --upgrade pip
                        pip install -r requirements.txt
                    '''
                }
            }
        }
        
        stage('Run Tests') {
            steps {
                script {
                    // Run tests inside the virtual environment
                    sh '''
                        . $VENV/bin/activate
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
