pipeline {
    agent any
    
    environment {
        APP_PORT = '9090'
        JOB_NAME = "${JOB_NAME}"
    }
    
    stages {
        stage('Build') {
            steps {
                sh 'mvn -DskipTests package'
            }
        }
        stage('Integration Test') {
            parallel {
                stage('Running Application') {
                    agent any
                    options {
                        timeout(time: 60, unit: 'SECONDS')
                    }
                    steps {
                        script {
                            try {
                                dir('target') {
                                    sh 'java -jar -DAPP_PORT=${APP_PORT} contact.war'
                                }
                                
                            } catch (Exception e) {
                                echo = 'ERROR'
                            }
                        }
                    }
                }
                stage('Running Test') {
                    steps {
                        
                        sleep(time: 30, unit: 'SECONDS')
                        sh 'mvn -Dtest=RestIT test'
                    }
                }
            }
        }
    }
}
