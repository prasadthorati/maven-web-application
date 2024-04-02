pipeline {
    agent any
        environment {
        KUBECONFIG = '/var/lib/jenkins/.kube/config'
    }
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/prasadthorati/maven-web-application.git'
            }
        }
        stage('Build') {
            steps {
                script {
                    // Configure Maven settings
                    def mvnHome = tool 'maven'
                    def mvnCmd = "${mvnHome}/bin/mvn"
                    
                    // Run Maven build
                    sh "${mvnCmd} clean package"
                    //sh "cp /var/lib/jenkins/workspace/project-task/target/*.war"
                }
            }
        }
        stage('Docker Build') {
            steps {
                script {
                    // Define Dockerfile location
                    def dockerfilePath = './Dockerfile'
                    
                    // Build Docker image
                    sh "sudo docker build -t gcr.io/gcp-demo-project-413710/project-tag:${env.BUILD_NUMBER} -f ${dockerfilePath} ."
                    sh "pwd"
                    sh "ls -lrt"
                }
            }
        }
        stage('Docker Push') {
            steps {
                script {
                    sh "sudo docker push gcr.io/gcp-demo-project-413710/project-tag:${env.BUILD_NUMBER}"
                }
            }
        }
                // Commented out stages
        stage('SonarQube Analysis') {
            steps {
                    sh 'sudo mvn clean sonar:sonar'
            }
        }
        stage('Deploy to GKE') {
            steps {
                script {
                    sh "kubectl apply -f deployment.yaml"
                }
            }
        }
    }
    post {
        success {
            // Send email on success
            emailext to: 'prasadthorati972@gmail.com',
                     subject: "Pipeline Build #${env.BUILD_NUMBER} succeeded",
                     body: "Pipeline Build #${env.BUILD_NUMBER} succeeded. Build status: ${currentBuild.result}.",
                     replyTo: 'prasadthorati972@gmail.com'
        }
        failure {
            // Send email on failure
            emailext to: 'prasadthorati972@gmail.com',
                     subject: "Pipeline Build #${env.BUILD_NUMBER} failed",
                     body: "Pipeline Build #${env.BUILD_NUMBER} failed. Build status: ${currentBuild.result}.",
                     replyTo: 'prasadthorati972@gmail.com'
        }
    }
}
