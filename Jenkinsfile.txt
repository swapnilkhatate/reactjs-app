pipeline {
    agent any
    
    stages {
        stage("Git Checkout") {
            steps {
                git url: "https://github.com/javahometech/reactjs-app", branch: "main"
            }
        }

        stage("Docker Build") {
            steps {
                bat "docker build -t kammana/react-app:${env.BUILD_NUMBER} ."
            }
        }

        stage("Docker Push") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'docker_password', usernameVariable: 'docker_user')]) {
                    bat "docker login -u %docker_user% -p %docker_password%"
                }
                bat "docker push kammana/react-app:${env.BUILD_NUMBER}"
            }
        }

        stage("Dev Deploy") {
            steps {
                sshagent(['docker-dev']) {
                    bat "ssh -o StrictHostKeyChecking=no ec2-user@172.31.15.57 docker rm -f react 2>NUL"
                    bat "ssh ec2-user@172.31.15.57 docker run -d -p 80:80 --name=react kammana/react-app:${env.BUILD_NUMBER}"
                }
            }
        }
    }
}
