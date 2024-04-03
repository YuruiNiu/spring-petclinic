pipeline {
    agent any
    environment {
        // Replace 'your-credential-id' with the actual ID of your GitHub credentials
        GIT_CREDENTIAL_ID = credentials('github-credentials')
        // Define the environment variable for SonarQube if needed here
        // SONARQUBE_CREDENTIAL_ID = 'your-sonar-credentials-id'
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
                withSonarQubeEnv('SonarQube') {
                    // Add 'echo' to print the Maven command being run
                    echo "Running SonarQube analysis..."
                    script {
                        def mvnCmd = "mvn clean verify sonar:sonar -Dsonar.projectKey=petclinic -Dsonar.projectName='petclinic'"
                        echo "Executing: ${mvnCmd}"
                        sh mvnCmd
                    }
                }
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
            sh 'pkill -f \'java -jar target/*.jar\''
        }
    }
}
