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

                checkout([$class: 'GitSCM',
                    branches: [[name: '*/main']],
                    doGenerateSubmoduleConfigurations: false,
                    userRemoteConfigs: [[
                        url: 'https://github.com/zaheerkhan-kz/Jenkins-ArgoCD.git'
                    ]]
                ])

                dir('java-app') {
                    sh 'ls -la'
                    sh 'cat pom.xml'
                    sh 'mvn --version'
                    sh 'mvn clean test'
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
