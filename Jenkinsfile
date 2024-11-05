pipeline {
    agent any
    
    tools {nodejs "NodeJS-18-20-4"}

    environment {
        SONAR_TOKEN = credentials('global-sonar-token')
    }

    stages {
        stage('Clone sources') {
            steps {
                git branch: 'main', credentialsId: 'my-credential', url: 'https://github.com/global-develop-qa/model.git'
            }
        }

        stage ("Import Sonarqube Defect DOJO") {
            steps {
                script {
                    
                    sh """python3 --version /
                    pwd /
                    ls -lha"""

                    sh """
                       export DB_USERNAME="${SONAR_TOKEN}"
                       export DB_PASSWORD="${SONAR_TOKEN}"
                       python3 upload.py
                    """                    
                }
            }
        }
        
    }
}
