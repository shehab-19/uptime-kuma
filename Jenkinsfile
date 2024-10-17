pipeline {
    agent {   
        label 'agent1'
    }

    environment {
        DOCKER_IMAGE = 'shehab19/uptime-kuma'  // Replace with your Docker Hub username
    }

    stages {
        stage('Fetch Code') {
            steps {
                // Clone the Uptime Kuma repository
                git url: 'https://github.com/shehab-19/uptime-kuma.git', branch: 'master'
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                // Navigate to the docker directory and run docker-compose up
                dir('docker') {
                    sh "sudo docker-compose -f docker-compose-dev.yml up -d"
                }
            }
        }

        stage('Login to DockerHub') {
            steps {
                script {
                    // Login to Docker Hub using stored credentials
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                        sh '''
                        echo $DOCKERHUB_PASSWORD | docker login -u $DOCKERHUB_USERNAME --password-stdin
                        '''
                    }
                }
            }
        }

        stage('Push the Image') {
            steps {
                // skipped due to errors in the dockerfile
                sh "sudo docker push ${DOCKER_IMAGE}:v2"
            }
        }
    }

    post {
        always {
            // Clean up any existing containers (if necessary)
            sh "sudo docker-compose -f docker/docker-compose-dev.yml down"
        }

        success {
            echo 'Deployment completed successfully'
        }
        failure {
            echo 'Deployment failed'
        }
    }
}
