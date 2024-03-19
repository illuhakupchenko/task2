pipeline {
    agent any
    
    environment {
        APP_PORT = '9090'
    }
    
    // Save job name in a global variable
    environment {
        JOB_NAME = "${JOB_NAME}"
    }
    
    stages {
        stage('Build') {
            steps {
                sh 'mvn package' // Use the maven package phase to build the project
            }
        }
        stage('Integration Test') {
            parallel {
                stage('Running Application') {
                    agent any
                    options {
                        timeout(time: 60, unit: 'SECONDS') // Set 60 seconds to complete the task
                    }
                    steps {
                        script {
                            try {
                                dir('target') {
                                    sh 'java -jar contact.war'
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
                        sh 'sleep 30' // Wait 30 seconds for "contact.war" application to run
                        sh 'mvn -Dtest=RestIT test' // Run only the "RestIT" integration test in the "test" phase of maven
                    }
                }
            }
        }
    }
}
