pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = "syrinaaa/student-management"
        DOCKER_CREDENTIALS = credentials('dockerhub-credentials')
    }
    
    stages {
        stage('GIT') {
            steps {
                git branch: 'main',
                url: 'https://github.com/toumisyrine/student-management-jenkins.git'
            }
        }
        
        stage('Build JAR') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        
        stage('Docker Build & Push') {
            steps {
                sh """
                    docker build -t ${DOCKER_IMAGE}:latest .
                    echo ${DOCKER_CREDENTIALS_PSW} | docker login -u ${DOCKER_CREDENTIALS_USR} --password-stdin
                    docker push ${DOCKER_IMAGE}:latest
                    docker logout
                """
            }
        }
    }
    
    post {
        success {
            echo '✅ SUCCESS! Image pushed to Docker Hub'
            echo "🐳 docker pull ${DOCKER_IMAGE}:latest"
        }
        failure {
            echo '❌ Build failed'
        }
    }
}