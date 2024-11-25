pipeline {
    agent any

    environment {
        DOCKER_REGISTRY = "docker.io"
        MAIN_REPO = "abotoi/main"
        MR_REPO = "abotoi/mr"
        GIT_COMMIT_SHORT = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
    }

    stages {
    //     stage('Checkstyle') {
    //         steps {
    //             script {
    //                 // Run Gradle Checkstyle
    //                 sh './gradlew checkstyleMain'
    //             }
    //             archiveArtifacts artifacts: '**/checkstyle/*.xml', allowEmptyArchive: true
    //         }
    //     }

        stage('Test') {
            steps {
                script {
                    // Run Gradle tests
                    sh './mvnw clean test'
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    // Build project without running tests
                    sh './gradlew build -x test'
                }
            }
        }

        stage('Create Docker Image (MR)') {
            steps {
                script {
                    // Build the Docker image with the short Git commit hash
                    sh "docker build -t ${MR_REPO}:${GIT_COMMIT_SHORT} ."
                    // Push the image to the "mr" Docker repository
                    sh "docker push ${MR_REPO}:${GIT_COMMIT_SHORT}"
                }
            }
        }

        stage('Create Docker Image (Main)') {
            steps {
                script {
                    // Build the Docker image
                    sh "docker build -t ${MAIN_REPO}:latest ."
                    // Push the image to the "main" Docker repository
                    sh "docker push ${MAIN_REPO}:latest"
                }
            }
        }
    }

    post {
        always {
            // Clean up, if needed
            cleanWs()
        }
    }
}
