pipeline {
    environment {
        REGISTRY_HOST = "registry-harbor.app.bangun-kreatif.com"
        IMAGE_NAME = "tes-fe"
        FULL_IMAGE_NAME = "$REGISTRY_HOST/empatnusabangsa/tes/$IMAGE_NAME"
        DOCKER_IMAGE = ''
        REGISTRY_CRED = "registry-cred"
    }

    agent any

    stages {

        stage('Deploy') { 
            steps { 
                script { 
                    docker.withRegistry( ${env.REGISTRY_HOST}, REGISTRY_CRED ) { 

                      def customImage = docker.build("$FULL_IMAGE_NAME:${env.BUILD_ID}")

                        /* Push the container to the custom Registry */
                        customImage.push()
                    }
                } 
            }
        }


        stage('Cleaning up') {
            steps {
                sh "docker rmi $FULL_IMAGE_NAME:$BUILD_NUMBER"
            }
        }
        stage('Deploy to GKE') {
            steps {       
                    sh "sed -i 's/${env.IMAGE_NAME}:latest/${env.IMAGE_NAME}:${env.BUILD_ID}/g' deployment.yaml"
            }
        }
        stage('Deploy to local') {         
            steps{
                withKubeCredentials(kubectlCredentials: [[contextName: 'k3s', credentialsId: 'kubernetesToken', namespace: 'default', 
                serverUrl: 'https://127.0.0.1:6443']]){               
                 sh "kubectl apply -f deployment.yaml  "         
                }  
            }
          }
        }
    }
