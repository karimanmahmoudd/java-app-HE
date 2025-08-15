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

    stage('Update ArgoCD Manifest') {
      steps {
        dir('gitops-repo') {
          git branch: "${GITOPS_BRANCH}",
              url: "${GITOPS_URL}",
              credentialsId: 'github-credentials-id'

          sh """
            # Update the image tag in the deployment.yaml
            sed -i 's|image: ${IMAGE_REPO}:.*|image: ${IMAGE_REPO}:${IMAGE_TAG}|' ${GITOPS_FILE}

            git config user.email "jenkins@ci"
            git config user.name "Jenkins CI"
            git add ${GITOPS_FILE}
            git commit -m "Update image tag to ${IMAGE_TAG}"
            git push origin ${GITOPS_BRANCH}
          """
        }
      }
    }
  }
}
