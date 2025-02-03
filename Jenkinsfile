pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'dd7efc5c-52bb-484b-909b-d26c6f83ab3e'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
        REACT_APP_VERSION = "1.0.$BUILD_ID"
    }

    stages {
        stage('AWS') {
            agent {
                docker {
                    image 'amazon/aws-cli'
                    args "--entrypoint=''"
                }
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh '''
                        aws --version
                        aws s3 ls
                    '''
                }
                
            }
        }

        // stage('Build') {
        //     agent {
        //         docker {
        //             image 'node:18-alpine'
        //             reuseNode true
        //         }
        //     }
        //     steps {
        //         sh '''
        //             echo "small changes"
        //             ls -la
        //             node --version
        //             npm --version
        //             npm ci
        //             npm run build
        //             ls -la
        //         '''
        //     }
        // }

        // stage('Deploy prod') {
        //     agent {
        //         docker {
        //             image 'my-playwright'
        //             reuseNode true
        //         }
        //     }

        //     environment {
        //         CI_ENVIRONMENT_URL = 'YOUR NETLIFY SITE URL'
        //     }

        //     steps {
        //         sh '''
        //             node --version
        //             netlify --version
        //             echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
        //             netlify status
        //             netlify deploy --dir=build --prod
        //         '''
        //     }
        // }

    }

    post {
        always {
            junit 'test-results/junit.xml'
        }
    }
}
