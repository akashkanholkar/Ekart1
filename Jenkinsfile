pipeline {
    agent any
    tools{
        jdk 'java21'
        maven 'mavan'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, credentialsId: '1a7683e9-068d-48a3-9617-9aebbb65ae22', poll: false, url: 'https://github.com/akashkanholkar/Ekart1.git'
            }
        }
        stage('COMPILE') {
            steps {
               bat "mvn clean compile -DskipTests=true"
            }
        }
        stage('OWASP Scan') {
            steps {
               dependencyCheck additionalArguments: '--scan ./' , odcInstallation: 'DP'
               dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('Build') {
            steps {
               bat "mvn clean package -DskipTests=true"
            }
        }
        stage('Docker build and Push') {
            steps {
               script{
                   withDockerRegistry(credentialsId: 'aa5e5ecb-4325-499a-9554-b700f4523ac7', toolName: 'docker') {
                       bat "docker build -t shopping-cart -f docker/Dockerfile ."
                       bat "docker tag shopping-cart akashkanholkar/shopping-cart:latest"
                       bat "docker push akashkanholkar/shopping-cart:latest"

                   }
               }
            }
        }
    }
}
