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
        always {
            echo 'Pipeline finished!'
        }
    }
}
