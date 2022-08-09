pipeline {
    
        
    
    stages {
        stage('Build and Push backend Docker Image in parallel') {
            parallel {
                stage('build frontend') {
                  
                    steps {
                        script {

                            def repository = "099242274141.dkr.ecr.eu-west-1.amazonaws.com/frontend"
                                def tag = "${env.BUILD_ID}"
                                def dockerfile = "Dockerfile-frontend"
                                sh """
                                aws ecr get-login-password --region eu-west-1 \
                                | docker login --username AWS --password-stdin ${repository}

                            docker build -t ${repository}:${tag} -f ${dockerfile} public
                                docker push ${repository}:${tag}
                            """
                        }
                    }
                }
                stage('build backend') {

                    steps {
                        script {

                            def repository = "099242274141.dkr.ecr.eu-west-1.amazonaws.com/backend"
                                def tag = "${env.BUILD_ID}"
                                def dockerfile = "Dockerfile-backend"
                                sh """
                                aws ecr get-login-password --region eu-west-1 \
                                | docker login --username AWS --password-stdin ${repository}

                            docker build -t ${repository}:${tag} -f ${dockerfile} .
                                docker push ${repository}:${tag}
                            """
                        }
                    }
                }
            } 
        }
        stage('Deploy to kubernetes') {
          
            steps {
                script {
                    sh """
                        helm upgrade --install backend  --set image.tag=${env.BUILD_ID} charts/backend
                        helm upgrade --install frontend --set image.tag=${env.BUILD_ID} charts/frontend
                        """
                }

            }
        }
    }
    }
