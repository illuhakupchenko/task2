pipeline {
    agent any

    // Save the job name in a global variable
    environment {
        JOB_NAME = "${JOB_NAME}"
        APP_PORT = '9090'
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn package'
            }
        }
        
        stages('Integration Test') {
            parallel {
                stage('Running Application') {
                    agent any
                    timeout(time: 60, unit: 'SECONDS') {
                        steps {
                            script {
                                try {
                                    dir('target') {
                                        sh 'java -jar contact.war'
                                    }
                                } catch (Exception e) {
                                    currentBuild.result = 'SUCCESS'
                                }
                            }
                        }
                    }
                }
                
                stage('Running Test') {
                    agent any
                    steps {
                        timeout(time: 30, unit: 'SECONDS') {
                            sh 'mvn test -Dtest=RestIT'
                        }
                    }
                }
            }
        }
    }
}
