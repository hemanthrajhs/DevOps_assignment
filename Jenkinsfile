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
                        if [ $(docker ps -q -f name=mysql) ]; then
                            docker stop mysql && docker rm -f mysql
                        fi
                    """
                    
                    // Start MySQL container
                    sh """
                        docker run --name my
