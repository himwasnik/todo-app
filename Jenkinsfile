pipeline {

    agent any

    environment {

        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {

        stage('Checkout Code') {

            steps {

                git branch: 'master',
                url: 'https://github.com/himwasnik/todo-app.git'

            }
        }

        stage('SonarQube Analysis') {

            steps {

                withSonarQubeEnv('sonar') {

                    sh '''
                    $SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.projectKey=todo-app \
                    -Dsonar.projectName=todo-app \
                    -Dsonar.sources=. \
                    '''

                }
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
