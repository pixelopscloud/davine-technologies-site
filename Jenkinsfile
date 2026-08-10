pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out code from GitHub...'
                checkout scm
            }
        }

        stage('Validate') {
            steps {
                echo 'Validating project files...'

                sh '''
                    test -f index.html
                    test -f README.md

                    echo "Required files found"
                '''
            }
        }

        stage('Build') {
            steps {
                echo 'Building the website...'

                sh '''
                    mkdir -p build
                    cp index.html build/
                    cp README.md build/

                    echo "Build completed"
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'

                sh '''
                    if grep -q "Davine Technologies" build/index.html; then
                        echo "Website title test PASSED"
                    else
                        echo "Website title test FAILED"
                        exit 1
                    fi

                    if grep -q "DevOps" build/index.html; then
                        echo "DevOps content test PASSED"
                    else
                        echo "DevOps content test FAILED"
                        exit 1
                    fi
                '''
            }
        }

        stage('Package') {
            steps {
                echo 'Creating deployment package...'

                sh '''
                    tar -czf davine-site.tar.gz build/
                    ls -lh davine-site.tar.gz
                '''
            }
        }

        stage('Archive') {
            steps {
                echo 'Archiving build artifact...'

                archiveArtifacts artifacts: 'davine-site.tar.gz',
                                 fingerprint: true
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
