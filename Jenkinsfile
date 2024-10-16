pipeline{
    agent any
    tools {
        maven 'M2_HOME'
    }


    environment {
BRANCH_NAME = 'main'
SCANNER_HOME= tool 'sonar-tool'
GIT_URL = 'https://github.com/AstridH83/geoapp.git'
GITHUB_CREDENTIALS = 'github-credentials'
SONAQUBE_CRED='sonar-cred'
SONAQUBE_INSTALLATION='sonar-server'
APP_NAME='geoapp'
QG_CONDITION= 'false'
    }

    stages {
        stage('checkout'){
            steps{
              git branch: "${BRANCH_NAME}", credentialsId: "${GITHUB_CREDENTIALS}", url: "${GIT_URL}"  
            }
        }
        stage('unit test'){
            steps{
                sh 'mvn clean'
                sh 'mvn test'
                sh 'mvn compile'
            }
        }
        stage('Sonarqube Scan'){
            steps{
                withSonarQubeEnv(credentialsId: "${SONAQUBE_CRED}", \
                installationName: "${SONAQUBE_INSTALLATION}" ) {
              sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=${APP_NAME} -Dsonar.projectKey=${APP_NAME} \
                   -Dsonar.java.binaries=. '''
}
            }
        }
        stage('Quality Gate Check'){
            steps{
              script{
                 waitForQualityGate abortPipeline: "${QG_CONDITION}", credentialsId: "${SONAQUBE_CRED}" 
              }
            }
        }

        

    }
}