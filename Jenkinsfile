pipeline {
  agent any

  environment {
        registry = 'docker.io'
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
        script {
          sh 'printenv'
          withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
            sh 'echo $USERNAME'
            sh 'echo $PASSWORD'
            sh 'echo $PASSWORD | docker login -u $USERNAME --password-stdin docker.io'
            sh 'docker build -t ardydocker/devsecops-application:latest .'
            sh 'docker push ardydocker/devsecops-application:latest'
            // docker.withRegistry('https://registry.hub.docker.com/v2/', 'docker-hub') {
            //   def customImage = docker.build("ardydocker/devsecops-application:"+ imageTag)
            //   customImage.push()
            // }
          }
        }
      }
    }

    stage('Kubernetes Deployment - DEV') {
      steps {
        withKubeConfig([credentialsId: 'kube-config', serverUrl: '']) {
          sh "sed -i 's#REPLACE_ME#ardydocker/devsecops-application:latest#g' k8s_deployment_service.yaml"
          sh "kubectl apply -f k8s_deployment_service.yaml"
        }
      }
    }
  }
}
