pipeline {
    agent any

    tools {
        jdk "jdk17"
        maven "mvn3.8.7"
    }

    stages {

        stage("Initial Parallel Tasks") {
            parallel {

                stage("Run Host Commands") {
                    steps {
                        sh '''
                        hostname
                        df -h .
                        hostname -i
                        pwd
                        whoami
                        java -version
                        '''
                    }
                }

                stage("Checkout Code") {
                    steps {
                        git branch: 'main', url: 'https://github.com/shgupta140-max/test2-repo.git'
                    }
                }

            }
        }

        stage("Test") {
            steps {
                sh 'mvn test'
            }
        }

        stage("Build") {
            steps {
                sh 'mvn clean package'
            }
        }
    }
}
