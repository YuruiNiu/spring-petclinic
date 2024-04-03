pipeline {
    agent any
    environment {
        // Replace 'your-credential-id' with the actual ID of your GitHub credentials
        GIT_CREDENTIAL_ID = 'github-credentials'
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
                withSonarQubeEnv('SonarQube') {
                    sh "mvn clean verify sonar:sonar -Dsonar.projectKey=petclinic -Dsonar.projectName='petclinic'"
                }
            }
        }
        stage('Deliver') {
            steps {
                echo 'Delivering..'
            }
        }
    }
}
