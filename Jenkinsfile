pipeline {
    agent any

    environment {
        APP_REPO = 'https://github.com/varha2021/covoiti-devops.git'
        BRANCH = 'develop'
        GIT_CREDENTIALS = 'gitHub-covoiti-pat'
    }

    tools {
        maven 'Maven-3.9.9' // Ensure this matches your Jenkins Maven tool name
    }

    stages {
        stage('Checkout App Code') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: "${BRANCH}"]],
                    userRemoteConfigs: [[
                        url: "${APP_REPO}",
                        credentialsId: "${GIT_CREDENTIALS}"
                    ]]
                ])
            }
        }

        stage('Build with Maven') {
            steps {
                dir('covoiti-back') {
                    sh 'mvn clean install -DskipTests=true'
                }
            }
        }

        stage('Run Unit Tests') {
            steps {
                dir('covoiti-back') {
                    sh 'mvn test'
                }
            }
        }

        stage('Package App') {
            steps {
                dir('covoiti-back') {
                    sh 'mvn package -DskipTests=true'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('covoiti-back') {
                    sh 'docker build -t covoiti-back:latest .'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                dir('covoiti-back/k8s') {
                    sh 'kubectl apply -f .'
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Build or test failed.'
        }
    }
}
