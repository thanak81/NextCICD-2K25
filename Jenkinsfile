pipeline {
    agent any

    environment {
        BUILD_ID = env.BUILD_ID
    }
    
    stages {
        stage ("Git Clone") {
            steps{ 
                git branch: "main", url: "https://github.com/thanak81/NextCICD-2K25.git" 
            }
        }
        stage ("Build DockerFilee"){
            steps{
                sh "docker build -t thanak81/nextcicd-homework-2k25:${BUILD_ID} ."
            }
        }
        stage ("Login to DockerHub"){
            steps{
                script {
                        withCredentials([usernamePassword(credentialsId: "dockerhub_cred",
                        usernameVariable: "USERNAME", passwordVariable: "PASSWORD"
                )]){
                    sh "docker login --username $username --password $password"
                }
                }
           
            }
        }
        stage ("Push to DockerHub"){
            steps{
                sh "docker push thanak81/nextcicd-homework-2k25:${BUILD_ID}"
            }
        }
        stage ("Start a contaner"){
            steps {
                script {
                def containerStatus = sh(script: "docker ps | grep -q nextcicd", returnStatus: true)
                if (containerStatus == 0) {
                    sh "docker stop nextcicd"
                    sh "docker run -rm -d -p 4000:3000 --name nextcicd thanak81/nextcicd-homework:${BUILD_ID}" 
                }
                else if (containerStatus != 0){
                    sh "docker run --rm -d -p 4000:3000 --name nextcicd thanak81/nextcicd-homework:${BUILD_ID}" 
                }
                }
                // sh "docker ps | grep nextcicd && docker stop nextcicd"
            }
        }
    }


}