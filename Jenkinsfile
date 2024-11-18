pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'ameliamae/nodejschatapp'
        GIT_REPO = 'https://github.com/CWEB2116/NodejsChatApp.git'
        // Removed SONAR_SCANNER_HOME since we'll use the plugin
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning GitHub Repository...'
                git branch: 'main', url: "${env.GIT_REPO}"
            }
        }

        stage('Snyk Security Scan') {
            steps {
                echo 'Running Snyk Security Scan...'
                snykSecurity(
                    snykInstallation: 'Default', // Use 'Default' unless you have multiple installations
                    snykTokenId: 'SNYK_TOKEN',
                    severity: 'high',
                    failOnIssues: true,
                    monitorProjectOnBuild: false
                )
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo 'Running SonarQube Analysis...'
                withSonarQubeEnv('YourSonarQubeServerName') { // Replace with your SonarQube server name in Jenkins
                    // Use the embedded SonarScanner
                    sh 'sonar-scanner \
                        -Dsonar.projectKey=NodeJSChatApp \
                        -Dsonar.sources=. \
                        -Dsonar.exclusions=node_modules/**'
                }
            }
        }

        stage('Build and Tag Docker Image with Compose') {
            steps {
                script {
                    echo 'Building and Tagging Docker Image with Docker Compose...'
                    sh "docker compose -f docker-compose.yml build app"
                    sh "docker tag nodejschatapp ${DOCKER_IMAGE}:latest"
                    sh "docker tag nodejschatapp ${DOCKER_IMAGE}:${BUILD_NUMBER}"
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    echo 'Pushing Docker Image to Docker Hub...'
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                        sh "echo \$DOCKERHUB_PASSWORD | docker login -u \$DOCKERHUB_USERNAME --password-stdin"
                        sh "docker push ${DOCKER_IMAGE}:latest"
                        sh "docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}"
                    }
                }
            }
        }

        stage('Run Application with Docker Compose') {
            steps {
                script {
                    echo 'Pulling and Running Docker Image with Docker Compose...'
                    sh "docker pull ${DOCKER_IMAGE}:latest"
                    sh "docker compose -f docker-compose.yml up -d"
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up Docker resources...'
            // Optionally, clean up Docker resources if needed
            // sh "docker compose -f docker-compose.yml down"
            // sh "docker system prune -f"
        }
    }
}
