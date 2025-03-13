#!/usr/bin/env groovy

@Library('shared-library') _  // Assuming the library is named 'your-shared-library'

pipeline {
  agent { label 'executor-v2' }

  options {
    timestamps()
    buildDiscarder(logRotator(numToKeepStr: '30'))
  }

  triggers {
    cron(getDailyCronString())
  }

  stages {
    stage('Build Docker image') {
      steps {
        script {
          buildApplication()  // Using the buildApplication script from the shared library
        }
      }
    }

    stage('Test') {
      parallel {
        stage('Test Postgres') {
          steps {
            script {
              testDatabases("postgres")  // Using the testDatabases script from the shared library
            }
          }
        }

        stage('Test MySQL') {
          steps {
            script {
              testDatabases("mysql")  // Using the testDatabases script from the shared library
            }
          }
        }

        stage('Test MSSQL') {
          steps {
            script {
              testDatabases("mssql")  // Using the testDatabases script from the shared library
            }
          }
        }
      }
    }

    stage('Scan Docker image') {
      parallel {
        stage('Scan Docker image for fixable issues') {
          steps {
            script {
              TAG = sh(returnStdout: true, script: 'cat VERSION').trim()
              scanAndReport("demo-app:${TAG}", "HIGH", false)  // Assuming scanAndReport is a method in your library
            }
          }
        }
        stage('Scan Docker image for all issues') {
          steps {
            script {
              TAG = sh(returnStdout: true, script: 'cat VERSION').trim()
              scanAndReport("demo-app:${TAG}", "NONE", true)  // Assuming scanAndReport is a method in your library
            }
          }
        }
      }
    }

    stage('Publish Docker image to registry') {
      when {
        tag(
          pattern: "^v[0-9]+\\.[0-9]+\\.[0-9]+\$",
          comparator: "REGEXP"
        )
      }

      steps {
        script {
          sh './bin/publish'  // Assuming publish logic is not abstracted in the shared library
        }
      }
    }
  }

  post {
    always {
      script {
        cleanupAndNotify(currentBuild.currentResult)  // Assuming cleanupAndNotify is a method in your library
      }
    }
  }
}

