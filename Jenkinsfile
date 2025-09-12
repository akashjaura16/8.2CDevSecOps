pipeline {
    agent any
    environment {
        SONAR_TOKEN = credentials('sonar-token')
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/akashjaura16/8.2CDevSecOps.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test -- --coverage'
            }
        }

        stage('Security Scan') {
            steps {
                sh 'npm audit --audit-level=high || true'
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                sh """
                npx sonar-scanner \
                  -Dsonar.projectKey=akashjaura16_8.2CDevSecOps \
                  -Dsonar.organization=akashjaura16 \
                  -Dsonar.host.url=https://sonarcloud.io \
                  -Dsonar.login=$SONAR_TOKEN
                """
            }
        }
    }
}
