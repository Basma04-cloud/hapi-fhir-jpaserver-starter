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
    bat 'docker rm -f hapi-fhir 2>nul & exit /b 0'
    bat 'docker rmi hapi-fhir-jpaserver:latest 2>nul & exit /b 0'
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
