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
                echo 'Running Snyk Security Scan with Debugging...'
                withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'DEBUG_SNYK_API_TOKEN')]) {
                    sh '''
                        echo "DEBUG: Using Snyk API Token: $DEBUG_SNYK_API_TOKEN"
                    '''
                }
                snykSecurity(
                    snykInstallation: 'Default',
                    snykTokenId: 'SNYK_TOKEN',
                    projectName: 'NodejsChatApp',
                    severity: 'high',
                    targetFile: 'app/package.json',
                    monitorProjectOnBuild: true
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
