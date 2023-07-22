pipeline {
    agent any

    tools {
        // Define the SonarScanner tool with the correct type and name
        sonarScanner 'SonarScanner'
    }

    stages {
        stage('Code Checkout'){
            steps {
                checkout changelog: false, poll: false, scm: scmGit(
                    branches: [[name: '*/master']],
                    extensions: [], 
                    userRemoteConfigs: [[credentialsId: 'GitHubCreds', url: 'https://github.com/anujdevopslearn/MavenBuild']]
                )
            }
        }

        stage('Build Automation'){
            steps {
                sh """
                    ls -lart
                    mvn clean install
                    ls -lart target
                """
            }
        }

        stage('Code Scan'){
            steps {
                withSonarQubeEnv(credentialsId: 'SonarQubeCreds') {
                    sh "${tool name: 'SonarScanner'}/bin/sonar-scanner"
                }
            }
        }

        stage('Code Deployment'){
            steps {
                deploy adapters: [
                    tomcat9(
                        credentialsId: 'TomcatCreds', 
                        path: '', 
                        url: 'http://54.197.62.94:8080/'
                    )
                ], 
                contextPath: 'Planview', 
                onFailure: false, 
                war: 'target/*.war'
            }
        }
    }
}
