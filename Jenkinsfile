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
 
        stage('Building our image') {
            steps {
                script {
                    DOCKER_IMAGE = docker.build FULL_IMAGE_NAME + ":$BUILD_NUMBER"
                }
            }
        }

        stage('Deploy') { 
            steps { 
                script { 
                    docker.withRegistry( '$REGISTRY_HOST', REGISTRY_CRED ) { 
                        dockerImage.push(DOCKER_IMAGE) 
                         dockerImage.push('latest')
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
