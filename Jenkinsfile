pipeline {
    agent any
	tools {
		nodejs 'NodeJS'
	}
	environment {
		DOCKER_HUB_REPO = 'abraham218/gitopsapp'
		DOCKER_HUB_CREDENTIALS_ID = 'DOCKER_HUB_CREDS'
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
					echo "Building Docker Image"
					def app = docker.build("${DOCKER_HUB_REPO}:latest")
				}
			}
		}
		stage('Trivy Scan') {
			steps {
				//sh 'trivy image --severity HIGH,CRITICAL --no-progress --format table -o trivy-scan-report.txt ${DOCKER_HUB_REPO}:latest'
				sh 'trivy image --severity HIGH,CRITICAL --skip-update --no-progress --format table -o trivy-scan-report.txt ${DOCKER_HUB_REPO}:latest'
			}
		}
		stage('DockerHub Push') {
			steps{
				script {
					docker.withRegistry('https://registry.hub.docker.com', "${DOCKER_HUB_CREDENTIALS_ID}") {
						def app = docker.build("${DOCKER_HUB_REPO}:latest")
						app.push()
					}
				}
			}
		}
		stage('Install Kubectl & ArgoCD CLI') {
			steps {
				sh '''
				echo 'installing Kubectl & ArgoCD cli...'
				curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
				chmod +x kubectl
				mv kubectl /usr/local/bin/kubectl
				curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
				chmod +x /usr/local/bin/argocd
				'''
			}
		}
		stage('Apply Kubernetes Manifests & Sync App with ArgoCD') {
			steps {
				script {
					kubeconfig(credentialsId: 'kubeconfig', serverUrl: 'https://192.168.49.2:8443') {
						sh '''
						argocd login --insecure 192.168.0.60:8081 --username admin --password 6APJLVyWlqmkTfgahuF071jd9GeXisZB
						echo "Syncing Application..."
						argocd app sync argocdjenkins
						'''
					}
				}
			}
		}
    }
}
