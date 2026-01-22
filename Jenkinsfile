pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "tushar9303/means-project:latest" // Docker image name
        KUBE_DEPLOYMENT = "means-project-deploy"         // Kubernetes deployment name
        KUBE_CONTAINER = "means-project-container"      // Container name in deployment
        KUBE_NAMESPACE = "default"                       // Namespace (change if needed)
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', 
                    url: 'https://github.com/tushar5464/means-project.git',
                    credentialsId: 'github-creds' // GitHub credentials
            }
        }

   
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE} ." // Build Docker image
            }
        }

   
        stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-hub',    // DockerHub credentials ID in Jenkins
                    usernameVariable: 'DOCKER_USER', 
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    '''
                }
            }
        }

     
        stage('Push Docker Image') {
            steps {
                sh "docker push ${DOCKER_IMAGE}" // Push Docker image to DockerHub
            }
        }

      
        stage('Deploy to Kubernetes') {
            steps {
                sh """
                # Update image if deployment exists
                kubectl -n ${KUBE_NAMESPACE} set image deployment/${KUBE_DEPLOYMENT} \
                ${KUBE_CONTAINER}=${DOCKER_IMAGE} || \
                # If deployment doesn't exist, create it using a YAML manifest
                kubectl -n ${KUBE_NAMESPACE} apply -f k8s-deployment.yaml

                # Wait for rollout to complete
                kubectl -n ${KUBE_NAMESPACE} rollout status deployment/${KUBE_DEPLOYMENT}
                """
            }
        }
    }
}


