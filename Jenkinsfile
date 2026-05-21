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
                sh "docker image inspect ${RUNNER_IMAGE} && echo 'Image ready'"
            }
        }

        stage('Run Tests') {
            steps {
                sh """
                    mkdir -p ${REPORT_DIR}
                    docker run --rm \
                        -v jenkins_home:/var/jenkins_home \
                        -w /var/jenkins_home/workspace/csv302-grade-tests \
                        -v /var/run/docker.sock:/var/run/docker.sock \
                        ${RUNNER_IMAGE} \
                        mvn test -Dsurefire.reportsDirectory=${REPORT_DIR}
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
            archiveArtifacts artifacts: "**/surefire-reports/*.xml", allowEmptyArchive: true
        }
        success {
            echo 'BUILD PASSED — all tests green.'
        }
        failure {
            echo 'BUILD FAILED — check test results.'
        }
    }
}