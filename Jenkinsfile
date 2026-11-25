pipeline {
    agent any

    stages {
        stage("Checkout") {
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
        stage("Tag Docker Image") {
            steps { 
                sh 'docker tag dockerized-app:latest 387701295760.dkr.ecr.ap-south-1.amazonaws.com/dockerized-app:latest'
            }
        }
        stage("Push Docker Image") {
            steps {
                sh '''
                  aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 387701295760.dkr.ecr.ap-south-1.amazonaws.com

                  docker push 387701295760.dkr.ecr.ap-south-1.amazonaws.com/dockerized-app:latest
                  ''' 
            }
        }
    }
}