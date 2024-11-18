pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/CWEB2116/NodejsChatApp.git'
        SONARQUBE_SERVER = 'SonarQube' // Name configured in Jenkins SonarQube settings
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo 'Cloning GitHub Repository...'
                git branch: 'main', url: "${GIT_REPO}"
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo 'Running SonarQube Analysis...'
                withSonarQubeEnv('SonarQube-Installation') { // Matches SonarQube server name in Jenkins
                    sh '''
                        echo "Running SonarQube Scanner..."
                        sonar-scanner \
                          -Dsonar.projectKey=NodejsChatApp \
                          -Dsonar.sources=. \
                          -Dsonar.exclusions=node_modules/** \
                          -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info \
                          -Dsonar.host.url=$SONAR_HOST_URL \
                          -Dsonar.login=$SONAR_AUTH_TOKEN
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
