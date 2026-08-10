pipeline {

    agent any

    environment {
        APP_NAME = 'davine-site'
        APP_VERSION = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Checking out source code..."
                checkout scm
            }
        }

        stage('Validate') {
            steps {
                echo "Validating ${APP_NAME}..."

                sh '''
                    test -f index.html
                    test -f README.md
                    test -f Jenkinsfile

                    echo "All required files found."
                '''
            }
        }

        stage('Build') {
            steps {
                echo "Building ${APP_NAME} version ${APP_VERSION}..."

                sh '''
                    rm -rf build
                    mkdir build

                    cp index.html build/
                    cp README.md build/

                    echo "${APP_NAME}" > build/app-name.txt
                    echo "${APP_VERSION}" > build/version.txt

                    echo "Build completed."
                '''
            }
        }

        stage('Test') {
            steps {
                echo "Running application tests..."

                sh '''
                    echo "Test 1: Checking application name..."

                    grep -q "Davine Technologies" build/index.html

                    echo "Test 1 PASSED"


                    echo "Test 2: Checking DevOps content..."

                    grep -q "DevOps" build/index.html

                    echo "Test 2 PASSED"


                    echo "Test 3: Checking version..."

                    test -s build/version.txt

                    echo "Test 3 PASSED"
                '''
            }
        }

        stage('Package') {
            steps {
                echo "Creating package..."

                sh '''
                    tar -czf ${APP_NAME}-${APP_VERSION}.tar.gz build/

                    ls -lh ${APP_NAME}-${APP_VERSION}.tar.gz
                '''
            }
        }

        stage('Archive') {
            steps {
                echo "Archiving artifact..."

                archiveArtifacts artifacts: "${APP_NAME}-${APP_VERSION}.tar.gz",
                                 fingerprint: true
            }
        }
    }

    post {

        success {
            echo "================================"
            echo "PIPELINE SUCCESS"
            echo "Application: ${APP_NAME}"
            echo "Version: ${APP_VERSION}"
            echo "================================"
        }

        failure {
            echo "================================"
            echo "PIPELINE FAILED"
            echo "Application: ${APP_NAME}"
            echo "Build: ${BUILD_NUMBER}"
            echo "================================"
        }
    }
}
