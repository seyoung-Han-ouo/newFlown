node {
    def app
    stage('Clone repository') {
        git 'https://github.com/seyoung-Han-ouo/newFlown.git'
    }
    stage('Build image') {
        app = docker.build("hanseyoung/flown")
    }
    stage('Test image') {
        app.inside {
            sh 'make test'
        }
    }
    stage('Push image') {
        docker.withRegistry('https://registry.hub.docker.com', 'hanseyoung') {
           app.push("${env.BUILD_NUMBER}")
           app.push("latest")
        }
    }
}