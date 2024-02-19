pipeline {
    agent none // Dış agent tanımı kullanmamak için none kullanıyoruz.

    environment {
        DOCKER_IMAGE = 'dockerhub.yekcan.com/library/jenkins:test2'
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
      value: 192.168.1.240:2375
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
                        sh "echo Harbor12345 | docker login dockerhub.yekcan.com --username admin --password-stdin"
                   
                        sh "docker push ${env.DOCKER_IMAGE}"
                    }
                }
            }
        }
    }
}
