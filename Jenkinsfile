pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Git Check-Out'
                git branch: 'main', credentialsId: 'GITOPS_TOKEN_GITHUB', url: 'https://github.com/abraham218/gitops-project-2025.git'
            }
        }
    }
}
