pipeline {

    agent any

    stages {

        stage('Checkout Code') {

            steps {

                git branch: 'master',
                url: 'https://github.com/himwasnik/todo-app.git'

            }
        }

        stage('Build Docker Image') {

            steps {

                sh 'docker build -t todo-app .'

            }
        }

        stage('Trivy Scan') {

            steps {

                sh 'trivy image todo-app'

            }
        }
    }
}
