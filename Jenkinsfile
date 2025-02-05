pipeline {
   tools {
        maven 'MAVEN3'
    }
    agent any
    environment {
        registry = "400597339412.dkr.ecr.ap-south-1.amazonaws.com/my-kubernetes-repo"
    }
   
    stages {
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/akannan1087/springboot-app']]])     
            }
        }
      stage ('Build') {
          steps {
            sh 'mvn clean install'           
            }
      }
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry 
        }
      }
    }
   
    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
     steps{  
         script {
                sh 'aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 400597339412.dkr.ecr.ap-south-1.amazonaws.com'
                sh 'docker push 400597339412.dkr.ecr.ap-south-1.amazonaws.com/my-kubernetes-repo:latest'
         }
        }
      }

       stage('K8S Deploy') {
        steps{   
            script {
                kubeconfig(credentialsId: 'K8S', serverUrl: '') {
                sh ('kubectl apply -f  eks-deploy-k8s.yaml')
                }
            }
        }
       }
    }
}
