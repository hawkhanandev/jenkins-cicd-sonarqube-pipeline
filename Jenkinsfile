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

        stage('Verify Checkout') {
            steps {
                sh 'find . -maxdepth 2 -type f | head -50'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''
                        sonar-scanner \
                          -Dsonar.projectKey=assignment6-app \
                          -Dsonar.projectName="Assignment 6 App" \
                          -Dsonar.sources=application/app/src \
                          -Dsonar.host.url=http://assignment6-sonarqube:9000
                    '''
                }
            }
        }
    }
}
