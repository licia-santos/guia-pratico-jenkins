pipeline {
    agent any

    triggers {
        pollSCM('')
    }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    dockerapp = docker.build("liciasantos/guia-jenkins:${env.BUILD_ID}", '-f ./src/Dockerfile ./src')
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        dockerapp.push('latest')
                        dockerapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }

        stage('Deploy no Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig-minikube-inline', variable: 'KUBECONFIG')]) {
                    sh "sed -i 's/{{tag}}/${env.BUILD_ID}/g' ./k8s/deployment.yaml"
                    sh 'kubectl apply -f k8s/deployment.yaml'
                }
            }
        }

    }
}

