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
          withCredentials([string(credentialsId: 'docker-hub', variable: 'PW1')]) {
              echo "My password is '${PW1}'!"
          }
          docker.withRegistry('https://registry.hub.docker.com/v2/', 'docker-hub') {
            def customImage = docker.build("ardydocker/devsecops-application:"+ imageTag)
            customImage.push()
          }
        }
      }
    }
  }
}
