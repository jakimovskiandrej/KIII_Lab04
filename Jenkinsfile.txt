pipeline {
    agent any

    environment {
        DOCKER_USER = 'jakimovskiandrej'
        DOCKER_HUB_CREDS = credentials('5008bf0f-f812-41c6-b3b3-e44e1e888192')
    }

    stages {
        stage('Clone repository') {
            steps {
                checkout scm
            }
        }

        stage('Build image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_USER}/kiii-lab:latest ."
                }
            }
        }

        stage('Push image') {
            steps {
                script {
                    sh "echo \$DOCKER_HUB_CREDS_PSW | docker login -u \$DOCKER_HUB_CREDS_USR --password-stdin"
                    sh "docker push ${DOCKER_USER}/kiii-lab:latest"
                }
            }
        }
    }

    post {
        success {
            echo 'Апликацијата е успешно изградена и пратена на Docker Hub!'
        }
        failure {
            echo 'Процесот не успеа. Провери ги лозинките или лог фајловите.'
        }
    }
}