pipeline {
    agent any
    tools {
        // The name 'M3' should match the name given to the Maven installation in your Jenkins global tools configuration
        maven 'M3'
    }
    environment {
        // Replace 'your-credential-id' with the actual ID of your GitHub credentials
        GIT_CREDENTIAL_ID = credentials('github-credentials')
        // Define the environment variable for SonarQube if needed here
        // SONARQUBE_CREDENTIAL_ID = 'A1'
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
                // Injects the SonarQube server configuration and credentials stored in Jenkins
                withSonarQubeEnv('My SonarQube Server', credentialsId: "${SONARQUBE_CREDENTIAL_ID}") {
                    // The 'mvn' command will use the Maven installation defined above by 'tools'
                    sh 'mvn sonar:sonar -Dsonar.projectKey=petclinic -Dsonar.projectName="petclinic"'
                }
        }
        stage('Deliver') {
            steps {
                echo 'Delivering the application...'
                // Run the JAR file built by Maven. Adjust the path to the JAR as necessary.
                sh 'java -jar target/*.jar &'
                // Here we assume the app takes some time to start. Adjust the sleep time as necessary.
                sh 'sleep 30'
                // Here you could add a curl command to check the health of the application if an endpoint is available
                // sh 'curl http://localhost:8080/health'
                echo 'Application is delivered. Please manually verify its running state and take a screenshot.'
            }
        }
    }
    post {
        always {
            // Ensure that the application is killed after the job is done
            sh 'pkill -f \'java -jar target/*.jar\' || true'
        }
    }
}
