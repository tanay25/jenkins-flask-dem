pipeline(

    agent any

    environment{
        IMAGE_NAME="tanay25/jenkins-flask-demo"
        IMAGE_TAG="$(BUILD_NUMBER)"
    }
    stages {
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
            
        }
    }
)