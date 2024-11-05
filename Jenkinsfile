pipeline {
    agent any
    
    tools {nodejs "NodeJS-18-20-4"}

    environment {
        SONAR_TOKEN = credentials('global-sonar-token')
    }

    stages {
        stage('Clone sources') {
            steps {
                git branch: 'main', credentialsId: 'my-credential', url: 'https://github.com/matheusbanhos/global-lakeit.git'
            }
        }
        
        stage('SonarQube Report Export') {
            steps {
                script {
                    def sonarServer = 'http://52.200.180.65:9000'
                    def sonarReportData = sh (
                    script: "curl -s -u ${SONAR_TOKEN}: ${sonarServer}/api/issues/search?componentKeys=global-lakeit",
                    returnStdout: true
                    ).trim()
                    writeFile file: 'sonarqube-report.json', text: sonarReportData
                }
            }
        }

        stage ("Import Sonarqube Defect DOJO") {
            steps {
                script {
                    sh "python3 upload-reports-sonarqube.py sonarqube-report.json"
                    
                    sh """python3 --version /
                    pwd /
                    ls -lha"""
                }
            }
        }
        
    }
}
