pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "iheb137/flask-hello-cicd"
        DOCKER_CREDENTIALS = credentials('dockerhub-credentials')
        KUBE_CREDENTIALS = credentials('minikube-credentials')
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/iheb137/flask-hello-cicd.git', credentialsId: 'github-app-credentials'
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
                sh 'python3 -m pytest' // Ajustez selon vos tests, ex: 'pytest tests/'
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
                script {
                    withKubeConfig(credentialsId: 'minikube-credentials', namespace: 'default') {
                        sh 'kubectl apply -f deployment.yaml'
                    }
                }
            }
        }
        stage('Notify Jira') {
            when {
                expression { return false } // Désactivé pour l'instant, à activer avec un credential Jira plus tard
            }
            steps {
                echo 'Jira notification to be implemented'
            }
        }
    }
    post {
        always {
            echo 'Cleaning up...'
            // Ajoutez ici des commandes de nettoyage si nécessaire
        }
    }
}
