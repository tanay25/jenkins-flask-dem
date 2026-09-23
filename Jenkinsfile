pipeline {

    agent any

    environment {
        IMAGE_NAME = "tanay25/jenkins-flask-demo"
        IMAGE_TAG  = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/tanay25/jenkins-flask-dem.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Building Application'
                sh '''
                    sudo apt install python3.14-venv
                     python3 -m venv venv
                    . venv/bin/activate

                    pip install --upgrade pip
                    pip install -r requirements.txt

                    python app.py
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Running Tests'
                sh '''
                    python3 -m py_compile app.py
                '''
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                '''
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USER" --password-stdin
                    '''
                }
            }
        }

        stage('Docker Push') {
            steps {
                sh '''
                    docker push ${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker stop jenkins-flask-demo || true
                    docker rm jenkins-flask-demo || true

                    docker run -d \
                        --name jenkins-flask-demo \
                        -p 5000:5000 \
                        ${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }
    }
}