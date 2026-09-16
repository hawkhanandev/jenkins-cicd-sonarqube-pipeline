pipeline {
    agent {
        label 'assignment6-agent'
    }

    stages {
        stage('Hello') {
            steps {
                echo 'Jenkins pipeline is working!'
            }
        }

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Application') {
            steps {
                dir('application') {
                    git(
                        branch: 'main',
                        url: 'YOUR_APP_REPO'
                    )
                }
            }
        }

        stage('Checkout Deployment') {
            steps {
                dir('deployment') {
                    git(
                        branch: 'main',
                        url: 'https://github.com/hawkhanandev/jenkins-cicd-sonarqube-pipeline'
                    )
                }
            }
        }
    }
}