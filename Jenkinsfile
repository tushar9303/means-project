pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "tushar9303/means-project:latest"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/tushar9303/means-project.git',
                    credentialsId: 'github-creds'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build(DOCKER_IMAGE)
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub') {
                        docker.image(DOCKER_IMAGE).push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl set image deployment/means-project-deploy \
                means-project-container=${DOCKER_IMAGE}
                kubectl rollout status deployment/means-project-deploy
                '''
            }
        }
    }
}
