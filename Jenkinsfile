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
                    docker.withRegistry( 'https://registry-harbor.app.bangun-kreatif.com', REGISTRY_CRED ) { 

                      def customImage = docker.build("FULL_IMAGE_NAME:${env.BUILD_ID}")

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
    }
}
