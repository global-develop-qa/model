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
        
        stage("SonarQube Analysis"){
            environment {
                SCANNER_HOME = tool 'SonarQubeScanner';    
            }

            steps{
               withSonarQubeEnv("SonarQube"){
                   sh '''$SCANNER_HOME/bin/sonar-scanner \
                   -Dsonar.projectName=global-lakeit \
                   -Dsonar.projectKey=global-lakeit \
                   -Dsonar.sources=. \
                   -Dsonar.language=ts \
                   -Dsonar.sourceEncoding=UTF-8 \
                   -Dsonar.scm.disabled=true \
                   -Dsonar.issuesReport.html.enable=true \
                   -Dsonar.report.export.path=report.json'''
               }
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

        stage("SonarQube Quality Gates"){
            steps{
               timeout(time: 1, unit: "MINUTES"){
                   waitForQualityGate abortPipeline: false
               }
            }
        }
        
        stage("OWASP Dependency-Check"){
            steps{
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'OWASP'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }

        stage ("Import OWASP Defect DOJO") {
            steps {
                script {
                    sh "python3 upload-reports.py dependency-check-report.xml"
                    
                    sh """python3 --version /
                    pwd /
                    ls -lha"""
                }
            }
        }

        stage("TRIVY Scan") {            
            steps {                
                sh "trivy fs -f json . > trivy-fs_report.json"
            }        
        }

        stage ("Import TRIVY Defect DOJO") {
            steps {
                script {
                    sh "python3 upload-reports-trivy.py trivy-fs_report.json"
                    
                    sh """python3 --version /
                    pwd /
                    ls -lha"""
                }
            }
        }
        
    }
}
