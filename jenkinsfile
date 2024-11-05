pipeline {
    agent any
    tools {
        maven 'Maven'  // Specify Maven version configured in Jenkins
    }
    stages {
        stage('Build') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'development') {
                        echo 'Building development environment...'
                        sh 'mvn clean install -Denvironment=dev'
                    } else if (env.BRANCH_NAME == 'staging') {
                        echo 'Building staging environment...'
                        sh 'mvn clean install -Denvironment=staging'
                    } else if (env.BRANCH_NAME == 'production') {
                        echo 'Building production environment...'
                        sh 'mvn clean install -Denvironment=prod'
                    }
                }
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Deploy') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'production') {
                        echo 'Deploying to production server...'
                        // Add production deployment steps here
                    } else if (env.BRANCH_NAME == 'staging') {
                        echo 'Deploying to staging server...'
                        // Add staging deployment steps here
                    } else {
                        echo 'Development build complete. No deployment for development branch.'
                    }
                }
            }
        }
    }
    post {
        success {
            echo "Build for ${env.BRANCH_NAME} branch was successful!"
        }
        failure {
            echo "Build for ${env.BRANCH_NAME} branch failed."
        }
    }
}

