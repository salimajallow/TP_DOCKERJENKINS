pipeline {
    agent any
    
    environment {
        PATH = "/usr/local/bin:/Applications/Docker.app/Contents/Resources/bin:${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                echo 'Code récupéré avec succès'
            }
        }
        
        stage('Build') {
            steps {
                script {
                    // REMPLACE "bat" PAR "sh" POUR MAC/LINUX
                    if (isUnix()) {
                        // Commande pour Mac/Linux
                        sh '''
                            echo "Building Docker image..."
                            docker build -t mon-app .
                        '''
                    } else {
                        // Commande pour Windows (gardée pour compatibilité)
                        bat '''
                            echo "Building Docker image..."
                            docker build -t mon-app .
                        '''
                    }
                }
            }
        }
        
        stage('Run') {
            steps {
                script {
                    // AJOUTE CETTE LIGNE POUR CRÉER LA VARIABLE
                    def CONTAINER_ID = sh(
                        script: 'docker run -d -p 8080:80 mon-app',
                        returnStdout: true
                    ).trim()
                    
                    echo "Container ID: ${CONTAINER_ID}"
                    
                    // SAUVEGARDE LA VARIABLE POUR L'UTILISER PLUS TARD
                    env.CONTAINER_ID = CONTAINER_ID
                }
            }
        }
        
        stage('Test') {
            steps {
                script {
                    sh 'sleep 10'  // Attends que le container démarre
                    sh 'curl -f http://localhost:8080 || exit 1'
                }
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                // ICI tu peux utiliser env.CONTAINER_ID
                echo "Container déployé: ${env.CONTAINER_ID}"
            }
        }
    }
    
    post {
        always {
            echo 'Cleanup...'
            script {
                // UTILISE env.CONTAINER_ID qui a été sauvegardé
                if (env.CONTAINER_ID) {
                    sh "docker stop ${env.CONTAINER_ID} || true"
                    sh "docker rm ${env.CONTAINER_ID} || true"
                }
            }
        }
        success {
            echo 'Pipeline réussi ! 🎉'
        }
        failure {
            echo 'Pipeline échoué 😢'
        }
    }
}