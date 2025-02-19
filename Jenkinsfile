pipeline {
    agent any
    
    tools {
        
        jdk 'jdk17'
        maven 'maven3'
    }
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('git checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/MedAljane/Task-Master-Pro.git'
            }
        }
        stage('mvn compile') {
            steps {
                sh "mvn compile"
            }
        }
         stage('mvntest') {
            steps {
                sh "mvn test"
            }
        }
        //  stage('trivy') {
        //     steps {
        //         sh "trivy fs --format table -o trivy-fs-report.html ."
        //     }
        // }
        stage('SonarQube Analysis') {
             steps {
               withSonarQubeEnv('sonar') {
                sh '''
                  $SCANNER_HOME/bin/sonar-scanner \
                  -Dsonar.projectName=Task-Master-Pro \
                  -Dsonar.projectKey=Task-Master-Pro \
                  -Dsonar.java.binaries=.
                '''
        }
    }
}
        stage('SonarQuality') {
            steps {
                script { 
                    waitForQualityGate abortPipeline: false, credentialsID: 'sonar-token'
                }
            }
        }
        stage('mvn build') {
            steps {
                sh "mvn package"
            }
        }
        stage('publish artifacts to Nexus') {
            steps {
                withMaven(globalMavenSettingsConfig: 'global-settings', jdk: 'jdk17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                    sh "mvn deploy"
    
             }
            }
        }
        stage('Docker build') {
            steps {
                withDockerRegistry(credentialsId: 'docker-cred', url: '') {
                 sh "docker build -t hammaljane/devops:latest ."
                
                
            }
            }
            
        // stage('trivy dokcer image scan') {
        //     steps {
        //         sh "trivy image --format table -o trivy-fs-report.html gita/task-master:latest"
        //     }
        // }
    }
}
}
