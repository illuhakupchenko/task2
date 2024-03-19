pipeline {    
    // Use any agent
    agent any
    environment {
        // Set the environment variable APP_PORT=9090
        APP_PORT = '9090'
        // Save the job name in a global variable
        JOB_NAME = "${JOB_NAME}"
    }
    
    stages {
        stage('Build') {
            steps {
                // Use the maven package phase to build the project
                sh 'mvn package'
            }
        }
        stage('Integration Test') {
            // Run the following stages in parallel
            parallel {
                stage('Running Application') {
                    // Use any agent
                    agent any
                    options {
                        // Set 60 seconds to complete the task
                        timeout(time: 60, unit: 'SECONDS')
                    }
                    steps {
               
                        script {
                            try {
                                dir('target') {
                                    // Run the "contact.war" application from the "target" folder
                                    sh 'java -jar contact.war'
                                }
                            } catch (Exception e) {
                                echo "Some error here: ${e}"
                                // Return "success" if the task is stopped after 60 seconds
                                currentBuild.result = 'SUCCESS'
                            }
                        }
                    }
                }
                stage('Running Test') {
                    steps {
                        // Wait 30 seconds for "contact.war" application to run
                        sh 'sleep 30' 
                        // Run only the "RestIT" integration test in the "test" phase of maven
                        sh 'mvn -Dtest=RestIT test' 
                    }
                }
                // End of parallel stages
            }
        }        
    }
}
