pipeline {
    agent any

    tools {
        jdk 'JDK-21'
        maven 'Maven-3.8.4'
    }

    stages {
        stage('Build') {
            steps {
                sh 'java -version'
                sh 'mvn -version'
                sh 'mvn clean package -DskipTests'
            }
        }
    }
}