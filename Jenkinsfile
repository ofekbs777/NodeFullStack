pipeline {
     agent {
        label 'ec2'
    }
    
    stages {
        stage('Build and Push backend Docker Image in parallel') {
            parallel {
                stage('build frontend') {
                      agent {
        label 'ec2'
                    }
                    
                  
                    steps {
                        script {

                            def repository = "517056076755.dkr.ecr.us-east-1.amazonaws.com/ofekproj"
                                def tag = "${env.BUILD_ID}"
                                def dockerfile = "Dockerfile-frontend"
                                sh """
                                aws ecr get-login-password --region us-east-1 \
                                | docker login --username AWS --password-stdin ${repository}

                            docker build -t ${repository}:${tag} -f ${dockerfile} public
                                docker push ${repository}:${tag}
                            """
                        }
                    }
                }
                stage('build backend') {
                      agent {
        label 'ec2'
    }
                    
                    
                    steps {
                        script {

                            def repository = "517056076755.dkr.ecr.us-east-1.amazonaws.com/ofekproj"
                                def tag = "${env.BUILD_ID}"
                                def dockerfile = "Dockerfile-backend"
                                sh """
                                aws ecr get-login-password --region us-east-1 \
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
            
            agent {
        label 'ec2'
    }
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
