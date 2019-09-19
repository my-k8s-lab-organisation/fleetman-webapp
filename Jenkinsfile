pipeline {
   agent any

   environment {
     YOUR_DOCKERHUB_USERNAME="abbi1680"
        ORGANIZATION_NAME="my-k8s-lab-organisation"
     
     SERVICE_NAME = "fleetman-webapp"
     REPOSITORY_TAG="${YOUR_DOCKERHUB_USERNAME}/${ORGANIZATION_NAME}-${SERVICE_NAME}:${BUILD_ID}"
   }

   stages {
      stage('Preparation') {
         steps {
            cleanWs()
            git credentialsId: 'github_key', url: "https://github.com/${ORGANIZATION_NAME}/${SERVICE_NAME}"
         }
      }
      
      
      stage('Build Docker Image') {
            steps {
                script {
                    app = docker.build("${REPOSITORY_TAG}")
                    app.inside {
                        sh 'echo Docker image is built'
                    }
                }
            }
        }

    stage('Push Docker Image to docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                      app.push("${BUILD_ID}")
   
                        app.push("latest")
                        
                    }
                }
            }
        }
      

      stage('Deploy to Cluster') {
          steps {
            sh 'envsubst < ${WORKSPACE}/deploy.yaml | kubectl apply -f -'
          }
      }
   }
}
