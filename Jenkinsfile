pipeline {
    agent any

    environment {
        REPO_URL = "git@github.com:PratikKr10/my_python_project.git"
        WORKDIR = "${WORKSPACE}/my_python_project"
        VENV_PATH = "${WORKSPACE}/venv"
        DOCKER_IMAGE = "my-python-app:latest"
    }

    stages {
        stage('Clone Repository') {
            steps {
                cleanWs()
                sh '''
                    echo "Current directory: $PWD"
                    if [ -d "$WORKDIR" ]; then
                        echo "Directory already exists, removing it"
                        rm -rf "$WORKDIR"
                    fi
                    git clone $REPO_URL "$WORKDIR"
                    echo "Repository cloned successfully!"
                    ls -la "$WORKDIR"
                '''
            }
        }

        stage('Setup Python Environment') {
            steps {
                sh '''
                python3 -m venv "$VENV_PATH"
                . "$VENV_PATH/bin/activate"
                pip install --upgrade pip build pytest
                '''
            }
        }

        stage('Build Wheel') {
            steps {
                dir('my_python_project') {
                    sh '''
                    . "$VENV_PATH/bin/activate"
                    python3 -m build --wheel
                    '''
                }
            }
        }

        stage('Test') {
            steps {
                dir('my_python_project') {
                    sh '''
                    . "$VENV_PATH/bin/activate"
                    export PYTHONPATH=$PYTHONPATH:$(pwd)/src  # Add src/ to PYTHONPATH
                    pytest tests/
                    '''
                }
            }
        }


        stage('Build Docker Image') {
            steps {
                dir('my_python_project') {
                    sh '''
                    docker build -t $DOCKER_IMAGE .
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker stop my-python-container || true
                docker rm my-python-container || true
                docker run -d --name my-python-container $DOCKER_IMAGE
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Repository cloned, built, and deployed successfully!'
        }
        failure {
            echo '❌ Pipeline failed. Check the logs for details.'
        }
    }
}
