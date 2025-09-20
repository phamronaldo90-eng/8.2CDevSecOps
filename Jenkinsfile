pipeline {
  agent any
  tools { nodejs 'node18' }   // thêm dòng này

pipeline {
  agent any
  options { timestamps() }

  environment {
    RECIPIENTS = 'ppn02072005@gmail.com'
    REPO_URL   = 'https://github.com/phamronaldo90-eng/8.2CDevSecOps.git'
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: "${env.REPO_URL}"
        script {
          if (isUnix()) {
            sh 'mkdir -p logs'
          } else {
            bat 'if not exist logs mkdir logs'
          }
        }
      }
    }

    stage('Install Dependencies') {
      steps {
        script {
          if (isUnix()) {
            sh 'npm install'
          } else {
            bat 'npm install'
          }
        }
      }
    }

    stage('Run Tests') {
      steps {
        script {
          if (isUnix()) {
            sh 'npm test 2>&1 | tee logs/test.log || true'
          } else {
            bat 'cmd /c "npm test 1>> logs\\test.log 2>&1" || exit /b 0'
          }
        }
      }
    }

    stage('Notify: After Tests') {
      steps {
        emailext (
          subject: "Jenkins - Tests stage: ${currentBuild.fullDisplayName}",
          body: """Hello,

The Tests stage has completed on job: ${env.JOB_NAME} #${env.BUILD_NUMBER}.
Result so far: ${currentBuild.currentResult}

See attached test log.
""",
          to: "${env.RECIPIENTS}",
          attachmentsPattern: "logs/test.log",
          attachLog: false
        )
      }
    }

    stage('Generate Coverage Report') {
      steps {
        script {
          if (isUnix()) {
            sh 'npm run coverage 2>&1 | tee logs/coverage.log || true'
          } else {
            bat 'cmd /c "npm run coverage 1>> logs\\coverage.log 2>&1" || exit /b 0'
          }
        }
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        script {
          if (isUnix()) {
            sh 'npm audit 2>&1 | tee logs/npm-audit.log || true'
          } else {
            bat 'cmd /c "npm audit 1>> logs\\npm-audit.log 2>&1" || exit /b 0'
          }
        }
      }
    }

    stage('Notify: After Security Scan') {
      steps {
        emailext (
          subject: "Jenkins - Security Scan stage: ${currentBuild.fullDisplayName}",
          body: """Hello,

The Security Scan (npm audit) stage has completed on job: ${env.JOB_NAME} #${env.BUILD_NUMBER}.
Result so far: ${currentBuild.currentResult}

See attached npm audit log.
""",
          to: "${env.RECIPIENTS}",
          attachmentsPattern: "logs/npm-audit.log",
          attachLog: false
        )
      }
    }

    stage('Archive Logs (optional)') {
      steps {
        archiveArtifacts artifacts: 'logs/*.log', fingerprint: true, onlyIfSuccessful: false
      }
    }
  }

  post {
    failure {
      emailext (
        subject: "Jenkins - BUILD FAILED: ${currentBuild.fullDisplayName}",
        body: "Build failed. See attached console log.",
        to: "${env.RECIPIENTS}",
        attachLog: true
      )
    }
    success {
      emailext (
        subject: "Jenkins - BUILD SUCCESS: ${currentBuild.fullDisplayName}",
        body: """Build finished successfully.

Attachments:
- logs/test.log
- logs/npm-audit.log
""",
        to: "${env.RECIPIENTS}",
        attachmentsPattern: "logs/test.log,logs/npm-audit.log",
        attachLog: false
      )
    }
  }
}
