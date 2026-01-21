pipeline {
    agent any

    environment {
        SONARQUBE_SCANNER = tool 'SonarQubeScanner' // Name of SonarQube scanner tool in Jenkins
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Code Quality') {
            steps {
                withSonarQubeEnv('sonar') { // SonarQube server name in Jenkins
                    bat "\"%SONARQUBE_SCANNER%\\bin\\sonar-scanner.bat\""
                }
            }
        }

        stage('Build') {
            steps {
                echo 'Building JAR...'
                bat 'gradlew clean build'

                echo 'Generating documentation...'
                bat 'gradlew javadoc'

                echo 'Archiving artifacts...'
                archiveArtifacts artifacts: 'build/libs/*.jar, build/docs/javadoc/**', fingerprint: true
            }
        }
    }

    post {
        success {
            echo 'Build succeeded! Sending notifications...'
            // Email notification on success
                mail to: 'srcimene@gmail.com',

                 subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "The build succeeded!\nCheck details: ${env.BUILD_URL}"

            // slackSend channel: '#builds', color: 'good', message: "Build SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
        }
        failure {
            echo 'Build failed! Sending notifications...'

            mail to: 'srcimene@gmail.com',
                 subject: "FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "The build failed!\nCheck details: ${env.BUILD_URL}"
            // Optional Slack notification (if plugin configured)
            // slackSend channel: '#builds', color: 'danger', message: "Build FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
        }
        always {
            echo 'Pipeline finished!'
        }
    }
}
