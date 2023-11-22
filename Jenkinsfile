pipeline {
  agent any

  environment {
        registry = 'hub.docker.com'
        registryCredential = 'docker-hub' // Credential ID configured in Jenkins
        imageName = 'ardydocker/devsecops-application'
        imageTag = 'latest'
    }

  stages {

    stage('Build Artifact - Maven') {
      steps {
        sh "mvn clean package -DskipTests=true"
        archive 'target/*.jar'
      }
    }

    stage('Unit Tests - JUnit and Jacoco') {
      steps {
        sh "mvn test"
      }
      post {
        always {
          junit 'target/surefire-reports/*.xml'
          jacoco execPattern: 'target/jacoco.exec'
        }
      }
    }

    stage('Docker image build and push') {
      steps {
        // script {
        //     docker.withRegistry('https://' + registry, registryCredential) {
        //         def customImage = docker.build("${imageName}:${imageTag}")
        //         customImage.push()
        //     }
        // }
        docker.withRegistry([credentialsId: registryCredential, url: 'https://' + registry]){
          sh 'printenv'
          sh 'docker build -t ardydocker/devsecops-application:latest .'
          sh 'docker push ardydocker/devsecops-application:latest'
        }
       }
     }
   }
 }
