pipeline {
  agent any
  tools { nodejs 'Node18' }   // must match the Name field in your screenshot

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/akashjaura16/8.2CDevSecOps.git'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'npm ci || npm install'
      }
    }

    stage('Run Tests') {
      steps {
        sh 'npm test || true'
      }
    }

    stage('Coverage') {
      steps {
        sh 'npm run coverage || true'
      }
    }

    stage('NPM Audit') {
      steps {
        sh 'npm audit --audit-level=low || true'
      }
    }

    stage('SonarCloud Analysis') {
      steps {
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          sh 'sonar-scanner -Dsonar.login=$SONAR_TOKEN || true'
        }
      }
    }
  }
}
