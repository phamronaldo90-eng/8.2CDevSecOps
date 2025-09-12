pipeline {
  agent any
  triggers {
    pollSCM('H/2 * * * *') // check every 2 minutes for new commits
  }
  stages {
    stage('Build') {
      steps { echo 'Building with Maven/npm...' }
    }
    stage('Unit & Integration Tests') {
      steps { echo 'Running unit + integration tests with JUnit/Jest...' }
    }
    stage('Code Analysis') {
      steps { echo 'Analyzing code with SonarQube/ESLint/Checkstyle...' }
    }
    stage('Security Scan') {
      steps { echo 'Scanning with npm audit / Snyk...' }
    }
    stage('Deploy to Staging') {
      steps { echo 'Deploying app to staging server...' }
    }
    stage('Integration Tests on Staging') {
      steps { echo 'Running E2E tests with Cypress/Postman...' }
    }
    stage('Deploy to Production') {
      steps { echo 'Deploying to production environment...' }
    }
  }
}
