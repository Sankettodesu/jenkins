pipeline {
    agent any

    environment {
        // Change these to your actual details
        DOCKER_IMAGE = "my-portfolio-web"
        REMOTE_USER  = "ubuntu" 
        REMOTE_HOST  = "192.168.174.131" 
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build the image on the Rocky Linux box
                sh "docker build -t ${DOCKER_IMAGE}:latest ."
            }
        }

        stage('Deploy to Production') {
            steps {
                // This command tells Ubuntu to pull the code and run it
                // We use SSH to execute commands remotely
                sh """
                ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_HOST} '
                    docker stop portfolio-site || true
                    docker rm portfolio-site || true
                    docker run -d --name portfolio-site -p 80:80 ${DOCKER_IMAGE}:latest
                '
                """
            }
        }
    }
}
