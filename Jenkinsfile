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
                echo 'Running Snyk Security Scan with Jenkins Plugin...'
                snykSecurity(
                    snykInstallation: 'Default',  // Use 'Default' unless you have a custom installation name
                    snykTokenId: 'SNYK_TOKEN',   // Replace with your API token credential ID
                    projectName: 'NodejsChatApp', // Project name for better tracking
                    severity: 'high',           // Set severity threshold (e.g., 'high', 'critical')
                    targetFile: 'app/package.json', // Path to the package.json file in your project
                    monitorProjectOnBuild: true // Enable monitoring for long-term tracking in Snyk
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
