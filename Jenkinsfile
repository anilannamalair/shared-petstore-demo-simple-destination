@Library('my-shared-library') _  // Load the shared library

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
                    org.example.BuildAndTest.buildDockerImage()
                }
            }
        }

        stage('Test') {
            parallel {
                stage('Test Postgres') {
                    steps {
                        script {
                            org.example.BuildAndTest.testPostgres()
                        }
                    }
                }

                stage('Test MySQL') {
                    steps {
                        script {
                            org.example.BuildAndTest.testMySQL()
                        }
                    }
                }

                stage('Test MSSQL') {
                    steps {
                        script {
                            org.example.BuildAndTest.testMSSQL()
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
                            TAG = sh(returnStdout: true, script: 'cat VERSION')
                            org.example.BuildAndTest.scanImage(TAG, "HIGH", false)
                        }
                    }
                }
                stage('Scan Docker image for all issues') {
                    steps {
                        script {
                            TAG = sh(returnStdout: true, script: 'cat VERSION')
                            org.example.BuildAndTest.scanImage(TAG, "NONE", true)
                        }
                    }
                }
            }
        }

        stage('Publish Docker image to registry') {
            when {
                tag(pattern: "^v[0-9]+\\.[0-9]+\\.[0-9]+\$", comparator: "REGEXP")
            }
            steps {
                script {
                    org.example.BuildAndTest.publishImage()
                }
            }
        }
    }

    post {
        always {
            cleanupAndNotify(currentBuild.currentResult)
        }
    }
}
