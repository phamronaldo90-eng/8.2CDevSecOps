pipeline {
  agent any
  tools { nodejs 'node18' }
  options { timestamps() }

  environment {
    RECIPIENTS = 'ppn02072005@gmail.com'
    FROM_ADDR  = 'ppn02072005@gmail.com'
    REPO_URL   = 'https://github.com/phamronaldo90-eng/8.2CDevSecOps.git'
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: "${env.REPO_URL}"
        bat 'if not exist logs mkdir logs'
      }
    }

    stage('Install Dependencies') {
      steps { bat 'npm install' }
    }

   stage('Run Tests') {
  steps { bat 'cmd /c npm test 1>> logs\\test.log 2>&1 || exit /b 0' }
}
    stage('Notify: After Tests') {
      steps {
        emailext(
          from: "${env.FROM_ADDR}",
          to: "${env.RECIPIENTS}",
          subject: "Jenkins - Tests stage: ${currentBuild.fullDisplayName}",
          body: "Tests stage finished. See attached test log.",
          attachmentsPattern: "logs/test.log",
          attachLog: false
        )
      }
    }

    stage('Generate Coverage Report') {
      steps { bat 'cmd /c npm run coverage 1>> logs\\coverage.log 2>&1' }
    }

    stage('NPM Audit (Security Scan)') {
      steps { bat 'cmd /c npm audit 1>> logs\\npm-audit.log 2>&1' }
    }

    stage('Notify: After Security Scan') {
      steps {
        emailext(
          from: "${env.FROM_ADDR}",
          to: "${env.RECIPIENTS}",
          subject: "Jenkins - Security Scan stage: ${currentBuild.fullDisplayName}",
          body: "Security Scan finished. See attached npm-audit log.",
          attachmentsPattern: "logs/npm-audit.log",
          attachLog: false
        )
      }
    }

    stage('Archive Logs (optional)') {
      steps { archiveArtifacts artifacts: 'logs/*.log', fingerprint: true, onlyIfSuccessful: false }
    }
  }

  post {
    failure {
      emailext(
        from: "${env.FROM_ADDR}",
        to: "${env.RECIPIENTS}",
        subject: "Jenkins - BUILD FAILED: ${currentBuild.fullDisplayName}",
        body: "Build failed. Console log attached.",
        attachLog: true
      )
    }
    success {
      emailext(
        from: "${env.FROM_ADDR}",
        to: "${env.RECIPIENTS}",
        subject: "Jenkins - BUILD SUCCESS: ${currentBuild.fullDisplayName}",
        body: "Build finished successfully. Logs attached.",
        attachmentsPattern: "logs/test.log,logs/npm-audit.log",
        attachLog: false
      )
    }
  }
}
