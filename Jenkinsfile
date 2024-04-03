pipeline {
    agent any
    environment {
        GIT_CREDENTIAL_ID = 'github-credentials'

    stages {
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
        stage('Deliver') {
            steps {
                echo 'Delivering..'
            }
        }
    }
}


