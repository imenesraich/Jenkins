pipeline {
    agent any

    environment {
        SONARQUBE_SCANNER = tool 'SonarQubeScanner' // Name of SonarQube scanner tool in Jenkins
        MAVEN_REPO_URL    = "${MAVEN_REPO_URL}"
        MAVEN_REPO_USER   = "${MAVEN_REPO_USER}"
        MAVEN_REPO_PASS   = "${MAVEN_REPO_PASS}"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
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
   stage('Code Quality') {
            steps {
                withSonarQubeEnv('sonar') { // SonarQube server name in Jenkins
                    bat "\"%SONARQUBE_SCANNER%\\bin\\sonar-scanner.bat\" -Dsonar.projectKey=Tp7 -Dsonar.projectName=Tp7"
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying JAR to Maven repository...'
                bat 'gradlew publish -PrepoUrl=%MAVEN_REPO_URL% -PrepoUser=%MAVEN_REPO_USER% -PrepoPassword=%MAVEN_REPO_PASS%'
            }
        }
    }

    post {
        success {
            echo 'Build and deployment succeeded. Sending email...'
            mail to: 'srcimene@gmail.com',
                 subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "Build and deployment finished successfully.\nCheck console output at ${env.BUILD_URL}"
        }
        failure {
            echo 'Build or deployment failed. Sending email...'
            mail to: 'srcimene@gmail.com',
                 subject: "FAILURE: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "Build or deployment failed.\nCheck console output at ${env.BUILD_URL}"
        }
        always {
            echo 'Pipeline finished.'
        }
    }
}
