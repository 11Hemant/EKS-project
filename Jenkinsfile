pipeline {
    agent any

    environment {
        registry = "227962133227.dkr.ecr.us-east-1.amazonaws.com/my-docker-repo-2"
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/ygminds73/EKS-project.git']]])
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
                
                sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 227962133227.dkr.ecr.us-east-1.amazonaws.com"
                sh "docker push 227962133227.dkr.ecr.us-east-1.amazonaws.com/my-docker-repo-2:latest"
                    
                 
             }
           }   
        }
        
        stage ("Kube Deploy") {
            steps {
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', serverUrl: ''){
                 sh "kubectl apply -f eks-deploy-from-ecr.yaml"
                }
            }
        }
    }
}
