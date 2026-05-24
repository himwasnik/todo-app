pipeline {

    agent any
    parameters {

        string(
            name: 'GIT_BRANCH',
            defaultValue: 'main',
            description: 'Git branch to build'
        )

        string(
            name: 'DOCKER_IMAGE_TAG',
            defaultValue: 'latest',
            description: 'Docker image tag'
        )

        booleanParam(
            name: 'RUN_TRIVY_SCAN',
            defaultValue: true,
            description: 'Run Trivy security scan'
        )

        booleanParam(
            name: 'PUSH_TO_DOCKERHUB',
            defaultValue: true,
            description: 'Push image to DockerHub'
        )

        booleanParam(
            name: 'DEPLOY_CONTAINER',
            defaultValue: true,
            description: 'Deploy Docker container'
        )
    }

    environment {

        IMAGE_NAME = 'todo-app'

        CONTAINER_NAME = 'todo-app-container'

        DOCKERHUB_USERNAME = 'himwasnik'

        IMAGE_TAG = "${DOCKERHUB_USERNAME}/todo-app:${params.DOCKER_IMAGE_TAG}"

        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {

        stage('Checkout Code') {
            steps {

                git branch: "${params.GIT_BRANCH}",
                url: 'https://github.com/YOUR_GITHUB_USERNAME/YOUR_REPO.git'

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

                sh '''
                docker build -t $IMAGE_NAME .
                '''

            }
        }

        stage('Trivy Scan') {

            when {
                expression { params.RUN_TRIVY_SCAN == true }
            }

            steps {

                sh '''
                trivy image --severity HIGH,CRITICAL $IMAGE_NAME
                '''

            }
        }

        stage('Tag Image') {
            steps {

                sh '''
                docker tag $IMAGE_NAME $IMAGE_TAG
                '''

            }
        }

        stage('Login to Docker Hub') {

            when {
                expression { params.PUSH_TO_DOCKERHUB == true }
            }

            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {

                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    '''

                }
            }
        }

        stage('Push to Docker Hub') {

            when {
                expression { params.PUSH_TO_DOCKERHUB == true }
            }

            steps {

                sh '''
                docker push $IMAGE_TAG
                '''

            }
        }

        stage('Stop Old Container') {

            when {
                expression { params.DEPLOY_CONTAINER == true }
            }

            steps {

                sh '''
                docker rm -f $CONTAINER_NAME || true
                '''

            }
        }

        stage('Run Container') {

            when {
                expression { params.DEPLOY_CONTAINER == true }
            }

            steps {

                sh '''
                docker run -d \
                --name $CONTAINER_NAME \
                -p 8000:8000 \
                $IMAGE_NAME
                '''

            }
        }

        stage('Verify Running Container') {

            when {
                expression { params.DEPLOY_CONTAINER == true }
            }

            steps {

                sh '''
                docker ps
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

        always {

            sh 'docker image prune -f || true'

        }
    }
}
