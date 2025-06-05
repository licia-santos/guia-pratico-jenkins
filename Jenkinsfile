pipeline {
  agent any

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
        withKubeConfig([credentialsId: 'kubeconfig']) {
        sh """
            export TAG_VERSION=${env.BUILD_ID}
            envsubst < ./k8s/deployment.yaml | kubectl apply -f -
        """
    }
  }
}

  }
}
