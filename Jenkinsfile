pipeline {
    agent {
        label 'do-agent'
    }
    stages {
        stage('Checkout') {
            steps {
                // Instead of `checkout scm`, explicitly define a GitSCM checkout using SSH
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']], // or whichever branch(es) you want
                    extensions: [],
                    userRemoteConfigs: [[
                        url: 'git@github.com:Asirush/cicd-pipeline.git',
                        credentialsId: 'ab80dd21-4128-4569-9d16-c60f4ef043f4'
                    ]]
                ])
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
                sh 'docker build -t mybuildimage .'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker_hub_creds_id', 
                        usernameVariable: 'DOCKERHUB_USERNAME', 
                        passwordVariable: 'DOCKERHUB_PASSWORD'
                    )
                ]) {
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
