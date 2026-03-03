pipeline {
    agent any
 
    tools {
        maven 'Maven3'   // Maven name configured in Jenkins
    }
 
    stages {
 
        stage('Checkout Code') {
            steps {
                // Pull code from GitHub repo
                git branch: 'main',
                    url: 'https://github.com/viswaswar/login-app.git'
            }
        }
 
        stage('Maven Build') {
            steps {
                // Clean and package project
                sh 'mvn clean package'
            }
        }
 
    }
}
 
