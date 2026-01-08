pipeline {
    agent none

    environment {
        DJANGO_SETTINGS_MODULE = 'mysite'
    }

    stages {

        stage('Python Tests') {
            agent any
            steps {
                checkout scm
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
                checkout scm      // ✅ checkout on the Maven agent
                dir('java-app') {
                    sh '''
                    mvn --version
                    mvn clean test
                    '''
                }
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
