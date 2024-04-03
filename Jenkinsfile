pipeline {
    agent any
    
    tools {
        maven 'Maven' // Assumes you've configured Maven named 'Maven' in Global Tool Configuration
        jdk 'Java' // Assumes you've configured JDK named 'Java' in Global Tool Configuration
    }

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
