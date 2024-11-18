pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'ameliamae/nodejschatapp'
        GIT_REPO = 'https://github.com/CWEB2116/NodejsChatApp.git'
        SONAR_SCANNER_HOME = tool 'SonarQube-Installation' // Ensure this matches your SonarQube scanner installation name in Jenkins
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning GitHub Repository...'
                git branch: 'main', url: "${env.GIT_REPO}"
            }
        }

        // Removed 'Install Dependencies' stage since node_modules are included

        stage('Snyk Security Scan') {
            steps {
                echo 'Running Snyk SCA and SAST Scans...'
                withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
                    sh 'npm install -g snyk' // Install Snyk CLI
                    sh 'snyk auth $SNYK_TOKEN'
                    // SCA Scan (Dependencies)
                    sh 'snyk test --severity-threshold=high'
                    // SAST Scan (Code)
                    sh 'snyk code test --severity-threshold=high'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo 'Running SonarQube Analysis...'
                withSonarQubeEnv('Sonarqube-Server') { // Replace with your SonarQube server name in Jenkins
                    sh "${SONAR_SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.projectKey=NodejsChatApp \
                        -Dsonar.sources=. \
                        -Dsonar.exclusions=node_modules/**"
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
                        sh "echo $DOCKERHUB_PASSWORD | docker login -u $DOCKERHUB_USERNAME --password-stdin"
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
                    // Pull the latest image
                    sh "docker pull ${DOCKER_IMAGE}:latest"
                    // Start application container using docker-compose
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
