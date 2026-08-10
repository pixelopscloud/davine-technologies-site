pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code from GitHub...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building the project...'
                sh 'ls -la'
                sh 'echo "Build step completed"'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh '''
                    if [ -f index.html ]; then
                        echo "index.html found - test PASSED"
                    else
                        echo "index.html missing - test FAILED"
                        exit 1
                    fi
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs above.'
        }
    }
}
