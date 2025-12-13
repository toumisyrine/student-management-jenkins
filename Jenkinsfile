pipeline {
    agent any
    tools {
        jdk 'JAVA_HOME'
        maven 'M2_HOME'
    }
    stages {
        stage('GIT') {
            steps {
                git branch: 'main',
                url: 'https://github.com/maramrzeigui/Jenkins.git'
            }
        }
        stage('Compile Stage') {
            steps {
                sh 'mvn clean compile'
            }
        }
        stage('SONARQUBE') {
            environment {
                SONAR_HOST_URL = 'http://host.docker.internal:9000'
                SONAR_AUTH_TOKEN = credentials('sonarqube')
            }
            steps {
                sh '''
                mvn sonar:sonar \
                  -Dsonar.projectKey=devops_git \
                  -Dsonar.host.url=$SONAR_HOST_URL \
                  -Dsonar.token=$SONAR_AUTH_TOKEN
                '''
            }
        }
    }
}
