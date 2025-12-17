pipeline {
    agent any

    environment {
        IMAGE_NAME = "sum-python-app"
        CONTAINER_ID = ""
        TEST_FILE_PATH = "test_variables.txt"
    }

    stages {

        stage('Build') {
            steps {
                bat "docker build -t ${IMAGE_NAME} ."
            }
        }

        stage('Run') {
            steps {
                script {
                    def output = bat(
                        script: "docker run -d ${IMAGE_NAME}",
                        returnStdout: true
                    )
                    CONTAINER_ID = output.trim()
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    def lines = readFile(TEST_FILE_PATH).split('\n')

                    for (line in lines) {
                        if (line.trim() == "") continue

                        def vars = line.split(' ')
                        def arg1 = vars[0]
                        def arg2 = vars[1]
                        def expected = vars[2].toFloat()

                        def output = bat(
                            script: "docker exec ${CONTAINER_ID} python /app/sum.py ${arg1} ${arg2}",
                            returnStdout: true
                        )

                        def result = output.trim().toFloat()

                        if (result == expected) {
                            echo "OK: ${arg1} + ${arg2} = ${result}"
                        } else {
                            error "ERREUR: ${arg1} + ${arg2} ≠ ${expected}"
                        }
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                bat "docker login"
                bat "docker tag ${IMAGE_NAME} your_dockerhub_username/${IMAGE_NAME}"
                bat "docker push your_dockerhub_username/${IMAGE_NAME}"
            }
        }
    }

    post {
        always {
            bat "docker stop ${CONTAINER_ID}"
            bat "docker rm ${CONTAINER_ID}"
        }
    }
}
