pipeline {
    agent any
    
    environment {
        CONTAINER_ID = ''
        SUM_PY_PATH = "${WORKSPACE}/sum.py"
        DIR_PATH = "${WORKSPACE}"
        TEST_FILE_PATH = "${WORKSPACE}/test_variables.txt"
    }
    
    stages {
        // Les étapes suivront
        stage('Build') {
          steps {
        script {
            docker.build("python-sum-app:${BUILD_ID}")
        }
          }
}
 stage('Run') {
    steps {
        script {
            def container = docker.run(
                image: "python-sum-app:${BUILD_ID}",
                args: "-d --name python-sum-container-${BUILD_ID}"
            )
            CONTAINER_ID = container.id
            echo "Conteneur démarré avec ID : ${CONTAINER_ID}"
        }
    }
}
stage('Test') {
    steps {
        script {
            def testLines = readFile(TEST_FILE_PATH).split('\n')
            
            for (line in testLines) {
                if (line.trim()) {
                    def vars = line.split(' ')
                    def arg1 = vars[0]
                    def arg2 = vars[1]
                    def expectedSum = vars[2].toFloat()
                    
                    def output = sh(
                        script: "docker exec ${CONTAINER_ID} python /app/sum.py ${arg1} ${arg2}",
                        returnStdout: true
                    ).trim()
                    
                    def result = output.toFloat()
                    
                    if (result == expectedSum) {
                        echo "✅ Test réussi : ${arg1} + ${arg2} = ${result}"
                    } else {
                        error "❌ Test échoué : ${arg1} + ${arg2}. Attendu: ${expectedSum}, Obtenu: ${result}"
                    }
                }
            }
        }
    }
}
post {
    always {
        script {
            if (CONTAINER_ID) {
                sh "docker stop ${CONTAINER_ID} || true"
                sh "docker rm ${CONTAINER_ID} || true"
                echo "Conteneur nettoyé"
            }
        }
    }
}
stage('Deploy to DockerHub') {
    steps {
        script {
            withCredentials([usernamePassword(
                credentialsId: 'docker-hub-credentials',
                usernameVariable: 'DOCKER_USER',
                passwordVariable: 'DOCKER_PASS'
            )]) {
                sh "docker login -u ${DOCKER_USER} -p ${DOCKER_PASS}"
                sh "docker tag python-sum-app:${BUILD_ID} ${DOCKER_USER}/python-sum-app:latest"
                sh "docker push ${DOCKER_USER}/python-sum-app:latest"
            }
        }
    }
}
    }
}
