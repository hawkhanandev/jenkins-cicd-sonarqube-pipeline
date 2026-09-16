pipeline {
    agent {
        label 'assignment6-agent'
    }

    stages {
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
                        url: 'https://github.com/hawkhanandev/Ansible-Roles-Docker-Multi-Stage-Builds'
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

        stage('Verify Agent Environment') {
            steps {
                echo '=== 1. Tool Availability Check ==='
                sh 'git --version'
                sh 'node -v || echo "Node.js not installed"'
                sh 'npm -v || echo "npm not installed"'
                sh 'docker --version || echo "Docker CLI not installed"'
                
                echo '=== 2. Checked-Out Application Structure ==='
                sh 'ls -la application/'
                sh 'ls -la application/app/'
            }
        }
    }
}
