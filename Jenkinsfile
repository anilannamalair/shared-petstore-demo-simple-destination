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
                sh 'mvn clean install -DskipTests' // This will install dependencies and skip tests
            }
        }
        stage('Run Unit Tests') {
            steps {
                sh 'mvn test' // Run unit tests using Maven
            }
        }
        stage('Build Artifact') {
            steps {
                sh 'mvn package' // This will build the Maven artifact (e.g., JAR or WAR file)
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
