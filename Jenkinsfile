pipeline {
    agent any

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

        stage('Run tests') {
            parallel {
                stage('Test 1') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }

                    steps {
                        sh '''
                            test -f build/index.html
                            npm test
                        '''
                    }
                }

                stage('Test 2') {
                    steps {
                        sh '''
                            ls -la
                        '''
                    }
                }
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
