pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "davidadeleke23/train-schedule:${env.BUILD_NUMBER}"
    }
    stages {
        stage('Build Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh 'docker login -u ${USER} -p ${PASS}'
            // This will try the push up to 3 times if it fails with an EOF error
            retry(3) {
                    sh 'docker push davidadeleke23/train-schedule:4'
                  }
               }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                // Use the Kubernetes CLI plugin helper
                  withKubeConfig([credentialsId: 'kubeconfig-file']) {
               sh "sed -i 's|davidadeleke23/train-schedule:latest|davidadeleke23/train-schedule:5|g' deployment.yaml"
               sh 'kubectl apply -f deployment.yaml'
            }
        }
    }
}
