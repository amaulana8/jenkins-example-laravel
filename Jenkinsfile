pipeline {
  agent any
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }
  environment {
    HEROKU_API_KEY = credentials('nexus-pass')
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
        sh 'echo $HEROKU_API_KEY | docker login --username=_ --password-stdin http://nexus-lb.nexus.scv.cluster.local'
      }
    }
    stage('Push to Nexus registry') {
      steps {
        sh '''
          docker tag $IMAGE_NAME:$IMAGE_TAG nexus-lb.nexus.scv.cluster.local/$APP_NAME/web
          docker push nexus-lb.nexus.scv.cluster.local/$APP_NAME/web
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
