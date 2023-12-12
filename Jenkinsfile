pipeline {
    agent any

    stages {
        stage("Repo checkout") {
            steps {
                checkout scm
            }
        }

        stage('NPM Install') {
            steps {
                bat "npm install"
            }
        }

        stage('Run integration tests') {
            steps {
                bat "npm run test"
            }
        }

        stage('Build Image') {
            steps {
               withCredentials([usernamePassword(credentialsId: 'd6d7086e-f6d3-4a3d-b275-726a41a05e26', passwordVariable: 'pass', usernameVariable: 'user')])  {
                    bat """docker build -t borrisow/student-registry:1.0.0 .
                        docker login -u %dockername% --password %dockerpassword%
                        docker push borrisow/student-registry:1.0.0"""
                }
            }
        }
        
        stage('Deploy Image') {
            steps {
              script {
                    // Prompt for input approval
                    input("Deploy to production?") 
                }
                withCredentials([usernamePassword(credentialsId: 'd6d7086e-f6d3-4a3d-b275-726a41a05e26', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                    bat """docker pull borrisow/student-registry:1.0.0 
                    docker run -d -p 8081:8081 borrisow/student-registry:1.0.0 """
                }
            }
        }
    }
}