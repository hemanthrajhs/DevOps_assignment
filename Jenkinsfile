pipeline {
    agent {
        docker {
      image 'abhishekf5/maven-abhishek-docker-agent:v1'
      args '--user root -v /var/run/docker.sock:/var/run/docker.sock' // mount Docker socket to access the host's Docker daemon
    }
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
