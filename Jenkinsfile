pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "viswaraje/blue-green-app:v1"
        BLUE_CONTAINER_NAME = "blue-green-app:v1-blue"
        GREEN_CONTAINER_NAME = "blue-green-app:v1-green"
    }

    stages {
        stage('Build') {
            steps {
                script {
                    docker.build(DOCKER_IMAGE)
                }
            }
        }

        stage('Deploy to Blue') {
            steps {
                script {
                    try {
                        docker.image(DOCKER_IMAGE).run("--name $BLUE_CONTAINER_NAME -d -p 3001:3000")
                    } catch (e) {
                        echo "Blue environment already exists. Starting a new one."
                        sh "docker rm -f $BLUE_CONTAINER_NAME"
                        docker.image(DOCKER_IMAGE).run("--name $BLUE_CONTAINER_NAME -d -p 3001:3000")
                    }
                }
            }
        }

        stage('Test Blue') {
            steps {
                script {
                    def response = sh(script: "curl -s http://localhost:3001/status", returnStdout: true).trim()
                    if (response != '{"status":"API is running"}') {
                        error "Blue environment test failed"
                    }
                }
            }
        }

        stage('Deploy to Green') {
            steps {
                script {
                    try {
                        docker.image(DOCKER_IMAGE).run("--name $GREEN_CONTAINER_NAME -d -p 3002:3000")
                    } catch (e) {
                        echo "Green environment already exists. Starting a new one."
                        sh "docker rm -f $GREEN_CONTAINER_NAME"
                        docker.image(DOCKER_IMAGE).run("--name $GREEN_CONTAINER_NAME -d -p 3002:3000")
                    }
                }
            }
        }

        stage('Test Green') {
            steps {
                script {
                    def response = sh(script: "curl -s http://localhost:3002/status", returnStdout: true).trim()
                    if (response != '{"status":"API is running"}') {
                        error "Green environment test failed"
                    }
                }
            }
        }

        stage('Switch Traffic to Green') {
            steps {
                script {
                    // Here, you would update the load balancer configuration to route traffic to Green
                    echo "Switching traffic to Green environment"
                    // For example, if you are using nginx, you could update the nginx config
                }
            }
        }

        stage('Clean Up Blue') {
            steps {
                script {
                    sh "docker rm -f $BLUE_CONTAINER_NAME"
                }
            }
        }
    }
}
