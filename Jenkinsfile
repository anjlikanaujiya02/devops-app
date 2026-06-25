pipeline {
    agent {
        docker {
            image 'docker:20.10.24'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    tools {
        maven 'Maven_3'
    }

    environment {
        DOCKER_IMAGE = "anjli14/app"
    }

    stages {

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh '''
                    mvn sonar:sonar \
                    -Dsonar.projectKey=devops-app \
                    -Dsonar.projectName=devops-app
                    '''
                }
            }
        }

        stage('Run Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:latest .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh '''
                    echo $PASS | docker login -u $USER --password-stdin
                    docker push $DOCKER_IMAGE:latest
                    '''
                }
            }
        }
    }
}
