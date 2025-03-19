pipeline {
    agent any
    options {
        timestamps()
    }
    stages {
        stage('Checkout Source Code') {
            steps {
                git branch: 'main', url: 'https://github.com/your-repo/your-project.git' // Replace with your repo
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'npm install' // Or 'pip install -r requirements.txt', 'mvn dependency:go-offline', etc.
            }
        }
        stage('Run Unit Tests') {
            steps {
                sh 'npm test' // Or 'pytest', 'mvn test', etc.
            }
        }
        stage('Build Artifact') {
            steps {
                sh 'npm run build' // Or 'mvn package', 'docker build -t my-image .', etc.
            }
        }
        stage('Deploy to Development') {
            steps {
                echo 'Deploying to development environment...'
                // Add deployment steps here (e.g., deploy to a container registry, push to a server)
            }
        }
    }
}
