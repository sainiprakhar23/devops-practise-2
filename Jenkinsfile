pipeline {

    agent any

    environment {

        DOCKER_USER = 'sainiprakhar23'

        IMAGE_NAME = 'nginx-practise-2'

        IMAGE_TAG = "${BUILD_NUMBER}"

        KUBECONFIG = 'C:\\Users\\saini\\.kube\\config'
    }

    stages {

        stage('Git Check') {

            steps {

                echo 'GitHub connected successfully'

            }
        }

        stage('Docker Check') {

            steps {

                bat 'docker --version'

            }
        }

        stage('Build Docker Image') {

            steps {

                bat """
                docker build -t %DOCKER_USER%/%IMAGE_NAME%:%IMAGE_TAG% .
                """

            }
        }

        stage('Docker Login') {

            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {

                    bat """
                    echo Logging into Docker Hub...
                    docker login -u "%DOCKER_USERNAME%" -p "%DOCKER_PASSWORD%"
                    """

                }
            }
        }

        stage('Push Docker Image') {

            steps {

                bat """
                docker push %DOCKER_USER%/%IMAGE_NAME%:%IMAGE_TAG%
                """

            }
        }

        stage('Kubernetes Check') {

            steps {

                bat """
                set KUBECONFIG=%KUBECONFIG%
                kubectl get nodes
                """

            }
        }

        stage('Deploy To Kubernetes') {

            steps {

                bat """
                set KUBECONFIG=%KUBECONFIG%
                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml
                kubectl set image deployment/ngnix-deployment-2 nginx=%DOCKER_USER%/%IMAGE_NAME%:%IMAGE_TAG%
                """

            }
        }

        stage('Verify Rollout') {

            steps {

                bat """
                set KUBECONFIG=%KUBECONFIG%
                kubectl rollout status deployment/ngnix-deployment-2
                """

            }
        }

        stage('Show Deployment') {

            steps {

                bat """
                set KUBECONFIG=%KUBECONFIG%
                kubectl get deployment ngnix-deployment-2
                kubectl get pods
                kubectl get svc nginx-service-2
                """

            }
        }
    }

    post {

        success {

            echo 'CI/CD Pipeline Completed Successfully'

        }

        failure {

            echo 'Pipeline Failed'

        }

        always {

            echo 'Pipeline Execution Finished'

        }
    }
}