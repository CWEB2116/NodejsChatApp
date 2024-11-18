stage('SonarQube Analysis') {
    steps {
        echo 'Running SonarQube Analysis...'
        withSonarQubeEnv('Sonarqube-Server') {
            script {
                def scannerHome = tool 'SonarQube-Installation'
                sh """
                    ${scannerHome}/bin/sonar-scanner \
                      -Dsonar.projectKey=NodeJSChatApp \  # Use the correct project key
                      -Dsonar.sources=. \
                      -Dsonar.exclusions=node_modules/** \
                      -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info \
                      -Dsonar.host.url=$SONAR_HOST_URL \
                      -Dsonar.login=$SONAR_AUTH_TOKEN
                """
            }
        }
    }
}
