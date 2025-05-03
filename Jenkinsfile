// Jenkinsfile
pipeline {
    agent any
    stages {
        stage('Build Docker Image') {
            steps {
                // สร้าง Docker image
                sh """
                    docker build --rm \
                    -f Dockerfile \
                    -t registry-1.docker.io/thitiporn2045/jenkins-oddscloud \
                    -t registry-1.docker.io/thitiporn2045/jenkins-oddscloud:${env.BUILD_NUMBER}
                    .
                """
            }
        }
        stage('Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'DOCKER_CREDENTIALS', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                sh """
                        docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD registry-1.docker.io
                        docker push registry-1.docker.io/thitiporn2045/jenkins-oddscloud:${env.BUILD_NUMBER}
                      """
                }
            }
        }
        stage('Deploy to server') {
            steps {
                script {
                    sshagent (credentials: ["SSH_KEY_DEV_SERVER_GIFT"]){
                        withCredentials([usernamePassword(credentialsId: 'DOCKER_CREDENTIALS_GIFT', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                            sh """
                                ssh -o StrictHostKeyChecking=no -l ubuntu 10.11.0.211 \"
                                    ls -l
                                    docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD registry-1.docker.io
                                    docker pull registry-1.docker.io/thitiporn2045/jenkins-oddscloud:${env.BUILD_NUMBER}
                                    docker run -dp 7001:80 registry-1.docker.io/thitiporn2045/jenkins-oddscloud:${env.BUILD_NUMBER}
                                \"
                            """
                        }
                    }
                }
            }
        }



    }
}
