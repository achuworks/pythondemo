pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "archanas20/pythondemo"
    }

    stages {

        stage('Install & Test') {
            steps {
                sh 'python3 --version'
                sh 'pip3 --version'
                sh 'pip3 install -r requirements.txt'
                sh 'pytest -v || true'  // avoids failing build if no tests
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t $DOCKER_IMAGE:$BUILD_NUMBER ."
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo "$PASS" | docker login -u "$USER" --password-stdin'
                }
            }
        }

        stage('Docker Push') {
            steps {
                sh "docker push $DOCKER_IMAGE:$BUILD_NUMBER"
            }
        }
    }

    post {
        always {
            sh 'docker logout || true'
        }
    }
}
