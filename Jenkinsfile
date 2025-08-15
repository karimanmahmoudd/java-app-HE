pipeline {
  agent any

  tools { 
    maven 'maven-tools'
    git 'git-v1.0'
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

    stage('Update ArgoCD') {
      steps {
          // Checkout repo using Jenkins credentials
          git branch: 'main',
              credentialsId: 'github-credentials',
              url: 'git@github.com:karimanmahmoudd/argocd-java-project.git'
  
          // Now run shell commands
          sh """
              pwd
              ls
              sed -i "s|image: .*|image: ${IMAGE_NAME}:v${IMAGE_TAG}|" deployment.yml
  
              git config user.email "jenkins@example.com"
              git config user.name "Jenkins CI"
  
              git add .
              git commit -m "update image"
              git push
          """
      }
  }

  }
}
