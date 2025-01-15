pipeline {
    agent any

    environment{
        DOCKER_IMAGE = 'thanak81/nextcicd-homework-2k25'
        DOCKER_TAG = "latest"
        DOCKER_RUN_CMD = "docker run --rm -d -p 4000:3000 --name nextcicd ${DOCKER_IMAGE}:${DOCKER_TAG}"
    }
    
    stages {
        stage ("Git Clone") {
            steps{ 
                git branch: "main", url: "https://github.com/thanak81/NextCICD-2K25.git" 
            }
        }
        stage ("Build DockerFilee"){
            steps{
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }
  
        stage ("Docker Tasks"){
            parallel {
                stage ("Login and Push to DockerHub"){
                    steps{
                        script {
                            withCredentials([usernamePassword(credentialsId: "dockerhub_cred",
                            usernameVariable: "USERNAME", passwordVariable: "PASSWORD"
                            )]){
                                sh "docker login --username $username --password $password"
                                sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                            }
                         }
           
                    }
                }
                stage ("Clean Old Images"){
                    steps {
                        sh "docker image prune -f"
                    }
                }
            }
        }

        stage ("Start a contaner"){
            steps {
                script {
                def containerStatus = sh(script: "docker ps | grep -q nextcicd", returnStatus: true)
                // def buildTag = (env.BUILD_ID.toInteger() - 1).toString()
                if (containerStatus == 0) {
                    sh "docker stop nextcicd"
                    sh ${DOCKER_RUN_CMD} 
                }
                else if (containerStatus != 0){
                    sh "${DOCKER_RUN_CMD}"
                }
                }
                // sh "docker ps | grep nextcicd && docker stop nextcicd"
            }
        }
        // stage ("Remove all dangling image"){
        //     steps {
        //         sh "docker image prune -f"
        //     }
        // }

        post {
            success {
                echo "Pipeline completed Successfully and Cool"
            }
            failure {
                echo "Pipeline failed"
            }
        }
    }


}