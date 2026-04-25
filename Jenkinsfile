pipeline {
    agent { label 'AGENT-1' }

    environment {
        PROJECT = 'expense'
        COMPONENT = 'backend'
        REGION = 'us-east-1'
        APP_VERSION = ''
        ENV = ''
    }

    options {
        disableConcurrentBuilds()
        timeout(time: 30, unit: 'MINUTES')
    }

    parameters {
        string(name: 'version', description: 'enter the application version')
        choice(name: 'deploy_to', choices: ['dev','qa','prod'], description: 'Pick environment')
    }

    stages {
        stage('Set Up environment') {
            steps {
                script {
                    env.APP_VERSION = params.version
                    env.ENV = params.deploy_to
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    withAWS(region: "${env.REGION}", credentials: "aws-creds-${env.ENV}") {
                        sh """
                            aws eks update-kubeconfig --region ${REGION} --name expense-${ENV}
                            kubectl get nodes
                            cd helm
                            sed -i "s/IMAGE_VERSION/${APP_VERSION}/g" values-${ENV}.yaml
                            helm upgrade --install ${COMPONENT} -n ${PROJECT} -f values-${ENV}.yaml .
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'i will run if it is success or fail'
            deleteDir()
        }
        failure {
            echo 'i will run when pipeline fails'
        }
        success {
            echo 'i will run when pipeline success'
        }
    }
}