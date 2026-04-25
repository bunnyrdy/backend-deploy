pipeline {
    agent { label 'AGENT-1'}
    environment {
        PROJECT = 'expense'
        COMPONENT = 'backend'
        DEPLOY_TO = 'production'
        REGION = 'us-east-1'
        appVersion = ''
        evironment = ''
    }
    options {
        disableConcurrentBuilds()
        timeout(time: 30, unit:'MINUTES')
    }
    parameters {
        string(name: 'version',  description: 'enter the application version')
        choice(name:'deploy_to',choices: ['dev','qa','prod'], description: 'Pick something')
    }
    stages{
        stage('Set Up environment'){
            steps{
                script{
                    appVersion = params.version
                    evironment = params.deploy_to
                }
            }
        }
        stage('Deploy'){
            steps{
                script{
                      withAWS(region: 'us-east-1', credentials: "aws-creds-${environment}") {
                sh """
                    aws eks update-kubeconfig --region $REGION --name expense-${environment}
                    kubectl get nodes
                    cd helm
                    sed -i 's/IMAGE_VERSION/${params.version}/g' values-${environment}.yaml
                    helm upgrade --install $COMPONENT -n $PROJECT -f values-${environment}.yaml .

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


