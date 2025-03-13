@Library('my-shared-library-') _  // Reference the shared library

pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                script {
                    // Assuming your buildProject.groovy is under 'vars' in the shared library
                    buildProjectWithMaven()
                }
            }
        }
    }
}
