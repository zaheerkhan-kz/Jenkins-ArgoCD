pipeline {
    agent none

    environment {
        DJANGO_SETTINGS_MODULE = 'mysite'
    }

    stages {

        stage('Checkout') {
            agent any
            steps {
                checkout scm
            }
        }

        stage('Python Tests') {
            agent any
            steps {
                sh '''
                python3 --version
                python3 -m venv venv
                . venv/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                python manage.py check
                python manage.py test
                '''
            }
        }

        stage('Maven Build') {
            agent { label 'maven-agent' }
            steps {
                sh '''
                mvn --version
                mvn clean test
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Python tests and Maven build successful"
        }
        failure {
            echo "❌ Pipeline failed"
        }
    }
}
