pipeline {
  agent any

  tools { 
    maven 'maven-tools' 
  }

  environment {
    DOCKER_USER = credentials('kariman-docker')
    DOCKER_PASS = credentials('password-docker')
    IMAGE_REPO  = 'karimanmahmoud/app-java'
    IMAGE_TAG   = "v${env.BUILD_NUMBER}"

    GITOPS_URL    = 'https://github.com/karimanmahmoudd/argocd-java-project.git'
    GITOPS_BRANCH = 'main'
    GITOPS_FILE   = 'deployment.yaml'
  }

  stages {
    stage('Checkout') { 
      steps { 
        git 'https://github.com/Hassan-Eid-Hassan/java.git' 
      } 
    }

    stage('Build & Test') { 
      steps { 
        sh 'mvn -B clean package' 
      } 
    }

    stage('Docker build & push') {
      steps {
        sh """
          docker build -t ${IMAGE_REPO}:${IMAGE_TAG} .
          echo "${DOCKER_PASS}" | docker login -u "${DOCKER_USER}" --password-stdin
          docker push ${IMAGE_REPO}:${IMAGE_TAG}
          docker logout || true
        """
      }
    }
  }
}
