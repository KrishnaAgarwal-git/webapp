pipeline {
    agent any

    tools {
        maven 'Maven-3.9.12'
    }

    stages {

        stage('Build') {
            steps {
                bat 'mvn -B -DskipTests clean package'
            }
        }

        stage('Test') { 
            steps {
                bat 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Sonar-Report') {
            steps {
                withSonarQubeEnv('SonarQube-Server') {
                    bat 'mvn clean verify sonar:sonar'
                }
            }
        }

        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }
}
