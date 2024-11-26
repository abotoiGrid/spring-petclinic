pipeline {
    agent any

    environment {
        DOCKER_REGISTRY = "docker.io"
        MAIN_REPO = "abotoi/main"
        MR_REPO = "abotoi/mr"
    }

    stages {
        stage('Set Variables') {
            steps {
                script {
                    GIT_COMMIT_SHORT = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
                }
            }
        }

    //     stage('Checkstyle') {
    //         steps {
    //             script {
    //                 // Run Gradle Checkstyle
    //                 sh './gradlew checkstyleMain'
    //             }
    //             archiveArtifacts artifacts: '**/checkstyle/*.xml', allowEmptyArchive: true
    //         }
    //     }
        // stage('Test') {
        //     steps {
        //         script {
        //             sh './gradlew test'
        //         }
        //     }
        // }
        

        // stage('Build') {
        //     steps {
        //         script {
        //             sh './gradlew build -x test'
        //         }
        //     }
        // }
        stage('Create Docker Image (MR)') {
            steps {
                script {
                    sh "docker build -t ${MR_REPO}:${GIT_COMMIT_SHORT} ."
                }
            }
        }

        stage('Push Docker Image (MR)') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'dockerhub', variable: 'DOCKER_PASSWORD')]) {
                    sh '''
                    echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USER" --password-stdin
                    '''
                    sh "docker push ${MR_REPO}:${GIT_COMMIT_SHORT}"
                    }
                }
            }
        }

         stage('Create Docker Image (Main)') {
            steps {
                script {
                    sh "docker build -t ${MAIN_REPO}:latest ."
                }
            }
        }

        stage('Push Docker Image (Main)') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'dockerhub', variable: 'DOCKER_PASSWORD')]) {
                    sh '''
                    echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USER" --password-stdin
                    '''
                    sh "docker push ${MAIN_REPO}:latest"
                    }
                }
            }
        }
    
    }
    post {
        always {
            cleanWs()
        }
    }

}
