pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: "https://github.com/jfrog/project-examples.git"
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean install' // Basic Maven build
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test' // Basic Maven test
            }
        }
        stage('Deploy') {
            steps {
                echo 'Simulating deployment' // Placeholder for deployment steps
            }
        }
    }
}
