pipeline {
    agent none  // No global agent, we define agents per stage

    environment {
        DB_HOST = 'mysql'  // Container name, which is accessible from within the Docker network
        DB_NAME = 'mydb'  // Your database name
        DB_USERNAME = 'user'  // Username for the database
        DB_PASSWORD = 'password'  // Password for the database
    }

    stages {
        stage('Start MySQL Container') {
            agent {
                docker {
                    image 'docker:latest'  // Use Docker image for Docker operations
                    args '--user root -v /var/run/docker.sock:/var/run/docker.sock'  // Mount Docker socket
                }
            }
            steps {
                script {
                    // Start MySQL container
                    sh """
                        docker run --name mysql -e MYSQL_ROOT_PASSWORD=${DB_PASSWORD} \
                        -e MYSQL_DATABASE=${DB_NAME} -e MYSQL_USER=${DB_USERNAME} \
                        -e MYSQL_PASSWORD=${DB_PASSWORD} -d -p 3306:3306 mysql:5.7
                    """
                    // Wait for the database to initialize (you can adjust the sleep time as necessary)
                    sh 'sleep 30'  // Wait for MySQL container to be ready
                }
            }
        }

        stage('Checkout Code') {
            agent {
                docker {
                    image 'abhishekf5/maven-abhishek-docker-agent:v1'
                    args '--user root -v /var/run/docker.sock:/var/run/docker.sock'  // Mount Docker socket to access Docker daemon
                }
            }
            steps {
                // Clone the repository and checkout the main branch
                git branch: 'main', url: 'https://github.com/hemanthrajhs/DevOps_assignment'
            }
        }

        stage('Build and Test') {
            agent {
                docker {
                    image 'abhishekf5/maven-abhishek-docker-agent:v1'
                    args '--user root -v /var/run/docker.sock:/var/run/docker.sock'  // Mount Docker socket to access Docker daemon
                }
            }
            steps {
                // Build the Spring Boot application
                sh 'mvn clean package'

                // Optional: Run tests (you can adjust this based on your requirements)
                sh 'mvn test'
            }
        }

        stage('Build Docker Image') {
            agent {
                docker {
                    image 'docker:latest'  // Use Docker image for Docker operations
                    args '--user root -v /var/run/docker.sock:/var/run/docker.sock'  // Mount Docker socket
                }
            }
            steps {
                script {
                    // Build Docker image for Spring Boot application
                    sh 'docker build -t myapp:${BUILD_NUMBER} .'
                }
            }
        }

        stage('Push Docker Image') {
            environment {
                DOCKER_IMAGE = "myapp:${BUILD_NUMBER}"
                REGISTRY_CREDENTIALS = credentials('docker-cred')  // Jenkins credentials for Docker registry
            }
            agent {
                docker {
                    image 'docker:latest'  // Docker operations
                    args '--user root -v /var/run/docker.sock:/var/run/docker.sock'  // Mount Docker socket
                }
            }
            steps {
                script {
                    // Push Docker image to Docker Hub (or any Docker registry)
                    def dockerImage = docker.image("${DOCKER_IMAGE}")
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-cred') {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Cleanup') {
            steps {
                // Stop and remove MySQL container
                sh 'docker stop mysql && docker rm mysql'
            }
        }
    }
}
