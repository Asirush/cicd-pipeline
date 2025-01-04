pipeline {
    agent { 
        label 'do-agent' 
    }

    stages {
        stage('Checkout') {
            steps {
                // Make sure you have a Git repository configured for your pipeline
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'chmod +x scripts/build.sh'
                sh './scripts/build.sh'
            }
        }

        stage('Test') {
            steps {
                sh 'chmod +x scripts/test.sh'
                sh './scripts/test.sh'
            }
        }

        stage('Docker Build') {
            steps {
                // Build the Docker image with a generic tag (e.g. latest)
                sh 'docker build -t mybuildimage .'
            }
        }

        stage('Docker Push') {
            steps {
                // Use Jenkins credentials to login to Docker registry before pushing
                withCredentials([usernamePassword(
                    credentialsId: 'docker_hub_creds_id', 
                    usernameVariable: 'DOCKERHUB_USERNAME', 
                    passwordVariable: 'DOCKERHUB_PASSWORD'
                )]) {
                    sh """
                        docker login -u \${DOCKERHUB_USERNAME} -p \${DOCKERHUB_PASSWORD}
                        docker tag mybuildimage \${DOCKERHUB_USERNAME}/my-app:\${BUILD_NUMBER}
                        docker push \${DOCKERHUB_USERNAME}/my-app:\${BUILD_NUMBER}
                        docker tag mybuildimage \${DOCKERHUB_USERNAME}/my-app:latest
                        docker push \${DOCKERHUB_USERNAME}/my-app:latest
                    """
                }
            }
        }
    }
}
