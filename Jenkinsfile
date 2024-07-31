pipeline {
  agent any
  
   tools {nodejs "node"}
    
  stages {
    stage("Clone code from GitHub") {
            steps {
                script {
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'GitHub', url: 'https://github.com/mmunta20/Deploy-NodeApp-to-AWS-EKS-using-Jenkins-Pipeline.git']])
                }
            }
        }
     
    stage('Node JS Build') {
      steps {
        sh 'npm install'
      }
    }
  
     stage('Build Node JS Docker Image') {
            steps {
                script {
                  sh 'docker build -t mmunta20/node-app-1.0 .'
                }
            }
        }


        stage('Deploy Docker Image to DockerHub') {
            steps {
                script {
                 withCredentials([string(credentialsId: 'dockerhublogin', variable: 'dockerhublogin')]) {
                    sh 'docker login -u devopshint -p ${dockerhublogin}'
            }
            sh 'docker push mmunta20/node-app-1.0'
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

