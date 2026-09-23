pipeline(

    agent any

    environment{
        IMAGE_NAME="tanay25/jenkins-flask-demo"
        IMAGE_TAG="$(BUILD_NUMBER)"
    }
    stages {
        stage('Checkout') {
            steps{
                git branch: 'main', url:'https://github.com/tanay25/jenkins-flask-dem.git'
            }
        }
        stage('Build'){
            steps {
                echo 'Building'
                sh '''
                    python3 --version
                    pip3 install -r requirements.txt
                '''
            }
            stage('Test'){
                steps {
                    echo 'Running tests'
                    sh '''
                        python3 -m py_compile app.py
                    '''
                }
            }
            stage("Docker Build"){
                steps{
                    sh '''
                        docker build -t ${IMAGE_NAME}: ${IMAGE_TAG} ,
                    '''
                }
            }
            stage('Docker Login'){
                steps {
                    withCredentials([usernamePassword(
                        credentialId: 'dockerhub',
                        usernameVaraibel: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )])
                    {
                        sh '''
                            echo "$DOCKER_PASSWORD | docekr login -u "$DOCKER_USER" --password-stdin
                        '''
                    }
                }
            }
            stage('Deploy'){
                steps{
                    sh '''
                        docker stop jenkins-flask-demo || true
                        docker rm jenkins-flask-demo || true
                        docker run -d -p 5000:5000 $IMAGE_NAME:${IMAGE_TAG}

                    '''
                }
            }
            
        }
    }
)