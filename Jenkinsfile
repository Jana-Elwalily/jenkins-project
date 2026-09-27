pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                sh 'docker build -t hello-world-app .'
            }
        }

        stage("Test") {
            steps {
                echo "Testing.."
            }
        }

        stage("Deploy") {
            steps {
                sh 'docker run -d --name hello-world-container -p 8081:80 hello-world-app'
            }
        }
    }
}
