pipeline {
    agent none

    environment {
        DOCKER_IMAGE = 'dockerhub.yekcan.com/library/jenkins:test2'
    }

    stages {
        stage('Build and Push Docker Image') {
            agent {
                kubernetes {
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
      value: tcp://docker:2375
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
                        sh "docker build -t ${env.DOCKER_IMAGE} ."
                        withCredentials([usernamePassword(credentialsId: 'harbor', usernameVariable: 'REGISTRY_USER', passwordVariable: 'REGISTRY_PASS')]) {
                            sh "echo $REGISTRY_PASS | docker login dockerhub.yekcan.com --username $REGISTRY_USER --password-stdin"
                        }
                        sh "docker push ${env.DOCKER_IMAGE}"
                    }
                }
            }
        }
    }
}
