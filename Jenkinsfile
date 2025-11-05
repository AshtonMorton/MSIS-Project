pipeline {
  agent any
  options { timestamps(); disableConcurrentBuilds() }

  environment {
    PUBLISH_DIR = 'out'
  }

  stages {
    stage('Checkout') { steps { checkout scm } }

    stage('Setup .NET') {
      steps {
        // If you installed .NET SDK on the Jenkins box, this is enough.
        bat 'dotnet --info'
      }
    }

    stage('Restore') { steps { bat 'dotnet restore' } }

    stage('Build')   { steps { bat 'dotnet build -c Release --no-restore' } }

    // (Optional) keep this if/when you add tests
    stage('Test')    { when { expression { fileExists('**/*Tests.csproj') } }
      steps { bat 'dotnet test -c Release --no-build --verbosity normal' }
    }

    stage('Publish') {
      steps {
        bat 'dotnet publish -c Release -o out --no-build'
      }
    }

    stage('Archive Artifacts') {
      steps {
        powershell 'Compress-Archive -Path out\\* -DestinationPath out\\site.zip -Force'
        archiveArtifacts artifacts: 'out/**', fingerprint: true
      }
    }
  }

  post {
    success { echo '✅ CI succeeded. (Deploy happens via GitHub Actions.)' }
    failure { echo '❌ CI failed. Check logs above.' }
  }
}
