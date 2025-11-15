pipeline {
    agent any
    tools { nodejs "Node 7.8.0" }
    environment {
        BRANCH_NAME = "${env.BRANCH_NAME}"
        IMAGE_TAG = 'v1.0'
    }
    stages {
        stage('Set Environment') {
            steps {
                script {
                    env.PORT = (env.BRANCH_NAME == 'main') ? '3000' : '3001'
                    env.IMAGE_NAME = (env.BRANCH_NAME == 'main') ? 'nodemain' : 'nodedev'
                }
            }
        }
        stage('Checkout') {
            steps { checkout scm }
        }
        stage('Build') {
            steps { sh 'npm install' }
        }
        stage('Test') {
            steps { sh './scripts/test.sh' }
        }
        stage('Build Docker Image') {
            steps { sh "docker build -t $$ {IMAGE_NAME}: $${IMAGE_TAG} ." }
        }
        stage('Deploy') {
            steps {
                sh """
                    docker ps -q --filter "name=${IMAGE_NAME}" | xargs -r docker stop
                    docker ps -aq --filter "name=${IMAGE_NAME}" | xargs -r docker rm
                    docker run -d --name ${IMAGE_NAME} --expose ${PORT} -p ${PORT}:3000 $$ {IMAGE_NAME}: $${IMAGE_TAG}
                """
            }
        }
    }
}
