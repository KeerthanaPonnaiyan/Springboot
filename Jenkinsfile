pipeline {
    agent any

    environment {
        registry = "292482036611.dkr.ecr.us-east-2.amazonaws.com/my-docker-repo"
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/KeerthanaPonnaiyan/Springboot.git']])
            }
        }
        
        stage ("Build JAR") {
            steps {
                sh "mvn clean install"
            }
        }
        stage('Building image') {
             steps{
                script {
                    dockerImage = docker.build registry 
                    dockerImage.tag("$BUILD_NUMBER")
                }
            }
        }
         stage ("Push to ECR") {
            steps {
                script {
                    sh "aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 292482036611.dkr.ecr.us-east-2.amazonaws.com"
                    sh "docker push 292482036611.dkr.ecr.us-east-2.amazonaws.com/my-docker-repo:$BUILD_NUMBER"
                }
            }
        }
        stage ("Helm Deploy") {
            steps {
                script {
                     sh "helm upgrade first --install mychart --namespace helm-deployment --set image.tag=$BUILD_NUMBER"
                }
            }
        }
    }
}
