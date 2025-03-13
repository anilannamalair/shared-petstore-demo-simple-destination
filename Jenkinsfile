#!/usr/bin/env groovy

@Library('shared-library') _  // Import the shared library, which will be fetched from the repository

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
    stage('Clone Shared Jenkins Library') {
      steps {
        script {
          // Clone the shared Jenkins library repository dynamically (provided via the API)
          String sharedRepoUrl = env.SHARED_LIB_REPO_URL // This will be passed to the pipeline as an environment variable
          cloneSharedLibrary(sharedRepoUrl)  // Call method from shared library
        }
      }
    }

    stage('Build Docker image') {
      steps {
        sharedLibrary.buildDockerImage()  // Call shared library's method for building Docker image
      }
    }

    stage('Test') {
      parallel {
        stage('Test Postgres') {
          steps {
            sharedLibrary.testDatabase("postgres")  // Call shared library's method for testing Postgres
          }
        }

        stage('Test MySQL') {
          steps {
            sharedLibrary.testDatabase("mysql")  // Call shared library's method for testing MySQL
          }
        }

        stage('Test MSSQL') {
          steps {
            sharedLibrary.testDatabase("mssql")  // Call shared library's method for testing MSSQL
          }
        }
      }
    }

    stage('Scan Docker image') {
      parallel {
        stage('Scan Docker image for fixable issues') {
          steps {
            script {
              TAG = sh(returnStdout: true, script: 'cat VERSION')
            }
            sharedLibrary.scanDockerImage("demo-app:${TAG}", "HIGH", false)  // Call shared library's scan method
          }
        }
        stage('Scan Docker image for all issues') {
          steps {
            script {
              TAG = sh(returnStdout: true, script: 'cat VERSION')
            }
            sharedLibrary.scanDockerImage("demo-app:${TAG}", "NONE", true)  // Call shared library's scan method
          }
        }
      }
    }

    stage('Publish Docker image to registry') {
      when {
        tag(pattern: "^v[0-9]+\\.[0-9]+\\.[0-9]+\$", comparator: "REGEXP")
      }

      steps {
        sharedLibrary.publishDockerImage()  // Call shared library's publish method
      }
    }
  }

  post {
    always {
      cleanupAndNotify(currentBuild.currentResult)
    }
  }
}
