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
        stage('Tests') {
            parallel {
                stage('Unit tests'){
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                        echo "Test stage message"
                        test -f build/index.html
                        npm test
                        '''
                    }
                    post {
                        always {
                            // junit 'test-results/junit.xml'
                            junit 'jest-results/junit.xml'
                        }
                    }
                }
                stage('E2E'){
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                            # npm install -g serve
                            npm install serve
                            # serve -s build
                            node_modules/.bin/serve -s build & # ampersant is added to run 
                            # the command in background so won't block the execution
                            sleep 10
                            # npx playwright test
                            npx playwright test --reporter=html
                        '''
                    }
                    post {
                        always {
                            // the following script was generated from jenkins -> go on the pipeline -> configure -> Pipeline Syntax and fill with desired configuration and press generate pipeline script
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }
                }
            }
         }
    }

    // post {
    //     always {
    //         // junit 'test-results/junit.xml'
    //         junit 'jest-results/junit.xml'
    //         // the following script was generated from jenkins -> go on the pipeline -> configure -> Pipeline Syntax and fill with desired configuration and press generate pipeline script
    //         publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
    //     }
    // }
}