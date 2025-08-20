pipeline {
  agent any
  tools { maven 'Maven' }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Build') {
      steps { sh 'mvn -q clean compile' }
    }
    stage('Test') {
      steps { sh 'mvn -q test' }
      post { always { junit 'target/surefire-reports/*.xml' } }
    }
    stage('Package') {
      steps { sh 'mvn -q package' }
    }
    stage('Deploy') {
      steps {
        sh 'mkdir -p /opt/deployments && cp target/*.jar /opt/deployments/'
      }
    }
    stage('Run Deployed App') {
        steps {
            sh 'java -jar target/my-java-app-1.0-SNAPSHOT.jar'
        }
    }
  }
  post {
    success { echo '✅ Build, Test, Deploy done.' }
    failure { echo '❌ Pipeline failed.' }
  }
}
