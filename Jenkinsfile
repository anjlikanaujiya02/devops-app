pipeline {
    agent any

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
                sh 'sudo docker build -t anjli14/app:latest .'
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
                    echo $PASS | sudo docker login -u $USER --password-stdin
                    sudo docker push anjli14/app:latest
                    '''
                }
            }
        }
    }
}
