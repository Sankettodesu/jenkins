pipeline {
    agent any

    environment {
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
                // We build it locally on the Jenkins (Rocky) node
                sh "docker build -t ${DOCKER_IMAGE}:latest ."
            }
        }

        stage('Deploy to Production') {
            steps {
                // This is the "Magic" line: 
                // 1. 'docker save' turns the image into a data stream
                // 2. '| ssh' sends that stream to Ubuntu
                // 3. 'docker load' on Ubuntu turns that stream back into an image
                sh """
                docker save ${DOCKER_IMAGE}:latest | ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_HOST} '
                    docker load
                    docker stop portfolio-site || true
                    docker rm portfolio-site || true
                    docker run -d --name portfolio-site -p 8081:80 ${DOCKER_IMAGE}:latest
                '
                """
            }
        }
    }
}
