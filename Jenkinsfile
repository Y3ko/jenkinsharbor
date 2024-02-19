pipeline {
    agent {
        kubernetes {
            label 'docker-agent' // Kubernetes agent etiketi, doğru kullanım burada
        }
    }

    environment {
        DOCKER_CREDENTIALS_ID = '8742b749-d1d1-4c10-aecf-328c5d244315' // Jenkins'de kaydedilen Docker Hub kimlik bilgilerinin ID'si
        DOCKER_IMAGE = 'y3ko/jenkins:test1' // Docker imaj adı ve etiketi
    }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    // Docker imajını derleme komutu, docker.build'in doğru kullanımı için 'docker' değil, 'docker.build' kullanılmalıdır
                    docker.build("${env.DOCKER_IMAGE}")
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                script {
                    // Docker Hub kimlik bilgilerini kullanarak Docker imajını Docker Hub'a push et
                    docker.withRegistry('https://registry.hub.docker.com', "${env.DOCKER_CREDENTIALS_ID}") {
                        docker.image("${env.DOCKER_IMAGE}").push()
                    }
                }
            }
        }

        stage('Clean Up') {
            steps {
                script {
                    // Derleme sonrası temizlik işlemleri, kullanılan değişkenin doğru şekilde kullanımı
                    sh "docker rmi ${env.DOCKER_IMAGE}"
                }
            }
        }
    }
}
