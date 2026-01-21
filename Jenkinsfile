pipeline {
    agent any

    stages {
        stage('Code Quality') {
            steps {
                script {

                    def scannerHome = tool 'SonarQubeScanner'
                    withSonarQubeEnv('sonar') {
                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }

        stage('Build') {
            steps {

                sh './gradlew clean build'

                sh './gradlew javadoc'

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
