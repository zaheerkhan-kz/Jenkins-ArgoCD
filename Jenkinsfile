pipeline {
    agent any

    environment {
        DJANGO_SETTINGS_MODULE = 'mysite'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Python & Dependencies') {
            steps {
                sh '''
                python3 -m venv venv
                . venv/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run Django Checks') {
            steps {
                sh '''
                . venv/bin/activate
                python manage.py check
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                . venv/bin/activate
                python manage.py test
                '''
            }
        }
    }
}

        stage('Build Docker Image') {
            steps {
                sh '''
                docker build -t mydjangoapp:${BUILD_NUMBER} .
                '''
            }
        }

        stage('Tag & Push Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker tag mydjangoapp:${BUILD_NUMBER} $DOCKER_USER/mydjangoapp:${BUILD_NUMBER}
                    docker push $DOCKER_USER/mydjangoapp:${BUILD_NUMBER}
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "✅ Tests passed, image built and pushed"
        }
        failure {
            echo "❌ Tests failed"
        }
    }
}
