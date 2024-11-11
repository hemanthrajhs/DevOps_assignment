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
                    image 'docker:latest'
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
                    image 'abhishekf5/maven-abhishek-docker-agent:v1'
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
                    image 'abhishekf5/maven-abhishek-docker-ag
