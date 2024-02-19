pipeline {
    agent none // Dış agent tanımı kullanmamak için none kullanıyoruz.

    environment {
        DOCKER_CREDENTIALS_ID = '8742b749-d1d1-4c10-aecf-328c5d244315'
        DOCKER_IMAGE = 'y3ko/jenkins:test1'
    }

    stages {
        stage('Build and Push Docker Image') {
            agent {
                kubernetes {
                    inheritFrom 'docker-agent' // Burada, Docker daemon'una erişimi olan bir Kubernetes pod şablonunu miras alıyoruz.
                }
            }
            steps {
                container('docker') { // 'docker' container'ında çalışacak komutlar.
                    script {
                        // Docker imajını derle ve push et.
                        def builtImage = docker.build("${env.DOCKER_IMAGE}")
                        docker.withRegistry('https://registry.hub.docker.com', "${env.DOCKER_CREDENTIALS_ID}") {
                            builtImage.push()
                        }
                    }
                }
            }
        }

        stage('Clean Up') {
            agent {
                kubernetes {
                    inheritFrom 'docker-agent'
                }
            }
            steps {
                container('docker') {
                    script {
                        // Derleme sonrası temizlik işlemleri.
                        sh "docker rmi ${env.DOCKER_IMAGE}"
                    }
                }
            }
        }
    }
}
