pipeline {
    agent any
    environment {
        // Define environment variables here
        GIT_CREDENTIAL_ID = 'github-credentials'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: "${env.GIT_CREDENTIAL_ID}", url: 'https://github.com/YuruiNiu/spring-petclinic.git']]])
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
        stage('Deliver') {
            steps {
                echo 'Delivering..'
            }
        }
    }
}
