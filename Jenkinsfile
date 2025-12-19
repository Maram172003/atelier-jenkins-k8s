pipeline {
  agent any

  environment {
    DOCKERHUB_USER = 'maram172003'
    IMAGE_SERVER   = "${DOCKERHUB_USER}/atelier4-serveur"
    IMAGE_CLIENT   = "${DOCKERHUB_USER}/atelier4-client"
    IMAGE_TAG      = "build-${BUILD_NUMBER}"
    KUBECONFIG     = "/home/maram/.kube/config"
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Docker Build') {
      steps {
        sh """
          set -e
          docker build -t ${IMAGE_SERVER}:${IMAGE_TAG} ./serveur
          docker build -t ${IMAGE_CLIENT}:${IMAGE_TAG} ./client
          docker images | grep atelier4 || true
        """
      }
    }

    stage('Docker Push') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'docker-hub-credentials',
          usernameVariable: 'DH_USER',
          passwordVariable: 'DH_PASS'
        )]) {
          sh """
            set -e
            echo "\$DH_PASS" | docker login -u "\$DH_USER" --password-stdin
            docker push ${IMAGE_SERVER}:${IMAGE_TAG}
            docker push ${IMAGE_CLIENT}:${IMAGE_TAG}
          """
        }
      }
    }

    stage('Deploy to K8s') {
      steps {
        sh """
          set -e
          export KUBECONFIG=${KUBECONFIG}

          sed -i "s|maram172003/atelier4-serveur:TAG|${IMAGE_SERVER}:${IMAGE_TAG}|g" ci-cd-config/k8s-serveur-deployment.yaml
          sed -i "s|maram172003/atelier4-client:TAG|${IMAGE_CLIENT}:${IMAGE_TAG}|g" ci-cd-config/k8s-client-deployment.yaml

          kubectl apply --validate=false -f ci-cd-config/k8s-serveur-deployment.yaml
          kubectl apply --validate=false -f ci-cd-config/k8s-client-deployment.yaml
        """
      }
    }
  }
}

