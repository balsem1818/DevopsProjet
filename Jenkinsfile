pipeline {
    agent any

    tools {
        jdk 'jdk17'
        maven 'M2_HOME'
    
     }

    environment {
        // URL SonarQube (si Sonar tourne sur la même machine que Jenkins: http://localhost:9000)
        // Si Jenkins est dans une VM et Sonar dans docker sur la même VM, ça reste localhost.
        SONAR_HOST_URL = 'http://localhost:9000'

        // Mets ton token Sonar ici si tu veux tester vite (pas idéal)
        // Mieux: Credentials Jenkins (voir note en dessous)
       // SONAR_TOKEN = 'squ_e784b8cfd03b5c84f531ca3642af7806cf38b1ad'
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
                sh 'mvn -B clean compile'
            }
        }

          stage('SonarQube Analysis') {
            steps {
                // Utilise le credential sonar-token (recommandé)
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
    }

    post {
        success {
            echo 'Build + Sonar analysis SUCCESS'
        }
        failure {
            echo 'Build FAILED'
        }
    }
}    
