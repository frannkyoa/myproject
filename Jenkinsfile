pipeline {
    agent any
    environment {
        PROJECT_ID = 'gcp-devops-project-299410'
        CLUSTER_NAME = 'gke-terraform-cls01'
        LOCATION = 'europe-west2-c'
        CREDENTIALS_ID = 'gcp-devops-project-299410'
        registry = "frannyoa/frankie_docker_1repo"
        registryCredential = 'frannyoa'
        dockerImage = ''
    }
    stages {
        stage("Checkout code") {
            steps {
                checkout scm
            }
        }
        stage("Build image") {
            steps {
                script {
                    dockerImage = docker.build registry + ":${env.BUILD_ID}"
                }
            }
        }
        stage("Push image") {
            steps {
                script {
                    docker.withRegistry( '', registryCredential ) { 
                    dockerImage.push("latest")
                    dockerImage.push("${env.BUILD_ID}")
                    }
                }
            }
        }        
        stage('Deploy to GKE') {
            steps{
                sh "sed -i 's/hello:latest/hello:${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }
    }    
}