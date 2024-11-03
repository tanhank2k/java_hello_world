pipeline {
    agent {node { label 'java-slave' }}
    environment {
        APP_NAME = 'java_hello_world'
        DOCKER_IMAGE = 'spring-boot-app:latest'  // Update to your Docker Hub username
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                     sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    // Stop and remove the old container if it exists
                    sh """
                    if [ \$(docker ps -q -f name=${DOCKER_IMAGE}) ]; then
                        docker stop ${DOCKER_IMAGE}
                        docker rm ${DOCKER_IMAGE}
                    fi
                    """

                    // Run the new container
                    sh """
                    docker run -d -p 8080:8080 --name ${APP_NAME} ${DOCKER_IMAGE}
                    """
                }
            }
        }
    }
    post {
        success {
            echo 'Build and Docker image push were successful!'
        }
        failure {
            echo 'Build failed, please check the console output.'
        }
    }
}
