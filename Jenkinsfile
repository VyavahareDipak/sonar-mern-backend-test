pipeline {
    agent any
    tools {
        nodejs 'nodeJS' // Ensure this matches the NodeJS installation name in Jenkins Global Tool Configuration
    }

    environment {
        NODEJS_HOME = 'C:/Program Files/nodejs'
        SONAR_SCANNER_PATH = 'C:/Program Files/sonar-scanner-cli-6.2.1.4610-windows-x64/sonar-scanner-6.2.1.4610-windows-x64/bin'
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the source code from the repository
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                bat '''
                set PATH=%NODEJS_HOME%;%PATH%
                npm install
                '''
            }
        }

        stage('Lint') {
            steps {
                bat '''
                set PATH=%NODEJS_HOME%;%PATH%
                if not exist .eslintrc.json (
                    npm init @eslint/config --yes
                )
                npm run lint
                '''
            }
        }

        stage('SonarQube Analysis') {
            environment {
                SONAR_TOKEN = credentials('sonar-global-token') // Use a Jenkins credential ID for security
            }
            steps {
                bat '''
                set PATH=%SONAR_SCANNER_PATH%;%PATH%
                sonar-scanner -Dsonar.projectKey=mern-backend-test ^
                              -Dsonar.sources=. ^
                              -Dsonar.host.url=http://localhost:9000 ^
                              -Dsonar.login=%SONAR_TOKEN% 2>&1
                '''
            }
        }

        stage('Deploy') {
            steps {
                bat '''
                set PATH=%NODEJS_HOME%;%PATH%
                npm start
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully'
        }
        failure {
            echo 'Pipeline failed'
        }
        always {
            echo 'This runs regardless of the result.'
        }
    }
}
