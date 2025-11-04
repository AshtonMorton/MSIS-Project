pipeline {
  agent any
  environment {
    WEBAPP_NAME = 'msis-project'    
    PUBLISH_DIR = 'out'
    ZIP_PATH    = 'out/site.zip'
  }
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }
    stage('Restore') {
      steps {
        bat 'dotnet restore'
      }
    }
    stage('Build') {
      steps {
        bat 'dotnet build -c Release --no-restore'
      }
    }
    stage('Publish') {
      steps {
        bat 'dotnet publish -c Release -o out --no-build'
      }
    }
    stage('Package') {
      steps {
        powershell 'Compress-Archive -Path out\\* -DestinationPath out\\site.zip -Force'
        archiveArtifacts artifacts: 'out/**', fingerprint: true
      }
    }
    stage('Deploy to Azure') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'azure-publish',
          usernameVariable: 'AZ_USER',
          passwordVariable: 'AZ_PWD'
        )]) {
          bat '''
C:\\Windows\\System32\\curl.exe -X POST -u %AZ_USER%:%AZ_PWD% --data-binary @%ZIP_PATH% https://%WEBAPP_NAME%.scm.azurewebsites.net/api/zipdeploy
'''
        }
      }
    }
  }
}
