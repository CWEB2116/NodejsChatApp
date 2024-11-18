pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/CWEB2116/NodejsChatApp.git'
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning GitHub Repository...'
                git branch: 'main', url: "${GIT_REPO}"
            }
        }

        stage('Snyk Security Scan') {
            steps {
                echo 'Running Snyk Security Scan with failOnIssues set to false...'
                snykSecurity(
                    snykInstallation: 'Default',   // Snyk CLI installation name
                    snykTokenId: 'SNYK_TOKEN',     // Credential ID of type Snyk API Token
                    projectName: 'NodejsChatApp',  // Project name for tracking in Snyk
                    severity: 'high',              // Minimum severity threshold
                    targetFile: 'app/package.json', // Path to package.json for scanning
                    monitorProjectOnBuild: true,   // Enable monitoring in Snyk
                    failOnIssues: false            // Allow pipeline to pass despite issues
                )
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo 'Running SonarQube Analysis...'
                withSonarQubeEnv('Sonarqube-Server') { // Reference your SonarQube server name
                    script {
                        def scannerHome = tool 'SonarQube-Installation' // SonarQube Scanner installation
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                              -Dsonar.projectKey=NodeJSChatApp \
                              -Dsonar.sources=. \
                              -Dsonar.exclusions=node_modules/** \
                              -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info \
                              -Dsonar.host.url=$SONAR_HOST_URL \
                              -Dsonar.token=$SONAR_AUTH_TOKEN
                        """
                    }
                }
            }
        }

        stage('Run Application with Docker Compose') {
            steps {
                echo 'Building and Running the Node.js Chat App using Docker Compose...'
                script {
                    // Use Docker Compose to build and run the application locally
                    sh '''
                        docker compose up -d --build
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed.'
        }
        cleanup {
            script {
                echo 'Cleaning up Docker resources...'
                //sh '''
                //    docker compose down || true
                //    docker image prune -f || true
                //'''
            }
        }
    }
}
