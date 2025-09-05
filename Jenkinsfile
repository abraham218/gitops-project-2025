pipeline {
    agent any
	tools {
		nodejs 'NodeJS'
	}
	environment {
		DOCKER_HUB_REPO = 'abraham218/gitopsapp'
	}

    stages {
        stage('Checkout') {
            steps {
                echo 'Git Check-Out'
                git branch: 'main', credentialsId: 'GITOPS_TOKEN_GITHUB', url: 'https://github.com/abraham218/gitops-project-2025.git'
            }
        }
		stage('Install Nodejs'){
			steps{
				echo "installing npm dependencies"
				sh 'npm install'
			}
		}
		stage('Build Docker Image') {
			steps{
				script{
					echo "Buikding Docker Image"
					docker.image("${DOCKER_HUB_REPO}:latest")
				}
			}
		}
    }
}
