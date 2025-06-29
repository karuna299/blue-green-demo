pipeline {
    agent any
    environment {
        DEPLOY_BASE = "/var/www"
        CURRENT_LINK = "/var/www/current"
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Determine Target Directory') {
            steps {
                script {
                    def active = sh(script: "readlink -f $CURRENT_LINK | xargs basename", returnStdout: true).trim()
                    if (active == "blue") {
                        env.TARGET = "green"
                    } else {
                        env.TARGET = "blue"
                    }
                    echo "Deploying to: ${env.TARGET}"
                }
            }
        }
        stage('Deploy Files') {
            steps {
                script {
                    sh """
                        rm -rf $DEPLOY_BASE/$TARGET/*
                        cp -r * $DEPLOY_BASE/$TARGET/
                    """
                }
            }
        }
        stage('Switch Symlink') {
            steps {
                script {
                    sh """
                        ln -sfn $DEPLOY_BASE/$TARGET $CURRENT_LINK
                    """
                }
            }
        }
        stage('Reload Nginx') {
            steps {
                script {
                    sh "sudo systemctl reload nginx"
                }
            }
        }
    }
}

