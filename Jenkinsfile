pipeline {
    agent any

    environment {
        APP_NAME = 'devops-task2-app'
        IMAGE_TAG = "devops-task2-app:${BUILD_NUMBER}"
        TEST_CONTAINER = "devops-task2-test-${BUILD_NUMBER}"
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building the application Docker image'
                sh 'docker build --pull -t "$IMAGE_TAG" .'
            }
        }

        stage('Test') {
            steps {
                echo 'Starting the container and checking its HTTP response'
                sh '''
                    set -eu
                    docker run -d --name "$TEST_CONTAINER" "$IMAGE_TAG"

                    attempt=0
                    until docker exec "$TEST_CONTAINER" wget -q -O - http://127.0.0.1/ | grep -q 'DevOps Task 2'; do
                        attempt=$((attempt + 1))
                        if [ "$attempt" -ge 10 ]; then
                            echo "Application did not return the expected page"
                            docker logs "$TEST_CONTAINER" || true
                            exit 1
                        fi
                        sleep 2
                    done

                    echo 'HTTP smoke test passed'
                '''
            }
        }

        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                echo 'Deploying the tested image on the Jenkins Docker host'
                sh '''
                    set -eu
                    docker tag "$IMAGE_TAG" "$APP_NAME:latest"
                    docker rm -f "$APP_NAME" 2>/dev/null || true
                    docker run -d \
                        --name "$APP_NAME" \
                        --restart unless-stopped \
                        -p 8081:80 \
                        "$APP_NAME:latest"
                    echo 'Application is available on port 8081 of the Jenkins host'
                '''
            }
        }
    }

    post {
        always {
            sh 'docker rm -f "$TEST_CONTAINER" 2>/dev/null || true'
        }
        success {
            echo 'Pipeline completed successfully'
        }
        failure {
            echo 'Pipeline failed; review the stage logs above'
        }
    }
}
