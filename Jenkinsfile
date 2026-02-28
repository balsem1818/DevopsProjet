pipeline {
    agent any

    tools {
        jdk 'JAVA_HOME'
        maven 'M2_HOME'
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
                withSonarQubeEnv('SonarServer') {
                    sh 'mvn -B sonar:sonar'
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
