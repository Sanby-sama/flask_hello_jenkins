pipeline {
    agent {
        kubernetes {
            yaml '''
apiVersion: v1
kind: Pod
metadata:
  labels:
    component: ci
spec:
  containers:
  - name: python
    image: python:3.7
    command: ["cat"]
    tty: true
  - name: docker
    image: docker
    command: ["cat"]
    tty: true
    volumeMounts:
    - mountPath: /var/run/docker.sock
      name: docker-sock
  - name: kubectl
    image: lachlanevenson/k8s-kubectl:v1.17.2
    command: ["cat"]
    tty: true
  volumes:
  - name: docker-sock
    hostPath:
      path: /var/run/docker.sock
'''
        }
    }

    environment {
        REGISTRY_IP = '192.168.49.2'   // ←←← CHANGE ÇA avec ton IP minikube
    }

    stages {
        stage('Test python') {
            steps {
                container('python') {
                    sh "pip install -r requirements.txt"
                    sh "python test.py"
                }
            }
        }
        
        stage('Build image') {
            steps {
                container('docker') {
                    sh """
                        docker build -t flask_hello:latest .
                        echo "Image built successfully (push skipped for now)"
                    """
                }
            }
        }
        
        stage('Deploy') {
            steps {
                container('kubectl') {
                    sh "kubectl apply -f kubernetes/deployment.yaml"
                    sh "kubectl apply -f kubernetes/service.yaml"
                }
            }
        }
    }
}