pipeline {
    agent none

    environment {
        DB_HOST = 'mysql'
        DB_NAME = 'mydb'
        DB_USERNAME = 'user'
        DB_PASSWORD = 'password'
    }

    stages {
        stage('Start MySQL Container') {
            agent {
                docker {
                    image 'docker:latest' // Correct image name
                    args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                script {
                    // Stop and remove any existing MySQL container
                    sh """
                        if [ \$(docker ps -q -f name=mysql) ]; then
                            docker stop mysql && docker rm -f mysql
                        fi
                    """
                    
                    // Start MySQL container
                    sh """
                        docker run --name mysql -e 'MYSQL_ROOT_PASSWORD=${DB_PASSWORD}' \
                        -e 'MYSQL_DATABASE=${DB_NAME}' -e 'MYSQL_USER=${DB_USERNAME}' \
                        -e 'MYSQL_PASSWORD=${DB_PASSWORD}' -d -p 3306:3306 mysql:5.7
                    """
                    sh 'sleep 30'  // Wait for MySQL container to be ready
                }
            }
        }

        stage('Checkout Code') {
            agent {
                docker {
                    image 'abhishekf5/maven-abhishek-docker-agent:v1' // Corrected image name
                    args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                script {
                    // Checkout code from GitHub repository
                    git credentialsId: 'your-credentials-id', branch: 'main', url: 'https://github.com/hemanthrajhs/DevOps_assignment'
                }
            }
        }

        stage('Build and Test') {
            agent {
                docker {
                    image 'abhishekf5/maven-abhishek-docker-agent:v1' // Corrected image name
                    args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                sh 'mvn clean package'
                sh 'mvn test'
            }
        }

        stage('Build Docker Image') {
            agent {
                docker {
                    image 'docker:latest' // Correct image name
                    args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                script {
                    sh 'docker build -t myapp:${BUILD_NUMBER} .'
                }
            }
        }

        stage('Push Docker Image') {
            environment {
                DOCKER_IMAGE = "myapp:${BUILD_NUMBER}"
                REGISTRY_CREDENTIALS = credentials('docker-cred')
            }
            agent {
                docker {
                    image 'docker:latest' // Correct image name
                    args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                script {
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
