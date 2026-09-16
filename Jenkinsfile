pipeline {
    agent {
        label 'assignment6-agent'
    }

    environment {
        EC2_SERVER_IP = '3.85.204.187'
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

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Docker Build') {
            steps {
                dir('application/app') {
                    sh "docker build -t hawkhanandev/assignment6-app:${BUILD_NUMBER} -t hawkhanandev/assignment6-app:latest ."
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push hawkhanandev/assignment6-app:${BUILD_NUMBER}
                        docker push hawkhanandev/assignment6-app:latest
                        docker logout
                    '''
                }
            }
        }

        stage('Deploy to AWS EC2') {
            steps {
                sshagent(credentials: ['ec2-ssh-credentials']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no ubuntu@${EC2_SERVER_IP} << 'ENDSSH'
                            docker pull hawkhanandev/assignment6-app:latest
                            docker stop assignment6-deployed-app || true
                            docker rm assignment6-deployed-app || true
                            docker run -d --name assignment6-deployed-app -p 80:80 --restart always hawkhanandev/assignment6-app:latest
                        ENDSSH
                    '''
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                sshagent(credentials: ['ec2-ssh-credentials']) {
                    sh '''
                        sleep 5
                        ssh -o StrictHostKeyChecking=no ubuntu@${EC2_SERVER_IP} "docker ps | grep assignment6-deployed-app"
                        echo "✅ Application deployed and running successfully!"
                    '''
                }
            }
        }
    }

    post {
        success {
            emailext(
                to: 'hawkhanandev@gmail.com',
                subject: "✅ SUCCESS: ${env.JOB_NAME} Build #${env.BUILD_NUMBER}",
                body: """
                    <h2>Pipeline Succeeded!</h2>
                    <p><b>Job:</b> ${env.JOB_NAME}</p>
                    <p><b>Build:</b> #${env.BUILD_NUMBER}</p>
                    <p><b>Status:</b> <span style="color:green;">SUCCESS</span></p>
                    <p><b>Build URL:</b> <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                """,
                mimeType: 'text/html'
            )
        }

        failure {
            emailext(
                to: 'hawkhanandev@gmail.com',
                subject: "❌ FAILURE: ${env.JOB_NAME} Build #${env.BUILD_NUMBER}",
                body: """
                    <h2>Pipeline Failed!</h2>
                    <p><b>Job:</b> ${env.JOB_NAME}</p>
                    <p><b>Build:</b> #${env.BUILD_NUMBER}</p>
                    <p><b>Status:</b> <span style="color:red;">FAILED</span></p>
                    <p><b>Console:</b> <a href="${env.BUILD_URL}console">${env.BUILD_URL}console</a></p>
                """,
                mimeType: 'text/html'
            )
        }
    }
}
