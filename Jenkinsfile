pipeline {
  agent any
  options { timestamps() }
  triggers { pollSCM('H/5 * * * *') }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/akashjaura16/8.2CDevSecOps.git'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'npm ci || npm install'
        sh 'node -v && npm -v'
      }
    }

    stage('Run Tests') {
      steps {
        sh 'npm test || true'
      }
      post {
        always {
          junit allowEmptyResults: true, testResults: 'reports/**/*.xml'
        }
      }
    }

    stage('Generate Coverage Report') {
      steps {
        sh 'npm run coverage || true'
      }
      post {
        always { archiveArtifacts artifacts: 'coverage/**', allowEmptyArchive: true }
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        sh 'npm audit --audit-level=low || true'
        sh 'npm audit --json > npm-audit.json || true'
      }
      post {
        always { archiveArtifacts artifacts: 'npm-audit.json', allowEmptyArchive: true }
      }
    }

    stage('SonarCloud Analysis') {
      environment {
        SC_VERSION = '5.0.1.3006'
      }
      steps {
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          sh '''
            set -e
            TOOLS_DIR="$WORKSPACE/.tools"
            mkdir -p "$TOOLS_DIR"
            SC_DIR="$TOOLS_DIR/sonar-scanner-$SC_VERSION"

            if [ ! -x "$SC_DIR/bin/sonar-scanner" ]; then
              echo "Downloading sonar-scanner-cli ${SC_VERSION}..."
              curl -fsSL -o "$TOOLS_DIR/scanner.zip" \
                "https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-${SC_VERSION}-linux.zip"
              unzip -q "$TOOLS_DIR/scanner.zip" -d "$TOOLS_DIR"
              mv "$TOOLS_DIR/sonar-scanner-${SC_VERSION}-linux" "$SC_DIR"
            fi

            export PATH="$SC_DIR/bin:$PATH"

            # Ensure coverage file exists
            if [ ! -f coverage/lcov.info ]; then
              echo "Generating coverage report..."
              npm run coverage || true
            fi

            sonar-scanner -Dsonar.login="$SONAR_TOKEN"
          '''
        }
      }
      post {
        always { archiveArtifacts artifacts: '**/.scannerwork/**/*.log', allowEmptyArchive: true }
      }
    }
  }
}
