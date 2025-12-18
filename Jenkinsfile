pipeline {
    agent any

    environment {
        PATH = "/usr/local/bin:/Applications/Docker.app/Contents/Resources/bin:${env.PATH}"
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Code récupéré avec succès'
            }
        }

        stage('Build') {
            steps {
                sh 'docker --version'
                sh 'docker build -t mon-app .'
            }
        }

        stage('Run') {
            steps {
                sh 'docker run --rm mon-app'
            }
        }

        stage('Test') {
            steps {
                echo 'Test de l’application'
                sh 'python sum.py'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Déploiement simulé'
            }
        }
    }

    post {
        success {
            echo 'Pipeline réussi 🎉'
        }
        failure {
            echo 'Pipeline échoué 😢'
        }
        always {
            echo 'Nettoyage terminé'
        }
    }
}
