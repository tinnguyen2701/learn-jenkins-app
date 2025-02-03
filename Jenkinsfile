pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'dd7efc5c-52bb-484b-909b-d26c6f83ab3e'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
        REACT_APP_VERSION = "1.0.$BUILD_ID"
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

        // stage('Run tests') {
        //     parallel {
        //         stage('Test 1') {
        //             agent {
        //                 docker {
        //                     image 'node:18-alpine'
        //                     reuseNode true
        //                 }
        //             }

        //             environment {
        //                 CI_ENVIRONMENT_URL = "example-url-any"
        //             }

        //             steps {
        //                 sh '''
        //                     test -f build/index.html
        //                     npm test
        //                 '''
        //             }
        //         }

        //         stage('Test 2') {
        //             steps {
        //                 sh '''
        //                     ls -la
        //                 '''
        //             }
        //         }
        //     }
        // }

        stage('Deploy prod') {
            agent {
                docker {
                    image 'my-playwright'
                    reuseNode true
                }
            }

            environment {
                CI_ENVIRONMENT_URL = 'YOUR NETLIFY SITE URL'
            }

            steps {
                sh '''
                    node --version
                    netlify --version
                    echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                    netlify status
                    netlify deploy --dir=build --prod
                '''
            }
        }


        
        // stage('E2E') {
        //     agent {
        //         docker {
        //             image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
        //             reuseNode true
        //         }
        //     }

        //     steps {
        //         sh '''
        //             npm install serve
        //             node_modules/.bin/serve -s build
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
