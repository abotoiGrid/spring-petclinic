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
//         stage('Integration Test with Docker Compose') {
//     steps {
//         script {
//             echo 'Starting integration tests with docker-compose...'
//             sh 'docker compose up'
            
//             // Run tests or health checks
//             sh 'docker compose ps'
            
//             // Tear down
//             //sh 'docker-compose down'
//         }
//     }
// }        

        stage('Test') {
            steps {
                script {
                    sh './gradlew test'
                }
            }
        }
        

        stage('Build') {
            steps {
                script {
                    sh './gradlew build -x test'
                }
            }
        }
        stage('Install Docker') {
            steps {
                sh '''
                apk update
                apk add --no-cache docker
                '''
            }
        }

        stage('Create Docker Image (MR)') {
            steps {
                script {
                    sh "docker build -t ${MR_REPO}:${GIT_COMMIT_SHORT} ."
                    sh "docker push ${MR_REPO}:${GIT_COMMIT_SHORT}"
                }
            }
        }

        stage('Create Docker Image (Main)') {
            steps {
                script {

                    sh "docker build -t ${MAIN_REPO}:latest ."
                    sh "docker push ${MAIN_REPO}:latest"
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'target/surefire-reports/**/*.xml', allowEmptyArchive: true
            cleanWs()
        }
    }
}
