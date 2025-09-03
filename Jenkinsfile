pipeline {
agent any
environment {
DOCKER_IMAGE = "iheb99/flask-hello:latest"  // Remplacez par votre Docker Hub
}
stages {
stage("Checkout") {
steps {
git url: "https://github.com/votreuser/flask-hello-cicd.git", credentialsId: "github"
}
}
stage("Build Docker Image") {
steps {
script {
dockerImage = docker.build("${DOCKER_IMAGE}")
}
}
}
stage("Test") {
steps {
sh "docker run ${DOCKER_IMAGE} python -m pytest tests/"
}
}
stage("Push to Docker Hub") {
steps {
script {
docker.withRegistry("https://registry.hub.docker.com", "dockerhub") {
dockerImage.push()
}
}
}
}
stage("Deploy to Kubernetes") {
steps {
bat "kubectl apply -f deployment.yaml"
}
}
stage("Notify Jira") {
steps {
jiraComment body: "Build successful! Deployed to Kubernetes.", issueKey: "PROJ-123"  // Remplacez par votre issue Jira
}
}
}
post {
always {
echo "Cleaning up..."
}
}
}
