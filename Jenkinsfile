pipeline {
    agent any

    stages {
        stage("checkout") {
            steps {
               git branch: "main",
                url: 'https://github.com/prakashgit122/dockerized-app-deployment.git' 
            }
        }
        stage("Build") {
            steps {
                sh 'docker build -t dockerized-app .'
            }
        }
    }
}