pipeline {
    agent any
    environment {
        PROJECT_ID = 'flown-407707'
        CLUSTER_NAME = 'kube'
        LOCATION = 'asia-northeast3-a'
        CREDENTIALS_ID = 'ed96e8bf-d6ea-4446-bfdb-646835206e6c'
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
                    docker.withRegistry('https://registry.hub.docker.com', 'hanseyoung') {
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
