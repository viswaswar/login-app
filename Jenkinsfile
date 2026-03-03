pipeline {
  agent any

  options {
    timestamps()
  }

  environment {
    // ====== Azure App Service values ======
    AZURE_APP_NAME     = 'your-unique-webapp-name'     // e.g., employee-static-demo
    AZURE_RESOURCE_GRP = 'rg-static-site'              // your resource group
    // Jenkins credentials you must create (type: Secret text)
    AZ_APP_ID      = credentials('AZURE_APP_ID')       // Service principal appId
    AZ_PASSWORD    = credentials('AZURE_PASSWORD')     // Service principal password
    AZ_TENANT      = credentials('AZURE_TENANT_ID')    // Tenant ID
    AZ_SUBSCRIPTION= credentials('AZURE_SUBSCRIPTION_ID') // Subscription ID
  }

  tools {
    maven 'Maven-3' // Configure under "Manage Jenkins" → "Global Tool Configuration"
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/<your-username>/<your-repo>.git'
      }
    }

    stage('Build (Maven)') {
      steps {
        sh 'mvn -v'
        sh 'mvn -B clean package'
      }
    }

    stage('Package Artifact (zip)') {
      steps {
        sh '''
          rm -f artifact.zip || true
          # Expect static files copied by Maven to target/dist
          if [ ! -d target/dist ]; then
            echo "target/dist not found. If your static files are in ./site, ensure pom.xml copies them to target/dist."
            exit 1
          fi
          (cd target/dist && zip -r ../../artifact.zip .)
        '''
      }
      post {
        success {
          archiveArtifacts artifacts: 'artifact.zip', fingerprint: true
        }
      }
    }

    stage('Azure Login') {
      steps {
        sh '''
          az logout || true
          az login --service-principal -u "$AZ_APP_ID" -p "$AZ_PASSWORD" --tenant "$AZ_TENANT" >/dev/null
          az account set --subscription "$AZ_SUBSCRIPTION"
        '''
      }
    }

    stage('Deploy to Azure (Zip Deploy)') {
      steps {
        sh '''
          # Deploy zip to Azure Web App (Linux)
          az webapp deployment source config-zip \
            --resource-group "$AZURE_RESOURCE_GRP" \
            --name "$AZURE_APP_NAME" \
            --src artifact.zip
        '''
      }
    }
  }

  post {
    always {
      sh 'rm -f artifact.zip || true'
    }
  }
}
