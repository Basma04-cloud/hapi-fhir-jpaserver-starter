pipeline {
  agent any
  tools {
    jdk 'JDK17'
    maven 'Maven-3.9'
  }
  stages {
    stage('Checkout SCM') {
      steps { checkout scm }
    }
    stage('Build') {
      steps {
        bat 'mvn clean package -DskipTests'
      }
    }
    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv('SonarQube') {
          bat 'mvn sonar:sonar'
        }
      }
    }
    stage('Test') {
      steps { bat 'mvn test' }
    }
    stage('Docker Cleanup') {
      steps {
        bat 'docker rm -f hapi-fhir || true'
        bat 'docker rmi hapi-fhir-jpaserver:latest || true'
      }
    }
    stage('Docker Build & Run') {
      steps {
        bat 'docker build -t hapi-fhir-jpaserver:latest .'
        bat 'docker run -d -p 8090:8080 --name hapi-fhir hapi-fhir-jpaserver:latest'
      }
    }
  }
}
