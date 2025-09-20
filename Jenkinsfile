pipeline {
  agent any
  tools { nodejs 'node18' }   // NodeJS tool bạn đã tạo trong Jenkins
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
          from: "${env.FROM_ADDR}",
          to: "${env.RECIPIENTS}",
          subject: "Jenkins - Tests stage: ${currentBuild.fullDisplayName}",
          body: """Hello,

The Tests stage has completed on job: ${env.JOB_NAME} #${env.BUILD_NUMBER}.
Result so far: ${currentBuild.currentResult}

See attached test log.
""",
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
            bat 'cmd /
