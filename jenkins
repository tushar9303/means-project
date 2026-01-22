pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "tushar9303/means-project:latest"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/tushar9303/means-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    script {
                        docker.withRegistry('https://index.docker.io/v1/', 'docker-hub') {
                            docker.image("${DOCKER_IMAGE}").push()
                        }
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh """
                    kubectl set image deployment/means-project-deploy means-project-container=${DOCKER_IMAGE} --record
                    kubectl rollout status deployment/means-project-deploy
                    """
                }
            }
        }
    }
}
