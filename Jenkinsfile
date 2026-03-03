pipeline {
    agent any
 
    stages {
 
        stage('Checkout') {
            steps {
                git 'https://github.com/viswaswar/login-app.git'
            }
        }
 
        stage('Deploy to Azure') {
            steps {
                echo "Deploy static files to Azure"
            }
        }
    }
}
