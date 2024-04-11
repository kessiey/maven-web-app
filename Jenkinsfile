pipeline {
    agent any
    
    tools {
        maven "maven3.9.6"
    }

    stages {
        stage("Git clone") {
            steps {
                git 'https://github.com/kessiey/maven-web-app.git'
            }
        }

        stage("Build with Maven") {
            steps {
                sh "mvn clean"
            }
        }

        stage("Testing with Maven") {
            steps {
                sh "mvn test"
            }
        }

        stage("Package with Maven") {
            steps {
                sh "mvn package"
            }
        }

        stage('SonarQube Analysis') {
            environment {
                ScannerHome = tool 'sonar5.0'
            }
            steps {
                script {
                    withSonarQubeEnv('sonarqube') {
                        sh "${ScannerHome}/bin/sonar-scanner -Dsonar.projectKey=jomacs"
                    }
                }
            }
        }

        stage("Upload to Nexus") {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: '01-maven-web-app', classifier: '', file: '/var/lib/jenkins/workspace/maven-web-app/target/maven-web-app.war', type: 'war']], credentialsId: 'nexus-id', groupId: 'in.ashokit', nexusUrl: '3.145.216.253:8081/', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-web-app-release', version: '3.0-RELEASE'
            }
        }

        stage("Deploy to UAT") {
            steps {
                deploy adapters: [tomcat9(credentialsId: 'tomcat-credentials', path: '', url: 'http://3.135.209.206:8080/')], contextPath: null, war: 'target/*.war'
            }
        }
    }
}