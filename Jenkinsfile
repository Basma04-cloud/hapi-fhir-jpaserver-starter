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
        sh 'mvn clean package -DskipTests'
      }
    }
    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv('SonarQube') {
          sh 'mvn sonar:sonar'
        }
      }
    }
    stage('Test') {
      steps { sh 'mvn test' }
    }
    stage('Docker Cleanup') {
      steps {
        sh 'docker rm -f hapi-fhir || true'
        sh 'docker rmi hapi-fhir-jpaserver:latest || true'
      }
    }
    stage('Docker Build & Run') {
      steps {
        sh 'docker build -t hapi-fhir-jpaserver:latest .'
        sh 'docker run -d -p 8090:8080 --name hapi-fhir hapi-fhir-jpaserver:latest'
      }
    }
  }
}
