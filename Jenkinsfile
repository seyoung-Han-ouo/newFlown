pipeline {
    agent any
    environment {
        PROJECT_ID = 'flown-407707'
        CLUSTER_NAME = 'kube'
        LOCATION = 'asia-northeast3-a'
        CREDENTIALS_ID = 'gke'
    }
    stages {
        stage("Checkout code") { //git clone과 같은 효과
            steps {
                checkout scm
            }
        }
        stage("Build image") {
            steps {
                script {
                    myapp = docker.build("hanseyoung/flown:${env.BUILD_ID}")
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
			when {
				branch 'main'
			}
            steps{
                sh "sed -i 's/flown:latest/flown:${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }
    }    
}