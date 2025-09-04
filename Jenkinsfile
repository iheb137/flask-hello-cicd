pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "iheb137/flask-hello-cicd"
        DOCKER_CREDENTIALS = credentials('dockerhub-credentials')
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/iheb137/flask-hello-cicd.git', credentialsId: 'github-credentials', branch: 'main'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build(DOCKER_IMAGE)
                }
            }
        }
        stage('Test') {
            steps {
                sh 'python3 -m pytest' // Ajustez selon votre structure de tests (ex: 'pytest tests/')
            }
        }
        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
                        docker.image(DOCKER_IMAGE).push("${env.BUILD_NUMBER}")
                        docker.image(DOCKER_IMAGE).push("latest")
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                withKubeConfig([credentialsId: 'minikube-kubeconfig', namespace: 'default']) {
                    sh 'kubectl apply -f deployment.yaml'
                }
            }
        }
    }
    post {
        always {
            echo 'Cleaning up...'
            // Ajoutez des commandes de nettoyage si nécessaire (ex: suppression d'images temporaires)
        }
    }
}
