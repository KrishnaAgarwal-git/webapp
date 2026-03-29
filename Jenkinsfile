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

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube-Server') {

                    bat 'mvn verify sonar:sonar -Dsonar.projectKey=webapp'

                }
            }
        }


        stage('Deploy to Nexus') {
            steps {
                bat 'mvn deploy -DskipTests -f pom.xml'
            }
        }


        stage('Archive Artifact') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }


        stage('Deploy Application') {
            steps {
                bat '''
                echo Killing old app on port 9999...
                for /f "tokens=5" %%a in ('netstat -ano ^| findstr :9999') do taskkill /PID %%a /F

                echo Starting new app...
                cmd /c start "" /B java -jar target/java-webapp-1.0-shaded.jar
                '''
            }
        }

    }
}
