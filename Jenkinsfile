pipeline {
  agent any
  environment {
        //be sure to replace "willbla" with your own Docker Hub username
        DOCKER_IMAGE_NAME = "mmunta20/node-app-1.0"
    }
   tools {nodejs "node"}
    
  stages {
        
    stage('Node JS Build') {
      steps {
        sh 'npm install'
      }
    }
  
     stage('Build Node JS Docker Image') {
            steps {
                 script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                    app.inside {
                    sh 'echo Hello, World!'
                    }
            }
        }


        stage('Deploy Docker Image to DockerHub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhublogin') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
             }
         }          
     stage('Deploying Node App to Kubernetes') {
      steps {
                withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'sample', contextName: '', credentialsId: 'SECRET_TOKEN_KUBE', namespace: '', serverUrl:'https://172.18.71.69:6443']]) {
                sh 'curl -LO "https://storage.googleapis.com/kubernetes-release/release/v1.20.5/bin/linux/amd64/kubectl"'  
                sh 'chmod u+x ./kubectl'  
                sh './kubectl get nodes'
                sh "kubectl apply -f nodejsapp.yaml"
                sh './kubectl get pods'
            }
          }
      }
    }
}
}

