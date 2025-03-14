@Library('my-shared-library') _  // Reference the shared library

pipeline {
    agent any

    stages {
         stage('Hello') {
            steps {
                script {
                    sayHello("Anil")
                }
            }
        }
        stage('Install Maven Dependencies') {
            steps {
                script {
                    installMavenDependencies()
                }
            }
        }
        stage('Build') {
            steps {
                script {
                    buildProjectWithMaven()
                }
            }
        }
         stage('Run Tests') {
            steps {
                script {
                    runTestsWithMaven()
                }
            }
        }
        
    }
}
