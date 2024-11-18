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
                echo 'Running Snyk Security Scan using Jenkins Plugin...'
                snykSecurity(
                    snykInstallation: 'Default',   // Snyk CLI installation name
                    snykTokenId: 'SNYK_TOKEN',     // Credential ID of type Snyk API Token
                    projectName: 'NodejsChatApp',  // Project name for tracking in Snyk
                    severity: 'high',              // Minimum severity threshold
                    targetFile: 'app/package.json', // Path to package.json for scanning
                    monitorProjectOnBuild: true    // Enable monitoring in Snyk
                )
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed.'
        }
    }
}
