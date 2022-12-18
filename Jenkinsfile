pipeline {
  agent any
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }
  environment {
    NEXUS_CREDENTIAL_ID = credentials('nexus-app')
    IMAGE_NAME = 'darinpope/jenkins-example-laravel'
    IMAGE_TAG = 'latest'
    APP_NAME = 'jenkins-example-laravel'
  }
  stages {
    stage('Build') {
      steps {
        sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
      }
    }
    stage('Login') {
      steps {
        sh 'echo $NEXUS_CREDENTIAL_ID | docker login --username=_ --password-stdin http://nexus-lb.nexus.svc.cluster.local'
      }
    }
    stage('Push to Nexus registry') {
      steps {
        sh '''
          docker tag $IMAGE_NAME:$IMAGE_TAG nexus-lb.nexus.svc.cluster.local/$APP_NAME/web
          docker push nexus-lb.nexus.svc.cluster.local/$APP_NAME/web
        '''
      }
    }
    stage('Release the image') {
      steps {
        sh '''
          heroku container:release web --app=$APP_NAME
        '''
      }
    }
  }
  post {
    always {
      sh 'docker logout'
    }
  }
}
