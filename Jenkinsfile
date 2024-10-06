pipeline {
    agent any


    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/ChakraHs/ChakraHs-Laravel_CI-CD_Sonar.git', branch: 'main'
            }
        }

        stage('Install Deps') {
            steps {
                sh 'composer install'
            }
        }
        
        stage('Build Laravel') {
            steps {
                script {
                    sh 'php artisan serve'
                }
            }
        }

        stage('installer les dépendances Node') {
            steps {
                script {
                    sh 'npm install'
                }
            }
        }

        stage('compiler les assets Node') {
            steps {
                script {
                    sh 'npm run build'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'docker run --network=host -e SONAR_HOST_URL="http://127.0.0.1:9000" -v "$PWD:/usr/src" sonarsource/sonar-scanner-cli'
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                } 
            }
        }
    }
}