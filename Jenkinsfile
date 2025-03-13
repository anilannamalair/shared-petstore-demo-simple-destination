@Library('shared-jenkins-library') _  // Load the shared library

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
                    // Assuming 'buildAndTest' is a function in your shared library
                    buildAndTest.buildDockerImage()
                }
            }
        }

        stage('Test') {
            parallel {
                stage('Test Postgres') {
                    steps {
                        script {
                            buildAndTest.testPostgres()
                        }
                    }
                }

                stage('Test MySQL') {
                    steps {
                        script {
                            buildAndTest.testMySQL()
                        }
                    }
                }

                stage('Test MSSQL') {
                    steps {
                        script {
                            buildAndTest.testMSSQL()
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
                            buildAndTest.scanImage(TAG, "HIGH", false)
                        }
                    }
                }
                stage('Scan Docker image for all issues') {
                    steps {
                        script {
                            TAG = sh(returnStdout: true, script: 'cat VERSION').trim()
                            buildAndTest.scanImage(TAG, "NONE", true)
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
                    buildAndTest.publishImage()
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
