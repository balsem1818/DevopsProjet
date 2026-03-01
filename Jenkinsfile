
pipeline {
    agent any
    tools {
        jdk 'jdk21'
        maven 'M2_HOME'
    }
    environment {
        SONAR_HOST_URL = 'http://localhost:9000'
        DOCKER_IMAGE = 'balsembalsem/devopsprojet'
        DOCKER_TAG = "2.0.${BUILD_NUMBER}"
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                url: 'https://github.com/balsem1818/DevopsProjet.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn -B clean package -DskipTests'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
                    sh '''
                      mvn -B sonar:sonar \
                        -Dsonar.host.url=$SONAR_HOST_URL \
                        -Dsonar.token=$SONAR_TOKEN \
                        -Dsonar.projectKey=DevopsProjet \
                        -Dsonar.projectName=DevopsProjet
                    '''
                }
            }
        }

        stage('Docker Build & Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        docker build -t $DOCKER_IMAGE:$DOCKER_TAG .
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push $DOCKER_IMAGE:$DOCKER_TAG
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    kubectl set image deployment/spring-app \
                        spring-app=$DOCKER_IMAGE:$DOCKER_TAG \
                        -n devops
                    kubectl rollout status deployment/spring-app -n devops
                '''
            }
        }
    }
    post {
        success {
            echo 'Pipeline SUCCESS - Application déployée sur Kubernetes !'
        }
        failure {
            echo 'Pipeline FAILED'
        }
    }
}
