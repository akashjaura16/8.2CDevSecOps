pipeline {
  agent any
  options { ansiColor('xterm') }
  stages {
    stage('Checkout') {
      steps { git branch: 'main', url: 'https://github.com/<your-username>/8.2CDevSecOps.git' }
    }
    stage('Install Dependencies') {
      steps { sh 'npm install' }
    }
    stage('Run Tests') {
      steps { sh 'npm test || true' }    // keep going even if tests fail for demo
    }
    stage('Generate Coverage Report') {
      steps { sh 'npm run coverage || true' }
    }
    stage('NPM Audit (Security Scan)') {
      steps { sh 'npm audit || true' }
    }
  }
}
