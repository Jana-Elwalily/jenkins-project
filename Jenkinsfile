```groovy
pipeline {
    agent any

    parameters {
        choice(
            name: 'DEPLOY_TARGET',
            choices: [
                'Docker',
                'Kubernetes',
                'Docker and Kubernetes'
            ],
            description: 'Choose where to deploy the application'
        )
    }

    stages {

        stage('Build') {
            steps {
                sh 'docker build -t hello-world-app:${BUILD_NUMBER} .'
            }
        }

        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }

        stage('Approval') {
            input {
                message 'Do you approve the deployment?'
                ok 'Deploy'
            }

            steps {
                echo 'Deployment approved'
            }
        }

        stage('Deploy to Docker') {
            when {
                expression {
                    params.DEPLOY_TARGET == 'Docker'
                }
            }

            steps {
                sh 'docker rm -f hello-world-container '
                sh 'docker run -d --name hello-world-container -p 8081:80 hello-world-app:${BUILD_NUMBER}'
            }
        }

        stage('Deploy to Kubernetes') {
            when {
                expression {
                    params.DEPLOY_TARGET == 'Kubernetes'
                }
            }

            steps {
                //sed -iE "s|hello-world-app:.*|hello-world-app:${BUILD_NUMBER}|" k8s/deployment.yml
                sh 'sed -iE "s|image: hello-world-app:.*|image: hello-world-app:${BUILD_NUMBER}|" k8s/deployment.yml'
                sh 'kind load docker-image hello-world-app:${BUILD_NUMBER} --name php-cluster'
                sh 'kubectl apply -f k8s/deployment.yml'
            }
        }

        stage('Deploy to Docker and Kubernetes') {
            when {
                expression {
                    params.DEPLOY_TARGET == 'Docker and Kubernetes'
                }
            }

            parallel {

                stage('Docker') {
                    steps {
                        sh 'docker rm -f hello-world-container 2>/dev/null || true'
                        sh 'docker run -d --name hello-world-container -p 8081:80 hello-world-app:${BUILD_NUMBER}'
                    }
                }

                stage('Kubernetes') {
                    steps {
                        sh 'sed -i "s|image: hello-world-app:.*|image: hello-world-app:${BUILD_NUMBER}|" k8s/deployment.yml'
                        sh 'kind load docker-image hello-world-app:${BUILD_NUMBER} --name php-cluster'
                        sh 'kubectl apply -f k8s/deployment.yml'
                    }
                }
            }
        }
    }
}
```
