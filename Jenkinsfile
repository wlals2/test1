pipeline {
  agent any

  environment {
    IMAGE_REPO = 'fw4568/company-infra-jenkins'
    REGISTRY_CREDENTIALS = 'dockerhub-id'
    BRANCH = 'main'
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: "${BRANCH}", url: 'https://github.com/wlals2/test1.git'
      }
    }
    stage('Build') {
      steps {
        sh "docker build -t ${IMAGE_REPO}:${BUILD_NUMBER} ./jenkins"
      }
    }
    stage('Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: "${REGISTRY_CREDENTIALS}", passwordVariable: 'DOCKERHUB_PASS', usernameVariable: 'DOCKERHUB_USER')]) {
          sh '''
            echo $DOCKERHUB_PASS | docker login -u $DOCKERHUB_USER --password-stdin
            docker push ${IMAGE_REPO}:${BUILD_NUMBER}
          '''
        }
      }
    }
    stage('Update values.yaml') {
      steps {
        script {
          sh "sed -i 's/\(repository: fw4568\\/company-infra-jenkins\\s*tag: \\).*/\\1${BUILD_NUMBER}/' values.yaml"
          sh '''
            git config --global user.email "fw4568@gmail.com"
            git config --global user.name "jenkins-ci"
            git add values.yaml
            git commit -m "Update Jenkins image tag"
            git push origin ${BRANCH}
          '''
        }
      }
    }
  }
}

