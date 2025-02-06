pipeline {
    agent any

    environment {
        REACT_APP_VERSION = "1.0.$BUILD_ID"
        APP_NAME = "learnjenkinsapp"
        AWS_DEFAULT_REGION = "ap-southeast-2"
        AWS_DOCKER_REGISTRY = "891377092746.dkr.ecr.ap-southeast-2.amazonaws.com"
        AWS_ECS_CLUSTER = "LearnJenkinAppHi"
        AWS_ECS_SERVICE_PROD = "LearnJenkinsApp-Service-Prod"
        AWS_ECS_TD = "LearnJenkinApp-TaskDefinition-Prod"
        
    }

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }

        stage('Build Docker Image') {
            agent {
                // aws cli cần cài thêm docker 
                // aws cli image have docker client but not have docker daemon, nên cài thêm docker daemon (map docker sock) nữa
                docker {
                    image 'my-aws-cli'
                    // thêm line này để fix issue path build does not exist
                    reuseNode true
                    // thêm -u root chỉ để cho yum cài dc jq
                    args "-u root -v /var/run/docker.sock:/var/run/docker.sock --entrypoint=''"
                }
            }
            // thêm tag version cho app
            steps {
                withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh '''
                        docker build -t $AWS_DOCKER_REGISTRY/$APP_NAME:$REACT_APP_VERSION .
                        aws ecr get-login-password | docker login --username AWS --password-stdin $AWS_DOCKER_REGISTRY
                        docker push $AWS_DOCKER_REGISTRY/$APP_NAME:$REACT_APP_VERSION
                    '''
                }
            }
        }

        stage('deploy to AWS') {
            agent {
                docker {
                    image 'my-aws-cli'
                    // thêm line này để fix issue path build does not exist
                    reuseNode true
                    // thêm -u root chỉ để cho yum cài dc jq
                    args "--entrypoint=''"
                }
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh '''
                        aws --version
                        LATEST_TD_REVISION=$(aws ecs register-task-definition --cli-input-json file://aws/task-definition-prod.json | jq '.taskDefinition.revision')
                        echo $LATEST_TD_REVISION
                        aws ecs update-service --cluster $AWS_ECS_CLUSTER --service $AWS_ECS_SERVICE_PROD --task-definition $AWS_ECS_TD:$LATEST_TD_REVISION
                        aws ecs wait services-stable --cluster $AWS_ECS_CLUSTER --service $AWS_ECS_SERVICE_PROD
                    '''
                }
                
            }
        }
    }
}
