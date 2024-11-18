pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/CWEB2116/NodejsChatApp.git'
        // Replace 'SNYK_TOKEN' with the ID of your Snyk API token stored in Jenkins credentials
        SNYK_TOKEN = credentials('SNYK_TOKEN')
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning GitHub Repository...'
                git branch: 'main', url: "${GIT_REPO}"
            }
        }

        stage('List Project Files') {
            steps {
                echo 'Listing files in the workspace...'
                sh 'ls -la'
                sh 'ls -la app'
            }
        }

        stage('Snyk Security Scan') {
            steps {
                echo 'Running Snyk Security Scan...'
                withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
                    sh '''
                        echo "Downloading Snyk CLI..."
                        curl -Lo snyk https://static.snyk.io/cli/latest/snyk-linux
                        chmod +x snyk

                        echo "Authenticating with Snyk..."
                        ./snyk auth $SNYK_TOKEN

                        echo "Running Snyk Test on app/package.json..."
                        # Run Snyk test with specified target file in the app folder
                        ./snyk test app --file=package.json --severity-threshold=high || true
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed.'
        }
    }
}
