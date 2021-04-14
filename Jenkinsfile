pipeline {
    agent any
    environment {
        PROJECT_ID = 'noted-robot-310504'
        CLUSTER_NAME = 'azure-pipelines-cicd-dev'
        LOCATION = 'us-central1-a'
        CREDENTIALS_ID = 'gke'
    }
    stages {
        stage("Build image") {
            steps {
                script {
                    myapp = docker.build("kunalagarwal25/hello:${env.BUILD_ID}")
                }
            }
        }
        stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }        
        stage('Deploy to GKE') {
            steps{
                sh "sed -i 'kunalagarwal25/hello:${env.BUILD_ID}' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }
    } 
}
