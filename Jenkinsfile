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
                sh 'java -jar target/*.jar &'
                sh 'sleep 30'
                echo 'Application is delivered. Please manually verify its running state and take a screenshot.'
            }
        }
    }
    post {
        always {
            // Correctly placed within the pipeline structure
            sh 'pkill -f \'java -jar target/*.jar\' || true'
        }
    }
}
