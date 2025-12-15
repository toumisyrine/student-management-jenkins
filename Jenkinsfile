pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = "syrinaaa/student-management"
        DOCKER_CREDENTIALS = credentials('dockerhub-credentials')
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main',
                url: 'https://github.com/toumisyrine/student-management-jenkins.git'
            }
        }
        
        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:latest ."
            }
        }
        stage('🔍 SonarQube Analysis') {
            steps {
                echo '📊 Analyse SonarQube...'
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar -Dsonar.projectKey=student-management'
                }
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push ${DOCKER_IMAGE}:latest
                        docker logout
                    '''
                }
            }
        }
    }
    
    post {
        success {
            echo '✅ Pipeline SUCCESS!'
            echo "Image: ${DOCKER_IMAGE}:latest"
            echo "SonarQube: http://TON-IP:9000"
        }
        failure {
            echo '❌ Pipeline FAILED'
        }
        always {
            sh 'docker logout || true'
        }
    }
}
