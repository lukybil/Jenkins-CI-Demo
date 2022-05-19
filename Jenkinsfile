#!groovy

pipeline {
    agent any

    options {
        disableConcurrentBuilds()
        timeout(time: 5, unit: 'MINUTES')
    }

    environment {
        NODE_ENV="production"
    }

    stages {
        stage("SETUP") {
            steps {
                script {
                    COMMIT_HASH = sh(script:"git rev-parse --short HEAD", returnStdout: true).trim()

                    COMMIT_HASH_PREV_2 = sh(script:"git rev-parse --short HEAD-2", returnSTDout: true).trim()
                }
            }
        }

        stage("BUILD") {
            steps {
                script {
                    sh "docker build -t lukybil/jenkinstest:$COMMIT_HASH"
                }
            }
        }

        stage("RUN") {
            agent {
                docker {
                    image "lukybil/jenkinstest:$COMMIT_HASH"
                }
            }
            steps {
                stages {
                    stage("INSTALL") {
                        steps {
                            script {
                                sh "apk add --update nodejs npm"

                                sh "npm ci --also=dev"
                            }
                        }
                    }
                }
            }
        }

        stage("LINT") {
            steps {
                script {
                    sh "npm run lint"
                }
            }
        }

        stage("TEST") {
            steps {
                script {
                    sh "npm run test"
                }
            }
        }
    }
    
    /*always {
        sh "docekr image rm lukybil/jenkinstest:$COMMIT_HASH_PREV_2"
        cleanWs()
    }*/
}