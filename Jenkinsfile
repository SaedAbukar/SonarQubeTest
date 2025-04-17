pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    environment {
        SONARQUBE_SERVER = 'SonarQubeServer'
        SONAR_TOKEN = 'sqa_226976b1f05eb7555739bde3dc119f2da773eff8'
        DOCKERHUB_CREDENTIALS_ID = 'dockerhub-credentials'
        DOCKERHUB_REPO = 'saedabukar/sonarqubetest'
        DOCKER_IMAGE_TAG = 'latest'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/SaedAbukar/SonarQubeTest.git'
            }
        }

        stage('Build') {
            steps {
                bat 'mvn clean install'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${env.SONARQUBE_SERVER}") {
                    bat """
                        "C:\\Users\\saeda\\Downloads\\sonar-scanner-cli-7.0.2.4839-windows-x64\\sonar-scanner-7.0.2.4839-windows-x64\\bin\\sonar-scanner.bat" ^
                        -Dsonar.projectKey=devops-demo ^
                        -Dsonar.sources=src ^
                        -Dsonar.projectName=DevOps-Demo ^
                        -Dsonar.host.url=http://localhost:9000 ^
                        -Dsonar.login=${env.SONAR_TOKEN} ^
                        -Dsonar.java.binaries=target/classes
                    """
                }
            }
        }

        stage('Set Docker Host') {
            steps {
                script {
                    if (isUnix()) {
                        env.DOCKER_HOST = 'unix:///var/run/docker.sock'
                    } else {
                        env.DOCKER_HOST = 'npipe:////./pipe/docker_engine'
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    if (isUnix()) {
                        sh "docker build -t ${DOCKERHUB_REPO}:${DOCKER_IMAGE_TAG} ."
                    } else {
                        bat "docker build -t ${DOCKERHUB_REPO}:${DOCKER_IMAGE_TAG} ."
                    }
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS_ID) {
                        if (isUnix()) {
                            sh "docker push ${DOCKERHUB_REPO}:${DOCKER_IMAGE_TAG}"
                        } else {
                            bat "docker push ${DOCKERHUB_REPO}:${DOCKER_IMAGE_TAG}"
                        }
                    }
                }
            }
        }
    }
}
