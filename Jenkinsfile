pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "viswaraje/blue-green-app:v1"
        BLUE_PORT = "3001"
        GREEN_PORT = "3002"
    }
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/Viswaraje/lab_12_devops.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}")
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.image("${DOCKER_IMAGE}").push()
                }
            }
        }
        stage('Deploy Blue') {
            steps {
                sh 'docker run -d -p ${BLUE_PORT}:3000 --name blue ${DOCKER_IMAGE}'
            }
        }
        stage('Deploy Green') {
            steps {
                sh 'docker run -d -p ${GREEN_PORT}:3000 --name green ${DOCKER_IMAGE}'
            }
        }
        stage('Switch Traffic') {
            steps {
                input message: "Switch traffic to Green environment?"
                sh 'docker stop blue && docker start green'
            }
        }
    }
    post {
        always {
            sh 'docker stop green || true'
            sh 'docker rm green || true'
            sh 'docker stop blue || true'
            sh 'docker rm blue || true'
        }
    }
}
