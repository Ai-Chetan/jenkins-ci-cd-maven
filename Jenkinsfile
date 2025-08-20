pipeline {
  agent any
  tools { maven 'Maven' }  // Jenkins > Global Tool Configuration name

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
        // Example: copy artifact to a deployment folder inside the agent
        sh 'mkdir -p /opt/deployments && cp target/*.jar /opt/deployments/'
      }
    }
  }
  post {
    success { echo '✅ Build, Test, Deploy done.' }
    failure { echo '❌ Pipeline failed.' }
  }
}
