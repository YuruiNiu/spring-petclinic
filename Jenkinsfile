pipeline {
    agent any
    tools {
        maven 'M3'
    }
    environment {
        GIT_CREDENTIAL_ID = credentials('github-credentials')
        SONARQUBE_CREDENTIAL_ID = credentials('A1')
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm: [
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [],
                    userRemoteConfigs: [[
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
                sh 'java -jar target/spring-petclinic-3.2.0-SNAPSHOT.jar &'
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
