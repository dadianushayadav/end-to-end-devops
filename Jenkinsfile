pipeline {
    agent any

    tools {
        jdk 'JDK-21'
        maven 'Maven-3.8.4'
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube-Server') {
                    sh 'mvn sonar:sonar -Dsonar.projectKey=devops-portal'
                }
            }
        }
    }
}
