pipeline {
  agent any
  tools { 
        maven 'Maven'
        jdk 'JAVA_HOME'
  }
  stages {
    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace... */
      steps {
        checkout scm
      }
    }
    stage('Build') {
      steps {
        sh 'mvn -B -DskipTests clean package'
        sh 'echo $USER'
        sh 'echo whoami'
      }
    }
    stage('Docker Build') {
      steps {
        sh '/usr/bin/docker build -t address-service .'
      }
    }
   
    stage('push image to ECR'){
      steps {
       withDockerRegistry(credentialsId: 'ecr:ap-south-1:docker', url: 'http://887625267599.dkr.ecr.ap-south-1.amazonaws.com/address-service') {
          sh 'docker tag address-service:latest 887625267599.dkr.ecr.ap-south-1.amazonaws.com/address-service:latest'
          sh 'docker push 887625267599.dkr.ecr.ap-south-1.amazonaws.com/address-service:latest'
        } 
      }
    }
  stage('deploy to ECR') {
      steps {
        node('eks-master-node'){
          checkout scm
          sh 'aws eks --region ap-south1 update-kubeconfig --name terraform-eks-demo'
         sh 'kubectl apply -f deployment.yaml' 
         sh 'kubectl apply -f service.yaml' 
        }
      }
    } 
  }
}
