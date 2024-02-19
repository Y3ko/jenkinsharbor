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
                    // Docker-in-Docker için bir pod şablonu tanımlıyoruz
                    defaultContainer 'jnlp'
                    yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    some-label: docker-builder
spec:
  containers:
  - name: docker
    image: docker:19.03.12-dind
    env:
    - name: DOCKER_HOST
      value: http://192.168.1.120:2375
    securityContext:
      privileged: true
    volumeMounts:
    - name: docker-graph-storage
      mountPath: /var/lib/docker
  volumes:
  - name: docker-graph-storage
    emptyDir: {}
"""
                }
            }
            steps {
                container('docker') {
                    script {
                        // Docker imajını derle ve push et.
                        sh "docker build -t ${env.DOCKER_IMAGE} ."
                        sh "echo ${env.DOCKER_CREDENTIALS_ID} | docker login --username your-docker-hub-username --password-stdin"
                        sh "docker push ${env.DOCKER_IMAGE}"
                    }
                }
            }
        }

        stage('Clean Up') {
            agent any
            steps {
                script {
                    // Derleme sonrası temizlik işlemleri.
                    sh "docker rmi ${env.DOCKER_IMAGE}"
                }
            }
        }
    }
}
