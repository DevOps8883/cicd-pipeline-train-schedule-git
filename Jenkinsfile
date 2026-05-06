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
                    sh "docker push ${DOCKER_IMAGE}"
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                // Since you have kubectl installed locally, Jenkins can use the local context
                sh "sed -i 's|davidadeleke23/train-schedule:latest|${DOCKER_IMAGE}|g' deployment.yaml"
                sh "kubectl apply -f deployment.yaml"
            }
        }
    }
}
