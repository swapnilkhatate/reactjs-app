pipeline {
    agent any

    stages {
        
        stage("Checkout Code") {
            steps {
                git branch: 'main', url: "https://github.com/swapnilkhatate/reactjs-app.git"
            }
        }

        stage("Build Docker Image") {
            steps {
                bat "docker build -t swapnilkhatate/reactjs-app:%BUILD_NUMBER% ."
            }
        }

        stage("Push to DockerHub") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    bat "docker login -u %USER% -p %PASS%"
                }
                bat "docker push swapnilkhatate/reactjs-app:%BUILD_NUMBER%"
            }
        }

        stage("Run Container Locally") {
            steps {
                bat "docker rm -f reactjs 2>NUL"
                bat "docker run -d -p 3000:80 --name reactjs swapnilkhatate/reactjs-app:%BUILD_NUMBER%"
            }
        }
    }
}
