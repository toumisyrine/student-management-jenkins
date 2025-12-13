pipeline {
    agent any
    tools {
        jdk 'JAVA_HOME'
        maven 'M2_HOME'
    }
    
    environment {
        DOCKER_IMAGE = "syrinaaa/student-management"
    }
    
    stages {
        stage('GIT') {
            steps {
                git branch: 'main',
                url: 'https://github.com/toumisyrine/student-management-jenkins.git'
            }
        }
        
        stage('Compile Stage') {
            steps {
                bat 'mvn clean compile'
            }
        }
        
        stage('Package') {
            steps {
                bat 'mvn package -DskipTests'
            }
        }
        
        stage('SONARQUBE') {
            environment {
                SONAR_HOST_URL = 'http://host.docker.internal:9000'
                SONAR_AUTH_TOKEN = credentials('sonarqube')
            }
            steps {
                bat """
                mvn sonar:sonar ^
                  -Dsonar.projectKey=devops_git ^
                  -Dsonar.host.url=%SONAR_HOST_URL% ^
                  -Dsonar.token=%SONAR_AUTH_TOKEN%
                """
            }
        }
        
        stage('Docker Build') {
            steps {
                bat "docker build -t ${DOCKER_IMAGE}:latest ."
            }
        }
        
        stage('Docker Push') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        bat "docker login -u %DOCKER_USER% -p %DOCKER_PASS%"
                        bat "docker push ${DOCKER_IMAGE}:latest"
                    }
                }
            }
        }
    }
}