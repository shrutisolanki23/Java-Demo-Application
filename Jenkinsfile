node{
      
      stage('SCM Checkout'){
         git 'https://github.com/rajnikhattarrsinha/Java-Demo-Application.git'
      }
      
      stage('Build'){
         // Get maven home path and build
         def mvnHome =  tool name: 'Maven 3.5.4', type: 'maven'   
         sh "${mvnHome}/bin/mvn package -Dmaven.test.skip=true"
      }       
       
      stage ('Test'){
         def mvnHome =  tool name: 'Maven 3.5.4', type: 'maven'    
         sh "${mvnHome}/bin/mvn verify; sleep 3"
      }
      
      stage('Build Docker Image'){
         sh 'docker build -t shrutisolanki23/javademoapp_$JOB_NAME:$BUILD_NUMBER .'
      }  
   
      stage('Publish Docker Image'){
         withCredentials([string(credentialsId: 'Shru@12345', variable: 'dockerPWD')]) { 
               sh "docker login -u shrutisolanki23 -p ${dockerPWD}"
         }
        sh 'docker push rajnikhattarrsinha/javademoapp_$JOB_NAME:$BUILD_NUMBER'
        sh "sed -i.bak 's/#BUILD-NUMBER#/$BUILD_NUMBER/' deployment.yaml"
        sh "sed -i.bak 's/#JOB-NAME#/$JOB_NAME/' deployment.yaml"
      }
          
    
      // ********* For Azure Cluster**************************
      stage('Deploy'){
         def k8Apply= "kubectl apply -f deployment.yaml" 
         withCredentials([string(credentialsId: 'Shru@1234567', variable: 'k8pwd')]) {
          sh "sshpass -p ${k8PWD} ssh -o StrictHostKeyChecking=no ubuntu@13.67.36.48" 
          sh "sshpass -p ${k8PWD} scp -r deployment.yaml ubuntu@13.67.36.48:/home/ubuntu" 
          sh "sshpass -p ${k8PWD} ssh  -o StrictHostKeyChecking=no ubuntu@13.67.36.48 ${k8Apply}"
         }
       }
      
         
       
    /*
       
      // ********* For AWS Cluster**************************
      stage('Deploy'){
         def k8Apply= "kubectl apply -f deployment.yaml" 
         withCredentials([string(credentialsId: 'k8pwdrajni', variable: 'k8PWD')]) {
             sh "sshpass -p ${k8PWD} ssh -o StrictHostKeyChecking=no devops@54.196.52.131"  
             sh "sshpass -p ${k8PWD} scp -r deployment.yaml devops@54.196.52.131:/home/devops" 
             sh "sshpass -p ${k8PWD} ssh -o StrictHostKeyChecking=no devops@54.196.52.131 ${k8Apply}"
         }
       }
      */  
  }
