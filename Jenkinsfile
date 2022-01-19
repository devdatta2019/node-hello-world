pipeline {
  agent none
    
  stages {
    stage('Build') {
      agent {
        label 'maven'
      }
      steps {
        checkout scm
        sh './mvnw -DskipTests clean package'
      }
    }
  }
}
