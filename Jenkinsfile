pipeline {
    agent any
    environment {
        RUNNER_IMAGE = 'csv302lpu/grade-runner:v1'
        REPORT_DIR   = "${WORKSPACE}/surefire-reports"
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Pull Image') {
            steps {
                sh "docker pull ${RUNNER_IMAGE}"
            }
        }
        stage('Run Tests') {
            steps {
                sh """
                    mkdir -p ${REPORT_DIR}
                    docker run --rm \\
                        -v ${WORKSPACE}:/app \\
                        -v ${REPORT_DIR}:/app/target/surefire-reports \\
                        -w /app \\
                        ${RUNNER_IMAGE} \\
                        mvn test
                """
            }
        }
        stage('Publish Results') {
            steps {
                junit "${REPORT_DIR}/**/*.xml"
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: "surefire-reports/**/*.xml", allowEmptyArchive: true
        }
        success {
            echo 'BUILD PASSED — all tests green.'
        }
        failure {
            echo 'BUILD FAILED — check test results.'
        }
    }
}