pipeline {
    agent any
 
    stages {
 
        stage('Checkout') {
            steps {
                git 'https://github.com/viswaswar/login-app.git'
            }
        }
 
        stage('Zip Files') {
            steps {
                sh 'zip -r app.zip .'
            }
        }
 
        stage('Deploy to Azure') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'azure-loginpage',
                    usernameVariable: 'AZ_USER',
                    passwordVariable: 'AZ_PASS'
                )]) {
 
                    sh '''
                    curl -X POST \
                    -u $AZ_USER:$AZ_PASS \
                    --data-binary @app.zip \
                    https://Newloginpage.scm.azurewebsites.net/api/zipdeploy
                    '''
                }
            }
        }
    }
}
 
