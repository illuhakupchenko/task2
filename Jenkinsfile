pipeline {
    agent any
    
    environment {
        APP_PORT = '9090'
        JOB_NAME = "${JOB_NAME}"
    }
    
    stages {
        stage('Build') {
            steps {
                sh 'mvn package'
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
                                    run('java -jar -Dserver.port=9090 contact.war &')
                                }
                            } catch (Exception e) {
                                echo "Application failed to start: ${e}"
                                currentBuild.result = 'SUCCESS'
                            }
                        }
                    }
                }
                stage('Running Test') {
                    steps {
                        sh 'sleep 30'
                        sh 'mvn -Dtest=RestIT test'
                    }
                }
            }
        }
    }
}
