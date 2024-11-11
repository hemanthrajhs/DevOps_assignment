pipeline {
    agent any  // Defines that the pipeline will run on any available agent

    environment {
        DOCKER_IMAGE = "hemanthrajhs/app-devops-assign:${BUILD_NUMBER}"
        REGISTRY_CREDENTIALS = credentials('docker-cred')
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Using Docker image to run Git commands
                    docker.image('abhishekf5/maven-abhishek-docker-agent:v1').inside('--user root -v /var/run/docker.sock:/var/run/docker.sock') {
                        sh 'echo passed'
                        // Uncomment to pull the repository
                        git branch: 'main', url: 'https://github.com/hemanthrajhs/DevOps_assignment'
                    }
                }
            }
        }

        stage('Build and Test') {
            steps {
                script {
                    // Using Docker image to run the build and tests
                    docker.image('abhishekf5/maven-abhishek-docker-agent:v1').inside('--user root -v /var/run/docker.sock:/var/run/docker.sock') {
                        sh 'ls -ltr'
                        // Build the project and create a JAR file
                        sh 'mvn clean package'
                    }
                }
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    docker.build("${DOCKER_IMAGE}")
                    
                    // Push the Docker image to Docker Hub
                    def dockerImage = docker.image("${DOCKER_IMAGE}")
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-cred') {
                        dockerImage.push()
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution finished.'
        }
        success {
            echo 'Build and deployment successful!'
        }
        failure {
            echo 'Build failed.'
        }
    }
}
