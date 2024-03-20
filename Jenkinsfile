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
                                    sh 'java -jar contact.war'
                                }
                                
                            } catch (Exception e) {
                                echo = 'success'
                            }
                        }
                    }
                }
                stage('Running Test') {
                    steps {
                        // Запускаем интеграционные тесты, игнорируя ошибки Maven
                        // Даем приложению время на запуск
                        sleep(time: 30, unit: 'SECONDS')
                        sh 'mvn integration-test -Dtest=RestIT'
                    }
                }
            }
        }
    }
}
