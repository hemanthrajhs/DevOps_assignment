pipeline {
    agent {
        docker {
            image 'maven:3.8.6-openjdk-11'  // Maven image with OpenJDK 11
            args '--user root -v /var/run/docker.sock:/var/run/docker.sock' // Mount Docker socket for Docker-in-Docker use cases
        }
    }
    stages {
        stage('Checkout') {
            steps {
                sh 'echo passed'
                // Uncomment to pull the repository
                // git branch: 'main', url: 'https://github.com/hemanthrajhs/DevOps_assignment'
            }
        }

     stage('Build and test') {
            steps {
                sh 'ls -ltr'
                // Build the project and create a JAR file
                sh 'mvn clean package'
            }
        }

        stage('Build and Push Docker Image') {
            environment {
                DOCKER_IMAGE = "hemanthrajhs/app-devops-assign:${BUILD_NUMBER}"
                REGISTRY_CREDENTIALS = credentials('docker-cred')
            }
            steps {
                script {
                    // Build the Docker image
                    sh 'docker build -t ${DOCKER_IMAGE} .'
                    
                    // Push the Docker image to Docker Hub
                    def dockerImage = docker.image("${DOCKER_IMAGE}")
                    docker.withRegistry('https://index.docker.io/v1/', "docker-cred") {
                        dockerImage.push()
                    }
                }
            }
        }
    }
}
