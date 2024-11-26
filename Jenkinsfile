pipeline {
    agent any

    environment {
        DOCKER_REGISTRY = "docker.io"
        MAIN_REPO = "abotoi/main"
        MR_REPO = "abotoi/mr"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Set Variables') {
            steps {
                script {
                    GIT_COMMIT_SHORT = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
                }
            }
        }
        stage('Determine Pipeline Type') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        currentBuild.description = "Main Branch Pipeline"
                    } else if (env.CHANGE_ID) {
                        currentBuild.description = "Merge Request Pipeline - PR #${env.CHANGE_ID}"
                    } else {
                        error("Unsupported branch type: ${env.BRANCH_NAME}")
                    }
                }
            }
        }

    //     stage('Checkstyle') { 
    //      when {
    //          expression { env.CHANGE_ID != null } // Run for PRs only
    //      }
    //         steps {
    //             script {
    //                 // Run Gradle Checkstyle
    //                 sh './gradlew checkstyleMain'
    //             }
    //             archiveArtifacts artifacts: '**/checkstyle/*.xml', allowEmptyArchive: true
    //         }
    //     }
        // stage('Test') {
        //     when {
        //         expression { env.CHANGE_ID != null } // Run for PRs only
        //     }
        //     steps {
        //         script {
        //             sh './gradlew test'
        //         }
        //     }
        // }
        

        // stage('Build') {
        //     when {
        //         expression { env.CHANGE_ID != null } // Run for PRs only
        //     }
        //     steps {
        //         script {
        //             sh './gradlew build -x test'
        //         }
        //     }
        // }
        stage('Create and Push Docker Image') {
            steps {
                script {
                    def repo = env.CHANGE_ID != null ? MR_REPO : MAIN_REPO
                    def tag = env.CHANGE_ID != null ? "${GIT_COMMIT_SHORT}" : "latest"
                    sh "docker build -t ${DOCKER_REGISTRY}/${repo}:${tag} ."

                    withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh '''
                    echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USER" --password-stdin
                    '''
                        sh "docker push ${DOCKER_REGISTRY}/${repo}:${tag}"
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
