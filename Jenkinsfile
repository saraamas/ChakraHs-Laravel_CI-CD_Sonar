pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/saraamas/ChakraHs-Laravel_CI-CD_Sonar.git', branch: 'main'
            }
        }

        stage('Install Deps') {
            steps {
                // Running composer install natively on Windows
                bat 'composer install'
                echo 'composer install'
            }
        }
        
        stage('Build Laravel') {
            steps {
                script {
                    // Running php artisan serve natively on Windows
                    bat 'php artisan serve &'
                }
                echo 'php artisan serve &'
            } 
        } 

        stage('installer les dépendances Node') {
            steps {
                // Run npm install on Windows
                bat 'npm install'
                echo 'npm install'
            }
        }

        stage('compiler les assets Node') {
            steps { 
                // Run npm run build on Windows
                bat 'npm run build'
                echo 'npm run build'
            } 
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'sonar-scanner'
                    withSonarQubeEnv('SonarQube') {
                        // Running SonarQube scanner natively on Windows
                        bat """
                            ${scannerHome}/bin/sonar-scanner ^
                            -Dsonar.projectKey=-Laravel_CI-CD_Sonar ^
                            -Dsonar.host.url=http://localhost:9000 ^
                            -Dsonar.login=sqb_d09f59e5a37a961a0cdf2ae5c361cb4cd6aa116b ^
                            -Dsonar.sources=./app ^
                            -Dsonar.exclusions="vendor/*,storage/**,bootstrap/cache/*"
                        """
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        always {
            echo "Analyse terminée, vérifiez SonarQube pour les résultats."
        }

        failure {
            script {
                emailext(
                    subject: "Pipeline Failed: ${env.JOB_NAME} ${env.BUILD_NUMBER}",
                    body: """<p>Bonjour,</p>
                             <p>Le pipeline <strong>${env.JOB_NAME}</strong> a échoué à l'étape de Quality Gate lors de l'exécution de la build numéro <strong>${env.BUILD_NUMBER}</strong>.</p>
                             <p>Statut de la Quality Gate: <strong style="color: red;">${currentBuild.result}</strong></p>
                             <p>Vérifiez les détails de la build ici : <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                             <p>Cordialement,</p>
                             <p>Votre serveur Jenkins</p>""",
                    to: 'saramasmoudi20010@gmail.com', // Replace with desired email addresses
                    from:"chakra.hs.business@gmail.com",
                    replyTo:"chakra.hs.business@gmail.com",
                    mimeType: 'text/html'
                )
            }
        }

        success {
            script {
                emailext(
                    subject: "Pipeline Succeeded: ${env.JOB_NAME} ${env.BUILD_NUMBER}",
                    body: """<p>Bonjour,</p>
                             <p>Le pipeline <strong>${env.JOB_NAME}</strong> s'est terminé avec succès à l'étape de Quality Gate lors de l'exécution de la build numéro <strong>${env.BUILD_NUMBER}</strong>.</p>
                             <p>Statut de la Quality Gate: <strong style="color: green;">${currentBuild.result}</strong></p>
                             <p>Vérifiez les détails de la build ici : <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                             <p>Cordialement,</p>
                             <p>Votre serveur Jenkins</p>""",
                    to: 'saramasmoudi2001@gmail.com', // Replace with desired email addresses
                    from:"chakra.hs.business@gmail.com",
                    replyTo:"chakra.hs.business@gmail.com",
                    mimeType: 'text/html'
                )
            }
        }
    }
}
