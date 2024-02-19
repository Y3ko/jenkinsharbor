pipeline {
    agent {
        kubernetes {
            // Doğru kullanım: label 'my-kubernetes-agent'
            label 'docker-agent'
        }

    environment {
        DOCKER_CREDENTIALS_ID = '8742b749-d1d1-4c10-aecf-328c5d244315' // Jenkins'de kaydedilen Docker Hub kimlik bilgilerinin ID'si
        DOCKER_IMAGE = 'y3ko/jenkins:test1' // Örnek: 'kullaniciadi/myapp:latest'
    }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    // Docker imajını derle
                    docker.build(env.DOCKER_IMAGE)
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                script {
                    // Docker Hub kimlik bilgilerini kullan
                    docker.withRegistry('https://registry.hub.docker.com', env.DOCKER_CREDENTIALS_ID) {
                        // Docker imajını Docker Hub'a push et
                        docker.image(env.DOCKER_IMAGE).push()
                    }
                }
            }
        }

        stage('Clean Up') {
            steps {
                script {
                    // Derleme sonrası temizlik işlemleri
                    sh "docker rmi ${env.DOCKER_IMAGE}"
                }
            }
        }
    }
}
}
