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

        stage('Build and Push Image to Registry') { 
            steps { 
                script { 
                    docker.withRegistry( "https://$REGISTRY_HOST", REGISTRY_CRED ) { 

                      def customImage = docker.build("$FULL_IMAGE_NAME:${env.BUILD_ID}")

                        /* Push the container to the custom Registry */
                      customImage.push()
                    }
                } 
            }
        }
        stage('Cleaning up and Change Manifest k8s deployment') {
            steps {
                sh "docker rmi $FULL_IMAGE_NAME:$BUILD_NUMBER"
                sh "sed -i 's/${env.IMAGE_NAME}:latest/${env.IMAGE_NAME}:${env.BUILD_ID}/g' deployment.yaml"
            }
        }
        stage('Deploy to Kubernetes') {         
            steps{
                withKubeCredentials(kubectlCredentials: [[contextName: 'k3s', credentialsId: 'kubernetesToken', namespace: 'default', serverUrl: 'https://api.k3s.com:6443']])
                {               
                 sh "kubectl apply -f deployment.yaml  "         
                }  
             }
          }
       }
    }
