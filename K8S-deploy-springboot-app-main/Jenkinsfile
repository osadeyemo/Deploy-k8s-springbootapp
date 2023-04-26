pipeline {
    agent any
    tools {
        maven 'Maven3'
    }
    environment {
        registry = "public.ecr.aws/m8z1b9u3/myecr-repo"
    }
    stages {
        stage('ckeckout') {
            steps {
               checkout scmGit(branches: [[name: 'main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/fdjapi/springboot-app.git']])
            }
        }
        stage('biuild jar file ') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Building image') {
            steps{
              script {
                dockerImage = docker.build registry 
              }
            }
        }
        stage('Pushing to ECR') {
           steps{  
              script {
                sh 'aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/m8z1b9u3'
                sh 'docker push public.ecr.aws/m8z1b9u3/myecr-repo:latest'
              }
            }
        }
        stage('K8S Deploy') {
         steps{   
            script {
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
                sh ('kubectl apply -f  eks-deploy-k8s.yaml')
                }
            }
         }
        }
    }
}
