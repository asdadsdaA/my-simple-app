pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'solumieres/my-simple-app'
        DOCKER_CREDENTIALS_ID = '1234'
    }
    
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/asdadsdaA/my-simple-app.git'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    // Заменяем версию в HTML
                    sh "sed -i 's/BUILD_VERSION/${BUILD_NUMBER}/' index.html"
                    // Собираем образ
                    docker.build("${DOCKER_IMAGE}:${BUILD_NUMBER}")
                }
            }
        }
        
        stage('Test Image') {
            steps {
                script {
                    sh """
                        docker run -d --name test-container ${DOCKER_IMAGE}:${BUILD_NUMBER}
                        sleep 5
                        docker ps | grep test-container
                        docker stop test-container
                        docker rm test-container
                    """
                }
            }
        }
        
        stage('Deploy to Production') {
            steps {
                script {
                    sh 'docker stop running-app || true'
                    sh 'docker rm running-app || true'
                    sh """
                        docker run -d \
                        -p 80:80 \
                        --name running-app \
                        ${DOCKER_IMAGE}:${BUILD_NUMBER}
                    """
                }
            }
        }
    }
    
    post {
        success {
            echo '✅ CI/CD цепочка успешно завершена!'
            echo "Приложение доступно по адресу: http://ваш-ip-сервера"
        }
        failure {
            echo '❌ Сборка или деплой завершились ошибкой'
        }
    }
}
