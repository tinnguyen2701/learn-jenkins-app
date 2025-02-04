pipeline {
    agent any

    environment {
        REACT_APP_VERSION = "1.0.$BUILD_ID"
        AWS_DEFAULT_REGION = "ap-southeast-2"
    }

    stages {
        stage('deploy to AWS') {
            agent {
                docker {
                    image 'amazon/aws-cli'
                    // thêm line này để fix issue path build does not exist
                    reuseNode true
                    args "--entrypoint=''"
                }
            }
            // nhớ thêm region để tránh lỗi
            // You must specify a region. You can also configure your region by running "aws configure".
            steps {
                withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh '''
                        aws --version
                        aws ecs register-task-definition --cli-input-json file://aws/task-definition-prod.json
                    '''
                }
                
            }
        }

        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "small changes"
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }
    }
}
