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
                    sh '''
                        mvn org.sonarsource.scanner.maven:sonar-maven-plugin:5.2.0.4988:sonar \
                        -Dsonar.projectKey=devops-portal
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Upload to Nexus') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'nexus-credentials',
                    usernameVariable: 'NEXUS_USER',
                    passwordVariable: 'NEXUS_PASS'
                )]) {
                    sh '''
                        set +x

                        cat > /tmp/nexus-settings.xml <<EOF
<settings>
    <servers>
        <server>
            <id>nexus-snapshots</id>
            <username>${NEXUS_USER}</username>
            <password>${NEXUS_PASS}</password>
        </server>
                    </servers>
</settings>
EOF

                        mvn -s /tmp/nexus-settings.xml \
                        org.apache.maven.plugins:maven-deploy-plugin:3.1.3:deploy-file \
                        -DgroupId=com.devops \
                        -DartifactId=devops-portal \
                        -Dversion=0.0.1-SNAPSHOT \
                        -Dpackaging=jar \
                        -Dfile=target/devops-portal-0.0.1-SNAPSHOT.jar \
                        -DrepositoryId=nexus-snapshots \
                        -Durl=http://54.196.33.180:8081/repository/maven-snapshots/ \
                        -DgeneratePom=true

                        rm -f /tmp/nexus-settings.xml
                    '''
                }
            }
        }
    }
}
