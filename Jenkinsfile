pipeline {
  agent any

  environment {
        registry = 'https://registry.hub.docker.com/v2/'
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
            sh 'docker login -u $USERNAME -p $PASSWORD https://registry.hub.docker.com/v2/'
            sh 'docker build -t ardydocker/devsecops-application:lastest .'
            sh 'docker push ardydocker/devsecops-application:lastest'
            // docker.withRegistry('https://registry.hub.docker.com/v2/', 'docker-hub') {
            //   def customImage = docker.build("ardydocker/devsecops-application:"+ imageTag)
            //   customImage.push()
            // }
          }
        }
      }
    }
  }
}
