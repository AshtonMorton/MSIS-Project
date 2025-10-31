pipeline {
  agent any
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }
    stage('Build') {
      steps {
        bat 'echo Build successful! && dir'
      }
    }
    stage('Archive') {
      steps {
        bat 'mkdir out && echo hello > out\\artifact.txt'
        archiveArtifacts artifacts: 'out/**', fingerprint: true
      }
    }
  }
}
