pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'my-python-project:latest'
        VENV_PATH = "${WORKSPACE}/venv"  // Define virtual environment path
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'git@github.com:PratikKr10/my_python_project.git'
            }
        }

        stage('Setup Python Environment') {
            steps {
                sh '''
                python3 -m venv ${VENV_PATH}
                source ${VENV_PATH}/bin/activate
                pip install --upgrade pip
                '''
            }
        }

        stage('Build Wheel') {
            steps {
                sh '''
                source ${VENV_PATH}/bin/activate
                pip install build
                python -m build --wheel
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                source ${VENV_PATH}/bin/activate
                pip install pytest
                pytest tests/
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${DOCKER_IMAGE} .'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker stop my-python-container || true
                docker rm my-python-container || true
                docker run -d --name my-python-container ${DOCKER_IMAGE}
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
