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

        stage("Check AWS CLI Version") {
            steps {
                sh 'aws --version'
            }
        }
        
        stage("Deploy Artifacts to Code Artifact") {
            steps {
                // Use Jenkins Credentials Plugin to inject AWS keys securely
                withCredentials([aws(credentialsId: env.AWS_CREDS_ID)]) {
                    // 1. Log in to CodeArtifact: 
                    // This generates a temporary auth token and updates the Maven settings file.
                    // IMPORTANT: The server ID here must match the <id> in pom.xml (e.g., codeartifact-repo-id)
                    sh "aws codeartifact login --tool maven --domain ${env.AWS_CA_DOMAIN} --repository ${env.AWS_CA_REPO} --region ${env.AWS_REGION} --server-id codeartifact-repo-id"                    
                    
                    // 2. Deploy the artifact using the configured settings
                    sh 'mvn deploy -DskipTests'
                }
            }
        }
    }
}
