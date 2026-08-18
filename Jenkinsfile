pipeline {

    agent any

    environment {
        STABLE_IMAGE = "day18-stable"
        NEW_IMAGE = "day18-new"
        STABLE_CONTAINER = "day18-stable-container"
        NEW_CONTAINER = "day18-new-container"
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Source code checked out from GitHub'
            }
        }

        stage('Build Stable Image') {
            steps {
                sh 'docker build -t ${STABLE_IMAGE} ./stable'
            }
        }

        stage('Build New Version Image') {
            steps {
                sh 'docker build -t ${NEW_IMAGE} ./new-version'
            }
        }

        stage('Stop Old New Version') {
            steps {
                sh '''
                docker rm -f ${NEW_CONTAINER} || true
                '''
            }
        }

        stage('Deploy New Version') {
            steps {
                sh '''
                docker run -d \
                --name ${NEW_CONTAINER} \
                -p 8082:80 \
                ${NEW_IMAGE}
                '''
            }
        }

        stage('Health Check New Version') {
            steps {
                sh 'sleep 3'
                sh 'curl -f http://localhost:8082'
            }
        }

        stage('Deployment Successful') {
            steps {
                echo 'New version passed health check.'
                echo 'Deployment can continue.'
            }
        }
    }

    post {

        success {
            echo 'SUCCESS: New version deployed successfully.'
        }

        failure {
            echo 'FAILURE: New version failed.'
            echo 'Rollback should be performed.'
        }
    }
}
