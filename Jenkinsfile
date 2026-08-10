pipeline {

    agent any

    environment {
        APP_NAME = 'davine-site'
        APP_VERSION = "${BUILD_NUMBER}"
        CONTAINER_NAME = 'davine-site-container'
        HOST_PORT = '8081'
        CONTAINER_PORT = '80'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code from GitHub...'
                checkout scm
            }
        }

        stage('Validate') {
            steps {
                echo 'Validating project files...'

                sh '''
                    test -f index.html
                    test -f README.md
                    test -f Jenkinsfile
                    test -f Dockerfile

                    echo "All required files found."
                '''
            }
        }

        stage('Build') {
            steps {
                echo "Building ${APP_NAME} version ${APP_VERSION}..."

                sh '''
                    rm -rf build
                    mkdir -p build

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
                echo 'Running application tests...'

                sh '''
                    echo "Test 1: Checking Davine Technologies..."
                    grep -q "Davine Technologies" build/index.html
                    echo "Test 1 PASSED"

                    echo "Test 2: Checking DevOps..."
                    grep -q "DevOps" build/index.html
                    echo "Test 2 PASSED"

                    echo "Test 3: Checking version..."
                    test -s build/version.txt
                    echo "Test 3 PASSED"
                '''
            }
        }

        stage('Docker Build') {
            steps {
                echo "Building Docker image..."

                sh '''
                    docker build \
                        -t ${APP_NAME}:${APP_VERSION} \
                        -t ${APP_NAME}:latest \
                        .
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                echo 'Deploying Docker container...'

                sh '''
                    docker rm -f ${CONTAINER_NAME} || true

                    docker run -d \
                        --name ${CONTAINER_NAME} \
                        -p ${HOST_PORT}:${CONTAINER_PORT} \
                        ${APP_NAME}:${APP_VERSION}
                '''
            }
        }

        stage('Container Test') {
    steps {
        echo 'Testing running Docker container...'

        sh '''
            sleep 3

            docker exec ${CONTAINER_NAME} \
                wget -qO- http://localhost/ \
                | grep -q "Davine Technologies"

            echo "Container test PASSED"
        '''
    }
}

        stage('Package') {
            steps {
                echo 'Creating application package...'

                sh '''
                    tar -czf ${APP_NAME}-${APP_VERSION}.tar.gz build/

                    ls -lh ${APP_NAME}-${APP_VERSION}.tar.gz
                '''
            }
        }

        stage('Archive') {
            steps {
                echo 'Archiving build artifact...'

                archiveArtifacts \
                    artifacts: "${APP_NAME}-${APP_VERSION}.tar.gz",
                    fingerprint: true
            }
        }
    }

    post {

        success {
            echo '======================================'
            echo 'PIPELINE SUCCESS'
            echo "Application: ${APP_NAME}"
            echo "Version: ${APP_VERSION}"
            echo "Container: ${CONTAINER_NAME}"
            echo "Port: ${HOST_PORT}"
            echo '======================================'
        }

        failure {
            echo '======================================'
            echo 'PIPELINE FAILED'
            echo "Application: ${APP_NAME}"
            echo "Build: ${BUILD_NUMBER}"
            echo 'Check the logs above.'
            echo '======================================'
        }

        always {
            echo 'Pipeline execution finished.'
        }
    }
}
