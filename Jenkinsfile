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

        stage('Deploy Artifacts to Code Artifact') {
            steps {
                withCredentials([aws(credentialsId: env.AWS_CREDS_ID)]) {
                    sh """
                    # 1. Explicitly execute the V2 binary found at /usr/local/bin/aws
                    /usr/local/bin/aws codeartifact login \
                        --tool maven \
                        --domain devops-sg \
                        --repository Java-Demo-Repo \
                        --region ap-south-1 \
                        --server-id codeartifact-repo-id
                    
                    # 2. Deploy the artifact using the standard Maven command
                    mvn deploy -DskipTests
                    """
                }
            }
        }
    }
}
