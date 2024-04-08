pipeline {
    agent any
    tools {
        // The name 'M3' should match the name given to the Maven installation in your Jenkins global tools configuration
        maven 'M3'
    }
    environment {
        // Replace 'github-credentials' with the actual ID of your GitHub credentials
        GIT_CREDENTIAL_ID = credentials('github-credentials')
        // Now using the SonarQube credentials
        SONARQUBE_CREDENTIAL_ID = credentials('A1') // Correctly define the SonarQube credentials ID
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
                // Correctly formatted withSonarQubeEnv usage
                withSonarQubeEnv('SonarQube', credentialsId: "${SONARQUBE_CREDENTIAL_ID}") {
                    sh 'mvn sonar:sonar -Dsonar.projectKey=petclinic -Dsonar.projectName="petclinic"'
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
