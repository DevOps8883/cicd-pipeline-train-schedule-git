pipeline {
    agent any
    environment {
        // Using env.BUILD_NUMBER to keep every version unique
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
                    retry(3) {
                        sh "docker push ${DOCKER_IMAGE}"
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                // Switched to 'file' credential because your KubeConfig plugin is broken
                withCredentials([file(credentialsId: 'kubeconfig-file', variable: 'KUBECONFIG')]) {
                    script {
                        // Dynamically updates the image tag to match the current build number
                        sh "sed -i 's|image:.*|image: ${DOCKER_IMAGE}|g' deployment.yaml"
                        // Uses the --kubeconfig flag to bypass the plugin requirement
                        sh 'kubectl --kubeconfig=${KUBECONFIG} apply -f deployment.yaml'
                    }
                }
            }
        }
    }
}
