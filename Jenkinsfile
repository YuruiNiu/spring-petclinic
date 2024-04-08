pipeline {
    agent any
    tools {
        // The name 'M3' should match the name given to the Maven installation in your Jenkins global tools configuration
        maven 'M3'
    }
    environment {
        GIT_CREDENTIAL_ID = credentials('github-credentials')
        SONARQUBE_CREDENTIAL_ID = credentials('A1')
    }
    stages {
        stage('Checkout') {
            steps {
                // This will use the credentials defined above to check out your code
                checkout scm: [
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [],
                    userRemoteConfigs: [[
                        // Use double quotes for variable expansion
                        credentialsId: "${GIT_CREDENTIAL_ID}",
                        url: 'https://github.com/YuruiNiu/spring-petclinic.git'
                    ]]
                ]
            }
        }
        stage('Build') {
            steps {
                echo 'Building..'
                sh './mvnw clean package'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
                sh './mvnw test'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv(installationName: 'SonarQube', credentialsId: 'A1') {
                sh 'mvn sonar:sonar -Dsonar.projectKey=spring-petclinic -Dsonar.projectName="spring-petclinic" -Dsonar.host.url=http://sonarqube:9000'
                }
            }
        }
        stage('Deliver') {
             steps {
                echo 'Delivering the application...'
                // Assuming the jar file is named 'spring-petclinic-3.2.0-SNAPSHOT.jar' after the build. Adjust the name accordingly.
                sh 'java -jar target/spring-petclinic-3.2.0-SNAPSHOT.jar --server.port=9090 &'

                // Optionally wait for the application to start
                sh 'sleep 30'
                echo 'Application should now be running.'
            }
        }
    }
    post {
        always {
        sh 'pkill -f "spring-petclinic-3.2.0-SNAPSHOT.jar" || true'
    }
    }
}
