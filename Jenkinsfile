pipeline {
    agent any

    environment {
        AWS_CA_DOMAIN = 'devops-sg'
        AWS_CA_REPO = 'Java-Demo-Repo'
        AWS_REGION = 'ap-south-1'
        // This is the ID of your AWS Credentials saved in Jenkins
        AWS_CREDS_ID = 'Jenkins-uploader' 
    }

    tools {
        jdk "JDK17"
        maven "Maven 3.8.7"
    }

    stages {

        stage("Initial Parallels Tasks") {
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


        stage('Check AWS CLI Version') {
            steps {
                sh """
                echo "--- PATH DEBUG START ---"
                echo "Current PATH: \$PATH"
                echo "Which AWS: \$(which aws)"
                aws --version
                echo "--- PATH DEBUG END ---"
                """
            }
        }

        stage('Debug AWS CLI Version') {
            steps {
                sh """
                echo "==== PATH ===="
                echo \$PATH

                echo "==== which aws ===="
                which aws || echo "aws not found in PATH"

                echo "==== aws --version ===="
                aws --version || echo "aws version check failed"

                echo "==== /usr/local/bin/aws --version ===="
                /usr/local/bin/aws --version || echo "explicit path failed"

                echo "==== codeartifact login help ===="
                /usr/local/bin/aws codeartifact login help || echo "help failed"
                """
            }
        }
    }
}
