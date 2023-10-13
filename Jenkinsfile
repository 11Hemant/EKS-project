pipeline {
    agent any

    environment {
        registry = "263292212959.dkr.ecr.us-west-1.amazonaws.com/batch-15-repo"
    }
    
    stages {
        stage('Checkout') {
            steps {
                   checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/11Hemant/EKS-project.git']])
            }
        }
    
           stage ("Build jar") {
            steps {
                script {
                    sh "mvn clean install"
                }
            }
        }
    
        stage ("Build image") {
            steps {
                script {
                    docker.build registry
                }
            }
        }
        
        stage ("docker push") {
         steps {
             script {
                
                sh "aws ecr get-login-password --region us-west-1 | docker login --username AWS --password-stdin 263292212959.dkr.ecr.us-west-1.amazonaws.com"
                sh "docker push 263292212959.dkr.ecr.us-west-1.amazonaws.com/batch-15-repo:latest"
                    
                 
             }
           }   
        }
        
        stage ("Kube Deploy") {
            steps {
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'Batch-15', namespace: '', restrictKubeConfigAccess: false, serverUrl: ''){
                 sh "kubectl apply -f eks-deploy-from-ecr.yaml"
                }
            }
        }
    }
}
